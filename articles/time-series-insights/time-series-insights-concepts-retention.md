---
title: Comprendere la conservazione dei dati nell'ambiente in uso - Azure Time Series Insight Documenti Microsoft
description: Questo articolo descrive due impostazioni che consentono di controllare la conservazione dati nell'ambiente Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/14/2020
ms.custom: seodec18
ms.openlocfilehash: fd34595d5ea942602efc920904ff326fc203c088
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81380681"
---
# <a name="understand-data-retention-in-azure-time-series-insights"></a>Informazioni sulla conservazione dei dati in Azure Time Series InsightsUnderstand data retention in Azure Time Series Insights

Questo articolo descrive due impostazioni principali che influiscono sulla conservazione dei dati nell'ambiente Azure Time Series Insights.This article describes two primary settings that impact data retention in your Azure Time Series Insights environment.

## <a name="video"></a>Video

### <a name="the-following-video-summarizes-time-series-insights-data-retention-and-how-to-plan-for-itbr"></a>Il video seguente riepiloga la conservazione dei dati di Time Series Insights e come pianificarli.</br>

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

Ognuno degli ambienti Azure Time Series Insights ha un'impostazione che controlla il tempo di **conservazione dei dati.** Il valore va da 1 a 400 giorni. I dati vengono eliminati in base alla capacità di archiviazione dell'ambiente o alla durata di conservazione, a seconda dell'evento che si presenta per primo.

Inoltre, l'ambiente Azure Time Series Insights ha un **limite di archiviazione superato** l'impostazione di comportamento. Controlla il comportamento di ingresso ed eliminazione quando viene raggiunta la capacità massima di un ambiente. Quando lo si configura, è possibile scegliere tra due comportamenti:There are two behaviors to choose from when configuring it:

- **Eliminare i dati meno recenti** (impostazione predefinita)  
- **Sospendere il traffico in ingresso**

> [!NOTE]
> Per impostazione predefinita, quando si crea un nuovo ambiente l'assorbimento è configurato per **eliminare i dati meno recenti**. Questa impostazione può essere attivata o disattivata in base alle esigenze dopo il tempo di creazione usando il portale di Azure, nella pagina Configura dell'ambiente Time Series Insights.This setting can be toggled as needed after creation time using the Azure portal, on the **Configure** page of the Time Series Insights environment.
> * Per informazioni su come configurare i criteri di conservazione, vedere [Configurazione della conservazione in Time Series Insights](time-series-insights-how-to-configure-retention.md).

Entrambi i criteri di conservazione dei dati sono descritti in dettaglio di seguito.

## <a name="purge-old-data"></a>Eliminare i dati meno recenti

- **Elimina dati obsoleti** è l'impostazione predefinita per gli ambienti Azure Time Series Insights.Purge old data is the default setting for Azure Time Series Insights environments.  
- **L'eliminazione dei dati obsoleti** è preferibile quando gli utenti desiderano disporre sempre dei *dati più recenti* nell'ambiente Time Series Insights.
- L'impostazione **Elimina dati precedenti** elimina i dati una volta *raggiunti* i limiti dell'ambiente (tempo di conservazione, dimensioni o conteggio, a seconda dell'evento che si trova per primo). L'assorbimento è impostato a 30 giorni per impostazione predefinita.
- I dati meno recenti vengono eliminati per primi (approccio "First In First Out").

### <a name="example-one"></a>Esempio uno

Si consideri un ambiente di esempio con il comportamento di conservazione **Continuare il traffico in ingresso ed eliminare i dati meno recenti**:

**Il tempo di conservazione dei dati** è impostato su 400 giorni. La **capacità** è impostata sull'unità S1, che contiene 30 GB di capacità totale. Si supponga che in media ogni giorno si accumulino 500 MB di dati in ingresso. Questo ambiente può mantenere i dati solo per 60 giorni in base alla velocità dei dati in ingresso, poiché la capacità massima viene raggiunta a 60 giorni. I dati in ingresso si accumulano in questo modo: 500 MB al giorno x 60 giorni = 30 GB.

Il giorno 61, l'ambiente mostra i dati più recenti, ma elimina i dati meno recenti, più vecchi di 60 giorni. L'eliminazione consente di creare spazio per i nuovi dati in ingresso, in modo che questi possano continuare a essere esaminati. Se l'utente desidera conservare i dati più a lungo, può aumentare le dimensioni dell'ambiente aggiungendo unità supplementari o può inserire una minore quantità di dati.  

### <a name="example-two"></a>Esempio due

Si consideri un ambiente in cui è stato configurato ancora il comportamento di assorbimento **Continuare il traffico in ingresso ed eliminare i dati meno recenti**. In questo esempio il **periodo di conservazione dei dati** è impostato su un valore inferiore, ovvero 180 giorni. La **capacità** è impostata sull'unità S1, che contiene 30 GB di capacità totale. Per memorizzare i dati per l'intero periodo di 180 giorni, il traffico in ingresso giornaliero non può superare 0,166 GB, ovvero 166 MB, al giorno.  

Ogni volta che la velocità di ingresso giornaliera dell'ambiente supera 0,166 GB al giorno, i dati non possono essere archiviati per 180 giorni, poiché alcuni dati vengono eliminati. Si consideri questo stesso ambiente in un intervallo di tempo in cui la velocità in ingresso aumenta. Si supponga che la velocità di ingresso dell'ambiente possa aumentare a una media di 0,189 GB al giorno. In tale intervallo di tempo, i dati vengono conservati per circa 158 giorni (30GB/0,189 = 158,73 giorni di conservazione). Questo periodo di tempo è inferiore rispetto all'intervallo di tempo di conservazione dei dati desiderato.

## <a name="pause-ingress"></a>Sospendere i dati in ingresso

- L'impostazione **Sospendi in ingresso** è progettata per garantire che i dati non vengano eliminati se i limiti di dimensione e conteggio vengono raggiunti prima del periodo di conservazione.  
- **Pausa in ingresso** fornisce tempo aggiuntivo per gli utenti di aumentare la capacità del proprio ambiente prima che i dati vengono eliminati a causa di violazione del periodo di conservazione.
- Consente di proteggerti dalla perdita di dati, ma può creare un'opportunità per la perdita dei dati più recenti se l'ingresso viene sospeso oltre il periodo di conservazione dell'origine eventi.
- Tuttavia, una volta raggiunta la capacità massima di un ambiente, l'ambiente sospende l'ingresso dei dati fino a quando non si verificano le seguenti azioni aggiuntive:

   - Aumentare la capacità massima dell'ambiente per aggiungere più unità di scala come descritto in [come scalare l'ambiente Time Series Insights](time-series-insights-how-to-scale-your-environment.md).
   - Il periodo di conservazione dei dati viene raggiunto e i dati vengono eliminati, portando l'ambiente al di sotto della sua capacità massima.

