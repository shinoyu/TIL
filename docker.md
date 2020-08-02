# 起動しているコンテナを全部killする

```
docker ps | awk 'NR<=1 {next} {print $1}' | xargs docker kill
```


# DockerのStartupコンテナの解除と調査方法

https://stackoverflow.com/questions/40513545/how-to-prevent-docker-from-starting-a-container-automatically-on-system-startup


docker-composeで環境作る時、`restart: always`を指定していると起こりやすい問題。
docker-compose側の設定を消しても一度作成された状態だとalwaysが残ってしまうので、設定を個別に変更する必要がある。
```
dokcer inspect {container} | grep -A 3 RestartPolicy
```


NameがAlwaysになっていたら自動起動が有効になっている。
```
 "RestartPolicy": {
                "Name": "always",
                "MaximumRetryCount": 0
            },
```


以下で自動起動を停止できる。
```
docker update --restart=no {container} 
```
