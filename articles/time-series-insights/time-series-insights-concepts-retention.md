---
title: Comprendere la conservazione dati nell'ambiente Azure Time Series Insights | Microsoft Docs
description: Questo articolo descrive due impostazioni che consentono di controllare la conservazione dati nell'ambiente Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/30/2019
ms.custom: seodec18
ms.openlocfilehash: dc192b1e8f5492cdfbfb434b5efb573182c51bb1
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/17/2019
ms.locfileid: "67164571"
---
# <a name="understand-data-retention-in-azure-time-series-insights"></a>Comprendere la conservazione dei dati in Azure Time Series Insights

Questo articolo descrive due impostazioni che influenzano la conservazione dati nell'ambiente Azure Time Series Insights.

## <a name="video"></a>Video

### <a name="the-following-video-summarizes-time-series-insights-data-retention-and-how-to-plan-for-itbr"></a>Il video seguente vengono riepilogate la conservazione dei dati di Time Series Insights e sulla pianificazione per tale.</br>

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

Ognuno degli ambienti Azure Time Series include un'impostazione che consente di controllare **tempo di conservazione dati**. Il valore va da 1 a 400 giorni. I dati vengono eliminati in base la capacità di archiviazione dell'ambiente o la durata di conservazione, a seconda del valore raggiunto per primo.

Inoltre, nell'ambiente Azure Time Series è un **comportamento limite di archiviazione superato** impostazione. Controlla traffico in ingresso ed eliminare il comportamento quando viene raggiunta la capacità massima di un ambiente. Sono possibili due comportamenti in cui scegliere quando si configura lo:

- **Eliminare i dati meno recenti** (impostazione predefinita)  
- **Sospendere il traffico in ingresso**

> [!NOTE]
> Per impostazione predefinita, quando si crea un nuovo ambiente l'assorbimento è configurato per **eliminare i dati meno recenti**. Questa impostazione può essere attivata/disattivata in base alle esigenze dopo l'ora di creazione nel portale di Azure, nelle **configura** pagina dell'ambiente Time Series Insights.

Per informazioni su come modificare i comportamenti di assorbimento, vedere [Configuring retention in Time Series Insights](time-series-insights-how-to-configure-retention.md) (Configurazione dell'assorbimento in Time Series Insights).

Confrontare il comportamento di conservazione dati:

## <a name="purge-old-data"></a>Eliminare i dati meno recenti

- Questo comportamento è quello predefinito per gli ambienti Time Series Insights.  
- Questo comportamento viene preferito quando gli utenti vogliono visualizzare sempre loro *dati più recenti* nel proprio ambiente Time Series Insights.
- Consente di *eliminare* i dati quando si raggiungono i limiti dell'ambiente (periodo di conservazione, dimensioni o numero di elementi, a seconda del limite che viene raggiunto prima). L'assorbimento è impostato a 30 giorni per impostazione predefinita.
- I dati acquisiti meno recenti vengono eliminati prima (approccio FIFO).

### <a name="example-one"></a>Esempio n. 1

Si consideri un ambiente di esempio con il comportamento di conservazione **Continuare il traffico in ingresso ed eliminare i dati meno recenti**:

**Tempo di conservazione dati** è impostato a 400 giorni. La **capacità** è impostata sull'unità S1, che contiene 30 GB di capacità totale.   Si supponga che in media ogni giorno si accumulino 500 MB di dati in ingresso. Questo ambiente può mantenere i dati solo per 60 giorni in base alla velocità dei dati in ingresso, poiché la capacità massima viene raggiunta a 60 giorni. I dati in ingresso si accumulano al ritmo di 500 MB al giorno per 60 giorni per un totale di 30 GB.

Il giorno 61 l'ambiente Mostra usare i dati, ma i dati meno recente, meno di 60 giorni vengono eliminati. L'eliminazione consente di creare spazio per i nuovi dati in ingresso, in modo che questi possano continuare a essere esaminati. Se l'utente desidera conservare i dati più a lungo, può aumentare le dimensioni dell'ambiente aggiungendo unità supplementari o può inserire una minore quantità di dati.  

### <a name="example-two"></a>Esempio 2

Si consideri un ambiente in cui è stato configurato ancora il comportamento di assorbimento **Continuare il traffico in ingresso ed eliminare i dati meno recenti**. In questo esempio il **periodo di conservazione dei dati** è impostato su un valore inferiore, ovvero 180 giorni. La **capacità** è impostata sull'unità S1, che contiene 30 GB di capacità totale. Per memorizzare i dati per l'intero periodo di 180 giorni, il traffico in ingresso giornaliero non può superare 0,166 GB, ovvero 166 MB, al giorno.  

Se la velocità in ingresso giornaliera di questo ambiente supera 0,166 GB al giorno, i dati non possono essere archiviati per 180 giorni poiché alcuni di questi vengono eliminati. Si consideri questo stesso ambiente in un intervallo di tempo in cui la velocità in ingresso aumenta. Si supponga che la velocità in ingresso dell'ambiente possa raggiungere in media 0,189 GB al giorno. In tale intervallo di tempo, i dati vengono conservati per circa 158 giorni (30GB/0,189 = 158,73 giorni di conservazione). Questo periodo di tempo è inferiore rispetto all'intervallo di tempo di conservazione dei dati desiderato.

## <a name="pause-ingress"></a>Sospendere i dati in ingresso

- Il **sospendere ingress** impostazione è progettata per assicurarsi che i dati non vengono eliminati se prima del periodo di conservazione vengono raggiunti i limiti di dimensione e conteggio.  
- **Sospendere in ingresso** fornisce ulteriore tempo per gli utenti aumentare la capacità dell'ambiente prima che i dati vengono eliminati perché viene superato il periodo di mantenimento dati
- Consente di proteggersi dalla perdita di dati, ma può creare un'opportunità per la perdita di dati più recenti se il traffico in ingresso viene sospeso oltre il periodo di conservazione dell'origine evento.
- Tuttavia, quando viene raggiunta la capacità massima di un ambiente, viene sospeso l'ambiente di inserimento dei dati fino a quando non si verificano le azioni aggiuntive seguenti:

   - Si aumenta la capacità massima dell'ambiente per aggiungere altre unità di scala, come descritto in [come scalare l'ambiente Time Series Insights](time-series-insights-how-to-scale-your-environment.md).
   - Viene raggiunto il periodo di conservazione dei dati e i dati vengono eliminati, riportare l'ambiente di sotto della capacità massima.

