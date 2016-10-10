<properties
    pageTitle="Configurare SSL end-to-end e i criteri SSL con il gateway applicazione | Microsoft Azure"
    description="Questo articolo descrive come configurare SSL end-to-end con un gateway applicazione tramite PowerShell di Azure Resource Manager"
    services="application-gateway"
    documentationCenter="na"
    authors="georgewallace"
    manager="carmonmills"
    editor="tysonn"/>  

<tags
    ms.service="application-gateway"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="09/26/2016"
    ms.author="gwallace"/>  

# Configurare SSL end-to-end e i criteri SSL con il gateway applicazione tramite PowerShell

## Overview

Il gateway applicazione supporta la crittografia end-to-end del traffico. A tale scopo, il gateway applicazione chiude la connessione SSL nel gateway applicazione. Il gateway quindi applica le regole di routing al traffico, crittografare nuovamente il pacchetto e inoltra il pacchetto al back-end appropriato in base alle regole di routing definite. Eventuali risposte dal server Web subiscono lo stesso processo in ritorno verso l'utente finale.

Un'altra funzionalità supportata dal gateway applicazione è l a disabilitazione di alcune versioni del protocollo SSL. Il gateway applicazione supporta la disattivazione delle versioni del protocollo seguenti: TLSv1.0, TLSv1.1 e TLSv1.2.

![immagine dello scenario][scenario]  

## Scenario

Questo scenario mostra come creare un gateway applicazione usando SSL end-to-end tramite PowerShell.

Questo scenario illustrerà come:

- Creare un gruppo di risorse denominato "appgw-rg"
- Creare una rete virtuale denominata "appgwvnet" con un blocco CIDR riservato 10.0.0.0/16.
- Creare due subnet denominate "appgwsubnet" e "appsubnet".
- Creare un piccolo gateway applicazione che supporti la crittografia SSL end-to-end che disabilita alcuni protocolli SSL.

## Prima di iniziare

Per configurare SSL end-to-end con un gateway applicazione, è richiesto un certificato per il gateway e diversi certificati per i server back-end. Il certificato del gateway viene usato per crittografare e decrittografare il traffico inviato tramite SSL. Il certificato del gateway deve essere in formato PFX (Personal Information Exchange). Questo formato di file consente l'esportazione della chiave privata necessaria al gateway applicazione per eseguire la crittografia e la decrittografia del traffico.

Per la crittografia SSL end-to-end, il back-end deve essere incluso nell'elenco delle applicazioni consentite, insieme al gateway applicazione. A tale scopo, caricare il certificato pubblico dei back-end nel gateway applicazione. Ciò garantisce che il gateway applicazione comunichi solo con istanze di back-end note, proteggendo la comunicazione end-to-end. Il processo è descritto nella procedura seguente:

## Creare il gruppo di risorse

Questa sezione descrive la creazione di un gruppo di risorse che contiene il gateway applicazione.

### Passaggio 1

Accedere all'account Azure.

    Login-AzureRmAccount

### Passaggio 2

Selezionare la sottoscrizione da usare per questo scenario.

    Select-AzureRmsubscription -SubscriptionName "<Subscription name>"

### Passaggio 3

Creare un gruppo di risorse. Ignorare questo passaggio se si usa un gruppo di risorse esistente.

    New-AzureRmResourceGroup -Name appgw-rg -Location "West US"

## Creare una rete virtuale e una subnet per il gateway applicazione

Nell'esempio seguente viene creata una rete virtuale e due subnet. Una subnet viene usata per ospitare il gateway applicazione. L'altra subnet viene usata per i server back-end che ospitano l'applicazione Web.

### Passaggio 1

Assegnare un intervallo di indirizzi che la subnet possa usare per il gateway applicazione.

    $gwSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24

### Passaggio 2

Assegnare un intervallo di indirizzi da usare per il pool di indirizzi di back-end.

    $nicSubnet = New-AzureRmVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24

### Passaggio 3

Creare una rete virtuale con le subnet definite nei passaggi precedenti.

    $vnet = New-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet

### Passaggio 4

Recuperare la risorsa di rete virtuale e le risorse della subnet da usare nei passaggi seguenti:

    $vnet = Get-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
    $gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
    $nicSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet

## Creare un indirizzo IP pubblico per la configurazione front-end

Creare una risorsa IP pubblica da usare per il gateway applicazione. Questo indirizzo IP pubblico viene usato in uno dei prossimi passaggi.

    $publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -Name 'appgwpip' -Location "West US" -AllocationMethod Dynamic

> [AZURE.IMPORTANT] Il gateway applicazione non supporta l'uso di un indirizzo IP pubblico creato con la definizione di un'etichetta di dominio. È supportato solo un indirizzo IP pubblico con etichetta di dominio creata in modo dinamico. Se è necessario un nome DNS intuitivo per il gateway applicazione, è consigliabile usare un record cname come alias.

## Creare un oggetto di configurazione gateway applicazione

È necessario impostare tutti gli elementi di configurazione prima di creare il gateway applicazione. La procedura seguente consente di creare gli elementi di configurazione necessari per una risorsa del gateway applicazione.

### Passaggio 1

Creare una configurazione IP del gateway applicazione che consente di configurare la subnet usata dal gateway applicazione. All'avvio, il gateway applicazione seleziona un indirizzo IP dalla subnet configurata e instrada il traffico di rete agli indirizzi IP nel pool di indirizzi IP back-end. Tenere presente che ogni istanza ha un indirizzo IP.

    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet

