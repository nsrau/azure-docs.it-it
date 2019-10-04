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
ms.topic: article
ms.date: 07/31/2019
ms.author: apimpm
ms.openlocfilehash: 29c86363842299870179b35a0466d2e44d2e56e0
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072195"
---
# <a name="using-azure-api-management-service-with-an-internal-virtual-network"></a>Uso del servizio Gestione API di Azure con una rete virtuale interna
Grazie alle reti virtuali di Azure, Gestione API è in grado di gestire API non accessibili su Internet. Sono disponibili varie tecnologie VPN per stabilire la connessione. È possibile distribuire Gestione API in due modalità principali all'interno di una rete virtuale:
* Altre informazioni
* Interne

Quando Gestione API viene distribuito in modalità di rete virtuale interna, tutti gli endpoint di servizio, ovvero il gateway proxy, il portale per sviluppatori, la gestione diretta e git, sono visibili solo all'interno di una rete virtuale a cui si controlla l'accesso. Nessuno degli endpoint di servizio è registrato nel server DNS pubblico.

> [!NOTE]
> Poiché non sono presenti voci DNS per gli endpoint del servizio, questi endpoint non saranno accessibili fino a quando non [viene configurato il DNS](#apim-dns-configuration) per la rete virtuale.

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
+ Quando un servizio gestione API viene distribuito in una rete virtuale, viene usato un [elenco di porte](./api-management-using-with-vnet.md#required-ports) che devono essere aperte. 

## <a name="enable-vpn"> </a>Creazione di un servizio Gestione API in una rete virtuale interna
Il servizio gestione API in una rete virtuale interna è ospitato dietro un servizio di [bilanciamento del carico interno (classico)](https://docs.microsoft.com/azure/load-balancer/load-balancer-get-started-ilb-classic-cloud). Questa è l'unica opzione disponibile e non può essere modificata.

### <a name="enable-a-virtual-network-connection-using-the-azure-portal"></a>Abilitare la connessione a una rete virtuale usando il portale di Azure

1. Selezionare l'istanza di Gestione API di Azure nel [portale di Azure](https://portal.azure.com/).
2. Selezionare **Rete virtuale**.
3. Configurare l'istanza di Gestione API da distribuire all'interno della rete virtuale.

    ![Menu per la configurazione di Gestione API di Azure in una rete virtuale interna][api-management-using-internal-vnet-menu]

4. Selezionare **Salva**.

Una volta completata la distribuzione, verranno visualizzati l'indirizzo IP virtuale **privato** e l'indirizzo IP virtuale **pubblico** del servizio gestione API nel pannello panoramica. L'indirizzo IP virtuale **privato** è un indirizzo IP con carico bilanciato dall'interno della subnet delegata `gateway`di `portal`gestione API su cui è possibile accedere agli endpoint, `management` e `scm` . L'indirizzo IP virtuale **pubblico** viene usato **solo** per il traffico del piano `management` di controllo verso l'endpoint sulla porta 3443 e può essere bloccato al serviceTag [ApiManagement][ServiceTags] .

![Dashboard di Gestione API con una rete virtuale interna configurata][api-management-internal-vnet-dashboard]

> [!NOTE]
> La console di test disponibile nel portale di Azure non funzionerà per il pacchetto distribuito VNET **interno**, poiché l'URL del gateway non è registrato nel DNS pubblico. In questo caso, è necessario usare la console di test disponibile nel **portale per sviluppatori**.

### <a name="enable-a-virtual-network-connection-by-using-powershell-cmdlets"></a>Abilitare una connessione di rete virtuale tramite i cmdlet di PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

È possibile abilitare la connettività a una rete virtuale anche usando i cmdlet di PowerShell.

* Creare un servizio gestione API all'interno di una rete virtuale: Usare il cmdlet [New-AzApiManagement](/powershell/module/az.apimanagement/new-azapimanagement) per creare un servizio gestione API di Azure all'interno di una rete virtuale e configurarlo per usare il tipo di rete virtuale interna.

* Aggiornare una distribuzione esistente di un servizio gestione API all'interno di una rete virtuale: Usare il cmdlet [Update-AzApiManagementRegion](/powershell/module/az.apimanagement/update-azapimanagementregion) per spostare un servizio gestione API esistente all'interno di una rete virtuale e configurarlo per l'uso del tipo di rete virtuale interna.

## <a name="apim-dns-configuration"></a>Configurazione del DNS
Quando si usa Gestione API in modalità di rete virtuale esterna, il servizio DNS è gestito da Azure. Per la modalità di rete virtuale interna, è necessario gestire il routing proprio.

> [!NOTE]
> Il servizio Gestione API non ascolta le richieste in arrivo dagli indirizzi IP. Risponde solo alle richieste per il nome host configurato negli endpoint di servizio. Questi endpoint includono gateway, portale di Azure, portale per sviluppatori, endpoint di gestione diretta e GIT.

### <a name="access-on-default-host-names"></a>Accesso con i nomi host predefiniti
Quando si crea un servizio gestione API, denominato "contosointernalvnet", ad esempio, per impostazione predefinita vengono configurati gli endpoint di servizio seguenti:

   * Gateway o proxy: contosointernalvnet.azure-api.net

   * Portale per sviluppatori: contosointernalvnet.portal.azure-api.net

   * Il nuovo portale per sviluppatori: contosointernalvnet.developer.azure-api.net

   * Endpoint di gestione diretta: contosointernalvnet.management.azure-api.net

   * Git: contosointernalvnet.scm.azure-api.net

Per accedere a questi endpoint del servizio Gestione API è possibile creare una macchina virtuale in una subnet connessa alla rete virtuale in cui viene distribuito Gestione API. Supponendo che l'indirizzo IP virtuale interno per il servizio sia 10.1.0.5, è possibile eseguire il mapping del file hosts,%SystemDrive%\drivers\etc\hosts, come indicato di seguito:

   * 10.1.0.5     contosointernalvnet.azure-api.net

   * contosointernalvnet.portal.azure-api.net 10.1.0.5

   * contosointernalvnet.developer.azure-api.net 10.1.0.5

   * contosointernalvnet.management.azure-api.net 10.1.0.5

   * 10.1.0.5     contosointernalvnet.scm.azure-api.net

È quindi possibile accedere a tutti gli endpoint di servizio dalla macchina virtuale creata.
Se si usa un server DNS personalizzato in una rete virtuale, è anche possibile creare record DNS A e accedere a questi endpoint da qualsiasi punto nella rete virtuale.

### <a name="access-on-custom-domain-names"></a>Accesso con i nomi di dominio personalizzati

1. Se non si vuole accedere al servizio Gestione API con i nomi host predefiniti, è possibile configurare nomi di dominio personalizzati per tutti gli endpoint di servizio come mostrato nella figura seguente:

   ![Configurazione di un dominio personalizzato per Gestione API][api-management-custom-domain-name]

2. È quindi possibile creare record nel server DNS per accedere agli endpoint accessibili solo dall'interno della rete virtuale.

## <a name="routing"> </a> Routing

* Un indirizzo IP virtuale *privato* con bilanciamento del carico dall'intervallo di subnet verrà riservato e usato per accedere agli endpoint del servizio gestione API dall'interno della rete virtuale. Questo indirizzo IP *privato* è disponibile nel pannello panoramica per il servizio nel portale di Azure. Questo indirizzo deve essere registrato con i server DNS utilizzati dalla rete virtuale.
* Un indirizzo IP *pubblico* (VIP) con carico bilanciato verrà inoltre riservato per fornire l'accesso all'endpoint del servizio di gestione sulla porta 3443. Questo indirizzo IP *pubblico* è disponibile nel pannello panoramica per il servizio nel portale di Azure. L'indirizzo IP *pubblico* viene usato solo per il traffico del piano di `management` controllo verso l'endpoint sulla porta 3443 e può essere bloccato in [ApiManagement][ServiceTags] serviceTag.
* Gli indirizzi IP dell'intervallo IP della subnet (DIP) verranno assegnati a ogni macchina virtuale nel servizio e verranno usati per accedere alle risorse all'interno della rete virtuale. Un indirizzo IP pubblico (VIP) verrà usato per accedere alle risorse esterne alla rete virtuale. Se gli elenchi di restrizioni IP vengono usati per proteggere le risorse all'interno della rete virtuale, è necessario specificare l'intero intervallo per la subnet in cui è distribuito il servizio gestione API per concedere o limitare l'accesso al servizio.
* Gli indirizzi IP pubblici e privati con bilanciamento del carico sono disponibili nel pannello panoramica della portale di Azure.
* Gli indirizzi IP assegnati per l'accesso pubblico e privato possono cambiare se il servizio viene rimosso da e quindi aggiunto nuovamente alla rete virtuale. In tal caso, potrebbe essere necessario aggiornare le registrazioni DNS, le regole di routing e gli elenchi di restrizioni IP all'interno della rete virtuale.

## <a name="related-content"></a>Contenuti correlati
Per altre informazioni, vedere gli articoli seguenti:
* [Problemi comuni di configurazione di rete durante la configurazione di gestione API di Azure in una rete virtuale][Common network configuration problems]
* [Domande frequenti sulla rete virtuale di Azure](../virtual-network/virtual-networks-faq.md)
* [Creazione di un record in DNS](/previous-versions/windows/it-pro/windows-2000-server/bb727018(v=technet.10))

[api-management-using-internal-vnet-menu]: ./media/api-management-using-with-internal-vnet/api-management-using-with-internal-vnet.png
[api-management-internal-vnet-dashboard]: ./media/api-management-using-with-internal-vnet/api-management-internal-vnet-dashboard.png
[api-management-custom-domain-name]: ./media/api-management-using-with-internal-vnet/api-management-custom-domain-name.png

[Create API Management service]: get-started-create-service-instance.md
[Common network configuration problems]: api-management-using-with-vnet.md#network-configuration-issues

[ServiceTags]: ../virtual-network/security-overview.md#service-tags

