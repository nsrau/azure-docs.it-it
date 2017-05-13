---
title: Creare e gestire un gateway applicazione di Azure - PowerShell | Documentazione Microsoft
description: Questa pagina fornisce istruzioni per la creazione, la configurazione, l&quot;avvio e l&quot;eliminazione di un gateway applicazione di Azure usando Gestione risorse di Azure
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: tysonn
ms.assetid: 866e9b5f-0222-4b6a-a95f-77bc3d31d17b
ms.service: application-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/04/2017
ms.author: gwallace
ms.translationtype: Human Translation
ms.sourcegitcommit: 64bd7f356673b385581c8060b17cba721d0cf8e3
ms.openlocfilehash: 8a0eb841b1a41a14e443b6ce93e6b8fb8985a803
ms.contentlocale: it-it
ms.lasthandoff: 05/02/2017


---
# <a name="create-start-or-delete-an-application-gateway-by-using-azure-resource-manager"></a>Creare, avviare o eliminare un gateway applicazione tramite Gestione risorse di Azure

> [!div class="op_single_selector"]
> * [Portale di Azure](application-gateway-create-gateway-portal.md)
> * [PowerShell per Azure Resource Manager](application-gateway-create-gateway-arm.md)
> * [PowerShell per Azure classico](application-gateway-create-gateway.md)
> * [Modello di Azure Resource Manager](application-gateway-create-gateway-arm-template.md)
> * [Interfaccia della riga di comando di Azure](application-gateway-create-gateway-cli.md)

Il gateway applicazione di Azure è un dispositivo di bilanciamento del carico di livello 7. Fornisce richieste HTTP con routing delle prestazioni e failover tra server diversi, sia nel cloud che in locale.
Il gateway applicazione offre numerose funzionalità di controller per la distribuzione di applicazioni (ADC, Application Delivery Controller), tra cui bilanciamento del carico HTTP, affinità di sessione basata su cookie, offload SSL (Secure Sockets Layer), probe di integrità personalizzati, supporto per più siti e molte altre.

Per un elenco completo delle funzionalità supportate, vedere [Panoramica del gateway applicazione](application-gateway-introduction.md)

Questo articolo illustra in dettaglio i passaggi necessari per creare e configurare, avviare ed eliminare un gateway applicazione.

> [!IMPORTANT]
> Prima di iniziare a usare le risorse di Azure, è importante comprendere che Azure al momento offre due modelli di distribuzione, quello classico e Gestione risorse. È importante conoscere i [modelli e gli strumenti di distribuzione](../azure-classic-rm.md) prima di usare le risorse di Azure. È possibile visualizzare la documentazione relativa a diversi strumenti facendo clic sulle schede nella parte superiore di questo articolo. Questo documento illustra la creazione di un gateway applicazione con Azure Resource Manager. Per usare la versione classica, passare a [Creare, avviare o eliminare un gateway applicazione](application-gateway-create-gateway.md).

## <a name="before-you-begin"></a>Prima di iniziare

1. Installare la versione più recente dei cmdlet di Azure PowerShell usando l'Installazione guidata piattaforma Web. È possibile scaricare e installare la versione più recente dalla sezione **Windows PowerShell** della [pagina Download](https://azure.microsoft.com/downloads/).
1. Se è già disponibile una rete virtuale, selezionare una subnet vuota esistente oppure creare una subnet nella rete virtuale esclusivamente per l'uso da parte del gateway applicazione. Non è possibile distribuire il gateway applicazione in una rete virtuale diversa da quella delle risorse da distribuire dietro il gateway applicazione.
1. È necessario che i server configurati per l'uso del gateway applicazione esistano oppure che i relativi endpoint siano stati creati nella rete virtuale o con un indirizzo IP/VIP pubblico assegnato.

## <a name="what-is-required-to-create-an-application-gateway"></a>Elementi necessari per creare un gateway applicazione

