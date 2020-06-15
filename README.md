# Gradle
> 안드로이드 애플리케이션은 그레이들 빌드 시스템으로 빌드한다.  
그레이들은 쉽게 사용자 정의를 할 수 있는 최신 API를 제공하며 자바 진영에서 널리 사용한다.

## 구조
> 안드로이드: 멀티 프로젝트 구조  
> Project Stucture창으로 속성 값들을 시각적으로 표현 가능
- setting.gradle: 하위 프로젝트(이하 모듈)을 포함하는 파일
- 프로젝트 build.gradle: 
  - buildscript: 안드로이드 플러그인을 버전을 몇으로 할지 어디서 다운받을지 지정
    - repositories: 기본값(jcenter), 다른 저장소도 가능(주로 mavenCentral)
    - dependencies
  - allprojects: 최상위 프로젝트와 하위 모듈이 공통적으로 사용할 내용을 지정
    - repositories: 기본값(jcenter)
  - 사용자 정의 테스크: 기본(clean: 루프 프로젝트와 하위 모듈의 build 디렉터리를 제거, type: Delete부분은 내장 테스크인 Delete를 상속받겠다는 의미)
- 모듈 build.gradle: 
  - apply: 안드로이드 플러그인을 지정
  - android: 프로젝트 세부 내용 정의
    - compileSdkVersion: 컴파일 SDK 버전
    - BuildToolVersion: 빌드 툴 버전
    - defaultConfig: 아래 속성들 지정
      - appllicationId: 애플리케이션 패키지 이름 정의, 우선순위가 높으며 구글플레이 스토어에서 유일한 이름
      - minSdkVersion: 지원하는 최소 SDK버전, 이 값보다 낮은 기기는 해당 애플리케이션을 검색, 설치조차 할 수 없다.
      - targetSdkVersion: 애플리케이션이 의도하는 SDK버전
      - versionCode: 애플리케이션의 버전을 나타내는 정수값
      - versionName: 사용자에게 배포되는 애플리케이션 버전을 나타내는 문자열
  - dependencies:
    - compile fileTree(dir: 'libs', include: ['*.jar']): libs 디렉터리 안에 있는 모든 jar 파일들을 참조
    - testCompile 'junit:junit4.12': junit:junit4.12을 다운받고 안드로이드 API에 연관되지 않는 로컬 유닛 테스트를 위한 /src/androidTest/java 디렉터리에 참조

## 그레이들 빌드
- gradlew: gradlewapper, 그레이들을 실행할 때 사용자가 사전에 그레이들을 설치할 필요가 없게 하는 것이 목적
> gradlew.bat파일 실행, 또는 터미널 창으로도 가능
  - gradle-wapper.jar
  - gradle-wapper.properties

## 그래이들 뷰
- 구성
  - android
  - build
  - install
  - other
- run configurations: 자주 실행하는 task 모음

## 외부 라이브러리 추가
- configuartion: 외부 라이브러리 명시
  - compile
  - runtime
  - testCompile
  - testRuntime
- files, fileTree: 로컬 파일 시스템에 있는 파일 참조
- androidDependencies task: 참조하는 라이브러리와 전이적 의존성을 보여줌
- dependencies블록의 transtive: true, false로 의존하는 라이브러리까지 받을 것인지 지정한 라이브러리만 받을 것인지 결정
- Project Stucture - Dependencies 탭: 외부라이브러리 관리 창

## 저장소
- jcenter: HTTPS로 연결해서 빠른 속도 유지
- maven: 
  - mavenCentral: 공개 메이븐 중앙 저장소
  - mavenLocal: 로컬 메이븐 캐시
  - 기타: URL을 직접 지정
    - 비밀번호가 걸린 저장소: credentials로 하드코딩(하드코딩한 내용들은 보통 gradle.properties에 저장한다.)
- ivy: 로컬 저장소 가져오기, URL로 직접 지정
- flatFir: 로컬 파일 시스템에 있는 라이브러리 파일을 참조

## gradle.properties
```
// gradle.properties
login='user'
pass='my_long_and_highly_complex_password'

// 모듈 build.gradle
repositories {
  maven {
    url 'http://repo.mycompony.com/maven2'
    credentials {
      username login
      password pass
    }
  }
}
```

## gradle 공통
- allprojects
- subprojects: 하위프로젝트 적용

## APK 서명
> 기본 인증서 나열  
.android> keytool -list -keystore debug.keystore

- 기본 값은 디버그 모드로 서명
- 확인: keytool 명령어
- 디버그 키 저장소: .android 딝터리
  - 키 저장소 이름: debug.keystore
    - 비밀번호: andorid
- signingConfigs
  - keyAlias: 서명할 때 keytool에서 사용하는 키의 별칭
  - keyPassword: 서명할 때 사용한 키의 비밀번호
  - storeFile: keytool에 의해 생성된 키와 인증서를 저장하는 물리적인 파일
  - storePassword: 키 저장소의 비밀번호
  
  ## 빌드 타입
> 애플리케이션을 어떻게 패키징할 것인가를 결정

```
andorid {
  buildTypes {
    debug {
      applicationIDsuffix '.debug'  // 한 기기에 여러 빌드 타입을 설치하려면 applicationIDsuffix을 변경
      versionNameSuffix '-debug'
      debugable true          // debug만 기본값으로 true
    }

    release {
      minifyEnabled true      // 코드 줄이기
      shrinkResources true    // 리소스 줄이기
      debugable false
    }
  }
}
```

## 제품 특성
> 동일한 애플리케이션의 다양한 버전을 의미(예: 유료 버전, 무료 버전)
  지정하기 위해선 productFlavors 지정
```
andorid {
  productFlavors {
    아무이름a {
      applicationId 'com.compony.product.a'
    }
    아무이름b {
      applicationId 'com.compony.product.b'
    }
  }
}
```
