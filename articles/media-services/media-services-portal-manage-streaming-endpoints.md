---
title: Gestire gli endpoint di streaming con il portale di Azure | Microsoft Docs
description: Questo argomento illustra come gestire gli endpoint di streaming mediante il portale di Azure.
services: media-services
documentationcenter: 
author: Juliako
writer: juliako
manager: cfowler
editor: 
ms.assetid: bb1aca25-d23a-4520-8c45-44ef3ecd5371
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2017
ms.author: juliako
ms.openlocfilehash: 89b4f7cee6d00b5bbbca4799eeca74a280147fc7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="manage-streaming-endpoints-with-the-azure-portal"></a>Gestire gli endpoint di streaming con il portale di Azure

L'argomento illustra come usare il Portale di Azure per gestire gli endpoint di streaming. 

>[!NOTE]
>Assicurarsi di rivedere l'argomento sulla [panoramica](media-services-streaming-endpoints-overview.md). 

Per informazioni su come ridimensionare l'endpoint di streaming, vedere [questo](media-services-portal-scale-streaming-endpoints.md) argomento.

## <a name="start-managing-streaming-endpoints"></a>Iniziare a gestire gli endpoint di streaming 

Per iniziare a gestire gli endpoint di streaming per l'account, procedere come segue.

1. Nel [portale di Azure ](https://portal.azure.com/) selezionare l'account Servizi multimediali di Azure.
2. Nel pannello **Impostazioni** selezionare **Endpoint di streaming**.
   
    ![endpoint di streaming](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints1.png)

> [!NOTE]
> Il costo verrà addebitato solo quando StreamingEndpoint è in stato di esecuzione.

## <a name="adddelete-a-streaming-endpoint"></a>Aggiungere o eliminare un endpoint di streaming

>[!NOTE]
>Gli endpoint di streaming predefiniti non possono essere eliminati.

Per aggiungere o eliminare gli endpoint di streaming tramite il portale di Azure, procedere come segue:

1. Per aggiungere un endpoint di streaming, fare clic su **+ Endpoint** nella parte superiore della pagina. 

    Se si prevedono più reti CDN o una rete CDN e l'accesso diretto, potrebbero essere necessari più endpoint di streaming.

2. Per eliminare un endpoint di streaming, fare clic sul pulsante **Elimina** .      
3. Fare clic sul pulsante **Avvia** per avviare l'endpoint di streaming.
   
    ![endpoint di streaming](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints2.png)


## <a id="configure_streaming_endpoints"></a>Configurare l'endpoint di streaming
L'endpoint di streaming consente di configurare le seguenti proprietà:

* Controllo di accesso
* Controllo cache
* Criteri di accesso tra siti

Per informazioni dettagliate su queste proprietà, vedere [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/operations/streamingendpoint).

>[!NOTE]
>Quando la rete CDN è abilitata, non è possibile usare l'accesso per indirizzi IP. L'accesso per indirizzi IP è applicabile solo quando la rete CDN non è presente.

È possibile configurare un endpoint di streaming eseguendo le operazioni seguenti:

1. Selezionare l'endpoint di streaming che si desidera configurare.
2. Fare clic su **Impostazioni**.

Seguirà una breve descrizione dei campi.

![endpoint di streaming](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints4.png)

1. Criteri della cache massima: consente di configurare la durata della cache per gli asset serviti tramite questo endpoint di streaming. Se non si imposta alcun valore, viene usato il valore predefinito. I valori predefiniti possono anche essere definiti direttamente in Archiviazione di Azure. Se la rete CDN di Azure è abilitata per l'endpoint di streaming, non impostare il valore dei criteri della cache a meno di 600 secondi.  
2. Indirizzi IP consentiti: consente di specificare gli indirizzi IP che possono connettersi all'endpoint di streaming pubblicato. Se non viene specificato alcun indirizzo IP, la connessione è consentita a qualsiasi indirizzo IP. È possibile specificare gli indirizzi IP come un singolo indirizzo IP (ad esempio "10.0.0.1"), un intervallo IP con un indirizzo IP e una subnet mask CIDR (ad esempio "10.0.0.1/22") o un intervallo IP con un indirizzo IP e una subnet mask decimale puntata (ad esempio "10.0.0.1(255.255.255.0)").
3. Configurazione per l'autenticazione dell'intestazione firma Akamai: consente di specificare la configurazione della richiesta di autenticazione intestazione firma proveniente dai server Akamai. La scadenza è in formato UTC.

## <a name="scale-your-premium-streaming-endpoint"></a>Scalabilità dell'endpoint di streaming Premium

Per altre informazioni, vedere [questo](media-services-portal-scale-streaming-endpoints.md) argomento.

## <a id="enable_cdn"></a>Abilitare l'integrazione della rete CDN di Azure

Quando si crea un nuovo account, l'integrazione della rete CDN di Azure dell'endpoint di streaming predefinita viene abilitata per impostazione predefinita.

Se in seguito si desidera disabilitare o abilitare la rete CDN, l'endpoint di streaming deve essere nello stato **interrotto**. L'abilitazione dell'integrazione della rete CDN di Azure e l'attivazione delle modifiche in tutti i POP della rete CDN potrebbero richiedere fino a due ore. Tuttavia, è possibile avviare l'endpoint di streaming e il flusso senza interruzioni dall'endpoint di streaming e dopo aver completato l'integrazione, il flusso verrà distribuito dalla rete CDN. Durante il periodo di provisioning l'endpoint di streaming è nello stato **avvio** ed è possibile osservarne le prestazioni ridotte.

L'integrazione della rete CDN è abilitata in tutti i centri dati di Azure ad eccezione della Cina e delle aree con governi federali.

Dopo essere stata attivata, la configurazione di **Controllo di accesso**, **Nome host personalizzato** e **Autenticazione firma Akamai** viene disabilitata.
 
> [!IMPORTANT]
> L'integrazione di Servizi multimediali di Azure con la rete CDN di Azure è implementata nella **rete CDN di Azure da Verizon** per gli endpoint di streaming standard. Gli endpoint di streaming Premium possono essere configurati usando tutti **i provider e i livelli di prezzo della rete CDN di Azure**. Per altre informazioni sulle funzionalità della rete CDN di Azure, vedere la [Panoramica della rete per la distribuzione di contenuti (rete CDN) di Azure](../cdn/cdn-overview.md).
 
### <a name="additional-considerations"></a>Considerazioni aggiuntive

* Quando la rete CDN è abilitata per un endpoint di streaming, i client non possono richiedere il contenuto direttamente dall'origine. Se è necessario testare il contenuto con o senza la rete CDN, è possibile creare un altro endpoint di streaming per la rete CDN.
* Il nome host dell'endpoint di streaming rimane invariato dopo l'abilitazione della rete CDN. Non è necessario apportare modifiche al flusso di lavoro di Servizi multimediali dopo l'abilitazione della rete CDN. Ad esempio, se il nome host dell'endpoint di streaming è strasbourg.streaming.mediaservices.windows.net, dopo avere abilitato la rete CDN, viene usato lo stesso identico nome host.
* Per i nuovi endpoint di streaming, è possibile abilitare la rete CDN semplicemente creando un nuovo endpoint. Per gli endpoint di streaming esistenti, è necessario arrestare prima l'endpoint e quindi abilitare/disabilitare la rete CDN.
* L'endpoint di streaming standard può essere configurato solo tramite **il provider della rete CDN Standard di Verizon** nel portale di gestione di Azure. Tuttavia, è possibile abilitare altri provider della rete CDN di Azure usando le API REST.

## <a name="configure-cdn-profile"></a>Configurazione del profilo della rete CDN

È possibile configurare il profilo della rete CDN selezionando il pulsante **Gestisci rete CDN** nella parte superiore.

![endpoint di streaming](./media/media-services-portal-manage-streaming-endpoints/media-services-manage-streaming-endpoints6.png)

## <a name="next-steps"></a>Passaggi successivi
Analizzare i percorsi di apprendimento di Servizi multimediali.

[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

