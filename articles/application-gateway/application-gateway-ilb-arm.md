---
title: Uso di un gateway applicazione di Azure con un servizio di bilanciamento del carico interno - PowerShell | Documentazione Microsoft
description: Questa pagina fornisce istruzioni per la creazione, la configurazione, l'avvio e l'eliminazione di un gateway applicazione di Azure con un dispositivo di bilanciamento del carico interno (ILB) per Gestione risorse di Azure
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: 75cfd5a2-e378-4365-99ee-a2b2abda2e0d
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: davidmu
ms.openlocfilehash: 8d96af009055a5c0349f0ac17054bebee4e54d36
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2017
---
# <a name="create-an-application-gateway-with-an-internal-load-balancer-ilb-by-using-azure-resource-manager"></a>Creare un gateway applicazione con un dispositivo di bilanciamento del carico interno (ILB) tramite Gestione risorse di Azure

> [!div class="op_single_selector"]
> * [PowerShell per Azure classico](application-gateway-ilb.md)
> * [PowerShell per Azure Resource Manager](application-gateway-ilb-arm.md)

Un gateway applicazione di Azure può essere configurato con un indirizzo VIP con connessione Internet o con un endpoint interno non esposto a Internet, detto anche endpoint del dispositivo di bilanciamento del carico interno (ILB). Configurare il gateway con un ILB è utile per le applicazioni line-of-business interne non esposte a Internet. È utile anche per servizi e livelli in un'applicazione a più livelli posti entro un limite di sicurezza non esposto a Internet, ma che richiedono la distribuzione del carico round robin, la persistenza delle sessioni o la terminazione Secure Sockets Layer (SSL).

In questo articolo verrà illustrata la procedura per configurare un gateway applicazione con un ILB.

## <a name="before-you-begin"></a>Prima di iniziare

1. Installare la versione più recente dei cmdlet di Azure PowerShell usando l'Installazione guidata piattaforma Web. È possibile scaricare e installare la versione più recente dalla sezione **Windows PowerShell** della [Pagina di download](https://azure.microsoft.com/downloads/).
2. Creare una rete virtuale e una subnet per il gateway applicazione. Assicurarsi che nessuna macchina virtuale o distribuzione cloud stia usando la subnet. Il gateway applicazione deve essere da solo in una subnet di rete virtuale.
3. È necessario che i server configurati per l'uso del gateway applicazione esistano oppure che i relativi endpoint siano stati creati nella rete virtuale o con un indirizzo IP/VIP pubblico assegnato.

## <a name="what-is-required-to-create-an-application-gateway"></a>Elementi necessari per creare un gateway applicazione

