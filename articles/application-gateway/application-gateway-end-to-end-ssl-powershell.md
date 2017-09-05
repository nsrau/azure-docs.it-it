---
title: Configurare SSL end-to-end con il gateway applicazione di Azure | Microsoft Docs
description: Questo articolo descrive come configurare SSL end-to-end con un gateway applicazione tramite PowerShell di Azure Resource Manager
services: application-gateway
documentationcenter: na
author: georgewallace
manager: timlt
editor: tysonn
ms.assetid: e6d80a33-4047-4538-8c83-e88876c8834e
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/19/2017
ms.author: gwallace
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: 6d969d6a0c649c263e1d5bb99bdbceec484cb9a3
ms.contentlocale: it-it
ms.lasthandoff: 08/16/2017

---
# <a name="configure-end-to-end-ssl-with-application-gateway-using-powershell"></a>Configurare SSL end-to-end con il gateway applicazione usando PowerShell

## <a name="overview"></a>Panoramica

Il gateway applicazione supporta la crittografia end-to-end del traffico. A tale scopo, il gateway applicazione chiude la connessione SSL nel gateway applicazione. Il gateway quindi applica le regole di routing al traffico, crittografare nuovamente il pacchetto e inoltra il pacchetto al back-end appropriato in base alle regole di routing definite. Eventuali risposte dal server Web subiscono lo stesso processo in ritorno verso l'utente finale.

Un'altra funzionalità supportata dal gateway applicazione è la definizione delle opzioni SSL personalizzate. Il gateway applicazione supporta la disabilitazione delle versioni del protocollo **TLSv1.0**, **TLSv1.1** e **TLSv1.2**, oltre alla definizione dei pacchetti di crittografia da usare e l'ordine di preferenza.  Per altre informazioni sulle opzioni SSL configurabili, vedere [SSL Policy overview](application-gateway-SSL-policy-overview.md) (Panoramica dei criteri SSL).

> [!NOTE]
> SSL 2.0 e SSL 3.0 sono disabilitati per impostazione predefinita e non possono essere abilitati. Sono considerati non sicuri e non possono essere usati con il gateway applicazione.

![immagine dello scenario][scenario]

## <a name="scenario"></a>Scenario

Questo scenario mostra come creare un gateway applicazione usando SSL end-to-end tramite PowerShell.

Questo scenario illustrerà come:

* Creare un gruppo di risorse denominato **appgw-rg**
* Creare una rete virtuale denominata **appgwvnet** con uno spazio indirizzi 10.0.0.0/16.
* Creare due subnet denominate **appgwsubnet** e **appsubnet**.
* Creare un piccolo gateway applicazione che supporti la crittografia SSL end-to-end che limita le versioni dei protocolli SSL e i pacchetti di crittografia.

## <a name="before-you-begin"></a>Prima di iniziare

Per configurare SSL end-to-end con un gateway applicazione, è richiesto un certificato per il gateway e diversi certificati per i server back-end. Il certificato del gateway viene usato per crittografare e decrittografare il traffico inviato usando SSL. Il certificato del gateway deve essere in formato PFX (Personal Information Exchange). Questo formato di file consente l'esportazione della chiave privata necessaria al gateway applicazione per eseguire la crittografia e la decrittografia del traffico.

Per la crittografia SSL end-to-end, il back-end deve essere incluso nell'elenco delle applicazioni consentite con il gateway applicazione. A tale scopo, caricare il certificato pubblico dei back-end nel gateway applicazione. Ciò garantisce che il gateway applicazione comunichi solo con istanze di back-end note, proteggendo ulteriormente la comunicazione end-to-end.

Il processo è descritto nella procedura seguente:

## <a name="create-the-resource-group"></a>Creare il gruppo di risorse

Questa sezione descrive la creazione di un gruppo di risorse che contiene il gateway applicazione.

### <a name="step-1"></a>Passaggio 1

Accedere all'account Azure.

```powershell
Login-AzureRmAccount
```

### <a name="step-2"></a>Passaggio 2

Selezionare la sottoscrizione da usare per questo scenario.

```powershell
Select-AzureRmsubscription -SubscriptionName "<Subscription name>"
```

