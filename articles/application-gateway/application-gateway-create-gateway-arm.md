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
   ms.date="08/07/2015"
   ms.author="joaoma"/>


# Creare, avviare o eliminare un Gateway applicazione utilizzando Gestione risorse di Azure

> [AZURE.SELECTOR]
- [Azure classic steps](application-gateway-create-gateway.md)
- [Resource Manager Powershell steps](application-gateway-create-gateway-arm.md)

In questa versione è possibile creare un gateway applicazione usando PowerShell o le chiamate API REST. Il supporto per il portale e per CLI sarà disponibile in una versione futura. Questo articolo illustra in modo dettagliato i passaggi necessari per creare e configurare, avviare ed eliminare un gateway applicazione.

## Prima di iniziare

1. Installare la versione più recente dei cmdlet di Azure PowerShell mediante l'Installazione guidata piattaforma Web. È possibile scaricare e installare la versione più recente dalla sezione **Windows PowerShell** della [Pagina di download](http://azure.microsoft.com/downloads/).
2. Si creerà una rete virtuale e subnet per il Gateway applicazione. Assicurarsi che nessuna macchina virtuale o distribuzione cloud stia usando la subnet. Il gateway applicazione deve essere da solo in una subnet di rete virtuale.
3. È necessario che i server che si configureranno per l'uso del gateway applicazione esistano oppure che i loro endpoint siano stati creati nella rete virtuale o che sia stato loro assegnato un indirizzo VIP/IP pubblico.

## Che cosa è necessario per creare un gateway applicazione?
 

