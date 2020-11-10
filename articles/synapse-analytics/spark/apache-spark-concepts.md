---
title: Concetti principali di Apache Spark
description: Introduzione ai concetti principali di Apache Spark in Azure Synapse Analytics.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: d29fa6454839b0c34830374282b8abb5fa6e4c51
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93313405"
---
# <a name="apache-spark-in-azure-synapse-analytics-core-concepts"></a>Concetti principali su Apache Spark in Azure Synapse Analytics

Apache Spark è un framework di elaborazione parallela che supporta l'elaborazione in memoria per migliorare le prestazioni di applicazioni analitiche di Big Data. Apache Spark in Azure Synapse Analytics è una delle implementazioni Microsoft di Apache Spark nel cloud. 

Azure Synapse semplifica la creazione e la configurazione delle funzionalità di Spark in Azure. Azure Synapse fornisce un'implementazione diversa di queste funzionalità Spark documentate qui.

## <a name="spark-pools-preview"></a>Pool di Spark (anteprima)

Un pool di Apache Spark serverless (anteprima) viene creato nel portale di Azure. È la definizione di un pool di Spark che, in caso di creazione di istanze, viene usata per creare un'istanza di Spark che elabora i dati. Quando viene creato, un pool di Spark esiste solo in forma di metadati e non viene utilizzata, eseguita o addebitata alcuna risorsa. Un pool di Spark ha una serie di proprietà che controllano le caratteristiche di un'istanza di Spark. Queste caratteristiche includono, in via esemplificativa, un nome, le dimensioni, il comportamento di scalabilità e la durata (TTL).

Poiché la creazione di pool di Spark non comporta costi o utilizzo di risorse, è possibile crearne un numero illimitato con tutte le configurazioni diverse necessarie. Ai pool di Spark possono anche essere applicate autorizzazioni che consentono agli utenti di accedere solo a determinati pool.

Come procedura consigliata, creare pool di Spark di dimensioni inferiori che possano essere usati per lo sviluppo e il debug e quindi pool più grandi per l'esecuzione di carichi di lavoro di produzione.

Per informazioni su come creare un pool di Spark e visualizzarne tutte le proprietà, vedere [Creare un pool di Apache Spark (anteprima) in Synapse Analytics usando gli strumenti Web](../quickstart-create-apache-spark-pool-portal.md).

## <a name="spark-instances"></a>Istanze di Spark

Le istanze di Spark vengono create quando ci si connette a un pool di Spark, si crea una sessione e si esegue un processo. Dato che più utenti possono avere accesso a un singolo pool di Spark, viene creata una nuova istanza di Spark per ogni utente che si connette. 

Quando si invia un secondo processo, se è disponibile capacità nel pool, anche l'istanza di Spark esistente avrà la capacità richiesta. Quindi, l'istanza esistente elaborerà il processo. In alternativa, se la capacità è disponibile a livello di pool, verrà creata una nuova istanza di Spark.

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
- Inviare ora un altro processo, J2, che usa 10 nodi perché nel pool è ancora disponibile capacità. L'istanza, J2, viene elaborata da SI1.

## <a name="quotas-and-resource-constraints-in-apache-spark-for-azure-synapse"></a>Quote e vincoli delle risorse in Apache Spark per Azure Synapse

### <a name="workspace-level"></a>Livello di area di lavoro

Ogni area di lavoro di Azure Synapse dispone di una quota predefinita di vCore che possono essere usati per Spark. La quota è suddivisa tra quota utente e quota del flusso di dati, in modo che nessuno dei due modelli di utilizzo usi tutti i vCore dell'area di lavoro. La quota varia a seconda del tipo di sottoscrizione, ma è divisa in modo simmetrico tra utente e flusso di lavoro. Tuttavia, se si richiedono più vCore di quanti ne rimangono nell'area di lavoro, si riceve il messaggio di errore seguente:

```console
Failed to start session: [User] MAXIMUM_WORKSPACE_CAPACITY_EXCEEDED
Your Spark job requested 480 vcores.
However, the workspace only has xxx vcores available out of quota of yyy vcores.
Try reducing the numbers of vcores requested or increasing your vcore quota. Click here for more information - https://go.microsoft.com/fwlink/?linkid=213499
```

Il collegamento nel messaggio rimanda a questo articolo.

L'articolo seguente spiega come richiedere un aumento della quota di vCore dell'area di lavoro.

- Selezionare "Azure Synapse Analytics" come tipo di servizio.
- Nella finestra Dettagli quota selezionare Apache Spark (vCore) per area di lavoro

[Richiedere un aumento di capacità tramite il portale di Azure](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests#request-a-standard-quota-increase-from-help--support)

### <a name="spark-pool-level"></a>Livello di pool di Spark

Quando si definisce un pool di Spark si definisce in effetti una quota per utente per tale pool. Se si eseguono più notebook o processi o una combinazione dei due, è possibile esaurire la quota del pool. In tal caso, viene generato un messaggio di errore simile al seguente:

```console
Failed to start session: Your Spark job requested xx vcores.
However, the pool is consuming yy vcores out of available zz vcores.Try ending the running job(s) in the pool, reducing the numbers of vcores requested, increasing the pool maximum size or using another pool
```

Per risolvere il problema, è necessario ridurre l'utilizzo delle risorse del pool prima di inviare una nuova richiesta di risorse eseguendo un notebook o un processo.

## <a name="next-steps"></a>Passaggi successivi

- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Documentazione di Apache Spark](https://spark.apache.org/docs/2.4.5/)
