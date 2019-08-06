---
layout: post
title:  "[Android] Android Model in Clean Architecture"
date:   2019-08-01 12:00:00
tags: android model mvvm mvp mvc clean cleanarchitecture viper
categories: devstory
---
# Android Model in Clean Architecture
 
Android App 개발 뿐만 아니라, iOS가 되었든, Spring으로 웹 서비스를 구축하든, VueJs든 React든 Model의 개념은 항상(대개) 존재합니다.

안드로이드 앱 개발시 아키텍쳐링을 하고 패턴을 사용하면 대개 아래와 같은 패턴을 선택하여 개발합니다.


##### **M** Something Pattern
```
MVC
MVVM
MVP
MVI
...
M😎😎
```

그렇다면, Model 이란 무엇일까요?


# Model
Wikipedia에서는 아래와 같이 설명이 되어있습니다. (MVC 패턴의 모델 설명구)

> The central component of the pattern. It is the application's dynamic data structure, independent of the user interface. It directly manages the data, logic and rules of the application.

다른 **M Something** 패턴에서도 대개 Model의 역할은 비슷하니, 위의 구문을 이해해봅시다. 

위 정의에 따르면, 크게 Model은 2가지 역할을 가지고 있네요.

**1. Data 관리**
**2. 어플리케이션의 로직, 규칙 관리 (== 비즈니스 로직, 도메인 로직)**

> 그렇군요, **데이터를 관리**하며 그 데이터를 핸들링하는 **비즈니스 로직**을 담고있는 것이 **Model** 입니다 !

---

우리가 잘 아는 MVP(~~Minimum  Viable Product~~ 아닙니다) 패턴에서도 주의할 점들이 많이 있는데요, 바로 Model의 확장성, 재사용성 등을 고려하지 않은채 단순하게 패턴만 적용하면 발생하는 문제점들입니다. 말로만 들어서는 이해가 잘 안갈 것 같아요. 한번 코드를 보면서 하나하나 살펴보도록 합시다 (Model 뿌개기 !)

> `[Note]`
> 중간 중간 아래의 **`MVP`** 패턴 아키텍쳐가 **`변화되는 모양`** 에 주목하세요 !

![](/static/assets/img/posts/androidmodel/android-model-1.png)


# 1. 완벽하지만, 허접한 MVP
다음과 같은 "맛집 찾기" 앱이 있습니다.
```
M : HotplaceModel
V : HotplaceView
P : HotplacePresenter
```

### Presenter Code
```kotlin
class HotplacePresenter(view: HotplaceView) : Presenter<HotplaceView>(view) {
    ...
    fun onCreate(restaurantName: String, zipCode: String) {
        val hotplace = HotplaceModel.getHotplace(restaurantName, zipCode)
        view.run {
            setName(hotplace.name)
            setZipCode(hotplace.zipCode)
            setMap(hotplace.map)
        }
    }
}
```


### 현재 Presenter 구조

![](/static/assets/img/posts/androidmodel/android-model-2.png)

## 현재 아키텍쳐

![](/static/assets/img/posts/androidmodel/android-model-3.png)

완벽하네요.
HotplacePresenter에서는 식당 이름 및 우편번호를 받아서, HotplaceModel에게 넘겨주고, hotplace를 얻어옵니다. HotplaceModel로 부터 얻은 hotplace 객체를 이용하여 HotplaceView를 업데이트 시켜줍니다.


## 문제..점?
하지만, 위의 구조적으로 완벽해보이는 코드에는 어떤 문제가 있을까요?

Hotplace 라는 데이터를 가져오는 로직을 보면, HotplaceModel에서 데이터를 가져올 때, 어떠한 추상화된 인터페이스 없이 바로 데이터를 가져오는 것을 볼 수 있습니다. 
이렇게 되면, HotplaceModel의 변화에 대해 HotplacePresenter의 코드 수정이 일어날 수 있음을 의미하고, 테스트 조차 용이하지 못하다는 것을 알 수 있습니다.


