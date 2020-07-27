# Azure-cli
AzureのPaaS作成やIaaS作成をAnsibleで自動化のモック。  
こちらも以前プライベートで作成した環境のパラメータを持ってきているのでパラメータは参考程度に。。。   

##  初期設定
### python
python3.8はtime moduleの仕様変更で動かないため
python3.7以下をインストールするか  
指定できる環境を準備すること。

### az
azureのazコマンドが使用できることかつ  
az loginでazureにlogin済みであること  
(参考:https://docs.microsoft.com/ja-jp/cli/azure/install-azure-cli?view=azure-cli-latest)

### ansible
ローカルにansibleが必要。動作確認はansible 2.9.7で実施しています。
インストール方法は環境によりますので各自ググってください。  
(macはbrew install ansibleで入ります。)

## 使い方

### Create or modify NSG
NSGを作成、既にある場合は変更を行う。
リソースグループがない場合は自動作成される。
Roleの中に各環境ごとの設定があるので環境に合わせて名前とパラメータを
書き換えて使用してください。

    ansible-playbook create_nsg.yml -e resource_group={{リソースグループ名}} (-e ansible_python_interpreter=python3.7)

### Create or modify VM
VMを作成。それに伴い必要なdiskとnicを作成。  
モックなのでpassword直書きのため本番は環境変数やansibleのハッシュで隠蔽することを推奨します。  
vNet及び、対象subnetの作成が必要。必要パラメータは./local_varsにまとめているので
./local_vars/{{リソースグループ名}}/{{vm名}}.ymlのパラメータを設定。

#### local IP のみVM用
    ansible-playbook create_vm.yml -e resource_group={{リソースグループ名}} -e vm=mansp01 (-e ansible_python_interpreter=python3.7)
※python3.8はtime moduleの仕様変更で動かない  
#### public IP付きVM用
    ansible-playbook create_public_vm.yml -e resource_group=productionDmz -e vm=profk01 -e ansible_python_interpreter=python3.7

#### 補足:imageのlist確認  
    az vm image list --offer UbuntuServer --all --output table

### Create or modify Storage
AzureのStorage Accountを作成。  

    ansible-playbook create_storage.yml -e resource_group=xxxxxx (-e ansible_python_interpreter=python3.7) 
※python3.8はtime moduleの仕様変更で動かない  