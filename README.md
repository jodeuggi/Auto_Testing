# test-automation-aos
에어클래스 안드로이드 테스트 자동화
## 사용한 기술

### 프레임워크

- **Appium**
    - Appium은 크로스 플랫폼(iOS, Android, Windows)을 지원하는 모바일 애플리케이션 및 모바일 웹 앱의 테스트 자동화를 위한 오픈소스 프레임워크입니다. Appium은 Selenium WebDriver 프로토콜을 기반으로 구현되어 있어, 테스트 자동화를 위한 API를 제공하며, 자바, 파이썬, 루비, 자바스크립트, C# 등 다양한 프로그래밍 언어를 지원

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
test-automation-aos(Project-Directory)  
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

---

## 코드 구조

### WebDriverSetup 구조

```python
import sys, os
sys.path.append(os.getcwd())
from Src.PageObject.Function.Test_output import TestOutput
from Src.TestBase import Desired_caps_list
from appium.webdriver.common.mobileby import MobileBy
from appium import webdriver
from selenium.webdriver.common.by import By
from time import sleep
import unittest
import time, base64, sys, datetime
import subprocess

class AppiumSetup(unittest.TestCase):
        
    @classmethod
    def setUpClass(cls): 
        cls.driver = webdriver.Remote('http://127.0.0.1:{}/wd/hub'.format(AppiumSetup.selected_port), selected_desired)
        cls.driver.implicitly_wait(3.5)
      
        #앱 실행 시 선택 엡데이트 팝업창 출력 유무를 확인합니다. 
        try:
            update_alert = cls.driver.find_element(By.ID, 'com.airklass.airklass:id/alertTitle').is_displayed()
            #선택 업데이트 팝업창이 출력 시 "나중에" 버튼을 클릭합니다.
            if update_alert:
                cls.driver.find_element(By.ID, 'android:id/button2').click()
        except:
            pass
        #입 실행 시 프로모션 카드 팝업창 출력 유무를 확인합니다.
        try:
            promotion_card = cls.driver.find_element(By.ID, 'com.airklass.airklass:id/card_root')
            #프로모션 팝업창이 출력 시 "닫기" 버튼을 클릭합니다.
            if promotion_card.is_displayed():
                cls.driver.find_element(By.ID, 'com.airklass.airklass:id/secondary_button').click()
        except:
            pass

    def setUp(self):
        #테스트 케이스가 실행될때 레코딩을 시작합니다
        self.driver.start_recording_screen()
        current_package = self.driver.current_package
        if 'com.airklass.airklass' != current_package:
            self.skipTest('앱에서 크래시가 발생되었습니다.')
        
    def tearDown(self):
        #pass
        #테스트 케이스가 종료하면 레코딩을 멈춥니다.
        stop = self.driver.stop_recording_screen()

    @classmethod
    def tearDownClass(cls):
        if cls.driver != None:
            cls.driver.quit()
```

### Locators_{page} 구조

```python
class {page ex : SignupPage}(object):
	
	# {element 이름} = {element_id} or {element_xpath} 
	⬇ ex) element_id 	
	sub_title = 'com.airklass.airklass:id/tv_sign_up_agreement_subtitle'
  	header_title = 'com.airklass.airklass:id/tv_sign_up_agreement_title'
	⬇ ex) element_xpath
	announcement_item = '/hierarchy/android.widget.FrameLayout/android.widget.LinearLayout/android.widget.FrameLayout/android.widget.LinearLayout/android.widget.FrameLayout/android.view.ViewGroup/android.widget.ScrollView/android.view.ViewGroup/android.widget.ScrollView/android.widget.LinearLayout/android.widget.FrameLayout/android.view.ViewGroup/android.widget.FrameLayout/android.view.ViewGroup/androidx.recyclerview.widget.RecyclerView[1]/android.widget.FrameLayout'
```

### Scripts 구조(ex : 회원가입 페이지)

- **AAA 구조 기준으로 작성**

