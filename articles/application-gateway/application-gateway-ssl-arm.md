---
title: Configurare l'offload SSL - Gateway applicazione di Azure - PowerShell | Documentazione Microsoft
description: Questo articolo fornisce istruzioni per creare un gateway applicazione con offload SSL usando Azure Resource Manager
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: 3c3681e0-f928-4682-9d97-567f8e278e13
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/19/2017
ms.author: davidmu
ms.translationtype: HT
ms.sourcegitcommit: 1c730c65194e169121e3ad1d1423963ee3ced8da
ms.openlocfilehash: 4bcd002a805f1584323f5baf75f791f2fb64201f
ms.contentlocale: it-it
ms.lasthandoff: 08/30/2017

---
# <a name="configure-an-application-gateway-for-ssl-offload-by-using-azure-resource-manager"></a>Configurare un gateway applicazione per l'offload SSL con Azure Resource Manager

> [!div class="op_single_selector"]
> * [Portale di Azure](application-gateway-ssl-portal.md)
> * [PowerShell per Azure Resource Manager](application-gateway-ssl-arm.md)
> * [PowerShell classico per Azure](application-gateway-ssl.md)
> * [Interfaccia della riga di comando di Azure 2.0](application-gateway-ssl-cli.md)

Il gateway applicazione di Azure può essere configurato per terminare la sessione Secure Sockets Layer (SSL) nel gateway ed evitare costose attività di decrittografia SSL nella Web farm. L'offload SSL semplifica anche la configurazione e la gestione del server front-end dell'applicazione Web.

## <a name="before-you-begin"></a>Prima di iniziare

1. Installare la versione più recente dei cmdlet di Azure PowerShell usando l'Installazione guidata piattaforma Web. È possibile scaricare e installare la versione più recente dalla sezione **Windows PowerShell** della [Pagina di download](https://azure.microsoft.com/downloads/).
2. Creare una rete virtuale e una subnet per il gateway applicazione. Assicurarsi che nessuna macchina virtuale o distribuzione cloud stia usando la subnet. Il gateway applicazione deve essere da solo in una subnet di rete virtuale.
3. È necessario che i server configurati per l'uso del gateway applicazione esistano oppure che i relativi endpoint siano stati creati nella rete virtuale o con un indirizzo IP o VIP pubblico assegnato.

## <a name="what-is-required-to-create-an-application-gateway"></a>Elementi necessari per creare un gateway applicazione

* **Pool di server back-end**: elenco di indirizzi IP dei server back-end. Gli indirizzi IP elencati devono appartenere alla subnet della rete virtuale o devono essere indirizzi IP/VIP pubblici.
* **Impostazioni del pool di server back-end**: ogni pool ha impostazioni come porta, protocollo e affinità basata sui cookie. Queste impostazioni sono associate a un pool e vengono applicate a tutti i server nel pool.
* **Porta front-end**: porta pubblica aperta sul gateway applicazione. Il traffico raggiunge questa porta e quindi viene reindirizzato a uno dei server back-end.
* **Listener**: ha una porta front-end, un protocollo (HTTP o HTPPS, queste impostazioni fanno distinzione tra maiuscole e minuscole) e il nome del certificato SSL, se si configura l'offload SSL.
* **Regola**: associa il listener e il pool di server back-end e definisce il pool di server back-end a cui indirizzare il traffico quando raggiunge un listener specifico. È attualmente supportata solo la regola *basic* . La regola *basic* è una distribuzione del carico di tipo round robin.

**Note aggiuntive sulla configurazione**

Per la configurazione dei certificati SSL, il protocollo in **HttpListener** deve essere sostituito con **Https** (distinzione tra maiuscole e minuscole). Aggiungere l'elemento **SslCertificate** ad **HttpListener** con il valore della variabile configurato per il certificato SSL. La porta front-end deve essere aggiornata a **443**.

**Per abilitare l'affinità basata sui cookie**: è possibile configurare un gateway applicazione per fare in modo che una richiesta proveniente da una sessione client sia sempre diretta alla stessa macchina virtuale nella Web farm. A tale scopo, aggiungere un cookie di sessione che consenta al gateway di indirizzare il traffico in modo appropriato. Per abilitare l'affinità basata sui cookie, impostare **CookieBasedAffinity** su **Enabled** nell'elemento **BackendHttpSettings**.

## <a name="create-an-application-gateway"></a>Creare un gateway applicazione

