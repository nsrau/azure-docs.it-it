---
title: Inserire nel pool SQL in Azure sinapsi Analytics
description: Informazioni su come inserire dati in un pool SQL in Azure sinapsi Analytics
services: synapse-analytics
author: djpmsft
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: daperlov
ms.reviewer: jrasnick
ms.openlocfilehash: fbd8e03b1f8af7802133c35ae4860116aaea0c3c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81430565"
---
# <a name="ingesting-data-into-a-sql-pool"></a>Inserimento di dati in un pool SQL

In questo articolo si apprenderà come inserire dati da un account di archiviazione Azure Data Lake generazione 2 in un pool SQL usando Azure sinapsi Analytics.

## <a name="prerequisites"></a>Prerequisiti

* **Sottoscrizione di Azure**: se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* **Account di archiviazione di Azure**: si usa Azure Data Lake storage generazione 2 come archivio dati di *origine* . Se non si ha un account di archiviazione, vedere [creare un account di archiviazione di Azure](../../storage/blobs/data-lake-storage-quickstart-create-account.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) per i passaggi necessari per crearne uno.
* **Analisi delle sinapsi di Azure**: si usa un pool SQL come archivio dati *sink* . Se non si ha un'istanza di Azure sinapsi Analytics, vedere [creare un pool SQL](../../sql-database/sql-database-get-started-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) per i passaggi da eseguire per crearne uno.

## <a name="create-linked-services"></a>Creare servizi collegati

In Azure sinapsi Analytics un servizio collegato è il punto in cui si definiscono le informazioni di connessione ad altri servizi. In questa sezione si aggiungerà un servizio di analisi delle sinapsi di Azure e Azure Data Lake Storage Gen2 il servizio collegato.

1. Aprire Azure sinapsi Analytics UX e passare alla scheda **Gestisci** .
1. In **connessioni esterne**selezionare **servizi collegati**.
1. Per aggiungere un servizio collegato, fare clic su **nuovo**.
1. Selezionare il riquadro Azure Data Lake Storage Gen2 dall'elenco e fare clic su **continua**.
1. Immettere le credenziali di autenticazione. La chiave dell'account, l'entità servizio e l'identità gestita sono i tipi di autenticazione attualmente supportati. Fare clic su Test connessione per verificare che le credenziali siano corrette. Al termine, fare clic su **Crea** .
1. Ripetere i passaggi 3-5, ma invece di Azure Data Lake Storage Gen2 selezionare il riquadro Azure sinapsi Analytics e immettere le credenziali di connessione corrispondenti. Per l'analisi delle sinapsi di Azure, l'autenticazione SQL, l'identità gestita e l'entità servizio sono attualmente supportate.

## <a name="create-pipeline"></a>Creare una pipeline

Una pipeline contiene il flusso logico per l'esecuzione di un set di attività. In questa sezione si creerà una pipeline contenente un'attività di copia che inserisce i dati da ADLS gen 2 in un pool SQL.

1. Passare alla scheda **orchestrazione** . fare clic sull'icona con il segno più accanto all'intestazione pipeline e selezionare **pipeline**.
1. In **spostamento e trasformazione** nel riquadro attività trascinare **copia dati** nell'area di disegno della pipeline.
1. Fare clic sull'attività di copia e passare alla scheda **origine** . fare clic su **nuovo** per creare un nuovo set di dati di origine.
1. Selezionare Azure Data Lake Storage Gen2 come archivio dati e fare clic su continua.
1. Selezionare DelimitedText come formato e fare clic su continue (continua).
1. Nel riquadro imposta proprietà selezionare il servizio collegato ADLS creato. Specificare il percorso del file dei dati di origine e specificare se la prima riga contiene un'intestazione. È possibile importare lo schema dall'archivio file o da un file di esempio. Al termine, fare clic su OK.
1. Passare alla scheda **sink** . fare clic su **nuovo** per creare un nuovo set di dati sink.
1. Selezionare Azure sinapsi Analytics come archivio dati e fare clic su continue (continua).
1. Nel riquadro imposta proprietà selezionare il servizio collegato Azure sinapsi Analytics creato. Se si sta scrivendo in una tabella esistente, selezionarla dall'elenco a discesa. In caso contrario, selezionare **modifica** e immettere il nome della nuova tabella. Al termine, fare clic su OK
1. Se si sta creando una tabella, abilitare **creazione automatica tabella** nel campo opzione tabella.

## <a name="debug-and-publish-pipeline"></a>Pipeline di debug e pubblicazione

Al termine della configurazione della pipeline, è possibile eseguire un'esecuzione del debug prima di pubblicare gli elementi per verificare che tutto sia corretto.

1. Per eseguire il debug della pipeline, selezionare **Debug** sulla barra degli strumenti. Lo stato dell'esecuzione della pipeline verrà visualizzato nella scheda **Output** nella parte inferiore della finestra. 
1. Una volta che la pipeline può essere eseguita correttamente, nella barra degli strumenti superiore selezionare **pubblica tutti**. Questa azione pubblica le entità (set di dati e pipeline) create nel servizio sinapsi Analytics.
1. Attendere fino alla visualizzazione del messaggio **Pubblicazione riuscita**. Per visualizzare i messaggi di notifica, fare clic sul pulsante a campana nella parte superiore destra. 


## <a name="trigger-and-monitor-the-pipeline"></a>Attivare e monitorare la pipeline

In questo passaggio viene attivata manualmente la pipeline pubblicata nel passaggio precedente. 

1. Selezionare **Aggiungi trigger** nella barra degli strumenti, quindi selezionare **Attiva adesso**. Nella pagina **Pipeline Run** (Esecuzione di pipeline) selezionare **Fine**.  
1. Passare alla scheda **monitoraggio** che si trova nella barra laterale sinistra. Viene visualizzata un'esecuzione della pipeline attivata da un trigger manuale. È possibile usare i collegamenti nella colonna **azioni** per visualizzare i dettagli dell'attività e per eseguire di nuovo la pipeline.
1. Per visualizzare le esecuzioni di attività associate all'esecuzione della pipeline, selezionare il collegamento **View Activity Runs** (Visualizza le esecuzioni di attività) nella colonna **Azioni**. In questo esempio è presente una sola attività, quindi nell'elenco viene visualizzata una sola voce. Per informazioni dettagliate sull'operazione di copia, selezionare il collegamento **Dettagli** (icona a forma di occhiali) nella colonna **Azioni**. Selezionare **Pipeline Runs** (Esecuzioni di pipeline) in alto per tornare alla visualizzazione delle esecuzioni. Per aggiornare la visualizzazione, selezionare **Aggiorna**.
1. Verificare che i dati siano scritti correttamente nel pool SQL.


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'integrazione dei dati per l'analisi delle sinapsi, vedere l'articolo inserimento di [dati in Azure Data Lake storage Gen2](data-integration-data-lake.md) .
