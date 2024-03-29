# NVIDIA-Base-Command-Manager-Install-Guide
## Base command簡易說明    

Base command  
主要挑戰  
> 人工智慧投資的投資報酬率實現起來可能很緩慢。  
> 管理和擴展人工智慧基礎設施可能具有挑戰性。  
> 可用硬體常常超額認購且使用率不足。   
> 缺乏企業級支援。  

好處  
> 透過用於加速資料中心的全範圍強化軟體來加快投資報酬率。  
> 管理任意規模的集群，涵蓋 DGX 系統、其他 GPU、CPU 和雲端節點。  
> 透過企業級作業排程和工作負載編排以及整合監控來最大限度地提高利用率。  
> Base Command 由世界一流的 NVIDIA 企業支援的支援。  

Base command Platfrom 是一個SaaS  
主要目的是將DGX 連結起來(其中會用到mutiple node的功能，所以其中的網路必須要用到IB才可以)  
他可以在NGC上面直接控管購買的DGX 平台  
可以將服務直接在上面進行執行選擇GPU的數量  
其中包含了大量功能，監控、排成管理、RBAC、整合NGC目錄、常用的jupyternotebook  

Base command manager 是一套地端服務(需要安裝)，可以支持從單一節點到多節點的規劃面向，符合SLA規範  


## 環境準備以及說明  

#### 需要準備之項目   

在測試環境中，使用四台虛擬機進行建置(實際環境中，可以是實體機)  
以下是一些準備項目以及其說明  
 | 準備項目 | 規格 | 說明 | 
