---
title: '안드로이드에서 뷰 홀더를 구분하기 위해 봉인 클래스 사용하기'
description: '안드로이드 리사이클러 뷰에 여러가지의 항목을 나타내야 할 때 봉인 클래스를 사용할 수 있습니다.'
date: 2022-01-03T12:12:59+09:00
image: 'https://static.nwlee.app/public/KVYH1C00/33653dea-cf90-418e-a3bd-ddd579416a0a.png'
math:
license:
hidden: false
comments: true
draft: true
tags: ['Recycler View', 'View Holder', 'Android', 'Sealed Class']
categories: ['Android']
---

# 리사이클러 뷰에 여러 항목 출력하기

안드로이드에서 여러 개의 항목을 한 화면에 나타내야 할 때 리사이클러 뷰를 사용한다. 리사이클러 뷰는 각 항목의 뷰를 재활용하므로 데이터를 보다 효율적으로 렌더링할 수 있다. 뷰를 재활용함으로써 앱의 성능을 개선시킬 수 있다.

뷰 홀더는 RecyclerView.ViewHoler 클래스를 상속받아 정의되고 각 데이터를 뷰 홀더에 바인딩시킨다. 어댑터는 뷰 홀더 객체를 생성하고 데이터를 설정하는 역할을 한다.

라사이클러 뷰에 여러 가지의 데이터를 렌더링해야 하는 경우가 있다. 예를 들면 여러 개의 이미지와 텍스트를 하나의 화면에 출력해야 하는 경우이다.

| 상단 바 |
| ------- |
| 이미지  |
| 이미지  |
| 텍스트  |
| 텍스트  |

리사이클러 뷰가 중첩되면 각각의 스크롤이 따로 동작하는 문제가 생긴다.

NestedScrollView를 사용하면 스크롤이 따로 동작하는 문제는 해결되지만, 리사이클러 뷰가 뷰를 재활용하지 못한다는 단점이 생긴다. 리사이클러 뷰가 아이템을 미리 생성하기 때문이다.

그래서 각 데이터 타입에 맞는 뷰 홀더 클래스를 선언하고 어댑터의 getItemViewType 함수를 오버라이딩하여 해당 데이터가 타입에 맞는 뷰 홀더와 연결시키도록 구성했다.

## 데이터 클래스

처음에는 데이터 클래스 내 타입을 구분해 줄 수 있는 속성을 따로 정의했다.

```Kotlin
data class Item(
  val id: Int,
  val title: String,
  val description: String,
  val poster: String,
  var isImage: Boolean,
)
```

레포지터리에서 HTTP 통신을 통해 API 응답을 받으면 데이터 내 항목을 살펴 이미지인지 아닌지 구별한 뒤 isImage 속성을 바꿔주었다.

하지만 데이터 클래스에서 var로 속성을 정의하는 것은 권장되지 않는다. 데이터 클래스는 기본적으로 해시 값을 리턴하는 hashCode 함수가 오버라이딩되어 있다. HashMap과 같이 데이터 클래스 객체를 키로 사용하는 객체의 경우 데이터 클래스 내 속성이 변경되면 해쉬 맵 상태가 잘못될 수 있다.

## 열린 클래스

그래서 열린 클래스(open class)로 선언된 기본 뷰 홀더를 상속받아 각 데이터에 맞는 상세 뷰 홀더를 생성하는 쪽으로 바꾸었다. 열린 클래스는 상속받는 클래스가 뭔지 정확히 알기 어려운 문제점이 남아있었다.

## 현재 해결 방법

봉인 클래스(sealed class)의 사용법을 알고 나서 기본 뷰 홀더를 봉인 클래스로 선언한 다음 각 뷰 홀더가 기본 뷰 홀더를 상속받도록 했다.

어댑터를 구현할 때 데이터 바인딩, ListAdapter 등을 사용했다. 데이터의 타입은 Any로 설정한 다음 onBindViewHolder 함수에서 데이터의 타입을 검사하도록 했다.

