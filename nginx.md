# X-Forwarded-Forをどう活用するか

> X-Forwarded-For (XFF) ヘッダーは、 HTTP プロキシ又はロードバランサーを通過してウェブサーバーへ接続したクライアントの、送信元 IP アドレスを特定するために事実上の標準となっているヘッダー

https://developer.mozilla.org/ja/docs/Web/HTTP/Headers/X-Forwarded-For

送信元IPアドレスを保持している。ALB等のクラウドサービスのロードバランサを使っている場合、サービスによって設定されていたりされなかったりする。AWSのELBの場合は、クライアントのIPアドレスを渡してくれる模様。
https://docs.aws.amazon.com/ja_jp/elasticloadbalancing/latest/classic/x-forwarded-headers.html

nginxでX-Formaded-Forの扱いとして、通常はproxy_add_x_forwarded_forを使うことで、Nginxに到達したX-Forwarded-Forにremote_addrを追加した値を設定してくれる。

> NGINX even provides a $proxy_add_x_forwarded_for variable to automatically append $remote_addr to any incoming X-Forwarded-For headers.

https://www.nginx.com/resources/wiki/start/topics/examples/forwarded/

しかし、例えばNginxでリバースプロキシやロードバランサを構築する等、バックエンドとの境界として設置する場合、そのままproxy_add_x_forwarded_forを使うとクライアントからの設定値で改ざんできてしまう。
https://www.serotoninpower.club/archives/790/#%E5%85%AC%E5%BC%8F%E3%83%89%E3%82%AD%E3%83%A5%E3%83%A1%E3%83%B3%E3%83%88%E3%81%AE-proxy-add-x-forwarded-for-%E3%81%AE%E8%AA%AC%E6%98%8E

X-Forwarded-Forにremote_adrrを設定し、クライアントが渡してくる値を使わないようにすることが必要。(現代はクラウド事業者側のロードバランサを使うことが多いと思うので、あまり心配しなくてもいいかもしれない)

# X-Forwarded-Protoをうまく使う

システム開発をする時、HTTPS -> LB -> HTTP -> nginx  の形で構築することをよくやる。理由としては、全部に証明書入れて管理するのが面倒というのが一番。なので終端だけHTTPSで中はHTTPで通すように設定している。

その時困るのがX-Forwarded-Proto。WEBフレームワークはCSRF検証がもはや当たり前のように入っており、トークンそのものの検証だけではなくリクエストされてきたURLがサーバー設定と完全一致しているかの信ぴょう性チェックも行ってくる。(Railsの場合だと、request_forgery_protectionが該当)

冒頭のケースだと、LBにはHTTPSで入ってくるが、バックエンド側はHTTPとして処理してしまう(フレームワークにもよるが)
LBに投げつけられたHTTPSと一致されず信憑性エラーで死ぬ。

例えばRailsだと、以下でその判定が行われている。
https://github.com/rails/rails/blob/98a4c0c76938e46009cca668da9c3b584a9e9e74/actionpack/lib/action_controller/metal/request_forgery_protection.rb#L459

> request.origin == request.base_url

ここで使われているbase_urlはRackで定義されているメソッドで。

https://github.com/rack/rack/blob/master/lib/rack/request.rb#L502

で、"#{scheme}://#{host_with_port}"の値を返却してくる。でこの値を最後まで追っていくと

https://github.com/rack/rack/blob/649c72bab9e7b50d657b5b432d0c205c95c2be07/lib/rack/request.rb#L628

に当たる。ここでHTTP_X_FORWARDED_PROTOが出てくる。つまり、HTTP_X_FORWARDED_PROTOにLBが受け付けたプロトコルと同じものが設定されていれば回避ができる(Railsの場合ならね)
https://qiita.com/incog_neet/items/aea655f927da2063512d


ALBを使っている場合、
> Elastic Load Balancing は、クライアントとロードバランサーの間で使用されたプロトコルを X-Forwarded-Proto リクエストヘッダーに格納し、このヘッダーをサーバーに渡します。`

https://docs.aws.amazon.com/ja_jp/elasticloadbalancing/latest/classic/x-forwarded-headers.html

とある。そのため、$http_x_forwarded_protoを使えばALBから渡されたものをそのまま利用することができる。なので冒頭のケースではHTTPSとして処理される。
よく$schemeが使われている例があるが、これはLBで受けたものがそのまま判断されるのでこの例ではHTTPになってしまう。なので、http_x_forwarded_protoを使う

