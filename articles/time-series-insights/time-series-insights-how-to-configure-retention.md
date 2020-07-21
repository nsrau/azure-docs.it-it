---
title: Come configurare la conservazione nell'ambiente-Azure Time Series Insights | Microsoft Docs
description: Informazioni su come configurare la conservazione nell'ambiente Azure Time Series Insights di Azure.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.topic: conceptual
ms.date: 06/30/2020
ms.custom: seodec18
ms.openlocfilehash: 9ee06501134515d9369e98e724e55a66f040fffa
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86495126"
---
# <a name="configuring-retention-in-azure-time-series-insights-gen1"></a>Configurazione della conservazione in Azure Time Series Insights Gen1

Questo articolo descrive come configurare il **periodo di conservazione dei dati** e il **comportamento limite di archiviazione superato** in Azure Time Series Insights.

## <a name="summary"></a>Summary

Ogni ambiente Azure Time Series Insights dispone di un'impostazione per configurare il **periodo di conservazione dei dati**. Il valore va da 1 a 400 giorni. I dati vengono eliminati in base alla capacità di archiviazione dell'ambiente o alla durata della conservazione, che va da 1 a 400 giorni, a seconda della condizione che si verifica per prima.

Ogni ambiente Azure Time Series Insights ha un'impostazione aggiuntiva del **limite di archiviazione superato il comportamento**. Questa impostazione controlla il comportamento di traffico in ingresso e pulizia quando viene raggiunta la capacità massima di un ambiente. È possibile scegliere tra due tipi di comportamento:

- **Eliminare i dati meno recenti** (impostazione predefinita)
- **Sospendere il traffico in ingresso**

Per informazioni dettagliate per comprendere meglio queste impostazioni, vedere informazioni sulla [conservazione in Azure Time Series Insights](time-series-insights-concepts-retention.md).  

## <a name="configure-data-retention"></a>Configurare la conservazione dei dati

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Individuare l'ambiente di Azure Time Series Insights esistente. Selezionare **Tutte le risorse** nel menu a sinistra del portale di Azure. Selezionare l'ambiente Azure Time Series Insights.

1. Sotto l'intestazione **Impostazioni** selezionare **Configurazione archiviazione**.

    [![In impostazioni selezionare Configurazione archiviazione](media/data-retention/configure-data-retention.png)](media/data-retention/configure-data-retention.png#lightbox)

1. Selezionare il **periodo di conservazione dei dati (in giorni)** per configurare la conservazione usando la barra del dispositivo di scorrimento o digitare un numero nella casella di testo.

1. Si noti l'impostazione **Capacità**, poiché questa configurazione influisce sulla quantità massima di eventi di dati e sulla capacità di archiviazione totale per l'archiviazione dei dati.

1. Attivare o disattivare l'impostazione **Comportamento limite di archiviazione superato**. Selezionare il comportamento **Purge old data** (Eliminare i dati meno recenti) o **Pause ingress** (Sospendere il traffico in ingresso).

    [![Sospendere il traffico in ingresso: accetta e Salva.](media/data-retention/pause-ingress-accept-and-save.png)](media/data-retention/pause-ingress-accept-and-save.png#lightbox)

1. Esaminare la documentazione per conoscere i potenziali rischi di perdita dei dati. Selezionare **Salva** per configurare le modifiche.

## <a name="next-steps"></a>Passaggi successivi

- Per ulteriori informazioni, vedere informazioni sulla [conservazione in Azure Time Series Insights](time-series-insights-concepts-retention.md).

- Informazioni [su come ridimensionare l'ambiente di Azure Time Series Insights](time-series-insights-how-to-scale-your-environment.md).

- Informazioni sulla [pianificazione dell'ambiente](time-series-insights-environment-planning.md).
