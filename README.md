# NVIDIA-Base-Command-Manager-Install-Guide
## Base command簡易說明    

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

#### 正式安裝 

可以看到三個連結  
其中Base view 是給管理員使用  
User Portal 是給使用者使用  
Ceph Dashboard讓BCM 提供 ceph object storage使用  
這邊選擇Base view  
圖片1  

預設帳號為`root`  
密碼為之前所設定之IP  
登入之後可以看到基礎管理畫面  
![image](https://github.com/ReSin-Yan/NVIDIA-Base-Command-Manager-Install-Guide/assets/22570422/55b71c0a-408c-4138-bc56-185c36990a35)



#### 設定允許webhook   
`Gitlab` > `Menu` > `Admin`  
左下角`Setting` > `Network` > `Outbound Request` > 將兩個都勾起  
 ``  

![img](https://github.com/ReSin-Yan/Kubernetes-Opensource-Project/blob/main/CICD/Jenkins/cicd/input%20token.PNG)   




![img](https://github.com/ReSin-Yan/Kubernetes-Opensource-Project/blob/main/CICD/Jenkins/cicd/addnode2.PNG)   