```python
from appium.webdriver.common.appiumby import AppiumBy
from HtmlTestRunner import HTMLTestRunner
from time import sleep
_________________________________________________________________________________
⬇ 앱 자동화 코드 실행 및 TC 수행에 필요한 모듈 선언
from Src.TestBase.WebDriverSetup import AppiumSetup
from Src.PageObject.Function.Base_function import BaseFunction
from Src.PageObject.Function.Utility.Utility import Utility
from Src.PageObject.Function.Test_output import TestOutput
_________________________________________________________________________________
⬇ TC 수행에 필요한 페이지의 클래스 선언
from Src.PageObject.Pages.Locators_home import Home
from Src.PageObject.Pages.Locators_Welcome import WelecomPage
from Src.PageObject.Pages.Locators_Signup import SignupPage
from Src.PageObject.Pages.Locators_Settings import SettingsPage
from Src.PageObject.Pages.Locators_Login import Login
import unittest

class SignupFullTest(AppiumSetup):
	test_reault = True
	pin_code = None

	def test_case_001(self):
		print('이메일로 회원가입 - 1단계 페이지 구성')
    		expected_result = [
			'해더 타이틀 출력 : True',
			'서브 타이틀 출력 : True',
			'약관 동의 체크박스 출력 : True',
			'다음 버튼 출력 : True'
		]
		check_list = ['안녕하세요!\n약관에 동의해 주세요', '이메일로 회원가입', '네, 모두 동의합니다.', '다음']
		check_result = []
    		unit_result = []
	
		try:
			⬇ 준비(Arrange)
			#우측 더보기 버튼이 출력될때까지 wait 후 우측 더 보기 버튼을 클릭 합니다.
      			gnb_right_menu = self.driver.find_element(Appium.XPATH, Home.gnb_right_menu)
			gnb_right_menu.click()
			#좌측 메뉴 Bar의 로그인/회원가입 버튼을 클릭 합니다.
      			right_menu_welcome = self.driver.find_element(AppiumBy.XPATH, Home.right_menu_welcome).click()
			right_menu_welcome,click()
			#웰컴 페이지에서 진입할때까지 대기
      			BaseFunction(self.driver).waittings(AppiumBy.ID, WelecomPage.element)
			#웰컴 페이지에서 "이메일 계정으로 시작하기" 버튼 클릭
      			email_login_btn = self.driver.find_element(AppiumBy.ID, WelecomPage.email_login).click()
      			email_login_btn.click()
			#로그인페이지 진입이 완료 될때까지 대기
			BaseFunction(self.driver).waittings(AppiumBy.ID, Login.header_title)
			#키패드를 숨김처리
			self.driver.hide_keyboard()
			#로그인 페이지 하단에 "이메일로 회원가입" 버튼 클릭
			signup_btn = self.driver.find_element(AppiumBy.ID, Login.signup_btn)
			signup_btn.click()
			
			⬇ 실행(Act)
			#회원가입 페이지 1단계 진입이 완료 될때까지 대기
			BaseFunction(self.driver).waittings(AppiumBy.ID, SignupPage.header_title)
			#회원가입 1단계 페이지의 구성 요소의 텍스트를 모두 가져옵니다.
			for i, element in enumerate([
				SignupPage.header_title,
				SignupPage.sub_title,
				SignupPage.all_agree_ckb,
				SignupPage.next_btn
			]):	
				get_text = self.driver.find_element(Appium.ID, element).text
					check_result.append(get_text)
				if check_list[i] == get_text:
					unit_result.append(True)
				else:
					unit_result.append(False)

			print('예상값 : {} \n가져온 값 : {}'.format(check_list, check_result))
		except:
			TestOutput(self.driver).resoult_output(self.__class__.__name__,'==', expected_result, None)
			SignupFullTest.test_result = False
      			self.assertTrue(False)
		try:
			⬇ 검증(Assert)
			result = [
				'해더 타이틀 출력 : {}'.format(unit_result[0]),
				'서브 타이틀 출력 : {}'.format(unit_result[1]),
				'약관 동의 체크박스 출력 : {}'.format(unit_result[2]),
				'다음 버튼 출력 : {}'.format(unit_result[3])
			]
      			self.assertEqual(expected_result, result)
      			TestOutput(self.driver).resoult_output(self.__class__.__name__,'==', expected_result, result)  
     		except:
       			TestOutput(self.driver).resoult_output(self.__class__.__name__,'==', expected_result, result)
       			self.assertEqual(expected_result, result)

	def test_case_002(self):
		print('이메일로 회원가입 - 약관 - 모두 동의하지 않는 경우)
		if SignupFullTest.test_result == False:
			unittest.skip("이전 테스트 케이스 실패로 인하여 제외합니다.")
		expected_result = ['다음 버튼이 비활성화 상태르 유지 : True']
		
		try:
			⬇ 실행(Act)
			next_btn_state = self.driver.find_element(Appium.ID, SignupPage.next_btn).get_attritube('enabled')
			if 'false' == next_btn_state:
				unit_result = True
			else:
				unit_result = False		
		except:
			TestOutput(self.driver).resoult_output(self.__class__.__name__,'==', expected_result, None)
      			self.assertTrue(False)
		try:
			⬇ 검증(Assert)
			result = ['다음 버튼이 비활성화 상태르 유지 : {}'.format(unit_result)]
		      	self.assertEqual(expected_result, result)
		      	TestOutput(self.driver).resoult_output(self.__class__.__name__,'==', expected_result, result)  
	     	except:
		       TestOutput(self.driver).resoult_output(self.__class__.__name__,'==', expected_result, result)
		       self.assertEqual(expected_result, result)

	def test_case_....
		......

if __name__ == "__main__":
    unittest.main(testRunner=HTMLTestRunner(
        combine_reports=True,
        report_title="이메일 회원가입 테스트 자동화 결과",
        report_name= SignupFullTest.device_name + "_" + "이메일 회원가입 테스트 자동화 결과" + datetime.datetime.today().strftime("%Y%m%d %H:%M:%S"), 
        add_timestamp=False,
        template='./HtmlTestRunner/template/report_template.html'
    )
```

