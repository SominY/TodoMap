# Todo Map Mobile Application - 위치기반 To do 앱
<br>

### 🚀 &nbsp; 제작기간 &nbsp; : &nbsp; 24.6.1 - 24.7.11 <br><br>
### 🚀 &nbsp; 팀원 &nbsp; : &nbsp; 4명 (팀원) <br><br>
### 🚀 &nbsp; 담당역할 <br>
&nbsp;&nbsp;&nbsp;&nbsp; - DB 정의서 작성 <br>
&nbsp;&nbsp;&nbsp;&nbsp; - 할 일 목록, 할 일 상세 페이지 구현
<br><br>

### ⚒️ &nbsp; 개발환경 <br>
- 언어 &nbsp; - &nbsp; Kotlin
- IDE &nbsp; - &nbsp; Android Studio Bumblebee | 2021.1.1 Canary 11
- SDK 버전 &nbsp; - &nbsp; Compile SDK 33, Target SDK 33
- Gradle JDK &nbsp; - &nbsp; 17
- Dependency
  * Room 데이터베이스
  * RecyclerView 
  * Material Design 캘린더 뷰 라이브러리
  * 이모티콘 지원 라이브러리
  * ViewModel 및 LiveData와 관련된 AndroidX 라이브러리
  * Google Maps 서비스 라이브러리
  * Google Location 서비스 라이브러리
  * 한글설정 라이브러리

 <br><br>

## My Code Review

### 1. DB package &nbsp; : &nbsp; AppDatabase &nbsp; | &nbsp; MyApp &nbsp; | &nbsp; TodoDao &nbsp; | &nbsp; TodoEntity

- AppDatabase &nbsp; - &nbsp; Room 라이브러리를 사용하여 데이터베이스를 설정하고 관리
  * companion object{} &nbsp; : &nbsp; 코틀린에서 companion object는 클래스의 정적 멤버에 접근하기 위해 사용. AppDatabase의 단일 인스턴스를 생성하고 반환하며, 이는 애플리케이션 전체에서 데이터베이스 연결을 공유하는 데 사용
- MyApp &nbsp; - &nbsp; 데이터베이스와 리포지토리를 초기화하는 역할
  * 초기화된 데이터베이스와 리포지토리 인스턴스는 companion object를 통해 전역적으로 접근할 수 있으며, 이를 통해 앱 전역에서 데이터베이스와 리포지토리를 공유
- TodoDao &nbsp; - &nbsp; Room 라이브러리를 사용하여 ToDoEntity 테이블과 상호작용하는 다양한 메서드를 정의
  * 이 인터페이스에서 정의된 메서드들은 애노테이션을 사용하여 SQL 쿼리와 연동
  * LiveData<List> &nbsp; : &nbsp; 이 메서드는 LiveData로 래핑된 List<ToDoEntity>를 반환함. LiveData는 관찰 가능한 데이터 홀더로, 데이터 변경 시 UI가 자동으로 업데이트되도록 함.
- TodoEntity &nbsp; - &nbsp;Room 라이브러리를 사용하여 데이터베이스 테이블을 나타내는 ToDoEntity 클래스

  <br>

### 2. TodoRepository &nbsp; - &nbsp; 데이터베이스와 통신하는 중개자 역할

- ToDoDao를 사용하여 데이터베이스 작업을 추상화하고, ViewModel이나 다른 데이터 소비자에게 데이터베이스의 데이터를 제공
- 삽입, 삭제, 업데이트 및 쿼리 작업을 수행하는 메서드를 포함하고 있으며, LiveData를 사용하여 데이터 변경 사항을 관찰하고 UI를 자동으로 업데이트
- 데이터베이스 작업과 UI 로직을 분리하여 코드의 유지보수성을 높이고, 데이터 일관성을 유지

<br>

### 3. viewmodels &nbsp; : &nbsp; TodoViewModel &nbsp; | &nbsp; TodoViewModelFactory

- TodoViewModel &nbsp; - &nbsp; MVVM 아키텍처의 ViewModel 역할을 하며, UI와 데이터 간의 중개자 역할
  * AndroidViewModel을 상속받아 애플리케이션 컨텍스트에 접근할 수 있으며, TodoRepository를 사용하여 데이터베이스 작업을 수행
  * 코루틴을 사용하여 비동기적으로 데이터베이스 작업을 처리하여 UI 스레드를 차단하지 않도록 함
  * 이를 통해 데이터베이스 작업과 UI 로직을 분리하고, 데이터 변경 시 UI가 자동으로 업데이트되도록 함
- TodoViewModelFactory &nbsp; - &nbsp; ViewModelProvider.Factory를 구현하여 TodoViewModel의 인스턴스를 생성하는 팩토리 역할
  * Application과 TodoRepository를 생성자 매개변수로 받아 TodoViewModel을 생성
  * ViewModelProvider.Factory 인터페이스를 구현하여 create 메서드를 오버라이드하고, TodoViewModel 인스턴스를 생성 및 반환
  * 이 팩토리 클래스를 사용하면 ViewModel에 필요한 매개변수를 안전하게 전달
 
<br>

### 4. TodoRecyclerViewAdapter  &nbsp; -  &nbsp; RecyclerView의 어댑터 역할을 하며, ToDoEntity 리스트를 관리하고 각 항목의 UI를 설정
- 각 항목의 뷰는 ItemTodoBinding을 통해 설정되며, 체크 상태 변경, 삭제, 클릭 등의 이벤트를 처리
- 데이터 변경 시 updateData 메서드를 사용하여 RecyclerView의 데이터를 업데이트

<br>

### 5. TodoListFragment  &nbsp; -  &nbsp; 할 일 목록을 관리하는 프래그먼트
- RecyclerView를 설정하고 ViewModel을 관찰하여 데이터를 업데이트하며, 항목 클릭, 스와이프 등 다양한 이벤트를 처리
- 모든 할 일이 완료되었을 때 알림을 표시

<br>

### 6. TodoDetailActivity &nbsp; - &nbsp; 할 일의 상세 정보를 보여주고 수정하는 액티비티
- ViewModel을 사용하여 데이터베이스와 상호작용하며, UI를 업데이트하고 사용자의 입력을 처리
- 이모티콘 팝업, 카테고리 선택, 날짜 선택, 알림 설정 등의 기능을 제공

<br>

### 7. AlarmReceiver &nbsp; - &nbsp; 앱에서 설정한 알람을 수신하고 알람 시간에 맞춰 사용자에게 알림을 생성
- 백그라운드 실행: CoroutineScope 및 Dispatchers.IO를 사용하여 메인 스레드를 차단하지 않고 데이터베이스 작업을 처리
- NotificationManager: 시스템 서비스에서 알림 관리자를 가져옴
- Notification Channel: Android Oreo 이상에서 알림 채널을 생성하여 새로운 Android 버전에서도 알림이 제대로 동작하도록 함
- Notification Intent: 알림을 클릭했을 때 실행할 TodoDetailActivity를 시작하는 인텐트를 설정
- PendingIntent: 알림 클릭 시 실행할 인텐트를 설정하여 사용자가 TODO 항목을 살펴볼 수 있도록 함
- NotificationCompat.Builder: NotificationCompat.Builder를 사용하여 작은 아이콘, 제목, 내용 텍스트, PendingIntent(알림 클릭 시 실행할 동작), 자동 취소 설정을 포함하여 알림을 구성

