---
title: Caricare dati da Office 365 tramite Azure Data Factory
description: Usare Azure Data Factory per copiare dati da Office 365
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/22/2018
ms.author: jingwang
ms.openlocfilehash: 3422176ed89b7f575c11cc40e5be8420da0018b0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415808"
---
# <a name="load-data-from-office-365-by-using-azure-data-factory"></a>Caricare dati da Office 365 tramite Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Questo articolo illustra come usare Data Factory per _caricare dati da Office 365 in un archivio BLOB di Azure_. È possibile seguire una procedura simile per copiare i dati in Azure Data Lake Gen1 o Gen2. Fare riferimento all'[articolo sul connettore di Office 365](connector-office-365.md) per informazioni generali sull'operazione di copia di dati da Office 365.

## <a name="create-a-data-factory"></a>Creare una data factory

1. Nel menu a sinistra selezionare **Crea una risorsa** > **Analytics** > **Data Factory:** 
   
   ![Selezione di Data Factory nel riquadro "Nuovo"](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Nella pagina **Nuova data factory** specificare i valori per i campi mostrati nell'immagine seguente:
      
   ![Pagina Nuova data factory](./media/load-office-365-data/new-azure-data-factory.png)
 
    * **Nome**: immettere un nome univoco globale per la data factory di Azure. Se viene visualizzato l'errore "Nome data factory *LoadFromOffice365Demo* non è disponibile", immettere un nome diverso per la data factory. È ad esempio possibile usare il nome _**nomeutente**_**LoadFromOffice365Demo**. Riprovare a creare la data factory. Per informazioni sulle regole di denominazione per gli elementi di Data Factory, vedere [Azure Data Factory - Regole di denominazione](naming-rules.md).
    * **Sottoscrizione**: selezionare la sottoscrizione di Azure in cui creare la data factory. 
    * **Gruppo di risorse**: selezionare un gruppo di risorse esistente nell'elenco a discesa oppure selezionare l'opzione **Crea nuovo** e immettere il nome di un gruppo di risorse. Per informazioni sui gruppi di risorse, vedere l'articolo relativo all'[uso di gruppi di risorse per la gestione delle risorse di Azure](../azure-resource-manager/management/overview.md).  
    * **Versione**: selezionare **V2**.
    * **Località**: selezionare la località per la data factory. Nell'elenco a discesa vengono mostrate solo le località supportate. Gli archivi dati usati dalla data factory possono trovarsi in altre località e aree. Questi archivi dati includono Azure Data Lake Store, Archiviazione di Azure, il database SQL di Azure e così via.

3. Selezionare **Create** (Crea).
4. Al termine della creazione, accedere alla data factory. Verrà visualizzata la home page **Data factory**, come mostrato nell'immagine seguente:
   
   ![Home page di Data factory](./media/load-office-365-data/data-factory-home-page.png)

5. Selezionare il riquadro **Crea e monitora** per avviare l'applicazione Integrazione dati in una scheda separata.

## <a name="create-a-pipeline"></a>Creare una pipeline

1. Nella pagina "Iniziamo" selezionare **Crea pipeline**.
 
    ![Creare una pipeline](./media/load-office-365-data/create-pipeline-entry.png)

2. Nella **scheda Generale** della pipeline immettere "CopyPipeline" come **Nome** della pipeline.

3. Nella casella degli strumenti Activities (Attività) > categoria Move & Transform (Sposta e trasforma) > trascinare l'attività **Copia** dalla casella degli strumenti nell'area di progettazione della pipeline. Specificare "CopyFromOffice365ToBlob" come nome dell'attività.

### <a name="configure-source"></a>Configurare l'origine

1. Passare alla pipeline > **scheda Origine**, fare clic su **+ Nuovo** per creare un set di dati di origine. 

2. Nella finestra Nuovo set di dati selezionare **Office 365**e quindi **Continua**.
 
3. È ora disponibili nella scheda Configurazione attività di copia. Fare clic sul pulsante **Modifica** accanto al set di dati di Office 365 per continuare la configurazione dei dati.

    ![Configurazione generale del set di dati di Office 365](./media/load-office-365-data/transition-to-edit-dataset.png)
 
4. Verrà aperta una nuova scheda per il set di dati di Office 365. Nella **scheda Generale** nella parte inferiore della finestra Proprietà immettere "SourceOffice365Dataset" come Nome.
 
5. Passare alla **scheda Connessione** della finestra Proprietà. Fare clic su **+ Nuovo** accanto alla casella di testo Servizio collegato.

6. Nella finestra Nuovo servizio collegato immettere "Office365LinkedService" come nome, immettere l'ID entità servizio e la chiave dell'entità servizio, quindi testare la connessione e selezionare **Crea** per distribuire il servizio collegato.

    ![Nuovo servizio collegato di Office 365](./media/load-office-365-data/new-office-365-linked-service.png)
 
7. Dopo aver creato il servizio collegato, si torna alle impostazioni del set di dati. Accanto a **Tabella**, scegliere la freccia in giù per espandere l'elenco dei set di dati di Office 365 disponibili e scegliere "BasicDataSet_v0. Message_v0" dall'elenco a discesa:

    ![Configurazione della tabella del set di dati di Office 365](./media/load-office-365-data/edit-dataset.png)

8. Tornare ora alla scheda Origine **della pipeline** > per continuare a configurare proprietà aggiuntive per l'estrazione dei dati di Office 365.Now go back to the pipeline**Source tab** to continue configuring additional properties for Office 365 data extraction.  L'ambito utente e il filtro dell'ambito utente sono predicati facoltativi che è possibile definire per limitare i dati che si desidera estrarre da Office 365. Vedere la sezione Proprietà del set di dati di [Office 365](https://docs.microsoft.com/azure/data-factory/connector-office-365#dataset-properties) per informazioni su come configurare queste impostazioni.

9. È necessario scegliere uno dei filtri data e fornire i valori dell'ora di inizio e di fine.

10. Fare clic sulla scheda Importa schema per importare lo schema per il set di dati Message.Click on the **Import Schema** tab to import the schema for Message dataset.

    ![Configurazione dello schema del set di dati di Office 365](./media/load-office-365-data/edit-source-properties.png)

### <a name="configure-sink"></a>Configurare il sink

1. Passare alla pipeline > **scheda Sink** e selezionare **+ Nuovo** per creare un set di dati sink.
 
2. Nella finestra Nuovo set di dati, si noti che solo le destinazioni supportate vengono selezionate durante la copia da Office 365. Selezionare **Archiviazione BLOB**di Azure , formato binario e quindi **Continua**.  In questa esercitazione si copiano i dati di Office 365 in un archivio BLOB di Azure.

3. Fare clic sul pulsante **Modifica** accanto al set di dati di Archiviazione BLOB di Azure per continuare la configurazione dei dati.

4. Nella scheda **Generale** della Finestra Proprietà immettere "OutputBlobDataset" in Nome.

5. Passare alla **scheda Connessione** della finestra Proprietà. Selezionare + Nuovo accanto alla casella di testo **Servizio collegato**.

6. Nella finestra Nuovo servizio collegato immettere "AzureStorageLinkedService" come nome, selezionare "Service Principal" dall'elenco a discesa dei metodi di autenticazione, immettere l'endpoint del servizio, il tenant, l'ID entità servizio e la chiave dell'entità servizio, quindi selezionare Salva per distribuire il servizio collegato.  Fare riferimento [qui](connector-azure-blob-storage.md#service-principal-authentication) per informazioni su come configurare l'autenticazione dell'entità servizio per Archiviazione BLOB di Azure.

    ![Nuovo servizio collegato BLOB](./media/load-office-365-data/configure-blob-linked-service.png)


## <a name="validate-the-pipeline"></a>Convalidare la pipeline

Per convalidare la pipeline, selezionare **Convalida** dalla barra degli strumenti.

È possibile visualizzare il codice JSON associato alla pipeline facendo clic su Codice in alto a destra.

## <a name="publish-the-pipeline"></a>Pubblicare la pipeline

Nella barra degli strumenti superiore selezionare l'azione **Publish All** (Pubblica tutto). Questa azione pubblica le entità create (set di dati e pipeline) in Data Factory.

![Pubblicare modifiche](./media/load-office-365-data/publish-changes.png) 

## <a name="trigger-the-pipeline-manually"></a>Attivare manualmente la pipeline

Selezionare **Aggiungi trigger** nella barra degli strumenti, quindi selezionare **Attiva adesso**. Nella pagina Pipeline Run (Esecuzione di pipeline) selezionare **Fine**. 

## <a name="monitor-the-pipeline"></a>Monitorare la pipeline

Passare alla scheda **Monitoraggio** a sinistra. Viene visualizzata un'esecuzione della pipeline attivata da un trigger manuale. È possibile utilizzare i collegamenti nella colonna **Azioni** per visualizzare i dettagli dell'attività e rieseguire la pipeline.

![Monitorare la pipeline](./media/load-office-365-data/pipeline-status.png) 

Per visualizzare le esecuzioni di attività associate all'esecuzione della pipeline, selezionare il collegamento **View Activity Runs** (Visualizza le esecuzioni di attività) nella colonna Azioni. In questo esempio è presente una sola attività, quindi nell'elenco viene visualizzata una sola voce. Per informazioni dettagliate sull'operazione di copia, selezionare il collegamento **Dettagli** (icona a forma di occhiali) nella colonna Azioni.

![Monitorare l'attività](./media/load-office-365-data/activity-status.png) 

Se questa è la prima volta che si richiedono dati per questo contesto (una combinazione di quale tabella di dati accede, quale account di destinazione è il caricamento dei dati e quale identità utente effettua la richiesta di accesso ai dati), verrà visualizzato lo stato dell'attività di copia come **In corso**e solo quando si fa clic sul collegamento "Dettagli" in Azioni verrà visualizzato lo stato **RequesetingConsent**.  Un membro del gruppo dei responsabili dell'approvazione dell'accesso ai dati deve approvare la richiesta in Privileged Access Management prima che l'estrazione dei dati prosegua.

_Stato come consenso richiesto:_
![Dettagli sull'esecuzione dell'attività - consenso della richiesta](./media/load-office-365-data/activity-details-request-consent.png) 

_Stato come estrazione dati:_

![Dettagli esecuzione attività - estrazione dati](./media/load-office-365-data/activity-details-extract-data.png) 

Una volta fornito il consenso, l'estrazione dei dati continuerà e, dopo qualche tempo, l'esecuzione della pipeline verrà visualizzata come completata.

![Monitorare la pipeline - esito positivo](./media/load-office-365-data/pipeline-monitoring-succeeded.png) 

Passare ora all'archivio BLOB di Azure di destinazione e verificare che i dati di Office 365 siano stati estratti in formato binario.

## <a name="next-steps"></a>Passaggi successivi

Leggere l'articolo seguente per altre informazioni sul supporto di Azure SQL Data Warehouse: 

> [!div class="nextstepaction"]
>[Connettore di Office 365](connector-office-365.md)