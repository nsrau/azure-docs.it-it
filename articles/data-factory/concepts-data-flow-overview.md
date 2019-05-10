---
title: Panoramica dei flussi di dati di mapping di Azure Data Factory
description: Una presentazione generale dei flussi di dati di mapping in Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/31/2019
ms.openlocfilehash: 051886f98d6d35594336291bbb2defb2a4acdfc5
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/07/2019
ms.locfileid: "65233060"
---
# <a name="what-are-mapping-data-flows"></a>Che cosa sono i Mapping di flusso dei dati?

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Flussi di dati di mapping sono trasformazione progettata in modo visivo dei dati in Azure Data Factory. I flussi di dati permettono agli ingegneri dei dati sviluppare la logica di trasformazione dati grafici senza scrivere codice. I flussi di dati risultanti vengono eseguiti come attività all'interno delle pipeline di Azure Data Factory usando cluster di Azure Databricks in cui è stato aumentato il numero di istanze.

Lo scopo di Flusso di dati di Azure Data Factory è quello di offrire un'esperienza completamente visiva senza la scrittura di codice. I flussi di dati verranno eseguiti nel cluster di esecuzione per l'elaborazione dei dati in più istanze. Azure Data Factory gestisce automaticamente tutte le attività di conversione del codice, ottimizzazione dei percorsi ed esecuzione dei processi dei flussi di dati.

Per iniziare, creare i flussi di dati in modalità Debug in modo da poter convalidare interattivamente la logica delle trasformazioni. Aggiungere quindi un'attività di Flusso di dati alla pipeline per eseguire e testare il flusso in modalità Debug oppure usare "Trigger Now" (Attiva ora) nella pipeline per testare il flusso di dati da un'attività della pipeline.

A questo punto, pianificare e monitorare le attività del flusso di dati usando le pipeline di Azure Data Factory che eseguono l'attività di Flusso di dati.

L'opzione per attivare e disattivare la modalità Debug nell'area di progettazione di Flusso di dati consente di creare trasformazioni di dati in modo interattivo. La modalità Debug offre un ambiente di preparazione per la creazione di flussi di dati.
