# 1. Shared Elements Transition
스토리 앱에 화면 전환 애니메이션 적용기

Shared Elements Transition을 이용하여 자연스러운 화면 전환을 적용한 내용과 적용 과정에서 발생한 이슈들과 해결 방법에 대해서 공유하고자 합니다.

- URL: https://if.kakao.com/session/109


## Shared Elements Transition
안드로이드에서는 주로 Fade/Slide의 애니메이션을 사용한다.
Shared Elements Transition을 사용하면, 화면과 화면의 공유 요소를 통하여, 하나의 Activity에서 Transition 이 이뤄지는 듯한 효과를 보여주어서 사용자에게 몰입감을 줄 수 있다.

(Hero Event)

API Restroctopm
equal and above Andorid 5.0(API 21)


1. Register Transition 

Activity A -> Activity B 로 Transition 을 해야 할 때.


```
// styles.xml
<style ...>

	<!-- ACTIVITY B에 대한 효과 -->
	<item name="android:windowShardElementEnterTransition" tools:ignore="NewApi">
		@android:transition/move
	</item>

	<!-- ACTIVITY A에 대한 효과 -->
	<item name="android:windowShardElementExitTransition" tools:ignore="NewApi">
		@android:transition/move
	</item>

</style>
```


```
// Activity.kt
override fun onCreate() {
	...
	window.run {
		sharedElementEnterTransition = TransitionInflater.from(context).inflateTransition(android.R.transition.move)

		sharedElementExitTransition = TransitionInflater.from(context).inflateTransition(android.R.transition.move)
	}
}


// ActivityA
val intent = Intent(context, ActivityB::class.java)
val options = ActivityOptionsCompat.makeSceneTransitionAnimation(context, view, name)
ActivityCompat.startActivity(context, intent, options.toBundle())

// ActiivtyB
ViewCompat.setTransitionName(view, name)



// If want to share several views at the same time,
// ActivtyA
val pair1 = Pair<View, String>(view1, name1)
val pair2 = Pair<View, String>(view2, name2)

val intent = Intent(context, ActivityB::class.java)
val options = ActivityOptionsCompat.makeSceneTransitionAnimation(context, pair1, pair2)
ActivityCompat.startActivity(context, intent, options.toBundle())

// ActivityB
ViewCompat.setTransitionName(view1, name1)
ViewCompat.setTransitionName(view2, name2)
```

