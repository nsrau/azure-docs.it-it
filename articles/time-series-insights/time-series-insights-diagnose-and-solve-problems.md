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
ms.sourcegitcommit: 7f8b63c22a3f5a6916264acd22a80649ac7cd12f
ms.openlocfilehash: 46d58e94f3fede3f7f19de8d97e23ac6910bc12a
ms.contentlocale: it-it
ms.lasthandoff: 05/01/2017

---
# <a name="diagnose-and-solve-problems"></a>Diagnostica e risoluzione dei problemi

## <a name="i-do-not-see-my-data"></a>Non vengono visualizzati i dati
Ecco alcuni motivi per cui i dati potrebbero non essere visualizzati nel proprio ambiente nel [portale di Time Series Insights](https://insights.timeseries.azure.com).

### <a name="does-your-event-source-have-data-in-json-format"></a>L'origine evento ha dati in formato JSON?
Azure Time Series Insights al momento supporta solo i dati in formato JSON. Per alcuni esempi di dati in formato JSON, vedere la sezione *Forme JSON supportate* [qui](time-series-insights-send-events.md#supported-json-shapes).

### <a name="when-registering-your-event-source-did-you-provide-the-key-with-required-permissions"></a>Durante la registrazione dell'origine evento, è stata fornita la chiave con le autorizzazioni necessarie?
1. Per IoTHub è necessario fornire la chiave con l'autorizzazione di *connessione al servizio*.

   ![Autorizzazione di connessione al servizio IotHub](media/diagnose-and-solve-problems/iothub-serviceconnect-permissions.png)

   Come illustrato nell'immagine precedente, entrambi i criteri "iothubowner" e "service" funzionerebbero, in quanto entrambi hanno l'autorizzazione di "connessione al servizio".
2. Per EventHub è necessario fornire la chiave con l'autorizzazione di *ascolto*.

   ![Autorizzazione di ascolto per l'hub eventi](media/diagnose-and-solve-problems/eventhub-listen-permissions.png)

   Come illustrato nell'immagine precedente, entrambi i criteri "iothubowner" e "service" funzionerebbero, in quanto entrambi hanno l'autorizzazione di "lettura"

### <a name="are-you-sure-that-the-consumer-group-provided-is-exclusive-to-time-series-insights"></a>Il gruppo di consumer fornito è esclusivo di Time Series Insights?
Per IoTHub o EventHub, durante la registrazione viene richiesto di specificare il gruppo di consumer che deve essere usato durante la lettura dei dati. Questo gruppo di consumer non deve essere condiviso. Se viene condiviso, l'hub eventi sottostante disconnette automaticamente uno dei lettori in modo casuale.

## <a name="i-see-my-data-but-there-is-a-lag"></a>I dati sono visualizzati, ma c'è un ritardo
Ecco alcuni motivi per cui si potrebbero vedere dati parziali nel proprio ambiente nel [portale di Time Series Insights](https://insights.timeseries.azure.com).

### <a name="your-environment-might-be-getting-throttled"></a>L'ambiente potrebbe essere in fase di limitazione
Le limitazioni vengono applicate in base alle capacità e al tipo di ambiente SKU. Tutte le origini evento all'interno dell'ambiente condividono questa capacità. Se l'origine eventi dell'hub eventi o dell'hub IoT esegue il push dei dati oltre il limite imposto, si osserveranno la limitazione e il ritardo.

Il diagramma seguente mostra un ambiente Time Series Insights con lo SKU S1 e la capacità 3. È consentito l'ingresso di 3 milioni di eventi al giorno.

![Capacità corrente dello SKU dell'ambiente](media/diagnose-and-solve-problems/environment-sku-current-capacity.png)

Si supponga che questo ambiente stesse inserendo messaggi da un hub eventi con la frequenza in ingresso illustrata nel diagramma seguente:

![Capacità corrente dello SKU dell'ambiente](media/diagnose-and-solve-problems/eventhub-ingress-rate.png)

Come illustrato nel diagramma, la frequenza in ingresso giornaliera è pari a circa 67.000 messaggi. Questa velocità si traduce approssimativamente in 46 messaggi al minuto. Se ogni messaggio dell'hub eventi viene appiattito a un singolo evento Time Series Insights, questo ambiente non sperimenterà alcuna limitazione. Se ogni messaggio dell'hub eventi viene appiattito a 100 eventi Time Series Insights , devono essere inseriti 4.600 eventi ogni minuto. Un ambiente SKU S1 con una capacità pari a tre consente l'inserimento di solo 2.100 eventi ogni minuto. (1 milione di eventi al giorno = > 700 eventi al minuto, 3 unità = > 2100 eventi al minuto). Pertanto si osserverà un ritardo dovuto alla limitazione. Per una descrizione generale di come funziona la logica di appiattimento, vedere la sezione *Forme JSON supportate* [qui](time-series-insights-send-events.md#supported-json-shapes).

#### <a name="recommended-steps"></a>Procedure consigliate
Per risolvere il ritardo aumentare la capacità SKU dell'ambiente. [Come ridimensionare l'ambiente Time Series Insights](time-series-insights-how-to-scale-your-environment.md)

### <a name="you-might-be-pushing-historical-data-and-hence-the-slow-ingress"></a>Se si esegue il push dei dati cronologici l'ingresso potrebbe essere lento
Se ci si connette a un'origine evento esistente, è probabile che l'hub eventi o l'hub IoT contenga già dati. Perciò l'ambiente inizia a eseguire il pull dei dati dall'inizio del periodo di conservazione dei messaggi dell'origine eventi. Questo comportamento è quello predefinito e non è possibile eseguirne l'override. La limitazione potrebbe essere attivata e potrebbe essere necessario un po' di tempo per l'inserimento dei dati cronologici.

#### <a name="recommended-steps"></a>Procedure consigliate
Per correggere il ritardo, seguire questa procedura:
1. Aumentare la capacità SKU al valore massimo consentito (10 in questo caso). Dopo aver aumentato la capacità, la procedura di ingresso inizia a essere molto più veloce. È possibile vedere tale rapidità nel grafico di disponibilità nel [portale di Time Series Insights](https://insights.timeseries.azure.com). La maggiore capacità sarà addebitata.
2. Una volta recuperato il ritardo, diminuire nuovamente la capacità SKU al tasso di ingresso normale.

## <a name="my-event-source-timestamp-property-name-setting-does-not-work"></a>L'impostazione *nome della proprietà Timestamp* dell'origine evento non funziona
Verificare che il nome e il valore siano conformi alle regole seguenti:
1. Il nome della proprietà Timestamp __fa distinzione fra maiuscole e minuscole__.
2. Il valore della proprietà Timestamp che proveniente dall'origine evento, come stringa JSON, deve avere il formato __aaaa-MM-ggTHH:mm:ss.FFFFFFFK__. Un esempio di tale stringa è "2008-04-12T12:53Z".
