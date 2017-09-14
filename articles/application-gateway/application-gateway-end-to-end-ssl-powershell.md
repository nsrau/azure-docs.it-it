---
title: Configurare SSL end-to-end con il gateway applicazione di Azure | Microsoft Docs
description: Questo articolo descrive come configurare SSL end-to-end con un gateway applicazione di Azure tramite PowerShell
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
ms.sourcegitcommit: eeed445631885093a8e1799a8a5e1bcc69214fe6
ms.openlocfilehash: dbc012526b062f21e675576c8269f0076f43cbc6
ms.contentlocale: it-it
ms.lasthandoff: 09/07/2017

---
# <a name="configure-end-to-end-ssl-by-using-application-gateway-with-powershell"></a>Configurare SSL end-to-end usando un gateway applicazione con PowerShell

## <a name="overview"></a>Panoramica

Il gateway applicazione di Azure supporta la crittografia end-to-end del traffico. Il gateway applicazione termina la connessione SSL nel gateway applicazione stesso. Il gateway applica quindi le regole di routing al traffico, crittografa di nuovo il pacchetto e inoltra il pacchetto al server back-end appropriato in base alle regole di routing definite. Eventuali risposte dal server Web subiscono lo stesso processo in ritorno verso l'utente finale.

Il gateway applicazione supporta la definizione di opzioni SSL personalizzate. Il gateway applicazione supporta anche queste versioni del protocollo: **TLSv1.0**, **TLSv1.1** e **TLSv1.2**, oltre alla definizione dei pacchetti di crittografia da usare e l'ordine di preferenza. Per altre informazioni sulle opzioni SSL configurabili, vedere [Panoramica dei criteri SSL](application-gateway-SSL-policy-overview.md).

> [!NOTE]
> SSL 2.0 e SSL 3.0 sono disabilitati per impostazione predefinita e non possono essere abilitati. Sono considerati non sicuri e non possono essere usati con il gateway applicazione.

![immagine dello scenario][scenario]

## <a name="scenario"></a>Scenario

Questo scenario mostra come creare un gateway applicazione usando SSL end-to-end con PowerShell.

Questo scenario illustrerà come:

* Creare un gruppo di risorse chiamato **appgw-rg**.
* Creare una rete virtuale chiamata **appgwvnet** con uno spazio indirizzi **10.0.0.0/16**.
* Creare due subnet denominate **appgwsubnet** e **appsubnet**.
* Creare un piccolo gateway applicazione che supporti la crittografia SSL end-to-end, che limita le versioni del protocollo SSL e i pacchetti di crittografia.

## <a name="before-you-begin"></a>Prima di iniziare

Per configurare SSL end-to-end con un gateway applicazione, sono necessari un certificato per il gateway e i certificati per i server back-end. Il certificato del gateway viene usato per crittografare e decrittografare il traffico inviato tramite SSL. Il certificato del gateway deve essere in formato PFX (Personal Information Exchange). Questo formato di file consente l'esportazione della chiave privata necessaria al gateway applicazione per eseguire la crittografia e la decrittografia del traffico.

Per la crittografia SSL end-to-end, il back-end deve essere incluso nell'elenco elementi consentiti con il gateway applicazione. È necessario caricare il certificato pubblico dei server back-end nel gateway applicazione. L'aggiunta del certificato garantisce che il gateway applicazione comunichi solo con istanze di back-end note, proteggendo ulteriormente la comunicazione end-to-end.

Questo processo di configurazione viene descritto nelle sezioni seguenti.

## <a name="create-the-resource-group"></a>Creare il gruppo di risorse.

Questa sezione descrive la creazione di un gruppo di risorse che contiene il gateway applicazione.


   1. Accedere all'account Azure.

   ```powershell
   Login-AzureRmAccount
   ```


   2. Selezionare la sottoscrizione da usare per questo scenario.

   ```powershell
   Select-AzureRmsubscription -SubscriptionName "<Subscription name>"
   ```


   3. Creare un gruppo di risorse. Se si usa un gruppo di risorse esistente, ignorare questo passaggio.

   ```powershell
   New-AzureRmResourceGroup -Name appgw-rg -Location "West US"
   ```

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Creare una rete virtuale e una subnet per il gateway applicazione

Nell'esempio seguente viene creata una rete virtuale e due subnet. Una subnet viene usata per contenere il gateway applicazione. L'altra subnet viene usata per i back-end che ospitano l'applicazione Web.


   1. Assegnare un intervallo di indirizzi per la subnet da usare per il gateway applicazione.

   ```powershell
   $gwSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24
   ```

   > [!NOTE]
   > Le subnet configurate per un gateway applicazione devono essere ridimensionate nel modo corretto. Un gateway applicazione può essere configurato per un massimo di 10 istanze. Ogni istanza accetta un indirizzo IP dalla subnet. Le dimensioni eccessivamente piccole di una subnet possono influire negativamente sull'aumento del numero di istanze di un gateway applicazione.
   > 
   > 

   2. Assegnare un intervallo di indirizzi da usare per il pool di indirizzi dei back-end.

   ```powershell
   $nicSubnet = New-AzureRmVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24
   ```

   3. Creare una rete virtuale con le subnet definite nei passaggi precedenti.

   ```powershell
   $vnet = New-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet
   ```

   4. Recuperare la risorsa di rete virtuale e le risorse della subnet da usare nei passaggi seguenti.

   ```powershell
   $vnet = Get-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
   $gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
   $nicSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet
   ```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Creare un indirizzo IP pubblico per la configurazione front-end

