# 연습 컨테이너 실행
docker run -it -p 8080:8080 -p 3000:3000  -v C:\\Users\\cheolgyu\\go\\github.com\\cheolgyu\\stock\\caddy:/caddy --name test ubuntu

# 가격 목록 돌다가 죽음 ec2 docker-ticker 컨테이너가 죽음 고루틴  안쉬고 돔. 18 .오류도 안나옴. 19

# 설치
ubuntu
    apt update  -y
    apt install -y curl systemctl
    apt install -y debian-keyring debian-archive-keyring apt-transport-https 
    curl -1sLf 'https://dl.cloudsmith.io/public/caddy/stable/gpg.key' |  apt-key add -
    curl -1sLf 'https://dl.cloudsmith.io/public/caddy/stable/debian.deb.txt' |  tee /etc/apt/sources.list.d/caddy-stable.list
    apt update  -y
    apt install -y caddy
    start caddy.service
    caddy version

    systemctl daemon-reload
    systemctl enable caddy
    systemctl start caddy
    systemctl status caddy


html이동
    docker cp C:\\Users\\cheolgyu\\go\\github.com\\cheolgyu\\stock-read-pub-site\\dist\\ infallible_bhaskara:/caddy/html/
    cd /caddy/html/dist

캔디실행.
nohup caddy file-server --root /caddy/html/dist --listen :8080 &
caddy start --config /caddy/config/Caddyfile --watch
caddy start
	[--config <path>]
	[--adapter <name>]
	[--envfile <file>]
	[--pidfile <file>]
	[--watch]


# go 설치
curl -O  https://dl.google.com/go/go1.16.5.linux-amd64.tar.gz
rm -rf /usr/local/go && tar -C /usr/local -xzf go1.16.5.linux-amd64.tar.gz
export PATH=$PATH:/usr/local/go/bin


#   s t o c k 
 
 