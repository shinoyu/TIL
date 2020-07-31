# 起動しているコンテナを全部killする

```
docker ps | awk 'NR<=1 {next} {print $1}' | xargs docker kill
```
