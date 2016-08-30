<properties
   pageTitle="Creare e configurare un gateway applicazione con un dispositivo di bilanciamento del carico interno (ILB) tramite Gestione risorse di Azure | Microsoft Azure"
   description="Questa pagina fornisce istruzioni per la creazione, la configurazione, l'avvio e l'eliminazione di un gateway applicazione di Azure con un dispositivo di bilanciamento del carico interno (ILB) per Gestione risorse di Azure"
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
   ms.date="08/19/2016"
   ms.author="gwallace"/>


# Creare un gateway applicazione con un dispositivo di bilanciamento del carico interno (ILB) tramite Gestione risorse di Azure

> [AZURE.SELECTOR]
- [Procedure per Azure classico](application-gateway-ilb.md)
- [Procedure PowerShell per Resource Manager](application-gateway-ilb-arm.md)

Un gateway applicazione di Azure può essere configurato con un indirizzo VIP con connessione Internet o con un endpoint interno non esposto a Internet, detto anche endpoint del dispositivo di bilanciamento del carico interno (ILB). Configurare il gateway con un ILB è utile per le applicazioni line-of-business interne non esposte a Internet. È utile anche per servizi e livelli in un'applicazione a più livelli posti entro un limite di sicurezza non esposto a Internet, ma che richiedono la distribuzione del carico round robin, la persistenza delle sessioni o la terminazione Secure Sockets Layer (SSL).

In questo articolo verrà illustrata la procedura per configurare un gateway applicazione con un ILB.

## Prima di iniziare

1. Installare la versione più recente dei cmdlet di Azure PowerShell usando l'Installazione guidata piattaforma Web. È possibile scaricare e installare la versione più recente dalla sezione **Windows PowerShell** della [Pagina di download](https://azure.microsoft.com/downloads/).
2. Creare una rete virtuale e una subnet per il gateway applicazione. Assicurarsi che nessuna macchina virtuale o distribuzione cloud stia usando la subnet. Il gateway applicazione deve essere da solo in una subnet di rete virtuale.
3. È necessario che i server configurati per l'uso del gateway applicazione esistano oppure che i relativi endpoint siano stati creati nella rete virtuale o con un indirizzo IP/VIP pubblico assegnato.

## Elementi necessari per creare un gateway applicazione