```Kotlin
class PlaceAdapter : ListAdapter<Any, PlaceAdapter.BaseViewHolder>(DiffUtil()) {
    private val viewType = { index: Int ->
        when (특정 조건) {
            true -> 0
            false -> 1
        }
    }

    sealed class BaseViewHolder(binding: ViewDataBinding) : RecyclerView.ViewHolder(binding.root)

    class TextViewHolder(private val binding: LayoutTextBinding) :
        BaseViewHolder(binding) {
        fun bind(detail: TextData) {
            // 텍스트 출력
            binding.executePendingBindings()
        }
    }

    class ImageViewHolder(private val binding: LayoutImageBinding) :
        BaseViewHolder(binding) {
        fun bind(image: ImageData) {
            // 이미지 내려받기
            binding.executePendingBindings()
        }
    }

    override fun getItemViewType(position: Int): Int {
        return viewType(position)
    }

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): BaseViewHolder {
        if (viewType == 0) {
            val binding: LayoutTextBinding = DataBindingUtil.inflate(
                LayoutInflater.from(parent.context),
                R.layout.layout_text,
                parent,
                false,
            )
            return TextViewHolder(binding)
        } else {
            val binding: LayoutImageBinding = DataBindingUtil.inflate(
                LayoutInflater.from(parent.context),
                R.layout.layout_image,
                parent,
                false
            )
            return ImageViewHolder(binding)
        }
    }

    override fun onBindViewHolder(holder: BaseViewHolder, position: Int) {
        val item = getItem(position)

        if (item is PlaceItem) {
            (holder as TextViewHolder).bind(item)
        } else {
            (holder as ImageViewHolder).bind(item as String)
        }
    }
}
```

viewType 함수에서 각 데이터의 인덱스를 인자로 하여 조건문에 따라 두 가지의 값을 출력하도록 했다.

onCreateViewHolder 함수는 viewType 함수가 반환하는 값에 따라서 텍스트 또는 이미지 뷰 홀더를 생성한다.

그 다음 onBindViewHolder에서 각각의 데이터를 가져온 다음 holder를 타입 캐스팅한 뒤 bind 함수를 실행시킨다.

## 봉인 클래스

봉인 클래스는 상속받는 클래스의 정의를 제한할 수 있다. when 식에서 봉인 클래스의 모든 하위 클래스를 검사하므로 따로 기본 분기 처리가 필요하지 않게 된다.

```Kotlin
sealed class Detail {
  class Image(
    val id: Int,
    val image: String,
  ): Detail()
  class Text(
    val id: Int,
    val content: String,
    val isHTML: Boolean,
  ): Detail()
}
```

```Kotlin
fun getDataType(data: Detail): Boolean {
  return when (data) {
    is Image -> {
      /* ... */
      true
    }
    is Text -> {
      /* ... */
      false
    }
    // else 처리가 필요하지 않다.
  }
}
```

봉인 클래스를 정의할 때는 생성자를 따로 명시하지 않아도 되지만, 클래스를 상속받는 하위 클래스를 정의할 때에는 봉인 클래스에 주 생성자를 뜻하는 빈 괄호를 반드시 명시해줘야 한다.

클래스를 정의할 때 별도의 생성자를 정의하지 않아도 컴파일러에서 인자가 없는 생성자를 생성해주기 때문이다.

하위 클래스를 정의할 때에는 하위 클래스의 주 생성자에서 상위 클래스의 생성자를 호출해야 한다. 상위 클래스를 초기화할 때 생성자 인자를 넘겨줄 수 있다.

```Kotlin
class BaseViewHolder(binding: ViewDataBinding) : RecyclerView.ViewHolder(binding.root)
```

RecyclerView의 ViewHolder 클래스를 상속받아 BaseViewHolder를 정의할 때 하위 생성자의 binding이 상위 생성자의 인자로 넘겨진다. 상위 클래스 ViewHolder는 생성자 인자로 View 객체를 받는데, 여기선 binding 객체의 root 속성의 타입이 View이다.
