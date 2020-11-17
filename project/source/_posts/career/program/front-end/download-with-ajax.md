---
title: 使用ajax下载文件
---

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <script src="https://cdn.bootcdn.net/ajax/libs/jquery/3.5.1/jquery.js"></script>
    <link href="https://cdn.bootcdn.net/ajax/libs/twitter-bootstrap/4.5.0/css/bootstrap.css" rel="stylesheet">
</head>
<body>
    <div class="container">

        <div class="form-group">
            <label class="control-label" for="userTokenInput">用户Token</label>
            <input type="text" class="form-control" id="userTokenInput" style="width:500px;" placeholder="用户token"
                value="">
        </div>
        <div class="form-group">
            <label class="control-label" for="urlInput">
                输入地址
            </label>
            <div class="form-check form-check-inline">
                <input class="form-check-input" type="radio" name="inputUrl" id="inputUrl1" value="oss" checked>
                <label class="form-check-label" for="inputUrl1">
                    OSS地址
                </label>
            </div>
            <div class="form-check form-check-inline">
                <input class="form-check-input" type="radio" name="inputUrl" id="inputUrl2" value="api">
                <label class="form-check-label" for="inputUrl2">
                    API附件地址
                </label>
            </div>
            <div class="form-check form-check-inline">
                <input class="form-check-input" type="radio" name="inputUrl" id="inputUrlTestApi" value="testapi">
                <label class="form-check-label" for="inputUrlTestApi">
                    测试线附件地址
                </label>
            </div>
            <div class="form-check form-check-inline">
                <input class="form-check-input" type="radio" name="inputUrl" id="inputUrl3" value="custom">
                <label class="form-check-label" for="inputUrl3">
                    自定义
                </label>
            </div>

            <input type="text" class="form-control" id="urlInput" style="width:500px;" placeholder="地址"
                value="" />
        </div>
        <div class="form-group">
            <button class="btn btn-primary" id="downloadBtn">ajax下载</button>
            <button class="btn btn-primary" id="xhrDownloadBtn">xhr下载</button>
        </div>

    </div>
    <script type="text/javascript">
        $("#downloadBtn").click(function () {
            $.ajax({
                url: $("#urlInput").val(),
                type: "get",
                xhrFields: {
                    responseType: 'blob',
                },
                headers: {
                    Authorization: $("#userTokenInput").val()
                },
                success: function (data, status, xhr) {
                    debugger
                    var type = xhr.getResponseHeader("Content-Type") || "application/octet-stream"
                    var dis = xhr.getResponseHeader("Content-Disposition")
                    var file = 'download'
                    if (dis) {
                        var splitter = 'filename='
                        if (dis.indexOf(splitter) > 0) {
                            file = dis.substr(dis.indexOf(splitter) + 1)
                        }
                    }
                    var blob = new Blob([data], {
                        type: type
                    })
                    var url = window.URL.createObjectURL(blob);
                    $('<a>', {
                        href: url,
                        download: file,
                    })[0].click()
                    window.URL.revokeObjectURL(url)
                },
                complete: function (data) {
                    console.log("complete")
                }
            });
        })

        $("#xhrDownloadBtn").click(function () {
            var request = new XMLHttpRequest();
            request.open('GET', $("#urlInput").val(), true);
            request.setRequestHeader('Authorization', $("#userTokenInput").val())
            request.responseType = 'blob';
            request.onload = function () {
                if (request.status != 200) {
                    return
                }
                var dis = request.getResponseHeader("Content-Disposition")
                var file = 'download'
                if (dis) {
                    var splitter = 'filename='
                    if (dis.indexOf(splitter) > 0) {
                        file = dis.substr(dis.indexOf(splitter) + 1)
                    }
                }
                var type = request.getResponseHeader("Content-Type") || "application/octet-stream"
                var blob = new Blob([request.response], {
                    type: type
                });
                var link = document.createElement('a');
                link.href = window.URL.createObjectURL(blob);
                link.download = file;
                document.body.appendChild(link);
                link.click();
                document.body.removeChild(link);
            };

            request.send();
        })

        var urls = {
            'oss': '',
            'api': '',
            'testapi':'',
            'custom': ''
        }
        $('input[name="inputUrl"]').click(function () {
            var type = $(this).val()
            $("#urlInput").val(urls[type] || '')
        })
    </script>
</body>

</html>
```