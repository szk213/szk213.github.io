# Node.js

## cluster
参考:
- [Node.jsでcluster環境でのlogging](http://christina04.hatenablog.com/entry/2015/11/26/224314)
- [Node.jsのClusterをセットアップして、処理を並列化・高速化する](http://postd.cc/setting-up-a-node-js-cluster/)

## ファイル操作

### 読み込み
非同期読み込み
```js
var fs = require('fs');
fs.readFile('./test.txt', 'utf8', function (err, text) {
    if(err) {
      return;
    }
    console.log(text);
});
```

同期読み込み
```js
var fs = require('fs');
var text = fs.readFileSync('test.txt', 'utf-8');
console.log(text);
```

### 書き込み
非同期書き込み
```js
var fs = require('fs');
var text = "sample text";
fs.writeFile('test.txt', text);
```

## エラーハンドリング
参考:
- [node.js におけるエラー処理のコーディングパターン](http://d.hatena.ne.jp/kazuhooku/20120420/1334891656)

## SHA256でハッシュ値生成
```js
const crypto = require('crypto');
const hash = crypto.createHash('sha256');

hash.update('some data to hash');
console.log(hash.digest('hex'));
```

# エンコード
## Punycode
エンコード
```js
// encode domain name parts
punycode.encode('mañana'); // 'maana-pta'
punycode.encode('☃-⌘'); // '--dqo34k'
```

デコード
```js
// decode domain name parts
punycode.decode('maana-pta'); // 'mañana'
punycode.decode('--dqo34k'); // '☃-⌘'
```

## URIエンコード
javascriptの標準ライブラリを使用する

### 参考
- [Node.js v6.2.1 Documentation](https://nodejs.org/api/punycode.html)

#　インストール
## source build install
```sh
yum install -y gcc-c++ make sudo
curl -O http://nodejs.org/dist/v0.10.32/node-v0.10.32.tar.gz && tar xvf node-v0.10.32.tar.gz && cd node-v0.10.32/ && ./configure --prefix=/usr/local && make && make install
ln -s /usr/local/bin/node /usr/bin/node
ln -s /usr/local/bin/npm /usr/bin/npm
ln -s /usr/local/lib/node_modules /usr/lib/node_modules
```

## requestモジュール
imageをbase64エンコードで取得
```js
var loadBase64Image = function (url, callback) {
    // Required 'request' module
    var request = require('request');

    // Make request to our image url
    request({url: url, encoding: null}, function (err, res, body) {
        if (!err && res.statusCode == 200) {
            // So as encoding set to null then request body became Buffer object
            var base64prefix = 'data:' + res.headers['content-type'] + ';base64,'
                , image = body.toString('base64');
            if (typeof callback == 'function') {
                callback(image, base64prefix);
            }
        } else {
            throw new Error('Can not download image');
        }
    });
};
```
参考: [get image from another domain and encode base64 by node js(11)](http://stackoverflow.com/questions/11280063/get-image-from-another-domain-and-encode-base64-by-node-js)

```js
var request = require('request');
var BufferList = require('bufferlist').BufferList;

bl = new BufferList(),

request({uri:'http://tinypng.org/images/example-shrunk-8cadd4c7.png',responseBodyStream: bl}, function (error, response, body)
{
    if (!error && response.statusCode == 200)
    {
        var type = response.headers["content-type"];
        var prefix = "data:" + type + ";base64,";
        var base64 = new Buffer(bl.toString(), 'binary').toString('base64');
        var data = prefix + base64;
        console.log(data);
    }
});
```
参考:[Node.js get image from web and encode with base64](http://stackoverflow.com/questions/17124053/node-js-get-image-from-web-and-encode-with-base64)


### Link

[結局のところ node.js をサーバーにインストールするにはどうやればいいのか？](http://moro-archive.hatenablog.com/entry/2015/07/27/225747)
[開発に関するメモ書き（Node.js）](http://kazuyan.hatenablog.com/entry/2016/05/29/181239)

### cookbook

[redguide/nodejs](https://github.com/redguide/nodejs)

berks vendorを行うこと。

## CentOS7へのインストール

**yum** でインストール

```sh
yum install nodejs npm --enablerepo=epel
```

**ソース** からインストール
```sh
sudo yum groupinstall 'Development tools'
curl -O http://nodejs.org/dist/v0.10.32/node-v0.10.32.tar.gz
tar xvf node-v0.10.32.tar.gz
cd node-v0.10.32/
./configure --prefix=/usr/local
make
sudo make install
```

### 参考
- [Node.jsのセキュリティ・チェックリスト](http://postd.cc/node-js-security-checklist/)


### phantomjsのエラー
Centos7上で`grunt-contrib-jasmine`を実行したところ、以下のエラーが出た。

```
PhantomJS threw an error:ERROR
>> /root/test/oip-frm/Front/node_modules/grunt-contrib-jasmine/node_modules/grunt file: No such file or directory 0 [ '/root/test/oip-frm/Front/node_modules/gruonfig.so.1: cannot open shared object file: No such file or directory' ]
>> In order for this task to work properly, PhantomJS must be installed locally
>> via NPM. If you're seeing this message, generally that means the NPM install
>> has failed. Please submit an issue providing as much detail as possible at:
>> https://github.com/gruntjs/grunt-lib-phantomjs/issues
Warning: PhantomJS not found. Use --force to continue.
```
パッケージインストール`yum install fontconfig`で解決