- **Pool di server back-end:** elenco di indirizzi IP dei server back-end. Gli indirizzi IP elencati devono appartenere alla subnet della rete virtuale o devono essere indirizzi IP/VIP pubblici. 
- **Impostazioni del pool di server back-end:** ogni pool ha impostazioni quali porta, protocollo e affinità basata sui cookie. Queste impostazioni sono associate a un pool e vengono applicate a tutti i server nel pool.
- **Porta front-end:** questa porta è la porta pubblica aperta sul gateway applicazione. Il traffico raggiunge questa porta e quindi viene reindirizzato a uno dei server back-end.
- **Listener:** il listener ha una porta front-end, un protocollo (Http o Https, con applicazione della distinzione tra maiuscole e minuscole) e il nome del certificato SSL (se si configura l'offload SSL). 
- **Regola:** la regola associa il listener e il pool di server back-end e definisce il pool di server back-end a cui deve essere indirizzato il traffico quando raggiunge un listener specifico. È attualmente supportata solo la regola *basic*. La regola *basic* è una distribuzione del carico di tipo round robin.


 
## Creare un nuovo gateway applicazione

La differenza tra utilizzare la gestione risorse di Azure e Azure classico sarà l'ordine in cui che si creerà il gateway di applicazione e gli elementi necessari da configurare.

Con Gestione risorse, tutti gli elementi che creeranno una Gateway Applicazione saranno configurati singolarmente e poi riuniti per creare la risorsa di Gateway Applicazione.


Di seguito i passaggi necessari per creare un Gateway applicazione:

1. Creare un gruppo di risorse per Gestione risorse
2. Creare una rete virtuale, una subnet e un IP pubblico per Gateway Applicazione
3. Creare un oggetto di configurazione di Gateway applicazione
4. Creare una risorsa per il gateway applicazione


## Creare un gruppo di risorse per Gestione risorse

Verificare di passare alla modalità di PowerShell per usare i cmdlet ARM. Altre informazioni sono disponibili in [Uso di Windows PowerShell con Gestione risorse](powershell-azure-resource-manager.md).

### Passaggio 1

    PS C:\> Switch-AzureMode -Name AzureResourceManager

### Passaggio 2

Accedere all'account Azure.


    PS C:\> Add-AzureAccount

Verrà richiesto di eseguire l'autenticazione con le proprie credenziali.


### Passaggio 3

Scegliere le sottoscrizioni ad Azure da utilizzare.

    PS C:\> Select-AzureSubscription -SubscriptionName "MySubscription"

Per visualizzare un elenco di sottoscrizioni disponibili, utilizzare il cmdlet "Get-AzureSubscription".


### Passaggio 4

Creare un nuovo gruppo di risorse (ignorare questo passaggio se si usa un gruppo di risorse esistente)

    PS C:\> New-AzureResourceGroup -Name appgw-rg -location "West US"

Gestione risorse di Azure richiede che tutti i gruppi di risorse specifichino un percorso che viene usato come percorso predefinito per le risorse presenti in tale gruppo di risorse. Assicurarsi che tutti i comandi per creare un Gateway Applicazione usino lo stesso gruppo di risorse.

Nell'esempio precedente è stato creato un gruppo di risorse denominato "appgw-RG" e la località "West US".

## Crea rete virtuale e subnet per il Gateway applicazione

Nell'esempio seguente viene illustrato come creare una rete virtuale utilizzando risorse di gestione:

### Passaggio 1	
	
	$subnet = New-AzureVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

Assegnare l’intervallo di indirizzo 10.0.0.0/24 al subnet variabile da utilizzare per creare una rete virtuale

### Passaggio 2	
	$vnet = New-AzurevirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet

Crea una rete virtuale denominata "appgwvnet" nel gruppo di risorsa "appw-rg" per l'area Stati Uniti occidentali utilizzando il prefisso 10.0.0.0/16 con subnet 10.0.0.0/24
	
## Creare l'indirizzo IP pubblico per la configurazione front-end

	$publicip = New-AzurePublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic

Crea una risorsa IP pubblica "publicIP01" nella risorsa gruppo "appw-rg" per l'area Stati Uniti occidentali.


## Creare un oggetto di configurazione di Gateway applicazione

### Passaggio 1

	$gipconfig = New-AzureApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

Crea una configurazione IP del Gateway applicazione denominata "gatewayIP01". Quando viene avviato il Gateway applicazione, preleverà un indirizzo IP dalla subnet configurata e indirizzerà il traffico di rete per gli indirizzi IP nel pool di IP back-end. Tenere in considerazione che ogni istanza avrà un indirizzo IP.
 
### Passaggio 2

	$pool = New-AzureApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50

Questo passaggio configurerà pool di indirizzi IP back-end denominati "pool01" con gli indirizzi IP "134.170.185.46, 134.170.188.221,134.170.185.50". Quelli saranno gli indirizzi IP che riceveranno il traffico di rete proveniente dall'endpoint IP front-end. Sostituire gli indirizzi IP precedenti per aggiungere il proprio endpoint dell’indirizzo IP dell'applicazione.

### Passaggio 3

	$poolSetting = New-AzureApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol HTTP -CookieBasedAffinity Disabled

Configura le impostazioni del gateway applicazione "poolsetting01" per il traffico di rete del carico bilanciato nel pool di back-end.

### Passaggio 4

	$fp = New-AzureApplicationGatewayFrontendPort -Name frontendport01  -Port 80

Configura la porta IP front-end denominata "frontendport01" in questo caso, per l'endpoint IP pubblico.

### Passaggio 5

	$fipconfig = New-AzureApplicationGatewayFrontendIPConfig -Name $fipconfigName -PublicIPAddress $publicip

Crea la configurazione IP di front-end associando l'indirizzo IP pubblico con la configurazione IP di front-end.

### Passaggio 6

	$listener = New-AzureApplicationGatewayHttpListener -Name $listenerName  -Protocol http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

Crea il listener a cui associare la porta di front-end per la configurazione IP di front-end.

### Passaggio 7 

	$rule = New-AzureApplicationGatewayRequestRoutingRule -Name $ruleName -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

Crea la regola routing del servizio di bilanciamento del carico, configurando il comportamento di bilanciamento del carico.

### Passaggio 8

	$sku = New-AzureApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

Consente di configurare le dimensioni dell'istanza del Gateway applicazione

>[AZURE.NOTE]Il valore predefinito per *InstanceCount* è 2, con un valore massimo di 10. Il valore predefinito per *GatewaySize* è Medium. È possibile scegliere tra Small, Medium e Large.

## Creare un Gateway applicazione utilizzando New-AzureApplicationGateway

	$appgw = New-AzureApplicationGateway -Name appgwtest -ResourceGroupName $rgname -Location $location -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku

Crea un Gateway applicazione con tutti gli elementi di configurazione da passaggi precedenti. Nell'esempio il Gateway applicazione è denominato "appgwtest".




## Avvia il Gateway applicazione

Dopo la configurazione del gateway, usare il cmdlet `Start-AzureApplicationGateway` per avviarlo. La fatturazione per un gateway applicazione verrà applicata a partire dall'avvio corretto del gateway.


**Nota:** il completamento del cmdlet `Start-AzureApplicationGateway` potrebbe richiedere fino a 15-20 minuti.

Per l'esempio riportato di seguito, il Gateway applicazione è denominato "appgwtest" e il gruppo di risorse è "app-rg":


### Passaggio 1

Ottenere l'oggetto Gateway Applicazione e associare a una variabile "$getgw":
 
	$getgw =  Get-AzureApplicationGateway -Name appgwtest -ResourceGroupName app-rg

### Passaggio 2
	 
Utilizzare `Start-AzureApplicationGateway`per avviare il Gateway Applicazione:

	PS C:\> Start-AzureApplicationGateway -ApplicationGateway $getgw  

	PS C:\> Start-AzureApplicationGateway AppGwTest 

	VERBOSE: 7:59:16 PM - Begin Operation: Start-AzureApplicationGateway 
	VERBOSE: 8:05:52 PM - Completed Operation: Start-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error 
	----       ----------------     ------------                             ----
	Successful OK                   fc592db8-4c58-2c8e-9a1d-1c97880f0b9b

## Verificare lo stato del Gateway Applicazione

Usare il cmdlet `Get-AzureApplicationGateway` per verificare lo stato del gateway. Se *Start-AzureApplicationGateway* ha avuto esito positivo nel passaggio precedente, lo stato dovrebbe essere *In esecuzione* e i valori di Vip e DnsName dovrebbero essere validi.

Questo esempio illustra un gateway applicazione attivo, in esecuzione e pronto per accettare il traffico destinato a `http://<generated-dns-name>.cloudapp.net`.

	PS C:\> Get-AzureApplicationGateway -Name appgwtest -ResourceGroupName app-rg

	VERBOSE: 8:09:28 PM - Begin Operation: Get-AzureApplicationGateway 
	VERBOSE: 8:09:30 PM - Completed Operation: Get-AzureApplicationGateway
	Name          : AppGwTest 
	Description   : 
	VnetName      : appgwvnet 
	Subnets       : {Subnet01} 
	InstanceCount : 2 
	GatewaySize   : Medium 
	State         : Running 
	Vip           : 138.91.170.26 
	DnsName       : appgw-1b8402e8-3e0d-428d-b661-289c16c82101.cloudapp.net


## Eliminare un Gateway Applicazione

Per eliminare un gateway applicazione, sarà necessario eseguire i passaggi seguenti nell'ordine indicato:

1. Usare il cmdlet `Stop-AzureApplicationGateway` per arrestare il gateway. 
2. Usare il cmdlet `Remove-AzureApplicationGateway` per rimuovere il gateway.
3. Assicurarsi che il gateway sia stato rimosso usando il cmdlet `Get-AzureApplicationGateway`.

Questo esempio illustra il cmdlet `Stop-AzureApplicationGateway` nella prima riga, seguito dall'output.

### Passaggio 1

Ottenere l'oggetto Gateway Applicazione e associare a una variabile "$getgw":
 
	$getgw =  Get-AzureApplicationGateway -Name appgwtest -ResourceGroupName app-rg

### Passaggio 2
	 
Utilizzare `Stop-AzureApplicationGateway`per arrestare il Gateway Applicazione:

	PS C:\> Stop-AzureApplicationGateway -ApplicationGateway $getgw  

	VERBOSE: 9:49:34 PM - Begin Operation: Stop-AzureApplicationGateway 
	VERBOSE: 10:10:06 PM - Completed Operation: Stop-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error 
	----       ----------------     ------------                             ----
	Successful OK                   ce6c6c95-77b4-2118-9d65-e29defadffb8

Quando lo stato del gateway applicazione è Arrestato, usare il cmdlet `Remove-AzureApplicationGateway` per rimuovere il servizio.


	PS C:\> Remove-AzureApplicationGateway -Name $appgwName -ResourceGroupName $rgname -Force

	VERBOSE: 10:49:34 PM - Begin Operation: Remove-AzureApplicationGateway 
	VERBOSE: 10:50:36 PM - Completed Operation: Remove-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error 
	----       ----------------     ------------                             ----
	Successful OK                   055f3a96-8681-2094-a304-8d9a11ad8301

>[AZURE.NOTE]L’opzione"-force" può essere utilizzata per eliminare il messaggio di conferma di rimozione
>

Per verificare che il servizio sia stato rimosso, è possibile usare il cmdlet `Get-AzureApplicationGateway`. Questo passaggio non è obbligatorio.


	PS C:\>Get-AzureApplicationGateway -Name appgwtest-ResourceGroupName app-rg

	VERBOSE: 10:52:46 PM - Begin Operation: Get-AzureApplicationGateway 

	Get-AzureApplicationGateway : ResourceNotFound: The gateway does not exist. 
	.....

## Passaggi successivi

Per configurare l'offload SSL, vedere [Configurare un gateway applicazione per l'offload SSL](application-gateway-ssl.md).

Per configurare un gateway applicazione per l'uso con ILB, vedere [Creare un gateway applicazione con un dispositivo di bilanciamento del carico interno (ILB)](application-gateway-ilb.md).

Per altre informazioni generali sulle opzioni di bilanciamento del carico, vedere:

- [Servizio di bilanciamento del carico di Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Gestione traffico di Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!---HONumber=August15_HO7-->