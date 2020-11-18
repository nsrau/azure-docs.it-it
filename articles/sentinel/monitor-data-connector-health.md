---
title: Monitorare l'integrità dei connettori dati con questa cartella di lavoro di Azure Sentinel | Microsoft Docs
description: Usare la cartella di lavoro Monitoraggio dello stato per tenere traccia delle prestazioni e della connettività dei connettori dati.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2020
ms.author: yelevin
ms.openlocfilehash: 7d52b76601a617f62ae5b10fa38841ef2608bf49
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94656991"
---
# <a name="monitor-the-health-of-your-data-connectors-with-this-azure-sentinel-workbook"></a>Monitorare l'integrità dei connettori dati con questa cartella di lavoro di Azure Sentinel

La **cartella di lavoro di monitoraggio dell'integrità dei connettori dati** consente di tenere traccia dell'integrità, della connettività e delle prestazioni dei connettori dati, dall'interno di Sentinel di Azure. La cartella di lavoro fornisce monitoraggi aggiuntivi, rileva le anomalie e fornisce informazioni relative allo stato di inserimento dei dati dell'area di lavoro. È possibile utilizzare la logica della cartella di lavoro per monitorare l'integrità generale dei dati inseriti e per creare visualizzazioni personalizzate e avvisi basati su regole.

## <a name="use-the-health-monitoring-workbook"></a>Usare la cartella di lavoro di monitoraggio dello stato

1. Dal portale di Azure Sentinel selezionare **cartelle di lavoro** dal menu **gestione delle minacce** .

1. Nella raccolta **cartelle di lavoro** immettere *integrità* nella barra di ricerca e selezionare **Monitoraggio integrità raccolta dati** tra i risultati.

1. Selezionare **Visualizza modello** per utilizzare la cartella di lavoro così come è oppure selezionare **Salva** per creare una copia modificabile della cartella di lavoro. Quando viene creata la copia, selezionare **Visualizza cartella di lavoro salvata**.

1. Una volta nella cartella di lavoro, selezionare prima di tutto la **sottoscrizione** e l' **area di lavoro** che si desidera visualizzare, quindi definire il **TimeRange** per filtrare i dati in base alle esigenze. Usare l'interruttore **Mostra guida** per visualizzare la spiegazione sul posto della cartella di lavoro.

    :::image type="content" source="media/monitor-data-connector-health/data-health-workbook-1.png" alt-text="pagina di destinazione del monitoraggio dell'integrità del connettore dati" lightbox="media/monitor-data-connector-health/data-health-workbook-1.png":::

Questa cartella di lavoro include tre sezioni a schede:

1. La scheda **Panoramica** Mostra lo stato generale dell'inserimento di dati nell'area di lavoro selezionata: misure del volume, velocità EPS e ora dell'ultimo log ricevuto.

1. La scheda **anomalie raccolta dati** consente di rilevare le anomalie nel processo di raccolta dei dati, in base alla tabella e all'origine dati. Ogni scheda presenta anomalie per una determinata tabella (la scheda **generale** include una raccolta di tabelle). Le anomalie vengono calcolate utilizzando la funzione **series_decompose_anomalies ()** che restituisce un **Punteggio di anomalie**. [Altre informazioni su questa funzione](/azure/data-explorer/kusto/query/series-decompose-anomaliesfunction?WT.mc_id=Portal-fx). Impostare i parametri seguenti per la funzione da valutare:

    - **AnomaliesTimeRange**: questo selettore di data/ora si applica solo alla visualizzazione Anomalie della raccolta dati.
    - **SampleInterval**: l'intervallo di tempo specifico in cui i dati vengono campionati. Il punteggio di anomalia viene calcolato solo sui dati dell'ultimo intervallo.
    - **PositiveAlertThreshold**: questo valore definisce la soglia dei punteggi di anomalia. Accetta valori decimali.
    - **NegativeAlertThreshold**: questo valore definisce la soglia dei punteggi di anomalia negativo. Accetta valori decimali.

        :::image type="content" source="media/monitor-data-connector-health/data-health-workbook-2.png" alt-text="Data Connector Monitoraggio integrità cartella di lavoro pagina anomalie" lightbox="media/monitor-data-connector-health/data-health-workbook-2.png":::

1. La scheda **informazioni agente** Mostra informazioni sull'integrità degli agenti di log Analytics installati nei vari computer, sia che si tratti di macchine virtuali di Azure, altre VM cloud, VM locali o fisiche. È possibile monitorare gli elementi seguenti:

   - Percorso di sistema

   - Stato e latenza heartbeat

   - Memoria e spazio su disco disponibili

   - Operazioni dell'agente

    In questa sezione è necessario selezionare la scheda che descrive l'ambiente dei computer: scegliere la scheda **computer gestiti da Azure** se si desidera visualizzare solo i computer gestiti da Azure Arc; scegliere la scheda **tutti i computer** per visualizzare i computer gestiti e non Azure con l'agente di log Analytics installato.

    :::image type="content" source="media/monitor-data-connector-health/data-health-workbook-3.png" alt-text="Data Connector monitoraggio dell'integrità della cartella di lavoro informazioni sull'agente" lightbox="media/monitor-data-connector-health/data-health-workbook-3.png":::

## <a name="next-steps"></a>Passaggi successivi
Informazioni su come caricare [i dati in Sentinel di Azure](quickstart-onboard.md), [connettere le origini dati](connect-data-sources.md)e [ottenere visibilità sui dati e potenziali minacce](quickstart-get-visibility.md).