La differenza tra l'uso del modello di distribuzione classica di Azure e di Azure Resource Manager risiede nell'ordine in cui vengono creati un gateway applicazione e gli elementi da configurare.

Con Gestione risorse, tutti gli elementi di un gateway applicazione vengono configurati singolarmente e quindi combinati per creare una risorsa gateway applicazione.

Per creare un gateway applicazione, seguire questa procedura:

1. [Creare un gruppo di risorse per Gestione risorse](#create-a-resource-group-for-resource-manager)
2. [Creare una rete virtuale, una subnet e un indirizzo IP pubblico per il gateway applicazione](#create-virtual-network-subnet-and-public-IP-for-the-application-gateway)
3. [Creare un oggetto di configurazione del gateway applicazione](#create-an-application-gateway-configuration-object)
4. [Creare una risorsa gateway applicazione](#create-an-application-gateway-resource)

## <a name="create-a-resource-group-for-resource-manager"></a>Creare un gruppo di risorse per Gestione risorse

Assicurarsi di passare alla modalità PowerShell per usare i cmdlet di Gestione risorse di Azure. Per altre informazioni, vedere [Uso di Windows PowerShell con Gestione risorse](../powershell-azure-resource-manager.md).

   1. Immettere il comando seguente:

   ```powershell
   Login-AzureRmAccount
   ```

   2. Per controllare le sottoscrizioni per l'account, immettere i comandi seguenti:

   ```powershell
   Get-AzureRmSubscription
   ```

   Verrà richiesto di eseguire l'autenticazione con le proprie credenziali.

   3. Per scegliere le sottoscrizioni ad Azure da usare, immettere il comando seguente:

   ```powershell
   Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
   ```

   4. Per creare un gruppo di risorse, usare il comando seguente. Se si usa un gruppo di risorse esistente, ignorare questo passaggio.

   ```powershell
   New-AzureRmResourceGroup -Name appgw-rg -Location "West US"
   ```

Gestione risorse di Azure richiede che tutti i gruppi di risorse specifichino un percorso Questa impostazione viene usata come località predefinita per le risorse presenti nel gruppo di risorse. Assicurarsi che tutti i comandi per creare un gateway applicazione usino lo stesso gruppo di risorse.

Nell'esempio precedente è stato creato un gruppo di risorse denominato **appgw-RG** e la località **Stati Uniti occidentali**.

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Creare una rete virtuale e una subnet per il gateway applicazione

L'esempio seguente illustra come creare una rete virtuale usando Gestione risorse:

   1. Immettere il comando seguente:

   ```powershell
   $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24
   ```

   Questo esempio assegna l'intervallo di indirizzi **10.0.0.0/24** a una variabile di subnet da usare per creare una rete virtuale.

   2. Immettere il comando seguente:

   ```powershell
   $vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
   ```

   Questo esempio crea una rete virtuale denominata **appgwvnet** nel gruppo di risorse **appgw-rg** per l'area **Stati Uniti occidentali** usando il prefisso **10.0.0.0/16** con **subnet 10.0.0.0/24**.

   3. Immettere il comando seguente:

   ```powershell
   $subnet = $vnet.Subnets[0]
   ```

   Questo esempio assegna l'oggetto subnet alla variabile **$subnet** per i passaggi successivi.

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Creare un indirizzo IP pubblico per la configurazione front-end

Per creare un indirizzo IP pubblico per la configurazione front-end, immettere il comando seguente:

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic
```

Questo esempio crea una risorsa IP pubblica **publicIP01** nel gruppo di risorse **appgw-rg** per l'area **Stati Uniti occidentali**.

## <a name="create-an-application-gateway-configuration-object"></a>Creare un oggetto di configurazione gateway applicazione

   1. Per creare un oggetto di configurazione di gateway applicazione, immettere il comando seguente:

   ```powershell
   $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet
   ```

   Questo esempio crea una configurazione IP del gateway applicazione denominata **gatewayIP01**. All'avvio, il gateway applicazione seleziona un indirizzo IP dalla subnet configurata e instrada il traffico di rete agli indirizzi IP nel pool di indirizzi IP back-end. Tenere presente che ogni istanza ha un indirizzo IP.

   2. Immettere il comando seguente:

   ```powershell
   $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50
   ```

   Questo esempio configura il pool di indirizzi IP back-end denominato **pool01** con gli indirizzi IP **134.170.185.46**, **134.170.188.221** e **134.170.185.50**. Tali valori saranno gli indirizzi IP che ricevono il traffico di rete proveniente dall'endpoint IP front-end. Sostituire gli indirizzi IP dell'esempio precedente con gli indirizzi IP degli endpoint dell'applicazione Web.

   3. Immettere il comando seguente:

   ```powershell
   $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Enabled
   ```

   Questo esempio configura l'impostazione **poolsetting01** del gateway applicazione per il traffico di rete con bilanciamento del carico nel pool back-end.

   4. Immettere il comando seguente:

   ```powershell
   $fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 443
   ```

   Questo esempio configura la porta IP front-end denominata **frontendport01** per l'endpoint IP pubblico.

   5. Immettere il comando seguente:

   ```powershell
   $cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path for certificate file> -Password "<password>"
   ```

   Questo esempio configura il certificato usato per la connessione SSL. Il certificato deve essere in formato PFX e la password deve essere compresa tra 4 e 12 caratteri.

   6. Immettere il comando seguente:

   ```powershell
   $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip
   ```

   Questo esempio crea la configurazione IP front-end denominata **fipconfig01** e associa l'indirizzo IP pubblico alla configurazione IP front-end.

   7. Immettere il comando seguente:

   ```powershell
   $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert
   ```

   Questo esempio crea il listener denominato **listener01** e associa la porta front-end al certificato e alla configurazione IP front-end.

   8. Immettere il comando seguente:

   ```powershell
   $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
   ```

   Questo esempio crea la regola di routing del servizio di bilanciamento del carico denominata **rule01** che configura il comportamento di bilanciamento del carico.

   9. Immettere il comando seguente:

   ```powershell
   $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
   ```

   Questo esempio configura le dimensioni dell'istanza del gateway applicazione.

   > [!NOTE]
   > Il valore predefinito per **InstanceCount** è **2**, con un valore massimo pari a 10. Il valore predefinito per **GatewaySize** è **Medium**. È possibile scegliere tra Standard_Small, Standard_Medium e Standard_Large.

   10. Immettere il comando seguente:

   ```powershell
   $policy = New-AzureRmApplicationGatewaySslPolicy -PolicyType Predefined -PolicyName AppGwSslPolicy20170401S
   ```

   Questo passaggio definisce i criteri SSL da usare sul gateway applicazione. Per altre informazioni, vedere [Configurare i pacchetti di crittografia e le versioni dei criteri SSL nel gateway applicazione](application-gateway-configure-ssl-policy-powershell.md).

## <a name="create-an-application-gateway-by-using-new-azureapplicationgateway"></a>Creare un gateway applicazione usando New-AzureApplicationGateway

Immettere il comando seguente:

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SslCertificates $cert -SslPolicy $policy
```

Questo esempio crea un gateway applicazione con tutti gli elementi di configurazione illustrati nei passaggi precedenti. Nell'esempio il gateway applicazione è denominato **appgwtest**.

## <a name="get-the-application-gateway-dns-name"></a>Ottenere il nome DNS del gateway applicazione

Dopo avere creato il gateway, il passaggio successivo prevede la configurazione del front-end per la comunicazione. Il gateway applicazione richiede un nome DNS assegnato in modo dinamico, non descrittivo quando si usa un IP pubblico. Per assicurarsi che gli utenti finali possano raggiungere il gateway applicazione, è possibile usare un record CNAME che faccia riferimento all'endpoint pubblico del gateway applicazione. Per altre informazioni, vedere [Configurare un nome di dominio personalizzato in Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). 

Per ottenere il nome DNS del gateway applicazione, recuperare i dettagli del gateway applicazione e il nome DNS e l'IP associati usando l'elemento **PublicIPAddress** collegato al gateway applicazione. Usare il nome DNS del gateway applicazione per creare un record CNAME che associ le due applicazioni Web a questo nome DNS. Non è consigliabile usare record A perché l'indirizzo VIP può cambiare al riavvio del gateway applicazione.


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

Per configurare un gateway applicazione da usare con un servizio di bilanciamento del carico interno, vedere [Creare un gateway applicazione con un servizio di bilanciamento del carico interno](application-gateway-ilb.md).

Per altre informazioni generali sulle opzioni di bilanciamento del carico, vedere:

* [Servizio di bilanciamento del carico di Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Gestione traffico di Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)