|-------|-------|-------| 
| HeadNode *1 | min, 4GB RAM, 80GB DISK, NIC*2 | 此節點作為head節點，會通過PXE BOOT的方式將RAM DISK部屬出去，所有的內容都會放在head，包含ROUTE |
|  | Production, 24cores 128GB RAM, 512GB DISK |  |
| ComputeNode | min, 4GB RAM, 80GB DISK | 此節點作為運算節點，如果環境中具備GPU的節點建議建立成Compute節點 |
| Lincese | [申請](https://customer.brightcomputing.com/Customer-Login) | 申請給出的授權總共為8node |

Head可以準備多台搭建成HA環境  
測試環境採用TYPE1的網路架構，並且在HEAD配置兩張網卡  

## 正式安裝             
#### 申請授權,ISO下載  

建立帳號  
[網址](https://customer.brightcomputing.com/Customer-Login)  
![image](https://github.com/ReSin-Yan/NVIDIA-Base-Command-Manager-Install-Guide/assets/22570422/a47d54e7-17d3-476c-a721-406f1be37829)
拿到授權之後下載  
選擇自己熟悉的linux版本  
[下載](https://customer.brightcomputing.com/Download)
![image](https://github.com/ReSin-Yan/NVIDIA-Base-Command-Manager-Install-Guide/assets/22570422/87de1ba2-9c94-4261-979f-ae7a595d5fe1)



#### 正式安裝  
將ISO掛給虛擬機    
![img](https://github.com/ReSin-Yan/NVIDIA-Base-Command-Manager-Install-Guide/blob/main/img/bcm%20(1).png)   


選擇圖形安裝(建議選項)  
安裝種類分為四種，其中圖形安裝是比較建議的方式  
![img](https://github.com/ReSin-Yan/NVIDIA-Base-Command-Manager-Install-Guide/blob/main/img/bcm%20(2).png)   

選擇開始安裝   


同意NVIDIA EULA  
![img](https://github.com/ReSin-Yan/NVIDIA-Base-Command-Manager-Install-Guide/blob/main/img/bcm%20(3).png)   


同意ubuntu授權  
![img](https://github.com/ReSin-Yan/NVIDIA-Base-Command-Manager-Install-Guide/blob/main/img/bcm%20(4).png)   


Kerenl可以選擇安裝後再入的模組  
這邊選擇預設即可  
![img](https://github.com/ReSin-Yan/NVIDIA-Base-Command-Manager-Install-Guide/blob/main/img/bcm%20(5).png)   

接下來可以看到硬體的資訊  
![img](https://github.com/ReSin-Yan/NVIDIA-Base-Command-Manager-Install-Guide/blob/main/img/bcm%20(6).png)   

選擇有掛階ISO的磁區  
建議可以選擇check  
![img](https://github.com/ReSin-Yan/NVIDIA-Base-Command-Manager-Install-Guide/blob/main/img/bcm%20(7).png)   

接下來設定此head的基本資訊  
以下皆為測試環境資訊
 | 設定 |  |
|-------|-------|
| clusterName | BCMCluster |
| Organization name | resinlab |
| Administrator email | a127504443@gmail.com |
| Time zone | +8:00 Asia/Taipei |
| Time servers | default |
| Nameserver | 8.8.8.8 |
| Search domains | None |
| environment modeules | Tcl moduels |
![img](https://github.com/ReSin-Yan/NVIDIA-Base-Command-Manager-Install-Guide/blob/main/img/bcm%20(8).png)   

HPC workload目前支援三種格式  
slurm,ibm spectrum lsf, Unuva Grid Engine  
選擇slurm  
其餘兩種皆需要額外的授權  
IBM spectrun LSF專門為高性能排程而設計  
Univa Grid Engine更為適合在混合雲環境中  
![img](https://github.com/ReSin-Yan/NVIDIA-Base-Command-Manager-Install-Guide/blob/main/img/bcm%20(9).png)   

網路拓樸圖根據自身網路狀況做選取  
通常情況下為1或是2  
更加建議的選擇為1，網路之間的連線可以錯開  
並且computenode的連線方式都會通過route的方式連線出去  
![img](https://github.com/ReSin-Yan/NVIDIA-Base-Command-Manager-Install-Guide/blob/main/img/bcm%20(10).png)   

設定Headnode基本設定 
包含hostname跟密碼，預設帳號為`root`  
Hardward manufacturer，如果headnode裝在實體機上面選擇對應的廠商，沒有就選擇其他  
![img](https://github.com/ReSin-Yan/NVIDIA-Base-Command-Manager-Install-Guide/blob/main/img/bcm%20(11).png)   

設定Conmpute基本設定 
包含Racks數量、node數量、nodestart數量、node基本名稱設定    
此設定可以在之後更改，這邊並不會直接執行部屬    
![img](https://github.com/ReSin-Yan/NVIDIA-Base-Command-Manager-Install-Guide/blob/main/img/bcm%20(12).png)   

BMC基本設定 
如果底層是實體機，那麼應該就會有如IPMI,iDRAC,iLO,CIMI這一種腳色  
測試環境使用虛擬機，所以兩個都選擇NO  
![img](https://github.com/ReSin-Yan/NVIDIA-Base-Command-Manager-Install-Guide/blob/main/img/bcm%20(13).png)   

網路設定 
由於我們選的是type1  
head跟compute之間的連線是通過internalnet，所以其實在本測試環境中externalnet只會配置給headnode  
internalnet配置給head跟compute，其中internalnet會自動配置DHCP，所以需要給定範圍  
本測試環境中internal會另外拉一個portgroup出來，所以不需要理會IP是否實際可以連線  
在同一個portgroup中可以連線即可  
![img](https://github.com/ReSin-Yan/NVIDIA-Base-Command-Manager-Install-Guide/blob/main/img/bcm%20(14).png)   
![img](https://github.com/ReSin-Yan/NVIDIA-Base-Command-Manager-Install-Guide/blob/main/img/bcm%20(15).png)   

網路設定(續)   
設定headnode網路分別輸入兩張網卡的interface，以及network，ipaddress  
computenode選擇internalnet(假的那段)  
![img](https://github.com/ReSin-Yan/NVIDIA-Base-Command-Manager-Install-Guide/blob/main/img/bcm%20(16).png)   
![img](https://github.com/ReSin-Yan/NVIDIA-Base-Command-Manager-Install-Guide/blob/main/img/bcm%20(17).png)   

DISK設定layout   
選擇安裝的硬體區    
![img](https://github.com/ReSin-Yan/NVIDIA-Base-Command-Manager-Install-Guide/blob/main/img/bcm%20(18).png)   

DISK設定layout   
這邊可以定義head跟compute部屬之後DISK的行為  
都可以在之後進行修改  
測試環境都選one big partion  
![img](https://github.com/ReSin-Yan/NVIDIA-Base-Command-Manager-Install-Guide/blob/main/img/bcm%20(19).png)   

確定完所有資訊之後點選開始    
![img](https://github.com/ReSin-Yan/NVIDIA-Base-Command-Manager-Install-Guide/blob/main/img/bcm%20(20).png)   

```

```

#### 授權、基本登入設定   

可以看到三個連結  
其中Base view 是給管理員使用  
User Portal 是給使用者使用  
Ceph Dashboard讓BCM 提供 ceph object storage使用  
這邊選擇Base view  
![image](https://github.com/ReSin-Yan/NVIDIA-Base-Command-Manager-Install-Guide/assets/22570422/6bb1db9c-6eef-4e34-9ba7-f650e1251beb)


預設帳號為`root`  
密碼為之前所設定之IP  
登入之後可以看到基礎管理畫面  
![image](https://github.com/ReSin-Yan/NVIDIA-Base-Command-Manager-Install-Guide/assets/22570422/55b71c0a-408c-4138-bc56-185c36990a35)


BCM基本安裝完成，接下來把授權輸入  
使用SSH登入BCM  
預設帳號為`root`  
密碼為之前所設定之IP  
![image](https://github.com/ReSin-Yan/NVIDIA-Base-Command-Manager-Install-Guide/assets/22570422/ec8924a8-0f94-4282-9129-650f8296358e)  
```
request-license  
```

參數參考  
大寫參數為預設  
 | 設定 |  |
|-------|-------|
| Product Key | [Your License Key] |
| Country Name | TW |
| State or Province Name | Taipei |
| Locality Name | Taipei |
| Organization Name | BCMLab |
| Organizational Unit Name | BCMLab |
| Cluster Name | bcm01 |
| MAC Address of primary head node | [Mac Address] |
| HA | N,if you set ha for Y |
| Submit certificate request | Y,if not gloabl net for N |
| Install license | Y |

其中最為重要的是MAC，BCM會去註冊授權，並且綁定MAC  
所以選擇可以對外的網卡的MAC，並且建議記下此資料  
如果需要重複練習，此MAC需要重複使用  
![image](https://github.com/ReSin-Yan/NVIDIA-Base-Command-Manager-Install-Guide/assets/22570422/ce265ff6-a557-4556-817c-6e4daa36de14)  


其中Country Name,State or Province Name,Locality Name,Organization Name,Organizational Unit Name 這幾個參數為產生憑證所需要的資料  
![image](https://github.com/ReSin-Yan/NVIDIA-Base-Command-Manager-Install-Guide/assets/22570422/3ae91c0c-4477-4d76-9bcf-85cd9252e9c0)

## BCM Lab   

#### 部屬compute node   
接來會簡單展示BCM的部屬服務  
包含可以通過head快速更新computenode的方式  
並且會以此環境建立出k8s平台  

大部分操作可以用指令`cmsh`來進行配置   
cmsh可以跟大部分得指令相容，也可以寫成bash腳本來大量控制  

快速指令腳本  
快速指令腳本也可以自定義  
```
cmsh
[bcm10-headnode]% alias
alias - goto -
alias .. exit
alias / home
alias ? help
alias ds device status
alias ls list
```

測試環境中準備三台虛擬機  
在device中複製三台虛擬機  
```
device
clone node001 node002
..
clone node001 node003
..
commit
ls
```
可以看到總共有兩台node被我們創建出來  
![image](https://github.com/ReSin-Yan/NVIDIA-Base-Command-Manager-Install-Guide/assets/22570422/5c274979-af36-412a-b53b-a72fc82dc155)

開啟三台虛擬機的電源  
注意需要放在同一段網段上  
打開虛擬機的web console  
過一段秒數會自動選擇  
![image](https://github.com/ReSin-Yan/NVIDIA-Base-Command-Manager-Install-Guide/assets/22570422/5eedd27d-9f88-482f-9c6f-da20588f2bac)

接下來會跳到node選擇中  
這邊由於還沒跟BCM註冊，所以會重複多次的執行(每9秒)  
我們需要手動選擇Manaully select node  
之後選擇對應的節點  
以下是一些執行執行的範例圖  
![image](https://github.com/ReSin-Yan/NVIDIA-Base-Command-Manager-Install-Guide/assets/22570422/145286be-380c-40c4-8f1c-827d8345009e) 
![image](https://github.com/ReSin-Yan/NVIDIA-Base-Command-Manager-Install-Guide/assets/22570422/9c4ce949-9b33-4451-a7a8-e88ccbd1495c)
![image](https://github.com/ReSin-Yan/NVIDIA-Base-Command-Manager-Install-Guide/assets/22570422/f695b5a8-4b74-42a7-9f6b-3999a55a8e50)
![image](https://github.com/ReSin-Yan/NVIDIA-Base-Command-Manager-Install-Guide/assets/22570422/09205406-cf0f-448a-bdfe-6945260014c5)
![image](https://github.com/ReSin-Yan/NVIDIA-Base-Command-Manager-Install-Guide/assets/22570422/5a27813d-64bf-47ef-b1e0-480a471a74a0)
![image](https://github.com/ReSin-Yan/NVIDIA-Base-Command-Manager-Install-Guide/assets/22570422/073e075d-dcc9-4a55-a23b-e562d40e9548)

等待時間  
Headnode會將ramdisk放入虛擬機中  
並且依照內容進行開機  
![image](https://github.com/ReSin-Yan/NVIDIA-Base-Command-Manager-Install-Guide/assets/22570422/c22d33fa-7c45-4791-aa07-2c0d259c56eb)
![image](https://github.com/ReSin-Yan/NVIDIA-Base-Command-Manager-Install-Guide/assets/22570422/fa4718d5-eeeb-4837-8d32-69b548f0199d)  


#### 建立新的softwareimages綁定category並且部屬至computenode  

接下來測試headnode進行更新的功能  
創建一個softwareimages，並且針對此software進行一些設計  
然後將此softwareimage綁定到category上  
然後將computenode1 computenode2配置此categroy  

先在cmsh中複製images  
```
cmsh
softwareimage
ls
clone default-image prod-image
commit
```
![image](https://github.com/ReSin-Yan/NVIDIA-Base-Command-Manager-Install-Guide/assets/22570422/3c8f140f-961d-40e3-a756-b29fd435c1dd)  


先確定是否是root@bcm10-headnode user  
有可能因為操作卡在cmsh上，需要先行退出  
嘗試隨意安裝一個套件(這邊安裝helm)  
並且隨意新增一個檔案  
```
cm-chroot-sw-img /cm/images/prod-image/
apt-get update
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh

vim demo.txt
#輸入任意文字之後離開

exit
```

使用cmsh  
配置catogroy  

```
cmsh
category
clone default prod
set softwareimage prod-image
commit
ls
```
![image](https://github.com/ReSin-Yan/NVIDIA-Base-Command-Manager-Install-Guide/assets/22570422/da36616a-a6f2-409b-ab93-50c685c2b47a)

配置computenode  
```
cmsh
device
set node001 category prod
set node002 category prod
commit
ls
reboot node001
reboot node002
```
![image](https://github.com/ReSin-Yan/NVIDIA-Base-Command-Manager-Install-Guide/assets/22570422/40599a20-beee-4054-b468-f1a3b01b6685)

等待全部更新完畢之後  
分別ssh登入這三node，確認功能是否更新  

```
ssh node002
cat /demo.txt
helm version
exit 
```
![image](https://github.com/ReSin-Yan/NVIDIA-Base-Command-Manager-Install-Guide/assets/22570422/30e6286e-b803-4577-9443-47609d1f3754)

```
ssh node003
cat /demo.txt
helm version
```
![image](https://github.com/ReSin-Yan/NVIDIA-Base-Command-Manager-Install-Guide/assets/22570422/cee5887c-4ec7-42e9-a258-aaa73d93529a)


#### 部屬kubernetes  
點選Container> Kubernetes>kubernetes Wizard  
![image](https://github.com/ReSin-Yan/NVIDIA-Base-Command-Manager-Install-Guide/assets/22570422/02fc4213-39f9-4299-ac6c-c0ca6390de2a)

cluster Setting保持基本預設即可  
![image](https://github.com/ReSin-Yan/NVIDIA-Base-Command-Manager-Install-Guide/assets/22570422/1137d089-389f-4179-b7a4-c72213ad7685)

網路設定選擇internalnet(computenode的網段)  
![image](https://github.com/ReSin-Yan/NVIDIA-Base-Command-Manager-Install-Guide/assets/22570422/135b0aab-3156-49f4-9211-9d3016e40b61)

選擇節點腳色(可以單獨選擇節點或是選擇catogroy)  
![image](https://github.com/ReSin-Yan/NVIDIA-Base-Command-Manager-Install-Guide/assets/22570422/ce0b194b-4d9c-42a3-b234-cdea9d5dcd80)

選擇SC(可以後續再新增)  
![image](https://github.com/ReSin-Yan/NVIDIA-Base-Command-Manager-Install-Guide/assets/22570422/32246219-055a-4a91-8ec6-800d1a75b5e0)

選擇要安裝的套件(必須要安裝的時候選擇，無法再從base view新增)  
測試環境沒有GPU，所以GPU相關套件功能都未開啟  
![image](https://github.com/ReSin-Yan/NVIDIA-Base-Command-Manager-Install-Guide/assets/22570422/fd104ec8-b40c-4777-8fa3-11b99e5ab995) 

選擇addons(必須安裝的時候選擇，無法再從base view新增)  
測試環境主要為了測試安裝過程，並沒有詳細把所有功能更on起來  
所以單純選擇一些比較用的到的功能  
![image](https://github.com/ReSin-Yan/NVIDIA-Base-Command-Manager-Install-Guide/assets/22570422/d374b05f-3fe2-4287-b382-c64754fe7b0d)

勾選Ready to deployment  
開始部屬  
![image](https://github.com/ReSin-Yan/NVIDIA-Base-Command-Manager-Install-Guide/assets/22570422/86d722a8-9f44-4d90-9d90-92346d9a7680)

等待部屬完畢  
![image](https://github.com/ReSin-Yan/NVIDIA-Base-Command-Manager-Install-Guide/assets/22570422/138e147a-f95c-48d6-af66-08b471ba3e4e)


```
export KUBECONFIG=/root/.kube/config-default
kubectl get pod -A
```
![image](https://github.com/ReSin-Yan/NVIDIA-Base-Command-Manager-Install-Guide/assets/22570422/37549e88-971a-4e9e-ae02-e866254526bf)
