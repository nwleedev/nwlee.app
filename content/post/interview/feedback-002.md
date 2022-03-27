---
title: 'Feedback 002'
description: '몰랐던 지식 정리'
date: 2022-01-19T22:03:00+09:00
image:
math:
license:
hidden: false
comments: true
draft: true
tags:
  [
    '의존성 주입',
    'Hilt',
    '프래그먼트',
    '액티비티',
    '생명주기',
    '네비게이션',
    'MVVM',
    '레포지터리',
    '뷰모델',
    'ViewModel',
    'Repository',
    'Fragment',
    'DI',
  ]
categories: ['피드백']
---

# 면접 피드백 002

## 프래그먼트를 사용하는 이유

- 매니페스트 파일에 따로 명시할 필요가 없다.
- 한 개의 액티비티 내에 여러 개의 프래그먼트를 배치함으로써 여러 개의 스크린을 화면에 나타낼 수 있다. 화면을 모듈화할 수 있다.
- 프래그먼트에 여러 개의 프래그먼트를 띄울 수 있다. 재사용이 용이하다.
- 네비게이션으로 프래그먼트 간 전환을 유도할 수 있으며 Safe Args로 매개변수의 전달을 보장할 수 있다.

## 프래그먼트 생명주기

다음과 같은 순서로 이루어진다.

