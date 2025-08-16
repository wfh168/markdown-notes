# Java Stream 流的使用指南

Java 8 引入的 Stream API 是处理集合数据的强大工具，它允许你以声明式方式处理数据集合（类似SQL语句），并且可以透明地并行化操作。下面是 Stream 流的详细用法。

## 一、Stream 基础概念

### 1. 什么是 Stream

- Stream 不是数据结构，它是对数据源（集合、数组等）的一种高级抽象
- Stream 不会存储数据，只是按需计算
- Stream 操作是延迟执行的（lazy evaluation）
- Stream 不可重复使用（一旦被消费就不能再使用）

### 2. Stream 操作分类

- **中间操作(Intermediate operations)**：返回Stream，可以链式调用（如filter, map, sorted等）
- **终端操作(Terminal operations)**：返回非Stream结果或产生副作用（如forEach, collect, reduce等）

## 二、创建 Stream

### 1. 从集合创建

```
List<String> list = Arrays.asList("a", "b", "c");
Stream<String> stream = list.stream();       // 顺序流
Stream<String> parallelStream = list.parallelStream(); // 并行流
```

### 2. 从数组创建

```
String[] array = {"a", "b", "c"};
Stream<String> stream = Arrays.stream(array);
```

### 3. 使用Stream.of()

```
Stream<String> stream = Stream.of("a", "b", "c");
```

### 4. 创建无限流

```
// 生成无限流
Stream<Integer> infiniteStream = Stream.iterate(0, n -> n + 2); // 0, 2, 4, 6...

// 生成随机数流
Stream<Double> randomStream = Stream.generate(Math::random);
```

## 三、常用中间操作

### 1. filter() - 过滤

```
List<String> filtered = list.stream()
    .filter(s -> s.startsWith("a"))
    .collect(Collectors.toList());
```

### 2. map() - 映射转换

```
List<Integer> lengths = list.stream()
    .map(String::length)
    .collect(Collectors.toList());
```

### 3. flatMap() - 扁平化映射

```
List<String> words = Arrays.asList("Hello", "World");
List<String> letters = words.stream()
    .flatMap(word -> Arrays.stream(word.split("")))
    .collect(Collectors.toList());
// 结果: ["H", "e", "l", "l", "o", "W", "o", "r", "l", "d"]
```

### 4. distinct() - 去重

```
List<Integer> numbers = Arrays.asList(1, 2, 1, 3, 3, 2, 4);
numbers.stream()
    .distinct()
    .forEach(System.out::println);
// 输出: 1 2 3 4
```

### 5. sorted() - 排序

```
// 自然排序
list.stream().sorted();

// 自定义排序
list.stream()
    .sorted((s1, s2) -> s2.compareTo(s1));
```

### 6. limit()/skip() - 截取/跳过

```
Stream.iterate(0, n -> n + 1)
    .limit(10)       // 取前10个
    .skip(5)         // 跳过前5个
    .forEach(System.out::println);
```

## 四、常用终端操作

### 1. forEach() - 遍历

```
list.stream().forEach(System.out::println);
```

### 2. collect() - 收集为集合

```
List<String> upperCaseList = list.stream()
    .map(String::toUpperCase)
    .collect(Collectors.toList());

Set<String> set = list.stream().collect(Collectors.toSet());
```

### 3. toArray() - 转为数组

```
String[] array = list.stream().toArray(String[]::new);
```

### 4. reduce() - 归约

```
// 求和
Optional<Integer> sum = numbers.stream().reduce(Integer::sum);

// 使用初始值
Integer sum = numbers.stream().reduce(0, Integer::sum);

// 连接字符串
String concatenated = list.stream().reduce("", String::concat);
```

### 5. min()/max() - 最值

```
Optional<String> min = list.stream().min(Comparator.naturalOrder());
Optional<String> max = list.stream().max(String::compareToIgnoreCase);
```

### 6. count() - 计数

```
long count = list.stream().filter(s -> s.startsWith("a")).count();
```

### 7. anyMatch()/allMatch()/noneMatch() - 匹配

```
boolean anyStartsWithA = list.stream().anyMatch(s -> s.startsWith("a"));
boolean allStartsWithA = list.stream().allMatch(s -> s.startsWith("a"));
boolean noneStartsWithZ = list.stream().noneMatch(s -> s.startsWith("z"));
```

### 8. findFirst()/findAny() - 查找

```
Optional<String> first = list.stream().findFirst();
Optional<String> any = list.parallelStream().findAny();
```

## 五、数值流（原始类型特化流）

```
// IntStream
IntStream intStream = IntStream.range(1, 100); // 1-99
int sum = intStream.sum();

// LongStream
LongStream longStream = LongStream.rangeClosed(1, 100); // 1-100

// DoubleStream
DoubleStream doubleStream = list.stream().mapToDouble(String::length);
```

## 六、收集器（Collectors）高级用法

### 1. 分组

```
Map<Integer, List<String>> groupByLength = list.stream()
    .collect(Collectors.groupingBy(String::length));
```

### 2. 分区

```
Map<Boolean, List<String>> partition = list.stream()
    .collect(Collectors.partitioningBy(s -> s.length() > 3));
```

### 3. 连接字符串

```
String joined = list.stream().collect(Collectors.joining(", "));
```

### 4. 汇总统计

```
IntSummaryStatistics stats = list.stream()
    .collect(Collectors.summarizingInt(String::length));
// 可获取count, sum, min, max, average
```

## 七、并行流

```
// 创建并行流
list.parallelStream()
    .filter(s -> s.length() > 3)
    .forEach(System.out::println);

// 顺序流转并行流
list.stream()
    .parallel()
    .filter(...)
    .collect(...);
```

## 八、实际应用示例

### 1. 统计单词频率

```
Map<String, Long> wordCount = words.stream()
    .collect(Collectors.groupingBy(Function.identity(), 
             Collectors.counting()));
```

### 2. 查找最长字符串

```
Optional<String> longest = list.stream()
    .reduce((s1, s2) -> s1.length() > s2.length() ? s1 : s2);
```

### 3. 转换Map

```
Map<String, Integer> map = list.stream()
    .collect(Collectors.toMap(Function.identity(), String::length));
```

### 4. 多级分组

```
Map<Integer, Map<Character, List<String>>> multiLevelGroup = list.stream()
    .collect(Collectors.groupingBy(String::length,
             Collectors.groupingBy(s -> s.charAt(0))));
```

## 九、注意事项

1. **流只能消费一次**：一旦终端操作执行，流就被消费

2. **避免副作用**：不要在流操作中修改外部状态

3. **性能考虑**：

   - 小数据量时，流可能比循环慢
   - 大数据量时，并行流能提高性能

4. **调试技巧**：使用peek()方法查看中间结果

   ```
   list.stream()
       .peek(System.out::println)
       .map(String::toUpperCase)
       .peek(System.out::println)
       .collect(Collectors.toList());
   ```

Stream API 提供了一种更简洁、更易读的方式来处理集合数据，尤其适合复杂的数据处理流水线。掌握Stream可以显著提高Java代码的表达能力和生产力。