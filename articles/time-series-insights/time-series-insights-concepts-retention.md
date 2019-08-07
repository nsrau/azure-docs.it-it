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
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 5388b157ebea78a69355eb745492910f260be3ad
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68823652"
---
# <a name="understand-data-retention-in-azure-time-series-insights"></a>Informazioni sulla conservazione dei dati in Azure Time Series Insights

Questo articolo descrive due impostazioni che influiscano sulla conservazione dei dati nell'ambiente Azure Time Series Insights.

## <a name="video"></a>Video

### <a name="the-following-video-summarizes-time-series-insights-data-retention-and-how-to-plan-for-itbr"></a>Il video seguente riepiloga Time Series Insights la conservazione dei dati e la relativa modalità di pianificazione.</br>

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

Ogni ambiente della serie temporale di Azure dispone di un'impostazione che controlla il **tempo di conservazione dei dati**. Il valore va da 1 a 400 giorni. I dati vengono eliminati in base alla capacità di archiviazione dell'ambiente o alla durata di conservazione, a seconda di quale si verifichi per primo.

Inoltre, l'ambiente Azure Time Series presenta un **limite di archiviazione che supera** l'impostazione del comportamento. Controlla il comportamento in ingresso e ripulisce quando viene raggiunta la capacità massima di un ambiente. Quando si configura, è possibile scegliere tra due comportamenti:

- **Eliminare i dati meno recenti** (impostazione predefinita)  
- **Sospendere il traffico in ingresso**

> [!NOTE]
> Per impostazione predefinita, quando si crea un nuovo ambiente l'assorbimento è configurato per **eliminare i dati meno recenti**. Questa impostazione può essere attivata o disattivata in base alle esigenze dopo l'ora di creazione usando il portale di Azure nella pagina **Configura** dell'ambiente Time Series Insights.

Per informazioni su come modificare i comportamenti di assorbimento, vedere [Configuring retention in Time Series Insights](time-series-insights-how-to-configure-retention.md) (Configurazione dell'assorbimento in Time Series Insights).

Confrontare il comportamento di conservazione dati:

## <a name="purge-old-data"></a>Elimina dati meno recenti

- Questo comportamento è il comportamento predefinito per gli ambienti Time Series Insights.  
- Questo comportamento è preferibile quando gli utenti vogliono visualizzare sempre i *dati più recenti* nell'ambiente Time Series Insights.
- Consente di *eliminare* i dati quando si raggiungono i limiti dell'ambiente (periodo di conservazione, dimensioni o numero di elementi, a seconda del limite che viene raggiunto prima). L'assorbimento è impostato a 30 giorni per impostazione predefinita.
- I dati acquisiti meno recenti vengono eliminati prima (approccio FIFO).

### <a name="example-one"></a>Esempio uno

Si consideri un ambiente di esempio con il comportamento di conservazione **Continuare il traffico in ingresso ed eliminare i dati meno recenti**:

Il **periodo di conservazione dei dati** è impostato su 400 giorni. La **capacità** è impostata sull'unità S1, che contiene 30 GB di capacità totale.   Si supponga che in media ogni giorno si accumulino 500 MB di dati in ingresso. Questo ambiente può mantenere i dati solo per 60 giorni in base alla velocità dei dati in ingresso, poiché la capacità massima viene raggiunta a 60 giorni. I dati in ingresso si accumulano al ritmo di 500 MB al giorno per 60 giorni per un totale di 30 GB.

Il 61 ° giorno l'ambiente Visualizza i dati più recenti, ma elimina i dati meno recenti, più vecchi di 60 giorni. L'eliminazione consente di creare spazio per i nuovi dati in ingresso, in modo che questi possano continuare a essere esaminati. Se l'utente desidera conservare i dati più a lungo, può aumentare le dimensioni dell'ambiente aggiungendo unità supplementari o può inserire una minore quantità di dati.  

### <a name="example-two"></a>Esempio due

Si consideri un ambiente in cui è stato configurato ancora il comportamento di assorbimento **Continuare il traffico in ingresso ed eliminare i dati meno recenti**. In questo esempio il **periodo di conservazione dei dati** è impostato su un valore inferiore, ovvero 180 giorni. La **capacità** è impostata sull'unità S1, che contiene 30 GB di capacità totale. Per memorizzare i dati per l'intero periodo di 180 giorni, il traffico in ingresso giornaliero non può superare 0,166 GB, ovvero 166 MB, al giorno.  

Se la velocità in ingresso giornaliera di questo ambiente supera 0,166 GB al giorno, i dati non possono essere archiviati per 180 giorni poiché alcuni di questi vengono eliminati. Si consideri questo stesso ambiente in un intervallo di tempo in cui la velocità in ingresso aumenta. Si supponga che la velocità in ingresso dell'ambiente possa raggiungere in media 0,189 GB al giorno. In tale intervallo di tempo, i dati vengono conservati per circa 158 giorni (30GB/0,189 = 158,73 giorni di conservazione). Questo periodo di tempo è inferiore rispetto all'intervallo di tempo di conservazione dei dati desiderato.

## <a name="pause-ingress"></a>Sospendi dati in ingresso

- L'impostazione **Sospendi ingresso** è progettata per garantire che i dati non vengano eliminati se si raggiungono i limiti relativi alle dimensioni e al numero prima del periodo di conservazione.  
- Sospendere il traffico in **ingresso** consente agli utenti di aumentare la capacità dell'ambiente prima che i dati vengano eliminati a causa della violazione del periodo di conservazione
- Consente di evitare la perdita di dati, ma può creare un'opportunità per la perdita dei dati più recenti se il traffico in ingresso viene sospeso oltre il periodo di conservazione dell'origine evento.
- Tuttavia, una volta raggiunta la capacità massima di un ambiente, l'ambiente sospende l'ingresso dei dati fino a quando non si verificano le azioni aggiuntive seguenti:

   - È possibile aumentare la capacità massima dell'ambiente per aggiungere altre unità di scala, come descritto in [come ridimensionare l'ambiente Time Series Insights](time-series-insights-how-to-scale-your-environment.md).
   - Viene raggiunto il periodo di conservazione dei dati e i dati vengono eliminati, portando l'ambiente al di sotto della capacità massima.

