<properties
   pageTitle="Creare, avviare o eliminare un gateway applicazione tramite Gestione risorse di Azure | Microsoft Azure"
   description="Questa pagina fornisce istruzioni per la creazione, la configurazione, l'avvio e l'eliminazione di un gateway applicazione di Azure usando Gestione risorse di Azure"
   documentationCenter="na"
   services="application-gateway"
   authors="georgewallace"
   manager="carmonm"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/09/2016"
   ms.author="gwallace"/>


# Creare, avviare o eliminare un gateway applicazione tramite Gestione risorse di Azure

Il gateway applicazione di Azure è un dispositivo di bilanciamento del carico di livello 7. Fornisce richieste HTTP con routing delle prestazioni e failover tra server diversi, sia nel cloud che in locale. Il gateway applicazione offre le seguenti funzionalità di distribuzione delle applicazioni: bilanciamento del carico HTTP, affinità delle sessioni basata sui cookie e offload SSL (Secure Sockets Layer).


> [AZURE.SELECTOR]
- [Portale di Azure](application-gateway-create-gateway-portal.md)
- [PowerShell per Azure Resource Manager](application-gateway-create-gateway-arm.md)
- [PowerShell per Azure classico](application-gateway-create-gateway.md)
- [Modello di Azure Resource Manager](application-gateway-create-gateway-arm-template.md)
- [Interfaccia della riga di comando di Azure](application-gateway-create-gateway-cli.md)

<BR>


Questo articolo illustra in dettaglio i passaggi necessari per creare e configurare, avviare ed eliminare un gateway applicazione.


>[AZURE.IMPORTANT] Prima di iniziare a usare le risorse di Azure, è importante comprendere che Azure al momento offre due modelli di distribuzione, quello classico e Gestione risorse. È importante conoscere i [modelli e gli strumenti di distribuzione](../azure-classic-rm.md) prima di usare le risorse di Azure. È possibile visualizzare la documentazione relativa a diversi strumenti facendo clic sulle schede nella parte superiore di questo articolo. Questo documento illustra la creazione di un gateway applicazione con Azure Resource Manager. Per usare la versione classica, passare a [Creare, avviare o eliminare un gateway applicazione](application-gateway-create-gateway.md).



## Prima di iniziare

1. Installare la versione più recente dei cmdlet di Azure PowerShell usando l'Installazione guidata piattaforma Web. È possibile scaricare e installare la versione più recente dalla sezione **Windows PowerShell** della [pagina Download](https://azure.microsoft.com/downloads/).
2. Se è già disponibile una rete virtuale, selezionare una subnet vuota esistente oppure creare una subnet nella rete virtuale esclusivamente per l'uso da parte del gateway applicazione. Non è possibile distribuire il gateway applicazione in una rete virtuale diversa da quella delle risorse da distribuire dietro il gateway applicazione.
3. È necessario che i server configurati per l'uso del gateway applicazione esistano oppure che i relativi endpoint siano stati creati nella rete virtuale o con un indirizzo IP/VIP pubblico assegnato.

## Elementi necessari per creare un gateway applicazione


