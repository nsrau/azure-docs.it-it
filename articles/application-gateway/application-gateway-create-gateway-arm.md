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


>[AZURE.IMPORTANT]Prima di lavorare con le risorse di Azure, è importante comprendere che Azure attualmente funziona con due modelli di distribuzione: Gestione delle risorse e Classico. È importante comprendere i [modelli e strumenti di distribuzione](azure-classic-rm.md) prima di lavorare con le risorse di Azure. È possibile visualizzare la documentazione relativa a diversi strumenti facendo clic sulle schede nella parte superiore di questo articolo. Questo documento illustrerà la creazione di un gateway applicazione tramite Gestione risorse di Azure. Per usare la versione classica, passare all'articolo relativo alla [creazione di una distribuzione classica del gateway applicazione tramite PowerShell](application-gateway-create-gateway.md).



## Prima di iniziare

1. Installare la versione più recente dei cmdlet di Azure PowerShell mediante l'Installazione guidata piattaforma Web. È possibile scaricare e installare la versione più recente dalla sezione **Windows PowerShell** della [pagina di download](http://azure.microsoft.com/downloads/).
2. Si creerà una rete virtuale e una subnet per il Gateway Applicazione. Assicurarsi che nessuna macchina virtuale o distribuzione cloud stia usando la subnet. Il gateway applicazione deve essere da solo in una subnet di rete virtuale.
3. È necessario che i server che si configureranno per l'uso del gateway applicazione esistano oppure che i loro endpoint siano stati creati nella rete virtuale o che sia stato loro assegnato un indirizzo VIP/IP pubblico.

## Che cosa è necessario per creare un gateway applicazione?
 

- **Pool di server back-end:** l’elenco di indirizzi IP dei server back-end. Gli indirizzi IP elencati devono appartenere alla subnet della rete virtuale o devono essere indirizzi IP/VIP pubblici. 
- **Impostazioni del pool di server back-end:** ogni pool ha impostazioni quali porta, protocollo e affinità basata sui cookie. Queste impostazioni sono associate a un pool e vengono applicate a tutti i server nel pool.
- **Porta front-end:** questa porta è la porta pubblica aperta sul gateway applicazione. Il traffico raggiunge questa porta e quindi viene reindirizzato a uno dei server back-end.
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

Gestione risorse di Azure richiede che tutti i gruppi di risorse specifichino un percorso che viene usato come percorso predefinito per le risorse presenti in tale gruppo di risorse. Assicurarsi che tutti i comandi per creare un Gateway Applicazione usino lo stesso gruppo di risorse.

Nell'esempio precedente è stato creato un gruppo di risorse denominato "appgw-RG" e la località "West US".

## Crea rete virtuale e subnet per il Gateway applicazione

Nell'esempio seguente viene illustrato come creare una rete virtuale utilizzando risorse di gestione:

### Passaggio 1	
	
	$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

Assegnare l’intervallo di indirizzo 10.0.0.0/24 al subnet variabile da utilizzare per creare una rete virtuale

### Passaggio 2	
	$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet

Crea una rete virtuale denominata "appgwvnet" nel gruppo di risorsa "appw-rg" per l'area Stati Uniti occidentali utilizzando il prefisso 10.0.0.0/16 con subnet 10.0.0.0/24

### Passaggio 3
	
	$subnet=$vnet.Subnets[0]

## Creare l'indirizzo IP pubblico per la configurazione front-end

	$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic

Crea una risorsa IP pubblica "publicIP01" nella risorsa gruppo "appw-rg" per l'area Stati Uniti occidentali.


## Creare un oggetto di configurazione di Gateway applicazione

### Passaggio 1

	$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

Crea una configurazione IP del Gateway applicazione denominata "gatewayIP01". Quando viene avviato il Gateway applicazione, preleverà un indirizzo IP dalla subnet configurata e indirizzerà il traffico di rete per gli indirizzi IP nel pool di IP back-end. Tenere in considerazione che ogni istanza avrà un indirizzo IP.
 
### Passaggio 2

	$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50

