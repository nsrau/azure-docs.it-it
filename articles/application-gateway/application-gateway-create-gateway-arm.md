---
title: Creare, avviare o eliminare un gateway applicazione tramite Azure Resource Manager | Documentazione Microsoft
description: Questa pagina fornisce istruzioni per la creazione, la configurazione, l&quot;avvio e l&quot;eliminazione di un gateway applicazione di Azure usando Gestione risorse di Azure
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: tysonn
ms.assetid: 866e9b5f-0222-4b6a-a95f-77bc3d31d17b
ms.service: application-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/12/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: cb2b7bc626294e12c6e19647c1e787e1f671595b
ms.openlocfilehash: 5da4b087131b0adef49f7019297db834d7bb9416


---
# <a name="create-start-or-delete-an-application-gateway-by-using-azure-resource-manager"></a>Creare, avviare o eliminare un gateway applicazione tramite Gestione risorse di Azure

> [!div class="op_single_selector"]
> * [Portale di Azure](application-gateway-create-gateway-portal.md)
> * [PowerShell per Azure Resource Manager](application-gateway-create-gateway-arm.md)
> * [PowerShell per Azure classico](application-gateway-create-gateway.md)
> * [Modello di Azure Resource Manager](application-gateway-create-gateway-arm-template.md)
> * [Interfaccia della riga di comando di Azure](application-gateway-create-gateway-cli.md)

Il gateway applicazione di Azure è un dispositivo di bilanciamento del carico di livello 7. Fornisce richieste HTTP con routing delle prestazioni e failover tra server diversi, sia nel cloud che in locale. Il gateway applicazione offre numerose funzionalità di controller per la distribuzione di applicazioni (ADC, Application Delivery Controller), tra cui bilanciamento del carico HTTP, affinità di sessione basata su cookie, offload SSL (Secure Sockets Layer), probe di integrità personalizzati, supporto per più siti e molte altre. Per un elenco completo delle funzionalità supportate, vedere [Panoramica del gateway applicazione](application-gateway-introduction.md)

Questo articolo illustra in dettaglio i passaggi necessari per creare e configurare, avviare ed eliminare un gateway applicazione.

> [!IMPORTANT]
> Prima di iniziare a usare le risorse di Azure, è importante comprendere che Azure al momento offre due modelli di distribuzione, quello classico e Gestione risorse. È importante conoscere i [modelli e gli strumenti di distribuzione](../azure-classic-rm.md) prima di usare le risorse di Azure. È possibile visualizzare la documentazione relativa a diversi strumenti facendo clic sulle schede nella parte superiore di questo articolo. Questo documento illustra la creazione di un gateway applicazione con Azure Resource Manager. Per usare la versione classica, passare a [Creare, avviare o eliminare un gateway applicazione](application-gateway-create-gateway.md).

## <a name="before-you-begin"></a>Prima di iniziare

1. Installare la versione più recente dei cmdlet di Azure PowerShell usando l'Installazione guidata piattaforma Web. È possibile scaricare e installare la versione più recente dalla sezione **Windows PowerShell** della [pagina Download](https://azure.microsoft.com/downloads/).
2. Se è già disponibile una rete virtuale, selezionare una subnet vuota esistente oppure creare una subnet nella rete virtuale esclusivamente per l'uso da parte del gateway applicazione. Non è possibile distribuire il gateway applicazione in una rete virtuale diversa da quella delle risorse da distribuire dietro il gateway applicazione.
3. È necessario che i server configurati per l'uso del gateway applicazione esistano oppure che i relativi endpoint siano stati creati nella rete virtuale o con un indirizzo IP/VIP pubblico assegnato.

## <a name="what-is-required-to-create-an-application-gateway"></a>Elementi necessari per creare un gateway applicazione

