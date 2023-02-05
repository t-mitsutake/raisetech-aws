# 第四回課題
### AWS上に新しくVPCを作ってください。
![image](https://github.com/t-mitsutake/test/blob/main/gazou/VPC_01.png)  
### EC2とRDS を構築してください
![image](https://github.com/t-mitsutake/test/blob/main/gazou/ec2%E3%82%A4%E3%83%B3%E3%82%B9%E3%82%BF%E3%83%B3%E3%82%B901.png) 
![image](https://github.com/t-mitsutake/test/blob/main/gazou/rds_01.png)
### EC2 から RDS へ接続をし、正常であることを確認して報告してください。
![image](https://github.com/t-mitsutake/test/blob/main/gazou/rds-02.png)

## その他

-----

#### 主な手順
- VPCの作成  
　あらかじめ構成したいサブネットを決めておいて、「サブネット CIDR ブロックをカスタマイズ」で確定するのが後々楽。  
　今回は以下の構成で作成した。

|ip|サブネット名|
| :--- | :--- |
|10.0.1.0/24|tokyo-a-public|
|10.0.2.0/24|tokyo-c-public|
|10.0.11.0/24|tokyo-a-private|
|10.0.12.0/24|tokyo-c-private|

- EC2の作成
 1. sshキーの作成
 2. パブリック IP の自動割り当て  
 3. 外部から遠隔する場合「有効化」
- RDSの作成
 1. RDS用のサブネットグループを作成。
 2. データベースの作成  
　反省点  
  →誤ってストレージ割り当てを200gbに設定してしまったため、料金請求が発生した。  
  RDSで利用がないときは消去するようにしているが、今後は請求書情報の増加に注視する。アラートメールは設定。
 3. 疎通確認  
 　最終的にec2サーバー側から以下コマンドを実施し、ipアドレスを確認したら、想定通りにtokyo-a-privateのセグメントに割り振られていることを確認できた。
 ~~~
 dig [rdsサーバ]
 ~~~
   
   また、セキュリティグループ設定後にポート閉塞を行ったところ、mysqlコマンドで接続できなくなったため、セキュリティグループも問題なく動作していることを確認できた。
- セキュリティグループの作成
1. EC2用  

【インバウンド】  

|ポート|ソース|
| :--- | :--- |
|ssh|自宅のプロバイダip|
|icmp|自宅のプロバイダip|
|icmp|10.0.0.0/16(AWSローカル)|

【アウトバウンド】  

|ポート|デスト|
| :--- | :--- |
|any|any|

2. RDS用  

【インバウンド】  

|ポート|ソース|
| :--- | :--- |
|3306|10.0.1.0/24|
|3306|10.0.2.0/24|

【アウトバウンド】  

|ポート|デスト|
| :--- | :--- |
|any|any|


### 構成図の作成練習
 ![image](https://github.com/t-mitsutake/test/blob/main/gazou/lecture04.png)
