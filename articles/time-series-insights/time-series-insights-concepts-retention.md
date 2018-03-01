---
title: Comprendere la conservazione dati nell'ambiente Azure Time Series Insights | Microsoft Docs
description: Questo articolo descrive due impostazioni che consentono di controllare la conservazione dati nell'ambiente Azure Time Series Insights.
services: time-series-insights
ms.service: time-series-insights
author: anshan
ms.author: anshan
manager: kfile
editor: MicrosoftDocs/tsidocs
ms.reviewer: jasonh, kfile, anshan
ms.workload: big-data
ms.topic: article
ms.date: 02/09/2018
ms.openlocfilehash: 46e0c4fa25c7d8a56763b80bf7de97c775c7ee99
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2018
---
# <a name="understand-data-retention-in-time-series-insights"></a>Comprendere la conservazione dati in Time Series Insights
Questo articolo descrive due impostazioni che influenzano la conservazione dati nell'ambiente Time Series Insights.

Ogni ambiente Time Series Insights dispone di un'impostazione che controlla il **periodo di conservazione dei dati**. Il valore va da 1 a 400 giorni. I dati vengono eliminati in base alla capacità di archiviazione dell'ambiente o alla durata dell'assorbimento, che va da 1 a 400 giorni, a seconda della condizione che si verifica per prima.

Ogni ambiente Time Series Insights dispone di un'impostazione aggiuntiva **Comportamento limite di archiviazione superato**. Questa impostazione controlla il comportamento in ingresso e di pulizia quando viene raggiunta la capacità massima di un ambiente. È possibile scegliere tra due tipi di comportamento:
- **Eliminare i dati meno recenti** (impostazione predefinita)  
- **Sospendere il traffico in ingresso**

> [!NOTE]
> Per impostazione predefinita, quando si crea un nuovo ambiente l'assorbimento è configurato per **eliminare i dati meno recenti**. Questa impostazione può essere attivata o disattivata in base alle esigenze in seguito a un periodo di creazione tramite il portale di Azure, nella pagina **Configura** dell'ambiente Time Series Insights.