# 2. Repository Pattern : Model => Data
위의 구조에서 보이는 문제점을 해결하기 위해서는 HotplaceModel을 추상화하여 작성하여야합니다.
흔히, **Repository Pattern** 이라고 불리우는 패턴을 사용하여 추상화시킬 수 있는데요 ~ 다음과 같이 작성이 가능합니다.


### Presenter Code
```kotlin
class HotplacePresenter(
    view: HotplaceView,
    val repository: HotplaceRepository) : Presenter<HotplaceView>(view) {
    ...
    fun onCreate(restaurantName: String, zipCode: String) {
        val hotplace = repository.getHotplace(restaurantName, zipCode)
        view.run {
            setName(hotplace.name)
            setZipCode(hotplace.zipCode)
            setMap(hotplace.map)
        }
    }
}
```
위와 같이 HotplacePresenter 생성시 주입해주는 repository를 이용하여 hotplace 데이터를 들고온다면, HotplaceRepository의 mock 객체등을 이용해 테스트도 용이해질뿐만 아니라, 기존의 HotplacePresenter와 Model간의 강한 결합도를 낮출 수 있습니다.



### 현재 Presenter 및 Repository 구조

![](/static/assets/img/posts/androidmodel/android-model-4.png)

이렇게 작성해놓고 보니, HotplaceRepository는 Model의 역할을 **`Data`** 의 관점에서 충실히 잘 수행해내고 있는 것 같습니다.


## 현재 아키텍쳐

![](/static/assets/img/posts/androidmodel/android-model-5.png)


## 문제..점?
위의 로직을 보아하니, 잘 작성된 코드 같습니다. 하지만 저희는 **프로 불편러**이니 굳이 문제점을 들춰내봅시다(문제가 없다면 문제를 만들어봅시다.)

코드로 명시되어있지는 않지만,  **HotplaceRepositoryImpl** 에서는 **네트워크 통신**을 통해 Hotplace를 들고오고 있습니다. 하지만, Android 특성상 "다른 앱"과 통신을 하여 데이터를 가져올 수도 있겠고, 로컬에 캐싱해놓은 데이터를 불러올 수도 있을 것입니다.

이렇듯, **`Data`** 를 가져오는 **위치**인 **`Source`** 에 대한 구현이 미흡한 점을 문제점으로 삼을 수 있을 것 같습니다.




# 3. Data Source : Model => Source
위에서 문제삼았던, **Source**를 추가 구현해보도록 합시다.

### Presenter Code
```kotlin
// 이전과 동일함
class HotplacePresenter(
    view: HotplaceView,
    val repository: HotplaceRepository) : Presenter<HotplaceView>(view) {
    ...
    fun onCreate(restaurantName: String, zipCode: String) {
        val hotplace = repository.getHotplace(restaurantName, zipCode)
        view.run {
            setName(hotplace.name)
            setZipCode(hotplace.zipCode)
            setMap(hotplace.map)
        }
    }
}
```

### Repository Code
```kotlin
class HotplaceRepositoryImpl(
    hotplaceApi: HotplaceNetworkDataSource,
    hotplaceCache: HotplaceLocalDBDataSource) : HotplaceRepository {
    
    override fun getHotplaceFromApi(): Hotplace = hotplaceApi.getHotplace()
    
    override fun getHotplaceFromDb(): Hotplace = hotplaceCache.getHotplace()
}
```
위와 같이 HotplacePresenter 생성시 주입해주는 repository를 이용하여 hotplace 데이터를 들고온다면, HotplaceRepository의 mock 객체등을 이용해 테스트도 용이해질뿐만 아니라, 기존의 HotplacePresenter와 Model간의 강한 결합도를 낮출 수 있습니다.


### 현재 Presenter 및 Repository 구조

![](/static/assets/img/posts/androidmodel/android-model-6.png)



## 현재 아키텍쳐