* **Pool di server back-end:** elenco di indirizzi IP, FQDN o NIC dei server back-end. Se si usano gli indirizzi IP, questi devono appartenere alla subnet della rete virtuale o devono essere indirizzi IP/VIP pubblici.
* **Impostazioni del pool di server back-end:** ogni pool ha impostazioni quali porta, protocollo e affinità basata sui cookie. Queste impostazioni sono associate a un pool e vengono applicate a tutti i server nel pool.
* **Porta front-end:** porta pubblica aperta sul gateway applicazione. Il traffico raggiunge questa porta e quindi viene reindirizzato a uno dei server back-end.
* **Listener** : ha una porta front-end, un protocollo (Http o Https, con distinzione tra maiuscole e minuscole) e il nome del certificato SSL (se si configura l'offload SSL).
* **Regola** : associa il listener e il pool di server back-end e definisce il pool di server back-end a cui deve essere indirizzato il traffico quando raggiunge un listener specifico.

## <a name="create-an-application-gateway"></a>Creare un gateway applicazione

La differenza tra l'uso di Azure classico e di Azure Resource Manager risiede nell'ordine in cui vengono creati il gateway applicazione e gli elementi da configurare.

Con Resource Manager, tutti gli elementi che costituiscono un gateway applicazione vengono configurati singolarmente e quindi combinati per creare la risorsa del gateway applicazione.

Per creare un gateway applicazione, seguire questa procedura.

## <a name="create-a-resource-group-for-resource-manager"></a>Creare un gruppo di risorse per Gestione risorse

Assicurarsi di usare la versione più recente di Azure PowerShell. Altre informazioni sono disponibili in [Uso di Azure PowerShell con Azure Resource Manager](../powershell-azure-resource-manager.md).

### <a name="step-1"></a>Passaggio 1

Accedere ad Azure

```powershell
Login-AzureRmAccount
```

Verrà richiesto di eseguire l'autenticazione con le proprie credenziali.

### <a name="step-2"></a>Passaggio 2

Controllare le sottoscrizioni per l'account.

```powershell
Get-AzureRmSubscription
```

### <a name="step-3"></a>Passaggio 3

Scegliere le sottoscrizioni ad Azure da usare.

```powershell
Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
```

### <a name="step-4"></a>Passaggio 4

Creare un gruppo di risorse. Ignorare questo passaggio se si usa un gruppo di risorse esistente.

```powershell
New-AzureRmResourceGroup -Name appgw-rg -Location "West US"
```

Gestione risorse di Azure richiede che tutti i gruppi di risorse specifichino un percorso che viene usato come percorso predefinito per le risorse presenti in tale gruppo di risorse. Assicurarsi che tutti i comandi per creare un gateway applicazione usino lo stesso gruppo di risorse.

Nell'esempio precedente è stato creato un gruppo di risorse denominato **appgw-RG** e l'area **West US**.

> [!NOTE]
> Se è necessario configurare un probe personalizzato per il gateway applicazione, visitare: [Creare un probe personalizzato per il gateway applicazione con PowerShell](application-gateway-create-probe-ps.md). Per altre informazioni, vedere l'articolo relativo a [probe personalizzati e monitoraggio dell'integrità](application-gateway-probe-overview.md) .

## <a name="create-a-virtual-network-and-a-subnet"></a>Creare una rete virtuale e una subnet

L'esempio seguente illustra come creare una rete virtuale usando Gestione risorse. Questo esempio crea una rete virtuale per il gateway applicazione. Il gateway applicazione richiede la propria subnet, per questo motivo la subnet creata per il gateway applicazione è più piccola dello spazio degli indirizzi della rete virtuale. L'uso di una subnet più piccola consente la presenza di altre risorse inclusi, ma non in via esclusiva, i server Web da configurare nella stessa rete virtuale.

### <a name="step-1"></a>Passaggio 1

Assegnare l'intervallo di indirizzi 10.0.0.0/24 alla variabile di subnet da usare per creare una rete virtuale. Questo passaggio crea l'oggetto di configurazione della subnet per il gateway applicazione, usato nell'esempio seguente.

```powershell
$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24
```

### <a name="step-2"></a>Passaggio 2

Creare una rete virtuale denominata **appgwvnet** nel gruppo di risorse **appgw-rg** per l'area Stati Uniti occidentali usando il prefisso 10.0.0.0/16 con subnet 10.0.0.0/24. Questo passaggio completa la configurazione della rete virtuale con una singola subnet in cui dovrà risiedere il gateway applicazione.

```powershell
$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
```

### <a name="step-3"></a>Passaggio 3

Assegnare la variabile di subnet per i passaggi successivi, questa variabile viene passata a un cmdlet `New-AzureRMApplicationGateway` in un passaggio successivo.

```powershell
$subnet=$vnet.Subnets[0]
```

## <a name="create-a-public-ip-address"></a>Creare un indirizzo IP pubblico

