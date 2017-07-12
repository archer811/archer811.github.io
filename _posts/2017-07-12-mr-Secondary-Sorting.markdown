---
layout: post
title:      "MapReduce二次排序"
subtitle:   "MR Secondary-Sorting"
date:       2017-07-12 12:00:00
author:     "Zhouxj"
header-img: "img/post-bg-mr-sort.jpg"
catalog: true
comments: true
tags:
    - MapReduce
    - 排序
---

### 题目描述
起因是这样一道题：
写mapreduce程序，实现二次排序。
样例数据如下
```
1904 100
1904 200
1904 50
1904 150
1904 160
1904 80
1904 60
1901 100
1901 200
1901 50
```
### 先学习一下wordcount的例子
主类：
```
public class WordCountDriver {
    public static void main(String[] args) throws Exception {
        Configuration conf = new Configuration();
        Job job = Job.getInstance(conf);
        job.setJarByClass(WordCountDriver.class);
        job.setMapperClass(WordCountMapper.class);
        job.setReducerClass(WordCountReducer.class);
        job.setMapOutputKeyClass(Text.class);  // 1
        job.setMapOutputValueClass(IntWritable.class); // 1
        job.setOutputKeyClass(Text.class); // 2
        job.setOutputValueClass(IntWritable.class); // 2
        job.setInputFormatClass(TextInputFormat.class);
        FileInputFormat.setInputPaths(job, new Path("c:/wordcount/input"));
        FileOutputFormat.setOutputPath(job, new Path("c:/wordcount/output"));
        boolean res = job.waitForCompletion(true);
        System.exit(res ? 0 : 1);
    }
}
```
Map类：
```
public class WordCountMapper extends Mapper<LongWritable, Text, Text, IntWritable> {
    @Override
    protected void map(LongWritable key, Text value, Context context)
            throws IOException, InterruptedException {
        String line = value.toString();
        String[] words = line.split(" ");
        for (String word : words) {
            context.write(new Text(word), new IntWritable(1));
        }
    }
}
```
Reduce类：
```
public class WordCountReducer extends Reducer<Text, IntWritable, Text, IntWritable> {
    @Override
    protected void reduce(Text key, Iterable<IntWritable> values, Context context)
            throws IOException, InterruptedException {
        int count = 0;
        for (IntWritable value : values) {
            count += value.get();
        }
        context.write(new Text(key), new IntWritable(count));
    }
}
```
刚开始的时候老是LongWritable,Text搞不清什么时候该用什么格式，老是碰到如下报错:
<img src="//archer811.github.io/img/post-mrsort.png"  width="650" alt="sentry code"/>
后来发现原因和规律：
因为我设置的job.setInputFormatClass(TextInputFormat.class)，Mapper<LongWritable, Text<LongWritable, Text,*,*>前两个类型是固定的。
另外 代码里1处，2处分别和Mapper，Reducer的后两个参数一样。


### 一次map相当于一次排序
怎么更好理解这句话，举另外一个例子：
wordcount之后的数据：
```
a    5
b    4
c    74
d    78
e    1
r    64
f    4
```
这时候要实现每个词出现的次数降序排列：
1，先把每行的两个值换位置：
5 a<br>
4 b<br>
74 c<br>
2，然后map后，hadoop会对结果进行分组
(5：a)<br>
(4:b,f)<br>
(74:c)<br>
3，因为hadoop对数据分组后默认是按照key升序排序的，每行两个值换回位置
b 4<br>
f 4<br>
a 5<br>


### 二次排序实现方法一
二次排序，执行两次排序，既然map可以执行一次，那么可以考虑在reduce里执行一次。
所以重写Reducer：
```
public class WordCountReducer extends Reducer<Text, IntWritable, Text, IntWritable> {
    protected void reduce(Text  key, Iterable<IntWritable> values,
        Context context)
            throws IOException, InterruptedException {
      List<Integer> arr = new ArrayList<Integer>();
      for(IntWritable value : values) {
        arr.add(Integer.valueOf(value.toString()));
      }
      Collections.sort(arr);
      for(Integer value : arr) {
        System.out.println(value);
      }
      for(Integer value : arr) {
        context.write(key, new IntWritable(value));
      }
    }
}
```
可行
### 二次排序实现方法二
另外一种比较多的人采用的方法
可以把两个值搭成一个组合，比较的时候同时比较两个数。
封装一个自定义类型
```
private static class MyNewKey implements WritableComparable<MyNewKey> {
        long firstNum;
        long secondNum;
        public MyNewKey() {
        }
        public MyNewKey(long first, long second) {
            firstNum = first;
            secondNum = second;
        }
        @Override
        public void write(DataOutput out) throws IOException {
            out.writeLong(firstNum);
            out.writeLong(secondNum);
        }
        @Override
        public void readFields(DataInput in) throws IOException {
            firstNum = in.readLong();
            secondNum = in.readLong();
        }
        @Override
        public int compareTo(MyNewKey anotherKey) {
            long min = firstNum - anotherKey.firstNum;
            if (min != 0) {
                // 说明第一列不相等，则返回两数之间小的数
                return (int) min;
            } else {
                return (int) (secondNum - anotherKey.secondNum);
            }
        }
    }
```
map的时候当做key活着value：
```
public static class MyMapper extends
            Mapper<LongWritable, Text, MyNewKey, LongWritable> {
        protected void map(
                LongWritable key,
                Text value,
                Mapper<LongWritable, Text, MyNewKey, LongWritable>.Context context)
                throws java.io.IOException, InterruptedException {
            String[] spilted = value.toString().split("\t");
            long firstNum = Long.parseLong(spilted[0]);
            long secondNum = Long.parseLong(spilted[1]);
            // 使用新的类型作为key参与排序
            MyNewKey newKey = new MyNewKey(firstNum, secondNum);
            context.write(newKey, new LongWritable(secondNum));
        };
    }
```
