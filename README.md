# AmazonLinuxのDockerイメージの作り方  

[参考](http://dev.classmethod.jp/cloud/aws/docker-serverspec-configspec-ci/)  


作成したAwsLinuxのディスクをマウントします
---
**マウントしたい/devのファイル名が変わる場合がある**  
間違ったのマウントしようとすると下記のようにエラーになる  
mount: /dev/xvdf is write-protected, mounting read-only  

`$ sudo mkdir /mnt/data`

何故かsdfからsdf1になってる。事前にSSHしてtouch /tmp/aしたファイルが残ってるので間違いない  
`$ sudo mount /dev/sdf1 /mnt/data`  
`$ sudo df -h`  

Docker Imageにするには不要な情報がいくつかありますので、削除しました。  
/var/log/* (全てのファイルを編集し0byteに)  

`$ cat /dev/null > audit/audit.log`  
`$ cat /dev/null > boot.log`  
`$ cat /dev/null > btmp`  
`$ cat /dev/null > cloud-init.log`  
`$ cat /dev/null > cron`  
`$ cat /dev/null > dmesg`  
`$ cat /dev/null > dracut.log`  
`$ cat /dev/null > lastlog`  
`$ cat /dev/null > mail/*`  
`$ cat /dev/null > maillog`  
`$ cat /dev/null > messages`  
`$ cat /dev/null > ntpstats/*`  
`$ cat /dev/null > secure`  
`$ cat /dev/null > spooler`  
`$ cat /dev/null > tallylog`  
`$ cat /dev/null > wtmp`  
`$ cat /dev/null > yum.log`  

`$ rm -fr var/cache/yum/*`  
`$ rm -fr home/ec2-user/.ssh/authorized_keys`  


その後、以下コマンドでimageとしてimportします。
---
#### イメージ名としては「ユーザー名/レポジトリ名」という形式が推奨されています  
`sudo tar --numeric-owner -c . | sudo docker import - k1row/amazonlinux-ja`  

AttachしたEBS Volumeはumountの上Dettachしておきます。  

`$ docker build -t k1row/amazonlinux-ja .`  
`$ docker ps -a`  
`$ docker commit -m "AmazonLinuxAMI" 12811a032554 k1row/amazonlinux-ja`  
