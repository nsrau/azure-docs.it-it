---
title: Diagnosticare e risolvere i problemi in Azure Time Series Insights | Microsoft Docs
description: Questo articolo descrive come diagnosticare e risolvere i problemi più comuni riscontrati nell'ambiente Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 04/09/2018
ms.custom: seodec18
ms.openlocfilehash: 36ea2b8d3649fbda5a5cd6cc5f2cd05cdc095902
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/18/2018
ms.locfileid: "53555813"
---
# <a name="diagnose-and-solve-issues-in-your-time-series-insights-environment"></a>Diagnosticare e risolvere i problemi nell'ambiente Time Series Insights

Questo articolo descrive alcuni problemi che si possono riscontrare nell'ambiente Azure Time Series Insights. L'articolo descrive le possibili cause e le relative soluzioni.

## <a name="video"></a>Video: 

Questo video illustra le problematiche e le correzioni più comuni affrontate dai clienti di Time Series Insights.</br>

> [!VIDEO https://www.youtube.com/embed/7U0SwxAVSKw]

## <a name="problem-1-no-data-is-shown"></a>Problema 1: non vengono visualizzati dati

Ecco alcuni motivi per cui i dati potrebbero non essere visualizzati nello [strumento di esplorazione di Azure Time Series Insights](https://insights.timeseries.azure.com):

### <a name="cause-a-event-source-data-isnt-in-json-format"></a>Causa A: i dati dell'origine evento non sono in formato JSON

Azure Time Series Insights supporta solo dati in formato JSON. Per alcuni esempi di dati in formato JSON, vedere [Forme JSON supportate](./how-to-shape-query-json.md).

### <a name="cause-b-the-event-source-key-is-missing-a-required-permission"></a>Causa B: alla chiave dell'origine manca un'autorizzazione obbligatoria

* Per un hub IoT in Hub IoT di Azure è necessario indicare la chiave con le autorizzazioni di **connessione al servizio**. Entrambi i criteri **iothubowner** o **service** funzioneranno perché entrambi dispongono delle autorizzazioni di **connessione al servizio**.

   ![Autorizzazioni di connessione al servizio Hub IoT](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png)


* Per un hub eventi in Hub eventi di Azure è necessario indicare la chiave che dispone delle autorizzazioni di **ascolto**. Entrambi i criteri **read** o **manage** funzioneranno in quanto entrambi dispongono delle autorizzazioni di **ascolto**.

   ![Autorizzazioni di ascolto dell'hub eventi](media/diagnose-and-solve-problems/eventhub-listen-permissions.png)

### <a name="cause-c-the-consumer-group-provided-isnt-exclusive-to-time-series-insights"></a>Causa C: il gruppo di consumer specificato non è esclusivo di Time Series Insights

Quando si registra un hub IoT o un hub eventi, è importante impostare il gruppo di consumer che si desidera usare per leggere i dati. Questo gruppo *non può essere condiviso*. Se viene condiviso, l'hub IoT o l'hub eventi sottostante disconnette automaticamente in modo casuale uno dei lettori. Specificare un gruppo di consumer univoco per la lettura dei dati da parte di Time Series Insights.

## <a name="problem-2-some-data-is-shown-but-data-is-missing"></a>Problema 2: vengono visualizzati solo alcuni dati

Quando i dati vengono visualizzati solo parzialmente e sembrano essere in ritardo, è necessario considerare diverse possibilità.

### <a name="cause-a-your-environment-is-being-throttled"></a>Causa A: l'ambiente è limitato nelle richieste

La limitazione delle richieste è un problema comune quando gli ambienti vengono sottoposti a provisioning dopo che è stata creata un'origine evento che contiene dati. Hub IoT di Azure e Hub eventi di Azure archiviano i dati per un massimo di sette giorni. Time Series Insights inizia sempre con l'evento meno recente nell'origine evento (First-In, First-Out o *FIFO*). 

Se ad esempio nell'origine evento sono presenti 5 milioni di eventi quando ci si connette a un S1, un ambiente Time Series Insights a unità singola, Time Series Insights legge circa 1 milione di eventi al giorno. Potrebbe sembrare che Time Series Insights riscontri cinque giorni di latenza. Quello che accade in effetti è che l'ambiente viene limitato nelle richieste. 

In presenza di eventi meno recenti nell'origine eventi, è possibile scegliere tra due approcci:

- Modificare i limiti di conservazione dell'origine eventi per eliminare gli eventi meno recenti che non si vuole visualizzare in Time Series Insights.
- Eseguire il provisioning di un ambiente di dimensioni maggiori, in termini di numero di unità, per aumentare la velocità effettiva per gli eventi meno recenti. Ritornando all'esempio precedente, se lo stesso ambiente S1 venisse esteso a cinque unità per un giorno, sarebbe possibile recuperare entro tale giorno. Se la produzione di eventi di stato costante è di circa 1 milione o meno di eventi al giorno, è possibile ridurre la capacità dell'evento tornando a una unità dopo essere tornati in pari.

La limitazione viene applicata in base alla capacità e al tipo di SKU dell'ambiente. Tutte le origini evento all'interno dell'ambiente condividono questa capacità. Se l'origine evento dell'hub IoT e dell'hub eventi esegue il push dei dati oltre il limite imposto, si verificheranno una limitazione nelle richieste e un ritardo.

Il diagramma seguente mostra un ambiente Time Series Insights con uno SKU S1 e una capacità pari a 3. È consentito l'ingresso di 3 milioni di eventi al giorno.

![Capacità corrente dello SKU dell'ambiente](media/diagnose-and-solve-problems/environment-sku-current-capacity.png)

Si supponga, ad esempio, che questo ambiente stia inserendo messaggi provenienti da un hub eventi. La figura seguente mostra la velocità di ingresso:

![Velocità di ingresso di esempio per un hub eventi](media/diagnose-and-solve-problems/eventhub-ingress-rate.png)

La velocità di ingresso giornaliera è pari a circa 67.000 messaggi. Questa velocità si traduce approssimativamente in 46 messaggi al minuto. Se ogni messaggio dell'hub eventi viene appiattito a un singolo evento Time Series Insights, non si verifica alcuna limitazione. Se ogni messaggio dell'hub eventi viene appiattito a 100 eventi Time Series Insights , vengono inseriti 4.600 eventi ogni minuto. Un ambiente SKU S1 con una capacità pari a 3 consente l'ingresso di soli 2.100 eventi al minuto (1 milione di eventi al giorno = 700 eventi al minuto in tre unità = 2.100 eventi al minuto). Si osserverà pertanto un ritardo dovuto alla limitazione delle richieste. 

Per una descrizione generale di come funziona la logica di appiattimento, vedere [Forme JSON supportate](./how-to-shape-query-json.md).

#### <a name="recommended-resolutions-for-excessive-throttling"></a>Risoluzioni consigliate per la limitazione eccessiva

Per correggere il ritardo, aumentare la capacità SKU dell'ambiente. Per altre informazioni, vedere [Scale your Time Series Insights environment](time-series-insights-how-to-scale-your-environment.md) (Scalare l'ambiente Time Series Insights).

### <a name="cause-b-initial-ingestion-of-historical-data-slows-ingress"></a>Causa B: l'inserimento iniziale dei dati cronologici rallenta in ingresso

Se ci si connette a un'origine evento esistente, è probabile che l'hub IoT o l'hub eventi contenga già dati. L'ambiente inizia a eseguire il pull dei dati dall'inizio del periodo di conservazione dei messaggi dell'origine evento. Questa è l'elaborazione predefinita e non può essere sottoposta a override. È possibile coinvolgere la limitazione delle richieste. La limitazione delle richieste può richiedere tempo per recuperare mentre si inseriscono i dati cronologici.

#### <a name="recommended-resolutions-for-large-initial-ingestion"></a>Risoluzioni consigliate per un inserimento iniziale di grandi quantità di dati

Per correggere il ritardo:

1. Aumentare la capacità SKU fino al valore massimo consentito (10 in questo caso). Dopo aver aumentato la capacità, il processo di inserimento inizia a velocizzarsi. La maggiore capacità sarà addebitata. Per vedere tale rapidità, è possibile visualizzare il grafico della disponibilità nello [strumento di esplorazione di Time Series Insights](https://insights.timeseries.azure.com). 

2. Dopo avere recuperato il ritardo, diminuire nuovamente la capacità SKU alla velocità di ingresso normale.

## <a name="problem-3-my-event-sources-timestamp-property-name-setting-doesnt-work"></a>Problema 3: l'impostazione del nome della proprietà Timestamp dell'origine evento non funziona

Verificare che il nome e il valore della proprietà timestamp siano conformi alle regole seguenti:
* Il nome della proprietà timestamp fa distinzione fra maiuscole e minuscole.
* Il valore della proprietà timestamp, che proveniente dall'origine evento come stringa JSON, deve avere il formato _aaaa-MM-ggTHH:mm:ss.FFFFFFFK_. Un esempio è **2008-04-12T12:53Z**.

Il modo più semplice per garantire l'acquisizione e il corretto funzionamento del nome della proprietà timestamp consiste nell'usare lo strumento di esplorazione di Azure Time Series Insights. All'interno dello strumento di esplorazione di Azure Time Series Insights, usando il grafico, selezionare un periodo di tempo dopo aver specificato il nome della proprietà timestamp. Fare clic con il pulsante destro del mouse sulla selezione e selezionare l'opzione **Esplora eventi**. 

L'intestazione della prima colonna deve essere il nome della proprietà timestamp. Accanto alla parola **Timestamp** è visualizzato **($ts)**. 

Non dovrebbero essere presenti i valori seguenti:
- *(abc)*: indica che Time Series Insights sta leggendo i valori dei dati come stringhe.
- *Icona calendario*: indica che Time Series Insights sta leggendo i valori dei dati come *datetime*.
- *#*: indica che Time Series Insights sta leggendo i valori dei dati come valori Integer.


## <a name="next-steps"></a>Passaggi successivi

- Per assistenza, avviare una conversazione nel [forum MSDN](https://social.msdn.microsoft.com/Forums/home?forum=AzureTimeSeriesInsights) o in [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-timeseries-insights). 
- Per le opzioni di supporto assistito, usare il [Supporto tecnico di Azure](https://azure.microsoft.com/support/options/).
