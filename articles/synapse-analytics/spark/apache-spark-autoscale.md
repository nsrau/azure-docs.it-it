---
title: Ridimensionare automaticamente le istanze di Apache Spark sinapsi di Azure
description: Usare la funzionalità di scalabilità automatica di Azure sinapsi per ridimensionare automaticamente le istanze di Apache Spark
author: euangMS
ms.author: euang
ms.reviewer: euang
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 03/31/2020
ms.openlocfilehash: a2f907384326aa887c12c293feb8c988f42bbaf1
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83210514"
---
# <a name="automatically-scale-azure-synapse-analytics-apache-spark-pools"></a>Ridimensionare automaticamente i pool di Apache Spark di Azure sinapsi Analytics

La funzionalità di scalabilità automatica del pool di Azure sinapsi di Azure consente di ridimensionare automaticamente il numero di nodi in un'istanza del cluster. Durante la creazione di un nuovo pool di Azure sinapsi Spark, è possibile impostare un numero minimo e massimo di nodi quando si seleziona ridimensionamento automatico. La scalabilità automatica monitora quindi i requisiti di risorse del carico e ridimensiona il numero di nodi verso l'alto o verso il basso. Non sono previsti costi aggiuntivi per questa funzionalità.

## <a name="metrics-monitoring"></a>Monitoraggio delle metriche

La scalabilità automatica monitora costantemente l'istanza di Spark e raccoglie le metriche seguenti:

|Metrica|Descrizione|
|---|---|
|CPU totale in sospeso|Il numero totale di core necessari per avviare l'esecuzione di tutti i nodi in sospeso.|
|Memoria totale in sospeso|Memoria totale (in MB) necessaria per avviare l'esecuzione di tutti i nodi in sospeso.|
|Totale CPU disponibile|Somma di tutti i core inutilizzati nei nodi attivi.|
|Memoria totale libera|Somma della memoria inutilizzata (in MB) sui nodi attivi.|
|Memoria usata per nodo|Il carico su un nodo. Un nodo in cui vengono usati 10 GB di memoria viene considerato sotto un carico maggiore rispetto a un ruolo di lavoro con 2 GB di memoria utilizzata.|

Le metriche sopra elencate vengono controllate ogni 30 secondi. La scalabilità automatica semplifica le decisioni di scalabilità verticale e verticale in base a queste metriche.

## <a name="load-based-scale-conditions"></a>Condizioni di scalabilità basata sul carico

Quando vengono rilevate le condizioni seguenti, la scalabilità automatica emette una richiesta di scalabilità:

|Aumentare|Ridimensionamento|
|---|---|
|Il totale CPU in sospeso è maggiore del totale CPU disponibile per più di 1 minuto.|Il totale della CPU in sospeso è inferiore alla CPU gratuita totale per più di 2 minuti.|
|Il totale memoria in sospeso è maggiore del totale memoria disponibile per più di 1 minuto.|Il totale della memoria in sospeso è inferiore alla memoria totale disponibile per più di 2 minuti.|

Per la scalabilità verticale, il servizio di scalabilità automatica di Azure sinapsi calcola il numero di nuovi nodi necessari per soddisfare i requisiti di CPU e memoria correnti, quindi emette una richiesta di scalabilità verticale per aggiungere il numero di nodi necessario.

Per il ridimensionamento, in base al numero di esecutori, ai Master applicazioni per nodo e ai requisiti di memoria e CPU correnti, la scalabilità automatica invia una richiesta di rimozione di un determinato numero di nodi. Il servizio rileva anche quali nodi sono candidati per la rimozione in base all'esecuzione del processo corrente. L'operazione di riduzione delle prestazioni consente innanzitutto di rimuovere le autorizzazioni dei nodi e quindi di rimuoverli dal cluster.

## <a name="get-started"></a>Introduzione

### <a name="create-a-spark-pool-with-autoscaling"></a>Creare un pool Spark con scalabilità automatica

Per abilitare la funzionalità di scalabilità automatica, completare i passaggi seguenti come parte del normale processo di creazione del pool:

1. Nella scheda **nozioni di base** , selezionare la casella di controllo **Abilita scalabilità** automatica.
1. Immettere i valori desiderati per le proprietà seguenti:  

    * Numero **minimo** di nodi.
    * Numero **massimo** di nodi.

Il numero iniziale di nodi sarà il minimo. Questo valore definisce la dimensione iniziale dell'istanza quando viene creata. Il numero minimo di nodi non può essere minore di tre.

## <a name="best-practices"></a>Procedure consigliate

### <a name="consider-the-latency-of-scale-up-or-scale-down-operations"></a>Prendere in considerazione la latenza delle operazioni di scalabilità verticale o verticale

Il completamento di un'operazione di ridimensionamento può richiedere da 1 a 5 minuti.

### <a name="preparation-for-scaling-down"></a>Preparazione per la scalabilità verticale

Durante il processo di riduzione delle istanze, la scalabilità automatica inserirà i nodi nello stato di rimozione delle autorizzazioni in modo che non sia possibile avviare nuovi esecutori su tale nodo.

I processi in esecuzione continueranno a essere eseguiti e completati. I processi in sospeso rimarranno in attesa di essere pianificati come normali con un minor numero di nodi disponibili.

## <a name="next-steps"></a>Passaggi successivi

Guida introduttiva per configurare un nuovo pool Spark [creare un pool Spark](..\quickstart-create-apache-spark-pool.md)