### <a name="example-three"></a>Esempio tre

Si consideri un ambiente con un comportamento di assorbimento configurato per **sospendere il traffico in ingresso**. In questo esempio il **periodo di conservazione dei dati** è configurato per 60 giorni. La **capacità** è impostata su tre (3) unità S1. Si supponga che questo ambiente abbia ogni giorno un traffico di 2 GB di dati in ingresso. In questo ambiente il traffico in ingresso viene sospeso quando viene raggiunta la capacità massima.

A questo punto, l'ambiente Visualizza lo stesso set di dati fino a quando il traffico in ingresso riprende o finché non viene attivato il traffico in **ingresso** , che elimina i dati meno recenti per fare spazio ai nuovi dati.

Quando il traffico in ingresso riprende:

- Il flusso di dati procede seguendo l'ordine di ricezione dell'origine evento
- Gli eventi vengono indicizzati in base al proprio timestamp, a meno che i criteri di conservazione nell'origine evento non siano stati superati. Per altre informazioni sulla configurazione di conservazione dell'origine eventi consultare [Domande frequenti sugli Hub eventi di Azure](../event-hubs/event-hubs-faq.md)

> [!IMPORTANT]
> È consigliabile impostare gli avvisi affinché inviino una notifica per evitare la sospensione del traffico in ingresso. Poiché il periodo di conservazione predefinito per le origini eventi di Azure è di 1 giorno, è possibile che si verifichi la perdita di dati. Pertanto quando il traffico in ingresso viene sospeso probabilmente i dati più recenti andranno persi a meno che l'utente non intervenga. È necessario aumentare la capacità o cambiare il comportamento in **Eliminare i dati meno recenti** per evitare un'eventuale perdita di dati.

Negli hub eventi interessati provare a regolare la proprietà **Conservazione messaggi** per ridurre al minimo la perdita di dati quando si verifica la sospensione del traffico in ingresso in Time Series Insights.

[![Conservazione dei messaggi nell'hub eventi.](media/time-series-insights-contepts-retention/event-hub-retention.png)](media/time-series-insights-contepts-retention/event-hub-retention.png#lightbox)

Se non è configurata alcuna proprietà nell'`timeStampPropertyName`origine evento (), Time Series Insights imposta come valore predefinito il timestamp di arrivo nell'hub eventi come asse X. Se `timeStampPropertyName` è configurato come altro, l'ambiente cerca la configurazione `timeStampPropertyName` nel pacchetto di dati quando vengono analizzati gli eventi.

Se è necessario potenziare l'ambiente per aumentarne la capacità o il periodo di conservazione, vedere [Come scalare l'ambiente Time Series Insights](time-series-insights-how-to-scale-your-environment.md) per altre informazioni.  

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni sulla configurazione o la modifica delle impostazioni di conservazione dei dati, vedere [configurazione della conservazione in Time Series Insights](time-series-insights-how-to-configure-retention.md).