### Scenario 구조(ex : 회원가입 페이지)

- 회원가입 전체 수행되는 TC는 총 32개입니다.
- 32개 TC 중에 회원가입 시나리오 테스트에 필요한 필수 TC만 조합니다.

```python
from unittest import TestLoader, TestSuite, TextTestRunner, makeSuite
from appium.webdriver.common.appiumby import AppiumBy
from HtmlTestRunner import HTMLTestRunner
from Test.Scripts.Signup.email_signup_full_test import SignupFullTest

import unittest

def signup_basic_suite():
    suite = TestSuite()
    case_list = [
        'test_case_001',
        'test_case_006',
        'test_case_007',
        'test_case_012',
        'test_case_013',
        'test_case_014',
        'test_case_017',
        'test_case_018',
        'test_case_023',
        'test_case_027',
        'test_case_031',
        'test_case_032'
    ]
    for test_case in case_list:
        suite.addTest(SignupFullTest(test_case))

    return suite

if __name__ == '__main__':
    #unittest.main(verbosity=2)
    test_runner = HTMLTestRunner(
        combine_reports=True, 
        report_title="이메일 회원가입 시나리오 자동화 테스트", 
        report_name= signup_basic_suite.__name__ + datetime.datetime.today().strftime("%Y%m%d %H:%M:%S"), 
        add_timestamp=False,
        template='./HtmlTestRunner/template/report_template.html'
    )

    test_runner.run(signup_basic_suite())
```

### 전체 테스트를 수행하는 코드 구조

- Unit_test_Runner
    
    ```python
    from HtmlTestRunner import HTMLTestRunner
    from unittest import TestLoader, TestSuite, TextTestRunner, makeSuite
    from Test.Scripts.Etc.after_login_left_menu import AfterLogin_LeftMenu
    from Test.Scripts.Etc.network_offline import Offline
    from Test.Scripts.Etc.settings_page import Settings
    from Test.Scripts.Login.email_login_full_test import LoginFullTest
    from Test.Scripts.Login.login_path_check import LoginPath 
    from Test.Scripts.Resetpw.resetpw_path_check import RestPwPath
    from Test.Scripts.Resetpw.restpw_full_test import RestPwFullTest
    .......
    
     
    def suite():
        suite = TestSuite()
        suite.addTest(makeSuite(SignupPath))
        suite.addTest(makeSuite(LoginPath))
        suite.addTest(makeSuite(MainHome))
        suite.addTest(makeSuite(AfterLogin_LeftMenu))
        .......
        suite.addTest(makeSuite(PaymentMethod))
        suite.addTest(makeSuite(PurchaseCompletion))
        suite.addTest(makeSuite(PurchaseFailure))
        return suite
    
    if __name__ == "__main__":
        
        test_runner = HTMLTestRunner(
            combine_reports=True,
            report_title="전체 테스트 자동화 결과",
            report_name= SignupPath.device_name + "_" + "전체 테스트 자동화 결과" + datetime.datetime.today().strftime("%Y%m%d %H:%M:%S"), 
            add_timestamp=False,
            template='./HtmlTestRunner/template/report_template.html'
        )
        test_runner.run(suite())
    ```
    
- Scenario_test_Runner
    
    ```python
    from HtmlTestRunner import HTMLTestRunner
    from unittest import TestLoader, TestSuite, TextTestRunner, makeSuite
    from Test.Scenario import email_signup
    from Test.Scenario import reset_password
    from Test.Scenario import login
    from Test.Scenario import per_klass_page
    from Test.Scenario.purchase import Purchase
     
    def suite():
        suite = TestSuite()
        suite.addTest(email_signup.signup_basic_suite())
        suite.addTest(login.login_basic_suite())
        suite.addTest(reset_password.reset_password_basic_suite())
        suite.addTest(per_klass_page.perklass_basic_suite())
        suite.addTest(makeSuite(Purchase))
        return suite
    
    if __name__ == "__main__":
        
        test_runner = HTMLTestRunner(
            combine_reports=True,
            report_title="필수 테스트 자동화 결과",
            report_name= Purchase.device_name + "_" + "필수 테스트 자동화 결과" + datetime.datetime.today().strftime("%Y%m%d %H:%M:%S"), 
            add_timestamp=False,
            template='./HtmlTestRunner/template/report_template.html'
        )
        test_runner.run(suite())
    ```
    

---

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
