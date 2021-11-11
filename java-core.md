# Java-Core

## Concurrency

### 1. Java Memory Model
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

### 2. ThreadLocal
> 强弱引用
>> - 强引用： 普通引用
>> - 弱引用（weak reference）：其指向的对象gc时会被回收
>> - 软引用（soft reference）：其指向对象在内存不足时会被回收
>> - 虚引用：不了解
>

> 为什么用弱引用？答：防止内存泄漏
>> 若是强引用，没有remove，且仅剩threadLocalMap里的引用时，这个无用的对象就彻底跟随这个线程了
>> 使用弱引用的话，仅剩这个弱引用时，gc时就会回收这个threadLocal对象，但这仅使Entry的key为null，
>> 整个无用的Entry还是存在，在下一次的set()、get()、remove()才会回收key为null的Entry。
>> 所以最好的实践还是手动调用remove方法。
>

## Java-Basic
> 1.类加载顺序
>> 父类优先、静态优先、代码块优先
>

> 2.StringBuffer、StringBuilder、String区别
>> String：默认创建在字符串常量池中
>> ```public static void main(String[] args){
>>             String a="abc";
>>             String b=new String("abc");
>>             String c=b.intern();
>>             System.out.println(b==a);
>>             System.out.println(b==c);
>>             System.out.println(c==a);
>>         }
>> ```
>> 输出结果为：false false true
>
>> StringBuffer线程安全、StringBuilder线程不安全;
>> 实现都是围绕char数组进行的，String中char数组是final修饰的，
>> 也没有提供可以修改char数组的方法，StringBuilder和StringBuffer中的char数组都可以修改，而提供的
>> 修改方法前者不是线程安全的，后者是。官方作者建议在非并发场景下使用前者，因为效率更高。
>  
>
> 3.Checked Exception VS Unchecked Exception
>> checked exception(父类是Exception,但不是RuntimeException): 显式声明在方法上，调用方须处理（throw or catch）,否则不能通过编译。
>> unchecked exception(父类是RuntimeException): 不用显示声明和处理，能通过编译，会在运行时抛出。
>
> 4.序列化（serialization）
>> transient关键字
>>> 默认情况下static修饰的、transient修饰的字段都不会被序列化，
>>> 除非自定义的writeObject和readObject方法中对该字段进行了序列化。
>
>> Serializable接口（参考javaDoc）
>>> serialVersionUID: 虽有默认值，但建议显示声明一个，因为不同的compiler implementation可能产生不同的默认值
 
 