- **Pool di server back-end:** elenco di indirizzi IP dei server back-end. Gli indirizzi IP elencati devono appartenere alla subnet della rete virtuale o devono essere indirizzi IP/VIP pubblici.
- **Impostazioni del pool di server back-end:** ogni pool ha impostazioni come porta, protocollo e affinità basata sui cookie. Queste impostazioni sono associate a un pool e vengono applicate a tutti i server nel pool.
- **Porta front-end:** porta pubblica aperta sul gateway applicazione. Il traffico raggiunge questa porta e quindi viene reindirizzato a uno dei server back-end.
- **Listener**: ha una porta front-end, un protocollo (Http o Https, con distinzione tra maiuscole e minuscole) e il nome del certificato SSL (se si configura l'offload SSL).
- **Regola**: associa il listener e il pool di server back-end e definisce il pool di server back-end a cui deve essere indirizzato il traffico quando raggiunge un listener specifico.



## Creare un gateway applicazione

La differenza tra l'uso di Azure classico e di Azure Resource Manager risiede nell'ordine in cui vengono creati il gateway applicazione e gli elementi da configurare.

Con Resource Manager, tutti gli elementi che costituiscono un gateway applicazione vengono configurati singolarmente e quindi combinati per creare la risorsa del gateway applicazione.


Per creare un gateway applicazione, seguire questa procedura.

## Creare un gruppo di risorse per Gestione risorse

Assicurarsi di usare la versione più recente di Azure PowerShell. Altre informazioni sono disponibili in [Uso di Azure PowerShell con Azure Resource Manager](../powershell-azure-resource-manager.md).

### Passaggio 1
Accedere all'account Login-AzureRmAccount di Azure.

Verrà richiesto di eseguire l'autenticazione con le proprie credenziali.<BR>
### Passaggio 2
Controllare le sottoscrizioni per l'account.

		Get-AzureRmSubscription

### Passaggio 3
Scegliere quali sottoscrizioni Azure usare. <BR>

		Select-AzureRmSubscription -Subscriptionid "GUID of subscription"

### Passaggio 4
Creare un nuovo gruppo di risorse. Ignorare questo passaggio se si sta usando un gruppo di risorse esistente.

    New-AzureRmResourceGroup -Name appgw-rg -location "West US"

Gestione risorse di Azure richiede che tutti i gruppi di risorse specifichino un percorso che viene usato come percorso predefinito per le risorse presenti in tale gruppo di risorse. Assicurarsi che tutti i comandi per creare un gateway applicazione usino lo stesso gruppo di risorse.

Nell'esempio precedente è stato creato un gruppo di risorse denominato "appgw-RG" e la località "West US".

>[AZURE.NOTE] Se è necessario configurare un probe personalizzato per il gateway applicazione, vedere [Creare un probe personalizzato per il gateway applicazione di Azure con PowerShell per Azure Resource Manager](application-gateway-create-probe-ps.md). Per altre informazioni, vedere l'articolo relativo a [probe personalizzati e monitoraggio dell'integrità](application-gateway-probe-overview.md).



## Creare una rete virtuale e una subnet per il gateway applicazione

L'esempio seguente illustra come creare una rete virtuale usando Gestione risorse.

### Passaggio 1

Assegnare l'intervallo di indirizzi 10.0.0.0/24 alla variabile di subnet da usare per creare una rete virtuale.

	$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24


### Passaggio 2

Creare una rete virtuale denominata "appgwvnet" nel gruppo di risorse "appgw-rg" per l'area Stati Uniti occidentali usando il prefisso 10.0.0.0/16 con subnet 10.0.0.0/24.

	$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet


### Passaggio 3

Assegnare una variabile di subnet per la creazione di un gateway applicazione nei passaggi successivi.

	$subnet=$vnet.Subnets[0]

## Creare un indirizzo IP pubblico per la configurazione front-end

Creare una risorsa IP pubblica "publicIP01" nel gruppo di risorse "appgw-rg" per l'area Stati Uniti occidentali.

	$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic


## Creare un oggetto di configurazione gateway applicazione

È necessario impostare tutti gli elementi di configurazione prima di creare il gateway applicazione. La procedura seguente consente di creare gli elementi di configurazione necessari per una risorsa del gateway applicazione.

### Passaggio 1

Creare una configurazione IP del gateway applicazione denominata "gatewayIP01". All'avvio, il gateway applicazione seleziona un indirizzo IP dalla subnet configurata e instrada il traffico di rete agli indirizzi IP nel pool di indirizzi IP back-end. Tenere presente che ogni istanza ha un indirizzo IP.


	$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet


### Passaggio 2

Configurare il pool di indirizzi IP back-end denominato "pool01" con gli indirizzi IP "134.170.185.46, 134.170.188.221,134.170.185.50". Questi indirizzi IP saranno quelli che ricevono il traffico di rete proveniente dall'endpoint IP front-end. Sostituire gli indirizzi IP precedenti e aggiungere gli endpoint di indirizzi IP dell'applicazione.

	$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50



### Passaggio 3

Configurare l'impostazione "poolsetting01" del gateway applicazione per il traffico di rete con bilanciamento del carico nel pool back-end.

	$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled


