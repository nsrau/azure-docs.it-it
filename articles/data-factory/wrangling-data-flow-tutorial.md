---
title: Introduzione alla verifica del flusso di dati in Azure Data Factory
description: Esercitazione su come preparare i dati in Azure Data Factory utilizzando il flusso di dati in corso
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: 8ae109045381dba77610dedc5bb95e233b213eee
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73682270"
---
# <a name="prepare-data-with-wrangling-data-flow"></a>Preparare i dati con il flusso di dati disputato

## <a name="create-a-wrangling-data-flow"></a>Creazione di un flusso di dati di cui litigare

Esistono due modi per creare un flusso di dati in Azure Data Factory. Un modo consiste nel fare clic sull'icona con il segno più e selezionare **flusso di dati** nel riquadro risorse Factory.

![Dispute](media/wrangling-data-flow/tutorial7.png)

L'altro metodo si trova nel riquadro attività dell'area di disegno della pipeline. Aprire lo **spostamento e trasformare** la fisarmonica e trascinare l'attività **flusso di dati** nell'area di disegno.

In entrambi i metodi, nel riquadro laterale che viene visualizzato, selezionare **Crea nuovo flusso di dati** e scegliere **flusso di dati in corso**. Fare clic su OK.

![Dispute](media/wrangling-data-flow/tutorial1.png)

## <a name="author-a-wrangling-data-flow"></a>Creazione di un flusso di dati in corso

Aggiungere un **set** di dati di origine per il flusso di dati in corso. È possibile scegliere un set di dati esistente o crearne uno nuovo. È anche possibile selezionare un set di dati sink. È possibile scegliere uno o più set di dati di origine, ma al momento è consentito un solo sink. La scelta di un set di dati sink è facoltativa, ma è necessario almeno un set di dati di origine.

> [!NOTE]
> Per l'anteprima limitata sono supportati solo i testi delimitati da ADLS gen 2. 

![Dispute](media/wrangling-data-flow/tutorial4.png)

Fare clic su **Crea** per aprire la Power query editor mashup online.

![Dispute](media/wrangling-data-flow/tutorial5.png)

Consente di creare il flusso di dati che si verifica usando la preparazione dei dati senza codice. Per l'elenco delle funzioni disponibili, vedere [funzioni di trasformazione](wrangling-data-flow-functions.md)/

![Dispute](media/wrangling-data-flow/tutorial6.png)

## <a name="running-and-monitoring-a-wrangling-data-flow"></a>Esecuzione e monitoraggio di un flusso di dati in corso

Per eseguire un'esecuzione del debug di una pipeline di un flusso di dati che si verifica, fare clic su **debug** nel Canvas della pipeline. Una volta pubblicato il flusso di dati, **trigger esegue ora** un'esecuzione su richiesta dell'ultima pipeline pubblicata. La pianificazione dei flussi di dati può essere pianificata con tutti i trigger di Azure Data Factory esistenti.

![Dispute](media/wrangling-data-flow/tutorial3.png)

Passare alla scheda **monitoraggio** per visualizzare l'output di un'esecuzione dell'attività flusso di dati avviata.

![Dispute](media/wrangling-data-flow/tutorial2.png)

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [creare un flusso di dati di mapping](tutorial-data-flow.md).