Creare una risorsa IP pubblica denominata **publicIP01** nel gruppo di risorse **appgw-rg** per l'area Stati Uniti occidentali. Il gateway applicazione può usare un indirizzo IP pubblico, un interno indirizzo IP o entrambi per ricevere le richieste di bilanciamento del carico.  Per questo esempio è stato usato solo un indirizzo IP pubblico. Nell'esempio seguente, nessun nome DNS è configurato per la creazione dell'indirizzo IP pubblico.  Il gateway applicazione non supporta i nomi DNS personalizzati negli indirizzi IP pubblici.  Se per l'endpoint pubblico è necessario un nome personalizzato, è necessario creare un record CNAME per fare riferimento al nome DNS generato automaticamente per l'indirizzo IP pubblico.

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic
```

> [!NOTE]
> All'avvio del servizio viene assegnato un indirizzo IP al gateway applicazione.

## <a name="create-the-application-gateway-configuration-objects"></a>Creare gli oggetti di configurazione gateway applicazione

Prima di creare il gateway applicazione, è necessario impostare tutti gli elementi di configurazione. La procedura seguente consente di creare gli elementi di configurazione necessari per una risorsa del gateway applicazione.

### <a name="step-1"></a>Passaggio 1

Creare una configurazione IP del gateway applicazione denominata **gatewayIP01**. All'avvio, il gateway applicazione seleziona un indirizzo IP dalla subnet configurata e instrada il traffico di rete agli indirizzi IP nel pool di indirizzi IP back-end. Tenere presente che ogni istanza ha un indirizzo IP.

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet
```

### <a name="step-2"></a>Passaggio 2

Configurare il pool di indirizzi IP back-end denominato **pool01** con gli indirizzi IP per **pool1**. Questi indirizzi IP sono gli indirizzi IP delle risorse che ospitano l'applicazione Web che devono essere protette dal gateway applicazione. Tutti questi membri del pool back-end vengono convalidati come integri mediante i probe, che siano probe di base o personalizzati.  Il traffico viene instradato verso di loro quando le richieste arrivano al gateway applicazione. I pool di back-end possono essere usati dalle regole multiple all'interno del gateway applicazione, ovvero un pool di back-end può essere usato per più applicazioni Web che si trovano nello stesso host.

```powershell
$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221, 134.170.185.50
```

Questo esempio mostra due pool back-end che indirizzano il traffico di rete in base al percorso dell'URL. Un pool riceve il traffico dal percorso dell'URL "/video" e l'altro riceve il traffico dal percorso "/image". Sostituire gli indirizzi IP precedenti e aggiungere gli endpoint di indirizzi IP dell'applicazione.

### <a name="step-3"></a>Passaggio 3

Configurare l'impostazione **poolsetting01** del gateway applicazione per il traffico di rete con carico bilanciato nel pool back-end. Ogni pool back-end può avere un'impostazione del pool back-end dedicata.  Le impostazioni di back-end HTTP vengono usate dalle regole per instradare il traffico verso i membri del pool di back-end corretti. Le impostazioni di back-end HTTP determinano il protocollo e la porta usata per l'invio di traffico ai membri del pool di back-end. Anche le sessioni basate sui cookie sono determinate dalle impostazioni HTTP di back-end.  Se abilitata, l'affinità di sessione basata su cookie invia traffico allo stesso back-end sotto forma di richieste precedenti per ogni pacchetto.

```powershell
$poolSetting01 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting01" -Port 80 -Protocol Http -CookieBasedAffinity Disabled -RequestTimeout 120
```

### <a name="step-4"></a>Passaggio 4

Configurare la porta front-end per un gateway applicazione. L'oggetto di configurazione della porta front-end viene usato da un listener per definire la porta del gateway applicazione in ascolto del traffico nel listener.

```powershell
$fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80
```

### <a name="step-5"></a>Passaggio 5

Configurare l'indirizzo IP front-end con l'endpoint di indirizzo IP pubblico. L'oggetto di configurazione IP front-end viene usato da un listener per correlare l'indirizzo IP rivolto all'esterno con il listener.

```powershell
$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip
```

### <a name="step-6"></a>Passaggio 6

