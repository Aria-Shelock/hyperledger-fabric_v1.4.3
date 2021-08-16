# hyperledger_v1.4.3
操作系统：Ubuntu 20.04.2.0
Go：1.11.11
Docker：20.10.7
Docker Compose：1.25.0
Hyperledger Fabric：1.4.3

## 下载VMware，配置Ubuntu系统
Ubuntu镜像：链接：https://pan.baidu.com/s/1eKKctAO75bRtLz3B6ORMfA 
            提取码：z8i9
## 换国内镜像源
系统设置->软件和更新下载自->其他-> "mirrors.aliyun.com"
完成后打开终端
$ sudo apt update

## 设置root密码
$ sudo passwd root

## 基础工具安装
$ sudo apt install git
$ sudo apt install curl
$ sudo apt install vim

## 安装Docker（这一步操作会比较慢）
$ sudo apt install docker.io
查看版本
$ docker --version
Docker version 20.10.7, build 20.10.7-0ubuntu1~20.04.1
设置成非 root 用户也能执行 docker
$ sudo usermod -aG docker 你的用户名 
重启

## 安装Docker-Compose
$ sudo apt install docker-compose
查看版本
$ docker-compose --version
docker-compose version 1.25.0, build unknown
允许其他用户执行 compose 相关命令
$ sudo chmod +x /usr/share/doc/docker-compose
重启

## Go语言安装及环境配置
1.实机下载，将压缩包拖动至虚拟机内
https://studygolang.com/dl/golang/go1.11.11.linux-amd64.tar.gz
在压缩包所在文件夹打开终端
$ sudo tar -zxvf go1.11.11.linux-amd64.tar.gz -C /usr/local/
2.配置环境变量
$ sudo gedit /etc/profile
在 profile 文件最后添加如下内容
export GOROOT=/usr/local/go
export GOPATH=$HOME/go
export PATH=$PATH:$GOROOT/bin:$GOPATH/bin
保存后在终端输入
$ source /etc/profile
3.使用 go version 命令验证是否安装成功
$ go version
go version go1.11.11 linux/amd64
重启

## 拉取Fabric源码（建议先在windows环境下拉取后放在虚拟机中）
$ mkdir -p ~/go/src/github.com/hyperledger 
$ cd ~/go/src/github.com/hyperledger 
拉取fabric的源码（很慢，有科学上网会快点）
$ git clone https://github.com/hyperledger/fabric.git https://gitee.com/mirrors/hyperledger-fabric.git
查看并切换当前分支
$ cd ./fabric
$ git branch -a  
$ git checkout v1.4.3  

## 拉取fabric-samples
$ sudo mkdir -p /etc/docker
1.配置镜像加速器
https://cr.console.aliyun.com/cn-hangzhou/instances/mirrors
替换自己的加速器地址
$ sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://eck51glv.mirror.aliyuncs.com"]
}
EOF
$ sudo systemctl daemon-reload
$ sudo systemctl restart docker
2.下载fabric-samples源码（建议先在windows环境下拉取后放在虚拟机中）
$ cd ~/go/src/github.com/hyperledger/fabric/scripts
$ git clone https://github.com/hyperledger/fabric-samples.git
https://gitee.com/giteemirror/fabric-samples.git（这一步可能会多次卡住，用ctrl+c退出后重新输入代码即可）
$ cd ./fabric-samples
$ git branch -a
$ git checkout v1.4.3
3.下载二进制文件
链接：https://pan.baidu.com/s/1sqeIRXZlmpKVKwGBO_O67w 
提取码：9rvz
下载的hyperledger-fabric-linux-amd64-1.4.3.tar.gz内有bin和config两个文件夹，hyperledger-fabric-ca-linux-amd64-1.4.3.tar.gz内有bin文件夹，将两个bin文件夹内的二进制文件汇总在一个bin文件夹内。最后将bin和config文件夹复制到fabric-samples文件夹内
4.下载Docker镜像
$ cd ~/go/src/github.com/hyperledger/fabric/scripts
修改脚本
$ sudo gedit bootstrap.sh
删除文档最后的samplesInstall和binariesInstall步骤，只剩下
if [ "$DOCKER" == "true" ]; then
  echo
  echo "Installing Hyperledger Fabric docker images"
  echo
  dockerInstall
