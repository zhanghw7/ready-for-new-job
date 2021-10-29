# Java-Core

## Concurrency

1. Java Memory Model
> JMM是java定义的一种规范，包括指令“重排序”、“线程本地缓存和公共缓存”。性能优化的同时也带出了两个问题，
> 可见性和重排序造成的逻辑错误。若要禁止重排序和解决可见性问题，可以使用volatile关键字。
>
> 还有一个happens before规范，即对于被volatile修饰的变量var之前的变量的修改，在var之后被访问时是具有可见性的。 
> synchronized等也有这个特性。
>
> [图片](pic/happens-before.jpg)

>> 实例：double-checked-locking singleton
>> ``` 
>> public class Resource {
>>       //创建单例,提供对外的获取方法
>>       private static volatile Resource resource;
>>   
>>       /**
>>        * first check: 提升效率，如果单例已被创建就不需要再去获取锁了
>>        * second check: 防止并发问题
>>        */
>>       public static Resource getResourceSingleton(){
>>           if (resource == null){
>>               synchronized (Resource.class){
>>                   if (resource == null){
>>                       // resource使用volatile修饰，禁止重排序，防止在first check处获取到一个未构造完全的实例
>>                       resource = new Resource("f1", "f2", "f3");
>>                   }
>>               }
>>           }
>>           return resource;
>>       }
>>   
>>       // 需要创建单例的资源
>>       private String f1;
>>       private String f2;
>>       private String f3;
>>   
>>       private Resource(String f1, String f2, String f3) {
>>           this.f1 = f1;
>>           this.f2 = f2;
>>           this.f3 = f3;
>>       }
>>   }
>>
>> ```