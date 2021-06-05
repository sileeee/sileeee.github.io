---
title: 의존성 주입<br>(Dependency Injection)
tags:
- Spring
author: Sieun
layout: post
excerpt_separator: "<!--more-->"
---

<br>
## 의존성 주입(DI)
>필요한 객체를 직접 생성하는 것이 아닌 외부로 부터 필요한 객체를 받아서 사용하는 것


Spring은 @Autowired 어노테이션을 이용한 다양한 의존성 주입 방법을 제공한다.
@Autowired 어노테이션은 Spring에게 의존성을 주입하라는 지시자 역할로 쓰이는데 생성자, field, setter에 붙일 수 있다.

<br><br>
### @Autowired를 사용하는 방법
#### 1) field로 주입받는 경우<br>
{% highlight ruby %}
@Autowired
private OwnerRepository owners;
{% endhighlight %}
<br>
#### 2) setter로 주입받는 경우<br>
{% highlight ruby %}
@Autowired
public void setOwners(OwnerRepository owners){
	this.owners = owners;
}
{% endhighlight %}
<br>
#### 3) 생성자로 주입받는 경우<br>
스프링 4.3부터 어떤 클래스의 생성자가 하나 뿐이고 그 생성자로 주입받는 참조변수들이 빈으로 등록되어 있다면, 그 빈을 자동으로 주입해주도록 기능이 추가되었다. 그래서 @Autowired를 생략할 수 있다.
{% highlight ruby %}
public OwnerController(OwnerRepository clinicService){
	this.owners = clinicServiec;
}
{% endhighlight %}
보통 생성자를 사용하여 의존성 주입하는 방법이 주로 권장된다. 그 이유는 필수적으로 사용해야하는 참조변수 없이는 인스턴스를 만들지 못하도록 강제할 수 있기 때문이다. (field와 setter를 사용하면 강제할 수 없다.)

<br>
field, setter을 사용하면 bean으로 등록되지 않은 참조변수로(즉, 의존성 없이도) 인스턴스를 만들 수 있다. 이는 장점이자 단점인데, 장점으로 작용하는 경우는 순함참조와 관련이 있다.

#### 순환참조
`A를 B가 참조하고, B가 A를 참조`<br>
이 경우, A와 B 둘 다 생성자주입을 사용하면 만들지 못한다. 하지만 field injection 또는 setter injection을 사용하면 인스턴스를 만든 다음 서로의 인스턴스를 주입할 수 있어 상호참조하는 의존성 문제가 해결가능하다.<br>
그러나 가급적 상호참조가 발생하지 않도록 의존성을 조율하는 것을 권장한다.

<br> 
<hr>
<br>
### 해당 타입의 빈이 여러 개인 경우
- @Primary를 붙여서 사용하고 싶은 빈을 벤치마킹
`@Repository @Primary`
- 해당 타입의 빈 모두 주입 받기
- @Qualifier을 사용하여 사용하고 싶은 빈을 벤치마킹
`@Autowired @Qualifier("BookRepository")`
<br><br>
#### 동작원리
* BeanPostProcessor : 새로 만든 빈 인스턴스를 수정할 수 있는 라이프 사이클 인터페이스
* AutowiredAnnotationBeanPostProcessor (extends BeanPostProcessor) : 스프링이 제공하는 @Autowired와 @Value 어노테이션 그리고 JSR-330의 @Inject 어노테이션을 지원하는 어노테이션 처리기
<br><br>
#### < Spring Bean Life Cycle >
![image](https://user-images.githubusercontent.com/31584255/120883225-e811e880-c616-11eb-9b49-c2ecc20af743.png)
<br>
Spring Bean Life Cycle을 참고하여 보면 BeanFactory가 BeanPostProcessor타입의 빈을 찾는데, 그 중의 하나가 AutowiredAnnotationBeanPostProcessor이다. 그래서 다른 일반적인 빈들에 BeanPostProcessor의 로직을 적용하는 것이다.
