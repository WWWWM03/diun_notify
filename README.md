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

# 4. discord配置
export DISCORD_WEBHOOK=""
export DISCORD_PICURL=""
```

3.自行替换中括号内的内容，替换三个路径。

```bash
docker run -d --name diun \
  -e "TZ=Asia/Shanghai" \
  -e "LOG_LEVEL=info" \
  -e "LOG_JSON=false" \
  -e "DIUN_PROVIDERS_DOCKER=true" \
  -e "DIUN_PROVIDERS_FILE_FILENAME=/custom-images.yml" \
  -v "[替换path]:/data" \
  -v "[替换path]/custom-images.yml:/custom-images.yml:ro" \
  -v "[替换path]/diun.yml:/diun.yml:ro" \
  -v "/var/run/docker.sock:/var/run/docker.sock" \
  crazymax/diun:latest
```

4.测试通知。

```bash
docker exec -it [容器名] diun notif test
```

![image](https://user-images.githubusercontent.com/74545085/194116386-1477a9cb-35f5-45d8-ad24-2d5028bbfd85.png)TG推送通道

![image](https://user-images.githubusercontent.com/74545085/195364047-7779a712-4e16-4798-a098-f12ba929d082.png)discord推送通道

![image](https://user-images.githubusercontent.com/74545085/194116502-45a70cf9-5a66-4647-86d0-618d8ab2ef5c.png)TG推送通道

![image](https://user-images.githubusercontent.com/74545085/194116536-c7e8849f-ffbb-4d48-a5ff-f1a8d34617eb.png)企微推送通道

**感谢这位大佬的脚本，我在它的基础上魔改了推送脚本的内容。**
https://github.com/Qliangw/emby_notify
