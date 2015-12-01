<properties 
   pageTitle="Configurare un gateway applicazione per l'offload SSL tramite Gestione risorse di Azure | Microsoft Azure"
   description="Questa pagina fornisce istruzioni per creare un gateway applicazione con offload SSL tramite Gestione risorse di Azure"
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

# Configurare un gateway applicazione per l'offload SSL tramite Gestione risorse di Azure

> [AZURE.SELECTOR]
-[Azure Classic Powershell](application-gateway-ssl.md)
-[Azure Resource Manager Powershell](application-gateway-ssl-arm.md)

 Gateway applicazione può essere configurato per terminare la sessione SSL nel gateway, evitando così una costosa decrittografia SSL nella Web farm. L'offload SSL semplifica anche la configurazione e la gestione del server front-end dell'applicazione Web.


>[AZURE.IMPORTANT]Prima di iniziare a usare le risorse di Azure, è importante comprendere che Azure al momento offre due modelli di distribuzione, Gestione risorse e modello di distribuzione classico. È importante comprendere i [modelli e strumenti di distribuzione](azure-classic-rm.md) prima di usare qualsiasi risorsa di Azure. Per visualizzare la documentazione relativa a ogni strumento, fare clic sulle schede nella parte superiore dell'articolo. Questo documento illustra come creare un gateway applicazione con Gestione risorse di Azure. Per usare la versione del modello di distribuzione classico, passare all'articolo [Configurare un gateway applicazione per l'offload SSL tramite la distribuzione classica di Azure](application-gateway-ssl.md).



## Prima di iniziare

1. Installare la versione più recente dei cmdlet di Azure PowerShell mediante l'Installazione guidata piattaforma Web. È possibile scaricare e installare la versione più recente dalla sezione **Windows PowerShell** della [pagina Download](http://azure.microsoft.com/downloads/).
2. Verranno create una rete virtuale e una subnet per il gateway applicazione. Assicurarsi che nessuna macchina virtuale o distribuzione cloud usi la subnet. Nella subnet della rete virtuale deve essere presente solo il gateway applicazione.
3. È necessario che i server che si configureranno per l'uso del gateway applicazione esistano oppure che i relativi endpoint siano stati creati nella rete virtuale o che sia stato assegnato loro un indirizzo IP/VIP pubblico.

## Elementi necessari per creare un gateway applicazione
 

- **Pool di server back-end:** elenco di indirizzi IP dei server back-end. Gli indirizzi IP elencati devono appartenere alla subnet della rete virtuale o devono essere indirizzi IP/VIP pubblici. 
- **Impostazioni del pool di server back-end:** per ogni pool sono disponibili impostazioni quali porta, protocollo e affinità basata sui cookie. Queste impostazioni sono associate a un pool e vengono applicate a tutti i server nel pool.
- **Porta front-end:** questa porta è la porta pubblica aperta sul gateway applicazione. Il traffico raggiunge questa porta e quindi viene reindirizzato a uno dei server back-end.
- **Listener:** il listener ha una porta front-end, un protocollo (Http o Https che fa distinzione tra maiuscole e minuscole) e il nome del certificato SSL (se si configura l'offload SSL). 
- **Regola:** la regola associa il listener e il pool di server back-end e definisce il pool di server back-end a cui deve essere indirizzato il traffico quando raggiunge un listener specifico. È attualmente supportata solo la regola *basic*. La regola *basic* è una distribuzione del carico di tipo round robin.

**Note aggiuntive sulla configurazione:**

Per la configurazione dei certificati SSL, il protocollo in **HttpListener** deve essere sostituito con *Https* (distinzione tra maiuscole e minuscole). L'elemento **SslCertificate** deve essere aggiunto a **HttpListener** con il valore della variabile configurato per il certificato SSL. La porta front-end deve essere aggiornata in 443.

**Per abilitare l'affinità basata su cookie**: un gateway applicazione può essere configurato per verificare che una richiesta proveniente da una sessione client sia sempre diretta alla stessa macchina virtuale nella Web farm. A tale scopo viene usata l'aggiunta di un cookie di sessione che consente al gateway di indirizzare il traffico in modo appropriato. Per abilitare l'affinità basata su cookie, impostare **CookieBasedAffinity** su *Enabled* nell'elemento **BackendHttpSettings**.

 
## Creare un nuovo gateway applicazione

La differenza tra l'uso del modello di distribuzione classico di Azure e Gestione risorse di Azure sta nell'ordine in cui verrà creato il gateway applicazione e gli elementi necessari da configurare.

Con Gestione risorse, tutti gli elementi che costituiscono un gateway applicazione saranno configurati singolarmente e poi combinati per creare la risorsa gateway applicazione.


Per creare un gateway applicazione, seguire questa procedura:

1. Creare un gruppo di risorse per Gestione risorse
2. Creare una rete virtuale, una subnet e un indirizzo IP pubblico per il gateway applicazione
3. Creare un oggetto di configurazione gateway applicazione
4. Creare una risorsa gateway applicazione


## Creare un gruppo di risorse per Gestione risorse

Verificare di passare alla modalità di PowerShell per usare i cmdlet ARM. Altre informazioni sono disponibili in Uso di [Windows PowerShell con Gestione risorse](powershell-azure-resource-manager.md).

### Passaggio 1

		PS C:\> Login-AzureRmAccount



### Passaggio 2

Controllare le sottoscrizioni per l'account

		PS C:\> get-AzureRmSubscription 

Verrà richiesto di eseguire l'autenticazione con le proprie credenziali.<BR>

### Passaggio 3 

Scegliere quali sottoscrizioni Azure usare. <BR>


		PS C:\> Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


### Passaggio 4

Creare un nuovo gruppo di risorse (ignorare questo passaggio se si usa un gruppo di risorse esistente)

    New-AzureRmResourceGroup -Name appgw-rg -location "West US"

Gestione risorse di Azure richiede che tutti i gruppi di risorse specifichino un percorso che viene usato come percorso predefinito per le risorse presenti in tale gruppo di risorse. Assicurarsi che tutti i comandi per creare un gateway applicazione usino lo stesso gruppo di risorse.

Nell'esempio precedente è stato creato un gruppo di risorse denominato "appgw-RG" e la località "West US".

## Creare la rete virtuale e la subnet per il gateway applicazione

Nell'esempio seguente viene illustrato come creare una rete virtuale utilizzando risorse di gestione:

### Passaggio 1	
	
	$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

Assegna l'intervallo di indirizzi 10.0.0.0/24 a una variabile di subnet da usare per creare una rete virtuale.

### Passaggio 2	
	$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet

Crea una rete virtuale denominata "appgwvnet" nel gruppo di risorse "appw-rg" per l'area Stati Uniti occidentali usando il prefisso 10.0.0.0/16 con subnet 10.0.0.0/24

### Passaggio 3

	$subnet=$vnet.Subnets[0]

L'oggetto subnet viene assegnato alla variabile $subnet per i passaggi successivi.
	
## Creare l'indirizzo IP pubblico per la configurazione front-end

	$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic

Crea una risorsa IP pubblica "publicIP01" nel gruppo di risorse "appw-rg" per l'area Stati Uniti occidentali.


## Creare un oggetto di configurazione gateway applicazione

### Passaggio 1

	$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

Questo passaggio crea una configurazione IP del gateway applicazione denominata "gatewayIP01". All'avvio, il gateway applicazione selezionerà un indirizzo IP dalla subnet configurata e indirizza il traffico di rete agli indirizzi IP nel pool IP back-end. Tenere in considerazione che ogni istanza avrà un indirizzo IP.
 
### Passaggio 2

	$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50

Questo passaggio configura il pool di indirizzi IP di back-end denominato "pool01" con gli indirizzi IP "134.170.185.46, 134.170.188.221,134.170.185.50". Quelli saranno gli indirizzi IP che riceveranno il traffico di rete proveniente dall'endpoint IP front-end. Sostituire gli indirizzi IP dell'esempio precedente con gli indirizzi IP degli endpoint dell'applicazione Web.

### Passaggio 3

	$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Enabled

Questo passaggio configura le impostazioni del gateway applicazione "poolsetting01" per il traffico di rete con bilanciamento del carico nel pool back-end.

### Passaggio 4

	$fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 443

Configura la porta IP front-end denominata "frontendport01", in questo caso, per l'endpoint IP pubblico.

### Passaggio 5 

	$cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path for certificate file> -Password ‘<password>’

Configura il certificato usato per la connessione SSL. Il certificato deve essere in formato PFX e la password compresa tra 4 e 12 caratteri.

### Passaggio 6

	$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

Crea la configurazione IP front-end denominata "fipconfig01" e associa l'indirizzo IP pubblico con la configurazione IP front-end.

### Passaggio 7

	$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert


Crea il listener denominato "listener01" e associa la porta front-end al certificato e alla configurazione IP front-end.

### Passaggio 8 

	$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

Crea la regola routing del servizio di bilanciamento del carico denominata "rule01", configurando il comportamento di bilanciamento del carico.

### Passaggio 9:

	$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

Questo passaggio configura le dimensioni dell'istanza del gateway applicazione.

>[AZURE.NOTE]Il valore predefinito per *InstanceCount* è 2, con un valore massimo pari a 10. Il valore predefinito per *GatewaySize* è Medium. È possibile scegliere tra Standard\_Small, Standard\_Medium e Standard\_Large.

## Creare un gateway applicazione usando il metodo New-AzureApplicationGateway

	$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SslCertificates $cert

Questo metodo crea un gateway applicazione con tutti gli elementi di configurazione illustrati nei passaggi precedenti. Nell'esempio il gateway applicazione è denominato "appgwtest".


## Avviare il gateway applicazione

Dopo la configurazione del gateway, usare il cmdlet `Start-AzureRmApplicationGateway` per avviarlo. La fatturazione per un gateway applicazione verrà applicata a partire dall'avvio corretto del gateway.


**Nota:** il completamento del cmdlet `Start-AzureRmApplicationGateway` potrebbe richiedere fino a 15-20 minuti.

Per l'esempio seguente il gateway applicazione è denominato "appgwtest" e il gruppo di risorse è "app-rg":


### Passaggio 1

Ottenere l'oggetto gateway applicazione e associarlo a una variabile "$getgw":
 
	$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName app-rg

### Passaggio 2
	 
Usare `Start-AzureRmApplicationGateway` per avviare il gateway applicazione:

	 Start-AzureRmApplicationGateway -ApplicationGateway $getgw  

	

## Verificare lo stato del gateway applicazione

Usare il cmdlet `Get-AzureRmApplicationGateway` per verificare lo stato del gateway. Se nel passaggio precedente l'operazione *Start-AzureApplicationGateway* è riuscita, lo stato risulterà *Operazione riuscita*.

	Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

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

Per configurare un gateway applicazione per l'uso con ILB, vedere [Creare un gateway applicazione con un dispositivo di bilanciamento del carico interno (ILB)](application-gateway-ilb.md).

Per altre informazioni generali sulle opzioni di bilanciamento del carico, vedere:

- [Servizio di bilanciamento del carico di Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Gestione traffico di Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!---HONumber=AcomDC_1125_2015-->