### Passaggio 4

Configurare la porta IP front-end denominata "frontendport01" per l'endpoint IP pubblico.

	$fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80

### Passaggio 5

Creare la configurazione IP front-end denominata "fipconfig01" e associare l'indirizzo IP pubblico alla configurazione IP front-end.

	$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip


### Passaggio 6

Creare il listener denominato "listener01" e associare la porta front-end alla configurazione IP front-end.

	$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

### Passaggio 7

Creare la regola di routing del dispositivo di bilanciamento del carico denominata "rule01" che configura il comportamento di bilanciamento del carico.

	$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

### Passaggio 8

Configurare le dimensioni dell'istanza del gateway applicazione.

	$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

>[AZURE.NOTE]  Il valore predefinito per *InstanceCount* è 2, con un valore massimo pari a 10. Il valore predefinito per *GatewaySize* è Medium. È possibile scegliere tra Standard\_Small, Standard\_Medium e Standard\_Large.

## Creare un gateway applicazione usando New-AzureRmApplicationGateway

Creare un gateway applicazione con tutti gli elementi di configurazione illustrati nei passaggi precedenti. Nell'esempio, il gateway applicazione è denominato "appgwtest".

	$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku

### Passaggio 9
Recuperare i dettagli relativi a DNS e indirizzo VIP del gateway applicazione dalla risorsa IP pubblica associata al gateway applicazione.

	Get-AzureRmPublicIpAddress -Name publicIP01 -ResourceGroupName appgw-rg  

	Name                     : publicIP01
	ResourceGroupName        : appgwtest 
	Location                 : westus
	Id                       : /subscriptions/<sub_id>/resourceGroups/appgw-rg/providers/Microsoft.Network/publicIPAddresses/publicIP01
	Etag                     : W/"12302060-78d6-4a33-942b-a494d6323767"
	ResourceGuid             : ee9gd76a-3gf6-4236-aca4-gc1f4gf14171
	ProvisioningState        : Succeeded
	Tags                     : 
	PublicIpAllocationMethod : Dynamic
	IpAddress                : 137.116.26.16
	IdleTimeoutInMinutes     : 4
	IpConfiguration          : {
	                             "Id": "/subscriptions/<sub_id>/resourceGroups/appgw-rg/providers/Microsoft.Network/applicationGateways/appgwtest/frontendIPConfigurations/fipconfig01"
	                           }
	DnsSettings              : {
	                             "Fqdn": "ee7aca47-4344-4810-a999-2c631b73e3cd.cloudapp.net"
	                           } 



## Eliminare un gateway applicazione

Per eliminare un gateway applicazione, seguire questa procedura:

### Passaggio 1

Ottenere l'oggetto gateway applicazione e associarlo a una variabile "$getgw".

	$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

### Passaggio 2

Usare **Stop-AzureRmApplicationGateway** per arrestare il gateway applicazione.

	Stop-AzureRmApplicationGateway -ApplicationGateway $getgw  


Quando il gateway applicazione è in stato di arresto, usare il cmdlet **Remove-AzureRmApplicationGateway** per rimuovere il servizio.


	Remove-AzureRmApplicationGateway -Name $appgwtest -ResourceGroupName appgw-rg -Force



>[AZURE.NOTE] L'opzione **-force** può essere usata per eliminare il messaggio di conferma della rimozione.


Per verificare che il servizio sia stato rimosso, è possibile usare il cmdlet **Get-AzureRmApplicationGateway**. Questo passaggio non è obbligatorio.


	Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg


## Passaggi successivi

Per configurare l'offload SSL, vedere [Configurare un gateway applicazione per l'offload SSL](application-gateway-ssl.md).

Per configurare un gateway applicazione da usare con un servizio di bilanciamento del carico interno, vedere [Creare un gateway applicazione con un servizio di bilanciamento del carico interno (ILB)](application-gateway-ilb.md).

Per altre informazioni generali sulle opzioni di bilanciamento del carico, vedere:

- [Servizio di bilanciamento del carico di Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Gestione traffico di Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!---HONumber=AcomDC_0831_2016-->