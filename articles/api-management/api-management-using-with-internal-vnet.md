---
title: Come usare Gestione API di Azure con le reti virtuali interne | Microsoft Docs
description: Informazioni su come installare e configurare Gestione API di Azure in una rete virtuale interna.
services: api-management
documentationcenter: ''
author: vladvino
manager: kjoshi
editor: ''
ms.assetid: dac28ccf-2550-45a5-89cf-192d87369bc3
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2019
ms.author: apimpm
ms.openlocfilehash: 7db40de921c0eb8826a2fee832c1a51c57796f6d
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64919828"
---
# <a name="using-azure-api-management-service-with-an-internal-virtual-network"></a>Uso del servizio Gestione API di Azure con una rete virtuale interna
Grazie alle reti virtuali di Azure, Gestione API è in grado di gestire API non accessibili su Internet. Sono disponibili varie tecnologie VPN per stabilire la connessione. È possibile distribuire Gestione API in due modalità principali all'interno di una rete virtuale:
* Esterno
* Interno

Quando il servizio Gestione API viene installato in modalità di rete virtuale interna, tutti gli endpoint di servizio, ovvero gateway, portale per sviluppatori, portale di Azure, gestione diretta e GIT, sono visibili solo all'interno di una rete virtuale i cui accessi sono gestiti dall'utente. Nessuno degli endpoint di servizio è registrato nel server DNS pubblico.

Usando Gestione API in modalità interna è possibile implementare gli scenari seguenti:

* Consentire un accesso esterno sicuro da parte di terzi alle API ospitate in un data center privato, tramite connessioni VPN da sito a sito o Azure ExpressRoute.
* Abilitare scenari cloud ibridi esponendo le API basate su cloud e locali tramite un gateway comune.
* Gestire le API ospitate in più aree geografiche usando un singolo endpoint del gateway.

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Prerequisiti

Per eseguire i passaggi descritti in questo articolo, è necessario disporre di:

