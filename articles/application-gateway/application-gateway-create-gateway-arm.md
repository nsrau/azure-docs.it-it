<properties 
   pageTitle="Creare, avviare o eliminare un Gateway applicazione utilizzando Gestione risorse di Azure | Microsoft Azure"
   description="Questa pagina fornisce istruzioni per la creazione, la configurazione, l'avvio e l'eliminazione di un gateway applicazione di Azure utilizzando Gestione risorse di Azure."
   documentationCenter="na"
   services="application-gateway"
   authors="joaoma"
   manager="jdial"
   editor="tysonn"/>
<tags 
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="hero-article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="11/24/2015"
   ms.author="joaoma"/>


# Creare, avviare o eliminare un Gateway applicazione utilizzando Gestione risorse di Azure

Il gateway applicazione è un servizio di bilanciamento del carico di livello 7. Fornisce servizi di failover e richieste HTTP di routing delle prestazioni tra server diversi, sia che si trovino sul cloud che in locale. Il gateway applicazione offre le funzionalità di distribuzione delle applicazioni seguenti: bilanciamento del carico HTTP, affinità delle sessioni basata sui cookie, offload SSL.


> [AZURE.SELECTOR]
- [Azure Classic PowerShell steps](application-gateway-create-gateway.md)
- [Azure Resource Manager PowerShell](application-gateway-create-gateway-arm.md)
- [Azure Resource Manager template ](application-gateway-create-gateway-arm-template.md)


<BR>


Questo articolo illustra in modo dettagliato i passaggi necessari per creare e configurare, avviare ed eliminare un gateway applicazione.


>[AZURE.IMPORTANT]Prima di lavorare con le risorse di Azure, è importante comprendere che Azure attualmente funziona con due modelli di distribuzione: Gestione delle risorse e Classico. È importante conoscere i [modelli e gli strumenti di distribuzione](azure-classic-rm.md) prima di usare le risorse di Azure. È possibile visualizzare la documentazione relativa a diversi strumenti facendo clic sulle schede nella parte superiore di questo articolo. Questo documento illustrerà la creazione di un gateway applicazione tramite Gestione risorse di Azure. Per usare la versione classica, passare all'articolo relativo alla [creazione di una distribuzione classica del gateway applicazione tramite PowerShell](application-gateway-create-gateway.md).



## Prima di iniziare

1. Installare la versione più recente dei cmdlet di Azure PowerShell mediante l'Installazione guidata piattaforma Web. È possibile scaricare e installare la versione più recente dalla sezione **Windows PowerShell** della pagina [Download](http://azure.microsoft.com/downloads/).
2. Si creerà una rete virtuale e una subnet per il Gateway Applicazione. Assicurarsi che nessuna macchina virtuale o distribuzione cloud stia usando la subnet. Il gateway applicazione deve essere da solo in una subnet di rete virtuale.
3. È necessario che i server che si configureranno per l'uso del gateway applicazione esistano oppure che i loro endpoint siano stati creati nella rete virtuale o che sia stato loro assegnato un indirizzo VIP/IP pubblico.

## Che cosa è necessario per creare un gateway applicazione?
 

