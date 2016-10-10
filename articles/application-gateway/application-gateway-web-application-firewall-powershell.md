<properties
   pageTitle="Configurare un firewall applicazione Web su un gateway applicazione nuovo o esistente | Microsoft Azure"
   description="Questo articolo fornisce indicazioni su come iniziare a usare il firewall applicazione Web in un gateway applicazione nuovo o esistente."
   documentationCenter="na"
   services="application-gateway"
   authors="georgewallace"
   manager="carmonm"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/26/2016"
   ms.author="gwallace"/>

# Configurare un firewall applicazione Web su un gateway applicazione nuovo o esistente

> [AZURE.SELECTOR]
- [Portale di Azure](application-gateway-web-application-firewall-portal.md)
- [PowerShell per Azure Resource Manager](application-gateway-web-application-firewall-powershell.md)

Il gateway applicazione di Azure è un dispositivo di bilanciamento del carico di livello 7. Fornisce richieste HTTP con routing delle prestazioni e failover tra server diversi, sia nel cloud che in locale. L'applicazione offre numerose funzionalità di controller per la distribuzione di applicazioni (ADC, Application Delivery Controller), tra cui bilanciamento del carico HTTP, affinità di sessione basata su cookie, offload SSL (Secure Sockets Layer), probe di integrità personalizzati, supporto per più siti e molte altre. Per un elenco completo delle funzionalità supportate, vedere la panoramica del gateway applicazione

Il firewall applicazione Web (WAF) nel gateway applicazione di Azure protegge le applicazioni Web dai comuni attacchi basati sul Web, come ad esempio gli attacchi SQL injection, gli attacchi di scripting intersito e il controllo delle sessioni.

L'articolo seguente illustra come [aggiungere il firewall applicazione Web a un gateway applicazione esistente](#add-web-application-firewall-to-an-existing-application-gateway) e [creare un gateway applicazione che usa il firewall applicazione Web](#create-an-application-gateway-with-web-application-firewall).

![immagine dello scenario][scenario]

## Differenze di configurazione dei WAF

Se è stato letto l'argomento relativo alla [creazione di un gateway applicazione con PowerShell](application-gateway-create-gateway-arm.md), si conoscono le impostazioni della SKU da configurare quando si crea un gateway applicazione. WAF fornisce impostazioni aggiuntive da definire quando si configura la SKU su un gateway applicazione. Non esistono altre modifiche aggiuntive da apportare sul gateway applicazione.

**SKU**: un gateway applicazione normale senza WAF supporta le dimensioni **Standard\_Small**, **Standard\_Medium** e **Standard\_Large**. Con l'introduzione di WAF sono disponibili due SKU aggiuntive, **WAF\_Medium** e **WAF\_Large**. WAF non è supportato sui gateway applicazione di piccole dimensioni.

**Livello**: i valori disponibili sono **Standard** o **WAF**. Quando si usa un firewall applicazione Web, è necessario scegliere **WAF**.

**Modalità**: questa impostazione è la modalità di WAF. I valori consentiti sono **Rilevamento** e **Prevenzione**. Quando il firewall WAF è configurato in modalità di rilevamento, tutte le minacce vengono archiviate in un file di log. In modalità di prevenzione, gli eventi vengono comunque registrati, ma l'autore dell'attacco riceve una risposta di mancata autorizzazione 403 dal gateway applicazione.

## Aggiungere un firewall applicazione Web a un gateway applicazione esistente

Assicurarsi di usare la versione più recente di Azure PowerShell. Altre informazioni sono disponibili in [Uso di Azure PowerShell con Azure Resource Manager](../powershell-azure-resource-manager.md).

### Passaggio 1

Accedere all'account Azure.

    Login-AzureRmAccount

### Passaggio 2

Selezionare la sottoscrizione da usare per questo scenario.

    Select-AzureRmSubscription -SubscriptionName "<Subscription name>"

### Passaggio 3

Recuperare il gateway a cui si sta aggiungendo il firewall applicazione Web.

    $gw = Get-AzureRmApplicationGateway -Name "AdatumGateway" -ResourceGroupName "MyResourceGroup"


