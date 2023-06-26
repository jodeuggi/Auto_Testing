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
	def __init__(self, driver):
	        self.driver = driver
		# {element 이름} = {element_id} or {element_xpath} 
		⬇ ex) element_id 	
		sub_title = (Mobile.ID, 'com.airklass.airklass:id/tv_sign_up_agreement_subtitle')
	  	header_title = (MobileBy.ID, 'com.airklass.airklass:id/tv_sign_up_agreement_title')
		⬇ ex) element_xpath
		announcement_item = (MobileBy.XPATH, '/hierarchy/android.widget.FrameLayout/android.widget.LinearLayout/android.widget.FrameLayout/android.widget.LinearLayout/android.widget.FrameLayout/android.view.ViewGroup/android.widget.ScrollView/android.view.ViewGroup/android.widget.ScrollView/android.widget.LinearLayout/android.widget.FrameLayout/android.view.ViewGroup/android.widget.FrameLayout/android.view.ViewGroup/androidx.recyclerview.widget.RecyclerView[1]/android.widget.FrameLayout')
	```

	def SignupStep1Wait(self):
        """
        회원가입 페이지 로드가 완료 될때까지 대기합니다.
        """
        	return WebDriverWait(self.driver, 10).until(EC.presence_of_element_located(self.sub_title))

 	def AgreementCkbClick(self, cnt):
        """
        회원가입 1단계 페이지의 checkbox(약관 동의)를 cnt를 지정하여 클릭하는 메소드입니다.\n
        cnt 값은 필수로 입력하여야 하며, 아래와같이 cnt를 선언하여 클릭 후, 해당 페이지의 모든 checkbox(총5개)의 check 여부 상태값을 리턴합니다.\n
        cnt=0 : 네 모두 동의합니다. 클릭 / cnt=1 : (필수) 만 14세 이상입니다. 클릭 / cnt=2 : (필수) 서비스 이용약관 동의 클릭 / cnt=3 : (필수) 개인정보처리방침 동의 클릭 / cnt=4 : (선택) 마케팅 정보 수신 동의 클릭
        """
	        try:
	            sleep(0.5)
	            all_agreement_ckb = self.driver.find_elements(*self.agreement_ckb)
	            sleep(0.5)
	            all_agreement_ckb[cnt].click()
	
	            under_agreement_ckb_statsu = []
	            under_agreement_ckb = self.driver.find_elements(*self.agreement_ckb)
	            for i in range(len(under_agreement_ckb)):
	                under_agreement_ckb_statsu.append(under_agreement_ckb[i].is_selected())
	            
	            return under_agreement_ckb_statsu
	        except:
	            print('네, 모두 동의합니다 체크 박스 클릭 시 하위 내용을 체크 유무 확인에 실패하였습니다.')
	            return False
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

TIME = datetime.datetime.today().strftime("%Y%m%d%H%M%S")

class SignupFullTest(WebDriverSetup):
    PIN_CODE = None
    EMAIL = 'quriouslyqa+{}@gmail.com'.format(TIME)
    PW = '11111aaaaa'
    NAME = "PC웹-QA-자동화"


    def test_case_001(self):
        print('회원가입 - 1단계(약간동의) - 약관 동의 페이지 구성')
        expected_result = [
            'Sub Title = 이메일로 회원가입 : True',
            'Header Title = 안녕하세요!\n약관에 동의해 주세요 : True',
            '[ ] 네, 모두 동의합니다. : True',
            '[ ](필수) 만 14세 이상입니다. : True',
            '[ ](필수) 서비스 이용약관 동의 보기(링크 텍스트) : True',
            '[ ](필수) 개인정보처리방침 동의 보기(링크 텍스트) : True',
            '[ ](선택) 마케팅 정보 수신 동의 : True',
            '<이전(활성화 상태) 버튼 / 다음으로>(default = 비활성화) 버튼 : True'
        ]
        check_list = ['이메일로 회원가입', ['안녕하세요! ', '약관에 동의해 주세요'], '네, 모두 동의합니다.', '(필수) 만 14세 이상입니다.', '(필수) 서비스 이용약관 동의', '(필수) 개인정보처리방침 동의', '(선택) 마케팅 정보 수신 동의', ['이전으로', True, '다음으로', False]]
        unit_result = []
        try:
						⬇ 준비(Arrange)
            #회원가입 1단계 페이지로 이동합니다.
            CommonFunction(self.driver).move_to_signup_page()
            singup_page = SignupPage(self.driver)
            #회원가입 1단계 페이지 로드가 완료될때까지 대기합니다.
            singup_page.SignupStep1Wait()
            #회원가입 1단계 페이지의 구성을 텍스트 형태로 가져옵니다.
						⬇ 실행(Act)
            check_result = singup_page.GetSignupStep1From()
            for i, get_result in enumerate(check_result):
                if check_list[i] == get_result:
                    unit_result.append(True)
                else:
                    unit_result.append(False)
            print('기대하는 확인 값 : {} \n 가져온 확인 값 : {}'.format(check_list, check_result)) 
        except:
            TestOutput(self.driver).resoult_output(self.__class__.__name__, expected_result, None)
            self.assertTrue(False)
        try:
						⬇ 검증(Assert)
            result = [
                'Sub Title = 이메일로 회원가입 : {}'.format(unit_result[0]),
                'Header Title = 안녕하세요!\n약관에 동의해 주세요 : {}'.format(unit_result[1]),
                '[ ] 네, 모두 동의합니다. : {}'.format(unit_result[2]),
                '[ ](필수) 만 14세 이상입니다. : {}'.format(unit_result[3]),
                '[ ](필수) 서비스 이용약관 동의 보기(링크 텍스트) : {}'.format(unit_result[4]),
                '[ ](필수) 개인정보처리방침 동의 보기(링크 텍스트) : {}'.format(unit_result[5]),
                '[ ](선택) 마케팅 정보 수신 동의 : {}'.format(unit_result[6]),
                '<이전(활성화 상태) 버튼 / 다음으로>(default = 비활성화) 버튼 : {}'.format(unit_result[7])
            ]
            self.assertEqual(expected_result, result)
            TestOutput(self.driver).resoult_output(self.__class__.__name__, expected_result, result)
        except:
            TestOutput(self.driver).resoult_output(self.__class__.__name__, expected_result, result)
            self.assertEqual(expected_result, result)

    def test_case_002(self):
        print('회원가입 - 1단계(약간동의) - <이전 버튼 클릭 시')
        expected_result = [
            '버튼 선택 시 "이메일 로그인" 페이지로 이동되어야 합니다. : True' 
        ]
        check_list = '이메일 계정으로 계속하기'
        try:
						⬇ 준비(Arrange)
            signup_page = SignupPage(self.driver)
            #이전으로> 버튼을 클릭합니다.
            sleep(1)
            signup_page.PreviousBtnClick()
            sleep(1)
            login_page = LoginPage(self.driver)
            #로그인 페이지 로드가 완료될때까지 대기합니다.
            login_page.LoginPageWait()
            #로그인 페이지 구성의 sub_title을 가져옵니다.
						⬇ 실행(Act)
            check_result = login_page.GetLoginForm()[0]
            if check_list == check_result:
                unit_result = True
            else:
                unit_result = False
            print('기대하는 확인 값 : {} \n 가져온 확인 값 : {}'.format(check_list, check_result)) 
        except:
            TestOutput(self.driver).resoult_output(self.__class__.__name__, expected_result, None)
            self.assertTrue(False)
        try:
						⬇ 검증(Assert)
            result = [
                '버튼 선택 시 "이메일 로그인" 페이지로 이동되어야 합니다. : {}'.format(unit_result) 
            ]
            self.assertEqual(expected_result, result)
            TestOutput(self.driver).resoult_output(self.__class__.__name__, expected_result, result)
        except:
            TestOutput(self.driver).resoult_output(self.__class__.__name__, expected_result, result)
            self.assertEqual(expected_result, result)

	def test_case_....
		......


if __name__ == '__main__':
     unittest.main(testRunner=HTMLTestRunner(
        combine_reports=True, 
        report_title="회원가입 페이지 전체 Test", 
        report_name= SignupFullTest.__name__ + datetime.datetime.today().strftime("%Y%m%d %H:%M:%S"), 
        add_timestamp=False,
        template='./HtmlTestRunner/template/report_template.html'
    ))
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
