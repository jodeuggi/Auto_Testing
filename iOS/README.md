
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
        cls.driver.implicitly_wait(2) 
        cls.home_page = HomePage(cls.driver)
        cls.settings_page = SettingsPage(cls.driver)
        cls.login_page = LoginPage(cls.driver)
        cls.signup_page = SignupPage(cls.driver)
        cls.klass_page = KlassPage(cls.driver)
        cls.signup_complete_page = SignupCompletePage(cls.driver)
        #캡처 이미지 & 동영상 저장할 폴더를 생성합니다.
        TestOutput(cls.driver).created_folder() 
        #앱 실행 시 선택 엡데이트 팝업창 출력 유무를 확인합니다. 
        try:
            #에어클래스 최초 실행 시 알림 허용 권한 얼럿창 출력 확인
            notification_alret = cls.driver.find_element(MobileBy.NAME, '‘에어클래스’에서 알림을 보내고자 합니다.')
            if notification_alret.is_displayed():
                #알림 허용 권한창의 "허용" 버튼을 클릭합니다.
                allowed_btn = cls.driver.find_element(MobileBy.NAME, '허용')
                allowed_btn.click()
        except:
            pass
        
        try:
            #선택 업데이트 얼럿창 출력 확인
            update_alret = cls.driver.find_element(MobileBy.NAME, '앱 버전 안내')
            if update_alret.is_displayed():
                #선택 업데이트 얼럽창의 "나중에" 버튼을 클릭합니다.
                later_btn = cls.driver.find_element(MobileBy.NAME, '나중에')
                later_btn.click()
        except:
            pass
    
    def setUp(self):
        #테스트 케이스가 시작하면 레코딩을 시작합니다.
        self.driver.start_recording_screen(
            videoType='mpeg4',
            videoQuality='medium',
            videoFps=24,
            timeLimit=180
        )
        pass
    def tearDown(self):
        #테스트 케이스가 종료하면 레코딩을 멈춥니다.
        stop = self.driver.stop_recording_screen()

    
    @classmethod
    def tearDownClass(cls):
        if cls.driver != None:
            cls.driver.quit()
```

### Locators_{page} 구조

```python
import sys, os, datetime

sys.path.append(os.getcwd())
from selenium.webdriver.support.ui import WebDriverWait
from appium.webdriver.common.touch_action import TouchAction
from selenium.webdriver.support import expected_conditions as EC
from appium.webdriver.common.mobileby import MobileBy
from Src.PageObject.Signup_Locators import Signup_Locator

class {page ex : SignupPage}(object):
	def __init__(self, driver):
        self.driver = driver
        #----------------------------------------------#
        ###회원가입 페이지
        self.header_title = (MobileBy.IOS_CLASS_CHAIN, '**/XCUIElementTypeStaticText[`label == "회원가입"`]')
        self.email = (MobileBy.NAME, '아이디(이메일)')
        self.email_input = (MobileBy.XPATH, '//XCUIElementTypeApplication[@name="에어클래스"]/XCUIElementTypeWindow[1]/XCUIElementTypeOther[3]/XCUIElementTypeOther/XCUIElementTypeOther/XCUIElementTypeOther/XCUIElementTypeOther/XCUIElementTypeTextField[1]')
        self.pw = (MobileBy.NAME, '비밀번호')
        self.pw_input = (MobileBy.XPATH, '//XCUIElementTypeApplication[@name="에어클래스"]/XCUIElementTypeWindow[1]/XCUIElementTypeOther[3]/XCUIElementTypeOther/XCUIElementTypeOther/XCUIElementTypeOther/XCUIElementTypeOther/XCUIElementTypeSecureTextField')
        self.pw_view_btn = (MobileBy.IOS_CLASS_CHAIN, '**/XCUIElementTypeWindow[1]/XCUIElementTypeOther[3]/XCUIElementTypeOther/XCUIElementTypeOther/XCUIElementTypeOther/XCUIElementTypeOther/XCUIElementTypeButton[1]')
        self.all_agree_btn = (MobileBy.IOS_CLASS_CHAIN, '**/XCUIElementTypeWindow[1]/XCUIElementTypeOther[3]/XCUIElementTypeOther/XCUIElementTypeOther/XCUIElementTypeOther/XCUIElementTypeOther/XCUIElementTypeButton[2]')
        self.terms_msg = (MobileBy.NAME, '에어클래스의 이용약관과 개인정보처리방침에 동의합니다.*')
        self.terms = (MobileBy.NAME, '이용약관')
        self.policy = (MobileBy.NAME, '개인정보처리방침')
        self.next_btn = (MobileBy.NAME, '다음')
        self.facebook_btn = (MobileBy.IOS_CLASS_CHAIN, '**/XCUIElementTypeWindow[1]/XCUIElementTypeOther[3]/XCUIElementTypeOther/XCUIElementTypeOther/XCUIElementTypeOther/XCUIElementTypeOther/XCUIElementTypeOther[5]/XCUIElementTypeOther[1]/XCUIElementTypeButton')
        self.apple_btn = (MobileBy.IOS_CLASS_CHAIN, '**/XCUIElementTypeButton[`label == "whiteLogoSquare"`]')
        self.close_btn = (MobileBy.XPATH, '//XCUIElementTypeNavigationBar[@name="회원가입"]/XCUIElementTypeButton[2]')
        self.error_msg = (MobileBy.IOS_CLASS_CHAIN, '**/XCUIElementTypeWindow[1]/XCUIElementTypeOther[3]/XCUIElementTypeOther/XCUIElementTypeOther/XCUIElementTypeOther/XCUIElementTypeOther/XCUIElementTypeStaticText[2]')
        #------------------------------------------------#
	
	def SignupPageWait(self):
        return WebDriverWait(self.driver, 10).until(EC.presence_of_element_located(self.header_title))

	def HeaderTitle(self):
        return self.driver.find_element(*self.header_title)

 	......

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
from Src.Function.Common.Common_Fucntion import CommonFunction
import unittest

