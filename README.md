자동화 테스트 자동화
## 사용한 기술

### 프레임워크

- **Appium**
    - Appium은 크로스 플랫폼(iOS, Android, Windows)을 지원하는 모바일 애플리케이션 및 모바일 웹 앱의 테스트 자동화를 위한 오픈소스 프레임워크입니다. Appium은 Selenium WebDriver 프로토콜을 기반으로 구현되어 있어, 테스트 자동화를 위한 API를 제공하며, 자바, 파이썬, 루비, 자바스크립트, C# 등 다양한 프로그래밍 언어를 지원

- **Seleium**
    - Selenium WebDriver 프로토콜을 기반으로 구현되어 있어, 테스트 자동화를 위한 API를 제공하며, 자바, 파이썬, 루비, 자바스크립트, C# 등 다양한 프로그래밍 언어를 지원
      
### 라이브러리

- **unittest**
    - unittest는 Python의 표준 라이브러리 중 하나로, 단위 테스트를 작성하고 실행할 수 있는 기능을 제공

### 언어

- **Python**

---

## Model

### POM(Page Object Model)

Page Object Model 은 드라이버, 테스트 로케이터, 테스트 케이스, 스위트 등을 별도의 파일로 모듈화 하여 관리하는 디자인 패턴으로 Page Object Model 에는 다음과 같은 구성 요소를 포함합니다.

- **Page Object Element** : 페이지 클래스는 페이지의 웹 UI 요소에 대한 객체 저장소
- **Test Cases** : Page Object Element 의 요소를 사용하여, 실제 테스트를 수행 페이지의 UI 가 변경된 경우, Page Object Element 만 변경

### POM을 사용한 이유

자동화를 진행 시에도 타겟(element)에 대한 정보는 계속 변경될 수 있으며, 유지보수에 어려움이 있습니다. 그렇기에 **한정된 리소스를 효율적으로 관리하고 커버리지를 보장하기 위해서는 Page Object Model (POM) 같은 오브젝트에 대한 디자인 패턴 적용으로 모듈화를 하여 효율성을 확보** 를 위해 사용하였습니다.

Page Object Model (POM) 로 구성하게 된다면 다음과 같은 장점이 있다.

- **테스트 자동화 코드 유지관리성 작업이 용이**
    - Page Object Element 에서 변경된 ID or Xpath 를 적용 시 모든 스크립터의 사용되는 엘리먼트 적용
- **테스트 자동화 코드에 대한 가독성 증가**
    - App의 element 값(id, xpath)만 본다면, 해당 값이 어떤 의미인지 가독성이 매우 떨어집니다. 하지만 Page Object Element 를  {페이지 ex)SignupPage}.{페이지의 내부 요소 ex)email_input} 으로  선언을 통해 자동화 코드에 대한 가독성 증가

---

## 프로젝트 폴더 구조

- 아래와 같이 POM 모델로 프로젝트 폴더 구조를 설계하였습니다.

```jsx
Auto_Testing(Project-Directory)  
	| - HtmlTestRunner ⬅ unittest 라이브러리를 사용하여 작성한 테스트 결과를 HTML 형식으로 출력해주는 오픈소스 
	| - Src
		| - PageObject
			| - Function
				| - Utillity
					| - utillty.py ⬅ 테스트 자동화에서 수집된 데이터 저장 및 편집을 위한 클래스
					| - ....
				| - Base_function.py ⬅ 스크립터 마다 반복적으로 수행하는 기능을 메소드 집합 클래스 
				| - Test_output.py ⬅ 테스트 결과 확인 시 이미지 & 동영상 캡처를 수행하는 클래스
				| - .....
			| - Pages ⬅ 페이지 클래스의 UI 요소에 대한 객체 저장 폴더
				| - Locators_Signup.py
				| - Locators_.....py
		| - TestBase ⬅ WebDriver 설정에 필요한 폴더
			| - Desired_caps_list.py
			| - WebDriverSetup.py
	| - Test
		| - Scenario ⬅ 기능에 대한 시나리오 테스트
			| - email_signup.py
			| - .....py
		| - Scripts ⬅ 단위 테스트
			| - Signup
				| - email_signup_full_test.py ⬅ 해당 페이지의 단위 테스트(TC_Case)의 묶음(Suite)
				| - email_signup_path_check.py
			| - ....
		| - TestSuite
			| - Scenario_test_Runner.py ⬅ 시나리오 전체 테스트(작성된 모든 페이지의 Suite를 모두 실행)
			| - Unit_test_Runner.py ⬅ 단위 전체 테스트(작성된 모든 페이지의 Suite를 모두 실행)
	| - image 
		| - cover_image 
			| - 기대하는 이미지
		| - naverpay_pw
			| - 네이버페이 키패드 0~9 이미지
	| - reports ⬅ 테스트 결과 리포트(html)이 저장되는 장소
		| - 테스트 결과 리포트(html 형식)
	| - requirments.text ⬅ 앱 자동화를 실행하기 위한 설치 패키지 리스트
```

### 테스트 리포트 구성

- Test Case 별로 테스트 결과를 모두 확인이 가능하며, **모든 케이스 결과에 캡처 이미지가 포함**되어 있습니다.
    
    ![스크린샷 2023-03-08 오후 7.09.14.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/b90d4230-ed6b-4ddf-a097-8e5b766e626e/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2023-03-08_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_7.09.14.png)
    
- **실패한 케이스 대상으로 해당 케이스 결과에 추가로 동영상 캡처를 포함되어 있습니다.**
    
    ![스크린샷 2023-03-09 오후 2.54.52.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/c6960d76-e5dc-49ba-bc0e-5cac0295bf3d/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2023-03-09_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_2.54.52.png)


### 테스트 결과 종류

- **PASS**
    - TC 검증 성공
- **Fail**
    - TC 검증 실패
        - 이유는 아래와 같습니다.
            - def test_case_xxx(self)의 준비 과정 중 앱이 크래시 될 경우
            - def test_case_xxx(self)의 준비 과정 중 스크립더를 더이상 수행할 수 없는 경우
            - def test_case_xxx(self)의 검증 절차에서 Fail 처리된 경우
- **Skip**
    - TC 제외
        - 이유는 아래와 같습니다.
            - 이전 def test_case_xxx(self)의 준비 과정 중 앱이 크래시되어, 다음  def test_case_xxx(self)을 수행할 경우
            - 이전 def test_case_xxx(self)의 Fail로 인하여 다음 def test_case_xxx(self)을 수행할 수 없는 경우
- **Error**
    - 시스템 에러
        - def test_case_xxx(self)의 파이썬 코드가 오류가 있는 경우
