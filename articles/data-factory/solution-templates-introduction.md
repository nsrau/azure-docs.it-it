---
title: Panoramica dei modelli
description: Informazioni su come usare un modello predefinito per iniziare rapidamente con Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: daperlov
author: djpmsft
manager: anandsub
ms.custom: seo-lt-2019
ms.date: 01/04/2019
ms.openlocfilehash: 515de0fed987f412adb060a3ba58978f0400ae0d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89441953"
---
# <a name="templates"></a>Modelli

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

I modelli sono pipeline predefinite della Data Factory di Azure che consentono di introdurre l'utente alla Data Factory. I modelli sono utili quando si ha familiarità con Data Factory e si desidera un'introduzione rapida. Questi modelli riducono i tempi di sviluppo della compilazione di progetti di integrazione dati migliorando in tal modo la produttività degli sviluppatori.

## <a name="create-data-factory-pipelines-from-templates"></a>Creare pipeline di Data Factory da modelli

È possibile iniziare a creare una pipeline di Data Factory da un modello nei due modi seguenti:

1.  Selezionare **Crea una pipeline da modello** nella pagina di panoramica per aprire la raccolta modelli.

    ![Aprire la raccolta modelli dalla pagina di panoramica](media/solution-templates-introduction/templates-intro-image1.png)

1.  Nella scheda Author in Esplora inventario risorse selezionare **+** , quindi **pipeline from template** per aprire la raccolta di modelli.

    ![Aprire la raccolta modelli dalla scheda Autore](media/solution-templates-introduction/templates-intro-image2.png)

## <a name="template-gallery"></a>Raccolta modelli

![La raccolta modelli](media/solution-templates-introduction/templates-intro-image3.png)

### <a name="out-of-the-box-data-factory-templates"></a>Modelli predefiniti di Data Factory

Data Factory usa modelli di Azure Resource Manager per salvare i modelli di pipeline di Data Factory. È possibile visualizzare tutti i modelli di Gestione risorse, insieme al file manifesto usato per i modelli Data Factory predefiniti, nel [repository ufficiale Azure Data Factory GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/templates). I modelli predefiniti forniti da Microsoft includono ma non sono limitati agli elementi seguenti:

-   Copiare modelli:

    -   [Copia bulk dal database](solution-template-bulk-copy-with-control-table.md)
    
    -   [Copiare i nuovi file in base a LastModifiedDate](solution-template-copy-new-files-lastmodifieddate.md)

    -   [Copia più contenitori di file tra archivi basati su file](solution-template-copy-files-multiple-containers.md)

    -   [Spostare file](solution-template-move-files.md)

    -   [Copia Delta dal database](solution-template-delta-copy-with-control-table.md)

    -   Copia da \<source\> a \<destination\>

        -   [Da Amazon S3 ad Azure Data Lake Storage di seconda generazione](solution-template-migration-s3-azure.md)

        -   Da Google Big Query ad Azure Data Lake Storage di seconda generazione

        -   Da HDF ad Azure Data Lake Storage di seconda generazione

        -   Da Netezza ad Azure Data Lake Storage di prima generazione

        -   Da SQL Server locale al database SQL di Azure

        -   Da SQL Server locale ad Azure sinapsi Analytics (in precedenza SQL Data Warehouse)

        -   Da Oracle locale ad Azure sinapsi Analytics (in precedenza SQL Data Warehouse)

-   Modelli SSIS

    -   Pianificare Azure-SSIS Integration Runtime per eseguire i pacchetti SSIS

-   Trasformare i modelli

    -   [ETL con Azure Databricks](solution-template-databricks-notebook.md)

### <a name="my-templates"></a>Modelli personali

È anche possibile salvare una pipeline come modello selezionando **Salva come modello** nella scheda della Pipeline.

![Salvare una pipeline come modello](media/solution-templates-introduction/templates-intro-image4.png)

È possibile visualizzare le pipeline salvate come modelli nella sezione **Modelli personali** della Raccolta modelli. È anche possibile visualizzarli nella sezione **Modelli** in Esplora risorse.

![Modelli personali](media/solution-templates-introduction/templates-intro-image5.png)

> [!NOTE]
> Per usare la funzionalità dei Modelli personali, è necessario abilitare l'integrazione di GIT. Azure DevOps GIT e GitHub sono supportati.
