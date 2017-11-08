---
title: Gestire i costi della conservazione dei dati in Log Analytics di Azure | Microsoft Docs
description: Informazioni su come modificare il piano tariffario e i criteri di conservazione dei dati per l'area di lavoro di Log Analytics nel portale di Azure.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2017
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 35064c792b72222d59b1d3f0913a92a4a2b34612
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/01/2017
---
# <a name="manage-cost-of-data-retention-with-your-log-analytics-workspace"></a>Gestire i costi di conservazione dei dati con l'area di lavoro di Log Analytics
Con l'iscrizione a Log Analytics, a seconda del piano selezionato, è previsto un limite sul tempo di archiviazione dei dati generati da origini connesse nell'area di lavoro.  Questo articolo evidenzia le considerazioni che possono influenzare i costi per la conservazione dei dati per diversi periodi di tempo e come configurare tale limite.   

Poiché Log Analytics può usare grandi quantità di dati da origini locali o da ambienti cloud e ibridi, i costi di archiviazione dei dati per un periodo di tempo possono essere considerevoli in base ai fattori seguenti:

* Numero di sistemi, componenti dell'infrastruttura, risorse cloud e così via, da cui avviene la raccolta
* Tipo di dati creati dall'origine, ad esempio code di messaggi, log, eventi, dati sulla sicurezza o metriche sulle prestazioni
* Volume dei dati generati da queste origini 
* Numero di soluzioni di gestione abilitate, origine dati e frequenza di raccolta

> [!NOTE]
> Consultare la documentazione per ogni soluzione per avere una stima della quantità di dati raccolti.   

Se si usa il piano *Gratuito*, i dati hanno un limite di conservazione di sette giorni.  Per il livello *Autonomo* o *A pagamento*, sono disponibili i dati raccolti negli ultimi 31 giorni.  

Quando si usa il piano *Gratuito*, se si superano le quantità consentite, è possibile convertire l'area di lavoro in un piano a pagamento per raccogliere dati oltre questo limite. 

> [!NOTE]
> Se si sceglie di selezionare un periodo di conservazione più lungo per il livello a pagamento, vengono addebitati dei costi. È possibile modificare il tipo di piano in qualsiasi momento. Per altre informazioni sui prezzi, vedere [Dettagli prezzi](https://azure.microsoft.com/pricing/details/log-analytics/). 

## <a name="change-the-data-retention-period"></a>Cambiare il periodo di conservazione dei dati 

1. Accedere al [portale di Azure](http://portal.azure.com). 
2. Fare clic su **Altri servizi** nell'angolo in basso a sinistra. Nell'elenco delle risorse digitare **Log Analytics**. Non appena si inizia a digitare, l'elenco viene filtrato in base all'input. Selezionare **Log Analytics**.
3. Nel riquadro di sottoscrizione di Log Analytics selezionare dall'elenco l'area di lavoro da modificare.
4. Nella pagina dell'area di lavoro fare clic su **Conservazione** dal riquadro di sinistra.
5. Nel riquadro di conservazione dell'area di lavoro spostare il dispositivo di scorrimento per aumentare o diminuire il numero di giorni e quindi fare clic su **Salva**.  Se si usa il livello *gratuito*, non è possibile modificare il periodo di conservazione dei dati ed è necessario eseguire l'aggiornamento al piano a pagamento per controllare questa impostazione.<br><br> ![Modificare l'impostazione di conservazione dei dati dell'area di lavoro](media/log-analytics-manage-cost/manage-cost-change-retention.png)

## <a name="next-steps"></a>Passaggi successivi  

Per determinare la quantità di dati raccolti, da quali origini vengono inviati e i diversi tipi di dati inviati per gestire il consumo e i costi, vedere [Analizzare l'utilizzo dei dati in Log Analytics](log-analytics-usage.md).