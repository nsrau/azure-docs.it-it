---
title: Creare un gateway applicazione con le regole di routing basato su URL | Microsoft Docs
description: Questa pagina fornisce istruzioni per la creazione e la configurazione di un gateway applicazione di Azure con le regole di routing basato su URL.
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: d141cfbb-320a-4fc9-9125-10001c6fa4cf
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/03/2017
ms.author: davidmu
ms.translationtype: HT
ms.sourcegitcommit: d24c6777cc6922d5d0d9519e720962e1026b1096
ms.openlocfilehash: b1ed7d5693ff7e6730255462411d462694b730e1
ms.contentlocale: it-it
ms.lasthandoff: 09/14/2017

---
# <a name="create-an-application-gateway-by-using-path-based-routing"></a>Creare un gateway applicazione con il routing basato sul percorso

> [!div class="op_single_selector"]
> * [Portale di Azure](application-gateway-create-url-route-portal.md)
> * [PowerShell per Azure Resource Manager](application-gateway-create-url-route-arm-ps.md)
> * [Interfaccia della riga di comando di Azure 2.0](application-gateway-create-url-route-cli.md)

Il routing basato sul percorso consente di associare le route in base al percorso dell'URL di una richiesta HTTP. Verifica se è disponibile una route per il pool back-end configurato per l'URL presentato nel gateway applicazione e invia il traffico di rete al pool back-end definito. In genere il routing basato su URL viene usato per le richieste di bilanciamento del carico per diversi tipi di contenuto tra vari pool di server back-end.

Per il gateway applicazione sono disponibili due tipi di regole: ruoting di base e routing basato sul percorso. Il tipo di regola di base fornisce il servizio di round robin per i pool back-end. Il routing basato sul percorso, oltre alla distribuzione round-robin, usa anche il modello del percorso dell'URL della richiesta per scegliere il pool back-end appropriato.

## <a name="scenario"></a>Scenario

Nell'esempio seguente il gateway applicazione gestisce il traffico per contoso.com con due pool di server back-end: un pool di server video e un pool di server immagini.

Le richieste per http://contoso.com/image* vengono instradate al pool di server di immagini (**pool1**), mentre quelle per http://contoso.com/video* vengono instradate al pool di server di video (**pool2**). In caso di mancata corrispondenza dei modelli di percorso, viene selezionato un pool di server predefinito (**pool1**).

