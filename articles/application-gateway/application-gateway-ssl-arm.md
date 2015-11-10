<properties 
   pageTitle="Configurare un gateway applicazione per l'offload SSL tramite Gestione risorse di Azure | Microsoft Azure"
   description="Questa pagina fornisce istruzioni per la creazione di un gateway applicazione con offload SSL tramite Gestione risorse di Azure."
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
   ms.date="10/28/2015"
   ms.author="joaoma"/>

# Configurare un gateway applicazione per l'offload SSL tramite Gestione risorse di Azure


 Gateway applicazione può essere configurato per terminare la sessione SSL nel gateway, evitando così una costosa decrittografia SSL nella Web farm. L'offload SSL semplifica anche la configurazione e la gestione del server front-end dell'applicazione Web.


>[AZURE.IMPORTANT]Prima di lavorare con le risorse di Azure, è importante comprendere che Azure attualmente funziona con due modelli di distribuzione: Gestione delle risorse e Classico. È importante comprendere i [modelli e gli strumenti di distribuzione](azure-classic-rm.md) prima di utilizzare una risorsa di Azure. È possibile visualizzare la documentazione relativa a diversi strumenti facendo clic sulle schede nella parte superiore di questo articolo. Questo documento illustrerà la creazione di un gateway applicazione tramite Gestione risorse di Azure. Per usare la versione classica, passare all'articolo [Configurare un gateway applicazione per l'offload SSL tramite PowerShell (classico)](application-gateway-ssl.md).



## Prima di iniziare

1. Installare la versione più recente dei cmdlet di Azure PowerShell mediante l'Installazione guidata piattaforma Web. È possibile scaricare e installare la versione più recente dalla sezione **Windows PowerShell** della pagina [Download](http://azure.microsoft.com/downloads/).
2. Si creerà una rete virtuale e una subnet per il Gateway Applicazione. Assicurarsi che nessuna macchina virtuale o distribuzione cloud stia usando la subnet. Il gateway applicazione deve essere da solo in una subnet di rete virtuale.
3. È necessario che i server che si configureranno per l'uso del gateway applicazione esistano oppure che i loro endpoint siano stati creati nella rete virtuale o che sia stato loro assegnato un indirizzo VIP/IP pubblico.

## Che cosa è necessario per creare un gateway applicazione?
 

