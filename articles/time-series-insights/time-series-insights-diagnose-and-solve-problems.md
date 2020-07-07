---
title: Diagnostica, risoluzione dei problemi e risoluzione dei problemi-Azure Time Series Insights
description: Questo articolo descrive come diagnosticare, risolvere i problemi e risolvere i problemi comuni nell'ambiente Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 02/04/2020
ms.custom: seodec18
ms.openlocfilehash: 4d9efa1ebf1a3e3b146c4f45b0e84047562141cd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82192715"
---
# <a name="diagnose-and-solve-issues-in-your-time-series-insights-environment"></a>Diagnosticare e risolvere i problemi nell'ambiente Time Series Insights

Questo articolo descrive i problemi che possono verificarsi nell'ambiente Azure Time Series Insights. L'articolo descrive le possibili cause e le relative soluzioni.

## <a name="video"></a>Video

### <a name="learn-about-common-time-series-insights-challenges-and-mitigationsbr"></a>Informazioni sulle problemi comuni di Time Series Insights e sulle mitigazioni</br>

> [!VIDEO https://www.youtube.com/embed/7U0SwxAVSKw]

## <a name="problem-no-data-is-shown"></a>Problema: non viene visualizzato alcun dato

Se non viene visualizzato alcun dato in [esplora Azure Time Series Insights](https://insights.timeseries.azure.com), prendere in considerazione le cause più comuni.

### <a name="cause-a-event-source-data-isnt-in-json-format"></a>Causa A: i dati dell'origine evento non sono in formato JSON

Azure Time Series Insights supporta solo dati in formato JSON. Per alcuni esempi di dati in formato JSON, vedere [Forme JSON supportate](./how-to-shape-query-json.md).

### <a name="cause-b-the-event-source-key-is-missing-a-required-permission"></a>Causa B: manca un'autorizzazione necessaria per la chiave dell'origine evento

* Per un hub Internet delle cose nell'hub Azure, è necessario fornire la chiave con autorizzazioni di connessione al servizio. Selezionare il **iothubowner** o il criterio del **servizio** . Entrambi hanno autorizzazioni di connessione al servizio.

   [![Autorizzazioni di connessione al servizio Hub IoT](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png)](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png#lightbox)

* Per un hub eventi in hub eventi di Azure, è necessario specificare la chiave che dispone delle autorizzazioni di ascolto. I criteri di **lettura** e **gestione** funzioneranno entrambi perché hanno autorizzazioni di ascolto.

   [![Autorizzazioni di ascolto dell'hub eventi](media/diagnose-and-solve-problems/eventhub-listen-permissions.png)](media/diagnose-and-solve-problems/eventhub-listen-permissions.png#lightbox)

### <a name="cause-c-the-provided-consumer-group-isnt-exclusive-to-time-series-insights"></a>Cause C: il gruppo di consumer specificato non è esclusivo per Time Series Insights

Quando si registra un hub IoT o un hub eventi, è importante impostare il gruppo di consumer che si desidera usare per leggere i dati. Questo gruppo *non può essere condiviso*. Se viene condiviso, l'hub IoT o l'hub eventi sottostante disconnette automaticamente in modo casuale uno dei lettori. Specificare un gruppo di consumer univoco per la lettura dei dati da parte di Time Series Insights.

### <a name="cause-d-the-environment-has-just-been-provisioned"></a>Motivo D: è stato appena eseguito il provisioning dell'ambiente

I dati verranno visualizzati in Esplora Time Series Insights entro pochi minuti dall'ambiente e i relativi dati verranno creati per la prima volta.

## <a name="problem-some-data-is-shown-but-data-is-missing"></a>Problema: alcuni dati vengono visualizzati, ma mancano i dati

Quando i dati vengono visualizzati solo parzialmente e i dati sembrano essere in ritardo, considerare questi possibili problemi.

### <a name="cause-a-your-environment-is-being-throttled"></a>Cause A: l'ambiente viene limitato

La [limitazione](time-series-insights-environment-mitigate-latency.md) è un problema comune quando viene eseguito il provisioning degli ambienti dopo la creazione di un'origine evento con dati. Hub IoT di Azure e Hub eventi di Azure archiviano i dati per un massimo di sette giorni. Time Series Insights inizia sempre con l'evento meno recente nell'origine evento (First-in, First-out o *FIFO*).

Se ad esempio nell'origine evento sono presenti 5 milioni di eventi quando ci si connette a un S1, un ambiente Time Series Insights a unità singola, Time Series Insights legge circa 1 milione di eventi al giorno. Potrebbe sembrare che Time Series Insights riscontri cinque giorni di latenza. Ma ciò che accade è che l'ambiente viene limitato.

In presenza di eventi meno recenti nell'origine eventi, è possibile scegliere tra due approcci:

- Modificare i limiti di conservazione dell'origine eventi per eliminare gli eventi meno recenti che non si vuole visualizzare in Time Series Insights.
- Eseguire il provisioning di un ambiente di dimensioni maggiori, in termini di numero di unità, per aumentare la velocità effettiva per gli eventi meno recenti. Nell'esempio precedente, se si aumenta lo stesso ambiente S1 a cinque unità per un giorno, l'ambiente dovrebbe rientrare in un giorno. Se la produzione di eventi a stato stazionario è pari a 1 milione o un numero inferiore di eventi al giorno, è possibile ridurre la capacità di un evento a un'unità dopo che Time Series Insights è stata interrotta.

Il limite di limitazione applicato è basato sul tipo di SKU e sulla capacità dell'ambiente. Tutte le origini evento all'interno dell'ambiente condividono questa capacità. Se l'origine evento per l'hub Internet o l'hub eventi esegue il push dei dati oltre i limiti applicati, si verificheranno una limitazione e un ritardo.

Il diagramma seguente mostra un ambiente Time Series Insights con uno SKU S1 e una capacità pari a 3. È consentito l'ingresso di 3 milioni di eventi al giorno.

[![Capacità ambiente](media/diagnose-and-solve-problems/environment-sku-current-capacity.png)](media/diagnose-and-solve-problems/environment-sku-current-capacity.png#lightbox)

Immaginate un ambiente che inserisce messaggi da un hub eventi. Ha una velocità in ingresso giornaliera di circa 67.000 messaggi. Questa velocità si traduce approssimativamente in 46 messaggi al minuto.

* Se ogni messaggio dell'hub eventi viene appiattito a un singolo evento Time Series Insights, non si verifica alcuna limitazione.
* Se ogni messaggio dell'hub eventi viene appiattito a 100 eventi Time Series Insights , vengono inseriti 4.600 eventi ogni minuto.

Un ambiente SKU S1 con una capacità pari a 3 consente l'ingresso di soli 2.100 eventi al minuto (1 milione di eventi al giorno = 700 eventi al minuto in tre unità = 2.100 eventi al minuto).

Per informazioni di alto livello sul funzionamento della logica di Flat, vedere [forme JSON supportate](./how-to-shape-query-json.md).

#### <a name="recommended-resolutions-for-excessive-throttling"></a>Risoluzioni consigliate per la limitazione eccessiva

Per correggere il ritardo, aumentare la capacità SKU dell'ambiente. Per altre informazioni, vedere [ridimensionare l'ambiente Time Series Insights](time-series-insights-how-to-scale-your-environment.md).

### <a name="cause-b-initial-ingestion-of-historical-data-slows-ingress"></a>Motivo B: l'inserimento iniziale dei dati cronologici rallenta il traffico in ingresso

Se ci si connette a un'origine evento esistente, è probabile che l'hub IoT o l'hub eventi contenga già dati. L'ambiente inizia a eseguire il pull dei dati dall'inizio del periodo di conservazione dei messaggi dell'origine evento. Questa elaborazione predefinita non può essere sottoposta a override. È possibile coinvolgere la limitazione delle richieste. La limitazione delle richieste può richiedere tempo per recuperare mentre si inseriscono i dati cronologici.

#### <a name="recommended-resolutions-for-large-initial-ingestion"></a>Risoluzioni consigliate per un inserimento iniziale di grandi quantità di dati

Per correggere il ritardo:

1. Aumentare la capacità SKU fino al valore massimo consentito (10 in questo caso). Dopo aver aumentato la capacità, il processo di inserimento inizia a velocizzarsi. Viene addebitata la capacità aumentata. Per vedere tale rapidità, è possibile visualizzare il grafico della disponibilità nello [strumento di esplorazione di Time Series Insights](https://insights.timeseries.azure.com).

2. Dopo avere recuperato il ritardo, diminuire nuovamente la capacità SKU alla velocità di ingresso normale.

## <a name="problem-data-was-showing-previously-but-is-no-longer-showing"></a>Problema: i dati sono stati visualizzati in precedenza ma non vengono più visualizzati

Se Time Series Insights non sta più inserendo dati, ma gli eventi continuano a essere trasmessi nell'hub o nell'hub eventi, prendere in considerazione questa possibile ragione.

### <a name="cause-a-your-hub-access-key-was-regenerated-and-your-environment-needs-to-be-updated"></a>Cause A: la chiave di accesso dell'hub è stata rigenerata ed è necessario aggiornare l'ambiente

Questo problema si verifica quando la chiave specificata al momento della creazione dell'origine evento non è più valida. I dati di telemetria vengono visualizzati nell'hub, ma nessun messaggio in ingresso ha ricevuto messaggi in Time Series Insights. Se non si è certi che la chiave sia stata rigenerata, è possibile cercare "crea o Aggiorna regole di autorizzazione dello spazio dei nomi" nel log attività dell'hub eventi. Per un hub Internet delle cose, cercare "crea o aggiorna risorsa IotHub".

Per aggiornare l'ambiente di Time Series Insights con la nuova chiave, aprire la risorsa Hub nel portale di Azure e copiare la nuova chiave. Passare alla risorsa Time Series Insights e selezionare **origini eventi**:

   [![Seleziona origini eventi](media/diagnose-and-solve-problems/update-hub-key-step-1.png)](media/diagnose-and-solve-problems/update-hub-key-step-1.png#lightbox)

Selezionare l'origine o le origini eventi da cui si è arrestato l'inserimento, incollare la nuova chiave e quindi selezionare **Salva**:

   [![Incollare la nuova chiave](media/diagnose-and-solve-problems/update-hub-key-step-2.png)](media/diagnose-and-solve-problems/update-hub-key-step-2.png#lightbox)

## <a name="problem-the-event-sources-timestamp-property-name-setting-doesnt-work"></a>Problema: l'impostazione del nome della proprietà timestamp dell'origine evento non funziona

Verificare che il valore della proprietà timestamp che deriva dall'origine evento come stringa JSON sia nel formato _aaaa-mm-ggThh: mm: SS. FFFFFFFK_. Ecco un esempio: **2008-04-12T12:53Z**.

Tenere presente che il nome della proprietà timestamp fa distinzione tra maiuscole e minuscole.

Il modo più semplice per garantire l'acquisizione e il corretto funzionamento del nome della proprietà timestamp consiste nell'usare lo strumento di esplorazione di Azure Time Series Insights. All'interno dello strumento di esplorazione di Azure Time Series Insights, usando il grafico, selezionare un periodo di tempo dopo aver specificato il nome della proprietà timestamp. Fare clic con il pulsante destro del mouse sulla selezione, quindi scegliere **Esplora eventi**.

L'intestazione della prima colonna deve essere il nome della proprietà timestamp. Accanto al **timestamp**di Word verrà visualizzato **($TS)** .

Non verranno visualizzati i valori seguenti:

- *(ABC)*: indica che Time Series Insights sta leggendo i valori dei dati come stringhe.
- *Calendar Icon*: indica che Time Series Insights sta leggendo i valori dei dati come valori DateTime.
- *#*: Indica che Time Series Insights sta leggendo i valori dei dati come numeri interi.

## <a name="next-steps"></a>Passaggi successivi

- Leggere le informazioni su [come ridurre la latenza in Azure Time Series Insights](time-series-insights-environment-mitigate-latency.md).

- Informazioni [su come ridimensionare l'ambiente di Time Series Insights](time-series-insights-how-to-scale-your-environment.md).