* **Pool di server back-end:** elenco di indirizzi IP dei server back-end. Gli indirizzi IP elencati devono appartenere alla subnet della rete virtuale o devono essere indirizzi IP/VIP pubblici.
* **Impostazioni del pool di server back-end:** ogni pool ha impostazioni come porta, protocollo e affinità basata sui cookie. Queste impostazioni sono associate a un pool e vengono applicate a tutti i server nel pool.
* **Porta front-end:** porta pubblica aperta sul gateway applicazione. Il traffico raggiunge questa porta e quindi viene reindirizzato a uno dei server back-end.
* **Listener** : ha una porta front-end, un protocollo (Http o Https, con distinzione tra maiuscole e minuscole) e il nome del certificato SSL (se si configura l'offload SSL).
* **Regola** : associa il listener e il pool di server back-end e definisce il pool di server back-end a cui deve essere indirizzato il traffico quando raggiunge un listener specifico.

## <a name="create-an-application-gateway"></a>Creare un gateway applicazione

La differenza tra l'uso di Azure classico e di Azure Resource Manager risiede nell'ordine in cui vengono creati il gateway applicazione e gli elementi da configurare.

Con Resource Manager, tutti gli elementi che costituiscono un gateway applicazione vengono configurati singolarmente e quindi combinati per creare la risorsa del gateway applicazione.

Per creare un gateway applicazione, seguire questa procedura.

## <a name="create-a-resource-group-for-resource-manager"></a>Creare un gruppo di risorse per Gestione risorse

Assicurarsi di usare la versione più recente di Azure PowerShell. Altre informazioni sono disponibili in [Uso di Azure PowerShell con Azure Resource Manager](../powershell-azure-resource-manager.md).

### <a name="step-1"></a>Passaggio 1

Accedere ad Azure

```powershell
Login-AzureRmAccount
```

Verrà richiesto di eseguire l'autenticazione con le proprie credenziali.

### <a name="step-2"></a>Passaggio 2

Controllare le sottoscrizioni per l'account.

```powershell
Get-AzureRmSubscription
```

### <a name="step-3"></a>Passaggio 3

Scegliere le sottoscrizioni ad Azure da usare.

```powershell
Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
```

### <a name="step-4"></a>Passaggio 4

Creare un gruppo di risorse. Ignorare questo passaggio se si usa un gruppo di risorse esistente.

```powershell
New-AzureRmResourceGroup -Name appgw-rg -Location "West US"
```

Gestione risorse di Azure richiede che tutti i gruppi di risorse specifichino un percorso che viene usato come percorso predefinito per le risorse presenti in tale gruppo di risorse. Assicurarsi che tutti i comandi per creare un gateway applicazione usino lo stesso gruppo di risorse.

Nell'esempio precedente è stato creato un gruppo di risorse denominato **appgw-RG** e l'area **West US**.

> [!NOTE]
> Se è necessario configurare un probe personalizzato per il gateway applicazione, vedere [Creare un probe personalizzato per il gateway applicazione di Azure con PowerShell per Azure Resource Manager](application-gateway-create-probe-ps.md). Per altre informazioni, vedere l'articolo relativo a [probe personalizzati e monitoraggio dell'integrità](application-gateway-probe-overview.md) .

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Creare una rete virtuale e una subnet per il gateway applicazione

L'esempio seguente illustra come creare una rete virtuale usando Gestione risorse.

### <a name="step-1"></a>Passaggio 1

Assegnare l'intervallo di indirizzi 10.0.0.0/24 alla variabile di subnet da usare per creare una rete virtuale.

```powershell
$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24
```

### <a name="step-2"></a>Passaggio 2

Creare una rete virtuale denominata **appgwvnet** nel gruppo di risorse **appgw-rg** per l'area Stati Uniti occidentali usando il prefisso 10.0.0.0/16 con subnet 10.0.0.0/24.

```powershell
$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
```

### <a name="step-3"></a>Passaggio 3

Assegnare una variabile di subnet per la creazione di un gateway applicazione nei passaggi successivi.

```powershell
$subnet=$vnet.Subnets[0]
```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Creare un indirizzo IP pubblico per la configurazione front-end

Creare una risorsa IP pubblica denominata **publicIP01** nel gruppo di risorse **appgw-rg** per l'area Stati Uniti occidentali.

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic
```

## <a name="create-the-application-gateway-configuration-objects"></a>Creare gli oggetti di configurazione gateway applicazione

È necessario impostare tutti gli elementi di configurazione prima di creare il gateway applicazione. La procedura seguente consente di creare gli elementi di configurazione necessari per una risorsa del gateway applicazione.

### <a name="step-1"></a>Passaggio 1

Creare una configurazione IP del gateway applicazione denominata **gatewayIP01**. All'avvio, il gateway applicazione seleziona un indirizzo IP dalla subnet configurata e instrada il traffico di rete agli indirizzi IP nel pool di indirizzi IP back-end. Tenere presente che ogni istanza ha un indirizzo IP.

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet
```

### <a name="step-2"></a>Passaggio 2

Configurare il pool di indirizzi IP back-end denominato **pool01** con gli indirizzi IP **134.170.185.46**, **134.170.188.221**, **134.170.185.50**. Questi indirizzi IP saranno quelli che ricevono il traffico di rete proveniente dall'endpoint IP front-end. Sostituire gli indirizzi IP precedenti e aggiungere gli endpoint di indirizzi IP dell'applicazione.

```powershell
$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50
```

### <a name="step-3"></a>Passaggio 3

Configurare l'impostazione **poolsetting01** del gateway applicazione per il traffico di rete con carico bilanciato nel pool back-end.

```powershell
$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled
```

### <a name="step-4"></a>Passaggio 4

Configurare la porta IP front-end denominata **frontendport01** per l'endpoint IP pubblico.

```powershell
$fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80
```

### <a name="step-5"></a>Passaggio 5

Creare la configurazione IP front-end denominata **fipconfig01** e associare l'indirizzo IP pubblico alla configurazione IP front-end.

