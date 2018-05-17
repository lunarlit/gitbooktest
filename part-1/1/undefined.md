# 인스타 태그 크롤러



```python
from selenium import webdriver
import time
import numpy as np
import re

dic = {}

driver = webdriver.Chrome('./chromedriver')
driver.implicitly_wait(3)

keyword = input("원하는 키워드를 입력해주세요.")
print(keyword, "에 대한 해시태그 수집을 시작합니다!")

driver.get('https://www.instagram.com/?hl=ko')

# facebook으로 로그인 버튼 클릭
driver.find_element_by_class_name('_qv64e').click()

driver.find_element_by_id('email').send_keys('') # id를 입력하세요
driver.find_element_by_id('pass').send_keys('') # 비밀번호를 입력하세요

# 로그인 버튼 클릭
driver.find_element_by_id('loginbutton').click()

# 검색 창 클릭
driver.find_element_by_class_name('_dcj9f').click()

# 검색어 입력
driver.find_element_by_class_name('_avvq0').send_keys(keyword)

# 검색 제시어 클릭
driver.find_element_by_class_name('_ndl3t').click()

# 첫번째 사진 클릭
driver.find_element_by_class_name('_mck9w').find_element_by_tag_name('a').click()

# 태그 숫자 카운트 함수
def inc_or_add(tag):
    num = dic.get(tag)
    if num:
        dic[tag] = num+1
    else:
        dic[tag] = 1

# 반복 수집
for i in range(300):

    # 첫번째 글의 텍스트 수집
    text = driver.find_element_by_class_name('_ezgzd').text

    # arr = re.split('(#[^#\s]+)', text)
    # filtered = list(filter(lambda t: t.startswith('#'), arr))

    # 해시태그 추출
    first = text.split(' ')
    second = list(map(lambda s: s.split('#')[1:], first))
    filtered = np.array(second).flatten()

    # dictionary에 태그 카운트
    list(map(lambda t: inc_or_add(t), filtered))

    # 다음 사진 클릭
    driver.find_element_by_class_name('_3a693').click()
    time.sleep(0.5)

# 결과 파일 오픈
f = open("crawl.csv", 'w', encoding='utf-8')

# dictionary 내용 기록
for k, v in dic.items():
    f.write(k + ',' + str(v) + '\n')

f.close()
driver.close()
```

