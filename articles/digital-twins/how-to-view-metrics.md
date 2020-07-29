---
title: Visualizzare le metriche con monitoraggio di Azure
titleSuffix: Azure Digital Twins
description: Vedere come visualizzare le metriche di Azure Digital gemelli in monitoraggio di Azure.
author: baanders
ms.author: baanders
ms.date: 7/24/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 09e32d5baa367b76c34ebca28adfdd20385e4e18
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87374552"
---
# <a name="view-and-understand-azure-digital-twins-metrics"></a>Visualizzare e comprendere le metriche di dispositivi gemelli digitali di Azure

Queste metriche forniscono informazioni sullo stato delle risorse dei dispositivi gemelli digitali di Azure nella sottoscrizione di Azure. Le metriche dei dispositivi gemelli digitali di Azure consentono di valutare l'integrità complessiva del servizio dispositivi gemelli digitali di Azure e le risorse connesse. Queste statistiche per l'utente consentono di vedere cosa accade con i dispositivi gemelli digitali di Azure e consentono di eseguire l'analisi della causa radice dei problemi senza dover contattare il supporto tecnico di Azure.

Le metriche sono abilitate per impostazione predefinita. È possibile visualizzare le metriche di Azure Digital gemelli dal [portale di Azure](https://portal.azure.com).

## <a name="how-to-view-azure-digital-twins-metrics"></a>Come visualizzare le metriche di Azure gemelli digitali

1. Creare un'istanza di dispositivi gemelli digitali di Azure. Per istruzioni su come configurare un'istanza di dispositivi gemelli digitali di Azure, vedere [*procedura: configurare un'istanza e l'autenticazione*](how-to-set-up-instance-scripted.md).

2. Trovare l'istanza di Azure Digital gemelli nella [portale di Azure](https:/portal.azure.com) (è possibile aprire la relativa pagina digitando il relativo nome nella barra di ricerca del portale). 

    Dal menu dell'istanza selezionare **metriche**.
   
    :::image type="content" source="media/how-to-view-metrics/azure-digital-twins-metrics.png" alt-text="Screenshot che mostra la pagina delle metriche per i dispositivi gemelli digitali di Azure":::

    Questa pagina mostra le metriche per l'istanza di Azure Digital gemelli. È anche possibile creare visualizzazioni personalizzate delle metriche selezionando quelle che si desidera visualizzare nell'elenco.
    
3. È possibile scegliere di inviare i dati di metrica a un endpoint di hub eventi o a un account di archiviazione di Azure facendo clic su **impostazioni di diagnostica** dal menu e quindi su **Aggiungi impostazione di diagnostica**.

    :::image type="content" source="media/how-to-view-metrics/diagnostic-settings.png" alt-text="Screenshot che mostra la pagina delle impostazioni di diagnostica e il pulsante per aggiungere":::

    Per altre informazioni su questo processo, vedere [*procedura: configurare la diagnostica*](how-to-set-up-diagnostics.md).

## <a name="azure-digital-twins-metrics-and-how-to-use-them"></a>Metriche per i dispositivi gemelli digitali di Azure e come usarle

I dispositivi gemelli digitali di Azure forniscono diverse metriche per offrire una panoramica dell'integrità dell'istanza e delle risorse associate. È anche possibile combinare le informazioni di più metriche per disegnare un'immagine più ampia dello stato dell'istanza. 

Le tabelle seguenti descrivono le metriche registrate da ogni istanza di Azure Digital Twins e il modo in cui ogni metrica è correlata allo stato generale dell'istanza.

#### <a name="api-request-metrics"></a>Metriche delle richieste API

Metriche che è necessario eseguire con le richieste API:

| Metrica | Nome visualizzato metrica | Unità | Tipo di aggregazione| Descrizione | Dimensioni |
| --- | --- | --- | --- | --- | --- |
| ApiRequests | Richieste API (anteprima) | Conteggio | Totale | Il numero di richieste API effettuate per le operazioni di lettura, scrittura, eliminazione e query dei dispositivi gemelli digitali. |  Authentication </br>Operazione </br>Protocollo </br>Codice di stato </br>Classe di codice di stato </br>Testo stato |
| ApiRequestsLatency | Latenza richieste API (anteprima) | Millisecondi | Media | Tempo di risposta per le richieste API. Questo si riferisce al momento in cui la richiesta viene ricevuta dai dispositivi gemelli digitali di Azure finché il servizio non invia un risultato di esito positivo/negativo per le operazioni di lettura, scrittura, eliminazione e query dei dispositivi gemelli digitali. | Authentication </br>Operazione </br>Protocollo |
| ApiRequestsFailureRate | Frequenza errori richieste API (anteprima) | Percentuale | Media | Percentuale di richieste API ricevute dal servizio per l'istanza che forniscono un codice di risposta errore interno (500) per le operazioni di lettura, scrittura, eliminazione e query dei dispositivi gemelli digitali. | Authentication </br>Operazione </br>Protocollo </br>Codice di stato </br>Classe di codice di stato </br>Testo stato

#### <a name="routing-metrics"></a>Metriche di routing

Metriche che è necessario eseguire con il routing:

| Metrica | Nome visualizzato metrica | Unità | Tipo di aggregazione| Descrizione | Dimensioni |
| --- | --- | --- | --- | --- | --- |
| Routing | Routing (anteprima) | Conteggio | Totale | Il numero di messaggi indirizzati a un servizio endpoint di Azure, ad esempio hub eventi, bus di servizio o griglia di eventi. | Operazione </br>Risultato |
| RoutingLatency | Latenza routing (anteprima) | Millisecondi | Media | Tempo trascorso tra un evento che viene indirizzato dai dispositivi gemelli digitali di Azure a quando viene inviato al servizio endpoint di Azure, ad esempio hub eventi, bus di servizio o griglia di eventi. | Operazione </br>Risultato |
| RoutingFailureRate | Frequenza errori di routing (anteprima) | Percentuale | Media | Percentuale di eventi che generano un errore quando vengono instradati da dispositivi gemelli digitali di Azure a un servizio endpoint di Azure, ad esempio hub eventi, bus di servizio o griglia di eventi. | Operazione </br>Risultato |

#### <a name="billing-metrics"></a>Metriche di fatturazione

Metriche che è necessario eseguire con la fatturazione:

>[!NOTE]
> Durante l'anteprima, **la fatturazione è a costo zero**. Sebbene queste metriche siano ancora visualizzate nell'elenco selezionabile, non si applicano durante la fase di anteprima e rimarranno azzerate fino a quando il servizio non verrà spostato oltre l'anteprima.

| Metrica | Nome visualizzato metrica | Unità | Tipo di aggregazione| Descrizione | Dimensioni |
| --- | --- | --- | --- | --- | --- |
| BillingApiOperations | Operazioni dell'API di fatturazione (anteprima) | Conteggio | Totale | Metrica di fatturazione per il conteggio di tutte le richieste API effettuate nel servizio Azure Digital Twins. | `MeterId` |
| BillingQueryUnits | Unità query di fatturazione (anteprima) | Conteggio | Totale | Il numero di unità di query, una misura calcolata internamente dell'utilizzo delle risorse del servizio, utilizzata per eseguire le query. È disponibile anche un'API helper per la misurazione delle unità di query: [classe QueryChargeHelper](https://docs.microsoft.com/dotnet/api/azure.digitaltwins.core.querychargehelper?view=azure-dotnet-preview) | `MeterId` |
| BillingMessagesProcessed | Messaggi di fatturazione elaborati (anteprima) | Conteggio | Totale | Metrica di fatturazione per il numero di messaggi inviati dai dispositivi gemelli digitali di Azure agli endpoint esterni. | `MeterId` |

## <a name="dimensions"></a>Dimensioni

Le dimensioni consentono di identificare altri dettagli sulle metriche. Alcune metriche di routing forniscono informazioni per ogni endpoint. Nella tabella seguente sono elencati i valori possibili per queste dimensioni.

| Dimension | Valori |
| --- | --- |
| Authentication | OAuth |
| Operazione (per le richieste API) | Microsoft. DigitalTwins/DigitalTwins/Delete</br>Microsoft. DigitalTwins/DigitalTwins/Write</br>Microsoft. DigitalTwins/DigitalTwins/Read </br>Microsoft. DigitalTwins/eventroutes/Read </br>Microsoft. DigitalTwins/eventroutes/Write </br>Microsoft. DigitalTwins/eventroutes/Delete </br>Microsoft. DigitalTwins/Models/Read </br>Microsoft. DigitalTwins/Models/Write </br>Microsoft. DigitalTwins/Models/Delete </br>Microsoft. DigitalTwins/query/Action |
Operazione (per il routing) | Griglia di eventi </br>Hub eventi </br>Bus di servizio |
| Protocollo | HTTPS |
| Risultato | Operazione completata </br>Operazioni non riuscite |
| Codice di stato | 200, 404, 500 e così via. |
| Classe di codice di stato | 2xx, 4xx, 5xx e così via. |
| Testo stato | Errore interno del server, non trovato e così via. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla gestione delle metriche registrate per i dispositivi gemelli digitali di Azure, vedere [*procedura: configurare la diagnostica*](how-to-set-up-diagnostics.md).

In alternativa, ora che è stata esaminata una panoramica delle metriche dei dispositivi gemelli digitali di Azure, seguire questi collegamenti per altre informazioni sulla gestione dei dispositivi gemelli digitali di Azure:

* [*Procedura: usare le API e gli SDK di dispositivi digitali gemelli di Azure*](how-to-use-apis-sdks.md)
* [*Procedura: Gestire modelli personalizzati*](how-to-manage-model.md)
* [*Procedura: gestire i dispositivi gemelli digitali*](how-to-manage-twin.md)
* [*Procedura: gestire il grafo gemello con relazioni*](how-to-manage-graph.md)
* [*Procedura: gestire endpoint e Route*](how-to-manage-routes.md)