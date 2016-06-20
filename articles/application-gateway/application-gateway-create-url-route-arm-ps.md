<properties
   pageTitle="Creare un gateway applicazione con le regole per il routing basato su URL | Microsoft Azure"
   description="Questa pagina fornisce istruzioni per la creazione e la configurazione di un gateway applicazione di Azure con le regole per il routing basato su URL"
   documentationCenter="na"
   services="application-gateway"
   authors="joaoma"
   manager="jdial"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/10/2016"
   ms.author="joaoma"/>


# Creare un gateway applicazione con il routing basato su URL 

Il routing basato su percorso URL consente di associare le route in base al percorso URL della richiesta HTTP. Verifica se è disponibile una route per il pool back-end configurato per gli elenchi di URL nel gateway applicazione e invia il traffico di rete al pool back-end definito. In genere il routing basato su URL viene usato per bilanciare il carico delle richieste per diversi tipi di contenuto tra vari pool di server back-end.

Il routing basato su URL introduce un nuovo tipo di regola per i gateway applicazione. Per il gateway applicazione sono disponibili due tipi di regole: basic e PathBasedRouting. Il tipo di regola basic fornisce un servizio di tipo round robin per i pool back-end, mentre la regola PathBasedRouting oltre alla distribuzione round robin tiene conto del modello di percorso dell'URL della richiesta per la scelta del pool back-end.

