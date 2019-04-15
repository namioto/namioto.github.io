---
layout: "post"
title: "Gitlab ssh 접속이 안돼요"
date: "2018-11-07 11:15"
author: namioto
tags: []
images: "/files/covers/image"
cover:
  title:
  link:
---



> git clone git@gitlab-server:DataWarehouse/poi-dataset.git
```sh
Cloning into 'poi-dataset'...
GitLab: The project you were looking for could not be found.
fatal: Could not read from remote repository.

Please make sure you have the correct access rights
and the repository exists.
```

> ssh -T git@61.33.249.118
```
Welcome to GitLab, Anonymous!
```


git 계정의 home 디렉토리
~/gitlab-shell/config.yml
```sh
# File used as authorized_keys for gitlab user
auth_file: "/var/opt/gitlab/.ssh/authorized_keys"
```

```sh
command="/opt/gitlab/embedded/service/gitlab-shell/bin/gitlab-shell key-8",
no-port-forwarding,no-X11-forwarding,no-agent-forwarding,no-pty
ssh-rsa AAAAB3NzaC1yc2EAAAADgUlxaZh
```

ssh-rsa 값이 key-여러개에 중복 되어 있었음

~/gitlab-shell/config.yml
```sh
# Log file.
# Default is gitlab-shell.log in the root directory.
log_file: "/var/log/gitlab/gitlab-shell/gitlab-shell.log"
```

의 로그를 보니

```
E, [2018-11-07T10:32:42.481146 #121800] ERROR -- : API call <GET http://127.0.0.1:8080/api/v4/internal/discover?key_id=11> failed: 404 => <{"message":"404 Not found"}>.
I, [2018-11-07T10:33:43.130375 #121961]  INFO -- : GET http://127.0.0.1:8080/api/v4/internal/discover?key_id=11 0.01529
```

잘못된 key_id를 호출하는 것을 확인

authorized_keys 파일에 중복되는 키들을 삭제하고 key_id가 높은 값 하나만 남겨둠.


> ssh -T git@61.33.249.118
```
Welcome to GitLab, Daesung Park!
```
