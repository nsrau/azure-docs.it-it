---
title: Copiare in blocco da un database ad Azure Data Explorer usando il modello di Azure Data FactoryCopy in bulk from a database to Azure Data Explorer by using the Azure Data Factory template
description: In questo articolo si apprenderà a usare un modello di Azure Data Factory per copiare in blocco da un database ad Azure Data Explorer
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/08/2019
ms.openlocfilehash: 884f4e956b37c2def6c25d0acdf20f15eddf7767
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76293556"
---
# <a name="copy-in-bulk-from-a-database-to-azure-data-explorer-by-using-the-azure-data-factory-template"></a>Copiare in blocco da un database ad Azure Data Explorer usando il modello di Azure Data FactoryCopy in bulk from a database to Azure Data Explorer by using the Azure Data Factory template 

Azure Data Explorer è un servizio di analisi dei dati veloce e completamente gestito. Offre analisi in tempo reale su grandi volumi di dati provenienti da molte origini, ad esempio applicazioni, siti Web e dispositivi IoT. 

Per copiare dati da un database in Oracle Server, Netezza, Teradata o SQL Server in Azure Data Explorer, è necessario caricare enormi quantità di dati da più tabelle. In genere, i dati devono essere partizionati in ogni tabella in modo che sia possibile caricare righe con più thread in parallelo da una singola tabella. In questo articolo viene descritto un modello da utilizzare in questi scenari.

[I modelli di Azure Data Factory](/azure/data-factory/solution-templates-introduction) sono pipeline di Data Factory predefinite. Questi modelli consentono di iniziare rapidamente a utilizzare Data Factory e di ridurre i tempi di sviluppo per i progetti di integrazione dei dati. 

Creare il modello Copia globale dal database a Esplora dati di Azure usando le attività Lookup e ForEach.You create the *Bulk Copy from Database to Azure Data Explorer* template by using *Lookup* and *ForEach* activities. Per una copia dei dati più rapida, è possibile usare il modello per creare molte pipeline per database o per tabella. 

> [!IMPORTANT]
> Assicurarsi di utilizzare lo strumento appropriato per la quantità di dati che si desidera copiare.
> * Usare il modello Copia in blocco dal database a Esplora dati di Azure per copiare grandi quantità di dati da database come SQL Server e Google BigQuery in Azure Data Explorer.Use the *Bulk Copy from Database to Azure Data Explorer* template to copy large amounts of data from databases such as SQL server and Google BigQuery to Azure Data Explorer. 
> * Usare [*lo strumento Copia dati di Data Factory*](data-factory-load-data.md) per copiare alcune tabelle con piccole o moderate quantità di dati in Esplora dati di Azure.Use the Data Factory Copy Data tool to copy a few tables with small or moderate amounts of data into Azure Data Explorer. 

## <a name="prerequisites"></a>Prerequisiti