### <a name="example-three"></a>Esempio 3

Si consideri un ambiente con un comportamento di assorbimento configurato per **sospendere il traffico in ingresso**. In questo esempio il **periodo di conservazione dei dati** è configurato per 60 giorni. **Capacità** è impostata su tre (3) l'unità di S1. Si supponga che questo ambiente abbia ogni giorno un traffico di 2 GB di dati in ingresso. In questo ambiente il traffico in ingresso viene sospeso quando viene raggiunta la capacità massima.

A quel punto, l'ambiente Mostra stesso set di dati fino al traffico in ingresso riprende o fino alla **traffico in ingresso continuo** è abilitato (che potrebbe eliminare i dati meno recenti per liberare spazio per nuovi dati).

Quando il traffico in ingresso riprende:

- Il flusso di dati procede seguendo l'ordine di ricezione dell'origine evento
- Gli eventi vengono indicizzati in base al proprio timestamp, a meno che i criteri di conservazione nell'origine evento non siano stati superati. Per altre informazioni sulla configurazione di conservazione dell'origine eventi consultare [Domande frequenti sugli Hub eventi di Azure](../event-hubs/event-hubs-faq.md)

> [!IMPORTANT]
> È consigliabile impostare gli avvisi affinché inviino una notifica per evitare la sospensione del traffico in ingresso. Poiché il periodo di conservazione predefinito per le origini eventi di Azure è di 1 giorno, è possibile che si verifichi la perdita di dati. Pertanto quando il traffico in ingresso viene sospeso probabilmente i dati più recenti andranno persi a meno che l'utente non intervenga. È necessario aumentare la capacità o cambiare il comportamento in **Eliminare i dati meno recenti** per evitare un'eventuale perdita di dati.

Negli hub eventi interessati provare a regolare la proprietà **Conservazione messaggi** per ridurre al minimo la perdita di dati quando si verifica la sospensione del traffico in ingresso in Time Series Insights.

[![Conservazione messaggi dell'hub eventi.](media/time-series-insights-contepts-retention/event-hub-retention.png)](media/time-series-insights-contepts-retention/event-hub-retention.png#lightbox)

Se nessuna proprietà è configurata nell'origine evento (`timeStampPropertyName`), Time Series Insights imposta il timestamp dell'arrivo all'hub eventi come asse x. Se `timeStampPropertyName` è configurato per un'altra, la ricerca di ambiente dell'applicazione configurata `timeStampPropertyName` nel pacchetto dati quando vengono analizzati gli eventi.

Se è necessario potenziare l'ambiente per aumentarne la capacità o il periodo di conservazione, vedere [Come scalare l'ambiente Time Series Insights](time-series-insights-how-to-scale-your-environment.md) per altre informazioni.  

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sulla configurazione o la modifica delle impostazioni di conservazione dei dati, esaminare [configurazione della conservazione in Time Series Insights](time-series-insights-how-to-configure-retention.md).