![Route dell'URL](./media/application-gateway-create-url-route-arm-ps/figure1.png)

## <a name="before-you-begin"></a>Prima di iniziare

1. Installare la versione più recente dei cmdlet di Azure PowerShell usando l'Installazione guidata piattaforma Web. È possibile scaricare e installare la versione più recente dalla sezione **Windows PowerShell** della [Pagina di download](https://azure.microsoft.com/downloads/).
2. Creare una rete virtuale e una subnet per il gateway applicazione. Verificare che nessuna macchina virtuale o distribuzione cloud stia usando la subnet. Il gateway applicazione deve essere da solo in una subnet di rete virtuale.
3. Verificare che i server aggiunti al pool back-end per l'uso del gateway applicazione esistano oppure che i relativi endpoint siano stati creati nella rete virtuale o che sia stato assegnato loro un indirizzo IP/VIP pubblico.

## <a name="requirements-to-create-an-application-gateway"></a>Requisiti per la creazione di un gateway applicazione

* **Pool di server back-end**: elenco di indirizzi IP dei server back-end. Gli indirizzi IP elencati devono appartenere alla subnet della rete virtuale o essere indirizzi IP/VIP pubblici.
* **Impostazioni del pool di server back-end**: ad esempio porta, protocollo e affinità basata sui cookie. Queste impostazioni sono associate a un pool e applicate a tutti i server nel pool.
* **Porta front-end**: porta pubblica aperta sul gateway applicazione. Il traffico raggiunge questa porta e quindi viene reindirizzato a uno dei server back-end.
* **Listener**: ha una porta front-end, un protocollo (Http o Https, con distinzione tra maiuscole e minuscole) e il nome del certificato SSL (se si configura l'offload SSL).
* **Regola**: associa il listener e il pool di server back-end e definisce il pool a cui deve essere indirizzato il traffico quando raggiunge un listener.

## <a name="create-an-application-gateway"></a>Creare un gateway applicazione

La differenza tra l'uso del portale di Azure classico e di Azure Resource Manager risiede nell'ordine in cui vengono creati il gateway applicazione e gli elementi da configurare.

Con Resource Manager, tutti gli elementi che costituiscono un gateway applicazione vengono configurati singolarmente e quindi combinati per creare la risorsa del gateway applicazione.

Per creare un gateway applicazione, seguire questa procedura:

1. Creare un gruppo di risorse per Gestione risorse.
2. Creare una rete virtuale, una subnet e un indirizzo IP pubblico per il gateway applicazione.
3. Creare un oggetto di configurazione del gateway applicazione.
4. Creare una risorsa del gateway applicazione.

## <a name="create-a-resource-group-for-resource-manager"></a>Creare un gruppo di risorse per Gestione risorse

Assicurarsi di usare la versione più recente di Azure PowerShell. Altre informazioni sono disponibili in [Uso di Azure PowerShell con Azure Resource Manager](../powershell-azure-resource-manager.md).

### <a name="step-1"></a>Passaggio 1

Accedere ad Azure.

```powershell
Login-AzureRmAccount
```

Verrà richiesto di eseguire l'autenticazione con le proprie credenziali.<BR>

### <a name="step-2"></a>Passaggio 2

Controllare le sottoscrizioni per l'account.

```powershell
Get-AzureRmSubscription
```

### <a name="step-3"></a>Passaggio 3

Scegliere le sottoscrizioni ad Azure da usare. <BR>

```powershell
Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
```

### <a name="step-4"></a>Passaggio 4

Creare un gruppo di risorse. Se si usa un gruppo di risorse esistente, ignorare questo passaggio.

```powershell
$resourceGroup = New-AzureRmResourceGroup -Name appgw-RG -Location "West US"
```

In alternativa, è anche possibile creare tag del gruppo di risorse per un gateway applicazione:

```powershell
$resourceGroup = New-AzureRmResourceGroup -Name appgw-RG -Location "West US" -Tags @{Name = "testtag"; Value = "Application Gateway URL routing"} 
```

Azure Resource Manager richiede che i gruppi di risorse specifichino un percorso predefinito usato per tutte le risorse presenti nel gruppo. Assicurarsi che tutti i comandi per creare un gateway applicazione usino lo stesso gruppo di risorse.

Nell'esempio precedente è stato creato un gruppo di risorse denominato "appgw-RG" con la località "Stati Uniti occidentali".

> [!NOTE]
> Se è necessario configurare un probe personalizzato per il gateway applicazione, vedere [Creare un probe personalizzato per il gateway applicazione con PowerShell](application-gateway-create-probe-ps.md). Per altre informazioni, vedere [Panoramica del monitoraggio dell'integrità del gateway applicazione](application-gateway-probe-overview.md).
> 
> 

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Creare una rete virtuale e una subnet per il gateway applicazione

L'esempio seguente illustra come creare una rete virtuale usando Gestione risorse. Questo esempio crea una rete virtuale per il gateway applicazione. Il gateway applicazione richiede una propria subnet. Per questo motivo la subnet creata per il gateway applicazione è più piccola dello spazio degli indirizzi della rete virtuale. Ciò consente alle altre risorse, inclusi in via esemplificativa i server Web, di essere configurate nella stessa rete virtuale.

### <a name="step-1"></a>Passaggio 1

Assegnare l'intervallo di indirizzi 10.0.0.0/24 alla variabile di subnet da usare per creare una rete virtuale.  Questo passaggio crea l'oggetto di configurazione della subnet per il gateway applicazione usato nell'esempio successivo.

```powershell
$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24
```

### <a name="step-2"></a>Passaggio 2

Creare una rete virtuale denominata **appgwvnet** nel gruppo di risorse **appgw-rg** per l'area Stati Uniti occidentali usando il prefisso 10.0.0.0/16 con subnet 10.0.0.0/24. Questo passaggio completa la configurazione della rete virtuale con una singola subnet nella quale risiede il gateway applicazione.

```powershell
$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
```

### <a name="step-3"></a>Passaggio 3

Assegnare la variabile subnet per i passaggi successivi. Questa variabile viene passata al cmdlet `New-AzureRMApplicationGateway` in un passaggio successivo.

```powershell
$subnet=$vnet.Subnets[0]
```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Creare un indirizzo IP pubblico per la configurazione front-end

Creare una risorsa IP pubblica denominata **publicIP01** nel gruppo di risorse **appgw-rg** per l'area Stati Uniti occidentali. Il gateway applicazione può usare un indirizzo IP pubblico, un indirizzo IP interno o entrambi per ricevere le richieste di bilanciamento del carico.  Per questo esempio è stato usato solo un indirizzo IP pubblico. Nell'esempio seguente, non è stato configurato nessun nome DNS per la creazione dell'indirizzo IP pubblico, perché il gateway applicazione non supporta nomi DNS personalizzati su indirizzi IP pubblici.  Se per l'endpoint pubblico è necessario un nome personalizzato, creare un record CNAME che faccia riferimento al nome DNS generato automaticamente per l'indirizzo IP pubblico.

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -name publicIP01 -location "West US" -AllocationMethod Dynamic
```

All'avvio del servizio viene assegnato un indirizzo IP al gateway applicazione.

## <a name="create-the-application-gateway-configuration"></a>Creare la configurazione del gateway applicazione

Prima di creare il gateway applicazione, è necessario impostare tutti gli elementi di configurazione. La procedura seguente consente di creare gli elementi di configurazione necessari per una risorsa del gateway applicazione.

### <a name="step-1"></a>Passaggio 1

Creare una configurazione IP del gateway applicazione denominata **gatewayIP01**. All'avvio, il gateway applicazione seleziona un indirizzo IP dalla subnet configurata e instrada il traffico di rete agli indirizzi IP nel pool di indirizzi IP dei back-end. Tenere presente che ogni istanza ha un indirizzo IP.

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet
```

### <a name="step-2"></a>Passaggio 2

Configurare il pool di indirizzi IP back-end denominato **pool1** e **pool2** con indirizzi IP per **pool1** e **pool2**. Questi indirizzi sono gli indirizzi IP delle risorse che ospitano l'applicazione Web da proteggere con il gateway applicazione. Tutti questi membri del pool back-end vengono convalidati come integri tramite i probe, che siano probe di base o personalizzati. Il traffico viene instradato verso di loro quando le richieste arrivano al gateway applicazione. I pool back-end possono essere usati da più regole all'interno del gateway applicazione. Ciò significa che un pool di back-end può essere usato per più applicazioni Web che si trovano nello stesso host.

```powershell
$pool1 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221, 134.170.185.50

$pool2 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool02 -BackendIPAddresses 134.170.186.47, 134.170.189.222, 134.170.186.51
```

Questo esempio mostra due pool back-end che indirizzano il traffico di rete in base al percorso dell'URL. Un pool riceve il traffico dal percorso dell'URL "/video" e l'altro riceve il traffico dal percorso "/image". Sostituire gli indirizzi IP precedenti e aggiungere gli endpoint di indirizzi IP dell'applicazione. 

### <a name="step-3"></a>Passaggio 3

Configurare le impostazioni **poolsetting01** e **poolsetting02** del gateway applicazione per il traffico di rete con carico bilanciato nel pool back-end. In questo esempio vengono configurate diverse impostazioni per i pool back-end. Ogni pool back-end può avere le proprie impostazioni.  Le impostazioni HTTP di back-end vengono usate dalle regole per instradare il traffico verso i membri del pool di back-end corretti. Ciò determina il protocollo e la porta usati durante l'invio di traffico ai membri del pool back-end. Anche le sessioni basate sui cookie sono determinate dalle impostazioni HTTP di back-end. Se abilitata, l'affinità di sessione basata su cookie invia traffico allo stesso back-end sotto forma di richieste precedenti per ogni pacchetto.

```powershell
$poolSetting01 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting01" -Port 80 -Protocol Http -CookieBasedAffinity Disabled -RequestTimeout 120

$poolSetting02 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting02" -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 240
```

### <a name="step-4"></a>Passaggio 4

Configurare l'indirizzo IP front-end con l'endpoint di indirizzo IP pubblico. L'oggetto di configurazione IP front-end viene usato da un listener per correlare l'indirizzo IP rivolto all'esterno con il listener.

```powershell
$fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip
```

### <a name="step-5"></a>Passaggio 5

Configurare la porta front-end per un gateway applicazione. L'oggetto di configurazione della porta front-end viene usato da un listener per definire la porta del gateway applicazione in ascolto del traffico nel listener.

```powershell
$fp01 = New-AzureRmApplicationGatewayFrontendPort -Name "fep01" -Port 80
```

### <a name="step-6"></a>Passaggio 6

Questo passaggio configura il listener per l'indirizzo IP pubblico e la porta usata per ricevere il traffico di rete in ingresso. L'esempio seguente prende in considerazione la configurazione IP front-end configurata in precedenza, la configurazione della porta front-end e un protocollo (Http o Https, con distinzione tra maiuscole e minuscole) e configura il listener. In questo esempio, il listener è in ascolto per il traffico HTTP sulla porta 80 all'indirizzo IP pubblico che è stato creato in precedenza.

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name "listener01" -Protocol Http -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01
```

### <a name="step-7"></a>Passaggio 7

Configurare i percorsi della regola per gli URL per i pool back-end. Questo passaggio configura il percorso relativo usato dal gateway applicazione e definisce il mapping tra il percorso dell'URL e il pool back-end assegnato per gestire il traffico di rete in ingresso.

> [!IMPORTANT]
> Ogni percorso deve iniziare con una barra rovesciata ("/") e l'unica posizione in cui è consentito l'asterisco è alla fine. Alcuni esempi validi: /xyz, /xyz* o /xyz/*. La stringa inviata al selettore di percorsi non include alcun testo dopo il primo carattere "?" o "#" e questi caratteri non sono consentiti. 

L'esempio seguente crea due regole: una per il percorso "/image" che instrada il traffico al pool back-end **pool1** e un'altra per il percorso "/video/" che instrada il traffico al pool back-end **pool2**. Queste regole assicurano che il traffico per ogni set di URL viene indirizzato al back-end. Ad esempio, http://contoso.com/image/figure1.jpg passa al **pool1** e http://contoso.com/video/example.mp4 al **pool2**.

```powershell
$imagePathRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "pathrule1" -Paths "/image/*" -BackendAddressPool $pool1 -BackendHttpSettings $poolSetting01

$videoPathRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "pathrule2" -Paths "/video/*" -BackendAddressPool $pool2 -BackendHttpSettings $poolSetting02
```

In caso di mancata corrispondenza con le regole di percorso predefinite, la configurazione del mapping dei percorsi della regola configura anche un pool di indirizzi back-end predefinito. Ad esempio, http://contoso.com/shoppingcart/test.html passa al **pool1** definito come pool predefinito per il traffico senza corrispondenza.

```powershell
$urlPathMap = New-AzureRmApplicationGatewayUrlPathMapConfig -Name "urlpathmap" -PathRules $videoPathRule, $imagePathRule -DefaultBackendAddressPool $pool1 -DefaultBackendHttpSettings $poolSetting02
```

### <a name="step-8"></a>Passaggio 8

Creare un'impostazione della regola. Questo passaggio configura il gateway applicazione per l'uso del routing basato sul percorso dell'URL. La variabile `$urlPathMap` definita nel passaggio precedente viene ora usata per creare la regola basata sul percorso. In questo passaggio la regola viene associata a un listener e al mapping del percorso URL creato in precedenza.

```powershell
$rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType PathBasedRouting -HttpListener $listener -UrlPathMap $urlPathMap
```

### <a name="step-9"></a>Passaggio 9:

Configurare il numero di istanze e le dimensioni per il gateway applicazione.

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name "Standard_Small" -Tier Standard -Capacity 2
```

## <a name="create-an-application-gateway"></a>Creare un gateway applicazione

Creare un gateway applicazione con tutti gli oggetti di configurazione illustrati nei passaggi precedenti.

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-RG -Location "West US" -BackendAddressPools $pool1,$pool2 -BackendHttpSettingsCollection $poolSetting01, $poolSetting02 -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener -UrlPathMaps $urlPathMap -RequestRoutingRules $rule01 -Sku $sku
```

## <a name="get-an-application-gateway-dns-name"></a>Ottenere un nome DNS del gateway applicazione

Dopo avere creato il gateway, viene configurato il front-end per la comunicazione. Quando si usa un IP pubblico, il gateway applicazione richiede un nome DNS assegnato in modo dinamico, non descrittivo. Per assicurarsi che gli utenti finali possano raggiungere il gateway applicazione, è possibile usare un record CNAME che faccia riferimento all'endpoint pubblico del gateway applicazione. Per altre informazioni, vedere [Configurazione di un nome di dominio personalizzato per un servizio cloud di Azure](../cloud-services/cloud-services-custom-domain-name-portal.md).

Per configurare il record CNAME per l'IP front-end, recuperare i dettagli del gateway applicazione e il nome DNS e l'IP associati usando l'elemento PublicIPAddress collegato al gateway applicazione. Per creare un record CNAME usare il nome DNS del gateway applicazione. Non è consigliabile usare record A perché l'indirizzo VIP può cambiare al riavvio del gateway applicazione.

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

Per informazioni sull'offload SSL (Secure Sockets Layer), vedere [Configurare un gateway applicazione per l'offload SSL con Azure Resource Manager](application-gateway-ssl-arm.md).


