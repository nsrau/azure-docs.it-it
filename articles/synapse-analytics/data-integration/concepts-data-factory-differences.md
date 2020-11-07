---
title: Differenze rispetto ad Azure Data Factory
description: Informazioni sul modo in cui le funzionalità di integrazione dei dati di Azure sinapsi Analytics sono diverse da quelle di Azure Data Factory
services: synapse-analytics
author: djpmsft
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 11/06/2020
ms.author: daperlov
ms.reviewer: jrasnick
ms.openlocfilehash: 10f5336dd4c8a02acf623b1b14226ca676006953
ms.sourcegitcommit: 0b9fe9e23dfebf60faa9b451498951b970758103
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2020
ms.locfileid: "94357650"
---
# <a name="data-integration-in-azure-synapse-analytics-versus-azure-data-factory"></a>Integrazione dei dati in Azure sinapsi Analytics rispetto a Azure Data Factory

In Azure sinapsi Analytics le funzionalità di integrazione dei dati, ad esempio i flussi di dati e le pipeline di sinapsi, sono basate su quelle di Azure Data Factory. Per ulteriori informazioni, vedere [che cos'è Azure Data Factory](../../data-factory/introduction.md). Quasi tutte le funzionalità sono identiche o simili e la documentazione viene condivisa tra i due servizi. Questo articolo evidenzia e identifica le differenze attuali tra Azure Data Factory e la sinapsi di Azure.

Per verificare se un Azure Data Factory funzionalità o un articolo si applica a sinapsi di Azure, controllare il moniker nella parte superiore dell'articolo.

![Si applica al moniker](../media/concepts-data-factory-differences/applies-to-moniker.png "Si applica al moniker")

## <a name="features-in-azure-data-factory-not-planned-for-azure-synapse"></a>Funzionalità di Azure Data Factory non pianificate per le sinapsi di Azure

Le funzionalità seguenti sono disponibili in Azure Data Factory, ma non sono pianificate per le sinapsi di Azure.

* **Pacchetti SSIS di durata e spostamento:** In Azure Data Factory è possibile trasferire e spostare i pacchetti SSIS tramite il runtime di integrazione SSIS. Sia il runtime di integrazione SSIS che l'attività Esegui pacchetto SSIS non sono disponibili nelle aree di lavoro sinapsi. 
* **Durata (TTL):** La durata (TTL) è un'impostazione nel runtime di integrazione di Azure che consente al cluster Spark di eseguire il mapping dei flussi di dati per restare *caldi* per un certo periodo di tempo dopo il completamento di un flusso di dati. Questa funzionalità non è disponibile nelle aree di lavoro sinapsi.

## <a name="azure-synapse-features-not-supported-in-azure-data-factory"></a>Le funzionalità di sinapsi di Azure non sono supportate in Azure Data Factory

Le funzionalità seguenti sono disponibili in sinapsi di Azure, ma non sono pianificate per Azure Data Factory.

* **Monitoraggio del processo Spark per il mapping dei flussi di dati:** In sinapsi, il motore di Spark è contenuto nella sottoscrizione dell'utente, in modo che gli utenti possano visualizzare i log dettagliati di Spark. In Azure Data Factory, l'esecuzione del processo viene eseguita in un cluster Spark gestito da Azure Data Factory e queste informazioni non sono disponibili. 

## <a name="azure-data-factory-features-that-behave-differently-in-synapse"></a>Azure Data Factory funzionalità che si comportano in modo diverso in sinapsi

Le funzionalità seguenti si comportano in modo diverso o non esistono attualmente in sinapsi di Azure. 

* **Litigi dei flussi di dati:** L'attività flusso di dati in corso è disponibile solo in Azure Data Factory al momento.
* **Raccolta di modelli di soluzione:** In Azure Data Factory gli utenti possono trovare modelli di pipeline nella raccolta di modelli di soluzione. Nelle aree di lavoro sinapsi, il centro informazioni contiene un set di modelli diverso insieme a set di dati aggiuntivi e script SQL. 
* **Integrazione con git e soluzione ci/CD nativa:** Attualmente un'area di lavoro sinapsi non è in grado di connettersi a un repository git né segue lo stesso processo di integrazione e recapito continuo come Azure Data Factory.
* **Integrazione con monitoraggio di Azure:** Le aree di lavoro sinapsi non si integrano con monitoraggio di Azure come Azure Data Factory.
* **Configurazione del runtime di integrazione ibrida:** All'interno di un'area di lavoro sinapsi, un utente non può avere sia un runtime di integrazione VNet gestito che un Azure IR. Questa funzionalità è supportata in Azure Data Factory.
* **Condivisione del runtime di integrazione:** I runtime di integrazione self-hosted non possono essere condivisi tra le aree di lavoro sinapsi. Questa funzionalità è supportata in Azure Data Factory.
* **Runtime di integrazione tra aree per i flussi di dati:** I flussi di dati non possono essere eseguiti in runtime di integrazione in aree diverse rispetto a un'area di lavoro sinapsi. Questa funzionalità è supportata in Azure Data Factory.

## <a name="next-steps"></a>Passaggi successivi

Introduzione all'integrazione dei dati nell'area di lavoro sinapsi imparando a [inserire i dati in un account di Azure Data Lake storage Gen2](data-integration-data-lake.md).
