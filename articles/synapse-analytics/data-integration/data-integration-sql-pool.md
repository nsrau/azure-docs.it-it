---
title: Inserire i dati in un pool SQL
description: Informazioni su come inserire dati in un pool SQL in Azure sinapsi Analytics
services: synapse-analytics
author: djpmsft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: daperlov
ms.reviewer: jrasnick
ms.openlocfilehash: 63e83e69e5e09c17b2a2ddb5ca7bee6474e2fddd
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87386674"
---
# <a name="ingest-data-into-a-sql-pool"></a>Inserire i dati in un pool SQL

In questo articolo si apprenderà come inserire dati da un account di archiviazione Azure Data Lake generazione 2 in un pool SQL usando Azure sinapsi Analytics.

## <a name="prerequisites"></a>Prerequisiti

* **Sottoscrizione di Azure**: se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* **Account di archiviazione di Azure**: si usa Azure Data Lake storage generazione 2 come archivio dati di *origine* . Se non si ha un account di archiviazione, vedere [Creare un account di archiviazione di Azure](../../storage/blobs/data-lake-storage-quickstart-create-account.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) per informazioni su come crearne uno.
* **Analisi delle sinapsi di Azure**: si usa un pool SQL come archivio dati *sink* . Se non si ha un'istanza di Azure sinapsi Analytics, vedere [creare un pool SQL](../../azure-sql/database/single-database-create-quickstart.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) per i passaggi da eseguire per crearne uno.

## <a name="create-linked-services"></a>Creare servizi collegati

In Azure Synapse Analytics si usano i servizi collegati per definire le informazioni di connessione ad altri servizi. In questa sezione si aggiungerà un servizio di analisi delle sinapsi di Azure e Azure Data Lake Storage Gen2 il servizio collegato.

1. Aprire l'interfaccia utente di Azure Synapse Analytics e passare alla scheda **Gestisci**.
1. In **Connessioni esterne** selezionare **Servizi collegati**.
1. Per aggiungere un servizio collegato, fare clic su **Nuovo**.
1. Selezionare il riquadro Azure Data Lake Storage Gen2 nell'elenco e fare clic su **Continua**.
1. Immettere le credenziali di autenticazione. I tipi di autenticazione attualmente supportati sono la chiave dell'account, l'entità servizio e l'identità gestita. Fare clic su Test connessione per verificare che le credenziali siano corrette. Al termine, fare clic su **Crea**.
1. Ripetere i passaggi 3-5, ma invece di Azure Data Lake Storage Gen2 selezionare il riquadro Azure sinapsi Analytics e immettere le credenziali di connessione corrispondenti. Per l'analisi delle sinapsi di Azure, l'autenticazione SQL, l'identità gestita e l'entità servizio sono attualmente supportate.

## <a name="create-pipeline"></a>Creare una pipeline

Una pipeline contiene il flusso logico per un'esecuzione di un set di attività. In questa sezione verrà creata una pipeline contenente un'attività di copia che inserisce i dati da ADLS Gen2 in un pool SQL.

1. Passare alla scheda **Orchestrate** (Orchestrazione). Fare clic sull'icona con il segno più accanto all'intestazione della pipeline e selezionare **Pipeline**.
1. In **Move and Transform** (Sposta e trasforma) nel riquadro delle attività trascinare **Copia dati** sul canvas della pipeline.
1. Fare clic sull'attività di copia e passare alla scheda **Origine**. Fare clic su **Nuovo** per creare un nuovo set di dati di origine.
1. Selezionare Azure Data Lake Storage Gen2 come archivio dati e fare clic su Continua.
1. Selezionare DelimitedText come formato e fare clic su Continua.
1. Nel riquadro di impostazione delle proprietà selezionare il servizio collegato Azure Data Lake Storage creato. Specificare il percorso del file dei dati di origine e indicare se la prima riga contiene un'intestazione. È possibile importare lo schema dall'archivio file o da un file di esempio. Al termine, fare clic su OK.
1. Passare alla scheda **Sink**. Fare clic su **Nuovo** per creare un nuovo set di dati sink.
1. Selezionare Azure sinapsi Analytics come archivio dati e fare clic su continue (continua).
1. Nel riquadro imposta proprietà selezionare il servizio collegato Azure sinapsi Analytics creato. Se si sta scrivendo in una tabella esistente, selezionarla dall'elenco a discesa. In caso contrario, selezionare **modifica** e immettere il nome della nuova tabella. Al termine, fare clic su OK
1. Se si sta creando una tabella, abilitare **creazione automatica tabella** nel campo opzione tabella.

## <a name="debug-and-publish-pipeline"></a>Eseguire il debug della pipeline e pubblicarla

Dopo aver completato la configurazione della pipeline, è possibile eseguire il debug prima di pubblicare gli artefatti per verificare che tutto sia corretto.

1. Per eseguire il debug della pipeline, selezionare **Debug** sulla barra degli strumenti. Lo stato dell'esecuzione della pipeline verrà visualizzato nella scheda **Output** nella parte inferiore della finestra. 
1. Quando è possibile eseguire correttamente la pipeline, nella barra degli strumenti superiore selezionare **Pubblica tutto**. Questa azione pubblica le entità create (set di dati e pipeline) create nel servizio Synapse Analytics.
1. Attendere fino alla visualizzazione del messaggio **Pubblicazione riuscita**. Per visualizzare i messaggi di notifica, fare clic sul pulsante a forma di campana in alto a destra. 


## <a name="trigger-and-monitor-the-pipeline"></a>Attivare e monitorare la pipeline

In questo passaggio si attiverà manualmente la pipeline pubblicata nel passaggio precedente. 

1. Selezionare **Aggiungi trigger** nella barra degli strumenti, quindi selezionare **Attiva adesso**. Nella pagina **Pipeline Run** (Esecuzione di pipeline) selezionare **Fine**.  
1. Passare alla scheda **Monitora** nella barra laterale sinistra. Viene visualizzata un'esecuzione della pipeline attivata da un trigger manuale. È possibile usare i collegamenti nella colonna **Azioni** per visualizzare i dettagli delle attività ed eseguire di nuovo la pipeline.
1. Per visualizzare le esecuzioni di attività associate all'esecuzione della pipeline, selezionare il collegamento **View Activity Runs** (Visualizza le esecuzioni di attività) nella colonna **Azioni**. In questo esempio è presente una sola attività, quindi nell'elenco viene visualizzata una sola voce. Per informazioni dettagliate sull'operazione di copia, selezionare il collegamento **Dettagli** (icona a forma di occhiali) nella colonna **Azioni**. Selezionare **Pipeline Runs** (Esecuzioni di pipeline) in alto per tornare alla visualizzazione delle esecuzioni. Per aggiornare la visualizzazione, selezionare **Aggiorna**.
1. Verificare che i dati siano scritti correttamente nel pool SQL.


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'integrazione dei dati per l'analisi delle sinapsi, vedere l'articolo inserimento di [dati in Azure Data Lake storage Gen2](data-integration-data-lake.md) .
