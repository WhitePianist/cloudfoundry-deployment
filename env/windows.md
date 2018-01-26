# Widnows 10 환경구성
https://docs.microsoft.com/ko-kr/virtualization/windowscontainers/quick-start/quick-start-windows-10

## Docker 설치
[Windows용 Docker를 다운로드](https://download.docker.com/win/stable/InstallDocker.msi)  

>https://docs.docker.com/docker-for-windows/install/

## Windows  전환
Docker 트레이메뉴 "Switch to Windows containers..." 실행

## 문제 해결
### Access Denied
docker-users 그룹에 사용자 추가
```
Docker for Windows - Access Denied
You are not allowed to use Docker. You must be in the “docker-users” group.
```

- "Windows + R" 실행창에서 "lusrmgr.msc" 입력
- "그룹" Click
- "docker-users" Double Click
- "추가(D)..." Click
- 사용자 입력 및 확인

https://forums.docker.com/t/docker-for-windows-access-denied-beta/33061

