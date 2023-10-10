based on https://github.com/baekteun/Tuist_Modular_Template


## 레이어 
Features - Services - Core - UserInterface - Shared
5개의 레이어를 가집니다.

- Feature
  - 사용자의 액션을 처리하거나 데이터를 보여주는, 사용자와 직접 맞닿는 레이어
  - ex) AuthFeature, ProfileFeature
- Domain
  - 도메인 로직이 진행되는 레이어
  - ex) AuthDomain, ProfileDomain
- Core
  - 앱의 비즈니스를 포함하지 않고 순수 기능성 모듈이 위치한 레이어
  - ex) NetworkingModule, DatabaseModule
- UserInterface
  - 공용 View, 디자인 시스템, 리소스 등 UI 요소 모듈이 위치한 레이어
  - ex) DesignSystem, LocalizableManager
- Shared
  - 로깅, extension 등 모든 레이어에서 공용으로 재사용될 모듈이 위치한 레이어
  - ex) UtilityModule, LoggingModule

## Micro Feature
각 모듈은 Micro Feature 구조를 기반으로 설계됩니다.
확장 가능하고 커지는 프로젝트를 기능별로 수평 확장이 가능하도록 Micro Service에서 영감을 얻은 아키텍쳐입니다.

<img src="https://user-images.githubusercontent.com/74440939/210211725-5ac7c9fe-bf25-4707-9775-4f46f1c0c522.png" width="200">

##### https://docs.tuist.io/building-at-scale/microfeatures/#product

## 프로젝트 세팅
프로젝트 루트에서 `make init` 를 실행하여, 프로젝트 이름과 organization 이름을 입력하여 기본 설정을 할 수 있습니다.

프로젝트 루트에서 `make signing`를 실행하면 프로젝트 Team Signing을 할 수 있습니다.

## 모듈 생성
프로젝트 루트에서 `make module`를 실행하면 모듈 레이어, 이름, Micro Feature 종류를 선택하여 새 모듈을 생성합니다.

## Makefile
프로젝트 루트에서 실행할 수 있는 명령어입니다.
- make init : `프로젝트 이름과 organization을 입력하여 프로젝트 기본 세팅`
  - swift Scripts/InitEnvironment.swift

- make signing : `프로젝트 Team Signing`
  - swift Scripts/CodeSigning.swift

- make generate : `외부 디펜던시 fetch 및 프로젝트 generate`
  - tuist fetch
  - tuist generate

- make module : `모듈 생성`
  - swift Scripts/GenerateModule.swift

- make dependency : `디펜던시 추가`
  - swift Scripts/NewDependency.swift

- make ci_generate : `디펜던시 fetch 및 CI용 프로젝트 generate (SwiftLint X)`
  - tuist fetch
  - TUIST_ENV=CI tuist generate

- make cd_generate : `디펜던시 fetch 및 CI용 프로젝트 generate (SwiftLint X)`
  - tuist fetch
  - TUIST_ENV=CD tuist generate

- make clean : `전체 xcodeproj, xcworkspace 파일 삭제`
  - rm -rf **/*.xcodeproj
  - rm -rf *.xcworkspace

- make reset : `tuist clean 후, 전체 xcodeproj, xcworkspace 파일 삭제`
  - tuist clean
  - rm -rf **/*.xcodeproj
  - rm -rf *.xcworkspace

## Scaffold
```sh
tuist Scaffold(Demo/Interface/Sources/Testing/Tests/UITests) 
  --layer (Features/Services/Core/Shared/UserInterface 레이어 이름)
  --name (모듈 이름)
```

으로 Project 모듈의 Target 모듈을 직접 생성 가능합니다.


## 사용법
처음엔 make init으로 프로젝트 이름과 organization을 생성한다.
기본적으로 각 레이어에 하나씩 디폴트로 들어가있다.
- Feature
  - BaseFeature
- Domain
  - BaseDomain
- Core
  - Networking
- UserInterface
  - DesignSystem
- Shared
  - GlobalThirdPartyLibrary

모듈 생성 시 make module 명령어로 해당 모듈이 가질 모듈을 전부 생성.
만약 잘못 생성했다면, `Plugin/DependencyPlugin/ProjectDescriptionHelpers/ModulePaths` 로 가서 해당 case를 지워주고, `Projects` 폴더 하위에 생성된 폴더를 삭제해야 한다. 또, XCConfig파일 안에 해당 Feature에 해당하는 폴더를 전부 지워주어야 한다.
만든 모듈을 연결하려면 `Projects` 폴더 하위에 `Project.swift` 파일에 dependencies 배열 안에 넣어주어야 한다.

의존성을 추가하려면 `make dependency` 명령어를 입력하면 된다. (SPM 기준)
네 가지를 입력해야 하는데, (1) dependency 이름, (2) Package 이름, (3) dependency github URL (4) 버전
- Dependency 이름 : Tuist에서 활용할 이 의존성의 이름(별명)을 정하면 된다. 아무거나 입력 가능.
- Package 이름 : 해당 패키지의 이름을 넣으면 된다. 아무거나 넣으면 안되고, 정해진 이름을 넣어야 한다. 예를 들어, TCA의 경우 `ComposableArchitecture`으로 넣어야 한다.
- Dependency Github URL : Github의 url을 넣으면 된다. 예를 들어, TCA의 경우 `https://github.com/pointfreeco/swift-composable-architecture.git` 를 넣으면 된다.
- exact 버전 : 해당 의존성의 버전을 명시해야 한다. Exact 버전으로 넣으면 되고, TCA의 경우 현재 최신인 `1.2.0` 을 넣었다.
의존성을 추가했으면 해당 모듈의 Project.swift로 가서 dependencies 배열에 `.SPM.TCA` 이런식으로 넣어주면 된다.
오류가 난다면 Package 이름이 잘못 된 것일 가능성이 크므로 잘 찾아보고 작성하자.

## 오류
만약 모듈을 만들 때 모듈이 없다는 에러가 뜨고 Interface 폴더가 생성이 안될 때는 빌드 폴더를 클린시켜주거나 `~/Library/Developer/Xcode/DerivedData` 폴더를 삭제하고 다시 시도해보자. 물론 위에 잘못 생성했을 때 지워주어야 하는 파일들도 전부 다시 지워주어야 한다.
