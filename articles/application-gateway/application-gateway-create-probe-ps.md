<properties
   pageTitle="Creare un probe personalizzato per il gateway applicazione con PowerShell in Gestione risorse | Microsoft Azure"
   description="Informazioni su come creare un probe personalizzato per il gateway applicazione con PowerShell in Gestione risorse"
   services="application-gateway"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/17/2015"
   ms.author="joaoma" />

# Creare un probe personalizzato per il gateway applicazione di Azure con PowerShell per Gestione risorse di Azure

[AZURE.INCLUDE [azure-probe-intro-include](../../includes/application-gateway-create-probe-intro-include.md)]


[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](application-gateway-create-probe-classic-ps.md).


[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]


### Passaggio 1

Usare Login-AzureRmAccount per l'autenticazione.

	Login-AzureRmAccount

### Passaggio 2

Controllare le sottoscrizioni per l'account.

		get-AzureRmSubscription

Verrà richiesto di eseguire l'autenticazione con le proprie credenziali.<BR>

### Passaggio 3

Scegliere quali sottoscrizioni Azure usare. <BR>


		Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


### Passaggio 4

Creare un nuovo gruppo di risorse. Ignorare questo passaggio se si sta usando un gruppo di risorse esistente.

    New-AzureRmResourceGroup -Name appgw-rg -location "West US"

Gestione risorse di Azure richiede che tutti i gruppi di risorse specifichino un percorso che viene usato come percorso predefinito per le risorse presenti in tale gruppo di risorse. Assicurarsi che tutti i comandi per creare un gateway applicazione usino lo stesso gruppo di risorse.

Nell'esempio precedente è stato creato un gruppo di risorse denominato "appgw-RG" e la località "West US".

## Creare una rete virtuale e una subnet per il gateway applicazione

I passaggi seguenti consentono di creare una rete virtuale e una subnet per il gateway applicazione.

### Passaggio 1


Assegnare l'intervallo di indirizzi 10.0.0.0/24 a una variabile di subnet da usare per creare una rete virtuale.

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


## Creare un oggetto di configurazione gateway applicazione con il probe personalizzato

È necessario impostare tutti gli elementi di configurazione prima di creare il gateway applicazione. La procedura seguente consente di creare gli elementi di configurazione necessari per una risorsa del gateway applicazione.


### Passaggio 1

Creare una configurazione IP del gateway applicazione denominata "gatewayIP01". All'avvio, il gateway applicazione seleziona un indirizzo IP dalla subnet configurata e indirizza il traffico di rete agli indirizzi IP nel pool di indirizzi IP back-end. Ogni istanza avrà un indirizzo IP.

	$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet


### Passaggio 2


Configurare il pool di indirizzi IP back-end denominato "pool01" con gli indirizzi IP "134.170.185.46, 134.170.188.221,134.170.185.50". Questi saranno gli indirizzi IP che ricevono il traffico di rete proveniente dall'endpoint IP front-end. Sostituire gli indirizzi IP precedenti per aggiungere il proprio endpoint dell’indirizzo IP dell'applicazione.

	$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50


### Passaggio 3


Il probe personalizzato viene configurato in questo passaggio.

I parametri usati sono:

- **-Interval**: configura i controlli dell'intervallo di probe in secondi.
- **-Timeout**: definisce il timeout del probe per un controllo della risposta HTTP.
- **-Hostname e -path**: completare il percorso URL richiamato dal gateway applicazione per determinare l'integrità dell'istanza. Se, ad esempio, si ha un sito Web http://contoso.com/, il probe personalizzato può essere configurato per "http://contoso.com/path/custompath.htm" in modo che i controlli del probe ottengano una risposta HTTP corretta.
- **-UnhealthyThreshold**: numero di risposte HTTP non riuscite necessario per contrassegnare l'istanza back-end come *non integra*.

<BR>

	$probe = New-AzureRmApplicationGatewayProbeConfig -Name probe01 -Protocol Http -HostName "contoso.com" -Path "/path/path.htm" -Interval 30 -Timeout 120 -UnhealthyThreshold 8


