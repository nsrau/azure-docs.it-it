<properties
   pageTitle="Configurare un gateway applicazione per l'offload SSL tramite Gestione risorse di Azure | Microsoft Azure"
   description="Questa pagina fornisce istruzioni per creare un gateway applicazione con offload SSL usando Gestione risorse di Azure"
   documentationCenter="na"
   services="application-gateway"
   authors="joaoma"
   manager="carmonm"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/03/2016"
   ms.author="joaoma"/>

# Configurare un gateway applicazione per l'offload SSL tramite Gestione risorse di Azure

> [AZURE.SELECTOR]
-[Azure Classic PowerShell](application-gateway-ssl.md)
-[Azure Resource Manager PowerShell](application-gateway-ssl-arm.md)

 Il gateway applicazione di Azure può essere configurato per terminare la sessione Secure Sockets Layer (SSL) nel gateway ed evitare costose attività di decrittografia SSL nella Web farm. L'offload SSL semplifica anche la configurazione e la gestione del server front-end dell'applicazione Web.


## Prima di iniziare

1. Installare la versione più recente dei cmdlet di Azure PowerShell usando l'Installazione guidata piattaforma Web. È possibile scaricare e installare la versione più recente dalla sezione **Windows PowerShell** della [Pagina di download](https://azure.microsoft.com/downloads/).
2. Creare una rete virtuale e una subnet per il gateway applicazione. Assicurarsi che nessuna macchina virtuale o distribuzione cloud stia usando la subnet. Il gateway applicazione deve essere da solo in una subnet di rete virtuale.
3. I server che verranno configurati per l'uso del gateway applicazione devono esistere. In alternativa, è necessario creare i relativi endpoint nella rete virtuale o assegnato loro un indirizzo IP/VIP pubblico.

## Elementi necessari per creare un gateway applicazione