### <a name="step-3"></a>Passaggio 3

Creare un gruppo di risorse. Ignorare questo passaggio se si usa un gruppo di risorse esistente.

```powershell
New-AzureRmResourceGroup -Name appgw-rg -Location "West US"
```

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Creare una rete virtuale e una subnet per il gateway applicazione

Nell'esempio seguente viene creata una rete virtuale e due subnet. Una subnet viene usata per contenere il gateway applicazione. L'altra subnet viene usata per i back-end che ospitano l'applicazione Web.

### <a name="step-1"></a>Passaggio 1

Assegnare un intervallo di indirizzi che la subnet possa usare per il gateway applicazione.

```powershell
$gwSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24
```

> [!NOTE]
> Le subnet configurate per il gateway applicazione devono essere ridimensionate in modo corretto. Un gateway applicazione può essere configurato per un massimo di 10 istanze. Ogni istanza accetta un indirizzo IP dalla subnet. Le dimensioni eccessivamente piccole di una subnet possono influire negativamente sull'aumento del numero di istanze di un gateway applicazione.
> 
> 

### <a name="step-2"></a>Passaggio 2

Assegnare un intervallo di indirizzi da usare per il pool di indirizzi di back-end.

```powershell
$nicSubnet = New-AzureRmVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24
```

### <a name="step-3"></a>Passaggio 3

Creare una rete virtuale con le subnet definite nei passaggi precedenti.

```powershell
$vnet = New-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet
```

### <a name="step-4"></a>Passaggio 4

Recuperare la risorsa di rete virtuale e le risorse della subnet da usare nei passaggi seguenti:

```powershell
$vnet = Get-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
$gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
$nicSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet
```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Creare un indirizzo IP pubblico per la configurazione front-end

Creare una risorsa IP pubblica da usare per il gateway applicazione. Questo indirizzo IP pubblico viene usato in uno dei prossimi passaggi.

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -Name 'publicIP01' -Location "West US" -AllocationMethod Dynamic
```

> [!IMPORTANT]
> Il gateway applicazione non supporta l'uso di un indirizzo IP pubblico creato con la definizione di un'etichetta di dominio. È supportato solo un indirizzo IP pubblico con etichetta di dominio creata in modo dinamico. Se è necessario un nome DNS intuitivo per il gateway applicazione, è consigliabile usare un record CNAME come alias.

## <a name="create-an-application-gateway-configuration-object"></a>Creare un oggetto di configurazione gateway applicazione

Tutti gli elementi di configurazione vengono impostati prima di creare il gateway applicazione. La procedura seguente consente di creare gli elementi di configurazione necessari per una risorsa del gateway applicazione.

### <a name="step-1"></a>Passaggio 1

Creare una configurazione IP del gateway applicazione che consente di configurare la subnet usata dal gateway applicazione. All'avvio, il gateway applicazione seleziona un indirizzo IP dalla subnet configurata e instrada il traffico di rete agli indirizzi IP nel pool di indirizzi IP back-end. Tenere presente che ogni istanza ha un indirizzo IP.

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet
```

### <a name="step-2"></a>Passaggio 2

Creare una configurazione IP front-end che consente di eseguire il mapping di un indirizzo IP pubblico o privato al front-end del gateway applicazione. Il passaggio seguente consente di associare l'indirizzo IP pubblico del passaggio precedente alla configurazione IP front-end.

```powershell
$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip
```

### <a name="step-3"></a>Passaggio 3

Configurare il pool di indirizzi IP back-end con gli indirizzi IP dei server Web back-end. Questi indirizzi IP saranno quelli che ricevono il traffico di rete proveniente dall'endpoint IP front-end. Sostituire gli indirizzi IP seguenti e aggiungere gli endpoint di indirizzi IP dell'applicazione.

```powershell
$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3
```

> [!NOTE]
> Il nome di dominio completo (FQDN) è un valore valido per sostituire un indirizzo IP per i server back-end usando l'opzione -BackendFqdns. 

### <a name="step-4"></a>Passaggio 4

Configurare la porta d indirizzo IP front-end con l'endpoint di indirizzo IP pubblico. Si tratta della porta a cui si connettono gli utenti finali.

