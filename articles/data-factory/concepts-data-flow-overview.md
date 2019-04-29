---
title: Panoramica dei flussi di dati di mapping di Azure Data Factory
description: Una presentazione generale dei flussi di dati di mapping in Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: 5064f47444b4ca6d9194ed66144938e6e3d51a4e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61261949"
---
# <a name="what-are-mapping-data-flows-in-azure-data-factory"></a>Che cosa sono i flussi di dati di mapping in Azure Data Factory?

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

I flussi di dati di mapping consentono ai data engineer di sviluppare graficamente la logica delle trasformazioni dei dati senza scrivere codice. I flussi di dati risultanti vengono eseguiti come attività all'interno delle pipeline di Azure Data Factory usando cluster di Azure Databricks in cui è stato aumentato il numero di istanze.

Lo scopo di Flusso di dati di Azure Data Factory è quello di offrire un'esperienza completamente visiva senza la scrittura di codice. I flussi di dati verranno eseguiti nel cluster di esecuzione per l'elaborazione dei dati in più istanze. Azure Data Factory gestisce automaticamente tutte le attività di conversione del codice, ottimizzazione dei percorsi ed esecuzione dei processi dei flussi di dati.

Per iniziare, creare i flussi di dati in modalità Debug in modo da poter convalidare interattivamente la logica delle trasformazioni. Aggiungere quindi un'attività di Flusso di dati alla pipeline per eseguire e testare il flusso in modalità Debug oppure usare "Trigger Now" (Attiva ora) nella pipeline per testare il flusso di dati da un'attività della pipeline.

A questo punto, pianificare e monitorare le attività del flusso di dati usando le pipeline di Azure Data Factory che eseguono l'attività di Flusso di dati.

L'opzione per attivare e disattivare la modalità Debug nell'area di progettazione di Flusso di dati consente di creare trasformazioni di dati in modo interattivo. La modalità Debug offre un ambiente di preparazione per la creazione di flussi di dati.
