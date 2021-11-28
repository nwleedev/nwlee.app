---
title: '코틀린 안드로이드 코루틴 예외 처리'
description: '코틀린 안드로이드 프로그래밍할 때 코루틴에서 예외 처리가 발생할 때 사용했던 방법입니다.'
date: 2021-11-28T22:02:19+09:00
image:
math:
license:
hidden: false
comments: true
draft: true
tags: ['Kotlin', 'Android', 'Coroutine', 'Error', 'Exception', 'Handling']
categories: ['Android']
---

# 안드로이드 코루틴 예외 처리

## 코루틴과 정지 함수

이번 안드로이드 프로젝트에서 MVVM 패턴을 사용했다. 뷰모델 내 코루틴(viewModelScope) 내에서 네트워크 요청한 뒤 데이터를 받으면 라이브데이터에 값을 업데이트해주는 방식이다.

레포지터리에서 네트워크를 요청하는 함수는 정지(suspend) 함수로 선언했다. 정지 함수는 특정한 시점에 멈췄다가 다시 실행할 수 있는 함수이다. 네트워크 요청 또는 파일 입출력 등의 시간이 오래 걸리는 작업을 할 때 함수를 정지한 다음, 원하는 데이터가 도착했을 때 다시 재개시키는 방식이었다. 정지 함수는 코루틴 내부에서 실행되고 해당 함수가 정지해 있는 동안에는 다른 함수 동작에 영향을 끼치지 않는다.

안드로이드 UI는 메인 스레드에서 작동하기 때문에 네트워크 통신이나 파일 입출력은 IO 스레드에서 동작하도록 했다. viewModelScope는 메인 스레드에서 작동하기 때문에 레포지터리의 함수에는 withContext(Dispatchers.IO)가 사용되었다.

예외 처리하기 전의 예시 코드이다.

네트워크 통신에는 Retrofit2를 사용했다. Service 인터페이스에서 loadDetailById 함수는 미리 정의되어 있다고 가정한다.

- 뷰모델

```Kotlin

private var _detail = MutableLiveData<DetailData>()
val detail: LiveData<DetailData> get() = _detail

fun loadDetailById(id: String) {
    viewModelScope.launch {
        val data = repository.loadDetailById(id)

        data?.also {
            _detail.value = it
        }
    }
}
```

- 레포지터리

```Kotlin
suspend fun loadDetailById(id: String): DetailData? {
    return withContext(Dispatchers.IO) {
        val apiKey = BuildConfig.API_KEY
        val call = service.loadDetailById(id, apiKey)
        val resp = call.awaitResponse()
        if (!resp.isSuccessful || resp.body() == null) {
            Log.d(TAG, resp.errorBody().toString())
        }
        resp.body()?.data
    }
}
```

레포지터리에서 네트워크를 요청하는 loadDetailById 정지 함수는 네트워크 통신이 성공적으로 끝나면 데이터를 반환할 것이다.

## 예외 처리

실제로 에뮬레이터로 테스트를 하면서 다양한 예외 상황에 부딪히게 되었다.

대표적으로 두 가지의 예외가 자주 발생했다.

- SocketTimeoutException
- JSONSyntaxException

SocketTimeoutException은 네트워크 요청할 때 소켓에서 일정 시간이 초과하면 발생하는 예외이다. JSONSyntaxException은 Gson에서 정의한 데이터의 속성 타입이 실제 데이터와 맞지 않을 때 발생하는 예외였다.

JSON 데이터가 다음과 같을 때

```JSON
{
  "item": "Hello World!"
}
```

코틀린 데이터 클래스 내 item 타입이 문자열이 아닐 경우에 발생한다.

```Kotlin
data class DetailData {
  // ...
  val item: Int,
  // ...
}
```

처음에는 타 언어에서 사용하던 습관대로 Try Catch 블록으로 각 예외 상황이 발생하면 그에 따른 처리를 해줬다.

예시

```Kotlin
suspend fun loadDetailById(id: String): DetailData? {
    return withContext(Dispatchers.IO) {
        try {
            val apiKey = BuildConfig.API_KEY
            val call = service.loadDetailById(id, apiKey)
            val resp = call.awaitResponse()
            if (!resp.isSuccessful || resp.body() == null) {
                Log.d(TAG, resp.errorBody().toString())
            }
            resp.body()?.data
        } catch (e: JsonSyntaxException) {
            Log.d(TAG, e.message.toString())
            null
        } catch (e: SocketTimeoutException) {
            Log.d(TAG, e.message.toString())
            null
        }
    }
}
```

이렇게 작성하면 모든 예외 상황마다 같은 코드를 작성해야 하는 불편함이 있었다. 예외 또는 에러 타입이 Error로 간주되는 타입스크립트와는 다르게 자바, 코틀린에서는 예외 상황에 대한 다양한 타입이 있었다.

그래서 모든 예외나 에러에 기본(Base) 클래스가 되는 Throwable로 에러 타입을 명시해줬다.

