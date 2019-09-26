---
title: Copiare in blocco da un database in Esplora dati di Azure usando il modello di Azure Data Factory
description: Questo articolo illustra come usare un modello di Azure Data Factory per la copia bulk da un database in Azure Esplora dati
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/08/2019
ms.openlocfilehash: ca50a1ecd4d2a21593ddd11f83337ae7476cf916
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300434"
---
# <a name="copy-in-bulk-from-a-database-to-azure-data-explorer-by-using-the-azure-data-factory-template"></a>Copiare in blocco da un database in Esplora dati di Azure usando il modello di Azure Data Factory 

Azure Esplora dati è un servizio di analisi dei dati veloce e completamente gestito. Offre analisi in tempo reale su grandi volumi di dati trasmessi da numerose origini, ad esempio applicazioni, siti Web e dispositivi Internet. 

Azure Data Factory è un servizio di integrazione dei dati completamente gestito e basato sul cloud. È possibile usarlo per popolare il database Esplora dati di Azure con i dati del sistema esistente. E consente di risparmiare tempo durante la compilazione di soluzioni di analisi. 

I [modelli Azure Data Factory](/azure/data-factory/solution-templates-introduction) sono pipeline data factory predefinite. Questi modelli consentono di iniziare rapidamente a usare Data Factory e ridurre i tempi di sviluppo nei progetti di integrazione dei dati. 

Per creare la *copia bulk dal database al* modello di Esplora dati di Azure, è possibile usare le attività di *ricerca* e *foreach* . Per velocizzare la copia dei dati, è possibile usare il modello per creare molte pipeline per database o per tabella. 

> [!IMPORTANT]
> Assicurarsi di utilizzare lo strumento appropriato per la quantità di dati che si desidera copiare.
> * Usare il modello di *copia bulk da database a Esplora dati di Azure* per copiare grandi quantità di dati da database come SQL Server e Google BigQuery in Azure Esplora dati. 
> * Usare lo [*strumento Copia dati Data Factory*](data-factory-load-data.md) per copiare alcune tabelle con quantità di dati ridotta o moderata in Esplora dati di Azure. 

## <a name="prerequisites"></a>Prerequisiti

* Se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* [Un cluster e un database di Esplora dati di Azure](create-cluster-database-portal.md).
* [Creare un data factory](data-factory-load-data.md#create-a-data-factory).
* Origine dati in un database.

## <a name="create-controltabledataset"></a>Crea ControlTableDataset

*ControlTableDataset* indica quali dati verranno copiati dall'origine alla destinazione nella pipeline. Il numero di righe indica il numero totale di pipeline necessarie per la copia dei dati. È necessario definire ControlTableDataset come parte del database di origine.

Un esempio del formato della tabella di origine SQL Server è illustrato nel codice seguente:
    
```sql   
CREATE TABLE control_table (
PartitionId int,
SourceQuery varchar(255),
ADXTableName varchar(255)
);
```

Gli elementi di codice sono descritti nella tabella seguente:

|Proprietà  |Descrizione  | Esempio
|---------|---------| ---------|
|PartitionId   |  Ordine di copia | 1  |  
|SourceQuery   |  Query che indica i dati che verranno copiati durante il runtime della pipeline | <br>`select * from table where lastmodifiedtime  LastModifytime >= ''2015-01-01 00:00:00''>` </br>    
|ADXTableName  |  Nome della tabella di destinazione | MyAdxTable       |  

Se il ControlTableDataset è in un formato diverso, creare un ControlTableDataset analogo per il formato.

## <a name="use-the-bulk-copy-from-database-to-azure-data-explorer-template"></a>Usare la copia bulk dal database al modello di Esplora dati di Azure

1. Nel riquadro attività **iniziali** selezionare **crea pipeline da modello** per aprire il riquadro **raccolta modelli** .

    ![Riquadro "Introduzione" Azure Data Factory](media/data-factory-template/adf-get-started.png)

1. Selezionare la **copia bulk dal database al modello di Esplora dati di Azure** .
 
    ![Modello "copia bulk dal database al Esplora dati di Azure"](media/data-factory-template/pipeline-from-template.png)

1.  Nel riquadro **copia bulk da database a Esplora dati di Azure** , in **input utente**specificare i set di dati attenendosi alla seguente procedura: 

    a. Nell'elenco a discesa **ControlTableDataset** selezionare il servizio collegato alla tabella dei controlli che indica quali dati vengono copiati dall'origine alla destinazione e dove verranno inseriti nella destinazione. 

    b. Nell'elenco a discesa **SourceDataset** selezionare il servizio collegato al database di origine. 

    c. Nell'elenco a discesa **AzureDataExplorerTable** selezionare la tabella Esplora dati di Azure. Se il set di dati non esiste, [creare il servizio collegato di Azure Esplora dati](data-factory-load-data.md#create-the-azure-data-explorer-linked-service) per aggiungere il set di dati.

    d. Selezionare **Usa questo modello**.

    ![Riquadro "copia bulk dal database al Esplora dati di Azure"](media/data-factory-template/configure-bulk-copy-adx-template.png)

1. Selezionare un'area nell'area di disegno, all'esterno delle attività, per accedere alla pipeline del modello. Selezionare la scheda **parametri** per immettere i parametri per la tabella, inclusi **nome** (nome della tabella di controllo) e **valore predefinito** (nomi di colonna).

    ![Parametri della pipeline](media/data-factory-template/pipeline-parameters.png)

1.  In **Lookup**selezionare **getpartitionare** per visualizzare le impostazioni predefinite. La query viene creata automaticamente.
1.  Selezionare l'attività del comando, **ForEachPartition**, selezionare la scheda **Impostazioni** e quindi eseguire le operazioni seguenti:

    a. Nella casella **batch count** immettere un numero compreso tra 1 e 50. Questa selezione determina il numero di pipeline eseguite in parallelo fino a quando non viene raggiunto il numero di righe *ControlTableDataset* . 

    b. Per assicurarsi che i batch di pipeline vengano eseguiti in parallelo, *non* selezionare la casella di controllo **sequenziale** .

    ![Impostazioni ForEachPartition](media/data-factory-template/foreach-partition-settings.png)

    > [!TIP]
    > La procedura consigliata consiste nell'eseguire più pipeline in parallelo, in modo che i dati possano essere copiati più rapidamente. Per aumentare l'efficienza, partizionare i dati nella tabella di origine e allocare una partizione per ogni pipeline, in base a data e tabella.

1. Selezionare **Validate all** per convalidare la pipeline di Azure Data Factory, quindi visualizzare il risultato nel riquadro di **output di convalida della pipeline** .

    ![Convalidare le pipeline modello](media/data-factory-template/validate-template-pipelines.png)

1. Se necessario, selezionare **debug**, quindi selezionare **Aggiungi trigger** per eseguire la pipeline.

    ![Pulsanti "debug" e "Esegui pipeline"](media/data-factory-template/trigger-run-of-pipeline.png)    

È ora possibile usare il modello per copiare in modo efficiente grandi quantità di dati dai database e dalle tabelle.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [copiare dati in Esplora dati di Azure usando Azure Data Factory](data-factory-load-data.md).
* Informazioni sul [connettore Azure Esplora dati](/azure/data-factory/connector-azure-data-explorer) in Azure Data Factory.
* Informazioni sulle [query di Azure Esplora dati](/azure/data-explorer/web-query-data) per l'esecuzione di query sui dati.






