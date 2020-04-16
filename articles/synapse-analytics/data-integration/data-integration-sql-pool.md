---
title: Inserimento nel pool SQL in Azure Synapse AnalyticsIngest into SQL pool in Azure Synapse Analytics
description: Informazioni su come inserire dati in un pool SQL in Azure Synapse Analytics
services: synapse-analytics
author: djpmsft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: daperlov
ms.reviewer: jrasnick
ms.openlocfilehash: fbd8e03b1f8af7802133c35ae4860116aaea0c3c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430565"
---
# <a name="ingesting-data-into-a-sql-pool"></a>Inserimento di dati in un pool SQLInging data into a SQL pool

In this article you will learn how to ingest data from an Azure Data Lake Gen 2 storage account into a SQL pool using Azure Synapse Analytics.

## <a name="prerequisites"></a>Prerequisiti

* **Sottoscrizione di Azure:** se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* **Account di archiviazione**di Azure: usare Archiviazione dati di Azure come archivio dati *di origine.* Se non si ha un account di archiviazione, vedere [Creare un account di archiviazione](../../storage/blobs/data-lake-storage-quickstart-create-account.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) di Azure per la procedura per crearne uno.
* **Analisi di Azure Synapse:** usare un pool SQL come archivio dati *sink.* Se non si dispone di un'istanza di Azure Synapse Analytics, vedere [Creare un pool SQL](../../sql-database/sql-database-get-started-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) per la procedura per crearne uno.

## <a name="create-linked-services"></a>Creare servizi collegati

In Azure Synapse Analytics, un servizio collegato consente di definire le informazioni di connessione ad altri servizi. In questa sezione si aggiungerà un servizio collegato Azure Synapse Analytics e Azure Data Lake Storage Gen2.In this section, you'll add a Azure Synapse Analytics and Azure Data Lake Storage Gen2 linked service.

1. Aprire l'esperienza utente di Azure Synapse Analytics e passare alla scheda **Gestisci.Open** the Azure Synapse Analytics UX and go to the Manage tab.
1. In **Connessioni esterne**selezionare Servizi **collegati**.
1. Per aggiungere un servizio collegato, fare clic su **Nuovo**.
1. Selezionare il riquadro Azure Data Lake Storage Gen2 dall'elenco e fare clic su **Continua**.
1. Immettere le credenziali di autenticazione. La chiave account, l'entità servizio e l'identità gestita sono tipi di autenticazione attualmente supportati. Fare clic su Test connessione per verificare che le credenziali siano corrette. Al termine, fare clic su **Crea.**
1. Ripetere i passaggi da 3 a 5, ma invece di Azure Data Lake Storage Gen2, selezionare il riquadro Analisi synapse di Azure e immettere le credenziali di connessione corrispondenti. Per Azure Synapse Analytics, l'autenticazione SQL, l'identità gestita e l'entità servizio sono attualmente supportate.

## <a name="create-pipeline"></a>Creare una pipeline

Una pipeline contiene il flusso logico per l'esecuzione di un set di attività. In questa sezione verrà creata una pipeline contenente un'attività di copia che inserisce i dati da ADLS gen 2 in un pool SQL.

1. Passare alla scheda **Orchestrate.** Fare clic sull'icona più accanto all'intestazione pipeline e selezionare **Pipeline**.
1. In **Sposta e trasforma** nel riquadro delle attività trascinare Copia **dati** nell'area di disegno della pipeline.
1. Fare clic sull'attività di copia e **New** passare alla scheda **Origine.**
1. Selezionare Azure Data Lake Storage gen2 come archivio dati e fare clic su Continua.Select Azure Data Lake Storage gen2 as your data store and click continue.
1. Selezionare DelimitedText come formato e fare clic su Continua.
1. Nel riquadro delle proprietà del gruppo selezionare il servizio collegato ADLS creato. Specificare il percorso del file dei dati di origine e specificare se la prima riga ha un'intestazione. È possibile importare lo schema dall'archivio file o da un file di esempio. Al termine, fare clic su OK.
1. Passare alla scheda **Sink.** **New**
1. Selezionare Analisi synapse di Azure come archivio dati e fare clic su Continua.Select Azure Synapse Analytics as your data store and click continue.
1. Nel riquadro delle proprietà del set selezionare il servizio collegato Azure Synapse Analytics creato. Se stai scrivendo in una tabella esistente, selezionala dall'elenco a discesa. In caso contrario, selezionare **Modifica** e immettere il nome della nuova tabella. Al termine, fare clic su OK
1. Se si sta creando una tabella, abilitare **Creazione automatica tabella** nel campo delle opzioni della tabella.

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

Per altre informazioni sull'integrazione dei dati per Synapse Analytics, vedere l'articolo Inserimento di dati in Azure Data Lake Storage Gen2.For more information on data integration for Synapse Analytics, see the [Ingesting data into Azure Data Lake Storage Gen2](data-integration-data-lake.md) article.