Creare una risorsa IP pubblica da usare per il gateway applicazione. Questo indirizzo IP pubblico viene usato in uno dei passaggi seguenti.

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -Name 'publicIP01' -Location "West US" -AllocationMethod Dynamic
```

> [!IMPORTANT]
> Il gateway applicazione non supporta l'uso di un indirizzo IP pubblico creato con un'etichetta di dominio definita. È supportato solo un indirizzo IP pubblico con etichetta di dominio creata in modo dinamico. Se è necessario un nome DNS intuitivo per il gateway applicazione, è consigliabile usare un record CNAME come alias.

## <a name="create-an-application-gateway-configuration-object"></a>Creare un oggetto di configurazione gateway applicazione

Tutti gli elementi di configurazione vengono impostati prima di creare il gateway applicazione. La procedura seguente consente di creare gli elementi di configurazione necessari per una risorsa del gateway applicazione.

   1. Creare una configurazione IP per il gateway applicazione. Questa impostazione configura le subnet usate dal gateway applicazione. All'avvio, il gateway applicazione seleziona un indirizzo IP dalla subnet configurata e instrada il traffico di rete agli indirizzi IP nel pool di indirizzi IP dei back-end. Tenere presente che ogni istanza ha un indirizzo IP.

   ```powershell
   $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet
   ```


   2. Creare una configurazione di indirizzi IP front-end. Questa impostazione esegue il mapping di un indirizzo IP pubblico o privato al front-end del gateway applicazione. Il passaggio seguente consente di associare l'indirizzo IP pubblico del passaggio precedente alla configurazione IP front-end.

   ```powershell
   $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip
   ```

   3. Configurare il pool di indirizzi IP dei back-end con gli indirizzi IP dei server Web back-end. Questi indirizzi IP saranno quelli che ricevono il traffico di rete proveniente dall'endpoint IP front-end. Sostituire gli indirizzi IP nell'esempio con gli endpoint di indirizzi IP dell'applicazione.

   ```powershell
   $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3
   ```

   > [!NOTE]
   > Anche un nome di dominio completo (FQDN) è un valore valido da usare al posto di un indirizzo IP per i server back-end. Per abilitarlo, usare l'opzione **-BackendFqdns**. 


   4. Configurare la porta d indirizzo IP front-end con l'endpoint di indirizzo IP pubblico. Si tratta della porta a cui si connettono gli utenti finali.

   ```powershell
   $fp = New-AzureRmApplicationGatewayFrontendPort -Name 'port01'  -Port 443
   ```

   5. Configurare il certificato per il gateway applicazione. Questo certificato viene usato per decrittografare e crittografare di nuovo il traffico nel gateway applicazione.

   ```powershell
   $cert = New-AzureRmApplicationGatewaySSLCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password <password for certificate file>
   ```

   > [!NOTE]
   > Questo esempio configura il certificato usato per la connessione SSL. Il certificato deve essere in formato PFX e la password deve contenere da 4 a 12 caratteri.

   6. Creare il listener HTTP per il gateway applicazione. Assegnare la configurazione IP, la porta e il certificato SSL del front-end da usare.

   ```powershell
   $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SSLCertificate $cert
   ```

   7. Caricare il certificato da usare per le risorse del pool back-end abilitate per SSL.

   > [!NOTE]
   > Il probe predefinito ottiene la chiave pubblica dall'associazione SSL *predefinita* nell'indirizzo IP del back-end e confronta il valore della chiave pubblica ricevuta con il valore della chiave pubblica specificata qui. 
   
   > Se si usano intestazioni host e la funzionalità Indicazione nome server (SNI) nel back-end, la chiave pubblica recuperata potrebbe non corrispondere al sito previsto in cui viene trasferito il traffico. In caso di dubbi, visitare la pagina all'indirizzo https://127.0.0.1/ nei server back-end per determinare il certificato usato per l'associazione SSL *predefinita*. In questa sezione usare la chiave pubblica ottenuta da tale richiesta. Se si usano intestazioni host e la funzionalità Indicazione nome server (SNI) nelle associazioni HTTPS e non si riceve una risposta e un certificato da una richiesta del browser manuale a https://127.0.0.1/ nei server back-end, è necessario configurare un'associazione SSL predefinita nei server. In caso contrario, i probe hanno esito negativo e il back-end non è consentito.

   ```powershell
   $authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name 'whitelistcert1' -CertificateFile C:\users\gwallace\Desktop\cert.cer
   ```

   > [!NOTE]
   > Il certificato fornito in questo passaggio deve essere la chiave pubblica del certificato PFX presente nel back-end. Esportare il certificato (non il certificato radice) installato nel server back-end in formato CER e usarlo in questo passaggio. Questo passaggio mostra come aggiungere il back-end all'elenco elementi consentiti con il gateway applicazione.

   8. Configurare le impostazioni HTTP per il back-end del gateway applicazione. Assegnare il certificato caricato nel passaggio precedente alle impostazioni HTTP.

   ```powershell
   $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert
   ```
   9. Creare la regola di routing del bilanciamento del carico per la configurazione del comportamento di bilanciamento del carico. In questo esempio viene creata una regola di round robin di base.

   ```powershell
   $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
   ```

   10. Configurare le dimensioni dell'istanza del gateway applicazione. Le dimensioni disponibili sono **Standard\_Small**, **Standard\_Medium** e **Standard\_Large**.  Per la capacità, i valori disponibili sono compresi tra **1** e **10**.

   ```powershell
   $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
   ```

   > [!NOTE]
   > A scopo di test si può scegliere 1 come numero di istanze. È importante tenere presente che qualsiasi numero di istanze inferiore a due non è coperto dal contratto di servizio ed è quindi sconsigliato. È opportuno usare gateway Small a scopo di sviluppo/test e non per la produzione.

   11. Configurare i criteri SSL da usare nel gateway applicazione. Il gateway applicazione supporta la funzionalità di impostazione di una versione minima per le versioni del protocollo SSL.

   I valori seguenti rappresentano un elenco di versioni del protocollo che è possibile definire:

    - **TLSV1_0**
    - **TLSV1_1**
    - **TLSV1_2**
    
   L'esempio seguente imposta la versione minima del protocollo su **TLSv1_2** e abilita solo **TLS\_ECDHE\_ECDSA\_WITH\_AES\_128\_GCM\_SHA256**, **TLS\_ECDHE\_ECDSA\_WITH\_AES\_256\_GCM\_SHA384** e **TLS\_RSA\_WITH\_AES\_128\_GCM\_SHA256**.

   ```powershell
   $SSLPolicy = New-AzureRmApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256"
   ```

## <a name="create-the-application-gateway"></a>Creare il gateway applicazione

Creare il gateway applicazione seguendo tutti i passaggi precedenti. La creazione del gateway è un processo a esecuzione prolungata.

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgateway -SSLCertificates $cert -ResourceGroupName "appgw-rg" -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SSLPolicy $SSLPolicy -AuthenticationCertificates $authcert -Verbose
```

