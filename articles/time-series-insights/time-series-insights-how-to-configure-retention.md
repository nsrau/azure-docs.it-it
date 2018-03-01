---
title: Come configurare la conservazione nell'ambiente Azure Time Series Insights | Microsoft Docs
description: Questo articolo descrive come configurare la conservazione nell'ambiente Time Series Insights.
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
ms.openlocfilehash: bd688f516e200a37a6c88a8779282f7391eaf8b8
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2018
---
# <a name="configuring-retention-in-time-series-insights"></a>Configurazione della conservazione in Time Series Insights
Questo articolo descrive come configurare il **periodo di conservazione dei dati** e il **comportamento limite di archiviazione superato** in Azure Time Series Insights.

Ogni ambiente di Time Series Insights (TSI) dispone di un'impostazione per configurare il **periodo di conservazione dei dati**. Il valore va da 1 a 400 giorni. I dati vengono eliminati in base alla capacità di archiviazione dell'ambiente o alla durata della conservazione, che va da 1 a 400 giorni, a seconda della condizione che si verifica per prima.

Ogni ambiente TSI dispone di un'impostazione aggiuntiva, **comportamento limite di archiviazione superato**. Questa impostazione controlla il comportamento di traffico in ingresso e pulizia quando viene raggiunta la capacità massima di un ambiente. È possibile scegliere tra due tipi di comportamento:
- **Eliminare i dati meno recenti** (impostazione predefinita)  
- **Sospendere il traffico in ingresso**

Per informazioni dettagliate per comprendere meglio queste impostazioni, vedere [Understanding retention in Time Series Insights](time-series-insights-concepts-retention.md) (Informazioni sulla conservazione in Time Series Insights).  

## <a name="configure-data-retention"></a>Configurare la conservazione dei dati

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Individuare l'ambiente Time Series Insights esistente. Selezionare **Tutte le risorse** nel menu a sinistra del portale di Azure. Selezionare l'ambiente Time Series Insights.

3. Sotto l'intestazione **IMPOSTAZIONI** selezionare **Configura**.

4. Selezionare il **periodo di conservazione dei dati** per configurare la conservazione tramite la barra di scorrimento o digitare un numero nella casella di testo.

5. Si noti l'impostazione **Capacità**, poiché questa configurazione influisce sulla quantità massima di eventi di dati e sulla capacità di archiviazione totale per l'archiviazione dei dati. 

6. Attivare o disattivare l'impostazione **Comportamento limite di archiviazione superato**. Selezionare il comportamento **Purge old data** (Eliminare i dati meno recenti) o **Pause ingress** (Sospendere il traffico in ingresso).

7. Selezionare **Salva** per configurare le modifiche.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni, vedere [Understanding retention in Time Series Insights](time-series-insights-concepts-retention.md) (Informazioni sulla conservazione in Time Series Insights).