* **Pool di server back-end:** elenco di indirizzi IP dei server back-end. Gli indirizzi IP elencati devono appartenere alla rete virtuale, ma devono trovarsi in una subnet diversa per il gateway applicazione, o devono essere un indirizzo IP/VIP pubblico.
* **Impostazioni del pool di server back-end:** ogni pool ha impostazioni quali porta, protocollo e affinità basata sui cookie. Queste impostazioni sono associate a un pool e vengono applicate a tutti i server nel pool.
* **Porta front-end:** porta pubblica aperta sul gateway applicazione. Il traffico raggiunge questa porta e quindi viene reindirizzato a uno dei server back-end.
* **Listener** : ha una porta front-end, un protocollo (Http o Https, con distinzione tra maiuscole e minuscole) e il nome del certificato SSL (se si configura l'offload SSL).
* **Regola** : associa il listener e il pool di server back-end e definisce il pool di server back-end a cui deve essere indirizzato il traffico quando raggiunge un listener specifico. È attualmente supportata solo la regola *basic* . La regola *basic* è una distribuzione del carico di tipo round robin.

## <a name="create-an-application-gateway"></a>Creare un gateway applicazione

La differenza tra l'uso di Azure classico e di Azure Resource Manager risiede nell'ordine in cui vengono creati il gateway applicazione e gli elementi da configurare.
Con Resource Manager tutti gli elementi che costituiscono un gateway applicazione vengono configurati singolarmente e quindi combinati per creare la risorsa del gateway applicazione.

Per creare un gateway applicazione, seguire questa procedura:

1. Creare un gruppo di risorse per Gestione risorse
2. Creare una rete virtuale e una subnet per il gateway applicazione
3. Creare un oggetto di configurazione gateway applicazione
4. Creare una risorsa del gateway applicazione

## <a name="create-a-resource-group-for-resource-manager"></a>Creare un gruppo di risorse per Gestione risorse

Assicurarsi di passare alla modalità PowerShell per usare i cmdlet di Gestione risorse di Azure. Altre informazioni sono disponibili in [Uso di Azure PowerShell con Azure Resource Manager](../powershell-azure-resource-manager.md).

### <a name="step-1"></a>Passaggio 1

```powershell
Login-AzureRmAccount
```

### <a name="step-2"></a>Passaggio 2

Controllare le sottoscrizioni per l'account.

```powershell
Get-AzureRmSubscription
```

Verrà richiesto di eseguire l'autenticazione con le proprie credenziali.

### <a name="step-3"></a>Passaggio 3

Scegliere quali sottoscrizioni Azure usare.

```powershell
Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
```

### <a name="step-4"></a>Passaggio 4

Creare un nuovo gruppo di risorse. Ignorare questo passaggio se si sta usando un gruppo di risorse esistente.

```powershell
New-AzureRmResourceGroup -Name appgw-rg -location "West US"
```

Gestione risorse di Azure richiede che tutti i gruppi di risorse specifichino un percorso che viene usato come percorso predefinito per le risorse presenti in tale gruppo di risorse. Assicurarsi che tutti i comandi per creare un gateway applicazione usino lo stesso gruppo di risorse.

Nell'esempio precedente è stato creato un gruppo di risorse denominato "appgw-rg" e la località "Stati Uniti occidentali".

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Creare una rete virtuale e una subnet per il gateway applicazione

L'esempio seguente illustra come creare una rete virtuale usando Gestione risorse:

### <a name="step-1"></a>Passaggio 1

```powershell
$subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24
```

Questo passaggio assegna l'intervallo di indirizzi 10.0.0.0/24 a una variabile di subnet da usare per creare una rete virtuale.

### <a name="step-2"></a>Passaggio 2

```powershell
$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnetconfig
```

Questo passaggio crea una rete virtuale denominata "appgwvnet" nel gruppo di risorse "appgw-rg" per l'area Stati Uniti occidentali usando il prefisso 10.0.0.0/16 con subnet 10.0.0.0/24.

### <a name="step-3"></a>Passaggio 3

```powershell
$subnet = $vnet.subnets[0]
```

Questo passaggio assegna l'oggetto subnet alla variabile $subnet per i passaggi successivi.

## <a name="create-an-application-gateway-configuration-object"></a>Creare un oggetto di configurazione gateway applicazione

### <a name="step-1"></a>Passaggio 1

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet
```

Questo passaggio crea una configurazione IP del gateway applicazione denominata "gatewayIP01". All'avvio, il gateway applicazione seleziona un indirizzo IP dalla subnet configurata e instrada il traffico di rete agli indirizzi IP nel pool di indirizzi IP back-end. Tenere presente che ogni istanza ha un indirizzo IP.

### <a name="step-2"></a>Passaggio 2

```powershell
$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 10.1.1.8,10.1.1.9,10.1.1.10
```

Questo passaggio configura il pool di indirizzi IP back-end denominato "pool01" con gli indirizzi IP "10.1.1.8, 10.1.1.9, 10.1.1.10". Questi saranno gli indirizzi IP che ricevono il traffico di rete proveniente dall'endpoint IP front-end. Sostituire gli indirizzi IP precedenti e aggiungere gli endpoint di indirizzi IP dell'applicazione.

### <a name="step-3"></a>Passaggio 3

```powershell
$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled
```

Questo passaggio configura l'impostazione "poolsetting01" del gateway applicazione per il traffico di rete con bilanciamento del carico nel pool back-end.

### <a name="step-4"></a>Passaggio 4

```powershell
$fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80
```

Questo passaggio configura la porta IP front-end denominata "frontendport01" per il bilanciamento del carico interno.

### <a name="step-5"></a>Passaggio 5

```powershell
$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -Subnet $subnet
```

Questo passaggio crea la configurazione IP front-end denominata "fipconfig01" e la associa a un indirizzo IP privato dalla subnet di rete virtuale corrente.

### <a name="step-6"></a>Passaggio 6

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp
```

Questo passaggio crea il listener denominato "listener01" e associa la porta front-end alla configurazione IP front-end.

### <a name="step-7"></a>Passaggio 7

```powershell
$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
```

Questo passaggio crea la regola di routing del servizio di bilanciamento del carico denominata "rule01" che configura il comportamento del servizio di bilanciamento del carico.

### <a name="step-8"></a>Passaggio 8

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
```

Questo passaggio configura le dimensioni dell'istanza del gateway applicazione.

> [!NOTE]
> Il valore predefinito per *InstanceCount* è 2, con un valore massimo pari a 10. Il valore predefinito per *GatewaySize* è Medium. È possibile scegliere tra Standard_Small, Standard_Medium e Standard_Large.

## <a name="create-an-application-gateway-by-using-new-azureapplicationgateway"></a>Creare un gateway applicazione usando New-AzureApplicationGateway

Crea un gateway applicazione con tutti gli elementi di configurazione illustrati nei passaggi precedenti. Nell'esempio, il gateway applicazione è denominato "appgwtest".

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku
```

Questo passaggio crea un gateway applicazione con tutti gli elementi di configurazione illustrati nei passaggi precedenti. Nell'esempio il gateway applicazione è denominato "appgwtest".

## <a name="delete-an-application-gateway"></a>Eliminare un gateway applicazione

Per eliminare un gateway applicazione sarà necessario eseguire questi passaggi nell'ordine indicato:

1. Usare il cmdlet `Stop-AzureRmApplicationGateway` per arrestare il gateway.
2. Usare il cmdlet `Remove-AzureRmApplicationGateway` per rimuovere il gateway.
3. Assicurarsi che il gateway sia stato rimosso usando il cmdlet `Get-AzureApplicationGateway`.

### <a name="step-1"></a>Passaggio 1

Ottenere l'oggetto gateway applicazione e associarlo a una variabile "$getgw".

```powershell
$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg
```

### <a name="step-2"></a>Passaggio 2

Usare `Stop-AzureRmApplicationGateway` per arrestare il gateway applicazione. Questo esempio illustra il cmdlet `Stop-AzureRmApplicationGateway` nella prima riga seguito dall'output.

```powershell
Stop-AzureRmApplicationGateway -ApplicationGateway $getgw  
```

```
VERBOSE: 9:49:34 PM - Begin Operation: Stop-AzureApplicationGateway
VERBOSE: 10:10:06 PM - Completed Operation: Stop-AzureApplicationGateway
Name       HTTP Status Code     Operation ID                             Error
----       ----------------     ------------                             ----
Successful OK                   ce6c6c95-77b4-2118-9d65-e29defadffb8
```

Quando il gateway applicazione si trova in stato di interruzione, usare il cmdlet `Remove-AzureRmApplicationGateway` per rimuovere il servizio.

```powershell
Remove-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Force
```

```
VERBOSE: 10:49:34 PM - Begin Operation: Remove-AzureApplicationGateway
VERBOSE: 10:50:36 PM - Completed Operation: Remove-AzureApplicationGateway
Name       HTTP Status Code     Operation ID                             Error
----       ----------------     ------------                             ----
Successful OK                   055f3a96-8681-2094-a304-8d9a11ad8301
```

> [!NOTE]
> L'opzione **-force** può essere usata per eliminare il messaggio di conferma della rimozione.

Per verificare che il servizio sia stato rimosso, è possibile usare il cmdlet `Get-AzureRmApplicationGateway`. Questo passaggio non è obbligatorio.

```powershell
Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg
```

```
VERBOSE: 10:52:46 PM - Begin Operation: Get-AzureApplicationGateway

Get-AzureApplicationGateway : ResourceNotFound: The gateway does not exist.
```

## <a name="next-steps"></a>Passaggi successivi

Per configurare l'offload SSL, vedere [Configurare un gateway applicazione per l'offload SSL](application-gateway-ssl.md).

Per configurare un gateway applicazione da usare con un servizio di bilanciamento del carico interno, vedere [Creare un gateway applicazione con un servizio di bilanciamento del carico interno (ILB)](application-gateway-ilb.md).

Per altre informazioni generali sulle opzioni di bilanciamento del carico, vedere:

* [Servizio di bilanciamento del carico di Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Gestione traffico di Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)

