---
layout: post
title:  "[Kotlin] 테스트 코드 작성이 힘든 이유와 테스트 가능 코드에 대한 고민"
date:   2018-11-20 09:00:00
tags: kotlin android test tdd java
categories: devstory
---

안녕하세요. 요새 저는 테스트 코드 작성에 ~~혈안~~ 집중을 하고 있는데요,
많은 분들이 '테스트 코드' 작성이 힘들다고 생각하시며, 과연 그게 '필요한가' 하는 고민까지 하는 것을 보았습니다. 
물론 저도 그렇게 생각했었구요(-ing)

테스트 코드 작성이 과연 "필요한가"에 대한 답은 사실 없습니다. 수행하는 도메인 로직마다 테스트 코드 커버리지가 얼마나 되어야하느냐는 사실 중요하지 않기 때문이죠. 
정말 중요한건, "코드"가 정상적으로 동작하는지 "확신"을 할 수 있는지를 구성원 대다수가 공감해야한다는 것 같아요. 그래서 이번 시간에는 ~~테스트 코드가 정말 필요한가~~라는 질문보다는 ***테스트 코드 작성을 어떻게 하면 쉽고, 효율적으로 할 수 있을까*** 를 고민해봤으면 합니다.


( *안드로이드 코드 베이스* 로 작성되었습니다. )



## 태초에...
다음과 같은 ***간단한*** 앱이 있다고 가정하고 개발/테스트 여정으로 떠나봅시다.

### 요구사항
1. 안드로이드 앱이 실행되면, 1개의 Button과 2개의 TextView가 있는 화면이 나타난다.
2. Button을 클릭하면 1개의 TextView에 1부터 차례로 1씩 숫자가 증가된다.
3. TextView에 3/6/9라는 숫자가 들어간다면, TextView에 "짝"이라는 글자가 띄워지고, 만약 숫자가 들어가지 않는다면 "빈 문자열"이 노출된다.


# 첫번째 프로그램

## Source
쉬운 이해를 위해, 모든 View에 해당하는 부분은 xml이 아니라 코드로 나타내었습니다. (원칙상 안드로이드에선 view 관련 코드는 xml에 표현합니다.)

다음과 같은 ***매우 간단한*** 코드가 있습니다.

```kotlin
class MainActivity : AppCompatActivity() {
    companion object {
        private const val PLUS_BUTTON_TEXT = "+"
        private const val CLAP_TEXT = "짝"
    }

    private lateinit var numberTextView: TextView // 숫자를 나타내주는 View
    private lateinit var clapTextView: TextView // 3, 6, 9 숫자가 들어가면 "짝"을 보여주는 View
    private lateinit var button: Button // 숫자를 증가시키는 View

    private var number = 0 // 숫자값을 카운팅하는 변수

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        // 레아아웃뷰 생성(전체 View를 담을 컨테이너)
        val linear = LinearLayout(this).apply {
            orientation = LinearLayout.VERTICAL
        }

        numberTextView = TextView(this.applicationContext)
        clapTextView = TextView(this.applicationContext)
        button = Button(this.applicationContext).apply {
            text = PLUS_BUTTON_TEXT

            // 버튼 클릭시, 숫자 증가 및 3/6/9 가 있는지 체크 후, TextView에 표시
            setOnClickListener {
                number++
                numberTextView.text = "$number"

                // 3/6/9 가 있는지 체크하는 로직
                var isContained = false
                run loop@{
                    listOf("3", "6", "9").forEach {
                        val numberString = number.toString()
                        isContained = numberString.contains(it)
                        if (isContained) {
                            return@loop
                        }
                    }
                }

                if (isContained) {
                    clapTextView.text = CLAP_TEXT
                } else {
                    clapTextView.text = ""
                }
            }
        }

        // 뷰 추가
        linear.run{
            addView(numberTextView)
            addView(clapTextView)
            addView(button)
        }

        setContentView(linear)
    }
}
```

## Test
위의 프로그램은 `onCreate`라는 콜백위에 모든 것이 정의되어 있습니다. 하위 뷰의 생성, 버튼 클릭시 데이터값 수정, 계산 및 뷰 수정까지 모든 로직이 하나에 들어있기에 다음과 같이 테스트 코드를 작성 할 수 있겠네요.

```kotlin
class MainActivityTest {

    @Test
    fun onCreate() {
        val mainActivity = MainActivity()
        mainActivity.onCreate(null, null)
    }
}
```
단순히 외부에서 MainActivity의 onCreate() 메소드를 호출해주는 테스트 코드입니다.

실행결과는 다음과 같습니다.

![1.png](/static/assets/img/posts/whytestcodehard/1.png)


어머낫! 실행/테스트가 수행 되지 않았습니다 !!