- **Pool di server back-end:** l’elenco di indirizzi IP dei server back-end. Gli indirizzi IP elencati devono appartenere alla subnet della rete virtuale o devono essere indirizzi IP/VIP pubblici. 
- **Impostazioni del pool di server back-end**: ogni pool ha impostazioni quali porta, protocollo e affinità basata sui cookie. Queste impostazioni sono associate a un pool e vengono applicate a tutti i server nel pool.
- **Porta front-end**: è la porta pubblica aperta sul gateway applicazione. Il traffico raggiunge questa porta e quindi viene reindirizzato a uno dei server back-end.
- **Listener:** il listener ha una porta front-end, un protocollo (Http o Https, con applicazione della distinzione tra maiuscole e minuscole) e il nome del certificato SSL (se si configura l'offload SSL). 
- **Regola:** la regola associa il listener e il pool di server back-end e definisce il pool di server back-end a cui deve essere indirizzato il traffico quando raggiunge un listener specifico. È attualmente supportata solo la regola *basic*. La regola *basic* è una distribuzione del carico di tipo round robin.


 
## Creare un nuovo gateway applicazione

La differenza tra utilizzare la gestione risorse di Azure e Azure classico sarà l'ordine in cui che si creerà il gateway applicazione e gli elementi necessari da configurare.

Con Gestione risorse, tutti gli elementi che creeranno una Gateway Applicazione saranno configurati singolarmente e poi riuniti per creare la risorsa di Gateway Applicazione.


Di seguito i passaggi necessari per creare un Gateway applicazione:

1. Creare un gruppo di risorse per Gestione risorse
2. Creare una rete virtuale, una subnet e un IP pubblico per Gateway Applicazione
3. Creare un oggetto di configurazione di Gateway applicazione
4. Creare una risorsa per il gateway applicazione


## Creare un gruppo di risorse per Gestione risorse

Assicurarsi di usare la versione più recente di Azure PowerShell. Altre informazioni sono disponibili in Uso di [Windows PowerShell con Gestione risorse](powershell-azure-resource-manager.md).

### Passaggio 1

		Login-AzureRmAccount



### Passaggio 2

Controllare le sottoscrizioni per l'account

		Get-AzureRmSubscription 

Verrà richiesto di eseguire l'autenticazione con le proprie credenziali.<BR>

### Passaggio 3 

Scegliere quali sottoscrizioni Azure usare. <BR>


		Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


### Passaggio 4

Creare un nuovo gruppo di risorse (ignorare questo passaggio se si usa un gruppo di risorse esistente)

    New-AzureRmResourceGroup -Name appgw-rg -location "West US"

Gestione risorse di Azure richiede che tutti i gruppi di risorse specifichino un percorso che viene usato come percorso predefinito per le risorse presenti in tale gruppo di risorse. Assicurarsi che tutti i comandi per creare un Gateway Applicazione usino lo stesso gruppo di risorse.

Nell'esempio precedente è stato creato un gruppo di risorse denominato "appgw-RG" e la località "West US".


>[AZURE.NOTE]Se è necessario configurare un probe personalizzato per il gateway applicazione, vedere l'articolo [Creare un gateway applicazione con probe personalizzati tramite PowerShell](application-gateway-create-probe-ps.md). Per altre informazioni, vedere [Probe personalizzati e monitoraggio dello stato](application-gateway-probe-overview.md).



## Crea rete virtuale e subnet per il Gateway applicazione

Nell'esempio seguente viene illustrato come creare una rete virtuale utilizzando risorse di gestione:

### Passaggio 1	
	
Assegnare l’intervallo di indirizzo 10.0.0.0/24 al subnet variabile da utilizzare per creare una rete virtuale

	$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24


### Passaggio 2	

Crea una rete virtuale denominata "appgwvnet" nel gruppo di risorse "appgw-rg" per l'area Stati Uniti occidentali usando il prefisso 10.0.0.0/16 con subnet 10.0.0.0/24

	$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet


### Passaggio 3
	
Assegnare una variabile di subnet per la creazione di un gateway applicazione nei passaggi successivi

	$subnet=$vnet.Subnets[0]

## Creare l'indirizzo IP pubblico per la configurazione front-end

Crea una risorsa IP pubblica "publicIP01" nel gruppo di risorse "appgw-rg" per l'area Stati Uniti occidentali.

	$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic


## Creare un oggetto di configurazione di Gateway applicazione

È necessario impostare tutti gli elementi di configurazione prima di creare il gateway applicazione dell'applicazione. La procedura seguente crea gli elementi di configurazione necessari per una risorsa del gateway applicazione

### Passaggio 1

Crea una configurazione IP del Gateway applicazione denominata "gatewayIP01". Quando viene avviato il Gateway applicazione, preleverà un indirizzo IP dalla subnet configurata e indirizzerà il traffico di rete per gli indirizzi IP nel pool di IP back-end. Tenere in considerazione che ogni istanza avrà un indirizzo IP.


	$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

 
### Passaggio 2

Questo passaggio configurerà pool di indirizzi IP back-end denominati "pool01" con gli indirizzi IP "134.170.185.46, 134.170.188.221,134.170.185.50". Quelli saranno gli indirizzi IP che riceveranno il traffico di rete proveniente dall'endpoint IP front-end. Sostituire gli indirizzi IP precedenti per aggiungere il proprio endpoint dell’indirizzo IP dell'applicazione.

	$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50



### Passaggio 3

Configura le impostazioni del gateway applicazione "poolsetting01" per il traffico di rete del carico bilanciato nel pool di back-end.

	$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled


### Passaggio 4

Configura la porta IP front-end denominata "frontendport01" in questo caso, per l'endpoint IP pubblico.

	$fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80


### Passaggio 5

Crea la configurazione IP di front-end denominata "fipconfig01" associando l'indirizzo IP pubblico con la configurazione IP di front-end.

	$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip


### Passaggio 6

Crea il listener denominato "listener01" associando la porta front-end alla configurazione IP front-end.

	$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

### Passaggio 7 

Crea la regola routing del servizio di bilanciamento del carico denominata "rule01" configurando il comportamento del bilanciamento del carico.

	$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

### Passaggio 8

Configura le dimensioni dell'istanza del Gateway Applicazione

	$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

>[AZURE.NOTE]Il valore predefinito per *InstanceCount* è 2, con un valore massimo di 10. Il valore predefinito per *GatewaySize* è Medium. È possibile scegliere tra Standard\_Small, Standard\_Medium e Standard\_Large.

## Creare un gateway applicazione con New-AzureRmApplicationGateway

Crea un Gateway applicazione con tutti gli elementi di configurazione da passaggi precedenti. Nell'esempio il Gateway Applicazione è denominato "appgwtest".

	$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku


## Eliminare un gateway applicazione

Per eliminare un gateway applicazione, sarà necessario eseguire i passaggi seguenti nell'ordine indicato:

1. Usare il cmdlet `Stop-AzureRmApplicationGateway` per arrestare il gateway. 
2. Usare il cmdlet `Remove-AzureRmApplicationGateway` per rimuovere il gateway.
3. Assicurarsi che il gateway sia stato rimosso usando il cmdlet `Get-AzureRmApplicationGateway`.

### Passaggio 1

Ottenere l'oggetto Gateway Applicazione e associare a una variabile "$getgw":
 
	$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

### Passaggio 2
	 
Usare `Stop-AzureRmApplicationGateway` per arrestare il gateway applicazione:

	Stop-AzureRmApplicationGateway -ApplicationGateway $getgw  


Quando lo stato del gateway applicazione è Arrestato, usare il cmdlet `Remove-AzureRmApplicationGateway` per rimuovere il servizio.


	Remove-AzureRmApplicationGateway -Name $appgwtest -ResourceGroupName appgw-rg -Force

	

>[AZURE.NOTE]L’opzione"-force" può essere utilizzata per eliminare il messaggio di conferma di rimozione


Per verificare che il servizio sia stato rimosso, è possibile usare il cmdlet `Get-AzureRmApplicationGateway`. Questo passaggio non è obbligatorio.


	Get-AzureRmApplicationGateway -Name appgwtest-ResourceGroupName appgw-rg

	
## Passaggi successivi

Per configurare l'offload SSL, vedere [Configurare un gateway applicazione per l'offload SSL](application-gateway-ssl.md).

Per configurare un gateway applicazione per l'uso con ILB, vedere [Creare un gateway applicazione con un dispositivo di bilanciamento del carico interno (ILB)](application-gateway-ilb.md).

Per altre informazioni generali sulle opzioni di bilanciamento del carico, vedere:

- [Servizio di bilanciamento del carico di Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Gestione traffico di Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!---HONumber=AcomDC_0107_2016-->