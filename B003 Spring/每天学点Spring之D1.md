

# ApplicationContext

-   ClassPathXmlApplicationContext
-   AnnoatationConfigApplicationContext

## 获取 Bean 对象

`getBean(String name)`

`getBean(Class class)`

## @Configuration

### @Bean

### @Scope

-   **prototype**
-   **singleton**：默认
-   **request**
-   **session**

### @Lazy

## @ComponentScan

-   **value**
-   **includeFilters** 
-   **excludeFilters** 
-   **useDefaultFilters**

### @Filter

-   type
-   classes

| type                       | 说明          | classes              |
| -------------------------- | ------------- | -------------------- |
| FilterType.ANNOTATION      | 注解          | Controller.class     |
| FilterType.ASSIGNABLE_TYPE | 给定的类型    |                      |
| FilterType.ASPECTJ         | ASPECTJ表达式 |                      |
| FilterType.REGEX           | 正则          |                      |
| FilterType.CUSTOM          | 自定义规则    | Interface TypeFilter |



```java

```



## @Condition

interface Condition



## @Import

-   普通类

-   ImportSelector.class

-   ImportBeanDefinitionRegistrar.class

    

# FactoryBean



# Bean的生命周期