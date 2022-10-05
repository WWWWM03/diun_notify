首先文档奉上

[Diun](https://crazymax.dev/diun/)

[https://github.com/WWWWM03/diun_notify](https://github.com/WWWWM03/diun_notify)

**下载github仓库内容。**

1.修改 **custom-images.yml 及 diun.yml** 文件。

**custom-images.yml** 内容是需要监控的image及tag，不写tag应该是默认latest。

```yaml
# /custom-images.yml
- name: yipengfei/movie-robot:beta
- name: linuxserver/emby:beta
- name: linuxserver/overseerr:latest
- name: linuxserver/tautulli:latest
- name: [lscr.io/linuxserver/plex:latest](http://lscr.io/linuxserver/plex:latest)
- name: 6053537/portainer-ce
- name: linuxserver/emby:latest
- name: vergilgao/mdc:latest
```

```yaml
# /diun.yml
db:
  path: "/data/diun.db"

watch:
  workers: 20
  schedule: "CRON_TZ=Asia/Shanghai */5 * * * *"
  firstCheckNotif: true

notif:
  script:
      cmd: "/data/notify.sh"
      args:
        - "DIUN_VERSION"
        - "DIUN_ENTRY_STATUS"
        - "DIUN_HOSTNAME"
        - "DIUN_ENTRY_PROVIDER"
        - "DIUN_ENTRY_IMAGE"
        - "DIUN_ENTRY_HUBLINK"
        - "DIUN_ENTRY_MIMETYPE"
        - "DIUN_ENTRY_DIGEST"
        - "DIUN_ENTRY_CREATED"
        - "DIUN_ENTRY_PLATFORM"        
#下面是discord推送,如不需要自行删除
   discord:
     webhookURL: **[webhookURL]**
     mentions:
       - "@everyone"
     renderFields: true
     timeout: 10s
     templateBody: |
       Docker tag {{ .Entry.Image }} which you subscribed to through {{ .Entry.Provider }} provider has been released.
      
regopts:
  - name: "myregistry"
    username: **[dockerhub_username]**
    password: **[dockerhub_password]**
    timeout: 20s
    insecureTLS: true
  - name: "docker.io"
    selector: image
    username: **[dockerhub_username]**
    password: **[dockerhub_password]**

providers:
  docker:
    watchStopped: true
```

2.修改 **user.conf** 文件相关推送通道参数

```yaml
# 1. bark配置
export BARK_KEY=""

# 2. 电报参数
export TG_CHAT_ID=""
export TG_TOKEN=""

# 3. 企微参数
export CORPID=""
export CORP_SECRET=""
export AGENTID=""
export MEDIA_ID=""
export TOUSER="@all"
```

3.自行替换中括号内的内容，替换三个路径。

```bash
docker run -d --name diun \
  -e "TZ=Asia/Shanghai" \
  -e "LOG_LEVEL=info" \
  -e "LOG_JSON=false" \
  -e "DIUN_PROVIDERS_DOCKER=true" \
  -e "DIUN_PROVIDERS_FILE_FILENAME=/custom-images.yml" \
  -v "**[替换path]**:/data" \
  -v "**[替换path]**/custom-images.yml:/custom-images.yml:ro" \
  -v "**[替换path]**/diun.yml:/diun.yml:ro" \
  -v "/var/run/docker.sock:/var/run/docker.sock" \
  crazymax/diun:latest
```

4.测试通知。

```bash
docker exec -it **[容器名]** diun notif test
```

![TG推送通道](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/8ae10996-0778-42e1-929d-dabe8ee895a4/Untitled.png)

TG推送通道

![discord推送通道](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/2f43d553-ceb3-4274-a8de-1c4bbc400c02/Untitled.png)

discord推送通道

![TG推送通道](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/28b77332-d0cb-40f4-9bd1-816c75022643/Untitled.png)

TG推送通道

![企微推送通道](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/1b77d4d2-6aba-4353-9d44-a9e9c85d63b7/Untitled.png)

企微推送通道

**感谢这位大佬的脚本，我在它的基础上魔改了推送脚本的内容。**
