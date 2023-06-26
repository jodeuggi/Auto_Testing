## 코드 구조

### WebDriverSetup 구조

```python
sys.path.append(os.getcwd())

from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.chrome.service import Service
from selenium.webdriver.chrome.options import Options
from Src.TestOutput.Test_output import TestOutput
from time import sleep
import unittest
import warnings
import urllib3

CHROME_DRIVER_PATH = os.getcwd() + "/Src/TestBase/Driver/chromedriver"
DOWNLOAD_PATH = os.getcwd() + "/Download"

class WebDriverSetup(unittest.TestCase):
    
    URL = '테스틑 대상인 도메인 URL 기본 주소'

    @classmethod
    def setUpClass(cls):
        chrome_options = Options()
        chrome_options.add_argument('--headless')
        prefs = {'download.default_directory': DOWNLOAD_PATH}
        chrome_options.add_experimental_option('prefs', prefs)
        chrome_options.add_experimental_option("prefs", {"profile.default_content_setting_values.notifications": 1})
        chrome_options.add_argument("user-agent=Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/112.0.5615.49 Safari/537.36")
        chrome_options.add_argument("--window-size=1920,1080")
        urllib3.disable_warnings(urllib3.exceptions.InsecureRequestWarning)
        cls.driver = webdriver.Chrome(executable_path=CHROME_DRIVER_PATH, options=chrome_options)
        cls.driver.implicitly_wait(2)
        cls.driver.maximize_window()
        cls.driver.get(WebDriverSetup.URL)
        
        #브라우즈 실행 후 테스트 결과 이미지 및 동영상 저장 폴더를 생성합니다.
        TestOutput(cls.driver).created_folder() 

    def setUp(self):
        pass
            
    def tearDown(self):
        pass
    
    @classmethod
    def tearDownClass(cls):
        if (cls.driver != None):
            print("Cleanup of test environment")
            cls.driver.close()
            cls.driver.quit()

if __name__ == '__main__':
    unittest.main()
```

### Locators_{page} 구조 | ex: 회원가입 페이지 Locators

```python
class {page ex : SignupPage}(object):
	
	def __init__(self, driver):
        self.driver = driver
        ##회원가입 엘리먼트
        self.signup_area = (By.CSS_SELECTOR, '#__next > div > div > div.MuiStack-root.css-1k6f0k5 > div > div > div > div')
        self.agreement_ckb = (By.CSS_SELECTOR, '[type="checkbox"]')
        self.under_agreement_ckb = (By.CSS_SELECTOR, '[class="MuiFormControlLabel-root MuiFormControlLabel-labelPlacementEnd css-iwom9w"]')
        self.email_auth_request_btn = (By.CSS_SELECTOR, '#__next > div > div > div.MuiStack-root.css-1k6f0k5 > div > div > div > div > div > form > div.MuiStack-root.css-yd8sa2 > div:nth-child(1) > div > button')
        self.input = (By.CSS_SELECTOR, '[type="text"]')
        self.email_input_error = (By.CSS_SELECTOR, '#\:r0\:-helper-text')
        self.pincode_input = (By.CSS_SELECTOR, '#\:r6\:')
        self.pincode_input_error = (By.CSS_SELECTOR, '#\:r1\:-helper-text')
        self.pw_input = (By.CSS_SELECTOR, '[type="password"]')
        self.pw_input_error = (By.CSS_SELECTOR, '#\:r6\:-helper-text')
        self.pw_check_input_error = (By.CSS_SELECTOR, '#\:r7\:-helper-text')
        self.pw_view_icon = (By.CSS_SELECTOR, '[class="MuiButtonBase-root MuiIconButton-root MuiIconButton-sizeMedium css-1469xk4"]')
        self.name_input_error = (By.CSS_SELECTOR, '#\:re\:-helper-text')
        self.signup_previous_btn = (By.CSS_SELECTOR, '#__next > div > div > div.MuiStack-root.css-1k6f0k5 > div > div > div > div > div > form > div.MuiStack-root.css-9npne8 > button')
        self.signup_next_btn = (By.CSS_SELECTOR, '#__next > div > div > div.MuiStack-root.css-1k6f0k5 > div > div > div > div > div > form > div.MuiStack-root.css-9npne8 > div > button')
        self.snack_msg = (By.ID, 'notistack-snackbar')

	def SignupStep1Wait(self):
        """
        회원가입 페이지 로드가 완료 될때까지 대기합니다.
        """
        	return WebDriverWait(self.driver, 10).until(EC.presence_of_element_located(self.agreement_ckb))
	
	def GetSignupStep1From(self):
        """
        회원가입 1단계 페이지(약관동의 페이지)의 구성을 텍스트화 시켜 리턴합니다.\n
        signup_step1_area_txt 변수의 0~11번까지는 아래 같은 순서로 텍스트가 들어옵니다.
        (signup_step1_area_txt[6],signup_step1_area_txt[8]은 '보기' 링크라서 제외하였습니다.)\n
        
        0. 서브 타이틀 / 1,2. 해더 파이틀 / 3. checkbox 첫번째 텍스트 / 4. checkbox 두번째 텍스트 / 5. checkbox 세번째 텍스트
        / 7. checkbox 네번째 텍스트 / 9. checkbox 다섯번째 텍스트 / 10. 이전으로> 버튼 / 11. 다음으로> 버튼 
        """
	        try:
	            result = []
	            signup_step1_area = self.driver.find_element(*self.signup_area).text
	            signup_step1_area_txt = signup_step1_area.split('\n')
	            signup_previous_btn = self.driver.find_element(*self.signup_previous_btn).is_enabled()
	            signup_next_btn = self.driver.find_element(*self.signup_next_btn).is_enabled()
	
	            return [signup_step1_area_txt[0], [signup_step1_area_txt[1], signup_step1_area_txt[2]], signup_step1_area_txt[3], signup_step1_area_txt[4], signup_step1_area_txt[5], signup_step1_area_txt[7], signup_step1_area_txt[9], [signup_step1_area_txt[10], signup_previous_btn, signup_step1_area_txt[11], signup_next_btn]]
	        except:
	            print('회원가입 1단계 구성 요소를 가져오는데 실패하였습니다.')
	            return False

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

.........
```