그 이유는, Unit 테스트를 수행할 때, Android SDK 경로에있는 `android.jar`가 아닌 `android-stubs-src.jar`를 이용하여 테스트를 수행하기 때문인데요, 
해당 파일에 있는 클래스/메소드들은 실제 구현이 없는 빈 껍데기이기 때문에 JUnit 상에서 정상적인 동작을 하지 못하기 때문입니다.

![2.png](/static/assets/img/posts/whytestcodehard/2.png)



## Next
그럼 JUnit 테스트를 위해서는 어떻게 해야할까요?
답은 간단합니다. `안드로이드 코드`와 `안드로이드 의존성이 없는 코드`를 분리해야합니다.


# 두번째 프로그램
여러 방법이 있겠지만, 다음과 같이 분리해보도록 하겠습니다.

## Source

3/6/9가 들어있는지(1~9 사이값 중 3의 배수인지)를 체크하는 로직을 `contains369()` 메소드로 뺀것에 주목하시면 좋을 것 같습니다.

`contains369()` 메소드에서 멤버변수인 ***number*** 를 참조하는 것이 아니라, 따로 파라미터로 받도록 설계를 한 것에도 주목을 해야겠네요. 이렇듯 ***강한 결합을 끊고*** 외부로부터 값을 받도록 설정하면, 해당 부분은 ```테스트가 가능한 로직```이 됩니다.

> 또한, `contains369()` 메소드는 항상 같은 입력에 같은 결과를 주고 있는 `순수 함수`로서, MainActivity의 어느 상태도 변경하지 않으므로, 외부로 공개해도 "안전한 메소드"라고 말할 수도 있겠네요 !! (그래서 public으로 접근자를 지정했습니다. == 외부 호출가능 == 테스트 가능)

```kotlin
class MainActivity : AppCompatActivity() {
    companion object {
        private const val PLUS_BUTTON_TEXT = "+"
        private const val CLAP_TEXT = "짝"
    }

    private lateinit var numberTextView: TextView // 숫자를 나타내주는 View
    private lateinit var clapTextView: TextView // 3, 6, 9 숫자가 들어가면 "짝"을 보여주는 View
    private lateinit var button: Button // 숫자를 증가시키는 View

    private var number = 0 // 숫자값을 카운팅하는 변수

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(makeView())
    }

    private fun makeView(): View {
        val linear = LinearLayout(this).apply {
            orientation = LinearLayout.VERTICAL
        }

        numberTextView = TextView(this.applicationContext)
        clapTextView = TextView(this.applicationContext)
        button = Button(this.applicationContext).apply {
            text = PLUS_BUTTON_TEXT

            // 버튼 클릭시, 숫자 증가 및 3/6/9 가 있는지 체크 후, TextView에 표시
            setOnClickListener {
                number++
                numberTextView.text = "$number"

                // 3/6/9 가 있는지 체크하는 로직
                if (contains369(number)) {
                    clapTextView.text = CLAP_TEXT
                } else {
                    clapTextView.text = ""
                }
            }
        }

        linear.run{
            addView(numberTextView)
            addView(clapTextView)
            addView(button)
        }

        return linear
    }

    fun contains369(num: Int): Boolean {
        listOf("3", "6", "9").forEach {
            val numberString = num.toString()
            if (numberString.contains(it)) {
                return true
            }
        }

        return false
    }
}
```


## Test
MainActivity의 인스턴스를 생성하여 테스트를 하고있는 것을 볼 수 있습니다. Activity를 직접생성하고는 있지만, 안드로이드 프레임워크 코드를 따라가는 부분은 전혀 없으므로, 유닛테스트가 정상적으로 동작함을 볼 수 있습니다.

```kotlin
class MainActivityTest {
    @Test
    fun `3,6,9가 들어있는지 확인하는 테스트`() {
        val mainActivity = MainActivity()

        // 0부터 100까지는 3/6/9
        val testSet = hashMapOf(
            Pair(true, listOf(3,6,9,13,16,203,216,999,1033)),
            Pair(false, listOf(0,1,2,4,5,10,17,28,40,41,47,71,507,788))
        )

        testSet[true]?.forEach {
            assertEquals(true, mainActivity.contains369(it))
        }

        testSet[false]?.forEach {
            assertEquals(false, mainActivity.contains369(it))
        }
    }
}
```



## Next
조금 더 멋있는 테스트 코드를 작성하려면 어떻게 해야할까요? 해당 본문이 "테스트 코드 작성이 힘든 이유"에 대해서 쓰여지고 있다는 것을 다시 떠올려 봅시다. 테스트 코드 작성이 힘든 이유는 "확장"에 대해 열려있고, "수정"에 대해서는 닫혀있는 개방폐쇄 원칙이 지켜지지 못하는 것과 각종 기능들이 한곳에 모여져있어서 "책임 분리의 원칙"이 지켜지지 못하는 것에(도) 있습니다. 