![](/static/assets/img/posts/androidmodel/android-model-7.png)


## 문제..점?
완벽합니다 : ) 각종 **Data Source**(데이터가 위치하는 곳, 데이터를 불러올 수 있는 통로)에 대해 대응할 수 있음은 물론, 예전에 가졌던 문제들도 모두 해결이 되었네요 !!

`하지만`.. 우리는 아까 말했듯 ~~프로 불편러~~이기 때문에, 문제를 만들어나가야합니다. (미래에 있을 문제를 예상해봐요)
만약, Hotplace App이 더욱 성장하여, **Review** 시스템을 새로 들여왔다고 가정해봅시다.
**HotplaceReview** 객체는 Hotplace에 대한 리뷰 정보들을 담고있는데요 ~ **HotplacePresenter** 에서는 **Hotplace** 정보와 **HotplaceReview** 객체를 `적절히` 조합하여 HotplaceView에 넘겨주어야합니다.

여기서 말하는 **`적절히`** 라는 단어에 집중해주세요.
적절히라는 말은 서비스 기획 단에서 정해지는 **비즈니스(도메인) 로직**이 됩니다.
예를 들어, Review 정보를 노출할 때, 상위 rating 5개 정도만 노출할 수도 있고, 사진이 있는 리뷰만 노출 할 수도 있을 것입니다.

위의 구조를 그대로 사용하기에는 **Presenter** 로직에 **비즈니스 로직(도메인)** 이 섞여서 작성될 우려가 큽니다 !




# 4. Business Logic 분리 : Domain
자 ! 이제 Presenter 로직에서 비즈니스 로직을 분리해봅시다.
비즈니스 로직을 Presenter에서 분리하는 방법은 다음 방법들이 있습니다.

1. Service 를 이용해 비즈니스 로직을 분리
2. UseCase 를 이용해 비즈니스 로직을 분리
    - 부록) Interactor 를 이용해 비즈니스 로직을 분리

각각의 방법으로 비즈니스 로직을 분리해보도록 하겠습니다.

우선 시작하기전에, 모든 방법들에 공통적으로 구현되어야하는 것은 "Repository"를 각기 가지고 있어야 한다는 것입니다. 결국 **Data**는 **Repository**로 부터 오기 때문이죠.


## 1) Business Logic 분리 : Service
**Service Layer Pattern**에서 말하는 Service는 어플리케이션을 여러개의 계층(Layer)로 나누어서 관리하는 것을 의미합니다.

Layered Pattern에서는 **Presentation(UI)-Application(Service)-Business(Domain)-Data Access(Persistence) Layer**로 각기 나눠서 구조를 잡는데요~
Spring Framework 등으로 엔터프라이즈 어플리케이션 개발을 할 때, 다수의 Services들을 만들고, 비즈니스 로직을 구현하곤 합니다.
**Service들은 각기 다른 Services를 가지기도 하며**, 위에서 언급한 Repository와 같은 Domain Layer들을 품고 있습니다.


다음과 같은 구현이 가능하겠습니다.

### Presenter Code
```kotlin
class HotplacePresenter(
    view: HotplaceView,
    val service: HotplaceService) : Presenter<HotplaceView>(view) {
    ...
    fun onCreate(restaurantName: String, zipCode: String) {
        val (hotplace, review) = service.getHotplaceWithReview(restaurantName, zipCode)
        view.run {
            setName(hotplace.name)
            setZipCode(hotplace.zipCode)
            setMap(hotplace.map)

            refreshReview(review)
        }
    }
}
```

### 현재 Presenter 및 Repository 구조

![](/static/assets/img/posts/androidmodel/android-model-8.png)


점점 복잡해지고 있네요 ! 하지만, 아직까지는 그리 복잡한 레이어는 아닙니다.
Presenter는 단일 HotplaceService를 가지고 있고, HotplaceService를 통해서 Hotplace 및 HotplaceReview 정보들을 가져옵니다.

