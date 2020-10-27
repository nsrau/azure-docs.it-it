---
title: Risolvere i problemi di routing dei messaggi di Azure
description: Come eseguire la risoluzione dei problemi per il routing dei messaggi di Azure
author: ash2017
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: asrastog
ms.openlocfilehash: 29127a9dff42c0f733e3721d1ea5fea7350e774e
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92547359"
---
# <a name="troubleshooting-message-routing"></a>Risoluzione dei problemi relativi al routing dei messaggi

Questo articolo fornisce indicazioni sul monitoraggio e sulla risoluzione dei problemi comuni e sulla risoluzione per il [routing dei messaggi](iot-hub-devguide-messages-d2c.md)dell'hub Internet.

## <a name="monitoring-message-routing"></a>Monitoraggio del routing del messaggio

Si consiglia di monitorare le [metriche dell'hub cose correlate al routing dei messaggi e agli endpoint](monitor-iot-hub-reference.md#routing-metrics) per offrire una panoramica dei messaggi inviati. È anche possibile creare un'impostazione di diagnostica per inviare le operazioni per le [ **Route** nei log delle risorse dell'hub](monitor-iot-hub-reference.md#routes) Internet in log di monitoraggio di Azure, Hub eventi o archiviazione di Azure per l'elaborazione personalizzata. Per altre informazioni sull'uso delle metriche, i log delle risorse e le impostazioni di diagnostica, vedere [monitorare l'hub](monitor-iot-hub.md). Per un'esercitazione, vedere [configurare e usare le metriche e i log delle risorse con un hub](tutorial-use-metrics-and-diags.md)Internet.

Si consiglia inoltre di abilitare la [Route di fallback](iot-hub-devguide-messages-d2c.md#fallback-route) se si desidera mantenere i messaggi che non corrispondono alla query in nessuna delle route. Questi possono essere conservati nell' [endpoint predefinito](iot-hub-devguide-messages-read-builtin.md) per la quantità di giorni di conservazione configurata.

## <a name="top-issues"></a>Problemi principali

Di seguito sono riportati i problemi più comuni osservati con il routing dei messaggi. Per avviare la risoluzione dei problemi, fare clic sul problema per i passaggi dettagliati.

* [I messaggi dei dispositivi non vengono indirizzati come previsto](#messages-from-my-devices-are-not-being-routed-as-expected)
* [Si è improvvisamente interrotto il recupero dei messaggi nell'endpoint predefinito di hub eventi](#i-suddenly-stopped-getting-messages-at-the-built-in-endpoint)

### <a name="messages-from-my-devices-are-not-being-routed-as-expected"></a>I messaggi dei dispositivi non vengono indirizzati come previsto

Per risolvere il problema, analizzare quanto segue.

#### <a name="the-routing-metrics-for-this-endpoint"></a>Metriche di routing per questo endpoint

Tutte le [metriche dell'hub delle cose correlate al routing](monitor-iot-hub-reference.md#routing-metrics) sono precedute dal *routing* . È possibile combinare le informazioni da più metriche per individuare la causa radice dei problemi. Usare, ad esempio, il **recapito del routing** della metrica per identificare il numero di messaggi recapitati a un endpoint o eliminati quando non corrispondono a query su una delle route e la route di fallback è stata disabilitata. Controllare la metrica della **latenza di routing** per verificare se la latenza per il recapito dei messaggi è stabile o in aumento. Una latenza crescente può indicare un problema con un endpoint specifico ed è consigliabile controllare [l'integrità dell'endpoint](#the-health-of-the-endpoint). Queste metriche di routing hanno anche [dimensioni](monitor-iot-hub-reference.md#metric-dimensions) che forniscono dettagli sulla metrica, ad esempio il tipo di endpoint, il nome dell'endpoint specifico e il motivo per cui il messaggio non è stato recapitato.

#### <a name="the-resource-logs-for-any-operational-issues"></a>Log delle risorse per eventuali problemi operativi

Osservare i [log delle risorse di **Route**](monitor-iot-hub-reference.md#routes) per ottenere altre informazioni sulle [operazioni](#operation-names) di routing ed endpoint oppure identificare gli errori e il [codice di errore](#common-error-codes) pertinente per comprendere ulteriormente il problema. Il nome dell'operazione **RouteEvaluationError** nel log, ad esempio, indica che non è stato possibile valutare la route a causa di un problema con il formato del messaggio. Usare i suggerimenti forniti per i [nomi di operazione](#operation-names) specifici per attenuare il problema. Quando un evento viene registrato come errore, il log fornirà anche ulteriori informazioni sul motivo per cui la valutazione non è riuscita. Se, ad esempio, il nome dell'operazione è **EndpointUnhealthy** , il [codice di errore](#common-error-codes) 403004 indica che l'endpoint ha esaurito lo spazio.

#### <a name="the-health-of-the-endpoint"></a>Stato dell'endpoint

Usare l'API REST per [ottenere l'integrità dell'endpoint](/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) per ottenere [lo stato di integrità](iot-hub-devguide-endpoints.md#custom-endpoints) degli endpoint. L'API *Ottieni endpoint Health* fornisce inoltre informazioni sull'ultima volta in cui un messaggio è stato inviato all'endpoint, l'ultimo [errore noto](#last-known-errors-for-iot-hub-routing-endpoints), l'ora dell'ultimo errore noto e l'ultima volta che è stato eseguito un tentativo di invio per questo endpoint. Utilizzare la possibile mitigazione fornita per l' [ultimo errore noto](#last-known-errors-for-iot-hub-routing-endpoints)specifico.

### <a name="i-suddenly-stopped-getting-messages-at-the-built-in-endpoint"></a>Si è improvvisamente interrotto il recupero dei messaggi dall'endpoint predefinito

Per risolvere il problema, analizzare quanto segue.

#### <a name="was-a-new-route-created"></a>È stata creata una nuova route?

Una volta creata una route, i dati vengono interrotti verso l'endpoint incorporato, a meno che non venga creata una route per tale endpoint. Per assicurarsi che i messaggi continuino a passare all'endpoint incorporato se viene aggiunta una nuova route, configurare una route per l'endpoint *degli eventi* . 

#### <a name="was-the-fallback-route-disabled"></a>La route di fallback è stata disabilitata?

La route di fallback Invia tutti i messaggi che non soddisfano le condizioni di query in nessuna delle route esistenti per gli [Hub eventi predefiniti](iot-hub-devguide-messages-read-builtin.md) (messaggi/eventi), che è compatibile con [Hub eventi](../event-hubs/index.yml). Se il routing dei messaggi è attivato, è possibile abilitare la funzionalità di route di fallback. Se non esistono route verso l'endpoint predefinito ed è abilitata una route di fallback, solo i messaggi che non corrispondono ad alcuna condizione di query sulle route verranno inviati all'endpoint predefinito. Inoltre, se vengono eliminate tutte le route esistenti, è necessario abilitare la route di fallback per ricevere tutti i dati sull'endpoint predefinito.

È possibile abilitare o disabilitare la route di fallback nel pannello routing dei messaggi portale di Azure >. È anche possibile usare Azure Resource Manager per [FallbackRouteProperties](/rest/api/iothub/iothubresource/createorupdate#fallbackrouteproperties) per usare un endpoint personalizzato per la route di fallback.

## <a name="last-known-errors-for-iot-hub-routing-endpoints"></a>Ultimi errori noti per gli endpoint di routing dell'hub Internet

<a id="last-known-errors"></a>
[!INCLUDE [iot-hub-include-last-known-errors](../../includes/iot-hub-include-last-known-errors.md)]

## <a name="routes-resource-logs"></a>Instrada i log delle risorse

Di seguito sono riportati i nomi delle operazioni e i codici di errore registrati nei [log delle risorse di route](monitor-iot-hub-reference.md#routes).

<a id="diagnostics-operation-names"></a>
### <a name="operation-names"></a>Nomi delle operazioni

[!INCLUDE [iot-hub-diagnostics-operation-names](../../includes/iot-hub-diagnostics-operation-names.md)]

<a id="diagnostics-error-codes"></a>
### <a name="common-error-codes"></a>Codici di errore comuni

[!INCLUDE [iot-hub-diagnostics-error-codes](../../includes/iot-hub-diagnostics-error-codes.md)]

## <a name="next-steps"></a>Passaggi successivi

Per ricevere assistenza, contattare gli esperti di Azure nei [forum MSDN e Stack Overflow relativi ad Azure](https://azure.microsoft.com/support/forums/). In alternativa, è possibile archiviare un evento imprevisto di supporto tecnico di Azure. Accedere al [sito del supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare **Ottenere supporto** .