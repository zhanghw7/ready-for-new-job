# Spring Framework

> 1.Why use spring?
>> a.IoC && AOP
>>> - IoC: inversion of control, 我的理解是实例化的地方发生变化，实例化交由第三方去做。
>>> - AOP: 实现原理就是代理模式，容器中生成的是代理对象。代理接口时用java原生动态代理，代理普通类时需要用到Cglib包
>> b.spring可以很方便整合其他框架
>
> 2.@Autowired VS @Resource
>> - 前者属于spring， 后者属于java；
>> - 前者只能用于spring，后者在其他ioc容器中也能用；
>> - 前者默认按类型装配，后者按名称。
>
> 3.spring bean的生命周期
>> 1. spring扫描生成BeanFactory(里面的BeanDefinitionMap，包含了所有待创建的bean的信息)
>> 2. BeanFactoryPostProcessor 可以对BeanFactory做一些修改
>> 3. 实例化bean
>> 4. 依赖注入、初始化、调用XXAware或其他和初始化相关的接口的实现（若有的话）
>> 5. 若有aop，生成代理对象
>> 6. 创建完成，放入单例池中
>
> 4.spring如何解决循环依赖
>> 1. 先实例化，放入map中，再去依赖注入；
>> 2. 由于考虑到并发和效率问题用到了二级缓存；
>> 3. aop生成代理对象的原因，用到三级缓存。