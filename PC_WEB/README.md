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
