---
title: '동반 객체(Companion Object)에 대하여'
description: 'DiffUtil에 동반 객체를 사용하면 안되는 이유를 알아보았습니다.'
date: 2021-11-15T01:28:11+09:00
image: 'https://static.nwlee.app/public/KVYH1C00/33653dea-cf90-418e-a3bd-ddd579416a0a.png'
math:
license:
hidden: false
comments: true
draft: true
---

# 학습 내용

안드로이드 앱 개발을 하다가 리사이클러 뷰(RecyclerView)에 부착할 리스트 어댑터(ListAdapter)를 정의해야 할 일이 생겼다.

리스트 어댑터를 사용함으로써 리사이클러 뷰 내 항목들을 보다 효율적으로 업데이트할 수 있다. DiffUtil을 사용함으로써 어떤 아이템이 변경되었는지 파악하고, 해당 어댑터에 업데이트 이벤트를 전달할 수 있다. DiffUtil.ItemCallback을 구현한 클래스를 정의했다.

아래 어댑터는 여러 개의 이미지를 가로 방향으로 나열해 줄 때 사용했다.

```Kotlin
class PlaceDetailAdapter : ListAdapter<PlaceDetailPhoto, PlaceDetailAdapter.ViewHolder>(diffUtil) {

    inner class ViewHolder(private val binding: ItemRecyclerPlaceDetailBinding) :
        RecyclerView.ViewHolder(binding.root) {

        fun bind(model: PlaceDetailPhoto) {
            val apiKey = BuildConfig.GOOGLE_MAP_KEY
            val imageUrl =
                "https://maps.googleapis.com/maps/api/place/photo?maxwidth=400&photo_reference=${model.photoId}&key=$apiKey"
            binding.model = model
            Glide.with(binding.root)
                .load(imageUrl)
                .centerCrop()
                .override(
                    binding.ivItemPlaceDetailPhoto.measuredWidth,
                    binding.ivItemPlaceDetailPhoto.measuredHeight
                )
                .into(binding.ivItemPlaceDetailPhoto)
            binding.executePendingBindings()
        }
    }

    companion object {
        val diffUtil = object : DiffUtil.ItemCallback<PlaceDetailPhoto>() {
            override fun areItemsTheSame(
                oldItem: PlaceDetailPhoto,
                newItem: PlaceDetailPhoto
            ) = oldItem.photoId == newItem.photoId

            override fun areContentsTheSame(
                oldItem: PlaceDetailPhoto,
                newItem: PlaceDetailPhoto
            ) = oldItem == newItem
        }
    }

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): ViewHolder {
        val binding: ItemRecyclerPlaceDetailBinding = DataBindingUtil.inflate(
            LayoutInflater.from(parent.context),
            R.layout.item_recycler_place_detail,
            parent,
            false
        )
        val viewHolder = ViewHolder(binding)
        return viewHolder
    }

    override fun onBindViewHolder(holder: ViewHolder, position: Int) {
        holder.bind(getItem(position))
    }
}
```

블로그 예제에서 동반 객체, Companion Object 내부에 DiffUtil을 구현하는 코드가 많이 보여서 따라해보았다. 이후 프로젝트 멘토님께서 안드로이드 SunFlower 예제를 찾아보고 작성된 어댑터 코드를 내 코드와 비교해보라고 하셨다.

Sunflower 예제 코드에서는 DiffUtil 클래스가 다음과 같이 정의돼 있었다.

[해당 소스 코드](https://github.com/android/sunflower/blob/main/app/src/main/java/com/google/samples/apps/sunflower/adapters/GalleryAdapter.kt)

```Kotlin
private class GalleryDiffCallback : DiffUtil.ItemCallback<UnsplashPhoto>() {
    override fun areItemsTheSame(oldItem: UnsplashPhoto, newItem: UnsplashPhoto): Boolean {
        return oldItem.id == newItem.id
    }

    override fun areContentsTheSame(oldItem: UnsplashPhoto, newItem: UnsplashPhoto): Boolean {
        return oldItem == newItem
    }
}
```

동반 객체를 사용하지 않고 클래스가 따로 정의되어 있었다.

이후 멘토님께서 동반 객체를 함부로 사용해서는 안되는 이유까지 찾아보라고 하셨다. 현재까지 배운 것은 다음과 같다.

코틀린은 자바의 Static 키워드를 지원하지 않는다. 동반 객체와 그 객체 내 함수를 정의함으로써 클래스의 비공개(Private) 속성에 접근할 수 있다.

동반 객체를 정의할 때 이름을 따로 지정하지 않아도 되므로 자바의 Static 키워드를 흉내낼 수 있다.

다만 동반 객체는 클래스 안에 정의된 일반 객체이다. 동반 객체가 인터페이스를 상속받거나, 확장 함수, 속성을 정의할 수 있는 것은 이 때문이다.

코틀린에서는 클래스 이름만으로 동반 객체를 참조할 수 있다. 따라서 클래스 내 1개만 선언할 수 있다.

## 예제 코드

```Kotlin
class Example {

    companion object {
        val value = 10
    }
}

class Person private constructor(
	val name: String,
    val age: Int,
) {
    companion object {
        fun makePerson(name: String, age: Int): Person {
        	return Person(name, age)
    	}
    }

    fun introduce() {
        println("Hello. ${this.name} ${this.age}")
    }
}

fun main() {
	val p = Example.value
    val q = Example.Companion.value
    println("$p $q")

    val john = Person.makePerson("John", 25)
    john.introduce()
}
```

## 중간 결론

현재까지는 자바의 static 변수, 메소드와는 다르게 코틀린 클래스 내 객체가 생성되고 객체 내 정의된 변수와 메소드를 외부 클래스의 이름으로 직접 참조할 수 있는 정도만 이해했다. 아직도 DiffUtil을 선언할 때 왜 동반 객체를 사용하면 안되는지 알 수 없었다.

스택오버플로우의 한 게시물에서는 클래스에서 DiffUtil에 직접적으로 접근이 가능하니 잘못하면 여러 개의 어댑터에도 사용할 위험이 있다는 답글이 작성되어 있었다. 멘토님의 생각과는 많이 다른 거 같다.

## 여담

코틀린 안드로이드에 입문하게 된 계기는 조금 복잡하다. 코틀린을 알기 전까진 리액트 네이티브와 플러터와 같은 크로스플랫폼 어플리케이션 개발만 하고 싶었는데 개발자 오픈톡방 내 많은 개발자 분들이 네이티브 앱 개발을 모르면 크로스플랫폼 개발도 힘들 거라고 조언해 주셨기 때문이었다.

마침 부스트캠프 신청 기간이었고, 자바 몰라도 코틀린만 **잘** 할 줄 알면 안드로이드 앱 개발할 수 있다고 한번 도전해보라고 하셨다. 하지만 지금 돌이켜보면 자바 지식이 있고 없고의 차이가 크다고 생각한다.

예전에 우아한형제들에서 코틀린 스프링에 대한 강연을 본 적이 있었는데, 자바를 모르는 상태에서 코틀린으로 스프링 웹 개발을 하는 것은 불가능하다는 걸 깨달았다. 최근에도 현업에서는 모바일 앱 개발할 때 자바를 사용한다고 들은 적도 많다.

코틀린 제대로 작성하기도 벅찬데 언제쯤 자바까지 공부할 수 있을까?

<img src="https://static.nwlee.app/public/KVYH1C00/cf945d93-8837-4bc8-9da3-3b624e907a12.jpeg" loading="lazy" >
