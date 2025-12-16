---
title: Faker
# 禁用目录
toc: true
comments: true
weight: 1
---

# Faker使用
依赖，只在test阶段使用
```shell
	<dependency>
			<groupId>com.github.javafaker</groupId>
			<artifactId>javafaker</artifactId>
			<scope>test</scope>
	</dependency>

```
```shell
private final Faker faker = new Faker(Locale.CHINA);
String email = faker.number().digits(10) + "@qq.com";
String cellPhone = faker.phoneNumber().cellPhone()

```