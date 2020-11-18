---
title: "Avvio rapido: Introduzione alla creazione di un'area di lavoro di Synapse"
description: In questa esercitazione si apprenderà a creare un'area di lavoro di Synapse, un pool SQL dedicato e un pool di Apache Spark serverless.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: workspace
ms.topic: tutorial
ms.date: 10/07/2020
ms.openlocfilehash: f7b96bcebb2106e52c62426ca2b64f9305e09141
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94515410"
---
# <a name="creating-a-synapse-workspace"></a>Creazione di un'area di lavoro di Synapse

In questa esercitazione si apprenderà a creare un'area di lavoro di Synapse, un pool SQL dedicato e un pool di Apache Spark serverless. 

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, è necessario avere accesso a un gruppo di risorse per il quale si dispone del ruolo di **Proprietario**. Creare l'area di lavoro Synapse in questo gruppo di risorse.

## <a name="create-a-synapse-workspace-in-the-azure-portal"></a>Creare un'area di lavoro Synapse nel portale di Azure

1. Aprire il [portale di Azure](https://portal.azure.com) e nella parte superiore cercare **Synapse**.
1. Nei risultati della ricerca, in **Servizi** selezionare **Azure Synapse Analytics (anteprima delle aree di lavoro)** .
1. Selezionare **Aggiungi** per creare un'area di lavoro.
1. In **Informazioni di base** immettere le proprie preferenze in **Sottoscrizione**, **Gruppo di risorse** e **Area**, quindi scegliere un nome di area di lavoro. In questa esercitazione si userà **myworkspace**.
1. Passare a **Selezionare Data Lake Storage Gen 2**. 
1. Fare clic su **Crea nuovo** e scegliere **contosolake** come nome.
1. Fare clic su **File system** e scegliere **users** come nome. Verrà creato un contenitore denominato **users**
1. L'area di lavoro userà questo account di archiviazione come account di archiviazione "primario" per le tabelle Spark e i log delle applicazioni Spark.
1. Selezionare **Rivedi e crea** > **Crea**. L'area di lavoro sarò pronta entro pochi minuti.

## <a name="open-synapse-studio"></a>Aprire Synapse Studio

Dopo aver creato l'area di lavoro di Azure Synapse, è possibile aprire Synapse Studio in due modi:

* Aprire l'area di lavoro di Synapse nel [portale di Azure](https://portal.azure.com). Nella parte superiore della sezione **Panoramica** selezionare **Avvia Synapse Studio**.
* Passare a `https://web.azuresynapse.net` e accedere alla propria area di lavoro.

## <a name="create-a-dedicated-sql-pool"></a>Creare un pool SQL dedicato

1. Nel riquadro sinistro di Synapse Studio selezionare **Gestisci** > **Pool SQL**.
1. Selezionare **Nuovo** e immettere le impostazioni seguenti:

    |Impostazione | Valore consigliato | 
    |---|---|---|
    |**Nome del pool SQL**| **SQLDB1**|
    |**Livello di prestazioni**|**DW100C**|
    |||

1. Selezionare **Rivedi e crea** > **Crea**. Il pool SQL dedicato sarà pronto in pochi minuti. Il pool SQL dedicato è associato a un database del pool SQL dedicato anch'esso denominato **SQLDB1**.

Un pool SQL dedicato utilizza risorse fatturabili finché è attivo. È possibile sospendere il pool in un secondo momento per ridurre i costi.

## <a name="create-a-serverless-apache-spark-pool"></a>Creare un pool di Apache Spark serverless

1. Nel riquadro sinistro di Synapse Studio selezionare **Gestisci** > **Pool di Apache Spark**.
1. Selezionare **Nuovo** e immettere le impostazioni seguenti:

    |Impostazione | Valore consigliato | 
    |---|---|---|
    |**Nome del pool di Apache Spark**|**Spark1**
    |**Dimensioni nodo**| **Small**|
    |**Numero di nodi**| Impostare il valore minimo e il valore massimo su 3|

1. Selezionare **Rivedi e crea** > **Crea**. Il pool di Apache Spark è pronto in pochi secondi.

Quando si esegue un'attività Spark in Azure Synapse, si specifica un pool di Spark da usare. Il pool indica ad Azure Synapse il numero di risorse Spark da usare. Verranno addebitati i costi solo per le risorse usate. Quando si interrompe attivamente l'uso del pool, le risorse raggiungono automaticamente il timeout e vengono riciclate.

> [!NOTE]
> I database Spark vengono creati in modo indipendente dai pool di Spark. In un'area di lavoro è sempre presente un database Spark **predefinito**. È possibile creare ulteriori database Spark.

## <a name="the-serverless-sql-pool"></a>Pool SQL serverless

Ogni area di lavoro include un pool predefinito denominato **Predefinito**. Questo pool non può essere eliminato. Il pool SQL serverless consente di usare SQL senza la necessità di creare o gestire un pool SQL serverless in Azure Synapse. Diversamente dai pool SQL dedicati, la fatturazione per un pool SQL serverless è basata sulla quantità di dati analizzati per eseguire la query e non sul numero di risorse usate per eseguirla.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Eseguire analisi con un pool SQL dedicato](get-started-analyze-sql-pool.md)