```Kotlin
suspend fun loadDetailById(id: String): DetailData? {
    return withContext(Dispatchers.IO) {
        try {
            val apiKey = BuildConfig.API_KEY
            val call = service.loadDetailById(id, apiKey)
            val resp = call.awaitResponse()
            if (!resp.isSuccessful || resp.body() == null) {
                Log.d(TAG, resp.errorBody().toString())
            }
            resp.body()?.data
        } catch (t: Throwable) {
            Log.d(TAG, t.stackTraceToString())
            null
        }
    }
}
```

멘토링에서 Try Catch 구문을 반복해서 작성하는 것은 좋지 않다는 멘토님의 말씀을 듣고 코루틴 예외 처리를 어떻게 하는지 좀 더 자세히 알아봤다. 네트워크를 요청하는 함수마다 try catch 구문을 작성해야 되는 건 솔직히 귀찮은 작업이었다. try catch 구문을 사용하지 않는다면 코드의 깊이(Depth)도 줄일 수 있을 것이다.

## CoroutineExceptionHandler의 사용

CoroutineExceptionHandler을 사용해서 하위 코루틴에서 발생한 예외 상황들을 전역적(Global)으로 처리할 수 있다. Thread.UncaughtExceptionHandler와 비슷하게 작동한다고 공식 문서에 적혀있다.

```
UncaughtExceptionHandler는 스레드에서 예상하지 못한 예외 상황이 발생할 때 작동할 핸들러를 명시한 인터페이스이다. 만약 스레드가 예외로 인해 종료된다면 JVM에서 Thread.getUncaughtExceptionHandler 메소드를 호출하고 작성된 핸들러의 uncaughtException 메소드를 호출한다.
```

CoroutineExceptionHandler 또한 미처 처리하지 못한 예외 상황이 발생했을 때 호출된다. 이를 뷰모델에서 사용하려면 다음과 같이 작성하면 된다.

```Kotlin
open class BaseViewModel: ViewModel() {
    private val coroutineExceptionHandler = CoroutineExceptionHandler { _, throwable ->
        Log.d("TAG", throwable.stackTraceToString())
    }

    protected val viewModelDispatcher = Dispatchers.Main + coroutineExceptionHandler
}
```

모든 뷰모델이 상속받을 수 있도록 Open 클래스를 하나 생성해 준다. ViewModel 클래스를 상속받는 BaseViewModel은 Dispatchers.Main과 coroutineExceptionHandler로 구성된 viewModelDispatcher 코루틴 컨텍스트가 하나 정의되어있다. BaseViewModel 클래스를 상속받는 뷰모델은 공통적으로 viewModelDispatcher를 사용할 수 있다.

```Kotlin
fun loadDetailById(id: String) {
    viewModelScope.launch(viewModelDispatcher) {
        val data = repository.loadDetailById(id)

        data?.also {
            _detail.value = it
        }
    }
}
```

특정한 예외 상황을 따로 처리하려면 Try Catch를 사용하는 수 밖에 없다. 예를 들어 Rest API에서 제공받는 데이터의 구조가 일관되지 않을 때가 있다.

```JSON
{
  "item" : [{}, {}]
}
```

```JSON
{
  "item" : {}
}
```

```JSON
{
  "item": "DATA"
}
```

```JSON
{
  "item": null
}
```

무슨 타입의 데이터를 받을지 예측할 수 없는 경우에는 직접 예외 처리를 해줬다.

```Kotlin
suspend fun loadDetailById(id: String): List<DetailData> {
    return withContext(Dispatchers.IO) {
        val jsonText = try {
            val apiKey = BuildConfig.API_KEY
            val call = service.loadDetailById(id, apiKey)
            val response = call.awaitResponse()
            if (!response.isSuccessful || response.body() == null) {
                Log.d(TAG, response.errorBody().toString())
            }

            val items = response.body()?.data
            gson.toJson(items)
        } catch (t: Throwable) {
            Log.d(TAG, t.stackTraceToString())
            ""
        }

        try {
            gson.fromJson(jsonText, DetailList::class.java).item
        } catch (e: JsonSyntaxException) {
            try {
                val data = gson.fromJson(jsonText, DetailItem::class.java)
                listOf(data.item)
            } catch (e: JsonSyntaxException) {
                Log.d(TAG, e.toString())
                listOf()
            }
        } catch (t: Throwable) {
            Log.d(TAG, t.stackTraceToString())
            listOf()
        }
    }
}
```

response.body()?.data의 타입은 Any이다. data의 item 속성이 무슨 타입을 가지고 있는지 알 수 없었기 때문에 Gson으로 직렬화를 했다. 그리고 try catch 구문 내에서 각각의 타입에 해당하는지 찾아내기 위해 역직렬화를 시도했다.

첫 번째 try 블록 내에서 역직렬화가 성공하면 item 속성의 타입은 리스트이고, 두 번째 try 블록 내에서 역직렳화가 성공하면 item은 클래스의 형태를 띄고 있을 것이다.

모든 타입에 대한 역직렬화가 실패할 경우 어쩔 수 없이 비어있는 리스트를 반환하도록 했다.