- **Pool di server back-end:** elenco di indirizzi IP dei server back-end. Gli indirizzi IP elencati devono appartenere alla subnet della rete virtuale o devono essere indirizzi IP/VIP pubblici.
- **Impostazioni del pool di server back-end:** ogni pool ha impostazioni quali porta, protocollo e affinità basata sui cookie. Queste impostazioni sono associate a un pool e vengono applicate a tutti i server nel pool.
- **Porta front-end:** porta pubblica aperta sul gateway applicazione. Il traffico raggiunge questa porta e quindi viene reindirizzato a uno dei server back-end.
- **Listener**: ha una porta front-end, un protocollo (HTTP o HTTPS che fa distinzione tra maiuscole e minuscole) e il nome del certificato SSL (se si configura l'offload SSL).
- **Regola**: associa il listener e il pool di server back-end e definisce il pool di server back-end a cui deve essere indirizzato il traffico quando raggiunge un listener specifico. È attualmente supportata solo la regola *basic*. La regola *basic* è una distribuzione del carico di tipo round robin.

**Note aggiuntive sulla configurazione**

Per la configurazione dei certificati SSL, il protocollo in **HttpListener** deve essere sostituito con *Https* (distinzione tra maiuscole e minuscole). L'elemento **SslCertificate** deve essere aggiunto a **HttpListener** con il valore della variabile configurato per il certificato SSL. La porta front-end deve essere impostata su 443.

**Per abilitare l'affinità basata sui cookie**: è possibile configurare un gateway applicazione per fare in modo che una richiesta proveniente da una sessione client sia sempre diretta alla stessa macchina virtuale nella Web farm. A tale scopo viene aggiunto un cookie di sessione che consente al gateway di indirizzare il traffico in modo appropriato. Per abilitare l'affinità basata sui cookie, impostare **CookieBasedAffinity** su *Enabled* nell'elemento **BackendHttpSettings**.


## Creare un nuovo gateway applicazione

La differenza tra l'uso del modello di distribuzione di Azure classico e Gestione risorse di Azure sta nell'ordine in cui vengono creati il gateway applicazione e gli elementi da configurare.

Con Gestione risorse tutti gli elementi che costituiscono un gateway applicazione saranno configurati singolarmente e poi combinati per creare la risorsa del gateway applicazione.


Per creare un gateway applicazione, seguire questa procedura:

1. Creare un gruppo di risorse per Gestione risorse
2. Creare una rete virtuale, una subnet e un indirizzo IP pubblico per il gateway applicazione
3. Creare un oggetto di configurazione gateway applicazione
4. Creare una risorsa del gateway applicazione


## Creare un gruppo di risorse per Gestione risorse

Assicurarsi di passare alla modalità PowerShell per usare i cmdlet di Gestione risorse di Azure. Altre informazioni sono disponibili in [Uso di Windows PowerShell con Gestione risorse](../powershell-azure-resource-manager.md).

### Passaggio 1

		PS C:\> Login-AzureRmAccount



### Passaggio 2

Controllare le sottoscrizioni per l'account.

		PS C:\> get-AzureRmSubscription

Verrà richiesto di eseguire l'autenticazione con le proprie credenziali.<BR>

### Passaggio 3

Scegliere quali sottoscrizioni Azure usare. <BR>


		PS C:\> Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


### Passaggio 4

Creare un nuovo gruppo di risorse. Ignorare questo passaggio se si sta usando un gruppo di risorse esistente.

    New-AzureRmResourceGroup -Name appgw-rg -location "West US"

Gestione risorse di Azure richiede che tutti i gruppi di risorse specifichino un percorso che viene usato come percorso predefinito per le risorse presenti in tale gruppo di risorse. Assicurarsi che tutti i comandi per creare un gateway applicazione usino lo stesso gruppo di risorse.

Nell'esempio precedente è stato creato un gruppo di risorse denominato "appgw-RG" e la località "West US".

## Creare una rete virtuale e una subnet per il gateway applicazione

L'esempio seguente illustra come creare una rete virtuale usando Gestione risorse:

### Passaggio 1

	$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

Assegna l'intervallo di indirizzi 10.0.0.0/24 a una variabile di subnet da usare per creare una rete virtuale.

### Passaggio 2
	$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet

Crea una rete virtuale denominata "appgwvnet" nel gruppo di risorse "appgw-rg" per l'area Stati Uniti occidentali usando il prefisso 10.0.0.0/16 con subnet 10.0.0.0/24.

### Passaggio 3

	$subnet=$vnet.Subnets[0]

Assegna l'oggetto subnet alla variabile $subnet per i passaggi successivi.

## Creare un indirizzo IP pubblico per la configurazione front-end

	$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic

Crea una risorsa IP pubblica "publicIP01" nel gruppo di risorse "appgw-rg" per l'area Stati Uniti occidentali.


## Creare un oggetto di configurazione gateway applicazione

### Passaggio 1

	$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

Crea una configurazione IP del gateway applicazione denominata "gatewayIP01". All'avvio, il gateway applicazione seleziona un indirizzo IP dalla subnet configurata e indirizza il traffico di rete agli indirizzi IP nel pool di indirizzi IP back-end. Ogni istanza avrà un indirizzo IP.

### Passaggio 2

	$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50

Configura il pool di indirizzi IP back-end denominato "pool01" con gli indirizzi IP "134.170.185.46, 134.170.188.221,134.170.185.50". Quelli saranno gli indirizzi IP che ricevono il traffico di rete proveniente dall'endpoint IP front-end. Sostituire gli indirizzi IP dell'esempio precedente con gli indirizzi IP degli endpoint dell'applicazione Web.

### Passaggio 3

	$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Enabled

Configura l'impostazione "poolsetting01" del gateway applicazione per il traffico di rete con bilanciamento del carico nel pool back-end.

### Passaggio 4

	$fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 443

Configura la porta IP front-end denominata "frontendport01" per l'endpoint IP pubblico.

### Passaggio 5

	$cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path for certificate file> -Password ‘<password>’

Configura il certificato usato per la connessione SSL. Il certificato deve essere in formato PFX e la password deve essere compresa tra 4 e 12 caratteri.

### Passaggio 6

	$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

Crea la configurazione IP front-end denominata "fipconfig01" e associa l'indirizzo IP pubblico alla configurazione IP front-end.

### Passaggio 7

	$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert


Crea il listener denominato "listener01" e associa la porta front-end al certificato e alla configurazione IP front-end.

### Passaggio 8

	$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

Crea la regola di routing del dispositivo di bilanciamento del carico denominata "rule01" che configura il comportamento di bilanciamento del carico.

### Passaggio 9:

	$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

Configura le dimensioni dell'istanza del gateway applicazione.

>[AZURE.NOTE]  Il valore predefinito per *InstanceCount* è 2, con un valore massimo pari a 10. Il valore predefinito per *GatewaySize* è Medium. È possibile scegliere tra Standard\_Small, Standard\_Medium e Standard\_Large.

## Creare un gateway applicazione usando New-AzureApplicationGateway

	$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SslCertificates $cert

Crea un gateway applicazione con tutti gli elementi di configurazione illustrati nei passaggi precedenti. Nell'esempio il gateway applicazione è denominato "appgwtest".

## Passaggi successivi

Per configurare un gateway applicazione da usare con il dispositivo di bilanciamento del carico interno, vedere [Creare un gateway applicazione con un dispositivo di bilanciamento del carico interno (ILB)](application-gateway-ilb.md).

Per altre informazioni generali sulle opzioni di bilanciamento del carico, vedere:

- [Servizio di bilanciamento del carico di Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Gestione traffico di Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!---HONumber=AcomDC_0309_2016-->