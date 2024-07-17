---
title: python
date: 2024-07-17 09:46:52
---
生成器返回文件流
```bash
def iterfile(filestream):
    yield from filestream

headers = {}
content_disposition = 'attachment; filename="{}"'.format(filename)
headers["Content-Disposition"] = content_disposition

z = zipfile.ZipFile(filestream, "r")
content = z.open(filename)

return StreamingResponse(iterfile(content),
                             status_code=200,
                             media_type="application/octet-stream",
                             headers=headers)
```