### Passaggio 4

Configurare la SKU del firewall applicazione Web. Le dimensioni disponibili sono **WAF\_Large** e **WAF\_Medium**. Quando si usa l'applicazione Web, il livello deve essere **WAF**.

    $gw | Set-AzureRmApplicationGatewaySku -Name WAF_Large -Tier WAF

### Passaggio 5

Configurare le impostazioni WAF nel modo definito nell'esempio seguente:

Per l'impostazione **WafMode**, i valori disponibili sono rilevamento e prevenzione.

    $config = Add-AzureRmApplicationGatewayWafConfig -Enabled $true -WafMode "Prevention" -ApplicationGateway $gw

### Passaggio 6

Aggiornare il gateway applicazione con le impostazioni definite nel passaggio precedente.

    Set-AzureRmApplicationGateway -ApplicationGateway $gw

Questo comando aggiorna il gateway applicazione con il firewall applicazione Web. È consigliabile visualizzare [Diagnostica del gateway applicazione](application-gateway-diagnostics.md) per comprendere come visualizzare i log per il gateway applicazione. Per le implicazioni di sicurezza del WAF, i log devono essere esaminati a intervalli regolari per essere aggiornati sulle condizioni di sicurezza delle applicazioni web.

## Creare un gateway applicazione con il firewall applicazione Web

I passaggi seguenti illustrano l'intero processo di creazione di un gateway applicazione con il firewall applicazione Web.

Assicurarsi di usare la versione più recente di Azure PowerShell. Altre informazioni sono disponibili in [Uso di Azure PowerShell con Azure Resource Manager](../powershell-azure-resource-manager.md).

### Passaggio 1

Accedere ad Azure

	Login-AzureRmAccount

Verrà richiesto di eseguire l'autenticazione con le proprie credenziali.

### Passaggio 2

Controllare le sottoscrizioni per l'account.

	Get-AzureRmSubscription

### Passaggio 3

Scegliere le sottoscrizioni ad Azure da usare.

    Select-AzureRmsubscription -SubscriptionName "<Subscription name>"

### Passaggio 4

Creare un gruppo di risorse. Ignorare questo passaggio se si usa un gruppo di risorse esistente.

    New-AzureRmResourceGroup -Name appgw-rg -Location "West US"

Gestione risorse di Azure richiede che tutti i gruppi di risorse specifichino un percorso che viene usato come percorso predefinito per le risorse presenti in tale gruppo di risorse. Assicurarsi che tutti i comandi per creare un gateway applicazione usino lo stesso gruppo di risorse.

Nell'esempio precedente è stato creato un gruppo di risorse denominato "appgw-RG" e la località "West US".

>[AZURE.NOTE] Se è necessario configurare un probe personalizzato per il gateway applicazione, vedere [Creare un probe personalizzato per il gateway applicazione di Azure con PowerShell per Azure Resource Manager](application-gateway-create-probe-ps.md). Per altre informazioni, vedere l'articolo relativo a [probe personalizzati e monitoraggio dell'integrità](application-gateway-probe-overview.md).

### Passaggio 5

Assegnare un intervallo di indirizzi che la subnet possa usare per il gateway applicazione.

    $gwSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24

> [AZURE.NOTE] La subnet per un'applicazione deve disporre almeno di 28 bit di maschera. Con questo valore, 10 indirizzi nella subnet rimangono disponibili per le istanze del gateway applicazione. Con una subnet più piccola, potrebbe non essere possibile aggiungere istanze del gateway applicazione in futuro.

### Passaggio 6

Assegnare un intervallo di indirizzi da usare per il pool di indirizzi di back-end.

    $nicSubnet = New-AzureRmVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24

### Passaggio 7