* Se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* [Un cluster e un database di Esplora dati di Azure](create-cluster-database-portal.md).
* [Creare una data factory](data-factory-load-data.md#create-a-data-factory).
* Origine dati in un database.

## <a name="create-controltabledataset"></a>Creare ControlTableDatasetCreate ControlTableDataset

*ControlTableDataset* indica quali dati verranno copiati dall'origine alla destinazione nella pipeline. Il numero di righe indica il numero totale di pipeline necessarie per copiare i dati. È necessario definire ControlTableDataset come parte del database di origine.

Un esempio del formato della tabella di origine di SQL Server è illustrato nel codice seguente:An example of the SQL Server source table format is shown in the following code:
    
```sql   
CREATE TABLE control_table (
PartitionId int,
SourceQuery varchar(255),
ADXTableName varchar(255)
);
```

Gli elementi di codice sono descritti nella tabella seguente:The code elements are described in the following table:

|Proprietà  |Descrizione  | Esempio
|---------|---------| ---------|
|PartitionId   |  L'ordine di copia | 1  |  
|SourceQuery   |  Query che indica quali dati verranno copiati durante il runtime della pipeline | <br>`select * from table where lastmodifiedtime  LastModifytime >= ''2015-01-01 00:00:00''>` </br>    
|ADXTableName (Nome ADXTable)  |  Nome della tabella di destinazione | Tabella Di MyAdx       |  

Se Il ControlTableDataset è in un formato diverso, creare un ControlTableDataset paragonabile per il formato.

## <a name="use-the-bulk-copy-from-database-to-azure-data-explorer-template"></a>Usare il modello Copia in blocco dal database ad Esplora dati di AzureUse the Bulk Copy from Database to Azure Data Explorer template

1. Nel riquadro **Introduzione** selezionare **Crea pipeline da modello** per aprire il riquadro **Raccolta modelli.**

    ![Riquadro "Iniziamo" di Azure Data Factory](media/data-factory-template/adf-get-started.png)

1. Selezionare il modello Copia globale dal database ad Esplora dati di **Azure.Select** the Bulk Copy from Database to Azure Data Explorer template.
 
    ![Modello "Copia in blocco dal database ad Azure Data Explorer"The "Bulk Copy from Database to Azure Data Explorer" template](media/data-factory-template/pipeline-from-template.png)

1.  Nel riquadro Copia bulk dal database a Esplora dati di **Azure,** in Input utente, specificare i set di dati eseguendo le operazioni seguenti:In the Bulk Copy from Database to Azure Data Explorer pane, under User **Inputs**, specify your datasets by doing the following: 

    a. Nell'elenco a discesa **ControlTableDataset** selezionare il servizio collegato alla tabella di controllo che indica quali dati vengono copiati dall'origine alla destinazione e dove verranno inseriti nella destinazione. 

    b. Nell'elenco a discesa **SourceDataset** selezionare il servizio collegato al database di origine. 

    c. Nell'elenco a discesa AzureDataExplorerTable selezionare la tabella Esplora dati di Azure.In the **AzureDataExplorerTable** drop-down list, select the Azure Data Explorer table. Se il set di dati non esiste, [creare il servizio collegato Azure Data Explorer](data-factory-load-data.md#create-the-azure-data-explorer-linked-service) per aggiungere il set di dati.

    d. Selezionare **Usa questo modello**.

    ![Riquadro "Copia in blocco dal database ad Azure Data Explorer"](media/data-factory-template/configure-bulk-copy-adx-template.png)

1. Selezionare un'area nell'area di disegno, all'esterno delle attività, per accedere alla pipeline del modello. Selezionare la scheda **Parametri** per immettere i parametri per la tabella, inclusi **Nome** (nome tabella di controllo) e **Valore predefinito** (nomi di colonna).

    ![Parametri della pipeline](media/data-factory-template/pipeline-parameters.png)

1.  In **Ricerca**selezionare **GetPartitionList** per visualizzare le impostazioni predefinite. La query viene creata automaticamente.
1.  Selezionare l'attività Comando, **ForEachPartition**, selezionare la scheda **Impostazioni** e quindi eseguire le operazioni seguenti:

    a. Nella casella **Conteggio batch** immettere un numero compreso tra 1 e 50. Questa selezione determina il numero di pipeline che vengono eseguite in parallelo fino a raggiungere il numero di righe *ControlTableDataset.* 

    b. Per assicurarsi che i batch della pipeline vengano eseguiti in parallelo, *non* selezionare la casella di controllo **Sequenziale.**

    ![Impostazioni ForEachPartition](media/data-factory-template/foreach-partition-settings.png)

    > [!TIP]
    > La procedura consigliata consiste nell'eseguire molte pipeline in parallelo in modo che i dati possano essere copiati più rapidamente. Per aumentare l'efficienza, partizionare i dati nella tabella di origine e allocare una partizione per pipeline, in base alla data e alla tabella.

1. Selezionare **Convalida tutto** per convalidare la pipeline di Azure Data Factory e quindi visualizzare il risultato nel riquadro **Output convalida pipeline.**

    ![Convalidare le pipeline di modelliValidate template pipelines](media/data-factory-template/validate-template-pipelines.png)

1. Se necessario, selezionare **Debug**e quindi **Aggiungi trigger** per eseguire la pipeline.

    ![I pulsanti "Debug" e "Esegui pipeline"](media/data-factory-template/trigger-run-of-pipeline.png)    

È ora possibile utilizzare il modello per copiare in modo efficiente grandi quantità di dati dai database e dalle tabelle.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come [copiare dati in Azure Data Explorer tramite Azure Data Factory.](data-factory-load-data.md)
* Informazioni sul [connettore](/azure/data-factory/connector-azure-data-explorer) di Azure Data Explorer in Azure Data Factory.Learn about the Azure Data Explorer connector in Azure Data Factory.
* Informazioni sulle query di [Azure Data Explorer](/azure/data-explorer/web-query-data) per l'esecuzione di query sui dati.






