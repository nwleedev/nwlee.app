---
title: '코틀린 기초 지식 정리'
description: '코틀린에서 제공하는 여러가지 문법에 대해 정리해봤습니다.'
date: 2022-01-05T21:24:31+09:00
image:
math:
license:
hidden: false
comments: true
draft: true
tags:
  [
    '코틀린',
    'Kotlin',
    '확장 함수',
    'Extension',
    '스코프 함수',
    'Scope Functions',
  ]
categories: ['코틀린']
---

# 코틀린 문법 정리

코틀린은 모든 자료형이 객체로 취급된다. 코틀린은 파일 최상단(Top Level)에 클래스 뿐만 아니라 함수 및 변수도 선언할 수 있다. 코틀린은 클래스를 상속하지 않고도 기능을 추가할 수 있는 확장 함수 개념을 제공한다. 정수형도 객체로 취급되므로 확장 함수를 선언할 수 있다.

```Kotlin
// Example.kt
val title = "Hello World!"

fun printOrNull(text: String?) {
    when (text) {
        null -> println("null")
        else -> println(text)
    }
}

fun Int.isZero(): Boolean {
    return this == 0
}
```

## 클래스 접근 제한자

코틀린에선 클래스, 싱글톤 객체(Object), 생성자, 함수, 객체 내 속성, 변수 등에 접근 제한자를 명시해 줄 수 있다. 예를 들어 함수 및 변수에 private을 명시하면 그 함수 및 변수는 자신이 선언된 파일 외부에서 사용할 수 없다.

```Kotlin
private const val TAG = "HomeFragment"

private fun canIUseThisOtherFiles() {
  println("NO")
}
```

접근 제한자는 public, private, internal, protected 이며, 기본적으로 public이 적용된다.

public은 클래스, 또는 파일 외부에서 접근 가능하다. private은 자신이 선언된 클래스(변수, 함수의 경우에는 파일) 내부에서만 접근할 수 있다.

패키지 기준으로는 protected 키워드는 파일 최상단에서 사용할 수 없다. internal 키워드는 같은 모듈 내 파일끼리 사용 가능하다.

클래스에서는 protected는 private처럼 클래스 내부에서만 접근할 수 있지만, 클래스를 상속받는 하위 클래스에서도 사용할 수 있는 점이 다르다. internal은 해당 클래스가 선언된 모듈 내에서 다른 클래스가 접근할 수 있다.

클래스 내 속성을 상속받도록 하기 위해선 해당 속성에 open 키워드를 명시해야 한다.

```Kotlin
open class Person (
    protected open val name: String,
    protected open val age: Int,
    private val capital: Int?,
) {

    internal open fun introduce() {
        println("I am $name, $age years old.")
    }

    protected open fun showCapital() {
        println("I have ${capital ?: 0} dollars")
    }
}

class Student (
    override val name: String,
    override val age: Int,
    private val capital: Int,
    private val major: String
) : Person(name, age, capital) {

    override fun introduce() {
        println("I am $name, $age years old, $major student, with $capital dollars.")
    }

    internal fun addMoney(money: Int): Student {
        return Student(name, age, capital + money, major)
    }

    override fun showCapital() {
        super.showCapital()
        println("It is overridden")
    }
}

class Outer(student: Student) {
    init {
        student.introduce()
        student.addMoney(100).introduce()

        // ERROR
        student.showCapital()
        println(student.name)
    }
}
```

Student 클래스에서 internal로 선언된 함수는 같은 모듈 내 다른 클래스, Outer에서도 사용 가능하다. 상위 클래스 Person에서 protected가 명시된 showCapital의 경우 하위 클래스에서 상속받을 수 있지만 타 클래스에서는 사용할 수 없다.

함수 및 속성을 오버라이딩 가능하게 하기 위해선 open 키워드를 명시해줘야 하고 protected, internal도 상속받을 수 있게 하려면 open을 작성해줘야 한다. 상속받은 속성 및 함수들도 접근 제한자는 동일하다. Outer 클래스에 생성자로 주어진 Student 인스턴스는 Person에서 protected 키워드가 명시된 name에 접근할 수 없다.

## 스코프 함수

람다 식을 이용해서 일시적인 스코프를 생성한 다음, it 또는 this 키워드로 객체에 접근할 수 있다. 스코프 내에서 객체를 그대로 사용할 수 있기 때문에, 객체의 이름을 일일이 작성할 필요가 없어지는 장점이 있다. 때에 따라서는 null 타입 체크도 가능하다.

