---
title: Introduzione al flusso di dati wrangling in Azure Data FactoryGetting started with wrangling data flow in Azure Data Factory
description: Esercitazione su come preparare i dati in Azure Data Factory usando il flusso di dati di wrangling
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: a180a7a0c85b642ac09d1d027c95809c4638dee1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81409003"
---
# <a name="prepare-data-with-wrangling-data-flow"></a>Preparare i dati con il flusso di dati wranglingPrepare data with wrangling data flow

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="create-a-wrangling-data-flow"></a>Creare un flusso di dati di wranglingCreate a wrangling data flow

Esistono due modi per creare un flusso di dati di wrangling in Azure Data Factory.There are two ways to create a wrangling data flow in Azure Data Factory. Un modo consiste nel fare clic sull'icona più e selezionare **Flusso di dati** nel riquadro delle risorse di fabbrica.

![Dispute](media/wrangling-data-flow/tutorial7.png)

L'altro metodo si trova nel riquadro delle attività dell'area di disegno della pipeline. Aprire la fisarmonica **Sposta e trasforma** e trascinare l'attività Flusso di **dati** nell'area di disegno.

In entrambi i metodi, nel riquadro laterale visualizzato, selezionare **Crea nuovo flusso** di dati e scegliere **Wrangling data flow**. Fare clic su OK.

![Dispute](media/wrangling-data-flow/tutorial1.png)

## <a name="author-a-wrangling-data-flow"></a>Creare un flusso di dati di wrangling

Aggiungere un **set di dati di origine** per il flusso di dati di wrangling.Add a Source dataset for your wrangling data flow. È possibile scegliere un set di dati esistente o crearne uno nuovo. È inoltre possibile selezionare un set di dati sink. È possibile scegliere uno o più set di dati di origine, ma in questo momento è consentito un solo sink. La scelta di un set di dati sink è facoltativa, ma è necessario almeno un set di dati di origine.

> [!NOTE]
> Solo ADLS Gen 2 Delimited Text è supportato per l'anteprima limitata. 

![Dispute](media/wrangling-data-flow/tutorial4.png)

Fare clic su **Crea** per aprire l'editor di mashup di Power Query Online.

![Dispute](media/wrangling-data-flow/tutorial5.png)

Crea il tuo flusso di dati di wrangling usando la preparazione dei dati senza codice. Per l'elenco delle funzioni disponibili, vedere [Funzioni di trasformazione](wrangling-data-flow-functions.md)/

![Dispute](media/wrangling-data-flow/tutorial6.png)

## <a name="running-and-monitoring-a-wrangling-data-flow"></a>Esecuzione e monitoraggio di un flusso di dati di wranglingRunning and monitoring a wrangling data flow

Per eseguire un'esecuzione di debug della pipeline di un flusso di dati di wrangling, fare clic su **Debug** nell'area di disegno della pipeline. Dopo aver pubblicato il flusso di dati, **Trigger ora** esegue un'esecuzione su richiesta dell'ultima pipeline pubblicata. I flussi di dati wrangling possono essere pianificati con tutti i trigger di Azure Data Factory esistenti.

![Dispute](media/wrangling-data-flow/tutorial3.png)

Passare alla scheda **Monitoraggio** per visualizzare l'output di un'esecuzione dell'attività del flusso di dati wrangling attivata.

![Dispute](media/wrangling-data-flow/tutorial2.png)

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come creare un flusso di dati di [mapping.](tutorial-data-flow.md)