### <a name="example-three"></a>Esempio tre

Si consideri un ambiente con un comportamento di assorbimento configurato per **sospendere il traffico in ingresso**. In questo esempio il **periodo di conservazione dei dati** è configurato per 60 giorni. **La capacità** è impostata su tre (3) unità di S1. Si supponga che questo ambiente abbia ogni giorno un traffico di 2 GB di dati in ingresso. In questo ambiente il traffico in ingresso viene sospeso quando viene raggiunta la capacità massima.

In quel momento, l'ambiente mostra lo stesso set di dati fino alla ripresa in ingresso o fino a quando non viene abilitato **l'ingresso (che** eliminerebbe i dati meno recenti per fare spazio anuovi dati).

Quando il traffico in ingresso riprende:

- Il flusso di dati procede seguendo l'ordine di ricezione dell'origine evento
- Gli eventi vengono indicizzati in base al proprio timestamp, a meno che i criteri di conservazione nell'origine evento non siano stati superati. Per altre informazioni sulla configurazione di conservazione dell'origine eventi consultare [Domande frequenti sugli Hub eventi di Azure](../event-hubs/event-hubs-faq.md)

> [!IMPORTANT]
> È consigliabile impostare gli avvisi affinché inviino una notifica per evitare la sospensione del traffico in ingresso. Poiché il periodo di conservazione predefinito per le origini eventi di Azure è di 1 giorno, è possibile che si verifichi la perdita di dati. Pertanto quando il traffico in ingresso viene sospeso probabilmente i dati più recenti andranno persi a meno che l'utente non intervenga. È necessario aumentare la capacità o cambiare il comportamento in **Eliminare i dati meno recenti** per evitare un'eventuale perdita di dati.

Negli hub eventi interessati provare a regolare la proprietà **Conservazione messaggi** per ridurre al minimo la perdita di dati quando si verifica la sospensione del traffico in ingresso in Time Series Insights.

[![Conservazione messaggi dell'hub eventi.](media/time-series-insights-concepts-retention/event-hub-retention.png)](media/time-series-insights-concepts-retention/event-hub-retention.png#lightbox)

Se nell'origine evento (`timeStampPropertyName`), Time Series Insights per impostazione predefinita viene impostato il timestamp di arrivo all'hub eventi come asse X. Se `timeStampPropertyName` è configurato per essere qualcos'altro, `timeStampPropertyName` l'ambiente cerca il configurato nel pacchetto di dati quando gli eventi vengono analizzati.

Leggere [Come ridimensionare l'ambiente Time Series Insights](time-series-insights-how-to-scale-your-environment.md) per ridimensionare l'ambiente in modo da adattarsi a capacità aggiuntive o aumentare la durata della conservazione.

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sulla configurazione o la modifica delle impostazioni di conservazione dei dati, vedere [Configurazione della conservazione in Time Series Insights](time-series-insights-how-to-configure-retention.md).

- Informazioni sulla [mitigazione della latenza in Azure Time Series Insights](time-series-insights-environment-mitigate-latency.md).
