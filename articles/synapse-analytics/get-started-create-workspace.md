---
title: "Esercitazione: Introduzione alla creazione di un'area di lavoro di Synapse"
description: In questa esercitazione si apprenderà a creare un'area di lavoro di Synapse, un pool SQL e un pool di Apache Spark.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.date: 07/20/2020
ms.openlocfilehash: b4d48dcc8f09ae8e2ec3bb198f8864de1c945682
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87093899"
---
# <a name="create-a-synapse-workspace"></a>Creare un'area di lavoro di Synapse

In questa esercitazione si apprenderà a creare un'area di lavoro di Synapse, un pool SQL e un pool di Apache Spark. 

## <a name="prepare-a-storage-account"></a>Preparare un account di archiviazione

1. Aprire il [portale di Azure](https://portal.azure.com).
1. Creare un nuovo account di archiviazione con le impostazioni seguenti:

    |Scheda|Impostazione | Valore consigliato | Descrizione |
    |---|---|---|---|
    |Nozioni di base|**Nome account di archiviazione**| Scegliere qualsiasi nome.| In questo documento si userà il nome **contosolake**.|
    |Nozioni di base|**Tipo di account**| **StorageV2** ||
    |Nozioni di base|**Posizione**|Scegliere la località desiderata.| È consigliabile che l'area di lavoro di Azure Synapse Analytics e l'account Azure Data Lake Storage Gen2 risiedano nella stessa area.|
    |Avanzate|**Data Lake Storage Gen2**|**Enabled**| Azure Synapse funziona solo con account di archiviazione con questa impostazione abilitata.|
    |||||

1. Dopo aver creato l'account di archiviazione, selezionare **Controllo di accesso (IAM)** nel riquadro a sinistra. Assegnare quindi i ruoli seguenti o verificare che siano già assegnati:
    * Assegnare a se stessi al ruolo **Proprietario**.
    * Assegnare a se stessi il ruolo **Proprietario dei dati dei BLOB di archiviazione**.
1. Nel riquadro a sinistra selezionare **Contenitori** e creare un contenitore.
1. È possibile assegnare al contenitore il nome desiderato. In questo documento verrà assegnato il nome **users**.
1. Accettare l'impostazione predefinita **Livello di accesso pubblico**, quindi selezionare **Crea**.

Nel passaggio seguente verranno configurati l'area di lavoro di Azure Synapse per usare questo account di archiviazione come account di archiviazione "primario" e il contenitore per archiviare i dati dell'area di lavoro. L'area di lavoro archivia i dati in tabelle Apache Spark. Archivia i log delle applicazioni Spark in una cartella denominata **/synapse/nome_area_di_lavoro**.

## <a name="create-a-synapse-workspace"></a>Creare un'area di lavoro di Synapse

1. Aprire il [portale di Azure](https://portal.azure.com) e nella parte superiore cercare **Synapse**.
1. Nei risultati della ricerca, in **Servizi** selezionare **Azure Synapse Analytics (anteprima delle aree di lavoro)** .
1. Selezionare **Aggiungi** per creare un'area di lavoro usando queste impostazioni:

    |Scheda|Impostazione | Valore consigliato | Descrizione |
    |---|---|---|---|
    |Nozioni di base|**Nome area di lavoro**|È possibile assegnare un nome qualsiasi.| In questo documento verrà usato il nome **myworkspace**.|
    |Nozioni di base|**Area**|Usare la stessa area dell'account di archiviazione.|

1. In **Selezionare Data Lake Storage Gen 2** selezionare l'account e il contenitore creati in precedenza.
1. Selezionare **Rivedi e crea** > **Crea**. L'area di lavoro sarò pronta entro pochi minuti.

## <a name="verify-access-to-the-storage-account"></a>Verificare l'accesso all'account di archiviazione

Le identità gestite per l'area di lavoro di Azure Synapse potrebbero già avere accesso all'account di archiviazione. Per verificare, seguire questa procedura:

1. Aprire il [portale di Azure](https://portal.azure.com) e aprire l'account di archiviazione primario scelto per l'area di lavoro.
1. Selezionare **Controllo di accesso (IAM)** dal riquadro a sinistra.
1. Assegnare i ruoli seguenti o verificare che siano già assegnati. Per l'identità dell'area di lavoro e il nome dell'area di lavoro verrà usato lo stesso nome.
    * Per il ruolo **Collaboratore ai dati dei BLOB di archiviazione** nell'account di archiviazione, assegnare **myworkspace** come identità dell'area di lavoro.
    * Assegnare **myworkspace** come nome dell'area di lavoro.

1. Selezionare **Salva**.

## <a name="open-synapse-studio"></a>Aprire Synapse Studio

Dopo aver creato l'area di lavoro di Azure Synapse, è possibile aprire Synapse Studio in due modi:

* Aprire l'area di lavoro di Synapse nel [portale di Azure](https://portal.azure.com). Nella parte superiore della sezione **Panoramica** selezionare **Avvia Synapse Studio**.
* Passare a `https://web.azuresynapse.net` e accedere alla propria area di lavoro.

## <a name="create-a-sql-pool"></a>Creare un pool SQL

1. Nel riquadro sinistro di Synapse Studio selezionare **Gestisci** > **Pool SQL**.
1. Selezionare **Nuovo** e immettere le impostazioni seguenti:

    |Impostazione | Valore consigliato | 
    |---|---|---|
    |**Nome del pool SQL**| **SQLDB1**|
    |**Livello di prestazioni**|**DW100C**|
    |||

1. Selezionare **Rivedi e crea** > **Crea**. Il pool SQL è pronto in pochi minuti. Il pool SQL viene associato a un database del pool SQL anch'esso denominato **SQLDB1**.

Un pool SQL utilizza risorse fatturabili finché è attivo. È possibile sospendere il pool in un secondo momento per ridurre i costi.

## <a name="create-an-apache-spark-pool"></a>Creare un pool di Apache Spark

1. Nel riquadro sinistro di Synapse Studio selezionare **Gestisci** > **Pool di Apache Spark**.
1. Selezionare **Nuovo** e immettere le impostazioni seguenti:

    |Impostazione | Valore consigliato | 
    |---|---|---|
    |**Nome del pool di Apache Spark**|**Spark1**
    |**Dimensioni nodo**| **Small**|
    |**Numero di nodi**| Impostare il valore minimo e il valore massimo su 3|

1. Selezionare **Rivedi e crea** > **Crea**. Il pool di Apache Spark è pronto in pochi secondi.

> [!NOTE]
> Nonostante il nome, un pool di Apache Spark non è simile a un pool SQL. Si tratta solo di alcuni metadati di base usati per indicare all'area di lavoro di Azure Synapse come interagire con Spark.

Poiché sono metadati, i pool di Spark non possono essere avviati o arrestati.

Quando si esegue un'attività Spark in Azure Synapse, si specifica un pool di Spark da usare. Il pool indica ad Azure Synapse il numero di risorse Spark da usare. Verranno addebitati i costi solo per le risorse usate. Quando si interrompe attivamente l'uso del pool, le risorse raggiungono automaticamente il timeout e vengono riciclate.

> [!NOTE]
> I database Spark vengono creati in modo indipendente dai pool di Spark. In un'area di lavoro è sempre presente un database Spark **predefinito**. È possibile creare ulteriori database Spark.

## <a name="the-sql-on-demand-pool"></a>Pool SQL su richiesta

Ogni area di lavoro include un pool predefinito denominato **SQL su richiesta**. Questo pool non può essere eliminato. Il pool SQL su richiesta consente di usare SQL senza la necessità di creare o gestire un pool SQL in Azure Synapse.

Diversamente dagli altri tipi di pool, la fatturazione per SQL su richiesta è basata sulla quantità di dati analizzati per eseguire la query e non sul numero di risorse usate per eseguirla.

* SQL su richiesta dispone di database SQL su richiesta indipendenti da qualsiasi pool SQL su richiesta.
* A un'area di lavoro è sempre associato esattamente un pool SQL su richiesta denominato **SQL su richiesta**.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Usare un pool SQL per l'analisi](get-started-analyze-sql-pool.md)