```powershell
$fp = New-AzureRmApplicationGatewayFrontendPort -Name 'port01'  -Port 443
```

### <a name="step-5"></a>Passaggio 5

Configurare il certificato per il gateway applicazione. Il certificato viene usato per crittografare e decrittografare il traffico sul gateway applicazione.

```powershell
$cert = New-AzureRmApplicationGatewaySSLCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password <password for certificate file>
```

> [!NOTE]
> Questo esempio configura il certificato usato per la connessione SSL. Il certificato deve essere in formato PFX e la password deve essere compresa tra 4 e 12 caratteri.

### <a name="step-6"></a>Passaggio 6

Creare il listener HTTP per il gateway applicazione. Assegnare la configurazione IP, la porta e il certificato SSL front-end da usare.

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SSLCertificate $cert
```

### <a name="step-7"></a>Passaggio 7

Caricare il certificato da usare per le risorse del pool back-end abilitate per SSL.

> [!NOTE]
> Il probe predefinito ottiene la chiave pubblica dall'associazione SSL **predefinita** nell'indirizzo IP del back-end e confronta il valore della chiave pubblica ricevuta con il valore della chiave pubblica specificata qui. La chiave pubblica recuperata potrebbe non corrispondere esattamente al sito previsto a cui il traffico viene trasferito **se** si usano intestazioni host e SNI nel back-end. In caso di dubbio, visitare https://127.0.0.1/ nei back-end per verificare quale certificato viene usato per l'associazione SSL **predefinita**. In questa sezione usare la chiave pubblica ottenuta da tale richiesta. Se si usano intestazioni host e SNI nelle associazioni HTTPS e non si ricevono una risposta e un certificato da una richiesta del browser manuale a https://127.0.0.1/ nei back-end, è necessario configurare un'associazione SSL predefinita nei back-end. In caso contrario, i probe hanno esito negativo e il back-end non è consentito.

```powershell
$authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name 'whitelistcert1' -CertificateFile C:\users\gwallace\Desktop\cert.cer
```

> [!NOTE]
> Il certificato fornito in questo passaggio deve essere la chiave pubblica del certificato con estensione PFX presente nel back-end. Esportare il certificato (non il certificato radice) installato nel server back-end in formato CER e usarlo in questo passaggio. Questo passaggio illustra come aggiungere il back-end all'elenco dei consentiti del gateway applicazione.

### <a name="step-8"></a>Passaggio 8

Configurare le impostazioni http di back-end del gateway applicazione. Assegnare il certificato caricato nel passaggio precedente alle impostazioni http.

```powershell
$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert
```

### <a name="step-9"></a>Passaggio 9:

Creare la regola di routing del servizio di bilanciamento del carico che configura il comportamento di bilanciamento del carico. In questo esempio viene creata una regola round robin di base.

```powershell
$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
```

### <a name="step-10"></a>Passaggio 10

Configurare le dimensioni dell'istanza del gateway applicazione.  Le dimensioni disponibili sono **Standard\_Small**, **Standard\_Medium** e **Standard\_Large**.  Per la capacità, i valori disponibili vanno da 1 a 10.

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
```

> [!NOTE]
> A scopo di test si può scegliere 1 come numero di istanze. È importante sapere che un numero di istanze inferiore a due non è coperto dal contratto di servizio e non è quindi consigliabile. È opportuno usare gateway Small a scopo di sviluppo/test e non per la produzione.

### <a name="step-11"></a>Passaggio 11

Configurare i criteri SSL da usare sul gateway applicazione. Il gateway applicazione supporta la funzionalità di impostazione di una versione minima per le versioni del protocollo SSL.

I valori seguenti rappresentano un elenco di versioni del protocollo che possono essere definite.

* **TLSv1_0**
* **TLSv1_1**
* **TLSv1_2**

Imposta la versione minima del protocollo su **TLSv1_2** e abilita solo **TLS\_ECDHE\_ECDSA\_WITH\_AES\_128\_GCM\_SHA256**, **TLS\_ECDHE\_ECDSA\_WITH\_AES\_256\_GCM\_SHA384** e **TLS\_RSA\_WITH\_AES\_128\_GCM\_SHA256**.