현재 코드는 Activity (엄밀히 말하면 View 단)에서 모두 이루어져있기에, 핵심 비즈니스 로직인 `contains369()` 로직이 변경된다면 View 로직이 있는 Activity 클래스를 수정해야하는 위험성이 있지요. 또한, 다양한 변화 가능성에 대해서는 닫혀있음을 볼 수 있습니다.

`MainActivityTest` 클래스에서 비즈니스 로직을 검사한다는 것 자체가 웃긴 상황이니, 마지막 스텝으로 비즈니스 로직을 분리해봅시다 !!



# 세번째 프로그램
View를 만드는 코드들은 모두 MainActivity에 두고, 나머지는 옮겨봅시다 !

주요 비즈니스 로직을 담당하는 GameController 입니다. 해당 클래스에 contains369를 조금 더 일반화하여 contains() 메소드를 만든 것을 확인 할 수 있습니다. 더해서, 단순히 369 포함 여부 뿐만 아니라, 포함 갯수까지 카운팅해주는 메소드도 작성 할 수 있습니다.(countOf369(), countOf() 메소드)

```kotlin
// GameController.kt
class GameController {

    fun contains369(num: Int): Boolean = contains(listOf("3", "6", "9"), num)

    private fun contains(list: List<String>, num: Int): Boolean {
        list.forEach {
            val numberString = num.toString()
            if (numberString.contains(it)) {
                return true
            }
        }

        return false
    }

    fun countOf369(num: Int): Int = countOf(listOf("3", "6", "9"), num)

    private fun countOf(list: List<String>, num: Int): Int {
        var count = 0
        list.forEach {
            val numberString = num.toString()
            if (numberString.contains(it)) {
                count++
            }
        }

        return count
    }
}
```

Android AAC에서 제공하는 ViewModel을 이용하여 다음과 같은 ViewModel 클래스를 구성하였습니다. Activity에는 단순히 `View` 위젯들과 `View` 위젯들의 액션(클릭 등등)들을 선언하는 반면, GameModel에서는 비즈니스 로직과 View를 이어주며, 실제 앱의 데이터를 관리하는 것을 볼 수 있습니다.

```kotlin
// GameModel.kt
class GameViewModel: ViewModel() {
    private val gameController = GameController()

    val number = MutableLiveData<Int>()
    val contains = MutableLiveData<Boolean>()

    init {
        number.value = 0
        contains.value = false
    }

    fun increaseNumber(diff: Int = 1) {
        number.value = number.value?.plus(diff)

        contains.value = gameController.contains369(number.value!!)
    }
}
```

마지막으로 View만을 담당하는 Activity 입니다. 처음에 비해서 `View`에 관련된 코드만 보이는 것을 알 수 있습니다. View를 정의하고, 각 View의 이벤트(클릭 등)를 정의하며, 데이터가 변경되었을 때(observe), View에 반영하는 로직만이 담겨있음을 볼 수 있습니다.

```kotlin
// MainActivity.kt
class MainActivity : AppCompatActivity() {
    companion object {
        private const val PLUS_BUTTON_TEXT = "+"
        private const val CLAP_TEXT = "짝"
    }

    private lateinit var numberTextView: TextView // 숫자를 나타내주는 View
    private lateinit var clapTextView: TextView // 3, 6, 9 숫자가 들어가면 "짝"을 보여주는 View
    private lateinit var button: Button // 숫자를 증가시키는 View
    private lateinit var viewModel: GameViewModel

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        viewModel = ViewModelProviders.of(this).get(GameViewModel::class.java)
        viewModel.number.observe(this, object: Observer<Int> {
            override fun onChanged(number: Int?) {
                numberTextView.text = "$number"
            }
        })

        viewModel.contains.observe(this, object: Observer<Boolean> {
            override fun onChanged(contains: Boolean?)
                    = if (contains!!) {
                        clapTextView.text = MainActivity.CLAP_TEXT
                    } else {
                        clapTextView.text = ""
                    }
        })

        setContentView(makeView())
    }

    private fun makeView(): View {
        val linear = LinearLayout(this).apply {
            orientation = LinearLayout.VERTICAL
        }

        numberTextView = TextView(this.applicationContext)
        clapTextView = TextView(this.applicationContext)
        button = Button(this.applicationContext).apply {
            text = PLUS_BUTTON_TEXT
            setOnClickListener {
                viewModel.increaseNumber()
            }
        }

        linear.run{
            addView(numberTextView)
            addView(clapTextView)
            addView(button)
        }

        return linear
    }
}
```


# 맺으며
개발에는 왕도가 없다는 말이 있습니다. 다만, "정석"에 근접하는 길이 있고, 보다 효율적인 길이 있다고 생각합니다. 응집도를 높이고, 결합도를 낮추는 코드들은 결국 `"테스트 코드"`를 짜기 쉬운 코드와 일맥상통하다고 생각합니다.

테스트 코드를 짜면서, 위와 같은 철학/개념을 익히는 하루하루가 되었으면 합니다 : )