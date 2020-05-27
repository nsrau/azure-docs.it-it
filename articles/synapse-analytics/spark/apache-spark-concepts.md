---
title: Apache Spark in Azure Synapse Analytics - Concetti principali
description: Questo articolo fornisce un'introduzione ad Apache Spark in Azure Synapse Analytics e presenta i diversi concetti.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 6276d198e547efec3d2e3cb88816da5e2b593aae
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83644693"
---
# <a name="apache-spark-in-azure-synapse-analytics-core-concepts"></a>Concetti principali su Apache Spark in Azure Synapse Analytics

Apache Spark è un framework di elaborazione parallela che supporta l'elaborazione in memoria per migliorare le prestazioni di applicazioni analitiche di Big Data. Apache Spark in Azure Synapse Analytics è una delle implementazioni Microsoft di Apache Spark nel cloud. 

Azure Synapse semplifica la creazione e la configurazione delle funzionalità di Spark in Azure. Azure Synapse fornisce un'implementazione diversa di queste funzionalità Spark documentate qui.

## <a name="spark-pools-preview"></a>Pool di Spark (anteprima)

Un pool di Spark (anteprima) viene creato nel portale di Azure. È la definizione di un pool di Spark che, quando ne viene creata un'istanza, viene usata per creare un'istanza di Spark che elabora i dati. Quando viene creato, un pool di Spark esiste solo in forma di metadati. Non viene utilizzata, eseguita o addebitata alcuna risorsa. Un pool di Spark ha una serie di proprietà che controllano le caratteristiche di un'istanza di Spark. Queste caratteristiche includono, tra le altre, il nome, la dimensione, il comportamento di ridimensionamento e la durata (TTL).

Poiché la creazione di pool di Spark non comporta costi o utilizzo di risorse, è possibile crearne un numero illimitato con tutte le configurazioni diverse necessarie. Ai pool di Spark possono anche essere applicate autorizzazioni che consentono agli utenti di accedere solo a determinati pool.

Come procedura consigliata, creare pool di Spark di dimensioni inferiori che possano essere usati per lo sviluppo e il debug e quindi pool più grandi per l'esecuzione di carichi di lavoro di produzione.

Per informazioni su come creare un pool di Spark e visualizzarne tutte le proprietà, vedere [Creare un pool di Apache Spark (anteprima) in Synapse Analytics usando gli strumenti Web](../quickstart-create-apache-spark-pool-portal.md).

## <a name="spark-instances"></a>Istanze di Spark

Le istanze di Spark vengono create quando ci si connette a un pool di Spark, si crea una sessione e si esegue un processo. Dato che più utenti possono avere accesso a un singolo pool di Spark, viene creata una nuova istanza di Spark per ogni utente che si connette. 

Quando si invia un secondo processo, se il pool ha capacità anche l'istanza di Spark esistente avrà capacità, quindi l'istanza esistente elaborerà il processo. In caso contrario e se esiste capacità a livello di pool, verrà creata una nuova istanza di Spark.

## <a name="examples"></a>Esempi

### <a name="example-1"></a>Esempio 1

- Creare un pool di Spark denominato SP1, con una dimensione del cluster predefinita di 20 nodi.
- Inviare un processo notebook, J1, che usa 10 nodi. Viene creata l'istanza di Spark SI1 per elaborare il processo.
- Inviare ora un altro processo, J2, che usa 10 nodi perché c'è ancora capacità nel pool. L'istanza, J2, viene elaborata da SI1.
- Se J2 avesse richiesto 11 nodi, non ci sarebbe stata capacità in SP1 o SI1. In questo caso, se J2 proviene da un notebook, il processo viene rifiutato. Se J2 proviene da un processo batch, viene accodato.

### <a name="example-2"></a>Esempio 2

- Creare un pool di Spark denominato SP2, con una scalabilità automatica di 10-20 nodi abilitata
- Inviare un processo notebook, J1, che usa 10 nodi. Viene creata l'istanza di Spark SI1 per elaborare il processo.
- Inviare ora un altro processo, J2, che usa 10 nodi perché c'è ancora capacità nel pool. L'istanza aumenta automaticamente a 20 nodi ed elabora J2.

### <a name="example-3"></a>Esempio 3

- Creare un pool di Spark denominato SP1, con una dimensione del cluster predefinita di 20 nodi.
- Inviare un processo notebook, J1, che usa 10 nodi. Viene creata l'istanza di Spark SI1 per elaborare il processo.
- Un altro utente, U2, invia un processo, J3, che usa 10 nodi. Viene creata una nuova istanza di Spark, SI2, per elaborare il processo.
- Inviare ora un altro processo, J2, che usa 10 nodi perché c'è ancora capacità nel pool. L'istanza, J2, viene elaborata da SI1.

## <a name="next-steps"></a>Passaggi successivi

- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Documentazione di Apache Spark](https://spark.apache.org/docs/2.4.4/)