SIGNUP_COMPLETE_IMAGE = './image/cover_img/signup_complete_img.png'

class SingupFullTest(AppiumSetup):

    USER_ID, USER_NAME, PIN_CODE = None, None, None
    test_case_result = True

    def test_case_001(self):
        print('이메일로 회원가입 - 회원가입 페이지(1단계) - 구성')
        expected_result = [
            '해더 타이틀 = 회원가입 : True',
            'X(닫기 버튼) 출력 : True',
            '아이디(이메일) : True',
            '이메일 입력창(Default = 이메일 주소를 입력해 주세요.) : True',
            '비밀번호 : True',
            '비밀번호 입력창(Default = 비밀번호를 입력해 주세요.) : True',
            'ⓥ 에어클래스의 이용약관과 개인정보처리방침에 동의합니다. : True',
            '다음 버튼 : 비활성화(default) : True'
        ]
        check_list = [
            '회원가입',
            True,
            '아이디(이메일)',
            '이메일 주소를 입력해 주세요.',
            '비밀번호',
            '비밀번호를 입력해 주세요.',
            '에어클래스의 이용약관과 개인정보처리방침에 동의합니다.*',
        ]
        unit_result, temparay_result = [], []
        try:
		⬇ 준비(Arrange)
            try:
                #회원가입 페이지로 이동합니다.
                CommonFunction(self.driver).move_to_signup_page()
            except:
                SingupFullTest.test_case_result = False
	
            header_title = self.signup_page.HeaderTitle().get_attribute('name')
            close_btn = self.signup_page.CloseBtn().is_displayed()
            email = self.signup_page.EmailText()
            email_input = self.signup_page.EmailInput().text
            pw = self.signup_page.PwText()
            pw_input = self.signup_page.PwInput().text
            all_agree_msg = self.signup_page.AllAgreeMsgText()
            next_btn = self.signup_page.NextBtn()
		⬇ 실행(Act)
            check_result = [header_title, close_btn, email, email_input, pw, pw_input, all_agree_msg, next_btn]

            for i, element in enumerate(check_result):
                temparay_result.append(element)
                if i == 7:
                    btn_text = element.text
                    btn_state = element.get_attribute('enabled')

                    if ['다음', 'false'] == [btn_text, btn_state]:
                        unit_result.append(True)
                    else:
                        unit_result.append(False)
                else:
                    if check_list[i] == element:
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
                '해더 타이틀 = 회원가입 : {}'.format(unit_result[0]),
                'X(닫기 버튼) 출력 : {}'.format(unit_result[1]),
                '아이디(이메일) : {}'.format(unit_result[2]),
                '이메일 입력창(Default = 이메일 주소를 입력해 주세요.) : {}'.format(unit_result[3]),
                '비밀번호 : {}'.format(unit_result[4]),
                '비밀번호 입력창(Default = 비밀번호를 입력해 주세요.) : {}'.format(unit_result[5]),
                'ⓥ 에어클래스의 이용약관과 개인정보처리방침에 동의합니다. : {}'.format(unit_result[6]),
                '다음 버튼 : 비활성화(default) : {}'.format(unit_result[7])
            ]
            self.assertEqual(expected_result, result)
            TestOutput(self.driver).resoult_output(self.__class__.__name__, expected_result, result)
        except:
            TestOutput(self.driver).resoult_output(self.__class__.__name__, expected_result, result)
            self.assertEqual(expected_result, result)

    
    def test_case_002(self):
        print('이메일로 회원가입 - 회원가입 페이지(1단계) - 이메일 - 잘못된 형식(이메일을 입력하지 않은경우) - 에러메시지')
        if SingupFullTest.test_case_result == False:
            self.skipTest("이전 테스트 케이스 실패로 인하여 제외합니다.")
        expected_result = [
            '이메일 주소를 입력해 주세요. 에러 메시지 발생 : True'
        ]
        error_msg = '이메일 주소를 입력해 주세요.'

        try:
		⬇ 준비(Arrange)
            self.signup_page.EmailInput().send_keys('1111')
            self.signup_page.EmailInput().clear()
            sleep(2)
	    	⬇ 실행(Act)
            try:
                get_error_msg = self.signup_page.EmailErrorText()
                if error_msg == get_error_msg:
                    unit_result = True
                else:
                    unit_result = '{} / 에러 메시지가 상이합니다. : {}'.format(False, get_error_msg)
            except:
                unit_result = '{} / 에러 메시지가 출력되지 않았습니다.'.format(False)
        except:
            TestOutput(self.driver).resoult_output(self.__class__.__name__, expected_result, None)
            self.assertTrue(False)
        try:
		⬇ 검증(Assert)
            result = [
                '이메일 주소를 입력해 주세요. 에러 메시지 발생 : {}'.format(unit_result)
            ]
            self.assertEqual(expected_result, result)
            TestOutput(self.driver).resoult_output(self.__class__.__name__, expected_result, result)
        except:
            TestOutput(self.driver).resoult_output(self.__class__.__name__, expected_result, result)
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
