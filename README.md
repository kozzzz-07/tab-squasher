# Tab Squasher
重複して開いているタブを削除するChrome拡張機能。

## オプション

### オリジン一致
originで一致するタブを削除する。

```
開いてるタブが下記の場合、2と3を削除する。

1. https://hoge.com/abc/top.html#abc
2. https://hoge.com/abc/top.html
3. https://hoge.com/def/contents.html
```

### パス一致
パスを含めて一致するタブを削除する。

```
開いてるタブが下記の場合、2を削除する。

1. https://hoge.com/abc/top.html#abc
2. https://hoge.com/abc/top.html
3. https://hoge.com/def/contents.html
```

### パラメーター含めて一致
パラメータまでを含めて一致するタブを削除する。

```
開いてるタブが下記の場合、3を削除する。

1. https://hoge.com/abc/top.html#abc
2. https://hoge.com/abc/top.html
3. https://hoge.com/abc/top.html#abc
4. https://hoge.com/abc/top.html#def
5. https://hoge.com/def/contents.html
```

## 削除しないタブ
- シークレットモードで開いているタブ
- ピン留されているタブ

## ローカル動作確認
- `npm run build`
- chrome://extensions/ にアクセス
- 「パッケージ化されていない拡張機能」を押下し`/dist`を読み込ませる