fi
执行 bootstrap.sh 脚本
$ ./bootstrap.sh 1.4.3 1.4.3 0.4.15
完成后会出现
===> List out hyperledger docker images
hyperledger/fabric-tools       1.4.3               18ed4db0cd57        7 weeks ago         1.55GB
hyperledger/fabric-tools       latest              18ed4db0cd57        7 weeks ago         1.55GB
hyperledger/fabric-ca          1.4.3               c18a0d3cc958        7 weeks ago         253MB
hyperledger/fabric-ca          latest              c18a0d3cc958        7 weeks ago         253MB
hyperledger/fabric-ccenv       1.4.3               3d31661a812a        7 weeks ago         1.45GB
hyperledger/fabric-ccenv       latest              3d31661a812a        7 weeks ago         1.45GB
hyperledger/fabric-orderer     1.4.3               b666a6ebbe09        7 weeks ago         173MB
hyperledger/fabric-orderer     latest              b666a6ebbe09        7 weeks ago         173MB
hyperledger/fabric-peer        1.4.3               fa87ccaed0ef        7 weeks ago         179MB
hyperledger/fabric-peer        latest              fa87ccaed0ef        7 weeks ago         179MB
hyperledger/fabric-javaenv     1.4.3               5ba5ba09db8f        2 months ago        1.76GB
hyperledger/fabric-javaenv     latest              5ba5ba09db8f        2 months ago        1.76GB
hyperledger/fabric-zookeeper   0.4.15              20c6045930c8        7 months ago        1.43GB
hyperledger/fabric-zookeeper   latest              20c6045930c8        7 months ago        1.43GB
hyperledger/fabric-kafka       0.4.15              b4ab82bbaf2f        7 months ago        1.44GB
hyperledger/fabric-kafka       latest              b4ab82bbaf2f        7 months ago        1.44GB
hyperledger/fabric-couchdb     0.4.15              8de128a55539        7 months ago        1.5GB
hyperledger/fabric-couchdb     latest              8de128a55539        7 months ago        1.5GB
5.设置环境变量
$ sudo gedit /etc/profile
在文件最后添加
export PATH=$PATH:$GOROOT/bin:$GOPATH/bin:$HOME/go/src/github.com/hyperledger/fabric/scripts/fabric-samples/bin
保存后在终端输入
$ source /etc/profile
检验环境变量是否成功
$ fabric-ca-client version
fabric-ca-client:
Version: 1.4.3
Go version: go1.11.5
OS/Arch: linux/amd64
重启

## 测试网络
$ cd ~/go/src/github.com/hyperledger/fabric/scripts/fabric-samples/first-network/
$ ./byfn.sh generate
$ ./byfn.sh up

## 测试中错误[已知1处]
Error：After 10 attempts,peer0.org1 has failed to join channel ‘mychannel’
Solution：
$ sudo gedit /etc/resolv.conf
注释掉以option开头的命令行，如
#options timeout:2 attempts:3 rotate single-request-reopen

## 启动网络
$ ./byfn.sh generate
$ ./byfn.sh up
$ docker exec -it cli bash
设置通道和证书路径环境变量
$ export CHANNEL_NAME=mychannel
$ export ORDERER_CA=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem
查询
$ peer chaincode query -C $CHANNEL_NAME -n mycc -c '{"Args":["query","a"]}'
-n： 指定要调用的链码名称
-C： 指定通道名称
-c 指定调用链码时所需要的参数
转账
$ peer chaincode invoke -o orderer.example.com:7050 --tls --cafile $ORDERER_CA -C $CHANNEL_NAME -n mycc --peerAddresses peer0.org1.example.com:7051 --tlsRootCertFiles /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt --peerAddresses peer0.org2.example.com:9051 --tlsRootCertFiles /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt -c '{"Args":["invoke","a","b","10"]}'
-o： 指定orderer节点地址
--tls： 开启TLS验证
--cafile： 指定了 Orderer 的根证书路径，用于验证 TLS 握手
-n: 指定链码名称
-C： 指定通道名称
-c： 指定调用链码的所需参数
查看链码日志
$ docker ps
$ docker logs <IMAGE>
关闭网络
$ ./byfn.sh down

## dev模式测试
启动网络
$ cd ./fabric-samples/chaincode-docker-devmode/
$ docker-compose -f docker-compose-simple.yaml up -d
进入网络
$ docker exec -it chaincode bash
$ cd chaincode_example02/go/
$ go build
$ CORE_PEER_ADDRESS=peer:7052 CORE_CHAINCODE_ID_NAME=mycc:0 ./go
新开终端
$ docker exec -it cli bash
$ peer chaincode install -p chaincodedev/chaincode/chaincode_example02/go -n mycc -v 0
$ peer chaincode instantiate -n mycc -v 0 -c '{"Args":["init","a", "100", "b","200"]}' -C myc
测试
$ peer chaincode query -n mycc -c '{"Args":["query","a"]}' -C myc
$ peer chaincode invoke -n mycc -c '{"Args":["invoke","a","b","10"]}' -C myc
$ peer chaincode query -n mycc -c '{"Args":["query","a"]}' -C myc
关闭dev网络
$ docker-compose -f docker-compose-simple.yaml down
