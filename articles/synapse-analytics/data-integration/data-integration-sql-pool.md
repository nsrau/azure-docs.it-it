---
title: Inserire i dati in un pool SQL dedicato
description: Informazioni su come inserire dati in un pool SQL dedicato in Azure sinapsi Analytics
services: synapse-analytics
author: djpmsft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 11/03/2020
ms.author: daperlov
ms.reviewer: jrasnick
ms.openlocfilehash: afebb6a67b4c8a33e812c0e51ff225f16cd35356
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96461463"
---
# <a name="ingest-data-into-a-dedicated-sql-pool"></a>Inserire i dati in un pool SQL dedicato

Questo articolo illustra come inserire dati da un account di archiviazione Azure Data Lake generazione 2 a un pool SQL dedicato in Azure sinapsi Analytics.

## <a name="prerequisites"></a>Prerequisiti

* **Sottoscrizione di Azure**: se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* **Account di archiviazione di Azure**: si usa Azure Data Lake storage generazione 2 come archivio dati di *origine* . Se non si ha un account di archiviazione, vedere [Creare un account di archiviazione di Azure](../../storage/blobs/data-lake-storage-quickstart-create-account.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) per informazioni su come crearne uno.
* **Analisi delle sinapsi di Azure**: si usa un pool SQL dedicato come archivio dati *sink* . Se non è disponibile un'istanza di Azure Synapse Analytics, vedere [Creare un pool SQL dedicato](../../azure-sql/database/single-database-create-quickstart.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) per crearne una.

## <a name="create-linked-services"></a>Creare servizi collegati

In Azure Synapse Analytics si usano i servizi collegati per definire le informazioni di connessione ad altri servizi. In questa sezione si aggiungerà un servizio di analisi delle sinapsi di Azure e Azure Data Lake Storage Gen2 il servizio collegato.

1. Aprire l'interfaccia utente di Azure Synapse Analytics e passare alla scheda **Gestisci**.
1. In **Connessioni esterne** selezionare **Servizi collegati**.
1. Per aggiungere un servizio collegato, selezionare **Nuovo**.
1. Selezionare il riquadro Azure Data Lake Storage Gen2 dall'elenco e selezionare **continua**.
1. Immettere le credenziali di autenticazione. I tipi di autenticazione attualmente supportati sono la chiave dell'account, l'entità servizio e l'identità gestita. Selezionare Test connessione per verificare che le credenziali siano corrette. Selezionare **Create** (Crea) al termine.
1. Ripetere i passaggi 3-5, ma invece di Azure Data Lake Storage Gen2 selezionare il riquadro Azure sinapsi Analytics e immettere le credenziali di connessione corrispondenti. Per l'analisi delle sinapsi di Azure, l'autenticazione SQL, l'identità gestita e l'entità servizio sono attualmente supportate.

## <a name="create-pipeline"></a>Creare una pipeline

Una pipeline contiene il flusso logico per un'esecuzione di un set di attività. In questa sezione verrà creata una pipeline contenente un'attività di copia che inserisce i dati da ADLS Gen2 in un pool SQL dedicato.

1. Passare alla scheda **integrazione** . Selezionare l'icona più accanto all'intestazione pipeline e selezionare **pipeline**.
1. In **Move and Transform** (Sposta e trasforma) nel riquadro delle attività trascinare **Copia dati** sul canvas della pipeline.
1. Selezionare nell'attività di copia e passare alla scheda **origine** . Selezionare **nuovo** per creare un nuovo set di dati di origine.
1. Selezionare Azure Data Lake Storage Gen2 come archivio dati e selezionare continua.
1. Selezionare DelimitedText come formato e selezionare continua.
1. Nel riquadro di impostazione delle proprietà selezionare il servizio collegato Azure Data Lake Storage creato. Specificare il percorso del file dei dati di origine e indicare se la prima riga contiene un'intestazione. È possibile importare lo schema dall'archivio file o da un file di esempio. Selezionare OK al termine.
1. Passare alla scheda **sink** . Selezionare **nuovo** per creare un nuovo set di dati sink.
1. Selezionare Azure sinapsi Analytics come archivio dati e selezionare continua.
1. Nel riquadro imposta proprietà selezionare il servizio collegato Azure sinapsi Analytics creato. Se si sta scrivendo in una tabella esistente, selezionarla dall'elenco a discesa. In caso contrario, selezionare **modifica** e immettere il nome della nuova tabella. Al termine, selezionare OK.
1. Se si sta creando una tabella, abilitare **creazione automatica tabella** nel campo opzione tabella.

## <a name="debug-and-publish-pipeline"></a>Eseguire il debug della pipeline e pubblicarla

Dopo aver completato la configurazione della pipeline, è possibile eseguire il debug prima di pubblicare gli artefatti per verificare che tutto sia corretto.

1. Per eseguire il debug della pipeline, selezionare **Debug** sulla barra degli strumenti. Lo stato dell'esecuzione della pipeline verrà visualizzato nella scheda **Output** nella parte inferiore della finestra. 
1. Quando è possibile eseguire correttamente la pipeline, nella barra degli strumenti superiore selezionare **Pubblica tutto**. Questa azione pubblica le entità create (set di dati e pipeline) create nel servizio Synapse Analytics.
1. Attendere fino alla visualizzazione del messaggio **Pubblicazione riuscita**. Per visualizzare i messaggi di notifica, selezionare il pulsante a forma di campana in alto a destra. 


## <a name="trigger-and-monitor-the-pipeline"></a>Attivare e monitorare la pipeline

In questo passaggio si attiverà manualmente la pipeline pubblicata nel passaggio precedente. 

1. Selezionare **Aggiungi trigger** nella barra degli strumenti, quindi selezionare **Attiva adesso**. Nella pagina **Pipeline Run** (Esecuzione di pipeline) selezionare **Fine**.  
1. Passare alla scheda **Monitora** nella barra laterale sinistra. Viene visualizzata un'esecuzione della pipeline attivata da un trigger manuale. È possibile usare i collegamenti nella colonna **Azioni** per visualizzare i dettagli delle attività ed eseguire di nuovo la pipeline.
1. Per visualizzare le esecuzioni di attività associate all'esecuzione della pipeline, selezionare il collegamento **View Activity Runs** (Visualizza le esecuzioni di attività) nella colonna **Azioni**. In questo esempio è presente una sola attività, quindi nell'elenco viene visualizzata una sola voce. Per informazioni dettagliate sull'operazione di copia, selezionare il collegamento **Dettagli** (icona a forma di occhiali) nella colonna **Azioni**. Selezionare **Pipeline Runs** (Esecuzioni di pipeline) in alto per tornare alla visualizzazione delle esecuzioni. Per aggiornare la visualizzazione, selezionare **Aggiorna**.
1. Verificare che i dati siano scritti correttamente nel pool SQL dedicato.


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'integrazione dei dati per Azure sinapsi Analytics, vedere l'articolo sull'inserimento di [dati in Azure Data Lake storage Gen2 ](data-integration-data-lake.md) .
