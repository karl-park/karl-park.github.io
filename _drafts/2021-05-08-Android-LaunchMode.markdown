PIP 모드에서 단일 재생 활동 사용
앱에서 동영상 재생 활동이 PIP 모드인 동안 사용자가 기본 화면에서 콘텐츠를 탐색할 때 새 동영상을 선택하는 경우가 있습니다. 사용자를 혼란스럽게 할 수 있는 새로운 활동을 실행하는 대신 기존 재생 활동의 새 동영상을 전체 화면 모드로 재생합니다.

동영상 재생 요청에 단일 활동을 사용하고 필요에 따라 PIP 모드로 전환해 들어가거나 나오려면 매니페스트에서 활동의 android:launchMode를 singleTask로 설정하세요.


"standard"
	- default
	- multiple instance : yes
	- 기본입니다. 시스템이 항상 대상 작업에 새 액티비티 인스턴스를 생성하고 인텐트를 해당 인스턴스로 라우팅합니다.
	- Default. The system always creates a new instance of the activity in the target task and routes the intent to it.
"singleTop"
	- multiple instance : yes(Conditionally)
	- 액티비티의 인스턴스가 이미 대상 작업의 맨 위에 존재하는 경우 시스템은 새 액티비티 인스턴스를 생성하는 대신 onNewIntent() 메서드를 호출하여 인텐트를 해당 인스턴스로 라우팅합니다.
	- If an instance of the activity already exists at the top of the target task, the system routes the intent to that instance through a call to its onNewIntent() method, rather than creating a new instance of the activity.
"singleTask"
	- multiple instance : no
	- 시스템이 새 작업의 루트에 액티비티를 생성하고 인텐트를 해당 액티비티로 라우팅합니다. 그러나 액티비티 인스턴스가 이미 존재하는 경우 시스템은 새 인스턴스를 생성하는 대신 onNewIntent() 메서드를 호출하여 인텐트를 기존 인스턴스로 라우팅합니다.
	- The system creates the activity at the root of a new task and routes the intent to it. However, if an instance of the activity already exists, the system routes the intent to existing instance through a call to its onNewIntent() method, rather than creating a new one.
	- 전체 tasks를 통틀어서, 1개의 instance 만 존재함.
	- 이미 어떤 특정 task의 stack에 activity instance가 존재한다면, 해당 activity 위의 다른 activities 를 모두 onDestroy() 하고 onNewIntent()를 호출함. 그게 아니라면, 새로 인스턴스를 생성함.
"singleInstance"
	- multiple instance : no
	- singleTask"와 동일합니다. 단, 인스턴스가 있는 작업에 대해 시스템이 어떤 액티비티도 시작하지 않은 경우는 예외입니다. 액티비티는 항상 해당 작업의 단일 멤버입니다.
	- Same as "singleTask", except that the system doesn't launch any other activities into the task holding the instance. The activity is always the single and only member of its task
	- singleTask와 동일하나, 해당 task안에 다른 activities가 존재 할 수 없음. 즉 startActivity()를 못함.
	




https://yebon-kim.tistory.com/6