+ **Una sottoscrizione di Azure attiva**.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ **Un'istanza di Gestione API**. Per altre informazioni, vedere [Create an Azure API Management instance](get-started-create-service-instance.md) (Creare un'istanza di Gestione API di Azure).
+ Quando un servizio Gestione API viene distribuito in una rete virtuale, un [elenco di porte](./api-management-using-with-vnet.md#required-ports) vengono usate e devono essere aperte. 

## <a name="enable-vpn"> </a>Creazione di un servizio Gestione API in una rete virtuale interna
Il servizio Gestione API in una rete virtuale interna viene ospitato dietro un [servizio di bilanciamento del carico interno (classico)](https://docs.microsoft.com/azure/load-balancer/load-balancer-get-started-ilb-classic-cloud). Questo è l'unica opzione disponibile e non può essere modificato.

### <a name="enable-a-virtual-network-connection-using-the-azure-portal"></a>Abilitare la connessione a una rete virtuale usando il portale di Azure

1. Selezionare l'istanza di Gestione API di Azure nel [portale di Azure](https://portal.azure.com/).
2. Selezionare **Rete virtuale**.
3. Configurare l'istanza di Gestione API da distribuire all'interno della rete virtuale.

    ![Menu per la configurazione di Gestione API di Azure in una rete virtuale interna][api-management-using-internal-vnet-menu]

4. Selezionare **Salva**.

Dopo la distribuzione ha esito positivo, si noterà **privati** indirizzo IP virtuale e **pubblico** indirizzo IP virtuale del servizio Gestione API nel pannello Panoramica. Il **privati** indirizzo IP virtuale è un carico bilanciato indirizzo IP in Gestione API delegate subnet su cui `gateway`, `portal`, `management` e `scm` endpoint accessibili. Il **pubbliche** indirizzo IP virtuale usato **solo** per traffico del piano di controllo per `management` endpoint oltre porta 3443 e può essere bloccato verso il basso per il [ApiManagement] [ ServiceTags] servicetag.

![Dashboard di Gestione API con una rete virtuale interna configurata][api-management-internal-vnet-dashboard]

> [!NOTE]
> La console di test disponibile nel portale di Azure non funzionerà per il pacchetto distribuito VNET **interno**, poiché l'URL del gateway non è registrato nel DNS pubblico. In questo caso, è necessario usare la console di test disponibile nel **portale per sviluppatori**.

### <a name="enable-a-virtual-network-connection-by-using-powershell-cmdlets"></a>Abilitare una connessione di rete virtuale tramite i cmdlet di PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

È possibile abilitare la connettività a una rete virtuale anche usando i cmdlet di PowerShell.

* Creare un servizio Gestione API all'interno di una rete virtuale: Usare il cmdlet [New-AzApiManagement](/powershell/module/az.apimanagement/new-azapimanagement) per creare un servizio Gestione API di Azure all'interno di una rete virtuale e configurarlo per usare il tipo di rete virtuale interna.

* Aggiornare una distribuzione esistente di un servizio Gestione API all'interno di una rete virtuale: Usare il cmdlet [Update-AzApiManagementRegion](/powershell/module/az.apimanagement/update-azapimanagementregion) per spostare un servizio Gestione API esistente all'interno di una rete virtuale e configurarlo per usare il tipo di rete virtuale interna.

## <a name="apim-dns-configuration"></a>Configurazione del DNS
Quando si usa Gestione API in modalità di rete virtuale esterna, il servizio DNS è gestito da Azure. Per la modalità di rete virtuale interna, è necessario gestire il routing proprio.

> [!NOTE]
> Il servizio Gestione API non ascolta le richieste in arrivo dagli indirizzi IP. Risponde solo alle richieste per il nome host configurato negli endpoint di servizio. Questi endpoint includono gateway, portale di Azure, portale per sviluppatori, endpoint di gestione diretta e GIT.

### <a name="access-on-default-host-names"></a>Accesso con i nomi host predefiniti
Quando si crea un servizio Gestione API, denominato "contosointernalvnet", ad esempio, per impostazione predefinita vengono configurati i seguenti endpoint di servizio:

   * Gateway o proxy: Azure-API.NET contosointernalvnet.azure

   * Il portale di Azure e il portale per sviluppatori: Azure-API.NET contosointernalvnet.portal.azure

   * Endpoint di gestione diretta: Azure-API.NET contosointernalvnet.management.azure

   * Git: contosointernalvnet.scm.azure-api.net

Per accedere a questi endpoint del servizio Gestione API è possibile creare una macchina virtuale in una subnet connessa alla rete virtuale in cui viene distribuito Gestione API. Supponendo che l'indirizzo IP virtuale interno per il servizio sia 10.1.0.5, è possibile eseguire il mapping del file hosts, % SystemDrive%\drivers\etc\hosts, come indicato di seguito:

   * 10.1.0.5     contosointernalvnet.azure-api.net

   * 10.1.0.5     contosointernalvnet.portal.azure-api.net

   * 10.1.0.5     contosointernalvnet.management.azure-api.net

   * 10.1.0.5     contosointernalvnet.scm.azure-api.net

È quindi possibile accedere a tutti gli endpoint di servizio dalla macchina virtuale creata.
Se si usa un server DNS personalizzato in una rete virtuale, è anche possibile creare record DNS A e accedere a questi endpoint da qualsiasi punto nella rete virtuale.

### <a name="access-on-custom-domain-names"></a>Accesso con i nomi di dominio personalizzati

1. Se non si vuole accedere al servizio Gestione API con i nomi host predefiniti, è possibile configurare nomi di dominio personalizzati per tutti gli endpoint di servizio come mostrato nella figura seguente:

   ![Configurazione di un dominio personalizzato per Gestione API][api-management-custom-domain-name]

2. È quindi possibile creare record nel server DNS per accedere agli endpoint accessibili solo dall'interno della rete virtuale.

## <a name="routing"> </a> Routing
+ Verrà riservato e usato un indirizzo IP virtuale privato con bilanciamento del carico dall'intervallo della subnet per accedere agli endpoint del servizio Gestione API dall'interno della rete virtuale.
+ Verrà inoltre riservato un indirizzo IP pubblico (VIP) con bilanciamento del carico per fornire l'accesso all'endpoint del servizio di gestione solo sulla porta 3443.
+ Verranno usati un indirizzo IP di un intervallo IP di subnet (DIP) per accedere alle risorse all'interno della rete rituale e un indirizzo IP pubblico (VIP) per accedere alle risorse esterne alla rete virtuale.
+ Gli indirizzi IP pubblici e privati con bilanciamento del carico sono disponibili nel pannello Panoramica/Informazioni di base del portale di Azure.

## <a name="related-content"></a>Contenuti correlati
Per altre informazioni, vedere gli articoli seguenti:
* [Problemi comuni di configurazione di rete durante la configurazione di Gestione API di Azure in una rete virtuale][Common network configuration problems]
* [Domande frequenti sulla rete virtuale di Azure](../virtual-network/virtual-networks-faq.md)
* [Creazione di un record in DNS](/previous-versions/windows/it-pro/windows-2000-server/bb727018(v=technet.10))

[api-management-using-internal-vnet-menu]: ./media/api-management-using-with-internal-vnet/api-management-using-with-internal-vnet.png
[api-management-internal-vnet-dashboard]: ./media/api-management-using-with-internal-vnet/api-management-internal-vnet-dashboard.png
[api-management-custom-domain-name]: ./media/api-management-using-with-internal-vnet/api-management-custom-domain-name.png

[Create API Management service]: get-started-create-service-instance.md
[Common network configuration problems]: api-management-using-with-vnet.md#network-configuration-issues

[ServiceTags]: ../virtual-network/security-overview.md#service-tags

