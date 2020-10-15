---
title: Introduzione alla verifica del flusso di dati in Azure Data Factory
description: Esercitazione su come preparare i dati in Azure Data Factory utilizzando il flusso di dati in corso
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: e9a4f7266522fc17c33e2086af6cb7367bbed39e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91650681"
---
# <a name="prepare-data-with-wrangling-data-flow"></a>Preparare i dati con il flusso di dati disputato

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

> [!NOTE]
> Il flusso di dati in corso è attualmente avilable in anteprima pubblica

## <a name="create-a-wrangling-data-flow"></a>Creazione di un flusso di dati di cui litigare

Esistono due modi per creare un flusso di dati in Azure Data Factory. Un modo consiste nel fare clic sull'icona con il segno più e selezionare **flusso di dati** nel riquadro risorse Factory.

![Screenshot che mostra il flusso di dati nel riquadro risorse Factory.](media/wrangling-data-flow/tutorial7.png)

L'altro metodo si trova nel riquadro attività dell'area di disegno della pipeline. Aprire lo **spostamento e trasformare** la fisarmonica e trascinare l'attività **flusso di dati** nell'area di disegno.

In entrambi i metodi, nel riquadro laterale che viene visualizzato, selezionare **Crea nuovo flusso di dati** e scegliere **flusso di dati in corso**. Fare clic su OK.

![Screenshot che evidenzia l'opzione del flusso di dati in corso.](media/wrangling-data-flow/tutorial1.png)

## <a name="author-a-wrangling-data-flow"></a>Creazione di un flusso di dati in corso

Aggiungere un **set** di dati di origine per il flusso di dati in corso. È possibile scegliere un set di dati esistente o crearne uno nuovo. È anche possibile selezionare un set di dati sink. È possibile scegliere uno o più set di dati di origine, ma al momento è consentito un solo sink. La scelta di un set di dati sink è facoltativa, ma è necessario almeno un set di dati di origine.

> [!NOTE]
> Per l'anteprima limitata sono supportati solo i testi delimitati da ADLS gen 2. 

![Dispute](media/wrangling-data-flow/tutorial4.png)

Fare clic su **Crea** per aprire la Power query editor mashup online.

![Screenshot che mostra il pulsante Crea che consente di aprire il Power Query Editor mashup online.](media/wrangling-data-flow/tutorial5.png)

Consente di creare il flusso di dati che si verifica usando la preparazione dei dati senza codice. Per l'elenco delle funzioni disponibili, vedere [funzioni di trasformazione](wrangling-data-flow-functions.md).

![Screenshot che illustra il processo per la creazione del flusso di dati in corso.](media/wrangling-data-flow/tutorial6.png)

## <a name="running-and-monitoring-a-wrangling-data-flow"></a>Esecuzione e monitoraggio di un flusso di dati in corso

Per eseguire un'esecuzione del debug di una pipeline di un flusso di dati che si verifica, fare clic su **debug** nel Canvas della pipeline. Una volta pubblicato il flusso di dati, **trigger esegue ora** un'esecuzione su richiesta dell'ultima pipeline pubblicata. La pianificazione dei flussi di dati può essere pianificata con tutti i trigger di Azure Data Factory esistenti.

![Screenshot che illustra come aggiungere un flusso di dati in confronto.](media/wrangling-data-flow/tutorial3.png)

Passare alla scheda **monitoraggio** per visualizzare l'output di un'esecuzione dell'attività flusso di dati avviata.

![Screenshot che mostra l'output di un'attività del flusso di dati avviata.](media/wrangling-data-flow/tutorial2.png)

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come [creare un flusso di dati di mapping](tutorial-data-flow.md).