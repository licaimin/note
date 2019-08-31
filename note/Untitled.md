需求背景

在某些业务中，大文件上传是一个比较重要的交互场景，如上传比较大的Excel表格数据、上传影音文件等。如果文件体积比较大，或者网络条件不好时，上传的时间会比较长（要传输更多的报文，丢包重传的概率也更大），用户不能刷新页面，只能耐心等待请求完成。

```html
<form id="j-puload-form" action="/fileUpload" method="post" enctype="multipart/form-data">    
    <input type="file" id="j-upload-input" name="upload"/><button type="submit">提交</button>
</form>
```

