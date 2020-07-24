---
title: Ridimensionare automaticamente le istanze di Apache Spark
description: Usare la funzionalità di scalabilità automatica di Azure Synapse per dimensionare automaticamente le istanze di Apache Spark
author: euangMS
ms.author: euang
ms.reviewer: euang
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 03/31/2020
ms.openlocfilehash: 7c6278d24e52159424c3e346b2382c93f4ab8160
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87075793"
---
# <a name="automatically-scale-azure-synapse-analytics-apache-spark-pools"></a>Dimensionare automaticamente i pool di Apache Spark in Azure Synapse Analytics

La funzionalità di scalabilità automatica del pool di Apache Spark per Azure Synapse Analytics consente di aumentare o ridurre automaticamente il numero di nodi in un istanza del cluster. Durante la creazione di un nuovo pool di Apache Spark per Azure Synapse Analytics è possibile impostare un numero minimo e massimo di nodi quando si seleziona la funzionalità di scalabilità automatica. La funzionalità di scalabilità automatica monitora quindi i requisiti del carico in termini di risorse e aumenta o riduce il numero di nodi. Per l'uso di questa funzionalità non sono previsti costi aggiuntivi.

## <a name="metrics-monitoring"></a>Monitoraggio delle metriche

La funzionalità di scalabilità automatica monitora continuamente l'istanza di Spark e raccoglie le metriche seguenti:

|Metrica|Descrizione|
|---|---|
|Total Pending CPU (Totale CPU in sospeso)|Numero totale di core necessari per avviare l'esecuzione di tutti i nodi in sospeso.|
|Total Pending Memory (Totale memoria in sospeso)|Memoria totale (in MB) necessaria per avviare l'esecuzione di tutti i nodi in sospeso.|
|Total Free CPU (Totale CPU disponibile)|Somma di tutti i core inutilizzati nei nodi attivi.|
|Total Free Memory (Totale memoria disponibile)|Somma della memoria inutilizzata (in MB) nei nodi attivi.|
|Used Memory per Node (Memoria utilizzata per nodo)|Carico su un nodo. Un nodo in cui sono usati 10 GB di memoria è considerato come sottoposto a un carico superiore rispetto a un nodo del ruolo di lavoro con 2 GB di memoria usata.|

Le metriche elencate in precedenza vengono controllate ogni 30 secondi. La funzionalità di scalabilità automatica si basa su queste metriche per decidere se aumentare o ridurre il numero di nodi.

## <a name="load-based-scale-conditions"></a>Condizioni di scalabilità in base al carico

Quando vengono rilevate le condizioni seguenti, la funzionalità di scalabilità automatica invia una richiesta di aumento o riduzione delle dimensioni:

|Aumentare|Riduzione|
|---|---|
|Il totale CPU in sospeso è maggiore del totale CPU disponibile per più di 1 minuto.|Il totale CPU in sospeso è minore del totale CPU disponibile per più di 2 minuti.|
|Il totale memoria in sospeso è maggiore del totale memoria disponibile per più di 1 minuto.|Il totale memoria in sospeso è minore del totale memoria disponibile per più di 2 minuti.|

Per aumentare il numero di nodi, il servizio di scalabilità automatica di Azure Synapse calcola il numero di nuovi nodi necessari per soddisfare i requisiti correnti in termini di CPU e memoria e quindi invia una richiesta di aumento dei nodi per aggiungere il numero di nodi necessario.

Per ridurre il numero di nodi, in base al numero di executor, ai master applicazioni per nodo e ai requisiti correnti in termini di CPU e memoria, il servizio di scalabilità automatica invia una richiesta per la rimozione di un determinato numero di nodi. Il servizio rileva anche i nodi candidati per la rimozione in base all'esecuzione del processo corrente. L'operazione di riduzione prevede prima la disattivazione e quindi il ritiro dei nodi dal cluster.

## <a name="get-started"></a>Introduzione

### <a name="create-a-spark-pool-with-autoscaling"></a>Creare un pool di Spark con scalabilità automatica

Per abilitare la funzionalità di scalabilità automatica, completare la procedura seguente durante il normale processo di creazione del pool:

1. Nella scheda **Informazioni di base** selezionare la casella di controllo **Abilita scalabilità automatica**.
1. Immettere i valori desiderati per le proprietà seguenti:  

    * Numero **minimo** di nodi.
    * Numero **massimo** di nodi.

Il numero iniziale di nodi corrisponderà al numero minimo. Questo valore definisce le dimensioni iniziali dell'istanza al momento della creazione. Il numero minimo di nodi non può essere inferiore a tre.

## <a name="best-practices"></a>Procedure consigliate

### <a name="consider-the-latency-of-scale-up-or-scale-down-operations"></a>Considerare la latenza delle operazioni di aumento o riduzione

Il completamento di un'operazione di ridimensionamento può richiedere da 1 a 5 minuti.

### <a name="preparation-for-scaling-down"></a>Preparazione per la riduzione

Durante il processo di riduzione delle istanze, la funzionalità di scalabilità automatica imposterà i nodi nello stato di disattivazione in modo che non sia possibile avviare nuovi executor su tale nodo.

I processi in esecuzione continueranno a essere eseguiti e verranno completati. I processi in sospeso rimarranno in attesa di essere pianificati come normali con un numero minore di nodi disponibili.

## <a name="next-steps"></a>Passaggi successivi

Avvio rapido per creare un nuovo pool di Spark: [Creare un pool di Spark](../quickstart-create-apache-spark-pool-portal.md)
