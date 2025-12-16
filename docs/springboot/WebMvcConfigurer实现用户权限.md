---
title: WebMvcConfigurer实现用户权限
# 禁用目录
toc: true
comments: true
weight: 1
---

# 中断器
执行顺序：

Filter  
Interceptor  
Controller 方法  
aop  
视图渲染  
Interceptor 的 afterCompletion  
Filter 的后置处理  

实现例子：
```java
@Configuration
public class UserConfiguration implements WebMvcConfigurer {

    /**
     * 用户信息传输拦截器
     */
    @Bean
    public UserTransmitInterceptor userTransmitInterceptor() {
        return new UserTransmitInterceptor();
    }

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        // 配置中断器
        registry.addInterceptor(userTransmitInterceptor())
                .addPathPatterns("/**");
    }

    /**
     * 自定义拦截器
     */
    static class UserTransmitInterceptor implements HandlerInterceptor {

        @Override
        public boolean preHandle(@Nullable HttpServletRequest request, @Nullable HttpServletResponse response, @Nullable Object handler) throws Exception {
            // 默认设置
            UserInfoDTO userInfoDTO = new UserInfoDTO("1", "qzh1234", 10000L);
            UserContext.setUser(userInfoDTO);
            return true;
        }

        @Override
        public void afterCompletion(@Nullable HttpServletRequest request, @Nullable HttpServletResponse response, @Nullable Object handler, Exception exception) throws Exception {
            // 防止泄漏
            UserContext.removeUser();
        }
    }
}
```

上下文设置

TransmittableThreadLocal依赖
```xml
<dependency>
      <groupId>com.alibaba</groupId>
      <artifactId>transmittable-thread-local</artifactId>
      <version>2.14.2</version>
</dependency>
```
```shell
public final class UserContext {
    /**
     * <a href="https://github.com/alibaba/transmittable-thread-local" />
     */
    private static final ThreadLocal<UserInfoDTO> USER_THREAD_LOCAL = new TransmittableThreadLocal<>();

    /**
     * 设置用户至上下文
     *
     * @param user 用户详情信息
     */
    public static void setUser(UserInfoDTO user) {
        USER_THREAD_LOCAL.set(user);
    }

    /**
     * 获取上下文中用户 ID
     *
     * @return 用户 ID
     */
    public static String getUserId() {
        UserInfoDTO userInfoDTO = USER_THREAD_LOCAL.get();
        return Optional.ofNullable(userInfoDTO).map(UserInfoDTO::getUserId).orElse(null);
    }

    /**
     * 获取上下文中用户名称
     *
     * @return 用户名称
     */
    public static String getUsername() {
        UserInfoDTO userInfoDTO = USER_THREAD_LOCAL.get();
        return Optional.ofNullable(userInfoDTO).map(UserInfoDTO::getUsername).orElse(null);
    }

    /**
     * 清理用户上下文
     */
    public static void removeUser() {
        USER_THREAD_LOCAL.remove();
    }

}
```