### Scripts 구조(ex : 회원가입 페이지)

- **AAA 구조 기준으로 작성**

```python
import sys, os
sys.path.append(os.getcwd())
_________________________________________________________________________________
⬇ 앱 자동화 코드 실행 및 TC 수행에 필요한 모듈 선언
from Src.TestBase.WebDriverSetup import WebDriverSetup
from HtmlTestRunner import HTMLTestRunner
from Src.PageObject.Function.Common.Common_Function import CommonFunction
from Src.TestOutput.Test_output import TestOutput
from Src.PageObject.Function.Common.Email_Certify_Number import EmailCheck
_________________________________________________________________________________
⬇ TC 수행에 필요한 페이지의 클래스 선언
from Src.PageObject.Page.Signup_page import SignupPage
from Src.PageObject.Page.Login_page import LoginPage
from Src.PageObject.Page.Home_page import HomePage
from time import sleep
import datetime
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
sys.path.append(os.getcwd())
from HtmlTestRunner import HTMLTestRunner
from unittest import TestLoader, TestSuite, TextTestRunner, makeSuite
from Src.TestBase.WebDriverSetup import WebDriverSetup
from Test.Scripts.Signup.test_full_signup_page import SignupFullTest

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

- Full_test_Runner
    
    ```python
    import sys, os, datetime, time
    
    sys.path.append(os.getcwd())
    from HtmlTestRunner import HTMLTestRunner
    from unittest import TestLoader, TestSuite, TextTestRunner, makeSuite
    from Src.TestBase.WebDriverSetup import WebDriverSetup
    from Test.Scripts.Signup.test_full_signup_page import SignupFullTest
    from Test.Scripts.User_Management.test_user_settings_page import UserSettingsPageTest
    from Test.Scripts.Login.test_full_login_page import LoginFullTest
    .........
    
    TIME = time.strftime("%Y-%m-%d %H:%M")
    
    class FullTest(WebDriverSetup):
    
        def __init__(self):
            super().setUpClass()
    
        def file_name(self):
            return '{}({})'.format(self.driver.capabilities['browserName'], self.driver.capabilities['browserVersion']) 
    
    		def suite():
    	    suite = TestSuite()
    	    suite.addTest(makeSuite(SignupFullTest))
    	    suite.addTest(makeSuite(LoginFullTest))
    	    ......
    
    	    return suite
    
    if __name__ == "__main__":
        full_test = FullTest()
        test_runner = HTMLTestRunner(
            combine_reports=True,
            report_title="전체 테스트 자동화 결과",
            report_name= full_test.file_name() + "_" + "전체 테스트 자동화 결과" + datetime.datetime.today().strftime("%Y%m%d %H:%M:%S"), 
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