```powershell
$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip
```

### <a name="step-6"></a>Passaggio 6

Creare il listener denominato **listener01** e associare la porta front-end alla configurazione IP front-end.

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp
```

### <a name="step-7"></a>Passaggio 7

Creare la regola di routing per il bilanciamento del carico denominata **rule01** che configura il comportamento per il bilanciamento del carico.

```powershell
$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
```

### <a name="step-8"></a>Passaggio 8

Configurare le dimensioni dell'istanza del gateway applicazione.

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
```

> [!NOTE]
> Il valore predefinito per **InstanceCount** è 2, con un valore massimo pari a 10. Il valore predefinito per **GatewaySize** è Medium. È possibile scegliere tra **Standard_Small**, **Standard_Medium** e **Standard_Large**.

## <a name="create-an-application-gateway-by-using-new-azurermapplicationgateway"></a>Creare un gateway applicazione usando New-AzureRmApplicationGateway

Creare un gateway applicazione con tutti gli elementi di configurazione illustrati nei passaggi precedenti. In questo esempio il gateway applicazione è denominato **appgwtest**.

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku
```

Recuperare i dettagli relativi a DNS e indirizzo VIP del gateway applicazione dalla risorsa IP pubblica associata al gateway applicazione.

```powershell
Get-AzureRmPublicIpAddress -Name publicIP01 -ResourceGroupName appgw-rg  
```

## <a name="delete-an-application-gateway"></a>Eliminare un gateway applicazione

Per eliminare un gateway applicazione, seguire questa procedura:

### <a name="step-1"></a>Passaggio 1

Ottenere l'oggetto gateway applicazione e associarlo a una variabile `$getgw`.

```powershell
$getgw = Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg
```

### <a name="step-2"></a>Passaggio 2

Usare `Stop-AzureRmApplicationGateway` per arrestare il gateway applicazione.

```powershell
Stop-AzureRmApplicationGateway -ApplicationGateway $getgw  
```

Quando il gateway applicazione si trova in stato di interruzione, usare il cmdlet `Remove-AzureRmApplicationGateway` per rimuovere il servizio.

```powershell
Remove-AzureRmApplicationGateway -Name $appgwtest -ResourceGroupName appgw-rg -Force
```

> [!NOTE]
> L'opzione **-force** può essere usata per eliminare il messaggio di conferma della rimozione.

Per verificare che il servizio sia stato rimosso, è possibile usare il cmdlet `Get-AzureRmApplicationGateway`. Questo passaggio non è obbligatorio.

```powershell
Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg
```

## <a name="get-application-gateway-dns-name"></a>Ottenere il nome DNS del gateway applicazione

Dopo avere creato il gateway, il passaggio successivo prevede la configurazione del front-end per la comunicazione. Quando si usa un IP pubblico, il gateway applicazione richiede un nome DNS assegnato in modo dinamico, non descrittivo. Per assicurarsi che gli utenti finali possano raggiungere il gateway applicazione, è possibile usare un record CNAME per fare riferimento all'endpoint pubblico del gateway applicazione. [Configurazione di un nome di dominio personalizzato in Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). A questo scopo, recuperare i dettagli del gateway applicazione e il nome DNS e l'IP associati, usando l'elemento PublicIPAddress collegato al gateway applicazione. Il nome DNS del gateway applicazione dovrà essere usato per creare un record CNAME che associa le due applicazioni Web a questo nome DNS. Non è consigliabile usare record A perché l'indirizzo VIP può cambiare al riavvio del gateway applicazione.

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -Name publicIP01
```

```
Name                     : publicIP01
ResourceGroupName        : appgw-RG
Location                 : westus
Id                       : /subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/publicIPAddresses/publicIP01
Etag                     : W/"00000d5b-54ed-4907-bae8-99bd5766d0e5"
ResourceGuid             : 00000000-0000-0000-0000-000000000000
ProvisioningState        : Succeeded
Tags                     : 
PublicIpAllocationMethod : Dynamic
IpAddress                : xx.xx.xxx.xx
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : {
                                "Id": "/subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/applicationGateways/appgwtest/frontendIP
                            Configurations/frontend1"
                            }
DnsSettings              : {
                                "Fqdn": "00000000-0000-xxxx-xxxx-xxxxxxxxxxxx.cloudapp.net"
                            }
```

## <a name="next-steps"></a>Passaggi successivi

Per configurare l'offload SSL, vedere [Configurare un gateway applicazione per l'offload SSL](application-gateway-ssl.md).

Per configurare un gateway applicazione da usare con un servizio di bilanciamento del carico interno, vedere [Creare un gateway applicazione con un servizio di bilanciamento del carico interno (ILB)](application-gateway-ilb.md).

Per altre informazioni generali sulle opzioni di bilanciamento del carico, vedere:

* [Servizio di bilanciamento del carico di Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Gestione traffico di Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)




<!--HONumber=Dec16_HO2-->


