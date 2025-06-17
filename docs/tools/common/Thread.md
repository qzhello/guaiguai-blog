


# 计算线程数量
`jstack <pid> | grep thread_name | wc -l` 

也可以导出
`jstack <pid> > file_name.txt`
```java
public class ParseThreadCount {
    public static void main(String[] args) {
        // 定义文件路径
        String filePath = "/Users/quzhihao/Downloads/a.txt";

        // 定义一个 Map 来存储线程名前缀和出现次数
        Map<String, Integer> threadCountMap = new HashMap<>();

        try (BufferedReader reader = new BufferedReader(new FileReader(filePath))) {
            String line;
            // 正则表达式匹配线程名
            Pattern threadNamePattern = Pattern.compile("^\"([^\"]+)\"");

            // 逐行读取文件内容
            while ((line = reader.readLine()) != null) {
                Matcher matcher = threadNamePattern.matcher(line);
                if (matcher.find()) {
                    // 提取完整线程名
                    String threadName = matcher.group(1);

                    // 截取线程名前缀（去掉后面的 `_数字` 部分）
                    String threadPrefix = threadName.split("_")[0];

                    // 更新线程名前缀出现次数
                    threadCountMap.put(threadPrefix, threadCountMap.getOrDefault(threadPrefix, 0) + 1);
                }
            }

            // 将 Map 转换为 List，并按照出现次数排序
            List<Map.Entry<String, Integer>> sortedThreadList = new ArrayList<>(threadCountMap.entrySet());
            sortedThreadList.sort((entry1, entry2) -> entry2.getValue().compareTo(entry1.getValue()));

            // 输出排序后的线程名前缀和出现次数
            System.out.println("线程名前缀出现次数（按数量排序）：");
            for (Map.Entry<String, Integer> entry : sortedThreadList) {
                System.out.println(entry.getKey() + ": " + entry.getValue());
            }

        } catch (IOException e) {
            System.err.println("文件读取失败：" + e.getMessage());
        }
    }
}

```