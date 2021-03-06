---
title: "Avvio rapido: Creare un'area di lavoro di Synapse"
description: Creare un'area di lavoro di Synapse seguendo la procedura descritta in questa guida.
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: workspace
ms.date: 09/03/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: fa7faf64e1889ef2d1abe644f23f0be363709a6b
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93322725"
---
# <a name="quickstart-create-a-synapse-workspace"></a>Avvio rapido: Creare un'area di lavoro di Synapse
Questo argomento di avvio rapido descrive la procedura per creare un'area di lavoro di Azure Synapse Analytics con il portale di Azure.

## <a name="create-a-synapse-workspace"></a>Creare un'area di lavoro di Synapse

1. Aprire il [portale di Azure](https://portal.azure.com) e nella parte superiore cercare **Synapse**.
1. Nei risultati della ricerca, in **Servizi** selezionare **Azure Synapse Analytics (anteprima delle aree di lavoro)** .
1. Selezionare **Aggiungi** per creare un'area di lavoro.
1. Nella scheda **Informazioni di base** assegnare un nome univoco all'area di lavoro. In questo documento si userà il nome **myworkspace**.
1. Per creare un'area di lavoro, è necessario un account ADLSGEN2. La soluzione più semplice consiste nel crearne una nuova. Se si vuole riutilizzarne una esistente, sarà necessario eseguire alcune operazioni di configurazione aggiuntive. 
1. OPZIONE 1 Creazione di un nuovo account ADLSGEN2 
    1. In **Seleziona account Data Lake Storage Gen2** fare clic su **Crea nuovo** e denominarlo **contosolake**.
    1. In **Seleziona account Data Lake Storage Gen2** fare clic su **File system** e denominarlo **users**.
1. OPZIONE 2 Vedere le istruzioni in **Preparare un account di archiviazione** nella parte inferiore di questo documento.
1. L'area di lavoro di Azure Synapse userà questo account di archiviazione come account di archiviazione "primario" e il contenitore per archiviare i dati dell'area di lavoro. L'area di lavoro archivia i dati in tabelle Apache Spark. Archivia i log delle applicazioni Spark in una cartella denominata **/synapse/nome_area_di_lavoro**.
1. Selezionare **Rivedi e crea** > **Crea**. L'area di lavoro sarò pronta entro pochi minuti.

## <a name="open-synapse-studio"></a>Aprire Synapse Studio

Dopo aver creato l'area di lavoro di Azure Synapse, è possibile aprire Synapse Studio in due modi:

* Aprire l'area di lavoro di Synapse nel [portale di Azure](https://portal.azure.com). Nella parte superiore della sezione **Panoramica** selezionare **Avvia Synapse Studio**.
* Passare a `https://web.azuresynapse.net` e accedere alla propria area di lavoro.

## <a name="prepare-an-existing-storage-account-for-use-with-synapse-analytics"></a>Preparare un account di archiviazione esistente per l'uso con Synapse Analytics

1. Aprire il [portale di Azure](https://portal.azure.com).
1. Passare a un account di archiviazione ADLSGEN2 esistente
1. Selezionare **Controllo di accesso (IAM)** dal riquadro sinistro. Assegnare quindi i ruoli seguenti o verificare che siano già assegnati:
    * Assegnare a se stessi al ruolo **Proprietario**.
    * Assegnare a se stessi il ruolo **Proprietario dei dati dei BLOB di archiviazione**.
1. Nel riquadro a sinistra selezionare **Contenitori** e creare un contenitore.
1. È possibile assegnare al contenitore il nome desiderato. In questo documento verrà assegnato il nome **users**.
1. Accettare l'impostazione predefinita **Livello di accesso pubblico** , quindi selezionare **Crea**.

### <a name="configure-access-to-the-storage-account-from-your-workspace"></a>Configurare l'accesso all'account di archiviazione dall'area di lavoro

Le identità gestite per l'area di lavoro di Azure Synapse potrebbero già avere accesso all'account di archiviazione. Per verificare, seguire questa procedura:

1. Aprire il [portale di Azure](https://portal.azure.com) e aprire l'account di archiviazione primario scelto per l'area di lavoro.
1. Selezionare **Controllo di accesso (IAM)** dal riquadro a sinistra.
1. Assegnare i ruoli seguenti o verificare che siano già assegnati. Per l'identità dell'area di lavoro e il nome dell'area di lavoro verrà usato lo stesso nome.
    * Per il ruolo **Collaboratore ai dati dei BLOB di archiviazione** nell'account di archiviazione, assegnare **myworkspace** come identità dell'area di lavoro.
    * Assegnare **myworkspace** come nome dell'area di lavoro.

1. Selezionare **Salva**.

## <a name="next-steps"></a>Passaggi successivi

* [Creare un pool SQL dedicato](quickstart-create-sql-pool-studio.md) 
* [Creare un pool di Apache Spark serverless](quickstart-create-apache-spark-pool-portal.md)
* [Usare i pool SQL serverless](quickstart-sql-on-demand.md)