### Passaggio 2

Creare una configurazione IP front-end che consente di eseguire il mapping di un indirizzo IP pubblico o privato al front-end del gateway applicazione. Il passaggio seguente consente di associare l'indirizzo IP pubblico del passaggio precedente alla configurazione IP front-end.

    $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip

### Passaggio 3

Configurare il pool di indirizzi IP back-end con gli indirizzi IP dei server Web back-end. Questi indirizzi IP saranno quelli che ricevono il traffico di rete proveniente dall'endpoint IP front-end. Sostituire gli indirizzi IP seguenti e aggiungere gli endpoint di indirizzi IP dell'applicazione.

    $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3

> [AZURE.NOTE] Il nome di dominio completo (FQDN) è un valore valido da usare al posto di un indirizzo IP per i server back-end.

### Passaggio 4

Configurare la porta d indirizzo IP front-end con l'endpoint di indirizzo IP pubblico. Si tratta della porta a cui si connettono gli utenti finali.

    $fp = New-AzureRmApplicationGatewayFrontendPort -Name 'port01'  -Port 443

### Passaggio 5

Configurare il certificato per il gateway applicazione. Il certificato viene usato per crittografare e decrittografare il traffico sul gateway applicazione.

	$cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password <password for certificate file>

> [AZURE.NOTE] Questo esempio configura il certificato usato per la connessione SSL. Il certificato deve essere in formato PFX e la password deve essere compresa tra 4 e 12 caratteri.

### Passaggio 6

Creare il listener HTTP per il gateway applicazione. Assegnare la configurazione IP, la porta e il certificato SSL front-end da usare.

	$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert

### Passaggio 7

Caricare il certificato da usare per le risorse del pool back-end SSL abilitato.

    $authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name 'whitelistcert1' -CertificateFile C:\users\gwallace\Desktop\cert.cer

> [AZURE.NOTE] Il certificato fornito in questo passaggio deve essere la chiave pubblica del certificato con estensione PFX presente nel back-end. Questo passaggio illustra come aggiungere il back-end all'elenco dei consentiti del gateway applicazione.

### Passaggio 8

Configurare le impostazioni http di back-end del gateway applicazione. Assegnare il certificato caricato nel passaggio precedente alle impostazioni http.

    $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert

### Passaggio 9:

Creare la regola di routing del servizio di bilanciamento del carico che configura il comportamento di bilanciamento del carico. In questo esempio viene creata una regola round robin di base.

    $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

### Passaggio 10

Configurare le dimensioni dell'istanza del gateway applicazione. Le dimensioni disponibili sono: **Standard\_Small**, **Standard\_Medium** e **Standard\_Large**. Per la capacità, i valori disponibili vanno da 1 a 10.

    $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

>[AZURE.NOTE] A scopo di test si può scegliere 1 come numero di istanze. È importante sapere che un numero di istanze inferiore a due non è coperto dal contratto di servizio e non è quindi consigliabile. È opportuno usare gateway Small a scopo di sviluppo/test e non per la produzione.

### Passaggio 11

Configurare i criteri SSL da usare sul gateway applicazione. Il gateway applicazione supporta la funzionalità di disattivazione di alcune versioni del protocollo SSL.

I valori seguenti rappresentano un elenco di versioni del protocollo che possono essere disabilitate.

- **TLSv1\_0**
- **TLSv1\_1**
- **TLSv1\_2**

Nell'esempio seguente vengono disabilitati TLSv1\_0 e TLSv1\_1.

    $sslPolicy = New-AzureRmApplicationGatewaySslPolicy -DisabledSslProtocols TLSv1_0, TLSv1_1

## Creare il gateway applicazione

Tramite tutti i passaggi precedenti è possibile creare il gateway applicazione. La creazione del gateway è un processo a esecuzione prolungata.

    $appgw = New-AzureRmApplicationGateway -Name appgateway -SslCertificates $cert -ResourceGroupName "appgw-rg" -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SslPolicy $sslPolicy -AuthenticationCertificates $authcert -Verbose

## Disabilitare le versioni del protocollo SSL su un gateway applicazione esistente

La procedura precedente guida nella creazione di un'applicazione con SSL end-to-end e nella disabilitazione di alcune versioni del protocollo SSL. Nell'esempio seguente alcuni criteri SSL vengono disabilitati su un gateway applicazione esistente.

### Passaggio 1

Recuperare il gateway applicazione da aggiornare.

    $gw = Get-AzureRmApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG

### Passaggio 2

Definire un criterio SSL. Nell'esempio seguente viene disabilitato TLSv1.0.

    Set-AzureRmApplicationGatewaySslPolicy -DisabledSslProtocols TLSv1_0 -ApplicationGateway $gw

### Passaggio 3

Infine, aggiornare il gateway. È importante notare che quest'ultimo passaggio rappresenta un'attività a esecuzione prolungata. Al termine, SSL end-to-end è configurato nel gateway applicazione.

    $gw | Set-AzureRmApplicationGateway

## Passaggi successivi

Per altre informazioni sul potenziamento della protezione delle applicazioni Web con il firewall applicazione Web tramite il gateway applicazione, visitare [Web Application Firewall Overview](application-gateway-webapplicationfirewall-overview.md) (Introduzione al firewall applicazione Web).

[scenario]: ./media/application-gateway-end-to-end-ssl-powershell/scenario.png

<!---HONumber=AcomDC_0928_2016-->