```Kotlin
val text: String? = "Hello World!"

text?.also {
  println(it)
}
```

변수 text가 null 타입이 아니라면 println 함수를 실행시키도록 할 수 있다.

### 컨텍스트 객체

람다 식에서 생성된 스코프에 전달된 람다 수신자, 또는 인자를 컨텍스트 객체라고 부른다. this, it으로 구분된다.

**this**

- 수신 객체의 속성에 바로 접근할 수 있다.
- this를 명시할 필요가 없어 코드의 길이가 줄어드는 장점이 있다.
- this를 생략할 경우 외부 함수와 구분하기 어려울 수 있다.

**it**

- 람다 식에 매개변수로 넘겨줄 때 이름을 따로 지정할 수 있다.
- it 키워드를 생략할 수 없다.

### 함수

**let**

람다 내 컨텍스트 객체는 람다 인자 it이다. 반환 값은 람다 식의 결과값이 된다. let은 여러 개의 함수를 체이닝 형태로 계속적으로 호출할 때 사용할 수 있다. 람다 식 내 하나의 매개변수만 받는 함수의 경우 메소드 참조를 사용할 수 있다. ? 연산자와 같이 사용하면 수신 객체가 null이 아닐 때에만 람다 식이 작동하도록 할 수 있다.

```Kotlin
intOrNull?.let(::println)
```

**run**

컨텍스트 객체가 람다 수신자이다. 반환 값은 람다 식의 결과값이다. 수신자를 호출하지 않고도 run을 사용할 수 있다. 이 때는 스코프 내부에서 여러가지 함수를 실행할 수 있다.

**apply**

컨텍스트 객체가 람다 수신자이다. 반환 값은 람다 식에 넘겨진 컨텍스트 객체이다.

**also**

컨텍스트 객체가 람다 인자이다. 반환 값은 람다 식에 넘겨진 컨텍스트 객체이다.

```Kotlin
val nums = mutableListOf(1, 2, 3)
nums
  .also { println(it) }
  .add(4)
```

also 식에서 리스트 값을 전부 출력하고 난 뒤 람다 인자로 주어진 리스트를 반환값으로 받아서 리스트에 요소를 추가할 수 있다.

**with**

컨텍스트 객체는 람다 인자이다. 람다 식 내부에서는 수신자로 사용할 수 있다. 반환값은 람다 식의 결과값이다.

## 코루틴

코루틴은 동시성 프로그래밍 패턴이다. 함수 실행 도중 정지하고 재개할 수 있는 정지 함수를 제공한다. 정지 함수를 통해 하나의 스레드에서 여러 개의 코루틴을 실행시킬 수 있다. 안드로이드의 경우 자체 코루틴 스코프도 지원한다. 대표적으로 뷰모델에서 사용할 수 있는 viewModelScope가 있다. 코루틴 스코프는 launch 또는 async로 실행된다.

코루틴을 async로 실행하면 최종 await 호출을 할 수 있는 Deferred 객체를 반환받는다. 이 객체가 리스트 형태로 이루어져 있으면 awaitAll 함수를 호출해 결과값들을 한 번에 받아올 수 있다.

안드로이드에서는 UI 작업을 메인 스레드에서 처리한다. 메인 스레드에서 네트워크 요청 및 데이터베이스와 같이 시간이 많이 소요되는 작업을 실행시키면 앱이 중단될 수 있다. withContext 함수를 이용하여 작업이 어느 스레드에서 진행될지 알려줄 수 있다. 예를 들어 withContext(Dispatchers.IO)를 통해 작업을 IO 스레드에서 실행하도록 한다. IO 스레드에서 장시간의 작업이 끝날 때까지 viewModelScope는 재개되지 않고 메인 스레드는 나머지 UI 작업을 계속할 수 있다.

정지 함수는 코루틴 스코프 내에서 실행되어야 한다. viewModelScope, lifeCycleScope와 같이 미리 정의된 코루틴 스코프를 사용할 수 있다.

```Kotlin
viewModelScope.launch(Dispatchers.Main) {
  // UI 작업
  val data = repository.fetchData()
  // 메인 스레드에서 작업 재개
}
```

레포지터리에서 데이터를 불러오는 동안 해당 코루틴은 정지 상태가 된다. viewModelScope가 정지되어도 나머지 UI 작업은 정상적으로 진행된다. 데이터를 다 불러오고 나면 다시 메인 스레드에서 작업을 재개한다.
