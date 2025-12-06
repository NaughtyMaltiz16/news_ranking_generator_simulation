# 뉴스랭킹제너레이터

---

## 프로젝트 구조

```
index.html                # 프론트엔드 웹 앱 (Live Server로 실행)
tooplate-eco-lume.css     # 스타일 파일
images/title_image.jpg    # 히어로 섹션 이미지
google_app_script.txt     # Google Apps Script 백엔드 코드
```

---

## 준비물

* Google Spreadsheet
* Google Apps Script
* VS Code (또는 Live Server 가능한 환경)

---

## 실행 핵심 요약 (TL;DR)

* Apps Script 코드 전체 붙여넣기
* 시트 3개 생성 + 헤더 정확히 입력
* Spreadsheet URL / Script URL 교체
* 권한 설정 + 웹앱 배포
* `index.html`을 Live Server로 실행

---

##  실행 절차

### 1) Google Apps Script 설치

`google_app_script.txt` 파일에 있는 **전체 코드를 Apps Script 프로젝트에 복사/붙여넣기** 합니다.

> ⚠️ 기존 코드 일부가 수정되었으므로 반드시 전체 교체해야 합니다.

---

### 2) Google Spreadsheet 준비

스프레드시트에는 아래 **3개의 시트**를 만들고, 첫 Row에 **헤더를 정확히** 작성합니다.

#### ① visitor

| id | landingUrl | ip | referer | time_stamp | utm | device |

#### ② feedback

| id | feedback | time_stamp | page | device |

#### ③ demo_reaction

| id | reaction | time_stamp | page | device |

> ⚠️ object key와 시트 헤더가 매칭되어야 데이터가 저장됩니다.
> **철자와 순서 모두 정확히 동일해야 합니다.**

---

### 3) Spreadsheet URL 연결

Apps Script 코드 상단의 아래 부분을

```js
var SHEET_URL = "https://docs.google.com/spreadsheets/d/xxxx/edit";
```

본인 시트의 URL로 변경합니다.

또한 `index.html`에서도 동일하게 Apps Script URL 및 Spreadsheet URL을 **본인 주소로 교체**해야 합니다.

> ⚠️ 코드에 있는 URL은 제가 개인적으로 프로젝트를 위해 사용한 링크들입니다.

---

## 4) Apps Script 승인 및 권한 설정 (중요)

뉴스 데이터를 수집하기 위해 Apps Script는 **외부 사이트(news.nate.com)** 에 접근해야 합니다.

이를 위해 **OAuth 권한 설정 + 승인**이 필요합니다.

---

### 4-1) appsscript.json 설정

Google Apps Script에서

**왼쪽 설정(⚙) → "매니페스트 파일 표시" 체크**

→ 그러면 `appsscript.json` 파일이 보입니다.

아래 내용으로 **전체 덮어씌웁니다:**

```json
{
  "timeZone": "Asia/Seoul",
  "dependencies": {},
  "exceptionLogging": "STACKDRIVER",
  "oauthScopes": [
    "https://www.googleapis.com/auth/script.external_request",
    "https://www.googleapis.com/auth/spreadsheets",
    "https://www.googleapis.com/auth/script.send_mail"
  ],
  "runtimeVersion": "V8",
  "webapp": {
    "executeAs": "USER_DEPLOYING",
    "access": "ANYONE_ANONYMOUS"
  }
}
```

---

### 4-2) 권한 승인 테스트

`Code.gs`에서 아래 함수를 실행합니다:

```js
function testNateAuthRaw() {
  var url = "https://news.nate.com/rank/pop?sc=eco&p=day&date=20221212";
  var res = UrlFetchApp.fetch(url);
  Logger.log(res.getResponseCode());
}
```

**실행 → 팝업 → 허용**

정상일 경우 **200** 코드가 출력됩니다.

> ⚠️ 이 과정이 선행되지 않으면 외부 요청이 차단되어 뉴스가 로딩되지 않습니다.

---

### 5) Apps Script 웹 앱 배포

1. Apps Script 화면에서 **Deploy → New deployment**
2. Type: **Web app**
3. Execute as → **Me**
4. Who has access → **Anyone**
5. Deploy → URL 복사

> 이 URL이 index.html에서 API 호출에 사용됩니다.

---

### 6) 프론트엔드 실행

1. `index.html`에서 **API 주소 및 시트 주소를 본인 주소로 교체**
2. VS Code에서 **Open Live Server**로 실행

---
