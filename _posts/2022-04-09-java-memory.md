---
layout: post
title: JVM Memory Structure
date: '2022-04-09 10:59:10 +0900'
description: 'JAVA의 메모리 구조를 알아보자.'
categories: [JAVA, JVM]
img: ''
tags: [JAVA, JVM]
---

## 1. JVM은 무엇인가
<hr>  
* JAVA Virtual Machine  
* JRE의 한 부분(구성요소)  
* OS에 영향을 받지 않고 클래스파일(ByteCode)을 실행  
* GC(메모리 관리)수행  

## 2. JVM은 무엇을 하는가
<hr>  
* 먼저 작성한 Java소스를 Java Compiler 의해 Class파일(ByteCode)로 변환한다.  
* 그후 프로그램을 실행시키면 JVM은 OS로부터 메모리를 할당받는다. 할당받은 메모리는 용도에 따라 나눠서 관리한다.  
* JVM에 적재된 ByteCode를 명령어 단위로 읽어서 실행다.  
![images_swcide_post_3c2e84d2-6508-49f2-859f-a380419ee4e8_jvm](https://user-images.githubusercontent.com/103012019/162865775-f44d1b6e-e7f1-4b9c-9811-7e2cc0ac00b3.png)

## 3. JVM의 Architecture
<hr>  
![jvm_architecture](https://user-images.githubusercontent.com/103012019/162580108-aa7d740d-4c1b-4e40-9171-44f31a9ee681.png)

### 3-1. Class Loader
프로그램이 실행되면 Class Loader는 필요한 ByteCode를 동적으로 읽어온다. 해당 단계에서 ByteCode를 검증하고 초기화하는 작업을 진행한다. 마지막으로 Runtime Data Area에 적재한다.

### 3-2. Runtime Data Area
프로그램이 실행되면서 할당받은 메모리 영역이다. 아래의 6개의 영역으로 세분화하여 메모리를 관리한다.
* Method Area
* Heap
* Stack
* PC Register
* Native Method Stack  

#### 1) Method Area
적재된 ByteCode들을 분석하여 클래스의 타입,변수,메소드 등의 정보를 저장한다. 이때 Class변수(static)도 함께 생성된다. 해당 영역은 정보만 저장할 뿐, 실제 데이터는 Heap에서 인스턴스하고 관리된다.

#### 2) Heap
클래스를 동적으로 생성하면 인스턴스가 메모리에 할당되는 영역이다. 또한 GC(Garbage Collection)의 대상이다.

> `Method Area`, `Heap`은 <mark>모든 Thread가 메모리를 공유하는 영역이다.</mark>

#### 3) Stack
메소드가 호출될 때 메소드의 스택프레임이 저장되는 영역.  
메소드가 호출되면 메소드의 지역변수, 매개변수 등 스택프레임에 저장하여 관리한다. 메소드가 호출이 완료되면 해당 스택프레임은 메모리에서 해재된다.  

#### 4) PC Register
Thread가 생성될 때 마다 생기는 메모리영역으로 Thread가 어떠한 명령을 실행하게 될지에 대한 부분을 기록을 한다.  

#### 5) Native Method Stack
자바 이외의 언어에서 제공되는 Method의 정보가 저장 되는 공간, JNI(Java Native Interface)를 통해 ByteCode로 변환 해준다.  

> `Stack`, `PC Register`, `Native Method Stack`은 <mark>각각 Thread마다 독립적으로 생성되는 영역이다.</mark>

### 3-3. Execution Engine
<hr>  
Class Loader를 통해 JVM 내의 Runtime Data Areas 에 배치된 바이트 코드는 Executin Engine에 의해 실행되며, 실행 엔진은 자바 바이트 코드를 명령어 단위로 읽어서 실행한다.
최초 JVM 이 나왔을 당시에는 Interperter방식(한 줄씩 해석하고 실행)이였기 때문에 속도가 느리다는 단점이 있었지만 JIT complier 방식을 통해 이 점을 보완했다.
