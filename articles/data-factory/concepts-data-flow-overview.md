---
title: Panoramica dei flussi di dati di mapping di Azure Data Factory
description: Una presentazione generale dei flussi di dati di mapping in Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: 6f4c124c59584c8538d85ac61650661ae559a77b
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70123980"
---
# <a name="what-are-mapping-data-flows"></a>Cosa sono i flussi di dati di mapping?

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

I flussi di dati di mapping sono una trasformazione di dati progettata visivamente in Azure Data Factory. I flussi di dati consentono agli ingegneri di dati di sviluppare la logica di trasformazione dei dati grafici senza scrivere codice. I flussi di dati risultanti vengono eseguiti come attività all'interno delle pipeline di Azure Data Factory usando cluster di Azure Databricks in cui è stato aumentato il numero di istanze.

Lo scopo di Flusso di dati di Azure Data Factory è quello di offrire un'esperienza completamente visiva senza la scrittura di codice. I flussi di dati verranno eseguiti nel cluster di esecuzione per l'elaborazione dei dati in più istanze. Azure Data Factory gestisce automaticamente tutte le attività di conversione del codice, ottimizzazione dei percorsi ed esecuzione dei processi dei flussi di dati.

Per iniziare, creare i flussi di dati in modalità Debug in modo da poter convalidare interattivamente la logica delle trasformazioni. Aggiungere quindi un'attività di Flusso di dati alla pipeline per eseguire e testare il flusso in modalità Debug oppure usare "Trigger Now" (Attiva ora) nella pipeline per testare il flusso di dati da un'attività della pipeline.

A questo punto, pianificare e monitorare le attività del flusso di dati usando le pipeline di Azure Data Factory che eseguono l'attività di Flusso di dati.

L'opzione per attivare e disattivare la modalità Debug nell'area di progettazione di Flusso di dati consente di creare trasformazioni di dati in modo interattivo. La modalità di debug fornisce un ambiente di preparazione dati e di anteprima dei dati per la costruzione del flusso di dati.

## <a name="begin-building-your-data-flow-logical-graph"></a>Inizia a creare il grafico logico del flusso di dati

Avviare la creazione di flussi di dati usando il segno + sotto risorse della factory per creare un nuovo flusso di dati.

![nuovo flusso di dati](media/data-flow/newdataflow2.png "nuovo flusso di dati")

Per iniziare, configurare la trasformazione origine e quindi aggiungere la trasformazione dati a ogni passaggio successivo usando il segno +. Quando si compila il grafico logico, è possibile passare da una modalità a un grafico a un'altra usando il pulsante "Mostra grafico" e "Nascondi grafico".

![Mostra grafico](media/data-flow/showg.png "Mostra grafico")

## <a name="configure-transformation-logic"></a>Configurare la logica di trasformazione

![Nascondi grafico](media/data-flow/hideg.png "Nascondi grafico")

Nascondendo il grafo, sarà possibile spostarsi tra i nodi di trasformazione in un secondo momento.

![Esplora](media/data-flow/showhide.png "Esplora")

## <a name="next-steps"></a>Passaggi successivi

* [Inizia con una trasformazione origine](data-flow-source.md)
