---
title: Visualizzare le metriche con monitoraggio di Azure
titleSuffix: Azure Digital Twins
description: Vedere come visualizzare le metriche di Azure Digital gemelli in monitoraggio di Azure.
author: baanders
ms.author: baanders
ms.date: 8/4/2020
ms.topic: troubleshooting
ms.service: digital-twins
ms.openlocfilehash: f2dc93767457bfb96a9457a73adb83c0ed965308
ms.sourcegitcommit: 1fe5127fb5c3f43761f479078251242ae5688386
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2020
ms.locfileid: "90069748"
---
# <a name="troubleshooting-azure-digital-twins-metrics"></a>Risoluzione dei problemi relativi ai dispositivi gemelli digitali di Azure: metriche

Le metriche descritte in questo articolo offrono informazioni sullo stato delle risorse dei dispositivi gemelli digitali di Azure nella sottoscrizione di Azure. Le metriche dei dispositivi gemelli digitali di Azure consentono di valutare l'integrità complessiva del servizio dispositivi gemelli digitali di Azure e le risorse connesse. Queste statistiche per l'utente consentono di vedere cosa accade con i dispositivi gemelli digitali di Azure e consentono di eseguire l'analisi della causa radice dei problemi senza dover contattare il supporto tecnico di Azure.

