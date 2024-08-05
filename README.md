# COVI iOS SDK 연동 가이드

## 목차
- [연동 프로세스](#연동-프로세스)

- [SDK 설치](#sdk-설치)
    - [Cocoapods](#cocoapods-사용해-설치)
    - [SPM](#spm-사용해-설치)

- [COVI 광고 삽입](#covi-광고-삽입)
    - [SDK 사용](#sdk-사용)
    - [COVI 플레이어 생성](#covi-플레이어-생성)
        - [Code(programmatic) 방식으로 COVI 플레이어 삽입](#codeprogrammatic-방식으로-covi-플레이어-생성)
        - [Storyboard 방식으로 COVI 플레이어 삽입](#storyboard-방식으로-covi-플레이어-생성)
    - [옵션 설정](#옵션-설정)
        - [옵션 설명](#옵션-설명)
    - [이벤트 핸들러 설정](#이벤트-핸들러-설정)
    - [COVI 플레이어 로드](#covi-플레이어-로드)

- [오류코드](#오류코드)

<br/>

# 연동 프로세스
<img width="770" alt="image" src="https://cnp-file.dev.covi.co.kr/docs/covi-ad-sdk/iOS/process.webp">

<br/>

# SDK 설치

## `Cocoapods` 사용해 설치
- 프로젝트의 Podfile에 `COVI-iOS-SDK`를 추가합니다.
- `pod install`명령을 실행합니다.

```ruby
## Podfile
use_frameworks!
target 'App' do
  pod 'COVI-iOS-SDK'
end
```

## `SPM` 사용해 설치
- Project > Package Dependencise 항목을 선택합니다.
- `+` 버튼을 눌러 패키지 설치 윈도우가 나오면 우측 상단의 검색 창에서 패키지명 COVI-iOS-SDK 또는 레포지토리 주소 https://github.com/covigroup/COVI-iOS-SDK.git 를 입력합니다.

<br/>

# COVI 광고 삽입

## SDK 사용
- COVI 플레이어를 삽입할 ViewController가 위치한 파일 상단에 `covisdk`를 import 합니다.
``` swift
import covisdk
```

<br/>

## COVI 플레이어 생성

### `Code(programmatic)` 방식으로 COVI 플레이어 생성

- COVI 플레이어를 삽입할 View의 ViewController에 coviPlayer 인스턴스를 적당한 크기로 생성합니다.
``` swift
let coviPlayer = CoviPlayer(frame: CGRect(x: 0, y: 0, width: 400, height: 200))
```

<br/>


### `Storyboard` 방식으로 COVI 플레이어 생성
- Storyboard 페이지에 UIView를 생성합니다.

- 생성한 UIView의 Custom Class 항목에서 Class를 CoviPlayer로 설정합니다.

- GUI로 COVI 플레이어를 삽입할 View의 하위에 CoviPlayer를 위치 시킵니다.

- GUI로 CoviPlayer를 COVI 플레이어를 삽입할 View의 ViewController와 연결합니다.

<br/>

## 옵션 설정
- COVI 광고 요청을 위한 옵션 값을 할당합니다.

``` swift
func setCoviOpts () {

    // 필수
    coviPlayer.type = "dev"
    coviPlayer.pcode = "매체사 코드"
    coviPlayer.category = "매체사 카테고리"
        
    // 권장
    coviPlayer.age = 20
    coviPlayer.gender = "M"
    coviPlayer.idfa = "test-idfa-value"
        
    // 선택
    coviPlayer.playType = .auto_play
    // coviPlayer.useInAppWebview = false
    coviPlayer.appViewController = self
}
```

<br/>

### 옵션 설명

| 이름     | 타입    | 설명                                                         | 필수여부              |
| -------- | ------- | ------------------------------------------------------------ | --------------------- |
| type     | String  | 개발버전, 상용버전 구분<br><font color="dodgerblue">dev</font>: 개발버전 (기본값)<br><font color="orange">prod</font>: 상용버전<br> * 개발버전에서 테스트 완료 후 승인 절차를 걸쳐 승인 완료 후 사용해야 합니다. | <strong>필수</strong> |
| pcode    | String  | 매체사 코드 | <strong>필수</strong> |
| category | String  | 매체사 카테고리                      | <strong>필수</strong> |
| age      | Int | 연령                       | 권장                  |
| gender   | String  | 성별                       | 권장                  |
| idfa     | String  | iOS 광고 ID            | 권장                  |
| playtype | Enum  | 비디오 재생 방법<br> .auto_play: 자동 재생 (기본값)<br>.click_to_play: 클릭 재생 | 선택                  |
| useInAppWebview | Bool  | 광고주 랜딩페이지로 이동할 때 인앱 웹뷰 사용 여부<br> true: 인앱 웹뷰 사용 (기본값)<br>false: 외부 브라우저 사용 | 선택                  |
| appViewController | UIViewController  | 인앱 웹뷰 사용을 위해 UIViewController를 SDK에 전달합니다.<br>* SDK에서 적절한 UIViewController를 찾을 수 없을 시 광고주 랜딩페이지로 이동할 때 외부 브라우저를 사용합니다. | 선택                  |

<br/>

## 이벤트 핸들러 설정
- COVI 플레이어의 이벤트 핸들러 함수를 설정합니다. 
``` swift
func coviEventHandler(event:String) {
    switch (event) {
    case "VAST_LOAD_SUCCESS":
        print("[covi] VAST_LOAD_SUCCESS")

    case "VAST_LOAD_FAIL":
        print("[covi] VAST_LOAD_FAIL")

    case "SDK_NO_ADS":
        print("[covi] no ads");
            
    case "PLAYER_VIDEO_PLAY":
            
    case "PLAYER_VIDEO_PAUSE":
            
    case "PLAYER_VIDEO_REPLAY":
                
    case "PLAYER_VIDEO_ENDED":
        break;
            
    default:
        break;
    }
}
```

<br/>

## COVI 플레이어 로드
- `viewDidLoad`에서 COVI 옵션을 설정하고, COVI 플레이어를 로드합니다.

```swift
override func viewDidLoad() {
    super.viewDidLoad()

    // COVI 옵션 설정
    setCoviOpts()

    // COVI 플레이어 로드
    coviPlayer.loadContent(coviEventHandler: coviEventHandler)
}
```

<br/>

# 오류코드
        
Code|Message|해결방법
---|---|---
E001|type 값이 정의되지 않았습니다.|type 정보 추가
E002|pcode 값이 정의되지 않았습니다.|pcode 정보 추가
E003|매체사 정보가 올바르지 않습니다. 담당자에게 문의해 보세요.|담당자에게 문의해서 매체사 코드 확인 후 pcode에 적용
E004|category 값이 정의되지 않았습니다.|매체사 category 정보 추가
E100|현재 진행중인 광고가 없습니다.|담당자에게 문의
---