이렇게 "계층화"된 코드들로 인해, **코드의 중복을 최대한 피할 수 있을 뿐더러**, 각 Service에 필요한 비즈니스 로직 및 다른 처리들 (Logging, 예외 처리 등)을 할 수 있는 장점이 있습니다.

다만, Service가 다른 Service를 가지는 계층 구조는 시스템이 커질수록 더욱 복잡한 관계를 띄게 됩니다. 서로가 서로에 대한 의존성이 강하게 걸려있어서, 서비스가 커지고 계층이 깊어질 수록 코드 유지보수는 힘들어집니다. 처음 설계시 골똘히 생각하지않고 다중 레이어로 마구마구 개발하다보면 프로젝트는 산으로 가겠지요.



## 2) Business Logic 분리 : UseCase
**Service** 와는 다르게 UseCase Pattern은 Layered 구조를 가지지 않습니다.
각기 다른 UseCase 들은 서로에게 독립적입니다. 즉 의존 관계가 없습니다. 유지보수 비용이 저렴한 대신, 코드의 중복은 다소 발생할 수 있습니다. 
**UseCase**를 구현하기위해서는 말 그대로 **한 개의 유저의 행동**에 매칭하는 **한 개의 UseCase**를 정의해야합니다.
(코드의 재활용을 신경쓰는 것보다, 유저의 각기 다른 행동에 하나의 유즈케이스를 매칭하는 설계가 매우 중요합니다.)


위의 예시에서는 Hotplace와 HotplaceReview 의 정보를 함께 사용하는 UseCase가 필요합니다.
다음과 같은 UseCase를 작성할 수 있겠네요.


```kotlin
abstract class UseCase<T, in Params> {
    protected abstract fun buildUseCase(params: Params): T
}

class GetHotplaceAndReview (
    val hotplaceRepository: HotplaceRepository,
    val hotplaceReviewRepository: HotplaceReviewRepository)
    : UseCase<Pair<Hotplace, List<HotplaceReview>>, Pair<String, String>>() {

    override fun buildUseCase(params: Pair<String, String>) : Pair<Hotplace, List<HotplaceReview>> {
        val hotplace = hotplaceRepository.getHotplace(params.first, params.second)
        val hotplaceReview = hotplaceReviewRepository.getHotplaceReview()

        return hotplace to hotplaceReview
    }
)
```

![](/static/assets/img/posts/androidmodel/android-model-9.png)


### Presenter Code
```kotlin
class HotplacePresenter(
    view: HotplaceView,
    val useCase: GetHotplaceAndReviewUseCase) : Presenter<HotplaceView>(view) {
    ...
    fun onCreate(restaurantName: String, zipCode: String) {
        val (hotplace, review) = useCase.buildUseCase(restaurantName to zipCode)
        view.run {
            setName(hotplace.name)
            setZipCode(hotplace.zipCode)
            setMap(hotplace.map)

            refreshReview(review)
        }
    }
}
```


### 부록) Business Logic 분리 : Interactor ~= UseCase
**Interactor**의 개념을 쉽게 이해할 수 있는 프레임워크는 iOS 개발때 자주 사용하는 **VIPER Framework**입니다. 
이 글은 Android 의 Model 위주로 설명을 하고있긴 하지만, 우리가 사용하는 **M Something 등의 패턴**들은  웹 프레임워크나 iOS 등에서도 널리 사용하고 있습니다. 
여기 부록에서는 iOS의 VIPER 프레임워크 구조를 잠깐 살펴보고, 사실은 이름만 다를뿐 **UseCase**와 지향점이 같다는 점을 훑어보고 가겠습니다.

VIPER는 다음과 같은 아키텍쳐를 따르고 있습니다.

![](/static/assets/img/posts/androidmodel/android-model-10.png)