Per informazioni su come modificare i comportamenti di assorbimento, vedere [Configuring retention in Time Series Insights](time-series-insights-how-to-configure-retention.md) (Configurazione dell'assorbimento in Time Series Insights).

Confrontare il comportamento di conservazione dati:

## <a name="purge-old-data"></a>Eliminare i dati meno recenti
- Questo è il comportamento predefinito per gli ambienti Time Series Insights e mostra lo stesso comportamento riscontrato negli ambienti Time Series Insights dal momento in cui sono stati avviati in anteprima pubblica.  
- Questo comportamento viene preferito quando gli utenti vogliono visualizzare sempre i *dati più recenti* nel proprio ambiente Time Series Insights. 
- Consente di *eliminare* i dati quando si raggiungono i limiti dell'ambiente (periodo di conservazione, dimensioni o numero di elementi, a seconda del limite che viene raggiunto prima). L'assorbimento è impostato a 30 giorni per impostazione predefinita. 
- I dati acquisiti meno recenti vengono eliminati prima (approccio FIFO).

### <a name="example-1"></a>Esempio 1:
Si consideri un ambiente di esempio con il comportamento di assorbimento **Continuare il traffico in ingresso ed eliminare i dati meno recenti**: in questo esempio il **periodo di conservazione dei dati** è impostato a 400 giorni. La **capacità** è impostata sull'unità S1, che contiene 30 GB di capacità totale.   Si supponga che in media ogni giorno si accumulino 500 MB di dati in ingresso. Questo ambiente può mantenere i dati solo per 60 giorni in base alla velocità dei dati in ingresso, poiché la capacità massima viene raggiunta a 60 giorni. I dati in ingresso si accumulano in questo modo: 500 MB al giorno x 60 giorni = 30 GB. 

In questo esempio, il giorno 61 l'ambiente mostra i dati più recenti, ma elimina quelli meno recenti, ovvero i dati che hanno più di 60 giorni. L'eliminazione consente di creare spazio per i nuovi dati in ingresso, in modo che questi possano continuare a essere esaminati. 

Se l'utente desidera conservare i dati più a lungo, può aumentare le dimensioni dell'ambiente aggiungendo unità supplementari o può inserire una minore quantità di dati.  

### <a name="example-2"></a>Esempio 2
Si consideri un ambiente in cui è stato configurato ancora il comportamento di assorbimento **Continuare il traffico in ingresso ed eliminare i dati meno recenti**. In questo esempio il **periodo di conservazione dei dati** è impostato su un valore inferiore, ovvero 180 giorni. La **capacità** è impostata sull'unità S1, che contiene 30 GB di capacità totale. Per memorizzare i dati per l'intero periodo di 180 giorni, il traffico in ingresso giornaliero non può superare 0,166 GB, ovvero 166 MB, al giorno.  

Se la velocità in ingresso giornaliera di questo ambiente supera 0,166 GB al giorno, i dati non possono essere archiviati per 180 giorni poiché alcuni di questi vengono eliminati. Si consideri questo stesso ambiente in un intervallo di tempo in cui la velocità in ingresso aumenta. Si supponga che la velocità in ingresso dell'ambiente possa raggiungere in media 0,189 GB al giorno. In tale intervallo di tempo, i dati vengono conservati per circa 158 giorni (30GB/0,189 = 158,73 giorni di conservazione). Questo periodo di tempo è inferiore rispetto all'intervallo di tempo di conservazione dei dati desiderato.

## <a name="pause-ingress"></a>Sospendere i dati in ingresso
- Questo comportamento è pensato per garantire che i dati non vengano eliminati se si raggiungono i limiti di numero e dimensioni prima del periodo di conservazione.  
- Consente all'utente di avere altro tempo per aumentare la capacità dell'ambiente prima che i dati vengano eliminati perché viene superato il periodo di conservazione
- Questo comportamento consente di impedire la perdita di dati, ma potrebbe comportare un'eventuale perdita dei dati più recenti se il traffico in ingresso viene sospeso oltre il periodo di conservazione dell'origine evento.
- Tuttavia quando si raggiunge la capacità massima di un ambiente, questo sospende il traffico di dati in ingresso fino a quando l'utente non interviene: 
   - L'utente aumenta la capacità massima dell'ambiente. Per altre informazioni, vedere [Come scalare l'ambiente Time Series Insights](time-series-insights-how-to-scale-your-environment.md) per aggiungere altre unità di scala.
   - Viene raggiunto il periodo di conservazione dei dati e i dati vengono eliminati, riportando l'ambiente a livelli al di sotto della capacità massima.

### <a name="example-3"></a>Esempio 3:
Si consideri un ambiente con un comportamento di assorbimento configurato per **sospendere il traffico in ingresso**. In questo esempio il **periodo di conservazione dei dati** è configurato per 60 giorni. La **capacità** è impostata a 3 unità di S1. Si supponga che questo ambiente abbia ogni giorno un traffico di 2 GB di dati in ingresso. In questo ambiente il traffico in ingresso viene sospeso quando viene raggiunta la capacità massima. A questo punto l'ambiente mostra lo stesso set di dati fino a quando il traffico in ingresso non riprende o fino a quando non viene abilitato il "traffico in ingresso continuo", che potrebbe eliminare i dati meno recenti per fare spazio ai nuovi dati. 

Quando il traffico in ingresso riprende:
- Il flusso di dati procede seguendo l'ordine di ricezione dell'origine evento
- Gli eventi vengono indicizzati in base al proprio timestamp, a meno che i criteri di conservazione nell'origine evento non siano stati superati. Per altre informazioni sulla configurazione di conservazione dell'origine eventi consultare [Domande frequenti sugli Hub eventi di Azure](../event-hubs/event-hubs-faq.md)

> [!IMPORTANT]
> È consigliabile impostare gli avvisi affinché inviino una notifica per evitare la sospensione del traffico in ingresso. Poiché il periodo di conservazione predefinito per le origini eventi di Azure è di 1 giorno, è possibile che si verifichi la perdita di dati. Pertanto quando il traffico in ingresso viene sospeso probabilmente i dati più recenti andranno persi a meno che l'utente non intervenga. È necessario aumentare la capacità o cambiare il comportamento in **Eliminare i dati meno recenti** per evitare un'eventuale perdita di dati.

Negli hub eventi interessati provare a regolare la proprietà **Conservazione messaggi** per ridurre al minimo la perdita di dati quando si verifica la sospensione del traffico in ingresso in Time Series Insights.

![Conservazione messaggi dell'hub eventi.](media/time-series-insights-contepts-retention/event-hub-retention.png)

Se nell'origine evento non è stata configurata alcuna proprietà (timeStampPropertyName), Time Series Insights imposta il timestamp di arrivo all'hub eventi come asse X predefinito. Se timeStampPropertyName è configurato in modo diverso, l'ambiente cerca il timeStampPropertyName configurato nel pacchetto dati quando vengono analizzati gli eventi. 

Se è necessario potenziare l'ambiente per aumentarne la capacità o il periodo di conservazione, vedere [Come scalare l'ambiente Time Series Insights](time-series-insights-how-to-scale-your-environment.md) per altre informazioni.  

## <a name="next-steps"></a>Passaggi successivi
Per informazioni su come modificare i comportamenti di assorbimento, vedere [Configuring retention in Time Series Insights](time-series-insights-how-to-configure-retention.md) (Configurazione della conservazione in Time Series Insights).
