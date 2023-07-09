### 1: aop code,  use THIS of aop, record myLog


在 Spring AOP 中，`target`、`this` 和 `within` 都是用来匹配切入点的关键字。它们的作用和区别如下：
1. `target`：匹配目标对象，即被代理的对象。对于动态代理，目标对象是实现了被代理接口的类或者其子类。对于 CGLIB 代理，目标对象是被代理类的子类。例如：
   @Before("target(com.example.MyService)")
   public void beforeMethod() {}
   上面的代码表示匹配类型为 `com.example.MyService` 的目标对象。
2. `this`：匹配当前对象，即当前正在执行的对象。这个关键字只能用于代理对象，不能用于目标对象。例如：
   @Before("this(com.example.MyInterface)")
   public void beforeMethod() {}
   上面的代码表示匹配实现了 `com.example.MyInterface` 接口的当前代理对象。
   
3. `within`：匹配指定类型内部的所有方法，包括该类型的子类型的方法。例如：
   @Before("within(com.example.MyController)")
   public void beforeMethod() {}
   上面的代码表示匹配类型为 `com.example.MyController` 及其子类型的所有方法。
   需要注意的是，在使用 AOP 时，`target` 和 `this` 只能匹配到被代理对象或代理对象实现的接口上的方法调用，而不能匹配到代理对象自身的方法调用。如果需要匹配到代理对象自身的方法调用，可以使用 `@annotation` 和 `@within` 注解来实现。


#### a：AOP

```java
package com.morris.pojo;

import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.*;

import java.util.Arrays;

@Aspect
public class AOP {

    @Pointcut("this(com.morris.pojo.Roleimplement)")
    public void print() {
    }

    ;

    @Before("print()")
    public void beforeprint() {
        System.out.println("这是前置方法");
    }

    @After("print()")
    public void afterprint() {
        System.out.println("这是后置方法");
    }

    @AfterReturning("print()")
    public void afterreturning() {
        System.out.println("正常返回");
    }

    @AfterThrowing("print()")
    public void afterthrowing() {
        System.out.println("异常防护");
    }

    @Around("print()")
    public Object around(ProceedingJoinPoint joinPoint) {
        Object[] args = joinPoint.getArgs();
        System.out.printf("req: %s, name: %s.%s %n", Arrays.asList(args), joinPoint.getSignature().getDeclaringTypeName() ,joinPoint.getSignature().getName());
        try {
            Object proceed = joinPoint.proceed();
            System.out.println("return: " + proceed);
            return proceed;
        } catch (Throwable e) {
            e.printStackTrace();
            System.err.println(e.getMessage());
            return null;
        }
    }
}
```

#### b: 接口

```java
package com.morris.pojo;

public interface Roleinterface {
}
```

#### c：子类

```java
package com.morris.pojo;
 
public class Roleimplement implements Roleinterface{
	
	public void print() {
		System.out.println("this is AOP");
	}

	public void print2() {
		System.out.println("this is print2");
	}

}
```

#### d：test

```java

    @Test
    void contextLoaÎds() {
        AnnotationConfigApplicationContext annCtx = new AnnotationConfigApplicationContext(MorrisApplication.class);

        annCtx.start();
        Roleimplement temp = annCtx.getBean(Roleimplement.class);
        temp.print2();
    }
```

其他方式参考链接：https://www.jianshu.com/p/007bd6e1ba1b