## <a name="limit-ssl-protocol-versions-on-an-existing-application-gateway"></a>Limitare le versioni del protocollo SSL in un gateway applicazione esistente

I passaggi precedenti descrivono la creazione di un'applicazione con SSL end-to-end e la disabilitazione di alcune versioni del protocollo SSL. Nell'esempio seguente alcuni criteri SSL vengono disabilitati su un gateway applicazione esistente.

   1. Recuperare il gateway applicazione da aggiornare.

   ```powershell
   $gw = Get-AzureRmApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG
   ```

   2. Definire un criterio SSL. Nell'esempio seguente **TLSv1.0** e **TLSv1.1** sono disabilitati e i pacchetti di crittografia **TLS\_ECDHE\_ECDSA\_WITH\_AES\_128\_GCM\_SHA256**, **TLS\_ECDHE\_ECDSA\_WITH\_AES\_256\_GCM\_SHA384** e **TLS\_RSA\_WITH\_AES\_128\_GCM\_SHA256** sono gli unici consentiti.

   ```powershell
   Set-AzureRmApplicationGatewaySSLPolicy -MinProtocolVersion -PolicyType Custom -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256" -ApplicationGateway $gw

   ```

   3. Infine, aggiornare il gateway. Notare che questo ultimo passaggio rappresenta un'attività a esecuzione prolungata. Al termine, SSL end-to-end è configurato nel gateway applicazione.

   ```powershell
   $gw | Set-AzureRmApplicationGateway
   ```

## <a name="get-an-application-gateway-dns-name"></a>Ottenere un nome DNS del gateway applicazione

Dopo avere creato il gateway, il passaggio successivo prevede la configurazione del front-end per la comunicazione. Il gateway applicazione richiede un nome DNS assegnato in modo dinamico, non descrittivo quando si usa un IP pubblico. Per assicurarsi che gli utenti finali possano raggiungere il gateway applicazione, è possibile usare un record CNAME che faccia riferimento all'endpoint pubblico del gateway applicazione. Per altre informazioni, vedere [Configurare un nome di dominio personalizzato in Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). 

Per configurare un alias, recuperare i dettagli del gateway applicazione e il nome DNS/indirizzo IP associato usando l'elemento **PublicIPAddress** collegato al gateway applicazione. Usare il nome DNS del gateway applicazione per creare un record CNAME che associ le due applicazioni Web a questo nome DNS. Non è consigliabile usare record A perché l'indirizzo VIP può cambiare al riavvio del gateway applicazione.

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

Per altre informazioni su come rafforzare la sicurezza delle applicazioni Web con il web application firewall tramite il gateway applicazione, vedere [Panoramica del web application firewall](application-gateway-webapplicationfirewall-overview.md).

[scenario]: ./media/application-gateway-end-to-end-SSL-powershell/scenario.png