### Passaggio 4

Configurare le impostazioni del gateway applicazione "poolsetting01" per il traffico nel pool back-end. Questo passaggio prevede anche una configurazione di timeout per la risposta del pool back-end a una richiesta del gateway applicazione. Quando una risposta back-end raggiunge un limite di timeout, il gateway applicazione annullerà la richiesta. È diverso dal timeout del probe che è solo per la risposta back-end ai controlli del probe.

	$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 80


### Passaggio 5

Configurare la porta IP front-end denominata "frontendport01" per l'endpoint IP pubblico.

	$fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80

### Passaggio 6

Creare la configurazione IP front-end denominata "fipconfig01" e associare l'indirizzo IP pubblico alla configurazione IP front-end.


	$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

### Passaggio 7

Creare il listener denominato "listener01" e associare la porta front-end alla configurazione IP front-end.

	$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

### Passaggio 8

Creare la regola di routing del dispositivo di bilanciamento del carico denominata "rule01" che configura il comportamento di bilanciamento del carico.

	$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

### Passaggio 9:

Configurare le dimensioni dell'istanza del gateway applicazione.

	$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2


>[AZURE.NOTE]  Il valore predefinito per *InstanceCount* è 2, con un valore massimo pari a 10. Il valore predefinito per *GatewaySize* è Medium. È possibile scegliere tra Standard\_Small, Standard\_Medium e Standard\_Large.

## Creare un gateway applicazione usando New-AzureRmApplicationGateway

Creare un gateway applicazione con tutti gli elementi di configurazione illustrati nei passaggi precedenti. Nell'esempio il gateway applicazione è denominato "appgwtest".

	$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -Probes $probe -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku

## Aggiungere un probe a un gateway applicazione esistente

Sono necessari quattro passaggi per aggiungere un probe personalizzato a un gateway applicazione esistente.

### Passaggio 1

Caricare la risorsa del gateway applicazione in una variabile di PowerShell usando **Get-AzureRmApplicationGateway**.

	$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

### Passaggio 2

Aggiungere un probe alla configurazione gateway esistente.

	$probe = Add-AzureRmApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name probe01 -Protocol Http -HostName "contoso.com" -Path "/path/custompath.htm" -Interval 30 -Timeout 120 -UnhealthyThreshold 8


Nell'esempio il probe personalizzato è configurato per cercare il percorso URL contoso.com/path/custompath.htm ogni 30 secondi. Una soglia di timeout di 120 secondi viene configurata con il numero massimo di 8 richieste probe non riuscite.

### Passaggio 3

Aggiungere il probe al timeout e alla configurazione dell'impostazione del pool back-end usando **-Set-AzureRmApplicationGatewayBackendHttpSettings**.


	 $getgw = Set-AzureRmApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 120

### Passaggio 4

Salvare la configurazione nel gateway applicazione usando **Set-AzureRmApplicationGateway**.

	Set-AzureRmApplicationGateway -ApplicationGateway $getgw -verbose

## Rimuovere un probe da un gateway applicazione esistente

Ecco i passaggi per rimuovere un probe personalizzato da un gateway applicazione esistente.

### Passaggio 1

Caricare la risorsa del gateway applicazione in una variabile di PowerShell usando **Get-AzureRmApplicationGateway**.

	$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg


### Passaggio 2

Rimuovere la configurazione del probe dal gateway applicazione usando **Remove-AzureRmApplicationGatewayProbeConfig**.

	$getgw = Remove-AzureRmApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name $getgw.Probes.name

### Passaggio 3

Aggiornare l'impostazione del pool back-end per rimuovere l'impostazione del probe e del timeout usando **-Set-AzureRmApplicationGatewayBackendHttpSettings**.


	 $getgw=Set-AzureRmApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol http -CookieBasedAffinity Disabled

### Passaggio 4

Salvare la configurazione nel gateway applicazione usando **Set-AzureRmApplicationGateway**.

	Set-AzureRmApplicationGateway -ApplicationGateway $getgw -verbose

<!---HONumber=AcomDC_0128_2016-->