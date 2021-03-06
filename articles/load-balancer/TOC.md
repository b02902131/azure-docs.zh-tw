# [Load Balancer 文件](index.md)

# 概觀
## [何謂負載平衡器？](load-balancer-overview.md)
## [何謂 Load Balancer 標準？](load-balancer-standard-overview.md)
## [負載平衡器探查](load-balancer-custom-probe-overview.md)
## [高可用性](load-balancer-ha-ports-overview.md)
## [IPv6 支援](load-balancer-ipv6-overview.md)
## [多個前端](load-balancer-multivip-overview.md)
## [輸出連線](load-balancer-outbound-connections.md)
## [標準 Load Balancer 和可用性區域](load-balancer-standard-availability-zones.md)
## [標準 Load Balancer計量和診斷](load-balancer-standard-diagnostics.md)

# 開始使用
## [建立基本 Load Balancer](quickstart-create-basic-load-balancer-portal.md)
### [建立基本 Load Balancer (CLI)](quickstart-create-basic-load-balancer-cli.md)
### [建立基本 Load Balancer (PowerShell)](quickstart-create-basic-load-balancer-powershell.md)
## [建立標準 Load Balancer](quickstart-load-balancer-standard-public-portal.md)
### [建立標準 Load Balancer (CLI)](quickstart-load-balancer-standard-public-cli.md)

# 作法

## [建立區域備援公用標準 Load Balancer](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
### [建立區域備援公用標準 Load Balancer (PowerShell)](load-balancer-get-started-internet-az-powershell.md)
### [建立區域備援公用標準 Load Balancer (CLI)](load-balancer-get-started-internet-az-cli.md)
## [建立區域公用標準 Load Balancer](load-balancer-get-started-internet-availability-zones-zonal-portal.md)
### [建立區域公用標準 Load Balancer (PowerShell)](load-balancer-get-started-internet-availability-zones-zonal-powershell.md)
### [建立區域備援公用標準 Load Balancer (CLI)](load-balancer-get-started-internet-availability-zones-zonal-cli.md)
## [跨越多個可用性區域為 VM 進行負載平衡](load-balancer-standard-public-availability-zones-portal.md)
###  [跨越多個可用性區域為 VM 進行負載平衡 (CLI)](load-balancer-standard-public-zone-redundant-cli.md)
## [在區域內為 VM 進行負載平衡](tutorial-load-balancer-standard-public-zonal-portal.md)  
###  [在區域內為 VM 進行負載平衡 (CLI)](load-balancer-standard-public-zonal-cli.md)   
## [建立基本 Load Balancer (範本)](load-balancer-get-started-internet-arm-template.md)
## [以 IPv6 建立公用 Load Balancer](load-balancer-ipv6-internet-ps.md)
### [以 IPv6 建立公用 Load Balancer (CLI)](load-balancer-ipv6-internet-cli.md)
### [以 IPv6 建立公用 Load Balancer (範本)](load-balancer-ipv6-internet-template.md)
## [設定內部 Load Balancer](load-balancer-get-started-ilb-arm-portal.md)
### [設定內部 Load Balancer (PowerShell)](load-balancer-get-started-ilb-arm-ps.md)
### [設定內部 Load Balancer (CLI)](load-balancer-get-started-ilb-arm-cli.md)
### [設定內部 Load Balancer (範本)](load-balancer-get-started-ilb-arm-template.md)
## [設定負載平衡器的 TCP 閒置逾時](load-balancer-tcp-idle-timeout.md)
## [設定 Load Balancer 的分配模式](load-balancer-distribution-mode.md)
## [合併負載平衡服務](../traffic-manager/traffic-manager-load-balancing-azure.md?toc=%2fazure%2fload-balancer%2ftoc.json)
## [使用多個 IP 組態](load-balancer-multiple-ip.md)
### [使用多個 IP 組態 (CLI)](load-balancer-multiple-ip-cli.md)
### [使用多個 IP 組態 (PowerShell)](load-balancer-multiple-ip-powershell.md)
## [Azure 負載平衡器的 Log Analytics](load-balancer-monitor-log.md)
## [設定內部負載平衡器的高可用性連接埠](load-balancer-configure-ha-ports.md)

## 疑難排解
### [針對 Azure Load Balancer 的問題進行疑難排解](load-balancer-troubleshoot.md)

## 傳統部署模型文章
### [輸出連線 (傳統)](load-balancer-outbound-connections-classic.md)
## [為雲端服務設定多個 IP 位址](load-balancer-multivip.md)
### [設定雲端服務的內部 Load Balancer](load-balancer-get-started-ilb-classic-cloud.md)
#### [設定雲端服務的內部 Load Balancer (PowerShell)](load-balancer-get-started-ilb-classic-ps.md)
#### [設定雲端服務的內部 Load Balancer (CLI)](load-balancer-get-started-ilb-classic-cli.md)
### [設定公用 Load Balancer (傳統 PowerShell)](load-balancer-get-started-internet-classic-ps.md)
#### [設定公用 Load Balancer (傳統雲端)](load-balancer-get-started-internet-classic-cloud.md)
#### [設定公用 Load Balancer (傳統 CLI)](load-balancer-get-started-internet-classic-cli.md)

# 參考
## [程式碼範例](https://azure.microsoft.com/en-us/resources/samples/?service=load-balancer)
## [Azure PowerShell](/powershell/module/azurerm.network)
## [Azure CLI](/cli/azure/network/lb)
## [.NET](/dotnet/api/microsoft.azure.management.network.models)
## [Java](/java/api/com.microsoft.azure.management.network)
## [Node.js](http://azure.github.io/azure-sdk-for-node/azure-arm-network/latest/LoadBalancers.html)
## [Ruby](http://www.rubydoc.info/gems/azure_mgmt_network/Azure/ARM/Network/LoadBalancers)
## [Python](http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.mgmt.network.operations.html#azure.mgmt.network.operations.LoadBalancersOperations)
## [REST](https://msdn.microsoft.com/library/azure/mt163651.aspx)

# 相關參考
## [應用程式閘道](/azure/application-gateway/)
## [ExpressRoute](/azure/expressroute/)
## [虛擬網路](/azure/virtual-network/)
## [VPN 閘道](/azure/vpn-gateway/)
## [虛擬機器](/azure/virtual-machines/)
## [流量管理員](/azure/traffic-manager/)
## [DNS](/azure/dns/)

# 資源
## [Azure 藍圖](https://azure.microsoft.com/roadmap/?category=networking)
## [價格](https://azure.microsoft.com/pricing/details/load-balancer/)
## [定價計算機](https://azure.microsoft.com/pricing/calculator/)
## [服務更新](https://azure.microsoft.com/updates/?product=load-balancer)
