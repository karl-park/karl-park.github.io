---
layout: post
title:  "[WebView Debugging] Android, iOS"
date:   2018-10-25 09:00:00
tags: etc android ios webview debugging
categories: devstory
---

# [WebView Debugging] Android, iOS

Android, iOS 개발을 하다보면 WebView를 붙일 때가 많습니다. 단순히 앱의 일부 프로세스에 WebView를 사용하거나 웹앱 기반의 하이브리드앱을 개발하기도 합니다. 유니티 등으로 게임을 개발하더라도, "공지사항" 등에는 웹뷰를 연동하는 경우가 많지요.

이 때, 디버그 모드로 앱을 디버깅하다보면 "웹뷰" 부분에서 디버깅이 막히는 것을 경험 할 수 있습니다.

본문에서는 Android, iOS 웹뷰를 디버깅 하는 법을 설명합니다.


### Prerequisite
- Android, iOS Device (에뮬레이터, 시뮬레이터로 대체 가능합니다)
- USB Connector (Between mobile device and desktop, 에뮬/시뮬레이터 사용이라면 굳이 필요없습니다)
- Android : Chrome Browser
- iOS : Safari Browser


# Android WebView Debugging
안드로이드에서 웹뷰를 사용하기 위해서는, Activity 내부에서 WebView 객체를 생성하여 load 하는 식으로 사용합니다.
해당 웹뷰 호출시 디버그 모드를 사용하겠다는 메소드 호출을 하고난 후, 크롬 브라우저로 디버깅합니다.


## 1. Add a code block for debugging
WebView 객체의 setWebContentsDebuggingEnabled를 true로 설정하여 디버깅 모드를 설정할 수 있습니다.

<b style="color:red">Android 웹뷰 디버그 모드는 API 19(Kitcat) 이상부터 지원되므로, 다음과 같은 버전 체크가 필요합니다.</b>

```kotlin
// kotlin
class MyActivity: Activity() {
    private lateinit var mWebView: WebView
    override fun onCreate(savedInstanceState: Bundle?) {
        mWebView = WebView()
        ...
        
        // Set DebugMode (default value is false)
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.KITKAT) {
            WebView.setWebContentsDebuggingEnabled(true)
        }
        mWebView.loadUrl("https://www.google.com")
    }
    ...
}
```

```java
// java
public class MyActivity extends Activity {
    private WebView mWebView;

    @Override 
    public void onCreate(Bundle savedInstanceState) {
        mWebView = new WebView();
        ...
        // Set DebugMode (default value is false)
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.KITKAT) {
            WebView.setWebContentsDebuggingEnabled(true);
        }
        mWebView.loadUrl("https://www.google.com");
    }
    ...
}
```


## 2. Chrome Browser
위와 같이 코드를 추가 한 후, 빌드를 하고 USB 연결 후(시뮬레이터도 가능합니다), 크롬 브라우저를 엽니다. 크롬 브라우저의 "개발자 도구"를 연 후, `Remote Devices` 를 클릭하여 연결되어있는 원격 디바이스 목록을 볼 수 있습니다.
![1.png](/static/assets/img/posts/webviewdebugging/1.png)

만약 연결된 디바이스에 아무런 `웹뷰`가 켜져있지 않다면 ***No browsers detected.*** 라는 문구만 노출됩니다.

단순한 크롬 브라우져만 실행하더라도, 다음과 같은 화면을 볼 수 있습니다.
![2.png](/static/assets/img/posts/webviewdebugging/2.png)
![3.png](/static/assets/img/posts/webviewdebugging/3.png)


실제 앱에서 웹뷰를 켜면 다음과 같은 화면을 볼 수 있습니다.
![4.png](/static/assets/img/posts/webviewdebugging/4.png)

여기서 `Inspect` 버튼을 누르면, 웹개발 때 디버깅을 하듯, 안드로이드 앱의 웹뷰를 디버깅 할 수 있습니다. 물론, console, network, performance 등의 탭을 이동하며 디버깅/프로파일링 등을 모두 수행할 수 있습니다.
![5.png](/static/assets/img/posts/webviewdebugging/5.png)
![6.png](/static/assets/img/posts/webviewdebugging/6.png)




# iOS WebView Debugging
## 1. Turn on the Web Inspector
웹뷰 디버깅을 사용하시려면, iOS에서는 별다른 코드 추가 없이 디버깅을 하실 수 있습니다. 
다만, Web Inspector 기능을 설정부에서 켜주셔야합니다. (시뮬레이터에서는 Web Inspector 버튼이 노출이 안될 수도 있다고 합니다.)

아이폰의 `설정 > 사파리 > Advanced > Web Inspector` 에 체크해줍니다.
![7.png](/static/assets/img/posts/webviewdebugging/7.png)

그 후, 웹뷰를 실행해줍니다. 이번 예제에서는 다음과 같은 코드로 웹뷰를 실행했습니다.
```swift
import UIKit
import WebKit

class ViewController: UIViewController, WKUIDelegate {
    var webView: WKWebView!
    
    override func loadView() {
        let webConfig = WKWebViewConfiguration()
        webView = WKWebView(frame: .zero, configuration: webConfig)
        webView.uiDelegate = self
        view = webView
    }
    
    override func viewDidLoad() {
        super.viewDidLoad()

        let url = URL(string: "https://www.google.com")
        let request = URLRequest(url: url!)
        webView.load(request)
    }
}
```

## 2. Safari Browser
사파리 설정에서 "개발자 메뉴"를 켜려면 다음과 같이 설정을 해줍니다.

사파리 브라우저를 켠 후, 사파리 환경설정의 고급란에서 "개발자용 메뉴보기"에 체크해줍니다.
![8.png](/static/assets/img/posts/webviewdebugging/8.png)

그 후, 개발자용 메뉴에서 현재 연결된 디바이스를 선택하여 디버깅을 할 수 있습니다.
본문에서는 "시뮬레이터"로 웹뷰를 띄웠으므로, 아래와 같이 들어가서 디버깅을 할 수 있습니다.
![9.png](/static/assets/img/posts/webviewdebugging/9.png)

www.google.com 이 웹뷰에 올라와있는 것을 볼 수 있고, "개발자 도구"창을 통해 해당 웹뷰 디버깅을 할 수 있음을 볼 수 있습니다.
![10.png](/static/assets/img/posts/webviewdebugging/10.png)