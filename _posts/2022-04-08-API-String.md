---
layout: post
title: String, String Builder, and String Buffer
date: '2022-04-08 10:59:10 +0900'
description: 'Difference between String, String Builder, and String Buffer.  '
categories: [JAVA, JAVA Basic]
img: ''
tags: [JAVA, String]
---

## #String 클래스

자바에서 문자열을 처리하는 클래스는 __`String`, `String Builder`, `String Buffer`__ 가 존재한다.  
<br>

__Q. 이 3가지의 차이점은 무엇인가?__  
* 우선 __`String`__(불변,Immutable)과 __`StringBuilder,StringBuffer`__(가변,mutable)으로 구분할 수 있다.  
   
> __`String`__ 클래스는 한번 메모리에 올라간 후, 수정이 발생하면 그의 메모리의 값을 변경하는 것이 아닌, 또 다른 객체를 생성하여 새롭게 메모리 참조를 하게된다. 이러한 이유로 __`String`__ 에 대한 문자열 연산이 빈번할 수록 성능이 저하될 수 있다.

<br>
<br>

__Q. 이런 단점이 존재하는데 왜 사용하는걸까?__  
* 대부분의 문자열은 복잡한 연산을 처리하기 보다, 한번 설정된 문자열을 여러곳에서 공유하는 경우가 많다.  
따라서 객체 또는 쓰레드에서의 공유에서 synchronization(동기화)와 같은 장치가 없어도 안전하게 공유 할 수 있다.

> __`StringBuilder,StringBuffer`__ 클래스는 한번 메모리에 올라간 후, 수정이 발생하면 새로 객체를 만드는 것이 아닌,  
> 기존의 메모리의 값을 변경한다. 문자열 연산이 빈번한 경우 __`String`__ 대신 사용하면 성능이 좋다.

<br>
<br>

__Q. `StringBuilder`,`StringBuffer`의 차이점은?__  
* 동기화 유무에 따른 멀티쓰레드에서의 안전성이다.  
__`StringBuilder`__ 는 동기화를 지원하지 않는다. 단일 쓰레드에서의 문자열 연산에 유리하다.  
__`StringBuffer`__ 는 동기화를 지원한다. 멀티 쓰레드에서 사용해도 안전하게 문자열 연산을 처리 할 수 있다. 하지만 성능면에서 가장 느리다.

<br>
<br>

> __<mark>NOTE</mark>__  
> * String객체 생성에는 2가지 방법이 존재한다
> 1. 리터럴 선언
> 2. new 생성자 호출  
>
> __`리터럴 선언`__ 의 경우, `String constant pool`을 참조.  
> 동일한 값이 있을 겨우, 해당 주소를 반환.  
> 동일한 값이 없을 경우, 새로운 주소에 값을 저장.  
> * JAVA8부터 StringPool은 Perm영역에서 Heap영역으로 이동(OOM문제로 인해), GC대상으로 변경됨.  
> 
> __`new 생성자 호출`__ 의 경우, 동일한 값이 존재 여부에 상관없이 새로운 객체를 생성.

```java
public class StringMemoryIntern {
    public static void main(String... args) {
        String l1 = "str"; // 리터럴 선언
        String l2 = "str"; // 리터럴 선언
        String o1 = new String("str"); // 생성자 호출
        String o2 = new String("str"); // 생성자 호출
        
        // == 연산자는 객체의 메모리 주소를 비교하는 연산자이다.(값을 비교하지 않음)
        System.out.println(l1 == l2); // true   String pool참조에 의해 동일한 메모리 주소 참조
        System.out.println(l1 == o1); // false  o1은 새로운 객체에 생성되어 메모리 주소가 다름
        System.out.println(o1 == o2); // false  o1, o2는 각각 다른 객체이기 떄문에 메모리 주소가 다름
        
        // equals()메소드는 메모리의 값을 비교. 주소값이 달라도 메모리의 값을 비교한다.
        System.out.println(l1.equals(o1)); // true
        System.out.println(o1.equals(o2)); // true
    }
}
```
<br>
<br>

| 클래스(java.lang) | 상세 | Thread Safe(동기화) |
|:--------|:-------|:-------|
| String   | 문자열을 저장하고 여러가지 정보를 얻을 때 사용| O |
| StringBuilder   | 문자열을 저장하고 내부 문자열을 조작할 때 사용| X |
| StringBuffer   | 문자열을 저장하고 내부 문자열을 조작할 때 사용| O |  

## #String
> * 문자열을 저장하고 여러 가지 정보를 얻을 때 사용  
> 문자열의 수정이 일어 날 경우, 해당 String객체를 수정하는 것이 아닌 새로운 String객체를 생성(=불변).

## #String Builder, String Buffer
> * 내부 Buffer(:데이터를 임시로 저장하는 메모리)에 문자열을 저장해두고, 그 안에서 추가, 수정, 삭제 작업을 할 수 있도록 설계되어 있다. 즉, String처럼 새로운 객체를 만들지 않고도 문자열을 조작할 수 있다.
>  
> __`String Builder`__
> : 단일 스레드 환경에서만 사용하도록 설계
> 
> __`String Buffer`__
> : 멀티 스레드 환경에서 사용할 수 있도록 동기화가 적용되어 있다(thread-safe)
