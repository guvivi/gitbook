# Spring MVC之参数解析过程
spring mvc是基于方法处理的，处理请求的过程中会将Controller的方法封装为ServletInvocableHandlerMethod，进行反射处理。里面有两个重要的步骤：

* 对方法参数的解析
* 对方法返回值的处理

### HttpMethodArgumentRevolver
spring 使用HttpMethodArgumentRevolver接口解析参数，spring会加入默认的实现，用户也可以加入自定义的实现（==配置bean RequestMappingHandlerAdapter的customArgumentResolvers属性==）。spring会根据条件选择合适的参数解析器进行解析参数。

==注：RequestMappingHandlerAdapter通过supportsParameter()表示支持的类型==

HttpMethodArgumentRevolver声明了两个方法：



```
boolean supportsParameter(MethodParameter parameter);

Object resolveArgument(MethodParameter parameter, ModelAndViewContainer mavContainer, 		NativeWebRequest webRequest, WebDataBinderFactory binderFactory) throws Exception; 
```

* supportsParameter：是否支持
* resolveArgument：解析参数


#### 默认实现类
RequestMappingHandlerAdapter#getDefaultArgumentResolvers()

##### 1、支持注解

类名 | 说明
------- | -------
RequestParamMethodArgumentResolver |
RequestParamMapMethodArgumentResolver |
PathVariableMethodArgumentResolver |
PathVariableMapMethodArgumentResolver |
MatrixVariableMethodArgumentResolver |
MatrixVariableMapMethodArgumentResolver |
ServletModelAttributeMethodProcessor|
RequestResponseBodyMethodProcessor|
RequestPartMethodArgumentResolver|
RequestHeaderMethodArgumentResolver|
RequestHeaderMapMethodArgumentResolver|
ServletCookieValueMethodArgumentResolver|
ExpressionValueMethodArgumentResolver|

##### 2、支持类型

类名 | 说明
------- | -------
ServletRequestMethodArgumentResolver|
ServletResponseMethodArgumentResolver|
HttpEntityMethodProcessor|
RedirectAttributesMethodArgumentResolver|
ModelMethodProcessor|
MapMethodProcessor|
ErrorsMethodArgumentResolver|
SessionStatusMethodArgumentResolver|
UriComponentsBuilderMethodArgumentResolver|


#### RequestParamMethodArgumentResolver
spring会加入两个RequestParamMethodArgumentResolver，一个支持注解，一个支持简单类型

##### 支持的类型
* @RequestParam
* BeanUtils#isSimpleProperty()
* MultipartFile或者javax.servlet.http.Part

简单的说，就是如果参数使用了注解@RequestParam，或者是简单类型的，就会使用这个解析器

##### 解析过程

1. 解析参数为Object类型
2. 使用spring的类型转换系统

#### ServletModelAttributeMethodProcessor
spring会加入两个RequestParamMethodArgumentResolver，一个支持注解，一个支持用户自定义的类型，如`Person`

##### 支持的类型
* @ModelAttribute
* !BeanUtils#isSimpleProperty()

简单的说，就是如果参数使用了注解@ModelAttribute，或者是不是简单类型的，就会使用这个解析器

##### 解析过程

1. 反射实例化参数
2. 分别设置参数的属性
	1. 使用spring的类型转换系统


