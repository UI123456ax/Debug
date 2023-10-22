# selenium自动化
准备工作
- chrome 驱动`chromedriver`需要linux版本
> chromedriver下载地址：
> - [常见版本](http://chromedriver.storage.googleapis.com/index.html)
> - [116版本及以上](https://googlechromelabs.github.io/chrome-for-testing/#stable)
>> 点击下载=>[116.0.5845.96](https://edgedl.me.gvt1.com/edgedl/chrome/chrome-for-testing/116.0.5845.96/linux64/chromedriver-linux64.zip) Linux

- 建立文件夹`chrome`用于存放chromedriver(linux版本)
- 建立文件夹`Spider`用来存放`requirements.txt` 和爬虫文件`test.py`
> `requirements.txt`: 安装python包
> `test.py`: 爬虫脚本

**requirements.txt**
```txt
requests==2.23.0
lxml==4.5.1
selenium==3.141.0
```
**test.py**
```python
from selenium import webdriver
from selenium.webdriver.chrome.options import Options
import os

# 取消可视化界面
chrome_options = webdriver.ChromeOptions()
chrome_options.add_argument('--headless')
chrome_options.add_argument('--no-sandbox')
chrome_options.add_argument('--disable-gpu')
chrome_options.add_argument('--disable-dev-shm-usage')
chromedriver = "/usr/bin/chromedriver"
os.environ["webdriver.chrome.driver"] = chromedriver

#  爬虫
driver = webdriver.Chrome(chrome_options=chrome_options,executable_path=chromedriver)
driver.get("https://www.baidu.com")
print(driver.title)
driver.quit()
```
# Github部署
建立一个`工作流`
`.github\workflows\test.yml`
Yml配置文件如下
```yaml
name: selenium

# Controls when the action will run. 
on:
  # Triggers the workflow on push or pull request events but only for the main branch
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]
  # Allows you to run this workflow manually from the Actions tab
  workflow_dispatch:

# A workflow run is made up of one or more jobs that can run sequentially or in parallel
jobs:
  # This workflow contains a single job called "build"
  build:
    # The type of runner that the job will run on
    runs-on: ubuntu-latest

    # Steps represent a sequence of tasks that will be executed as part of the job
    steps:
      # Checks-out your repository under $GITHUB_WORKSPACE, so your job can access it
      - name: Checkout
        uses: actions/checkout@v2

      # Runs a single command using the runners shell
      - name: 'Set up Python'
        uses: actions/setup-python@v1
        with:
           python-version: 3.7
      - name: 'Install requirements'
        run: pip install -r ./Spider/requirements.txt
      - name: 'Working'
        run: |
          sudo cp -p ./chrome/chromedriver /usr/bin/
          chmod -R 777 /usr/bin/chromedriver
          python ./Spider/test.py
```