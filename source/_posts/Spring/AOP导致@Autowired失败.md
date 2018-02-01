---
title: AOP导致@Autowired失败
date: 2017-04-20 18:34:47
categories: Spring
---

今天给某个service方法加了一个切面，然后项目就启动报错了。

<!-- more -->

用简单的代码演示一下。

**service代码**
```
public interface StuentService {
    void print();
}
```

**service实现类代码**
```
@service("studentServiceImpl")
public class StuentServiceImpl implements StuentService{
    public void print(){
        //
    }
}
```

**controller代码**
```
@RestController
public class StudentController {

    @Autowired
    private StuentServiceImpl studentServiceImpl;
    
    //省略mapping方法
}
```

**当我加上切面时**
```
@Around("execution(* wiki.lxl.service.StudentService.print(..))")
public Object print(ProceedingJoinPoint joinPoint) throws Throwable {
    return joinPoint.proceed();
}
```

启动项目，报错了
`Error creating bean with name 'studentController'`，因为没有类型为`StuentServiceImpl`的bean，所以注入失败了。

把controller里的
```
@Autowired
private StuentServiceImpl studentServiceImpl;
```
改成
```
@Autowired
private StuentService studentServiceImpl;
```
问题就解决了。

因为AOP是基于代理模式的，在切print方法的时候，其实是生成了一个实现`StudentService`的代理类proxy，这个proxy类是无法把引用赋给`StudentServiceImpl`的。


## 结语

平时的《设计模式之禅》没有白看，虽然平时框架帮我们实现好了N多功能，我们也许很少用到其中的设计模式，但是看到源码就知道这里使用了哪种设计模式的能力，个人觉得还是很有必要的。