Le metriche sono abilitate per impostazione predefinita. È possibile visualizzare le metriche di Azure Digital gemelli dal [portale di Azure](https://portal.azure.com).

## <a name="how-to-view-azure-digital-twins-metrics"></a>Come visualizzare le metriche di Azure gemelli digitali

1. Creare un'istanza di dispositivi gemelli digitali di Azure. Per istruzioni su come configurare un'istanza di dispositivi gemelli digitali di Azure, vedere [*procedura: configurare un'istanza e l'autenticazione*](how-to-set-up-instance-scripted.md).

2. Trovare l'istanza di Azure Digital gemelli nella [portale di Azure](https://portal.azure.com) (è possibile aprire la relativa pagina digitando il relativo nome nella barra di ricerca del portale). 

    Dal menu dell'istanza selezionare **metriche**.
   
    :::image type="content" source="media/troubleshoot-metrics/azure-digital-twins-metrics.png" alt-text="Screenshot che mostra la pagina delle metriche per i dispositivi gemelli digitali di Azure":::

    Questa pagina mostra le metriche per l'istanza di Azure Digital gemelli. È anche possibile creare visualizzazioni personalizzate delle metriche selezionando quelle che si desidera visualizzare nell'elenco.
    
3. È possibile scegliere di inviare i dati di metrica a un endpoint di hub eventi o a un account di archiviazione di Azure selezionando **impostazioni di diagnostica** dal menu, quindi **Aggiungi impostazione di diagnostica**.

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings.png" alt-text="Screenshot che mostra la pagina delle impostazioni di diagnostica e il pulsante per aggiungere":::

    Per altre informazioni su questo processo, vedere [*risoluzione dei problemi: configurare la diagnostica*](troubleshoot-diagnostics.md).

4. È possibile scegliere di configurare gli avvisi per i dati di metrica selezionando **avvisi** dal menu, quindi **+ nuova regola di avviso**.
    :::image type="content" source="media/troubleshoot-alerts/alerts-pre.png" alt-text="Screenshot che mostra la pagina e il pulsante avvisi per aggiungere":::

    Per ulteriori informazioni su questo processo, vedere [*Troubleshooting: set up Alerts*](troubleshoot-alerts.md).

## <a name="azure-digital-twins-metrics-and-how-to-use-them"></a>Metriche per i dispositivi gemelli digitali di Azure e come usarle

I dispositivi gemelli digitali di Azure forniscono diverse metriche per offrire una panoramica dell'integrità dell'istanza e delle risorse associate. È anche possibile combinare le informazioni di più metriche per disegnare un'immagine più ampia dello stato dell'istanza. 

Le tabelle seguenti descrivono le metriche registrate da ogni istanza di Azure Digital Twins e il modo in cui ogni metrica è correlata allo stato generale dell'istanza.

#### <a name="api-request-metrics"></a>Metriche delle richieste API

Metriche che è necessario eseguire con le richieste API:

| Metrica | Nome visualizzato metrica | Unità | Tipo di aggregazione| Descrizione | Dimensioni |
| --- | --- | --- | --- | --- | --- |
| ApiRequests | Richieste API (anteprima) | Conteggio | Totale | Il numero di richieste API effettuate per le operazioni di lettura, scrittura, eliminazione e query dei dispositivi gemelli digitali. |  Autenticazione <br>Operazione <br>Protocollo <br>Codice di stato, <br>Classe di codice di stato, <br>Testo stato |
| ApiRequestsFailureRate | Frequenza errori richieste API (anteprima) | Percentuale | Media | Percentuale di richieste API ricevute dal servizio per l'istanza che forniscono un codice di risposta errore interno (500) per le operazioni di lettura, scrittura, eliminazione e query dei dispositivi gemelli digitali. | Autenticazione <br>Operazione <br>Protocollo <br>Codice di stato, <br>Classe di codice di stato, <br>Testo stato
| ApiRequestsLatency | Latenza richieste API (anteprima) | Millisecondi | Media | Tempo di risposta per le richieste API. Questo si riferisce al momento in cui la richiesta viene ricevuta dai dispositivi gemelli digitali di Azure finché il servizio non invia un risultato di esito positivo/negativo per le operazioni di lettura, scrittura, eliminazione e query dei dispositivi gemelli digitali. | Autenticazione <br>Operazione <br>Protocollo |

#### <a name="billing-metrics"></a>Metriche di fatturazione

Metriche che è necessario eseguire con la fatturazione:

>[!NOTE]
> Durante l'anteprima, **la fatturazione è a costo zero**. Sebbene queste metriche siano ancora visualizzate nell'elenco selezionabile, non si applicano durante la fase di anteprima e rimarranno azzerate fino a quando il servizio non verrà spostato oltre l'anteprima.

| Metrica | Nome visualizzato metrica | Unità | Tipo di aggregazione| Descrizione | Dimensioni |
| --- | --- | --- | --- | --- | --- |
| BillingApiOperations | Operazioni dell'API di fatturazione (anteprima) | Conteggio | Totale | Metrica di fatturazione per il conteggio di tutte le richieste API effettuate nel servizio Azure Digital Twins. | ID contatore |
| BillingMessagesProcessed | Messaggi di fatturazione elaborati (anteprima) | Conteggio | Totale | Metrica di fatturazione per il numero di messaggi inviati dai dispositivi gemelli digitali di Azure agli endpoint esterni. | ID contatore |
| BillingQueryUnits | Unità query di fatturazione (anteprima) | Conteggio | Totale | Il numero di unità di query, una misura calcolata internamente dell'utilizzo delle risorse del servizio, utilizzata per eseguire le query. È disponibile anche un'API helper per la misurazione delle unità di query: [classe QueryChargeHelper](https://docs.microsoft.com/dotnet/api/azure.digitaltwins.core.querychargehelper?view=azure-dotnet-preview&preserve-view=true) | ID contatore |

#### <a name="ingress-metrics"></a>Metriche in ingresso

Metriche che è necessario eseguire con l'ingresso dei dati:

| Metrica | Nome visualizzato metrica | Unità | Tipo di aggregazione| Descrizione | Dimensioni |
| --- | --- | --- | --- | --- | --- |
| IngressEvents | Eventi in ingresso (anteprima) | Conteggio | Totale | Il numero di eventi di telemetria in ingresso nei dispositivi gemelli digitali di Azure. | Risultato |
| IngressEventsFailureRate | Frequenza degli errori degli eventi in ingresso (anteprima) | Percentuale | Media | Percentuale di eventi di telemetria in ingresso per i quali il servizio restituisce un codice di risposta errore interno (500). | Risultato |
| IngressEventsLatency | Latenza eventi in ingresso (anteprima) | Millisecondi | Media | Data e ora di arrivo di un evento quando è pronto per essere uscita dai dispositivi gemelli digitali di Azure, a quel punto il servizio invia un risultato di esito positivo o negativo. | Risultato |

#### <a name="routing-metrics"></a>Metriche di routing

Metriche che è necessario eseguire con il routing:

| Metrica | Nome visualizzato metrica | Unità | Tipo di aggregazione| Descrizione | Dimensioni |
| --- | --- | --- | --- | --- | --- |
| MessagesRouted | Messaggi instradati (anteprima) | Conteggio | Totale | Il numero di messaggi indirizzati a un servizio endpoint di Azure, ad esempio hub eventi, bus di servizio o griglia di eventi. | Tipo di endpoint, <br>Risultato |
| RoutingFailureRate | Frequenza errori di routing (anteprima) | Percentuale | Media | Percentuale di eventi che generano un errore quando vengono instradati da dispositivi gemelli digitali di Azure a un servizio endpoint di Azure, ad esempio hub eventi, bus di servizio o griglia di eventi. | Tipo di endpoint, <br>Risultato |
| RoutingLatency | Latenza routing (anteprima) | Millisecondi | Media | Tempo trascorso tra un evento che viene indirizzato dai dispositivi gemelli digitali di Azure a quando viene inviato al servizio endpoint di Azure, ad esempio hub eventi, bus di servizio o griglia di eventi. | Tipo di endpoint, <br>Risultato |

## <a name="dimensions"></a>Dimensioni

Le dimensioni consentono di identificare altri dettagli sulle metriche. Alcune metriche di routing forniscono informazioni per ogni endpoint. Nella tabella seguente sono elencati i valori possibili per queste dimensioni.

| Dimensione | Valori |
| --- | --- |
| Authentication | OAuth |
| Operazione (per le richieste API) | Microsoft. DigitalTwins/DigitalTwins/Delete, <br>Microsoft. DigitalTwins/DigitalTwins/Write, <br>Microsoft. DigitalTwins/DigitalTwins/Read, <br>Microsoft. DigitalTwins/eventroutes/Read, <br>Microsoft. DigitalTwins/eventroutes/Write, <br>Microsoft. DigitalTwins/eventroutes/Delete, <br>Microsoft. DigitalTwins/Models/Read, <br>Microsoft. DigitalTwins/Models/Write, <br>Microsoft. DigitalTwins/Models/Delete, <br>Microsoft. DigitalTwins/query/Action |
| Tipo di endpoint | Griglia di eventi, <br>Hub eventi, <br>Bus di servizio |
| Protocollo | HTTPS |
| Risultato | Successo <br>Errore |
| Codice di stato | 200, 404, 500 e così via. |
| Classe di codice di stato | 2xx, 4xx, 5xx e così via. |
| Testo stato | Errore interno del server, non trovato e così via. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla gestione delle metriche registrate per i dispositivi gemelli digitali di Azure, vedere [*risoluzione dei problemi: configurare la diagnostica*](troubleshoot-diagnostics.md).