Configurare il listener. Questo passaggio configura il listener per l'indirizzo IP pubblico e la porta usata per ricevere il traffico di rete in ingresso. L'esempio seguente prende in considerazione la configurazione IP front-end configurata in precedenza, la configurazione della porta front-end e un protocollo (http o https) e configura il listener. In questo esempio, il listener è in ascolto per il traffico HTTP sulla porta 80 all'indirizzo IP pubblico che è stato creato in precedenza.

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp
```

### <a name="step-7"></a>Passaggio 7

Creare la regola di routing per il bilanciamento del carico denominata **rule01** che configura il comportamento per il bilanciamento del carico. Le impostazioni del pool di back-end, il listener e il pool di back-end creati nei passaggi precedenti costituiscono la regola. In base ai criteri definiti il traffico viene indirizzato al back-end appropriato.

```powershell
$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting01 -HttpListener $listener -BackendAddressPool $pool
```

### <a name="step-8"></a>Passaggio 8

Configurare il numero di istanze e le dimensioni per il gateway applicazione.

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
```

> [!NOTE]
> Il valore predefinito per **InstanceCount** è 2, con un valore massimo di 10. Il valore predefinito per **GatewaySize** è Medium. È possibile scegliere tra **Standard_Small**, **Standard_Medium** e **Standard_Large**.

## <a name="create-the-application-gateway"></a>Creare il gateway applicazione

Creare un gateway applicazione con tutti gli elementi di configurazione illustrati nei passaggi precedenti. In questo esempio il gateway applicazione è denominato **appgwtest**.

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku
```

Recuperare i dettagli relativi a DNS e indirizzo VIP del gateway applicazione dalla risorsa IP pubblica associata al gateway applicazione.

```powershell
Get-AzureRmPublicIpAddress -Name publicIP01 -ResourceGroupName appgw-rg  
```

## <a name="delete-the-application-gateway"></a>Eliminare il gateway applicazione

Per eliminare un gateway applicazione, seguire questa procedura:

### <a name="step-1"></a>Passaggio 1

Ottenere l'oggetto gateway applicazione e associarlo a una variabile `$getgw`.

```powershell
$getgw = Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg
```

### <a name="step-2"></a>Passaggio 2

Usare `Stop-AzureRmApplicationGateway` per arrestare il gateway applicazione.

```powershell
Stop-AzureRmApplicationGateway -ApplicationGateway $getgw
```

Quando il gateway applicazione si trova in stato di interruzione, usare il cmdlet `Remove-AzureRmApplicationGateway` per rimuovere il servizio.

```powershell
Remove-AzureRmApplicationGateway -Name $appgwtest -ResourceGroupName appgw-rg -Force
```

> [!NOTE]
> L'opzione **-force** può essere usata per eliminare il messaggio di conferma della rimozione.

Per verificare che il servizio sia stato rimosso, è possibile usare il cmdlet `Get-AzureRmApplicationGateway`. Questo passaggio non è obbligatorio.

```powershell
Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg
```

## <a name="get-application-gateway-dns-name"></a>Ottenere il nome DNS del gateway applicazione

Dopo avere creato il gateway, il passaggio successivo prevede la configurazione del front-end per la comunicazione. Quando si usa un IP pubblico, il gateway applicazione richiede un nome DNS assegnato in modo dinamico, non descrittivo. Per assicurarsi che gli utenti finali possano raggiungere il gateway applicazione, è possibile usare un record CNAME per fare riferimento all'endpoint pubblico del gateway applicazione. [Configurazione di un nome di dominio personalizzato in Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). Per trovare il nome DNS creato dinamicamente, recuperare i dettagli del gateway applicazione e il nome DNS e l'IP associati usando l'elemento PublicIPAddress collegato al gateway applicazione. Il nome DNS del gateway applicazione dovrà essere usato per creare un record CNAME che associa le due applicazioni Web a questo nome DNS. Non è consigliabile usare record A perché l'indirizzo VIP può cambiare al riavvio del gateway applicazione.

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

## <a name="delete-all-resources"></a>Eliminare tutte le risorse

Per eliminare tutte le risorse create nell'esecuzione dell'esercizio, seguire questa procedura:

```powershell
Remove-AzureRmResourceGroup -Name appgw-RG
```

## <a name="next-steps"></a>Passaggi successivi

Per configurare l'offload SSL, visitare [Configurare un gateway applicazione per l'offload SSL](application-gateway-ssl.md).

Per configurare un gateway applicazione da usare con un servizio di bilanciamento del carico interno, visitare [Creare un gateway applicazione con un dispositivo di bilanciamento del carico interno (ILB)](application-gateway-ilb.md).

Per altre informazioni generali sulle opzioni di bilanciamento del carico, vedere:

* [Servizio di bilanciamento del carico di Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
* [Gestione traffico di Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)


