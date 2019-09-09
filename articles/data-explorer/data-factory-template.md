---
title: Usare Azure Data Factory modello per la copia bulk dal database in Azure Esplora dati
description: In questo argomento viene illustrato come usare un modello di Azure Data Factory per la copia bulk dal database in Azure Esplora dati
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/08/2019
ms.openlocfilehash: acfcee6e8308bc508bd709c40bd1794beab3130f
ms.sourcegitcommit: 95b180c92673507ccaa06f5d4afe9568b38a92fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/08/2019
ms.locfileid: "70804730"
---
# <a name="use-azure-data-factory-template-for-bulk-copy-from-database-to-azure-data-explorer"></a>Usare Azure Data Factory modello per la copia bulk dal database in Azure Esplora dati

Azure Esplora dati è un servizio di analisi dei dati veloce e completamente gestito per l'analisi in tempo reale su grandi volumi di flussi di dati da molte origini, ad esempio applicazioni, siti Web e dispositivi Internet. Azure Data Factory è un servizio di integrazione dei dati completamente gestito e basato sul cloud. È possibile usare il servizio Azure Data Factory per popolare il database di Esplora dati di Azure con i dati del sistema esistente e risparmiare tempo quando si compilano le soluzioni di analisi. 

I [modelli Azure Data Factory](/azure/data-factory/solution-templates-introduction) sono pipeline Azure Data Factory predefinite che consentono di iniziare rapidamente a usare Data Factory e ridurre i tempi di sviluppo per la compilazione di progetti di integrazione dei dati. La **copia bulk dal database al modello di Esplora dati di Azure** viene creata utilizzando le attività **Lookup** e **foreach** . È possibile usare il modello per creare molte pipeline per database o tabella per una copia più veloce dei dati. 

> [!IMPORTANT]
> * Usare il modello di **copia bulk da database a Esplora dati di Azure** per copiare grandi quantità di dati da database come SQL Server e Google BigQuery in Azure Esplora dati. 
> * Usare lo [strumento copia](data-factory-load-data.md) per copiare alcune tabelle con quantità di dati ridotta o moderata in Esplora dati di Azure. 

## <a name="prerequisites"></a>Prerequisiti

* Se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* [Un cluster e un database di Azure Esplora dati](create-cluster-database-portal.md)
* [Creare una data factory](data-factory-load-data.md#create-a-data-factory)
* Origine dati nel database

## <a name="create-controltabledataset"></a>Crea ControlTableDataset

**ControlTableDataset** indica quali dati verranno copiati dall'origine alla destinazione nella pipeline. Il numero di righe indica il numero totale di pipeline necessarie per la copia dei dati. **ControlTableDataset** deve essere definito come parte del database di origine.

Esempio di SQL Server formato della tabella di origine:
    
    ```sql   
    CREATE TABLE control_table (
        PartitionId int,
        SourceQuery varchar(255),
        ADXTableName varchar(255)
    );
    ```
    
        |Proprietà  |Descrizione  | Esempio
        |---------|---------| ---------|
        |PartitionId   |   ordine di copia | 1  |  
        |SourceQuery   |   query che indica i dati che verranno copiati durante il runtime della pipeline | <br>`select * from table where lastmodifiedtime  LastModifytime >= ''2015-01-01 00:00:00''>` </br>    
        |ADXTableName  |  Nome tabella di destinazione | MyAdxTable       |  

Se il **ControlTableDataset** è in un formato diverso, creare un **ControlTableDataset** analogo per il formato.

## <a name="use-bulk-copy-from-database-to-azure-data-explorer-template"></a>Usare la copia bulk dal database al modello di Esplora dati di Azure

1. Nella pagina **attività iniziali** selezionare il riquadro **crea pipeline dal modello** oppure selezionare l'icona della matita (scheda**autore** ) a destra **+** >  >  **pipeline da modello** per aprire la raccolta di modelli.

    ![Azure Data Factory iniziare](media/data-factory-template/adf-get-started.png)

1. Selezionare **copia bulk modello da database a Esplora dati di Azure**.
 
    ![Seleziona pipeline dal modello](media/data-factory-template/pipeline-from-template.png)

1.  Nella finestra **copia bulk da database a Esplora dati di Azure** selezionare set di dati esistenti dall'elenco a discesa. 

    * **ControlTableDataset** -servizio collegato per controllare la tabella che indica quali dati vengono copiati dall'origine alla destinazione e dove verranno inseriti nella destinazione. 
    * **SourceDataset** : servizio collegato al database di origine. 
    * **AzureDataExplorerTable** : tabella Esplora dati di Azure. Se il set di dati non esiste, [creare il servizio collegato di Azure Esplora dati](data-factory-load-data.md#create-the-azure-data-explorer-linked-service) per aggiungere il set di dati.
    * Selezionare **Usa questo modello**.

    ![configurare il modello di Esplora dati di Azure per la copia bulk](media/data-factory-template/configure-bulk-copy-adx-template.png)

1. Selezionare un'area nell'area di disegno, all'esterno delle attività, per accedere alla pipeline del modello. Selezionare i **parametri** per immettere i parametri per la tabella, inclusi **nome** (nome della tabella di controllo) e **valore predefinito** (nomi di colonna).

    ![Parametri della pipeline](media/data-factory-template/pipeline-parameters.png)

1.  Selezionare attività di ricerca, **Getpartitionname**, per visualizzare le impostazioni predefinite. La query viene creata automaticamente.
1.  Selezionare attività comando **ForEachPartition**, selezionare **Impostazioni**
    * **Conteggio batch**: Selezionare numero da 1-50. Questa selezione determina il numero di pipeline eseguite in parallelo fino a quando non viene raggiunto il numero di righe **ControlTableDataset** . 
    * Non selezionare la casella di controllo **sequenziale** in modo che i batch di pipeline vengano eseguiti in parallelo.

    ![Impostazioni ForEachPartition](media/data-factory-template/foreach-partition-settings.png)

    > [!TIP]
    > La procedura consigliata consiste nell'eseguire molte pipeline in parallelo, in modo che i dati possano essere copiati più rapidamente. Partizionare i dati nella tabella di origine e allocare una partizione per ogni pipeline, in base alla data e alla tabella, per aumentare l'efficienza.

1. Selezionare **Validate all** per convalidare la pipeline di ADF. Vedere **output di convalida della pipeline**.

    ![Convalidare le pipeline modello](media/data-factory-template/validate-template-pipelines.png)

1. Selezionare **debug**, se necessario, quindi **Aggiungi trigger** per eseguire la pipeline.

    ![Esegui pipeline](media/data-factory-template/trigger-run-of-pipeline.png)    


È ora possibile usare la **copia bulk dal database al modello di Esplora dati di Azure** per copiare in modo efficiente grandi quantità di dati dai database e dalle tabelle.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sulla procedura per [copiare i dati in Azure Esplora dati usando Azure Data Factory](data-factory-load-data.md).

* Informazioni sul [connettore Azure Esplora dati](/azure/data-factory/connector-azure-data-explorer) in Azure Data Factory.

* Informazioni sulle [query di Azure Esplora dati](/azure/data-explorer/web-query-data) per l'esecuzione di query sui dati.






