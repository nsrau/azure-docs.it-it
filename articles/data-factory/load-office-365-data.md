---
title: Caricare dati da Office 365 tramite Azure Data Factory | Microsoft Docs
description: Usare Azure Data Factory per copiare dati da Office 365
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/22/2018
ms.author: jingwang
ms.openlocfilehash: 72b2c630c514190d2c63e3dd7faa29b0b3abab19
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2018
ms.locfileid: "49944562"
---
# <a name="load-data-from-office-365-by-using-azure-data-factory"></a>Caricare dati da Office 365 tramite Azure Data Factory

Questo articolo illustra come usare Data Factory per _caricare dati da Office 365 in un archivio BLOB di Azure_. È possibile seguire una procedura simile per copiare i dati in Azure Data Lake Gen1 o Gen2. Fare riferimento all'[articolo sul connettore di Office 365](connector-office-365.md) per informazioni generali sull'operazione di copia di dati da Office 365.

## <a name="create-a-data-factory"></a>Creare una data factory

1. Nel menu a sinistra scegliere **Nuovo** > **Dati e analisi** > **Data factory**: 
   
   ![Creare una nuova data factory](./media/load-office-365-data/new-azure-data-factory-menu.png)
2. Nella pagina **Nuova data factory** specificare i valori per i campi mostrati nell'immagine seguente:
      
   ![Pagina Nuova data factory](./media/load-office-365-data/new-azure-data-factory.png)
 
    * **Nome**: immettere un nome univoco globale per la data factory di Azure. Se viene visualizzato l'errore "Il nome \"LoadFromOffice365Demo\" per la data factory non è disponibile", immettere un altro nome per la data factory. È ad esempio possibile usare il nome _**nomeutente**_**LoadFromOffice365Demo**. Riprovare a creare la data factory. Per informazioni sulle regole di denominazione per gli elementi di Data Factory, vedere [Azure Data Factory - Regole di denominazione](naming-rules.md).
    * **Sottoscrizione**: selezionare la sottoscrizione di Azure in cui creare la data factory. 
    * **Gruppo di risorse**: selezionare un gruppo di risorse esistente nell'elenco a discesa oppure selezionare l'opzione **Crea nuovo** e immettere il nome di un gruppo di risorse. Per informazioni sui gruppi di risorse, vedere l'articolo relativo all'[uso di gruppi di risorse per la gestione delle risorse di Azure](../azure-resource-manager/resource-group-overview.md).  
    * **Versione**: selezionare **V2**.
    * **Località**: selezionare la località per la data factory. Nell'elenco a discesa vengono mostrate solo le località supportate. Gli archivi dati usati dalla data factory possono trovarsi in altre località e aree. Questi archivi dati includono Azure Data Lake Store, Archiviazione di Azure, il database SQL di Azure e così via.

3. Selezionare **Create**.
4. Al termine della creazione, accedere alla data factory. Verrà visualizzata la home page **Data factory**, come mostrato nell'immagine seguente:
   
   ![Home page di Data factory](./media/load-office-365-data/data-factory-home-page.png)

5. Selezionare il riquadro **Crea e monitora** per avviare l'applicazione Integrazione dati in una scheda separata.

## <a name="create-a-pipeline"></a>Creare una pipeline

1. Nella pagina "Let's get started" (Attività iniziali) selezionare **Create pipeline** (Crea pipeline).
 
    ![Creare una pipeline](./media/load-office-365-data/create-pipeline-entry.png)

2. Nella scheda **Generale** della pipeline immettere "CopyPipeline" nel campo **Nome** della pipeline.

3. Nella casella degli strumenti Activities (Attività) > categoria Move & Transform (Sposta e trasforma) > trascinare l'attività **Copia** dalla casella degli strumenti nell'area di progettazione della pipeline. Specificare "CopyFromOffice365ToBlob" come nome dell'attività.

### <a name="configure-source"></a>Configurare l'origine

