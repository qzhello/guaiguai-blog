---
title: easyexcel
# 禁用目录
toc: true
comments: true
weight: 1
---

# easyexcel使用
alibaba的excel工具
```shell
<dependency>
     <groupId>com.alibaba</groupId>
     <artifactId>easyexcel</artifactId>
     <version>4.0.1</version>
</dependency>


```

实体定义
```shell
 @Data
@NoArgsConstructor
@AllArgsConstructor
@Builder
static class ExcelGenerateDemoData {

        @ColumnWidth(30)
        @ExcelProperty("用户ID")
        private String userId;

        @ColumnWidth(20)
        @ExcelProperty("手机号")
        private String phone;

        @ColumnWidth(30)
        @ExcelProperty("邮箱")
        private String mail;
}
```

生成

```shell
private final String excelPath = Paths.get("").toAbsolutePath().getParent() + "/tmp";

@Test
public void testGenerateExcel() {
        if (!FileUtil.exist(excelPath)) {
            FileUtil.mkdir(excelPath);
        }
        String fileName = excelPath + "/任务推送Excel.xlsx";
        EasyExcel.write(fileName, ExcelGenerateDemoData.class).sheet("优惠券推送列表").doWrite(data());
}


private List<ExcelGenerateDemoData> data() {
        List<ExcelGenerateDemoData> list = ListUtils.newArrayList();
        for (int i = 0; i < writeNum; i++) {
            ExcelGenerateDemoData data = ExcelGenerateDemoData.builder()
                    .mail(faker.number().digits(10) + "@163.com")
                    .phone(faker.phoneNumber().cellPhone())
                    .userId(IdUtil.getSnowflakeNextIdStr())
                    .build();
            list.add(data);
        }
        return list;
}
```