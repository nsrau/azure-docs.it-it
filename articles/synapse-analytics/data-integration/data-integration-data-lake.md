---
title: Inserire dati in Azure Data Lake Storage Gen2
description: Informazioni su come inserire dati in Azure Data Lake Storage Gen2 in Azure Synapse Analytics
services: synapse-analytics
author: djpmsft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: daperlov
ms.reviewer: jrasnick
ms.openlocfilehash: f06777e559187a57bfe0625cde700f30fb636a2b
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93309342"
---
# <a name="ingest-data-into-azure-data-lake-storage-gen2"></a>Inserire i dati in Azure Data Lake Storage Gen2 

Questo articolo illustra come inserire dati da una posizione a un'altra in un account di archiviazione Azure Data Lake Gen 2 (Azure Data Lake Gen 2) con Azure Synapse Analytics.

## <a name="prerequisites"></a>Prerequisiti

* **Sottoscrizione di Azure** : se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* **Account di archiviazione di Azure** : usare Azure Data Lake Gen 2 come archivio dati di *origine*. Se non si ha un account di archiviazione, vedere [Creare un account di archiviazione di Azure](../../storage/blobs/data-lake-storage-quickstart-create-account.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) per informazioni su come crearne uno.

## <a name="create-linked-services"></a>Creare servizi collegati

In Azure Synapse Analytics si usano i servizi collegati per definire le informazioni di connessione ad altri servizi. In questa sezione Azure Synapse Analytics e Azure Data Lake Gen 2 verranno aggiunti come servizi collegati.

1. Aprire l'interfaccia utente di Azure Synapse Analytics e passare alla scheda **Gestisci**.
1. In **Connessioni esterne** selezionare **Servizi collegati**.
1. Per aggiungere un servizio collegato, selezionare **nuovo**.
1. Selezionare il riquadro Azure Data Lake Storage Gen2 dall'elenco e selezionare **continua**.
1. Immettere le credenziali di autenticazione. I tipi di autenticazione attualmente supportati sono la chiave dell'account, l'entità servizio e l'identità gestita. Selezionare Test connessione per verificare che le credenziali siano corrette. 
1. Selezionare **Create** (Crea) al termine.

## <a name="create-pipeline"></a>Creare una pipeline

Una pipeline contiene il flusso logico per un'esecuzione di un set di attività. In questa sezione verrà creata una pipeline contenente un'attività di copia che inserisce i dati da Azure Data Lake generazione 2 in un pool SQL dedicato.

1. Passare alla scheda **orchestrazione** . Selezionare l'icona più accanto all'intestazione pipeline e selezionare **pipeline**.
1. In **Move and Transform** (Sposta e trasforma) nel riquadro delle attività trascinare **Copia dati** sul canvas della pipeline.
1. Selezionare nell'attività di copia e passare alla scheda **origine** . Selezionare **nuovo** per creare un nuovo set di dati di origine.
1. Selezionare Azure Data Lake Storage Gen2 come archivio dati e selezionare continua.
1. Selezionare DelimitedText come formato e selezionare continua.
1. Nel riquadro di impostazione delle proprietà selezionare il servizio collegato Azure Data Lake Storage creato. Specificare il percorso del file dei dati di origine e indicare se la prima riga contiene un'intestazione. È possibile importare lo schema dall'archivio file o da un file di esempio. Selezionare OK al termine.
1. Passare alla scheda **sink** . Selezionare **nuovo** per creare un nuovo set di dati sink.
1. Selezionare Azure Data Lake Storage Gen2 come archivio dati e selezionare continua.
1. Selezionare DelimitedText come formato e selezionare continua.
1. Nel riquadro di impostazione delle proprietà selezionare il servizio collegato Azure Data Lake Storage creato. Specificare il percorso della cartella in cui scrivere i dati. Selezionare OK al termine.

## <a name="debug-and-publish-pipeline"></a>Eseguire il debug della pipeline e pubblicarla

Dopo aver completato la configurazione della pipeline, è possibile eseguire il debug prima di pubblicare gli artefatti per verificare che tutto sia corretto.

1. Per eseguire il debug della pipeline, selezionare **Debug** sulla barra degli strumenti. Lo stato dell'esecuzione della pipeline verrà visualizzato nella scheda **Output** nella parte inferiore della finestra. 
1. Quando è possibile eseguire correttamente la pipeline, nella barra degli strumenti superiore selezionare **Pubblica tutto**. Questa azione pubblica le entità create (set di dati e pipeline) create nel servizio Synapse Analytics.
1. Attendere fino alla visualizzazione del messaggio **Pubblicazione riuscita**. Per visualizzare i messaggi di notifica, selezionare il pulsante a campana nella parte superiore destra. 


## <a name="trigger-and-monitor-the-pipeline"></a>Attivare e monitorare la pipeline

In questo passaggio si attiverà manualmente la pipeline pubblicata nel passaggio precedente. 

1. Selezionare **Aggiungi trigger** nella barra degli strumenti, quindi selezionare **Attiva adesso**. Nella pagina **Pipeline Run** (Esecuzione di pipeline) selezionare **Fine**.  
1. Passare alla scheda **Monitora** nella barra laterale sinistra. Viene visualizzata un'esecuzione della pipeline attivata da un trigger manuale. È possibile usare i collegamenti nella colonna **Azioni** per visualizzare i dettagli delle attività ed eseguire di nuovo la pipeline.
1. Per visualizzare le esecuzioni di attività associate all'esecuzione della pipeline, selezionare il collegamento **View Activity Runs** (Visualizza le esecuzioni di attività) nella colonna **Azioni**. In questo esempio è presente una sola attività, quindi nell'elenco viene visualizzata una sola voce. Per informazioni dettagliate sull'operazione di copia, selezionare il collegamento **Dettagli** (icona a forma di occhiali) nella colonna **Azioni**. Selezionare **Pipeline Runs** (Esecuzioni di pipeline) in alto per tornare alla visualizzazione delle esecuzioni. Per aggiornare la visualizzazione, selezionare **Aggiorna**.
1. Verificare che i dati siano scritti correttamente nel pool SQL dedicato.


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'integrazione dei dati per l'analisi delle sinapsi, vedere l'articolo inserimento di [dati in un pool SQL dedicato](data-integration-sql-pool.md) .
