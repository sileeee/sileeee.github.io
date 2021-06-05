---
title: 스프링 AOP<br>(Aspect Oriented Programming)
tags:
- Spring
author: Sieun
layout: post
excerpt_separator: "<!--more-->"
---

### AOP(Aspect Oriented Programming)
> 관점지향 프로그래밍


공통적인 일을 같은 클래스의 다른 메소드 또는 다른 클래스의 다른 메소드에서도 하는 경우, 공통적인 일들을 따로 모아 처리하도록 만드는 개념이다.
<br>만약, 똑같은 코드가 흩어져 있다면 해당 코드 중 한 부분만 고치려고 해도 코드를 다 찾아서 고쳐야한다. AOP는 이러한 번거로움을 줄여줄 수 있다. 즉, 흩어진 관심사를 Aspect로 모듈화하고 핵심적인 비즈니스 로직에서 분리하여 재사용하겠다는 것이 AOP의 취지이다.
<br><br>
### AOP구현 방법
1) 컴파일 이용 : .java에서 .class로 컴파일할 때 조작<br>
2) 바이트코드 조작 : 클래스 로더가 .class를 읽어와서 메모리에 올릴 때 조작<br>
3) 프록시 패턴 : 디자인 패턴 중 프록시 패턴을 사용하여 AOP와 같은 효과를 낸다.<br>

#### < 예제 - 프록시 패턴 기반 >

{% highlight ruby %}
@Component
@Aspect
public class LogAspect {
	Logger logger = LoggerFactory.getLogger(LogAspect.class);

	@Around("@annotation(LogExecutionTime)")
	public Object logExecutionTime(ProceedingJoinPoint joinPoint)throws Throwable{
		StopWatch stopWatch = new StopWatch();
		stopWatch.start();

		Object proceed = joinPoint.proceed();
		stopWatch.stop();
		logger.info(stopWatch.prettyPrint());
		return proceed;
	}
}
{% endhighlight %}
