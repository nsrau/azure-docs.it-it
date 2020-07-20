---
title: 'Avvio rapido: Usare Synapse Studio'
description: In questo argomento di avvio rapido verrà illustrato come è facile eseguire query su diversi tipi di file con Synapse Studio.
services: synapse-analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jovanpop
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 271fd77e519ce9d78e2fa4e4837004a77cf73522
ms.sourcegitcommit: 0b2367b4a9171cac4a706ae9f516e108e25db30c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2020
ms.locfileid: "86276057"
---
# <a name="quickstart-use-synapse-studio-preview"></a>Avvio rapido: Usare Synapse Studio (anteprima)

In questo argomento di avvio rapido verrà illustrato come eseguire query sui file con Synapse Studio.

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/).

## <a name="prerequisites"></a>Prerequisiti

[Creare un'area di lavoro di Azure Synapse e un account di archiviazione associato](quickstart-create-workspace.md).

## <a name="launch-synapse-studio"></a>Avviare Synapse Studio

Nell'area di lavoro di Azure Synapse nel portale di Azure fare clic su **Avvia Synapse Studio**.

![Avviare Synapse Studio](./media/quickstart-synapse-studio/launch-synapse-workspace.png)

In alternativa, per avviare Synapse Studio, fare clic su [Azure Synapse Analytics](https://web.azuresynapse.net) e fornire i valori appropriati per tenant, sottoscrizione e area di lavoro.

## <a name="browse-storage-accounts"></a>Esplorare gli account di archiviazione

Dopo aver aperto Synapse Studio, passare a **Dati** e quindi espandere **Account di archiviazione** per visualizzare l'account di archiviazione nell'area di lavoro.

![Sfogliare i file nello spazio di archiviazione](./media/quickstart-synapse-studio/browse-files-on-storage.png)

Per creare nuove cartelle e caricare i file, usare i collegamenti nella barra degli strumenti per organizzare i file.

## <a name="query-files-on-storage-account"></a>Eseguire query sui file nell'account di archiviazione

> [!IMPORTANT]
> Per poter eseguire query sui file, è necessario essere un membro del ruolo `Storage Blob Reader` nello spazio di archiviazione sottostante. Informazioni su come [assegnare le autorizzazioni **Ruolo con autorizzazioni di lettura per i dati dei BLOB di archiviazione** o **Collaboratore ai dati dei BLOB di archiviazione** di Controllo degli accessi in base al ruolo in Archiviazione di Azure](../storage/common/storage-auth-aad-rbac-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#assign-a-built-in-rbac-role).

1. Caricare alcuni file di `PARQUET`.
2. Selezionare uno o più file e quindi creare un nuovo script SQL o un notebook di Spark per visualizzare il contenuto dei file. Se si vuole creare un notebook, è necessario creare un [pool di Apache Spark nelle aree di lavoro Synapse](quickstart-create-apache-spark-pool-studio.md).

   ![Eseguire query sui file nello spazio di archiviazione](./media/quickstart-synapse-studio/query-files-on-storage.png)

3. Eseguire la query o il notebook generato per visualizzare il contenuto del file.

   ![Visualizzare il contenuto del file](./media/quickstart-synapse-studio/query-files-on-storage-result.png)

4. È possibile modificare la query per filtrare e ordinare i risultati. Per le funzionalità del linguaggio disponibili in SQL su richiesta nella [panoramica delle funzionalità di SQL](sql/overview-features.md).

## <a name="next-steps"></a>Passaggi successivi

- Consentire agli utenti di Azure AD di eseguire query sui file [assegnando le autorizzazioni **Ruolo con autorizzazioni di lettura per i dati dei BLOB di archiviazione** o **Collaboratore ai dati dei BLOB di archiviazione** di Controllo degli accessi in base al ruolo in Archiviazione di Azure](../storage/common/storage-auth-aad-rbac-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#assign-a-built-in-rbac-role)
- [Eseguire query su file in Archiviazione di Azure con SQL su richiesta](sql/on-demand-workspace-overview.md)
- [Creare il pool di Apache Spark con il portale di Azure](quickstart-create-apache-spark-pool-portal.md)
- [Creare il report di Power BI sui file archiviati in Archiviazione di Azure](sql/tutorial-connect-power-bi-desktop.md)