Questo passaggio configurerà pool di indirizzi IP back-end denominati "pool01" con gli indirizzi IP "134.170.185.46, 134.170.188.221,134.170.185.50". Quelli saranno gli indirizzi IP che riceveranno il traffico di rete proveniente dall'endpoint IP front-end. Sostituire gli indirizzi IP precedenti per aggiungere il proprio endpoint dell’indirizzo IP dell'applicazione.

### Passaggio 3

	$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled

Configura le impostazioni del gateway applicazione "poolsetting01" per il traffico di rete del carico bilanciato nel pool di back-end.

### Passaggio 4

	$fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80

Configura la porta IP front-end denominata "frontendport01" in questo caso, per l'endpoint IP pubblico.

### Passaggio 5

	$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

Crea la configurazione IP di front-end denominata "fipconfig01" associando l'indirizzo IP pubblico con la configurazione IP di front-end.

### Passaggio 6

	$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

Crea il listener denominato "listener01" associando la porta front-end alla configurazione IP front-end.

### Passaggio 7 

	$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

Crea la regola routing del servizio di bilanciamento del carico denominata "rule01" configurando il comportamento del bilanciamento del carico.

### Passaggio 8

	$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

Configura le dimensioni dell'istanza del Gateway Applicazione

>[AZURE.NOTE]Il valore predefinito per *InstanceCount* è 2, con un valore massimo pari a 10. Il valore predefinito per *GatewaySize* è Medium. È possibile scegliere tra Standard\_Small, Standard\_Medium e Standard\_Large.

## Creare un Gateway Applicazione utilizzando New-AzureApplicationGateway

	$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku

Crea un Gateway applicazione con tutti gli elementi di configurazione da passaggi precedenti. Nell'esempio il Gateway applicazione è denominato "appgwtest".


## Avvia il Gateway applicazione

Dopo la configurazione del gateway, usare il cmdlet `Start-AzureRmApplicationGateway` per avviarlo. La fatturazione per un gateway applicazione verrà applicata a partire dall'avvio corretto del gateway.


**Nota:** il completamento del cmdlet `Start-AzureRmApplicationGateway` potrebbe richiedere fino a 15-20 minuti.

Per l'esempio riportato di seguito, il Gateway applicazione è denominato "appgwtest" e il gruppo di risorse è "app-rg":


### Passaggio 1

Ottenere l'oggetto Gateway Applicazione e associare a una variabile "$getgw":
 
	$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName app-rg

### Passaggio 2
	 
Usare `Start-AzureRmApplicationGateway` per avviare il gateway applicazione:

	 Start-AzureRmApplicationGateway -ApplicationGateway $getgw  

	

## Verificare lo stato del Gateway Applicazione

Usare il cmdlet `Get-AzureRmApplicationGateway` per verificare lo stato del gateway. Se nel passaggio precedente l'azione *Start-AzureApplicationGateway* è riuscita, lo stato risulterà *In esecuzione* e le voci per l'indirizzo VIP e DnsName saranno valide.

Questo esempio illustra un gateway applicazione attivo, in esecuzione e pronto per accettare il traffico destinato a `http://<generated-dns-name>.cloudapp.net`.

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
>

Per verificare che il servizio sia stato rimosso, è possibile usare il cmdlet `Get-AzureRmApplicationGateway`. Questo passaggio non è obbligatorio.


	Get-AzureRmApplicationGateway -Name appgwtest-ResourceGroupName appgw-rg

	


## Passaggi successivi

Per configurare l'offload SSL, vedere [Configurare un gateway applicazione per l'offload SSL](application-gateway-ssl.md).

Per configurare un gateway applicazione per l'uso con ILB, vedere [Creare un gateway applicazione con un dispositivo di bilanciamento del carico interno (ILB)](application-gateway-ilb.md).

Per altre informazioni generali sulle opzioni di bilanciamento del carico, vedere:

- [Servizio di bilanciamento del carico di Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Gestione traffico di Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!---HONumber=AcomDC_1203_2015-->