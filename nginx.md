# X-Forwarded-Forの設定

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