>[AZURE.IMPORTANT] PathPattern: elenco dei modelli di percorso usati per la corrispondenza. Ognuno di loro deve iniziare con una barra / e l’unica posizione in cui è consentito il simbolo * è alla fine. Alcuni esempi validi: /xyz, /xyz* o /xyz/*. La stringa inviata al selettore di percorsi non include alcun testo dopo il primo simbolo ? o # e questi caratteri non sono consentiti.

## Scenario
Nell'esempio seguente il gateway applicazione gestisce il traffico per contoso.com con due pool di server back-end: pool di server video e pool di server immagini.

Le richieste per http://contoso.com/image* verranno indirizzate al pool di server immagini (pool1) e quelle per http://contoso.com/video* al pool di server video (pool2). In caso di mancata corrispondenza dei percorsi, viene selezionato un pool di server predefinito (pool1).

![route dell'URL](./media/application-gateway-create-url-route-arm-ps/figure1.png)

## Prima di iniziare

1. Installare la versione più recente dei cmdlet di Azure PowerShell usando l'Installazione guidata piattaforma Web. È possibile scaricare e installare la versione più recente dalla sezione **Windows PowerShell** della [pagina Download](https://azure.microsoft.com/downloads/).
2. Creare una rete virtuale e una subnet per il Gateway Applicazione. Assicurarsi che nessuna macchina virtuale o distribuzione cloud stia usando la subnet. Il gateway applicazione deve essere da solo in una subnet di rete virtuale.
3. È necessario che i server aggiunti al pool back-end per l'uso del gateway applicazione esistano oppure che i relativi endpoint siano stati creati nella rete virtuale o che sia stato assegnato loro un indirizzo IP/VIP pubblico.



## Elementi necessari per creare un gateway applicazione


- **Pool di server back-end:** elenco di indirizzi IP dei server back-end. Gli indirizzi IP elencati devono appartenere alla subnet della rete virtuale o devono essere indirizzi IP/VIP pubblici.
- **Impostazioni del pool di server back-end:** ogni pool ha impostazioni quali porta, protocollo e affinità basata sui cookie. Queste impostazioni sono associate a un pool e vengono applicate a tutti i server nel pool.
- **Porta front-end:** porta pubblica aperta sul gateway applicazione. Il traffico raggiunge questa porta e quindi viene reindirizzato a uno dei server back-end.
- **Listener**: ha una porta front-end, un protocollo (HTTP o HTTPS che fa distinzione tra maiuscole e minuscole) e il nome del certificato SSL (se si configura l'offload SSL).
- **Regola**: associa il listener e il pool di server back-end e definisce il pool di server back-end a cui deve essere indirizzato il traffico quando raggiunge un listener specifico.

## Creare un nuovo gateway applicazione

La differenza tra l'uso di Azure classico e Gestione risorse di Azure sta nell'ordine in cui vengono creati il gateway applicazione e gli elementi da configurare.

Con Gestione risorse tutti gli elementi che costituiscono un gateway applicazione saranno configurati singolarmente e poi combinati per creare la risorsa del gateway applicazione.


Per creare un gateway applicazione, seguire questa procedura:

1. Creare un gruppo di risorse per Gestione risorse.
2. Creare una rete virtuale, una subnet e un indirizzo IP pubblico per il gateway applicazione.
3. Creare un oggetto di configurazione del gateway applicazione.
4. Creare una risorsa del gateway applicazione.

## Creare un gruppo di risorse per Gestione risorse

Assicurarsi di usare la versione più recente di Azure PowerShell. Altre informazioni sono disponibili in [Uso di Windows PowerShell con Azure Resource Manager](../powershell-azure-resource-manager.md).

### Passaggio 1
Accedere all'account Login-AzureRmAccount di Azure

Verrà richiesto di eseguire l'autenticazione con le proprie credenziali.<BR>

### Passaggio 2
Controllare le sottoscrizioni per l'account.

		Get-AzureRmSubscription

### Passaggio 3
Scegliere quali sottoscrizioni Azure usare. <BR>


		Select-AzureRmSubscription -Subscriptionid "GUID of subscription"

### Passaggio 4
Creare un nuovo gruppo di risorse. Ignorare questo passaggio se si sta usando un gruppo di risorse esistente.

    New-AzureRmResourceGroup -Name appgw-RG -location "West US"
In alternativa, è anche possibile creare tag del gruppo di risorse per il gateway applicazione:
	
	$resourceGroup = New-AzureRmResourceGroup -Name appgw-RG -Location "West US" -Tags @{Name = "testtag"; Value = "Application Gateway URL routing"} 

Gestione risorse di Azure richiede che tutti i gruppi di risorse specifichino un percorso che viene usato come percorso predefinito per le risorse presenti in tale gruppo di risorse. Assicurarsi che tutti i comandi per creare un gateway applicazione usino lo stesso gruppo di risorse.

Nell'esempio precedente è stato creato un gruppo di risorse denominato "appgw-RG" e la località "West US".

>[AZURE.NOTE] Se è necessario configurare un probe personalizzato per il gateway applicazione, vedere l'articolo relativo alla [creazione di un gateway applicazione con probe personalizzati tramite PowerShell](application-gateway-create-probe-ps.md). Per altre informazioni, vedere l'articolo relativo a [probe personalizzati e monitoraggio dell'integrità](application-gateway-probe-overview.md).

## Creare una rete virtuale e una subnet per il gateway applicazione

L'esempio seguente illustra come creare una rete virtuale usando Gestione risorse.

### Passaggio 1
Assegnare l'intervallo di indirizzi 10.0.0.0/24 alla variabile di subnet da usare per creare una rete virtuale.

	$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24


### Passaggio 2
Creare una rete virtuale denominata "appgwvnet" nel gruppo di risorse "appgw-rg" per l'area Stati Uniti occidentali usando il prefisso 10.0.0.0/16 con subnet 10.0.0.0/24.

	$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet


### Passaggio 3
Assegnare una variabile di subnet per la creazione di un gateway applicazione nei passaggi successivi.

	$subnet=$vnet.Subnets[0]

## Creare un indirizzo IP pubblico per la configurazione front-end
Creare una risorsa IP pubblica "publicIP01" nel gruppo di risorse "appgw-rg" per l'area Stati Uniti occidentali.

	$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -name publicIP01 -location "West US" -AllocationMethod Dynamic

Verrà assegnato un indirizzo IP al gateway applicazione all'avvio del servizio.

## Creare la configurazione del gateway applicazione

È necessario impostare tutti gli elementi di configurazione prima di creare il gateway applicazione. La procedura seguente consente di creare gli elementi di configurazione necessari per una risorsa del gateway applicazione.

### Passaggio 1
Creare una configurazione IP del gateway applicazione denominata "gatewayIP01". All'avvio, il gateway applicazione seleziona un indirizzo IP dalla subnet configurata e indirizza il traffico di rete agli indirizzi IP nel pool di indirizzi IP back-end. Ogni istanza avrà un indirizzo IP.


	$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet


### Passaggio 2
Configurare i pool di indirizzi IP back-end denominati "pool01" con gli indirizzi IP "134.170.185.46, 134.170.188.221,134.170.185.50" e "pool2" con gli indirizzi IP "134.170.186.46, 134.170.189.221,134.170.186.50".


	$pool1 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50

	$pool2 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool02 -BackendIPAddresses 134.170.186.46, 134.170.189.221,134.170.186.50

Questo esempio mostra due pool back-end che indirizzano il traffico di rete in base al percorso dell'URL. Un pool riceve il traffico dal percorso URL "/video" e l'altro riceve il traffico dal percorso "image". È necessario sostituire gli indirizzi IP precedenti per aggiungere gli endpoint di indirizzi IP dell'applicazione.

### Passaggio 3
Configurare le impostazioni "poolsetting01" e "poolsetting02" del gateway applicazione per il traffico di rete con carico bilanciato nel pool back-end. In questo esempio verranno configurate diverse impostazioni per i pool back-end. Ogni pool back-end può avere un'impostazione del pool back-end dedicata.

	$poolSetting01 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting01" -Port 80 -Protocol Http -CookieBasedAffinity Disabled -RequestTimeout 120

	$poolSetting02 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting02" -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 240

### Passaggio 4
Configurare l'indirizzo IP front-end con l'endpoint di indirizzo IP pubblico.

	$fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip

### Passaggio 5 
Configurare la porta front-end per un gateway applicazione.

	$fp01 = New-AzureRmApplicationGatewayFrontendPort -Name "fep01" -Port 80
### Passaggio 6
Configurare il listener. Questo passaggio configura il listener per l'indirizzo IP pubblico e la porta usata per ricevere il traffico di rete in ingresso.
 
	$listener = New-AzureRmApplicationGatewayHttpListener -Name "listener01" -Protocol Http -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01

### Passaggio 7 
Configurare i percorsi della regola per gli URL per i pool back-end. Questo passaggio configura il percorso relativo usato dal gateway applicazione per definire il mapping tra il percorso URL e il pool back-end selezionato per gestire il traffico di rete in ingresso.

L'esempio seguente mostra come creare due regole: una per il percorso "/image" che indirizza il traffico al pool back-end "pool1" e un'altra per il percorso "/video/ che indirizza il traffico al pool back-end "pool2".
    
	$imagePathRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "pathrule1" -Paths "/image/*" -BackendAddressPool $pool1 -BackendHttpSettings $poolSetting01

	$videoPathRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "pathrule2" -Paths "/video/*" -BackendAddressPool $pool2 -BackendHttpSettings $poolSetting02

La configurazione del mapping dei percorsi della regola configura anche un pool back-end predefinito da usare in caso di mancata corrispondenza con le regole di percorso predefinite.

	$urlPathMap = New-AzureRmApplicationGatewayUrlPathMapConfig -Name "urlpathmap" -PathRules $videoPathRule, $imagePathRule -DefaultBackendAddressPool $pool1 -DefaultBackendHttpSettings $poolSetting02

### Passaggio 8 
Creare un'impostazione della regola. Questo passaggio configura il gateway applicazione per l'uso del routing basato su percorso URL.

	$rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType PathBasedRouting -HttpListener $listener -UrlPathMap $urlPathMap
### Passaggio 9: 
Configurare il numero di istanze e le dimensioni per il gateway applicazione.

	$sku = New-AzureRmApplicationGatewaySku -Name "Standard_Small" -Tier Standard -Capacity 2

## Creare il gateway applicazione
Creare un gateway applicazione con tutti gli oggetti di configurazione illustrati nei passaggi precedenti.

	$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-RG -Location "West US" -BackendAddressPools $pool1,$pool2 -BackendHttpSettingsCollection $poolSetting01, $poolSetting02 -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener -UrlPathMaps $urlPathMap -RequestRoutingRules $rule01 -Sku $sku

## Ottenere il gateway applicazione
	$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-RG

<!---HONumber=AcomDC_0608_2016-->