![프래그먼트 생명주기](https://static.nwlee.com/public/KYM7JEO0/a655fcfd-855e-42ec-bea3-2d5e4a1ecadb.png)

1. Fragment CREATED: 프래그먼트가 CREATED 상태가 되면 프래그먼트 매니저가 추가되고 onAttach 함수가 호출된다. onCreate 함수가 호출되면 SavedInstanceState Bundle 객체로부터 임시 저장된 데이터를 가져온다.
2. Fragment CREATED and View INITIALIZED: 프래그먼트의 뷰 생명주기가 생성된다. onCreateView 함수를 호출하거나 Fragment 생성자에 레이아웃 아이디를 넘겨줌으로써 레이아웃을 인플레이팅할 수 있다. 뷰 객체는 getView 함수로 불러올 수 있다. onViewCreated 함수도 호출된다. 뷰에 대해 라이브데이터와 같은 초기 상태가 세팅된다.
3. Fragment and View CREATED: 프래그먼트의 뷰가 생성되면 이전에 저장된 뷰의 상태가 복원된다. 또한 뷰의 생명주기도 CREATED로 변경된다. 뷰의 생명주기 소유자(ViewLifeCycleOwner)는 관찰자들에게 ON_CREATE 이벤트를 송출한다. onViewStateRestored 함수가 실행된다.
4. Fragment and View STARTED: 프래그먼트 뷰의 생명주기가 시작된다. onStart 함수가 실행된다.
5. Fragment and View RESUMED: 프래그먼트가 보이게 된다. 사용자와의 상호작용을 할 수 있다. 프래그먼트의 생명주기는 RESUMED 상태가 되고 onResume 함수가 실행된다.
6. Fragment and View STARTED: 프래그먼트가 여전히 화면에 보여지는 상황과 동시에 사용자가 프래그먼트를 벗어나게 되면 프래그먼트와 뷰의 생명주기는 STARTED 상태로 돌아간다. onPause 함수가 실행된다. 프래그먼트 내 관찰자들에게 ON_PAUSE 이벤트를 보낸다.
7. Fragment and View CREATED: 프래그먼트가 더 이상 보이지 않게 되면 각각의 생명주기는 CREATED 상태로 진입하게 되며 관찰자들에게 ON_STOP 이벤트를 보낸다. 부모 액티비티 또는 프래그먼트가 멈출 때, 혹은 상태가 저장될 때 실행된다. 이는 프래그먼트의 상태가 저장되고 나서 ON_STOP 이벤트가 발생한다는 것을 보장한다. API 28 버전 전후로 onStop과 onSaveInstanceState 함수의 실행 순서가 다르다.
8. Fragment CREATED and View DESTROYED: 뷰가 분리되고 뷰의 생명주기는 DESTROYED가 된다. 관찰자들에게 ON_DESTROY 이벤트를 전송한다. 뷰의 생명주기는 종료된다. onDestroyView 함수가 실행된다.
9. Fragment DESTROYED: 프래그먼트, 프래그먼트 매니저가 없어지고 프래그먼트의 생명주기는 DESTROYED가 된다. onDestroy 함수가 실행된다.

프래그먼트의 생명주기는 프래그먼트 뷰의 생명주기와 다르다. UI를 업데이트할 때에는 뷰의 생명주기를 따라야 한다. 라이브데이터를 사용할 때 ViewLifeCycleOwner를 전달해야 옵저버가 중복 호출되는 문제를 방지할 수 있다.

## 액티비티 생명주기

1. onCreate: 액티비티가 생성될 때 실행된다. 전체 생명주기 동안 한 번만 발생한다. 데이터 바인딩, 뷰모델 연결 등등의 작업이 수행된다. Bundle 객체에서 저장된 데이터를 가져올 수 있다.
2. onStart: 액티비티가 사용자에게 표시된다. 앱은 액티비티를 사용자와 상호작용할 수 있도록 준비한다. UI 관련 코드를 초기화한다.
3. onResume: 앱이 사용자와 상호작용할 수 있다. 앱에서 포커스가 떠날 때까지 이 상태가 유지된다.
4. onPause: 전화가 오거나, 사용자가 다른 앱으로 이동하거나, 기기 화면이 종료될 때 등등, 사용자가 액티비티를 벗어났을 때 이 함수가 호출된다. 사용자가 필요로 하지 않는 리소스를 해제할 수 있다.
5. onStop: 액티비티가 사용자에게 더 이상 표시되지 않을 때 실행된다. 필요하지 않은 리소스가 해제되거나 조정된다. 데이터를 데이터베이스에 저장하는 것과 같은 종료 작업이 실행된다.
6. onDestroy: 사용자가 앱을 완전히 종료하거나 기기 회전 등의 특정 이벤트가 발생했을 때 실행된다. 기기 회전의 경우 ViewModel을 통해 현재 상태를 보존할 수 있다. 모든 리소스가 해제된다.

## 레포지터리

레포지터리 클래스는 데이터 저장소를 구현하고 데이터를 접근하는 로직을 캡슐화한다. 로컬 데이터베이스 또는 외부 HTTP API에서 데이터를 가져오는 방법을 정의할 수 있다. 뷰모델에서는 데이터를 불러오거나 저장하는 방식에 관여하지 않고 레포지터리에 요청하여 데이터를 업데이트할 수 있다.

### 캡슐화

객체를 잘 사용하기 위해 내부 상세 구현사항은 숨기고 인터페이스를 통해 객체를 사용하도록 하는 것

객체 지향 프로그래밍

- 클래스를 통해 변수, 함수를 논리적으로 하나로 묶어놓는다. 객체 외부에서는 해당 객체가 제공하는 변수, 함수만 사용할 수 있다.
- 클래스 내 변수들은 함수를 통해서 접근할 수 있다. 객체 요소에 대해 접근을 제한하는 방법이 제공된다.
- 내부 데이터의 변경을 어렵게 하고 보호할 수 있다.
- 객체 내의 데이터가 변경되어도 타 객체에는 영향을 주지 않는다. 클래스의 결합도가 낮아져 재사용을 용이하게 할 수 있다.

## 의존성 주입

하나의 객체가 다른 객체의 의존성을 제공하는 기법

의존성: 의존 대상이 변경될 때 어떤 객체에 영향을 미치는 상황, 특정 객체에 의존하는 상황

클래스 내부에서 객체를 생성하는 것이 아닌, 외부에서 객체를 생성하여 넣어준다. 객체를 생성하는 번거로움을 줄일 수 있다. 또한 클래스 간의 결합도를 낮출 수 있다.

의존성 주입의 장점

- 코드의 재사용성을 높여준다.
- 보다 유연한 코드를 작성할 수 있다.

안드로이드 앱 개발을 하면서 클래스 생성자에 의존관계를 주입시키는 방법을 자주 사용했다.

### Hilt

의존성 주입을 위해 보일러플레이트 코드를 줄일 수 있는 안드로이드 라이브러리이다. 안드로이드 클래스의 수명주기를 자동으로 관리함으로써 애플리케이션의 의존성 주입을 제공할 수 있다.

애플리케이션 클래스에 HiltAndroidApp 어노테이션을 명시함으로써 애플리케이션 레벨의 의존성 컨테이너 역할을 하게 한다. 이후 AndroidEntryPoint 어노테이션이 명시된 다른 안드로이드 클래스에 의존성 주입을 할 수 있다. 구성요소 계층 구조에 따라 각자의 상위 클래스에서 의존성 주입 객체를 받는다.

![Hilt 계층 구조](https://static.nwlee.com/public/KYM7JEO0/7d9a22a9-1990-422d-b5f3-4ada346882ed.png)

클래스의 생성자에 Inject 주석으로 클래스를 제공하는 방법을 알려줄 수 있다. 주석이 지정된 매개변수는 클래스의 의존성 객체라고 할 수 있다.

인터페이스 및 외부 라이브러리의 클래스와 같이 생성자에 의존성 제공이 불가능한 경우 Hilt 모듈을 정의하여 Hilt에게 객체를 어떻게 만들어야 하는지 알려줄 수 있다. InstallIn 주석으로 각 모듈을 사용하거나 설치할 안드로이드 클래스를 Hilt에게 알려줘야 한다.

Context가 필요한 경우 ActivityContext와 같은 주석으로 컨텍스트 객체를 의존성 주입할 수 있다.

## 뷰모델 by 키워드

### ViewModelProvider

액티비티 또는 프래그먼트를 뷰모델 인스턴스와 연결할 때 Provider를 사용할 수 있다. ViewModelProvider(this)를 호출하면 현재 액티비티와 연결된 ViewModelProvider 인스턴스를 생성 및 반환한다.

```Kotlin
class MainActivity : AppCompatActivity() {

  override fun onCreate(savedInstanceState: Bundle?) {
    setContentView(R.layout.activity_main)

    val provider: ViewModelProvider = ViewModelProvider(this)
    val imageViewModel = provider.get(ImageViewModel::class.java)

    // ...
  }
}
```

provider의 get 함수를 호출하면 ImageViewModel 인스턴스를 받을 수 있다. 액티비티에서 최초로 ImageViewModel을 요청하면 Provider에서는 새 뷰모델 인스턴스를 생성하고 반환한다. 화면 전환이 발생하면 onDestroy 함수가 발생하여 앱 내 모든 리소스가 초기화되고 전환이 완료되면 액티비티에서는 onCreate 함수를 호출한다. 이 때에는 뷰모델 인스턴스가 새로 생성되지 않고 최초에 생성된 인스턴스를 제공한다.

앱이 완전히 종료되면 뷰모델도 메모리에서 제거된다.

## by 키워드

by viewModel 키워드를 사용하면 ViewModelProvider를 사용하지 않고 뷰모델을 지연 생성할 수 있다.

```Kotlin
val imageViewModel: ImageViewModel by viewModels()
```

by 키워드를 통한 인스턴스 생성 방식을 위임 패턴이라고 한다.

## 위임 패턴

하위 클래스가 상위 클래스의 메소드 중 일부를 오버라이딩하면 하위 클래스는 상위 클래스의 세부 구현 사항에 의존하게 된다. 시스템이 변하면서 상위 클래스의 구현이 변경되거나 메소드가 추가될 수 있다. 하위 클래스에 오버라이딩된 코드에 오류가 발생할 수 있다.

코틀린은 기본적으로 클래스가 final로 설정되어 상속을 할 수 없게 돼있다. 상속을 허용하지 않는 클래스에 새로운 동작을 추가하기 위해서 데코레이터 패턴을 사용할 수 있다.

새로운 클래스를 생성한 뒤 기존 클래스의 인터페이스를 데코레이터가 제공하고 기존 클래스를 데코레이터 내부 필드로 유지하는 것이다. 다시 말해서, 새로 필요한 기능은 데코레이터 메소드에 새로 정의하고 기존 기능이 필요한 부분은 데코레이터의 함수가 기존 클래스의 함수에게 요청을 전달한다. 준비 코드를 작성해야 한다는 단점이 존재한다.

인터페이스를 구현할 때 by 키워드를 사용하면 인터페이스에 대한 구현을 다른 객체에 위임 중이라는 걸 알려줄 수 있다.

```Kotlin
class Counter<T>(
  val innerSet: MutableCollection<T> = HashSet<T>()
): MutableCollection<T> by innerSet {

  var objectsAdded = 0

  override fun add(element: T): Boolean {
    objectsAdded++
    return innerSet.add(element)
  }

  override fun addAll(c: Collection<T>): Boolean {
    objectsAdded += c.size
    return innerSet.addAll(c)
  }
}
```

MutableCollection은 인터페이스이고 기존의 HashSet이 인터페이스를 구현한다. add, addAll을 오버라이딩해서 카운터, objectsAdded를 증가시킨다. 나머지 메소드는 innerSet에 전부 위임한다. MutableCollection에 대한 의존관계가 형성되지 않는 특징이 있다. Counter에서 함수가 실행되면 Counter 내부에서 처리할 수도 있고, 위임대상의 함수를 활용할 수도 있다. 중요한 점은 인터페이스의 구성이 변경되더라도 Counter의 코드는 잘 작동함을 보장할 수 있다.

뷰모델을 선언할 때 by viewModels를 작성하면 현재 프래그먼트에 뷰모델 위임을 주입할 수 있다.
