---
title: 表单上传文件-multipart/form-data
date: 2017-08-07 16:54:17
tags:
    - HTTP
    - Computer Network
    - node
    - HTML
---

<!-- more -->

- [W3C规范--Form content types](https://www.w3.org/TR/html401/interact/forms.html#h-17.13.4)
- [RFC7578--Returning Values from Forms: multipart/form-data](https://tools.ietf.org/html/rfc7578)

```html
<form action="/" method="post" enctype="multipart/form-data">
  <input type="text" name="description" value="some text">
  <input type="file" name="myFile">
  <button type="submit">Submit</button>
</form>
```
- `form`的`enctype` 属性规定在发送到服务器之前应该如何对表单数据进行编码
  - 默认为 `application/x-www-form-urlencoded`，对所有字符都会进行编码（空格转换为 "+" 加号，特殊符号转换为 ASCII HEX 值）。
  - 也可以设置为`text/plain`，空格转换为 `+` 加号，但不对特殊字符编码
  - 在上传文件的时候设置为`multipart/form-data`
- `application/x-www-form-urlencoded`在发送大的二进制数据或包含非ASCII字符的文本时效率很低。在提交包含文件、非ASCII数据和二进制数据的表单时应使用`multipart/form-data`


---

部分请求头如下所示
```
POST /foo HTTP/1.1
Content-Length: 68137
Content-Type: multipart/form-data; boundary=---------------------------974767299852498929531610575

---------------------------974767299852498929531610575
Content-Disposition: form-data; name="description" 

some text
---------------------------974767299852498929531610575
Content-Disposition: form-data; name="myFile"; filename="foo.txt" 
Content-Type: text/plain 

(content of the uploaded file foo.txt)
---------------------------974767299852498929531610575--
```
- POST`multipart/form-data`时，数据不是在`body`中，而是在`header`中。HTTP的POST是不设文件上传大小限制了，所以理论上传多大都行，只是不同服务器可能限制不同
- 上面的请求中`Content-Length`是整个request的大小
- `Content-Type`格式是` multipart/form-data; boundary=${...}`，其中`boundary`的值用于分隔表单的不同部分的数据，这个值可以任意制定，但是应该避免与请求的正常文本重复，
- 每一个表单部分格式包含
  - 分隔符，这里分隔符要比`Content-Type`中的多两个`-`
  - Content-Disposition：值为`form-data; name="${...}"`
  - 文件编码后的内容
  
  ```
  ---------------------------974767299852498929531610575
  Content-Disposition: form-data; name="description" 

  some text
  ```
- 整个请求最后，是`--boundary--`，注意前后两端都有`--`。在[MDN  Content-Type](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Type)中的示例，请求最后没有加`--`，应该是错误的请求？

---

应用层HTTP的header直接把所有数据进行了编码，进入运输层会有TCP分包传给服务器

---

### node解析
- [busboy](https://github.com/mscdex/busboy)
- [multer](https://github.com/expressjs/multer)
- [multiparty](https://github.com/pillarjs/multiparty)