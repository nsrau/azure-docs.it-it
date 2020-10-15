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
ms.date: 10/07/2020
ms.openlocfilehash: d3a5f2bd4bf536c1bc5b3723b9b612beef6a647c
ms.sourcegitcommit: 5abc3919a6b99547f8077ce86a168524b2aca350
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/07/2020
ms.locfileid: "91812319"
---
# <a name="creating-a-synapse-workspace"></a>Creazione di un'area di lavoro di Synapse

In questa esercitazione si apprenderà a creare un'area di lavoro di Synapse, un pool SQL e un pool di Apache Spark. 

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, è necessario avere accesso a un gruppo di risorse per il quale si dispone del ruolo di **Proprietrio**. Creare l'area di lavoro Synapse in questo gruppo di risorse.

## <a name="create-a-synapse-workspace-in-the-azure-portal"></a>Creare un'area di lavoro Synapse nel portale di Azure

1. Aprire il [portale di Azure](https://portal.azure.com) e nella parte superiore cercare **Synapse**.
1. Nei risultati della ricerca, in **Servizi** selezionare **Azure Synapse Analytics (anteprima delle aree di lavoro)** .
1. Selezionare **Aggiungi** per creare un'area di lavoro.
1. In **Nozioni di base** immettere i campi obbligatori e scegliere un nome per l'area di lavoro. In questa esercitazione si userà **myworkspace**.
1. Per creare un'area di lavoro, è necessario un account ADLSGEN2. La scelta più semplice consiste nel crearne una nuova. Se si vuole riutilizzarne una esistente, sarà necessario eseguire alcune operazioni di configurazione aggiuntive. 
1. OPZIONE 1 Creazione di un nuovo account ADLSGEN2 
    1. Passare a **Selezionare Data Lake Storage Gen 2**. 
    1. Fare clic su **Crea nuovo** e scegliere **contosolake** come nome.
    1. Fare clic su **File system** e scegliere **users** come nome. Verrà creato un contenitore denominato **users**
1. OPZIONE 2 Uso di un account ADLSGEN2 esistente. Vedere le istruzioni riportate in **Preparare un account di archiviazione ADLSGEN2** alla fine di questo documento.
1. L'area di lavoro di Azure Synapse userà questo account di archiviazione come account di archiviazione "primario" e il contenitore per archiviare i dati dell'area di lavoro. L'area di lavoro archivia i dati in tabelle Apache Spark. Archivia i log delle applicazioni Spark in una cartella denominata **/synapse/nome_area_di_lavoro**.
1. Selezionare **Rivedi e crea** > **Crea**. L'area di lavoro sarò pronta entro pochi minuti.

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

## <a name="preparing-a-adlsgen2-storage-account"></a>Preparare un account di archiviazione ADLSGEN2

### <a name="perform-the-following-steps-before-you-create-your-workspace"></a>Eseguire i passaggi seguenti PRIMA di creare l'area di lavoro

1. Aprire il [portale di Azure](https://portal.azure.com).
1. Passare all'account di archiviazione esistente
1. Selezionare **Controllo di accesso (IAM)** dal riquadro sinistro. 
1. Assegnare i ruoli seguenti o verificare che siano già assegnati:
    * Assegnare a se stessi al ruolo **Proprietario**.
    * Assegnare a se stessi il ruolo **Proprietario dei dati dei BLOB di archiviazione**.
1. Nel riquadro a sinistra selezionare **Contenitori** e creare un contenitore.
1. È possibile assegnare un nome al contenitore. In questo documento viene usato il nome **users**.
1. Accettare l'impostazione predefinita **Livello di accesso pubblico**, quindi selezionare **Crea**.

### <a name="perform-the-following-steps-after-you-create-your-workspace"></a>Eseguire i passaggi seguenti DOPO aver creato l'area di lavoro

Configurare l'accesso all'account di archiviazione dall'area di lavoro. Le identità gestite per l'area di lavoro di Azure Synapse potrebbero già avere accesso all'account di archiviazione. Per verificare, seguire questa procedura:

1. Aprire il [portale di Azure](https://portal.azure.com) e aprire l'account di archiviazione primario scelto per l'area di lavoro.
1. Selezionare **Controllo di accesso (IAM)** dal riquadro a sinistra.
1. Assegnare i ruoli seguenti o verificare che siano già assegnati. Per l'identità dell'area di lavoro e il nome dell'area di lavoro verrà usato lo stesso nome.
    * Per il ruolo **Collaboratore ai dati dei BLOB di archiviazione** nell'account di archiviazione, assegnare **myworkspace** come identità dell'area di lavoro.
    * Assegnare **myworkspace** come nome dell'area di lavoro.
1. Selezionare **Salva**.


## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Usare un pool SQL per l'analisi](get-started-analyze-sql-pool.md)