- **Pool di server back-end:** elenco di indirizzi IP dei server back-end. Gli indirizzi IP elencati devono appartenere alla rete virtuale, ma devono trovarsi in una subnet diversa per il gateway applicazione, o devono essere un indirizzo IP/VIP pubblico.
- **Impostazioni del pool di server back-end:** ogni pool ha impostazioni quali porta, protocollo e affinità basata sui cookie. Queste impostazioni sono associate a un pool e vengono applicate a tutti i server nel pool.
- **Porta front-end:** porta pubblica aperta sul gateway applicazione. Il traffico raggiunge questa porta e quindi viene reindirizzato a uno dei server back-end.
- **Listener**: ha una porta front-end, un protocollo (Http o Https, con distinzione tra maiuscole e minuscole) e il nome del certificato SSL (se si configura l'offload SSL).
- **Regola**: associa il listener e il pool di server back-end e definisce il pool di server back-end a cui deve essere indirizzato il traffico quando raggiunge un listener specifico. È attualmente supportata solo la regola *basic*. La regola *basic* è una distribuzione del carico di tipo round robin.



## Creare un gateway applicazione

La differenza tra l'uso di Azure classico e di Azure Resource Manager risiede nell'ordine in cui vengono creati il gateway applicazione e gli elementi da configurare. Con Resource Manager tutti gli elementi che costituiscono un gateway applicazione vengono configurati singolarmente e quindi combinati per creare la risorsa del gateway applicazione.


Per creare un gateway applicazione, seguire questa procedura:

1. Creare un gruppo di risorse per Gestione risorse
2. Creare una rete virtuale e una subnet per il gateway applicazione
3. Creare un oggetto di configurazione gateway applicazione
4. Creare una risorsa del gateway applicazione


## Creare un gruppo di risorse per Gestione risorse

Assicurarsi di passare alla modalità PowerShell per usare i cmdlet di Gestione risorse di Azure. Altre informazioni sono disponibili in [Uso di Azure PowerShell con Azure Resource Manager](../powershell-azure-resource-manager.md).

### Passaggio 1

	Login-AzureRmAccount

### Passaggio 2

Controllare le sottoscrizioni per l'account.

	Get-AzureRmSubscription

Verrà richiesto di eseguire l'autenticazione con le proprie credenziali.<BR>

### Passaggio 3

Scegliere quali sottoscrizioni Azure usare. <BR>


	Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


### Passaggio 4

Creare un nuovo gruppo di risorse. Ignorare questo passaggio se si sta usando un gruppo di risorse esistente.

    New-AzureRmResourceGroup -Name appgw-rg -location "West US"

Gestione risorse di Azure richiede che tutti i gruppi di risorse specifichino un percorso che viene usato come percorso predefinito per le risorse presenti in tale gruppo di risorse. Assicurarsi che tutti i comandi per creare un gateway applicazione usino lo stesso gruppo di risorse.

Nell'esempio precedente è stato creato un gruppo di risorse denominato "appgw-rg" e la località "West US".

## Creare una rete virtuale e una subnet per il gateway applicazione

L'esempio seguente illustra come creare una rete virtuale usando Gestione risorse:

### Passaggio 1

	$subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

Assegna l'intervallo di indirizzi 10.0.0.0/24 a una variabile di subnet da usare per creare una rete virtuale.

### Passaggio 2

	$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnetconfig

Crea una rete virtuale denominata "appgwvnet" nel gruppo di risorse "appgw-rg" per l'area Stati Uniti occidentali usando il prefisso 10.0.0.0/16 con subnet 10.0.0.0/24.

### Passaggio 3

	$subnet = $vnet.subnets[0]

Assegna l'oggetto subnet alla variabile $subnet per i passaggi successivi.

## Creare un oggetto di configurazione gateway applicazione

### Passaggio 1

	$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

Crea una configurazione IP del gateway applicazione denominata "gatewayIP01". All'avvio, il gateway applicazione seleziona un indirizzo IP dalla subnet configurata e instrada il traffico di rete agli indirizzi IP nel pool di indirizzi IP back-end. Tenere presente che ogni istanza ha un indirizzo IP.


### Passaggio 2

	$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50

Configura il pool di indirizzi IP back-end denominato "pool01" con gli indirizzi IP "134.170.185.46, 134.170.188.221,134.170.185.50". Questi saranno gli indirizzi IP che ricevono il traffico di rete proveniente dall'endpoint IP front-end. Sostituire gli indirizzi IP precedenti per aggiungere gli endpoint di indirizzi IP dell'applicazione.

### Passaggio 3

	$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled

Configura l'impostazione "poolsetting01" del gateway applicazione per il traffico di rete con bilanciamento del carico nel pool back-end.

### Passaggio 4

	$fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80

Configura la porta IP front-end denominata "frontendport01" per il bilanciamento del carico interno.

### Passaggio 5

	$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -Subnet $subnet

Crea la configurazione IP front-end denominata "fipconfig01" e la associa a un indirizzo IP privato dalla subnet di rete virtuale corrente.

### Passaggio 6

	$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

Crea il listener denominato "listener01" e associa la porta front-end alla configurazione IP front-end.

### Passaggio 7

	$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

Crea la regola di routing del dispositivo di bilanciamento del carico denominata "rule01" che configura il comportamento di bilanciamento del carico.

### Passaggio 8

	$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

Configura le dimensioni dell'istanza del gateway applicazione.

>[AZURE.NOTE]  Il valore predefinito per *InstanceCount* è 2, con un valore massimo pari a 10. Il valore predefinito per *GatewaySize* è Medium. È possibile scegliere tra Standard\_Small, Standard\_Medium e Standard\_Large.

## Creare un gateway applicazione usando New-AzureApplicationGateway

Crea un gateway applicazione con tutti gli elementi di configurazione illustrati nei passaggi precedenti. Nell'esempio, il gateway applicazione è denominato "appgwtest".


	$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku

Crea un gateway applicazione con tutti gli elementi di configurazione illustrati nei passaggi precedenti. Nell'esempio il gateway applicazione è denominato "appgwtest".


## Eliminare un gateway applicazione

Per eliminare un gateway applicazione, sarà necessario eseguire i passaggi seguenti nell'ordine indicato:

1. Usare il cmdlet **Stop-AzureRmApplicationGateway** per arrestare il gateway.
2. Usare il cmdlet **Remove-AzureRmApplicationGateway** per rimuovere il gateway.
3. Verificare che il gateway sia stato rimosso usando il cmdlet **Get-AzureApplicationGateway**.


### Passaggio 1

Ottenere l'oggetto gateway applicazione e associarlo a una variabile "$getgw".

	$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

### Passaggio 2

Usare **Stop-AzureRmApplicationGateway** per arrestare il gateway applicazione. Questo esempio include il cmdlet **Stop-AzureRmApplicationGateway** alla prima riga, seguito dall'output.

	PS C:\> Stop-AzureRmApplicationGateway -ApplicationGateway $getgw  

	VERBOSE: 9:49:34 PM - Begin Operation: Stop-AzureApplicationGateway
	VERBOSE: 10:10:06 PM - Completed Operation: Stop-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error
	----       ----------------     ------------                             ----
	Successful OK                   ce6c6c95-77b4-2118-9d65-e29defadffb8

Quando il gateway applicazione è in stato di arresto, usare il cmdlet **Remove-AzureRmApplicationGateway** per rimuovere il servizio.


	PS C:\> Remove-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Force

	VERBOSE: 10:49:34 PM - Begin Operation: Remove-AzureApplicationGateway
	VERBOSE: 10:50:36 PM - Completed Operation: Remove-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error
	----       ----------------     ------------                             ----
	Successful OK                   055f3a96-8681-2094-a304-8d9a11ad8301

>[AZURE.NOTE] L'opzione **-force** può essere usata per eliminare il messaggio di conferma della rimozione.


Per verificare che il servizio sia stato rimosso, è possibile usare il cmdlet **Get-AzureRmApplicationGateway**. Questo passaggio non è obbligatorio.


	PS C:\>Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

	VERBOSE: 10:52:46 PM - Begin Operation: Get-AzureApplicationGateway

	Get-AzureApplicationGateway : ResourceNotFound: The gateway does not exist.
	.....

## Passaggi successivi

Per configurare l'offload SSL, vedere [Configurare un gateway applicazione per l'offload SSL](application-gateway-ssl.md).

Per configurare un gateway applicazione da usare con un servizio di bilanciamento del carico interno, vedere [Creare un gateway applicazione con un servizio di bilanciamento del carico interno (ILB)](application-gateway-ilb.md).

Per altre informazioni generali sulle opzioni di bilanciamento del carico, vedere:

- [Servizio di bilanciamento del carico di Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Gestione traffico di Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!---HONumber=AcomDC_0824_2016-->