---
title: Differenze rispetto a Azure Data Factory
description: Informazioni sul modo in cui le funzionalità di integrazione dei dati di Azure sinapsi Analytics sono diverse da quelle di Azure Data Factory
services: synapse-analytics
author: djpmsft
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 09/23/2020
ms.author: daperlov
ms.reviewer: jrasnick
ms.openlocfilehash: be098977ba51e529aaecfb5dc3b7a03444f55a7e
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91343052"
---
# <a name="data-integration-in-azure-synapse-analytics-versus-azure-data-factory"></a>Integrazione dei dati in Azure sinapsi Analytics rispetto a Azure Data Factory

In Azure sinapsi Analytics le funzionalità di integrazione dei dati, ad esempio i flussi di dati e le pipeline di sinapsi, sono basate su quelle di Azure Data Factory. Per ulteriori informazioni, vedere [che cos'è Azure Data Factory](../../data-factory/introduction.md). Quasi tutte le funzionalità sono identiche o simili e la documentazione viene condivisa tra i due servizi. Questo articolo evidenzia e identifica le differenze attuali tra Azure Data Factory e la sinapsi di Azure.

Per verificare se un Azure Data Factory funzionalità o un articolo si applica a sinapsi di Azure, controllare il moniker nella parte superiore dell'articolo.

![Si applica al moniker](../media/concepts-data-factory-differences/applies-to-moniker.png "Si applica al moniker")

## <a name="features-in-azure-data-factory-not-planned-for-azure-synapse"></a>Funzionalità di Azure Data Factory non pianificate per le sinapsi di Azure

Le funzionalità seguenti sono disponibili in Azure Data Factory, ma non sono pianificate per le sinapsi di Azure.

* Possibilità di spostare i pacchetti SSIS in modalità Lift-and-Shift.
* Fiocco di neve come sink nell'attività di copia e nel flusso di dati di mapping.
* Impostazione della durata (TTL) del flusso di dati del mapping del runtime di integrazione di Azure.

## <a name="azure-synapse-features-not-supported-in-azure-data-factory"></a>Le funzionalità di sinapsi di Azure non sono supportate in Azure Data Factory

Le funzionalità seguenti sono disponibili in sinapsi di Azure, ma non sono pianificate per Azure Data Factory.

* Il monitoraggio del processo Spark per il mapping dei flussi di dati è disponibile solo in sinapsi. In sinapsi, il motore di Spark è contenuto nella sottoscrizione dell'utente, in modo che gli utenti possano visualizzare i log dettagliati di Spark. In Azure Data Factory, l'esecuzione del processo viene eseguita in un cluster Spark gestito da Azure Data Factory. 

## <a name="azure-data-factory-features-that-behave-differently-in-synapse"></a>Azure Data Factory funzionalità che si comportano in modo diverso in sinapsi

Le funzionalità seguenti si comportano in modo diverso o non esistono attualmente in sinapsi di Azure. 

* Litigi dei flussi di dati
* Raccolta di modelli di soluzione
* Integrazione con git e soluzione CI/CD nativa
* Integrazione con monitoraggio di Azure
* Ridenominazione delle risorse dopo la pubblicazione
* Configurazione del runtime di integrazione ibrida in un'area di lavoro sinapsi. Un utente non può avere sia un runtime di integrazione VNet gestito che un Azure IR.
* Condivisione del runtime di integrazione tra le aree di lavoro sinapsi

## <a name="next-steps"></a>Passaggi successivi

Introduzione all'integrazione dei dati nell'area di lavoro sinapsi imparando a [inserire i dati in un account di Azure Data Lake storage Gen2](data-integration-data-lake.md).