**Interactor** 라는 패턴도 결국은 Data(**Entity**)를 담고 있으며, Entity를 가공하여 Presenter에 "순수한 데이터" 형태로 넘기는 역할을 합니다.
즉, **Interactor**에 모든 비즈니스 로직이 들어가게 되는 것이죠.



## 종합
**"4. Business Logic 분리 : Domain"** 에서는 Repository(Data + Source)로 이루어져있던 우리의 맛집앱을 참- 많이도 수정하였습니다.
Presenter에 존재하였던 **비즈니스 로직**을 분리하여 **Domain**(UseCase or Services)을 분리해내는 작업을 하였고, 우리의 아키텍쳐는 다음과 같이 나타낼 수 있습니다.


## 현재 아키텍쳐

![](/static/assets/img/posts/androidmodel/android-model-11.png)


# 5. Exception Handling : Domain Logic
우리는 없던 문제까지 만들어가며, 훌륭한 구조로 맛집 앱을 개발했습니다.
마지막으로, 한가지만 !! 더 문제를 만들어보고, 이 글을 마무리하도록 합시다 : )

위에서 다룬 많은 레이어들은 "올바른 데이터"를 주고 받는 것을 전제로 짠 것 같습니다.
하지만, 만약, Exception Case에는 어떻게 반응해야할까요? 만약 다음과 같이 Presentation 로직에서 익셉션을 핸들링하면 어떻게 될까요?
아래 예에서는 **"403 HttpException"** 케이스에 대하여, 뷰에 에러 상황을 알려주는(**view.showErrorMessage("403")**) 코드를 작성하였습니다.

예외 처리 위해 Rx를 사용하여 아래처럼 처리하였습니다.  
갑자기 Rx가 툭 튀어나왔지만, Rx를 모르시는 분들은 subscribe 메소드의 onError 파라미터 및 handleExceptionCase() 메소드를 참고하시면 될 것 같습니다.


### Presenter Code
```kotlin
class HotplacePresenter(
    view: HotplaceView,
    val useCase: GetHotplaceAndReviewUseCase) : Presenter<HotplaceView>(view) {
    ...
    fun onCreate(restaurantName: String, zipCode: String) {
        useCase.buildUseCase(
            restaurantName to zipCode
        ).subscribe(
            onSuccess = { (hotplace, review) ->
                view.run {
                    setName(hotplace.name)
                    setZipCode(hotplace.zipCode)
                    setMap(hotplace.map)

                    refreshReview(review)
                }
            },
            onError = ::handleExceptionCase
        )
    }

    fun handleExceptionCase(throwable: Throwable) {
        when (throwable) {
            is HttpException -> {
                if (throwable.code == 403) {
                    view.showErrorMessage("403 Forbidden")
                } else {
                    view.showErrorMessage("${throwable.code} ${throwable.message}")
                }
            }
        }
    }
}
```


### Presenter Code
```kotlin
class HotplacePresenter(
    view: HotplaceView,
    val useCase: GetHotplaceAndReviewUseCase) : Presenter<HotplaceView>(view) {
    ...
    fun onCreate(restaurantName: String, zipCode: String) {
        val result = useCase.buildUseCase(restaurantName to zipCode)

        when (result) {
            is Left -> handleExceptionCase(result.value)
            is Right -> {
                val (hotplace, review) = result.value
                view.run {
                    setName(hotplace.name)
                    setZipCode(hotplace.zipCode)
                    setMap(hotplace.map)

                    refreshReview(review)
                }
            }
        }
    }

    fun handleExceptionCase(throwable: Throwable) {
        when (throwable) {
            is HttpException -> {
                if (throwable.code == 403) {
                    view.showErrorMessage("403 Forbidden")
                } else {
                    view.showErrorMessage("${throwable.code} ${throwable.message}")
                }
            }
        }
    }
}
```

## 문제..점!
위와 같이 개발을 하였다면, 문제점은 어디에 있을까요? 우리가 지금껏 보아왔던 것은 바로 **`(MVP 등의) 각 컴포넌트가 가져야 할 책임"`** 이었습니다.
> HttpException 등 세부 익셉션을 핸들링해야하는 것은 Presenter가 아닙니다.

