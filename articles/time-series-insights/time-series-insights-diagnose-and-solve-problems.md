---
title: Diagnostica e risoluzione dei problemi | Documentazione Microsoft
description: Questa esercitazione illustra come diagnosticare e risolvere i problemi nell&quot;ambiente Time Series Insights
keywords: 
services: time-series-insights
documentationcenter: 
author: venkatgct
manager: almineev
editor: cgronlun
ms.assetid: 
ms.service: time-series-insights
ms.devlang: na
ms.topic: how-to-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/24/2017
ms.author: venkatja
ms.translationtype: Human Translation
ms.sourcegitcommit: fc4172b27b93a49c613eb915252895e845b96892
ms.openlocfilehash: 4b8d5fdab1744b2db658917f91d6dac05db30d2f
ms.contentlocale: it-it
ms.lasthandoff: 05/12/2017

---
# <a name="diagnose-and-solve-problems-in-your-time-series-insights-environment"></a>Diagnosticare e risolvere i problemi nell'ambiente Time Series Insights

## <a name="i-dont-see-my-data"></a>Non vengono visualizzati i dati
Ecco alcuni motivi per cui i dati potrebbero non essere visualizzati nel proprio ambiente nel [portale di Azure Time Series Insights](https://insights.timeseries.azure.com).

### <a name="your-event-source-doesnt-have-data-in-json-format"></a>L'origine evento non dispone di dati in formato JSON
Azure Time Series Insights al momento supporta solo i dati in formato JSON. Per alcuni esempi di dati in formato JSON, vedere [Forme JSON supportate](time-series-insights-send-events.md#supported-json-shapes).

### <a name="when-you-registered-your-event-source-you-didnt-provide-the-key-that-has-the-required-permission"></a>Durante la registrazione dell'origine evento non è stata fornita la chiave con l'autorizzazione necessaria
* Per un hub IoT è necessario fornire la chiave con l'autorizzazione di **connessione al servizio**.

   ![Autorizzazione di connessione al servizio hub IoT](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png)

   Come illustrato nell'immagine precedente, entrambi i criteri **iothubowner** e **service** possono funzionare, in quanto entrambi dispongono dell'autorizzazione di **connessione al servizio**.
* Per un hub eventi è necessario fornire la chiave con l'autorizzazione di **ascolto**.

   ![Autorizzazione di ascolto per l'hub eventi](media/diagnose-and-solve-problems/eventhub-listen-permissions.png)

   Come illustrato nell'immagine precedente, entrambi i criteri **read** e **manage** potrebbero funzionare, in quanto entrambi dispongono dell'autorizzazione di **ascolto**.

### <a name="the-provided-consumer-group-is-not-exclusive-to-time-series-insights"></a>Il gruppo di consumer fornito non è esclusivo di Time Series Insights
Per un hub IoT o un hub eventi, durante la registrazione viene richiesto di specificare il gruppo di consumer da usare per la lettura dei dati. Questo gruppo di consumer non deve essere condiviso. Se viene condiviso, l'hub eventi sottostante disconnette automaticamente uno dei lettori in modo casuale.

## <a name="i-see-my-data-but-theres-a-lag"></a>I dati vengono visualizzati, ma c'è un ritardo
Ecco i motivi per cui potrebbero essere visualizzati dati parziali nel proprio ambiente nel [portale di Time Series Insights](https://insights.timeseries.azure.com).

### <a name="your-environment-is-getting-throttled"></a>L'ambiente è in fase di limitazione
La limitazione viene applicata in base alla capacità e al tipo di SKU dell'ambiente. Tutte le origini evento all'interno dell'ambiente condividono questa capacità. Se l'origine evento dell'hub IoT e dell'hub eventi esegue il push dei dati oltre il limite imposto, si verificheranno una limitazione e un ritardo.

Il diagramma seguente mostra un ambiente Time Series Insights con uno SKU S1 e una capacità pari a 3. È consentito l'ingresso di 3 milioni di eventi al giorno.

![Capacità corrente dello SKU dell'ambiente](media/diagnose-and-solve-problems/environment-sku-current-capacity.png)

Si supponga che questo ambiente stia ricevendo messaggi da un hub eventi con la velocità di ingresso indicata nel diagramma seguente:

![Velocità di ingresso di esempio per un hub eventi](media/diagnose-and-solve-problems/eventhub-ingress-rate.png)

Come illustrato nel diagramma, la frequenza in ingresso giornaliera è pari a circa 67.000 messaggi. Questa velocità si traduce approssimativamente in 46 messaggi al minuto. Se ogni messaggio dell'hub eventi viene appiattito a un singolo evento Time Series Insights, questo ambiente non sperimenterà alcuna limitazione. Se ogni messaggio dell'hub eventi viene appiattito a 100 eventi Time Series Insights , devono essere inseriti 4.600 eventi ogni minuto. Un ambiente SKU S1 con una capacità pari a 3 consente l'ingresso di soli 2.100 eventi al minuto (1 milione di eventi al giorno = 700 eventi al minuto in 3 unità = 2.100 eventi al minuto). Pertanto si osserverà un ritardo dovuto alla limitazione. 

Per una descrizione generale di come funziona la logica di appiattimento, vedere [Forme JSON supportate](time-series-insights-send-events.md#supported-json-shapes).

#### <a name="recommended-steps"></a>Procedure consigliate
Per correggere il ritardo, aumentare la capacità SKU dell'ambiente. Per altre informazioni, vedere [Come scalare l'ambiente Time Series Insights](time-series-insights-how-to-scale-your-environment.md).

### <a name="youre-pushing-historical-data-and-causing-slow-ingress"></a>Il push dei dati cronologici sta causando lentezza nell'ingresso
Se ci si connette a un'origine evento esistente, è probabile che l'hub IoT o l'hub eventi contenga già dati. Di conseguenza, l'ambiente inizia a eseguire il pull dei dati dall'inizio del periodo di conservazione dei messaggi dell'origine eventi. 

Questo comportamento è quello predefinito e non è possibile eseguirne l'override. Potrebbe attivarsi la limitazione e potrebbe essere necessario un po' di tempo per l'inserimento dei dati cronologici.

#### <a name="recommended-steps"></a>Procedure consigliate
Per correggere il ritardo, procedere come segue:
1. Aumentare la capacità SKU fino al valore massimo consentito (10 in questo caso). Dopo aver aumentato la capacità, il processo di ingresso inizia a essere molto più veloce. È possibile vedere tale rapidità nel grafico della disponibilità nel [portale di Time Series Insights](https://insights.timeseries.azure.com). La maggiore capacità sarà addebitata.
2. Dopo aver recuperato il ritardo, diminuire nuovamente la capacità SKU secondo la velocità di ingresso normale.

## <a name="my-event-sources-timestamp-property-name-setting-doesnt-work"></a>L'impostazione del *nome della proprietà Timestamp* dell'origine evento non funziona
Verificare che il nome e il valore siano conformi alle regole seguenti:
* Il nome della proprietà Timestamp _fa distinzione fra maiuscole e minuscole_.
* Il valore della proprietà Timestamp proveniente dall'origine evento, come stringa JSON, deve avere il formato _aaaa-MM-ggTHH:mm:ss.FFFFFFFK_. Un esempio di tale stringa è "2008-04-12T12:53Z".

