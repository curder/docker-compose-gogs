私有的 GOGS（github-clone）通过HTTPS通过NGINX提供服务
==========================================================

## 依赖环境
* [Docker](https://docs.docker.com/linux/step_one/) (>= 1.10.3)
* [docker-compose](https://docs.docker.com/compose/install/) (>= 1.6.2)

# 构建
升级完成系统依赖环境后，进入到项目目录执行下面的命令：

```
docker-compose up -d
```

## 首次运行
### 使用gogs的自签名证书
在第一次运行期间，nginx容器网络服务器将会提示没有证书可用并将退出。（但`gogs`将继续运行。）

解决这个问题，打开终端并执行（用`docker`打印的容器名称替换（`gogs`））：


```
docker exec -it gogs bash
```

这会将您连接到正在运行的`gogs`容器，并将在其中启动一个bash实例。然后输入（在这里用你的域名再次替换（主机名））：

```
cd /data/gogs/conf/
/app/gogs/gogs cert -ca=true -duration=8760h0m0s -host=(hostname)
exit
```

而已。您现在拥有`gogs`自签名证书。您现在可以重新启动失败的容器：

```
docker-compose up -d
```

### 授权签署的证书
如果您已经签署了证书，只需将它们复制到本地系统中的`/var/gogs/gogs/conf`。他们将在`docker-compose`启动时自动挂载。

请记住重命名证书：`cert.pem`和服务器密钥：`key.pem`。


# 用法
您现在可以使用任何浏览器通过https访问自己托管的`gogs`服务。由于证书是自签名的，因此您会收到警告。

### `git`通过自签名https

要使用自签名的`git`，您需要暂时禁用ssl验证：


```
export GIT_SSL_NO_VERIFY=true
```

或者永久禁用

```
git config --global http.sslverify false
```

### `git` 通过不同的端口SSH（10022）

在不同于默认端口22的端口上使用git和ssh：

```
git clone ssh://git@hostname:10022/user/repo.git
```

记得先添加你的ssh密钥！

# 参考
* [msis/docker-compose-nginx-gogs](https://github.com/msis/docker-compose-nginx-gogs)
* [gogs/docker github](https://github.com/gogits/gogs/tree/master/docker)
* [Unknown blog post](https://unknwon.io/setup-gogs-with-https/)
* [ANAND MANI SANKAR blog post](http://anandmanisankar.com/posts/docker-container-nginx-node-redis-example/)
* [DigitalOcean Tutorial 1](https://www.digitalocean.com/community/tutorials/understanding-the-nginx-configuration-file-structure-and-configuration-contexts)
* [DigitalOcean Tutorial 2](https://www.digitalocean.com/community/tutorials/how-to-run-nginx-in-a-docker-container-on-ubuntu-14-04)
* [stackoverflow question](http://stackoverflow.com/questions/21181231/server-certificate-verification-failed-cafile-etc-ssl-certs-ca-certificates-c)
* [misho gist](https://gist.github.com/micho/1712812)
* [docker compose on networking](https://docs.docker.com/compose/networking/)
* [docker on links](https://docs.docker.com/engine/userguide/networking/default_network/dockerlinks/)
