---
layout: post
title: JVM Memory Structure
date: '2022-04-09 10:59:10 +0900'
description: 'JAVA의 메모리 구조를 알아보자.'
categories: [JAVA, JVM]
img: ''
tags: [JAVA, JVM]
---

# 1. JVM은 무엇인가?
<hr>  
* JAVA Virtual Machine  
* JRE의 한 부분(구성요소)  
* OS에 영향을 받지 않고 클래스파일(ByteCode)을 실행  
* GC(메모리 관리)수행  

# 2. JVM은 무엇을 하는가?
<hr>  
* 먼저 작성한 Java소스를 Java Compiler 의해 Class파일(ByteCode)로 변환한다.  
* 그후 프로그램을 실행시키면 JVM은 OS로부터 메모리를 할당받는다. 할당받은 메모리는 용도에 따라 나눠서 관리한다.  
* 적재된 바이트코드를 OS가 인식 할 수 있는 기계어로 변환하는 작업을 한다.

# 3. JVM의 Architecture
<hr>  
![jvm_architecture](https://user-images.githubusercontent.com/103012019/162580108-aa7d740d-4c1b-4e40-9171-44f31a9ee681.png)

## 3-1. Class Loader
프로그램이 실행되면 Class Loader는 필요한 ByteCode들을 동적으로 읽어온다. 해당 단계에서 바이트코드를 검증하고 초기화하는 작업을 진행한다. 마지막으로 Runtime Data Area에 적재한다.
## 3-2. Runtime Data Area
프로그램이 실행되면서 할당받은 메모리 영역이다. 아래의 6개의 영역으로 세분화하여 메모리를 관리한다.
* Medhod Area
* Heap
* Stack
* PC Register
* Native Method Stack  

### 1) Method Area
적재된 ByteCode들을 분석하여 클래스의 타입,변수,메소드 등의 정보를 저장한다. 이때 Class변수(static)도 함께 생성된다.
### 2) Heap
클래스를 동적으로 생성하면 인스턴스가 메모리에 할당되는 영역이다. 또한 GC(Garbage Collection)의 대상이다.
* GC(Grabage Collection)  
자동으로 메모리를 관리하는 쓰레드. Heap에서 Unreachable Object들을 mark후 sweep하는 작업을 처리하여 Heap을 확보한다. Heap은 다음과 같은 영역으로 나뉜다.  

![image](https://user-images.githubusercontent.com/103012019/162654143-40796a83-7c79-4c10-9769-291d9945d03f.png)

* YG(Minor GC)  
__`Eden`__: 처음 객체가 생성될 때 적재되는 공간  
__`Survivor1,2`__: Eden의 메모리가 가득차면 `Minor GC`가 발생한다. 이때 reachable Object들을 Survivor영역으로 옮기는 작업을 한다.(단, Survivor영역의 한곳은 항상 비워둔다.)  

* OG(Major GC)  
YG영역에서 장기간 살아남은 객체들은 OG로 이동하게 된다.

> __YG/OG비교__  
> 메모리 크기: YG < OG  
> 처리빈도: YG > OG  
>
> 대부분의 객체는 장시간 참조되지 않는다. 그리고 오래된 객체는 새로 생성된 객체에 대해 대부분 참조하지 않는다. 따라서 YG의 `Minor GC`는 빈번하게 처리된다.

1. Eden이 가득차면 reachable Object을 탐색하고 Survivor1로 옮긴다. (Eden 메모리 초기화)  
2. 다음 `Minor GC`에서는 Eden, Survivor1을 탐색하고 Survivor2로 옮긴다. (Eden, Survivor1 메모리 초기화)  
3. 다음 `Minor GC`에서는 Eden, Survivor2를 탐색하고 Survivor1로 옮긴다. (Eden, Survivor2 메모리 초기화)  
4. 이 작업이 반복되면서 Survivor영역의 객체들은 Age값을 갖고, Survivor영역에서 다른 Survivor영역으로 이동될때 Age값이 증가한다. 
5. Age값이 설정된 임계치를 넘게되면, 해당 객체들은 OG영역으로 이동하게 된다.
6. OG의 메모리가 가득차면, `Magor GC`가 발생한다. OG의 reachable Object들을 탐색 후, 메모리를 정리한다.  

<img src="https://user-images.githubusercontent.com/103012019/162663678-68c1c8d9-3bd2-486b-b8a5-e56661c6ca3f.png" width="450" height="400">  
<img src="https://user-images.githubusercontent.com/103012019/162663759-0c1075b8-c29a-43df-b864-c5cd2d293cf8.png" width="450" height="408">  
<img src="https://user-images.githubusercontent.com/103012019/162664095-d8118ca7-00e9-4905-8583-ad37c5ada57f.png" width="450" height="400">  

> `Method Area`, `Heap`은 <mark>모든 Thread가 메모리를 공유하는 영역이다.</mark>

### 3) Stack
메소드가 호출될 때 메소드의 스택프레임이 저장되는 영역  
메소드가 호출되면 메소드의 지역변수, 매개변수 등 스택프레임에 저장하여 관리한다. 메소드가 호출이 완료되면 해당 스택프레임은 메모리에서 해재된다.  

### 4) PC Register
Thread가 생성될 때 마다 생기는 메모리영역으로 Thread가 어떠한 명령을 실행하게 될지에 대한 부분을 기록을 한다.  

### 5) Native Method Stack
자바 이외의 언어에서 제공되는 Method의 정보가 저장 되는 공간, JNI(Java Native Interface)를 통해 바이트코드로 변환 해준다.  

> `Stack`, `PC Register`, `Native Method Stack`은 <mark>각각 Thread마다 독립적으로 생성되는 영역이다.</mark>

## 3-3. Execution Engine
<hr>  
최종적으로 실행될 ByteCode를 기계어로 변역하는 영역이다.

# 4. GC알고리즘
<hr>  
* Serial GC
* Parallel GC
* CMS (Concurrent Mark & Sweep) GC
* 가비지 퍼스트 GC(G1GC)  

# 5. 요점정리
<hr>  