1. Passare alla pipeline > **scheda Origine**, fare clic su **+ Nuovo** per creare un set di dati di origine. 

2. Nella finestra Nuovo set di dati selezionare **Office 365** e quindi selezionare **Fine**.

    ![Nuovo set di dati di Office 365](./media/load-office-365-data/new-office-365-dataset.png)
 
3. Verrà aperta una nuova scheda per il set di dati di Office 365. Nella scheda **Generale** nella parte inferiore della Finestra Proprietà immettere "SourceOffice365Dataset" nel campo Nome.

    ![Configurazione generale del set di dati di Office 365](./media/load-office-365-data/config-office-365-dataset-general.png)
 
4. Passare alla scheda **Connessione** della Finestra Proprietà. Fare clic su **+ Nuovo** accanto alla casella di testo Servizio collegato.
 
    ![Configurazione della connessione del set di dati di Office 365](./media/load-office-365-data/config-office-365-dataset-connection.png)

5. Nella finestra New Linked Service (Nuovo servizio collegato) immettere "Office365LinkedService" come nome, immettere l'ID e la chiave dell'entità servizio, quindi selezionare Salva per distribuire il servizio collegato.

    ![Nuovo servizio collegato di Office 365](./media/load-office-365-data/new-office-365-linked-service.png)
 
6. Dopo aver creato il servizio collegato, si torna alle impostazioni del set di dati. Accanto a "Tabella" scegliere la freccia in giù per espandere l'elenco di set di dati di Office 365 disponibili e scegliere "BasicDataSet_v0.Contact_v0":

    ![Configurazione della tabella del set di dati di Office 365](./media/load-office-365-data/config-office-365-dataset-table.png)
 
7. Passare alla scheda **Schema** nella Finestra Proprietà e selezionare **Importa schema**.  Si noti che vengono visualizzati i valori di esempio e lo schema per set di dati di contatto.

    ![Configurazione dello schema del set di dati di Office 365](./media/load-office-365-data/config-office-365-dataset-schema.png)

8. Tornare ora alla pipeline -> scheda Origine e verificare che SourceBlobDataset sia selezionato.
 
### <a name="configure-sink"></a>Configurare il sink

1. Passare alla pipeline > **scheda Sink** e selezionare **+ Nuovo** per creare un set di dati sink.
 
2. Nella finestra Nuovo set di dati si noti che solo le destinazioni supportate vengono selezionate durante la copia da Office 365. Selezionare **Archiviazione BLOB di Azure** e fare clic su **Fine**.  In questa esercitazione si copiano i dati di Office 365 in un archivio BLOB di Azure.

    ![Nuovo set di dati del BLOB](./media/load-office-365-data/new-blob-dataset.png)

4. Nella scheda **Generale** della Finestra Proprietà immettere "OutputBlobDataset" in Nome.

5. Passare alla scheda **Connessione** della Finestra Proprietà. Selezionare **+ Nuovo** accanto alla casella di testo Servizio collegato.

    ![Configurazione della connessione del set di dati BLOB](./media/load-office-365-data/config-blob-dataset-connection.png) 

6. Nella finestra New Linked Service (Nuovo servizio collegato) immettere "AzureStorageLinkedService" come nome, selezionare "Entità servizio" nell'elenco a discesa dei metodi di autenticazione, compilare l'endpoint di servizio, l'ID dell'entità servizio del tenant e la chiave dell'entità servizio, quindi selezionare Salva per distribuire il servizio collegato.  Fare riferimento [qui](connector-azure-blob-storage.md#service-principal-authentication) per informazioni su come configurare l'autenticazione dell'entità servizio per Archiviazione BLOB di Azure.

    ![Nuovo servizio collegato BLOB](./media/load-office-365-data/new-blob-linked-service.png)