- **Pool di server back-end**: elenco di indirizzi IP dei server back-end. Gli indirizzi IP elencati devono appartenere alla subnet della rete virtuale o devono essere indirizzi IP/VIP pubblici. 
- **Impostazioni del pool di server back-end**: ogni pool ha impostazioni quali porta, protocollo e affinità basata sui cookie. Queste impostazioni sono associate a un pool e vengono applicate a tutti i server nel pool.
- **Porta front-end**: è la porta pubblica aperta sul gateway applicazione. Il traffico raggiunge questa porta e quindi viene reindirizzato a uno dei server back-end.
- **Listener**: ha una porta front-end, un protocollo (Http o Https che fa distinzione tra maiuscole e minuscole) e il nome del certificato SSL (se si configura l'offload SSL). 
- **Regola**: associa il listener e il pool di server back-end e definisce il pool di server back-end a cui deve essere indirizzato il traffico quando raggiunge un listener specifico. Attualmente è supportata solo la regola *di base*. La regola *di base* è una distribuzione del carico di tipo round robin.

**Note aggiuntive sulla configurazione:**

Per la configurazione dei certificati SSL, il protocollo in **HttpListener** deve essere sostituito con *Https* (distinzione tra maiuscole e minuscole). L'elemento **SslCertificate** deve essere aggiunto a **HttpListener** con il valore della variabile configurato per il certificato SSL. La porta front-end deve essere aggiornata in 443.

**Per abilitare l'affinità basata sui cookie**: un gateway applicazione può essere configurato per verificare che una richiesta proveniente da una sessione client sia sempre indirizzata alla stessa VM nella Web farm. A tale scopo viene usata l'aggiunta di un cookie di sessione che consente al gateway di indirizzare il traffico in modo appropriato. Per abilitare l'affinità basata sui cookie, impostare **CookieBasedAffinity** su *Enabled* nell'elemento **BackendHttpSettings**.

 
## Creare un nuovo gateway applicazione

La differenza tra utilizzare la gestione risorse di Azure e Azure classico sarà l'ordine in cui che si creerà il gateway applicazione e gli elementi necessari da configurare.

Con Gestione risorse, tutti gli elementi che creeranno una Gateway Applicazione saranno configurati singolarmente e poi riuniti per creare la risorsa di Gateway Applicazione.


Di seguito i passaggi necessari per creare un Gateway applicazione:

1. Creare un gruppo di risorse per Gestione risorse
2. Creare una rete virtuale, una subnet e un IP pubblico per Gateway Applicazione
3. Creare un oggetto di configurazione di Gateway applicazione
4. Creare una risorsa per il gateway applicazione


## Creare un gruppo di risorse per Gestione risorse

Verificare di passare alla modalità di PowerShell per usare i cmdlet ARM. Altre informazioni sono disponibili in [Uso di Azure PowerShell con Gestione risorse di Azure](powershell-azure-resource-manager.md).

### Passaggio 1

    Switch-AzureMode -Name AzureResourceManager

### Passaggio 2

Accedere all'account Azure.


    Add-AzureAccount

Verrà richiesto di eseguire l'autenticazione con le proprie credenziali.


### Passaggio 3

Scegliere le sottoscrizioni ad Azure da utilizzare.

    Select-AzureSubscription -SubscriptionName "MySubscription"

Per visualizzare un elenco di sottoscrizioni disponibili, utilizzare il cmdlet "Get-AzureSubscription".


### Passaggio 4

Creare un nuovo gruppo di risorse (ignorare questo passaggio se si usa un gruppo di risorse esistente)

    New-AzureResourceGroup -Name appgw-rg -location "West US"

Gestione risorse di Azure richiede che tutti i gruppi di risorse specifichino un percorso che viene usato come percorso predefinito per le risorse presenti in tale gruppo di risorse. Assicurarsi che tutti i comandi per creare un Gateway Applicazione usino lo stesso gruppo di risorse.

Nell'esempio precedente è stato creato un gruppo di risorse denominato "appgw-RG" e la località "West US".

## Crea rete virtuale e subnet per il Gateway applicazione

Nell'esempio seguente viene illustrato come creare una rete virtuale utilizzando risorse di gestione:

### Passaggio 1	
	
	$subnet = New-AzureVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

Assegna l'intervallo di indirizzi 10.0.0.0/24 a una variabile di subnet da usare per creare una rete virtuale.

### Passaggio 2	
	$vnet = New-AzurevirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet

Crea una rete virtuale denominata "appgwvnet" nel gruppo di risorse "appw-rg" per l'area Stati Uniti occidentali usando il prefisso 10.0.0.0/16 con subnet 10.0.0.0/24

### Passaggio 3

	$subnet=$vnet.Subnets[0]

L'oggetto subnet viene assegnato alla variabile $subnet per i passaggi successivi.
	
## Creare l'indirizzo IP pubblico per la configurazione front-end

	$publicip = New-AzurePublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic

Crea una risorsa IP pubblica "publicIP01" nel gruppo di risorse "appw-rg" per l'area Stati Uniti occidentali.


## Creare un oggetto di configurazione di Gateway applicazione

### Passaggio 1

	$gipconfig = New-AzureApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

Crea una configurazione IP del gateway applicazione denominata "gatewayIP01". Quando viene avviato il gateway applicazione, ottiene un indirizzo IP dalla subnet configurata e indirizza il traffico di rete agli indirizzi IP nel pool IP di back-end. Tenere in considerazione che ogni istanza avrà un indirizzo IP.
 
### Passaggio 2

	$pool = New-AzureApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50

Questo passaggio configura il pool di indirizzi IP di back-end denominato "pool01" con gli indirizzi IP "134.170.185.46, 134.170.188.221,134.170.185.50". Quelli saranno gli indirizzi IP che riceveranno il traffico di rete proveniente dall'endpoint IP front-end. Sostituire gli indirizzi IP dell'esempio precedente con gli indirizzi IP degli endpoint dell'applicazione Web.

### Passaggio 3

	$poolSetting = New-AzureApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Enabled

Configura le impostazioni del gateway applicazione "poolsetting01" per il traffico di rete con bilanciamento del carico nel pool di back-end.

### Passaggio 4

	$fp = New-AzureApplicationGatewayFrontendPort -Name frontendport01  -Port 443

Configura la porta IP front-end denominata "frontendport01", in questo caso, per l'endpoint IP pubblico.

### Passaggio 5 

	$cert = New-AzureApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path for certificate file> -Password ‘<password>’

Configura il certificato usato per la connessione SSL. Il certificato deve essere in formato PFX e la password compresa tra 4 e 12 caratteri.

### Passaggio 6

	$fipconfig = New-AzureApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

Crea la configurazione IP front-end denominata "fipconfig01" e associa l'indirizzo IP pubblico con la configurazione IP front-end.

### Passaggio 7

	$listener = New-AzureApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert


Crea il listener denominato "listener01" e associa la porta front-end al certificato e alla configurazione IP front-end.

### Passaggio 8 

	$rule = New-AzureApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

Crea la regola routing del servizio di bilanciamento del carico denominata "rule01", configurando il comportamento di bilanciamento del carico.

### Passaggio 9:

	$sku = New-AzureApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

Configura le dimensioni dell'istanza del gateway applicazione.

>[AZURE.NOTE]Il valore predefinito per *InstanceCount* è 2, con un valore massimo pari a 10. Il valore predefinito per *GatewaySize* è Medium. È possibile scegliere tra Standard\_Small, Standard\_Medium e Standard\_Large.

## Creare un Gateway Applicazione utilizzando New-AzureApplicationGateway

	$appgw = New-AzureApplicationGateway -Name appgwtest -ResourceGroupName appw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SslCertificates $cert

Crea un gateway applicazione con tutti gli elementi di configurazione dai passaggi precedenti. Nell'esempio il gateway applicazione è denominato "appgwtest".


## Avvia il Gateway applicazione

Dopo la configurazione del gateway, usare il cmdlet `Start-AzureApplicationGateway` per avviarlo. La fatturazione per un gateway applicazione verrà applicata a partire dall'avvio corretto del gateway.


**Nota:** il completamento del cmdlet `Start-AzureApplicationGateway` potrebbe richiedere fino a 15-20 minuti.

Per l'esempio riportato di seguito, il Gateway applicazione è denominato "appgwtest" e il gruppo di risorse è "app-rg":


### Passaggio 1

Ottenere l'oggetto Gateway Applicazione e associare a una variabile "$getgw":
 
	$getgw =  Get-AzureApplicationGateway -Name appgwtest -ResourceGroupName app-rg

### Passaggio 2
	 
Usare `Start-AzureApplicationGateway` per avviare il gateway applicazione:

	 Start-AzureApplicationGateway -ApplicationGateway $getgw  

	

## Verificare lo stato del Gateway Applicazione

Usare il cmdlet `Get-AzureApplicationGateway` per verificare lo stato del gateway. Se *Start-AzureApplicationGateway* è riuscito nel passaggio precedente, lo stato dovrebbe essere *In esecuzione* e i valori di Vip e DnsName dovrebbero essere validi.

Questo esempio illustra un gateway applicazione attivo, in esecuzione e pronto per accettare il traffico destinato a `http://<generated-dns-name>.cloudapp.net`.

	Get-AzureApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

	Sku                               : Microsoft.Azure.Commands.Network.Models.PSApplicationGatewaySku
	GatewayIPConfigurations           : {gatewayip01}
	SslCertificates                   : {}
	FrontendIPConfigurations          : {frontendip01}
	FrontendPorts                     : {frontendport01}
	BackendAddressPools               : {pool01}
	BackendHttpSettingsCollection     : {setting01}
	HttpListeners                     : {listener01}
	RequestRoutingRules               : {rule01}
	OperationalState                  : 
	ProvisioningState                 : Succeeded
	GatewayIpConfigurationsText       : [
                                      {
                                        "Subnet": {
                                          "Id": "/subscriptions/###############################/resourceGroups/appgw-rg
                                    /providers/Microsoft.Network/virtualNetworks/vnet01/subnets/subnet01"
                                        },
                                        "ProvisioningState": "Succeeded",
                                        "Name": "gatewayip01",
                                        "Etag": "W/"ddb0408e-a54c-4501-a7f8-8487c3530bd7"",
                                        "Id": "/subscriptions/###############################/resourceGroups/appgw-rg/p
                                    roviders/Microsoft.Network/applicationGateways/appgwtest/gatewayIPConfigurations/gatewayip
                                    01"
                                      }
                                    ]
	SslCertificatesText               : []
	FrontendIpConfigurationsText      : [
                                      {
                                        "PrivateIPAddress": null,
                                        "PrivateIPAllocationMethod": "Dynamic",
                                        "Subnet": null,
                                        "PublicIPAddress": {
                                          "Id": "/subscriptions/###############################/resourceGroups/appgw-rg
                                    /providers/Microsoft.Network/publicIPAddresses/publicip01"
                                        },
                                        "ProvisioningState": "Succeeded",
                                        "Name": "frontendip01",
                                        "Etag": "W/"ddb0408e-a54c-4501-a7f8-8487c3530bd7"",
                                        "Id": "/subscriptions/###############################/resourceGroups/appgw-rg/p
                                    roviders/Microsoft.Network/applicationGateways/appgwtest/frontendIPConfigurations/frontend
                                    ip01"
                                      }
                                    ]
	FrontendPortsText                 : [
                                      {
                                        "Port": 80,
                                        "ProvisioningState": "Succeeded",
                                        "Name": "frontendport01",
                                        "Etag": "W/"ddb0408e-a54c-4501-a7f8-8487c3530bd7"",
                                        "Id": "/subscriptions/###############################/resourceGroups/appgw-rg/p
                                    roviders/Microsoft.Network/applicationGateways/appgwtest/frontendPorts/frontendport01"
                                      }
                                    ]
	BackendAddressPoolsText           : [
                                      {
                                        "BackendAddresses": [
                                          {
                                            "Fqdn": null,
                                            "IpAddress": "134.170.185.46"
                                          },
                                          {
                                            "Fqdn": null,
                                            "IpAddress": "134.170.188.221"
                                          },
                                          {
                                            "Fqdn": null,
                                            "IpAddress": "134.170.185.50"
                                          }
                                        ],
                                        "BackendIpConfigurations": [],
                                        "ProvisioningState": "Succeeded",
                                        "Name": "pool01",
                                        "Etag": "W/"ddb0408e-a54c-4501-a7f8-8487c3530bd7"",
                                        "Id": "/subscriptions/###############################/resourceGroups/appgw-rg/p
                                    roviders/Microsoft.Network/applicationGateways/appgwtest/backendAddressPools/pool01"
                                      }
                                    ]
	BackendHttpSettingsCollectionText : [
                                      {
                                        "Port": 80,
                                        "Protocol": "Http",
                                        "CookieBasedAffinity": "Disabled",
                                        "ProvisioningState": "Succeeded",
                                        "Name": "setting01",
                                        "Etag": "W/"ddb0408e-a54c-4501-a7f8-8487c3530bd7"",
                                        "Id": "/subscriptions/###############################/resourceGroups/appgw-rg/p
                                    roviders/Microsoft.Network/applicationGateways/appgwtest/backendHttpSettingsCollection/set
                                    ting01"
                                      }
                                    ]
	HttpListenersText                 : [
                                      {
                                        "FrontendIpConfiguration": {
                                          "Id": "/subscriptions/###############################/resourceGroups/appgw-rg
                                    /providers/Microsoft.Network/applicationGateways/appgwtest/frontendIPConfigurations/fronte
                                    ndip01"
                                        },
                                        "FrontendPort": {
                                          "Id": "/subscriptions/###############################/resourceGroups/appgw-rg
                                    /providers/Microsoft.Network/applicationGateways/appgwtest/frontendPorts/frontendport01"
                                        },
                                        "Protocol": "Http",
                                        "SslCertificate": null,
                                        "ProvisioningState": "Succeeded",
                                        "Name": "listener01",
                                        "Etag": "W/"ddb0408e-a54c-4501-a7f8-8487c3530bd7"",
                                        "Id": "/subscriptions/###############################/resourceGroups/appgw-rg/p
                                    roviders/Microsoft.Network/applicationGateways/appgwtest/httpListeners/listener01"
                                      }
                                    ]
	RequestRoutingRulesText           : [
                                      {
                                        "RuleType": "Basic",
                                        "BackendAddressPool": {
                                          "Id": "/subscriptions/###############################/resourceGroups/appgw-rg
                                    /providers/Microsoft.Network/applicationGateways/appgwtest/backendAddressPools/pool01"
                                        },
                                        "BackendHttpSettings": {
                                          "Id": "/subscriptions/###############################/resourceGroups/appgw-rg
                                    /providers/Microsoft.Network/applicationGateways/appgwtest/backendHttpSettingsCollection/s
                                    etting01"
                                        },
                                        "HttpListener": {
                                          "Id": "/subscriptions/###############################/resourceGroups/appgw-rg
                                    /providers/Microsoft.Network/applicationGateways/appgwtest/httpListeners/listener01"
                                        },
                                        "ProvisioningState": "Succeeded",
                                        "Name": "rule01",
                                        "Etag": "W/"ddb0408e-a54c-4501-a7f8-8487c3530bd7"",
                                        "Id": "/subscriptions/###############################/resourceGroups/appgw-rg/p
                                    roviders/Microsoft.Network/applicationGateways/appgwtest/requestRoutingRules/rule01"
                                      }
                                    ]
	ResourceGroupName                 : appgw-rg
	Location                          : westus
	Tag                               : {}
	TagsTable                         : 
	Name                              : appgwtest
	Etag                              : W/"ddb0408e-a54c-4501-a7f8-8487c3530bd7"
	Id                                : /subscriptions/###############################/resourceGroups/appgw-rg/providers/Microsoft.Network/applicationGateways/appgwtest




## Passaggi successivi


Per configurare un gateway applicazione da usare con ILB, vedere [Creare un gateway applicazione con un dispositivo di bilanciamento del carico interno (ILB)](application-gateway-ilb.md).

Per altre informazioni generali sulle opzioni di bilanciamento del carico, vedere:

- [Servizio di bilanciamento del carico di Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Gestione traffico di Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!---HONumber=Nov15_HO2-->