Presenter에서는 오로지 View에 렌더링할 요소들만 Domain 로직으로 부터 받아서, View에 전달하기만 하면 될 뿐이죠.
Network Exception 코드를 위와 같이 if-else 문으로 핸들링한다면, 아무래도 **View**로 인해 거대해질 Presenter 코드는 더욱 엉망이 되고 말것입니다.



## 해결법
해결법은 간단합니다. HttpException 등을 핸들링하는 로직을 모두 **Model(Domain)** 레이어로 옮기는 것입니다.
Application에서 사용할 **고유한 Exception 도메인을 정의**하고(3rd party exception을 래핑, 매핑, 재정의), 정의한 Exception 객체만 Presenter에 전달하면 코드는 깔끔해집니다.

### UseCase Code

```kotlin
// Presenter에서 Exception을 핸들링하던 로직은 UseCase, 즉 Domain 레이어에 구현하도록 합니다.

class GetHotplaceAndReview (
    val hotplaceRepository: HotplaceRepository,
    val hotplaceReviewRepository: HotplaceReviewRepository)
    : UseCase<Either<Throwable, Pair<Hotplace, List<HotplaceReview>>>, Pair<String, String>>() {

    override fun buildUseCase(params: Pair<String, String>) : Either<Throwable, Pair<Hotplace, List<HotplaceReview>>> {
        try {
            val hotplace = hotplaceRepository.getHotplace(params.first, params.second)
            val hotplaceReview = hotplaceReviewRepository.getHotplaceReview()
        } catch (e: NetworkException) {
            return Left(NetworkException("Forbidden Exception"))
        } catch (e: HttpException) {
            return Left(ForbiddenException("Forbidden Exception"))
        }

        return Right(hotplace to hotplaceReview)
    }
)
```

### 개선된 Presenter Code
```kotlin
class HotplacePresenter(
    view: HotplaceView,
    val useCase: GetHotplaceAndReviewUseCase) : Presenter<HotplaceView>(view) {
    ...
    fun onCreate(restaurantName: String, zipCode: String) {
        val result = useCase.buildUseCase(restaurantName to zipCode)

        when (result) {
            is Left -> handleExceptionCase(result.value)
            is Right -> {
                val (hotplace, review) = result.value
                view.run {
                    setName(hotplace.name)
                    setZipCode(hotplace.zipCode)
                    setMap(hotplace.map)

                    refreshReview(review)
                }
            }
        }
    }

    // 변경된 handleExceptionCase
    fun handleExceptionCase(throwable: Throwable) {
        when (throwable) {
            is ForbiddenException -> view.showErrorMessage("Forbidden")
            is NetworkException -> view.showErrorMessage("Network Error")
            else -> view.showErrorMessage("${throwable.code} ${throwable.message}")
        }
    }
}
```


# 글을 맺으며
Android 기준의 글을 쓰긴했지만, Model 이라는 레이어, 컴포넌트는 다양한 플랫폼에서 구현하고 있는 개념일 것입니다.

**`데이터를 저장하고, 비즈니스 로직을 적용하는 레이어`** 라는 명확한 정의가 있음에도, 실제 프로젝트에서는 책임이 명확하게 분리되지 않은 경우를 종종 봅니다.

현실적으로 Model 등, 책임을 나누기 어려운 경우들이 많이 발생한 것은 시간이 지날수록 조금씩 엇나가며 개발되었기 때문은 아닐까요.

**`Clean Architecture`** 는 정답은 아닙니다. 수많은 단어들과 패턴들은 유일한 정답이 아니라는 소리죠. 하지만, 많은 이들의 검증을 받아온 개념인것 만큼, **Clean Architecture**과 위 본문에서 나오는 **철학**들에 공감하고, 실제 코드에 녹여나가는 연습/실전이 되었으면 합니다.