Creare una rete virtuale con le subnet precedenti nel gruppo di risorse creato nel passaggio: [Creare il gruppo di risorse](#create-the-resource-group)

    $vnet = New-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet

### Passaggio 8

Recuperare la risorsa di rete virtuale e le risorse della subnet da usare nei passaggi seguenti:

    $vnet = Get-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
    $gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
    $nicSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet

### Passaggio 9:

Creare una risorsa IP pubblica da usare per il gateway applicazione. Questo indirizzo IP pubblico viene usato in uno dei passaggi seguenti:

    $publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name 'appgwpip' -Location "West US" -AllocationMethod Dynamic

> [AZURE.IMPORTANT] Il gateway applicazione non supporta l'uso di un indirizzo IP pubblico creato con la definizione di un'etichetta di dominio. È supportato solo un indirizzo IP pubblico con etichetta di dominio creata in modo dinamico. Se è necessario un nome DNS intuitivo per il gateway applicazione, è consigliabile usare un record cname come alias.

### Passaggio 10

È necessario impostare tutti gli elementi di configurazione prima di creare il gateway applicazione. La procedura seguente consente di creare gli elementi di configurazione necessari per una risorsa del gateway applicazione.

Creare una configurazione IP del gateway applicazione che consente di configurare la subnet usata dal gateway applicazione. All'avvio, il gateway applicazione seleziona un indirizzo IP dalla subnet configurata e instrada il traffico di rete agli indirizzi IP nel pool di indirizzi IP back-end. Tenere presente che ogni istanza ha un indirizzo IP.

    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet

### Passaggio 11

Configurare il pool di indirizzi IP back-end con gli indirizzi IP dei server Web back-end. Questi indirizzi IP saranno quelli che ricevono il traffico di rete proveniente dall'endpoint IP front-end. Sostituire gli indirizzi IP seguenti e aggiungere gli endpoint di indirizzi IP dell'applicazione.

    $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3

### Passaggio 12

Configurare le impostazioni http di back-end del gateway applicazione. Assegnare il certificato caricato nel passaggio precedente alle impostazioni http.

    $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert

### Passaggio 13

Configurare la porta d indirizzo IP front-end con l'endpoint di indirizzo IP pubblico. Si tratta della porta a cui si connettono gli utenti finali.

    $fp = New-AzureRmApplicationGatewayFrontendPort -Name 'port01'  -Port 443

### Passaggio 14

Creare una configurazione IP front-end che consente di eseguire il mapping di un indirizzo IP pubblico o privato al front-end del gateway applicazione. Il passaggio seguente consente di associare l'indirizzo IP pubblico del passaggio precedente alla configurazione IP front-end.

    $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip

### Passaggio 15

Creare il listener HTTP per il gateway applicazione. Assegnare la configurazione IP, la porta e il certificato SSL front-end da usare.

	$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert

### Passaggio 16

Creare la regola di routing del servizio di bilanciamento del carico che configura il comportamento di bilanciamento del carico. In questo esempio viene creata una regola round robin di base.

    $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
   
### Passaggio 17

Configurare le dimensioni dell'istanza del gateway applicazione.

    $sku = New-AzureRmApplicationGatewaySku -Name WAF_Medium -Tier WAF -Capacity 2

>[AZURE.NOTE]  È possibile scegliere tra **WAF\_Medium** e **WAF\_Large**, il livello quando si usa WAF è sempre **WAF**. La capacità è qualsiasi numero compreso tra 1 e 10.

### Passaggio 18

Configurare la modalità per WAF; i valori accettabili sono **Prevenzione** e **Rilevamento**.

    $config = New-AzureRmApplicationGatewayWafConfig -Enabled $true -WafMode "Prevention"

### Passaggio 19

Creare un gateway applicazione con tutti gli elementi di configurazione illustrati nei passaggi precedenti. Nell'esempio il gateway applicazione è denominato "appgwtest".

	$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -WafConfig $config

## Passaggi successivi

Per informazioni su come configurare la registrazione diagnostica, per registrare gli eventi che vengono rilevati o bloccati con il firewall applicazione Web, visitare [Registrazione diagnostica per il gateway applicazione](application-gateway-diagnostics.md)


[scenario]: ./media/application-gateway-web-application-firewall-powershell/scenario.png

<!---HONumber=AcomDC_0928_2016-->