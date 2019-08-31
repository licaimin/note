 Crypto 加密模块

使用require('crypto')来访问这个模块。

crypto 模块需要node 所运行的运行支持OpenSSL，该模块为使用安全证书实现HTTPS 安全网络以及HTTP 连 接提供了支持。 模块同样为OpenSSL 的hash、hmac、cipher、decipher、sign 以及verify 方法提供一层包装（以方便在Node 中 使用）。

**crypto.createCredentials(details)**

建立一个证书对象，参数detail 是由键值对组成的字典。

key : 一个字符串，包含PEM 编码的私钥

cert: 一个字符串，包含PEM 编码的证书

ca : 一个包含PEM 编码的、可信任的数字中心认证证书的字符串或者字符串列表

如果参数details 中没有'ca' ， 那么node.js 将缺省使用http://mxr.mozilla.org/mozilla/source/security/nss/lib/ckfw/builtins/certdata.txt 中给出的可信任的公钥。

**crypto.createHash(algorithm)**

通过参数algorithm 指定算法建立并且返回一个哈希对象，可以用来产生哈希摘要。 algorithm 参数依赖于node 运行平台上OpenSSL 所支持的有效算法。例如sha1,md5,sha256,sha512等，在最近发 布的版本中, openssl list-message-digest-algorithms 将显示有效的算法摘要。

**hash.update(data)**

使用data 更新哈希表。当以流的方式接受新数据时（数据可能被为分多次接收），可多次调用此方法。

**hash.digest(encoding='binary')**

计算所有传递来数据的哈希摘要。编码可以是'hex','binary'或者'base64'。





## 2

用new Blob()

不需要formData了

https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequest/Sending_and_Receiving_Binary_Data

## 3

怎样将分片的数据放到一个数组，然后用promise.all

通过resolve然后再then

```
readStream.on('end', () => {
                //新建formdata数据对象
                var md5Value = md5.digest('hex');
                let blob = new Blob(arr)
                ajaxArray.push(
                    xhr({
                        action: `${options.action}/${id}`,
                        headers: Object.assign({}, options.headers, {
                            "Content-Type": "application/octet-stream", // 固定header
                            "x-chunks": pieces, // 文件分块数量
                            "x-chunk": i + 1, // 第几个文件分块，从 1开始
                            "x-chunk-md5": md5Value, // 文件分块md5
                            "x-total-length": size, // 文件总大小
                            "x-part-length": enddata - (i * chunkSize) // 文件分块大小
                        }),
                        method: 'post',
                        bifFile: 1,
                        data: blob,
                        onProgress: e => {
                        },
                        onSuccess: res => {

                        },
                        onError: res => {
                            console.log(err)
                        }
                    })
                )
                resolve(ajaxArray)
            })
```



```
  for (let i = 0; i < pieces; i++) {
        uploadPiece(i).then(res => {
            if (i === pieces - 1) {
                Promise.all(ajaxArray).then((res) => {
                    console.log(res)
                    xhr({
                        action: `https://airdisk-cvte-test.test.maxhub.vip/api/pub/files/${id}`,
                        headers: options.headers,
                        method: 'get',
                        onProgress: e => {

                        },
                        onSuccess: res => {
                            console.log(res.downloadUrl)
                        },
                        onError: res => {

                        }
```