```powershell
$SSLPolicy = New-AzureRmApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256"
```

## <a name="create-the-application-gateway"></a>Creare il gateway applicazione

Tramite tutti i passaggi precedenti è possibile creare il gateway applicazione. La creazione del gateway è un processo a esecuzione prolungata.

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgateway -SSLCertificates $cert -ResourceGroupName "appgw-rg" -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SSLPolicy $SSLPolicy -AuthenticationCertificates $authcert -Verbose
```

## <a name="limit-ssl-protocol-versions-on-an-existing-application-gateway"></a>Limitare le versioni del protocollo SSL in un gateway applicazione esistente

La procedura precedente illustra la creazione di un'applicazione con SSL end-to-end e la disabilitazione di alcune versioni del protocollo SSL. Nell'esempio seguente alcuni criteri SSL vengono disabilitati su un gateway applicazione esistente.

### <a name="step-1"></a>Passaggio 1

Recuperare il gateway applicazione da aggiornare.

```powershell
$gw = Get-AzureRmApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG
```

### <a name="step-2"></a>Passaggio 2

Definire un criterio SSL. Nell'esempio seguente TLSv1.0 e TLSv1.1 vengono disabilitati e i pacchetti di crittografia **TLS\_ECDHE\_ECDSA\_WITH\_AES\_128\_GCM\_SHA256**, **TLS\_ECDHE\_ECDSA\_WITH\_AES\_256\_GCM\_SHA384** e **TLS\_RSA\_WITH\_AES\_128\_GCM\_SHA256** sono i solo consentiti.

```powershell
Set-AzureRmApplicationGatewaySSLPolicy -MinProtocolVersion -PolicyType Custom -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256" -ApplicationGateway $gw

```

### <a name="step-3"></a>Passaggio 3

Infine, aggiornare il gateway. È importante notare che quest'ultimo passaggio rappresenta un'attività a esecuzione prolungata. Al termine, SSL end-to-end è configurato nel gateway applicazione.

```powershell
$gw | Set-AzureRmApplicationGateway
```

## <a name="get-application-gateway-dns-name"></a>Ottenere il nome DNS del gateway applicazione

Dopo avere creato il gateway, il passaggio successivo prevede la configurazione del front-end per la comunicazione. Quando si usa un IP pubblico, il gateway applicazione richiede un nome DNS assegnato in modo dinamico, non descrittivo. Per assicurarsi che gli utenti finali possano raggiungere il gateway applicazione, è possibile usare un record CNAME per fare riferimento all'endpoint pubblico del gateway applicazione. [Configurazione di un nome di dominio personalizzato in Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). A questo scopo, recuperare i dettagli del gateway applicazione e il nome DNS e l'IP associati, usando l'elemento PublicIPAddress collegato al gateway applicazione. Il nome DNS del gateway applicazione dovrà essere usato per creare un record CNAME che associa le due applicazioni Web a questo nome DNS. Non è consigliabile usare record A perché l'indirizzo VIP può cambiare al riavvio del gateway applicazione.

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -Name publicIP01
```

```
Name                     : publicIP01
ResourceGroupName        : appgw-RG
Location                 : westus
Id                       : /subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/publicIPAddresses/publicIP01
Etag                     : W/"00000d5b-54ed-4907-bae8-99bd5766d0e5"
ResourceGuid             : 00000000-0000-0000-0000-000000000000
ProvisioningState        : Succeeded
Tags                     : 
PublicIpAllocationMethod : Dynamic
IpAddress                : xx.xx.xxx.xx
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : {
                                "Id": "/subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/applicationGateways/appgwtest/frontendIP
                            Configurations/frontend1"
                            }
DnsSettings              : {
                                "Fqdn": "00000000-0000-xxxx-xxxx-xxxxxxxxxxxx.cloudapp.net"
                            }
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul potenziamento della protezione delle applicazioni Web con il firewall applicazione Web tramite il gateway applicazione, visitare [Web Application Firewall Overview](application-gateway-webapplicationfirewall-overview.md)

[scenario]: ./media/application-gateway-end-to-end-SSL-powershell/scenario.png

