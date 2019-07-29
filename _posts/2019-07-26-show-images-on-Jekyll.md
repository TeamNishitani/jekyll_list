Jekyllで画像が表示されない
--------------------------

-   .mdに以下を直書きで表示することができる．

``` {.example}
![キャプション]({{site.baseurl}}/assets/images/sample.png)
```

### 参考文献

-   [Jekyllのエントリに画像を貼る -
    たけぞう瀕死ブログ](https://takezoe.hatenablog.com/entry/20140629/p1)
    accessed on 25 July 2019.

![a]({{site.baseurl}}/assets/images/nichiyama-blog-red.svg)JekyllでSVGが表示されない

-   標準ではsvgが表示されないため，以下の手順で解決した．
    -   Jekyllのホームディレクトリに~pluginsディレクトリを作成~
    -   pluginsにsvg.rbというファイルを作成.
        -   内容は以下に

        ``` {.example}
        require 'webrick'
        include WEBrick
        WEBrick::HTTPUtils::DefaultMimeTypes.store 'svg', 'image/svg+xml'
        ```

### 参考文献

-   [SVGを処理するようにJekyllを設定するにはどうすればよいですか？ -
    コードブログ](https://codeday.me/jp/qa/20190512/808651.html)
    accessed on 25 July 2019

blog2jekyllによるsvg表示の自動化
--------------------------------

### やること

-   画像データをassets/imagesにcp
-   orgに書いてあるリンクを以下の形に変えて，mdへ変換

    ``` {.example}
    ![キャプション]({{site.baseurl}}/images/sample.png)
    ```

### 問題点

-   リンクを変更しmdに変換すると,
    !$$a$$({{site.baseurl}}/images/nichiyama-blog-red.svg)
    のように"がつく．

