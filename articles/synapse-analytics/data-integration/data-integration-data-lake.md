---
title: Inserimento in Azure Data Lake Storage Gen2 in Azure Synapse AnalyticsIngest into Azure Data Lake Storage Gen2 in Azure Synapse Analytics
description: Informazioni su come inserire dati in Azure Data Lake Storage Gen2 in Azure Synapse Analytics
services: synapse-analytics
author: djpmsft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: daperlov
ms.reviewer: jrasnick
ms.openlocfilehash: 4d7d7be523749797e5dbce0e50c307fc682974f2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430578"
---
# <a name="ingesting-data-into-azure-data-lake-storage-gen2"></a>Inserimento dei dati in Azure Data Lake Storage Gen2Inging data into Azure Data Lake Storage Gen2 

In this article, you'll learn how to ingest data from one location to another in an Azure Data Lake Gen 2 (Azure Data Lake Gen 2) storage account using Azure Synapse Analytics.

## <a name="prerequisites"></a>Prerequisiti

* **Sottoscrizione di Azure:** se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* **Account di Archiviazione di Azure:** si usa Azure Data Lake Gen 2 come archivio dati *di origine.* Se non si ha un account di archiviazione, vedere [Creare un account di archiviazione](../../storage/blobs/data-lake-storage-quickstart-create-account.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) di Azure per la procedura per crearne uno.

## <a name="create-linked-services"></a>Creare servizi collegati

In Azure Synapse Analytics, un servizio collegato consente di definire le informazioni di connessione ad altri servizi. In questa sezione verranno aggiunti Azure Synapse Analytics e Azure Data Lake Gen 2 come servizi collegati.

1. Aprire l'esperienza utente di Azure Synapse Analytics e passare alla scheda **Gestisci.Open** the Azure Synapse Analytics UX and go to the Manage tab.
1. In **Connessioni esterne**selezionare Servizi **collegati**.
1. Per aggiungere un servizio collegato, fare clic su **Nuovo**.
1. Selezionare il riquadro Azure Data Lake Storage Gen2 dall'elenco e fare clic su **Continua**.
1. Immettere le credenziali di autenticazione. La chiave account, l'entità servizio e l'identità gestita sono tipi di autenticazione attualmente supportati. Fare clic su Test connessione per verificare che le credenziali siano corrette. 
1. Al termine, fare clic su **Crea.**

## <a name="create-pipeline"></a>Creare una pipeline

Una pipeline contiene il flusso logico per l'esecuzione di un set di attività. In questa sezione verrà creata una pipeline contenente un'attività di copia che inserisce dati da Azure Data Lake Gen 2 in un pool SQL.

1. Passare alla scheda **Orchestrate.** Fare clic sull'icona più accanto all'intestazione pipeline e selezionare **Pipeline**.
1. In **Sposta e trasforma** nel riquadro delle attività trascinare Copia **dati** nell'area di disegno della pipeline.
1. Fare clic sull'attività di copia e **New** passare alla scheda **Origine.**
1. Selezionare Azure Data Lake Storage Gen2 come archivio dati e fare clic su Continua.Select Azure Data Lake Storage Gen2 as your data store and click continue.
1. Selezionare DelimitedText come formato e fare clic su Continua.
1. Nel riquadro delle proprietà del gruppo selezionare il servizio collegato ADLS creato. Specificare il percorso del file dei dati di origine e specificare se la prima riga ha un'intestazione. È possibile importare lo schema dall'archivio file o da un file di esempio. Al termine, fare clic su OK.
1. Passare alla scheda **Sink.** **New**
1. Selezionare Azure Data Lake Storage gen2 come archivio dati e fare clic su Continua.Select Azure Data Lake Storage gen2 as your data store and click continue.
1. Selezionare DelimitedText come formato e fare clic su Continua.
1. Nel riquadro delle proprietà del gruppo selezionare il servizio collegato ADLS creato. Specificare il percorso della cartella in cui si desidera scrivere i dati. Al termine, fare clic su OK.

## <a name="debug-and-publish-pipeline"></a>Eseguire il debug e la pubblicazione della pipelineDebug and publish pipeline

Dopo aver completato la configurazione della pipeline, è possibile eseguire un'esecuzione di debug prima di pubblicare gli elementi per verificare che tutto sia corretto.

1. Per eseguire il debug della pipeline, selezionare **Debug** sulla barra degli strumenti. Lo stato dell'esecuzione della pipeline verrà visualizzato nella scheda **Output** nella parte inferiore della finestra. 
1. Quando è possibile eseguire correttamente la pipeline, nella barra degli strumenti superiore selezionare **Pubblica tutto**. Questa azione consente di pubblicare entità (set di dati e pipeline) create nel servizio Synapse Analytics.
1. Attendere fino alla visualizzazione del messaggio **Pubblicazione riuscita**. Per visualizzare i messaggi di notifica, fai clic sul pulsante a campana in alto a destra. 


## <a name="trigger-and-monitor-the-pipeline"></a>Attivare e monitorare la pipeline

In questo passaggio viene attivata manualmente la pipeline pubblicata nel passaggio precedente. 

1. Selezionare **Aggiungi trigger** nella barra degli strumenti, quindi selezionare **Attiva adesso**. Nella pagina **Pipeline Run** (Esecuzione di pipeline) selezionare **Fine**.  
1. Vai alla scheda **Monitor** situata nella barra laterale sinistra. Viene visualizzata un'esecuzione della pipeline attivata da un trigger manuale. È possibile utilizzare i collegamenti nella colonna **Azioni** per visualizzare i dettagli dell'attività e rieseguire la pipeline.
1. Per visualizzare le esecuzioni di attività associate all'esecuzione della pipeline, selezionare il collegamento **View Activity Runs** (Visualizza le esecuzioni di attività) nella colonna **Azioni**. In questo esempio è presente una sola attività, quindi nell'elenco viene visualizzata una sola voce. Per informazioni dettagliate sull'operazione di copia, selezionare il collegamento **Dettagli** (icona a forma di occhiali) nella colonna **Azioni**. Selezionare **Pipeline Runs** (Esecuzioni di pipeline) in alto per tornare alla visualizzazione delle esecuzioni. Per aggiornare la visualizzazione, selezionare **Aggiorna**.
1. Verificare che i dati siano scritti correttamente nel pool SQL.


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'integrazione dei dati per Synapse Analytics, vedere l'articolo [Inserimento di dati in un pool SQL.](data-integration-sql-pool.md)