7. Dopo aver creato il servizio collegato, si torna alle impostazioni del set di dati. Accanto al percorso del file, selezionare **Sfoglia** per scegliere la cartella di output in cui verranno estratti i dati di Office 365.  In "File Format Settings" (Impostazione formato file), accanto a File Format (Formato file), scegliere "**Formato JSON**" e accanto a File Pattern (Modello file) scegliere "**Set of objects**" (Set di oggetti).

    ![Configurazione del percorso e del formato del set di dati BLOB](./media/load-office-365-data/config-blob-dataset-path-and-format.png) 

8. Tornare alla pipeline -> scheda Sink e verificare che OutputBlobDataset sia selezionato.

## <a name="validate-the-pipeline"></a>Convalidare la pipeline

Per convalidare la pipeline, selezionare **Convalida** dalla barra degli strumenti.

È possibile visualizzare il codice JSON associato alla pipeline facendo clic su Codice in alto a destra.

## <a name="publish-the-pipeline"></a>Pubblicare la pipeline

Nella barra degli strumenti superiore selezionare l'azione **Publish All** (Pubblica tutto). Questa azione pubblica le entità create (set di dati e pipeline) in Data Factory.

![Pubblicare modifiche](./media/load-office-365-data/publish-changes.png) 

## <a name="trigger-the-pipeline-manually"></a>Attivare manualmente la pipeline

Selezionare **Trigger** sulla barra degli strumenti e quindi selezionare **Trigger Now** (Attiva adesso). Nella pagina Pipeline Run (Esecuzione pipeline) selezionare **Fine**. 

## <a name="monitor-the-pipeline"></a>Monitorare la pipeline

Passare alla scheda **Monitoraggio** a sinistra. Viene visualizzata un'esecuzione della pipeline attivata da un trigger manuale. È possibile usare i collegamenti nella colonna **Azioni** per visualizzare i dettagli delle attività ed eseguire di nuovo la pipeline.

![Monitorare la pipeline](./media/load-office-365-data/pipeline-monitoring.png) 

Per visualizzare le esecuzioni di attività associate all'esecuzione della pipeline, selezionare il collegamento **View Activity Runs** (Visualizza le esecuzioni di attività) nella colonna Azioni. In questo esempio è presente una sola attività, quindi nell'elenco viene visualizzata una sola voce. Per informazioni dettagliate sull'operazione di copia, selezionare il collegamento **Dettagli** (icona a forma di occhiali) nella colonna Azioni.

![Monitorare l'attività](./media/load-office-365-data/activity-monitoring.png) 

Se questa è la prima volta che si richiedono dati per questo contesto (una combinazione di quale tabella dati occorre accedere, in quale account di destinazione devono essere caricati i dati e quale identità sta richiedendo l'accesso ai dati), si noterà che lo stato dell'attività di copia è "**In corso**" e solo quando si fa clic sul collegamento "Dettagli" nel riquadro azioni si visualizzerà lo stato "**RequesetingConsent**".  Un membro del gruppo dei responsabili dell'approvazione dell'accesso ai dati deve approvare la richiesta in Privileged Access Management prima che l'estrazione dei dati prosegua.

_Stato come richiesta di consenso:_
![Dettagli esecuzione attività - richiesta consenso](./media/load-office-365-data/activity-details-request-consent.png) 

_Stato come estrazione dati:_

![Dettagli esecuzione attività - estrazione dati](./media/load-office-365-data/activity-details-extract-data.png) 

Dopo avere ottenuto il consenso, l'estrazione dei dati continuerà e dopo qualche minuto l'esecuzione della pipeline risulterà completata.

![Monitorare la pipeline - esito positivo](./media/load-office-365-data/pipeline-monitoring-succeeded.png) 

Passare ora all'archivio BLOB di Azure di destinazione e verificare che i dati di Office 365 siano stati estratti in formato JSON.

## <a name="next-steps"></a>Passaggi successivi

Leggere l'articolo seguente per altre informazioni sul supporto di Azure SQL Data Warehouse: 

> [!div class="nextstepaction"]
>[Connettore di Office 365](connector-office-365.md)