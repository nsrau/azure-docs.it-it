---
title: "Esercitazione: Creare una pipeline con l&quot;attività di copia usando Visual Studio | Documentazione Microsoft"
description: "In questa esercitazione viene creata una pipeline di Azure Data Factory con un&quot;attività di copia usando Visual Studio."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 1751185b-ce0a-4ab2-a9c3-e37b4d149ca3
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/02/2017
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: 5e6ffbb8f1373f7170f87ad0e345a63cc20f08dd
ms.openlocfilehash: 39824fa66dee9f1bd57687e59ece97f4f4636b7d
ms.lasthandoff: 03/24/2017


---
# <a name="tutorial-create-a-pipeline-with-copy-activity-using-visual-studio"></a>Esercitazione: Creare una pipeline con l’attività Copia utilizzando Visual Studio
> [!div class="op_single_selector"]
> * [Panoramica e prerequisiti](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Copia guidata](data-factory-copy-data-wizard-tutorial.md)
> * [Portale di Azure](data-factory-copy-activity-tutorial-using-azure-portal.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Modello di Azure Resource Manager](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [API REST](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [API .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)
> 
> 

Questa esercitazione mostra come creare e monitorare un'istanza di Azure Data Factory con Visual Studio. La pipeline nella data factory usa un'attività di copia per copiare i dati dall'archivio BLOB di Azure al database SQL di Azure.

> [!NOTE]
> La pipeline di dati in questa esercitazione copia i dati da un archivio dati di origine a un archivio dati di destinazione. Non trasforma i dati di input per produrre dati di output. Per un'esercitazione su come trasformare i dati usando Azure Data Factory, vedere [Esercitazione: Creare la prima pipeline per elaborare i dati usando il cluster Hadoop](data-factory-build-your-first-pipeline.md).
> 
> È possibile concatenare due attività, ovvero eseguire un'attività dopo l'altra, impostando il set di dati di output di un'attività come set di dati di input di altre attività. Per informazioni dettagliate, vedere [Pianificazione ed esecuzione con Data Factory](data-factory-scheduling-and-execution.md).

Di seguito sono elencati i passaggi da eseguire in questa esercitazione:

1. Creare due servizi collegati: **AzureStorageLinkedService1** e **AzureSqlinkedService1**. 
   
    AzureStorageLinkedService1 collega una risorsa di archiviazione di Azure e AzureSqlLinkedService1 collega un database SQL di Azure alla data factory **ADFTutorialDataFactoryVS**. I dati di input per la pipeline si trovano in un contenitore BLOB nell'archivio BLOB di Azure e i dati di output vengono archiviati in una tabella nel database SQL di Azure. Questi due archivi dati vengono quindi aggiunti alla data factory come servizi collegati.
2. Creare due set di dati, **InputDataset** e **OutputDataset**, che rappresentano i dati di input/output memorizzati negli archivi dati. 
   
    Per InputDataset, specificare il contenitore BLOB che contiene un BLOB con i dati di origine. Per OutputDataset, specificare la tabella SQL che contiene i dati di output. Specificare anche altre proprietà come struttura, disponibilità e criteri.
3. Creare una pipeline denominata **ADFTutorialPipeline** in ADFTutorialDataFactoryVS. 
   
    La pipeline include un'**attività di copia** che copia i dati di input dal BLOB di Azure e li inserisce nella tabella di output di Azure SQL. L'attività di copia esegue lo spostamento dei dati in Azure Data Factory e si basa su un servizio disponibile a livello globale che può copiare dati tra diversi archivi dati in modo sicuro, affidabile e scalabile. Per informazioni dettagliate sull'attività di copia, vedere [Attività di spostamento dei dati](data-factory-data-movement-activities.md) . 
4. Creare una data factory denominata **VSTutorialFactory**. Distribuire la data factory e tutte le entità di Data Factory, ovvero i servizi collegati, le tabelle e la pipeline.    

## <a name="prerequisites"></a>Prerequisiti
1. Vedere la [panoramica dell'esercitazione](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) ed eseguire i passaggi relativi ai **prerequisiti** . 
2. Per potere pubblicare entità di Data Factory in Azure Data Factory, è necessario essere un **amministratore della sottoscrizione di Azure** .  
3. È necessario disporre dei seguenti prodotti installati nel computer in uso: 
   * Visual Studio 2013 o Visual Studio 2015
   * Download di Azure SDK per Visual Studio 2013 o Visual Studio 2015. Passare alla [pagina di download di Azure](https://azure.microsoft.com/downloads/) e fare clic su **VS 2013** o **VS 2015** nella sezione **.NET**.
   * Scaricare il plug-in Azure Data Factory più recente per Visual Studio: [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) o [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005). È anche possibile aggiornare il plug-in nel modo seguente: dal menu scegliere **Strumenti** -> **Estensioni e aggiornamenti** -> **Online** -> **Visual Studio Gallery** -> **Microsoft Azure Data Factory Tools for Visual Studio** -> **Aggiorna**.

## <a name="create-visual-studio-project"></a>Creare un progetto di Visual Studio
1. Avviare **Visual Studio 2013**. Fare clic su **File**, scegliere **Nuovo** e quindi fare clic su **Progetto**. Si dovrebbe vedere la finestra di dialogo **Nuovo progetto** .  
2. Nella finestra di dialogo **Nuovo progetto** selezionare il modello **DataFactory** e fare clic su **Progetto data factory vuoto**. Se il modello DataFactory non è visibile, chiudere Visual Studio, installare Azure SDK per Visual Studio 2013 e riaprire Visual Studio.  
   
    ![Finestra di dialogo Nuovo progetto](./media/data-factory-copy-activity-tutorial-using-visual-studio/new-project-dialog.png)
3. Immettere un **nome** per il progetto, un **percorso** e un nome per la **soluzione** e fare clic su **OK**.
   
    ![Esplora soluzioni](./media/data-factory-copy-activity-tutorial-using-visual-studio/solution-explorer.png)    

## <a name="create-linked-services"></a>Creazione di servizi collegati
I servizi collegati collegano archivi dati o servizi di calcolo a una data factory di Azure. Vedere gli [archivi dati supportati](data-factory-data-movement-activities.md#supported-data-stores-and-formats) per tutte le origini e i sink supportati dall'attività di copia. Per l'elenco di servizi di calcolo supportati da Data Factory, vedere [Servizi collegati di calcolo](data-factory-compute-linked-services.md) . Questa esercitazione non prevede l'uso di servizi di calcolo. 

In questo passaggio vengono creati due servizi collegati: **AzureStorageLinkedService1** e **AzureSqlLinkedService1**. Il servizio collegato AzureStorageLinkedService1 collega un account di archiviazione di Azure e AzureSqlLinkedService collega un database SQL di Azure alla data factory **ADFTutorialDataFactory**. 

### <a name="create-the-azure-storage-linked-service"></a>Creare il servizio collegato di archiviazione di Azure
1. Fare clic con il pulsante destro del mouse su **Servizi collegati** in Esplora soluzioni, scegliere **Aggiungi** e fare clic su **Nuovo elemento**.      
2. Nella finestra di dialogo **Aggiungi nuovo elemento** selezionare **Servizio collegato Archiviazione di Azure** nell'elenco e fare clic su **Aggiungi**. 
   
    ![Nuovo servizio collegato](./media/data-factory-copy-activity-tutorial-using-visual-studio/new-linked-service-dialog.png)
3. Sostituire `<accountname>` e `<accountkey>`* con il nome dell'account di archiviazione di Azure e la relativa chiave. 
   
    ![Servizio collegato Archiviazione di Azure](./media/data-factory-copy-activity-tutorial-using-visual-studio/azure-storage-linked-service.png)
4. Salvare il file **AzureStorageLinkedService1.json** .

> Per informazioni dettagliate sulle proprietà JSON, vedere la sezione relativa allo [spostamento di dati da o in BLOB di Azure](data-factory-azure-blob-connector.md#azure-storage-linked-service) .
> 
> 

### <a name="create-the-azure-sql-linked-service"></a>Creare il servizio collegato SQL di Azure
1. Fare nuovamente clic con il pulsante destro del mouse sul nodo **Servizi collegati** in **Esplora soluzioni**, scegliere **Aggiungi** e fare clic su **Nuovo elemento**. 
2. Questa volta selezionare **Servizio collegato SQL Azure** e fare clic su **Aggiungi**. 
3. Nel file **AzureSqlLinkedService1.json** sostituire `<servername>`, `<databasename>`, `<username@servername>` e `<password>` con i nomi del server di Azure SQL e del database, l'account utente e la password.    
4. Salvare il file **AzureSqlLinkedService1.json** . 

> [!NOTE]
> Per informazioni dettagliate sulle proprietà JSON, vedere la sezione relativa allo [spostamento di dati da o nel database SQL di Azure](data-factory-azure-sql-connector.md#linked-service-properties) .
> 
> 

## <a name="create-datasets"></a>Creare set di dati
Nel passaggio precedente sono stati creati i servizi collegati **AzureStorageLinkedService1** e **AzureSqlLinkedService1** per collegare un account di archiviazione di Azure e un database SQL di Azure alla data factory **ADFTutorialDataFactory**. In questo passaggio vengono definiti due set di dati, **InputDataset** e **OutputDataset**, che rappresentano i dati di input/output memorizzati negli archivi dati a cui fanno riferimento rispettivamente AzureStorageLinkedService1 e AzureSqlLinkedService1. Per InputDataset, specificare il contenitore BLOB che contiene un BLOB con i dati di origine. Per OutputDataset, specificare la tabella SQL che contiene i dati di output.

### <a name="create-input-dataset"></a>Creare set di dati di input
In questo passaggio viene creato un set di dati denominato **InputDataset** che punta a un contenitore BLOB in Archiviazione di Azure rappresentato dal servizio collegato **AzureStorageLinkedService1**. Una tabella è un set di dati rettangolare e attualmente è l'unico tipo di set di dati supportato. 

1. Fare clic con il pulsante destro del mouse su **Tabelle** in **Esplora soluzioni**, scegliere **Aggiungi** e fare clic su **Nuovo elemento**.
2. Nella finestra di dialogo **Aggiungi nuovo elemento** selezionare **BLOB di Azure** e fare clic su **Aggiungi**.   
3. Sostituire il testo JSON con il testo seguente e salvare il file **AzureBlobLocation1.json** . 

  ```json   
  {
    "name": "InputDataset",
    "properties": {
      "structure": [
        {
          "name": "FirstName",
          "type": "String"
        },
        {
          "name": "LastName",
          "type": "String"
        }
      ],
      "type": "AzureBlob",
      "linkedServiceName": "AzureStorageLinkedService1",
      "typeProperties": {
        "folderPath": "adftutorial/",
        "format": {
          "type": "TextFormat",
          "columnDelimiter": ","
        }
      },
      "external": true,
      "availability": {
        "frequency": "Hour",
        "interval": 1
      }
    }
  }
  ``` 
    Tenere presente quanto segue: 
   
   * Il set di dati **type** è impostato su **AzureBlob**.
   * L'oggetto **linkedServiceName** è impostato su **AzureStorageLinkedService**. Questo servizio collegato è stato creato nel passaggio 2.
   * L'oggetto **folderPath** è impostato sul contenitore **adftutorial**. È anche possibile specificare il nome di un BLOB all'interno della cartella usando la proprietà **fileName** . Poiché non si specifica il nome del BLOB, i dati da tutti i BLOB nel contenitore sono considerati come dati di input.  
   * L'oggetto **type** di format è impostato su **TextFormat**.
   * Nel file di testo sono presenti due campi, **FirstName** e **LastName**, separati da una virgola (**columnDelimiter**).    
   * L'oggetto **availability** è impostato su **hourly**. L'oggetto **frequency** è impostato su **hour** e l'oggetto **interval** è impostato su **1**. Quindi, il servizio Data Factory cerca i dati di input ogni ora nella cartella radice del contenitore BLOB**adftutorial**specificato. 
   
   Se non si specifica **fileName** per un set di dati di **input**, tutti i file e i BLOB della cartella di input **folderPath** vengono considerati input. Se si specifica un oggetto fileName nel JSON, solo il file/BLOB specificato viene considerato un input.
   
   Se non è stato specificato **fileName** per una **tabella di output**, i file generati in **folderPath** vengono denominati con il seguente formato: Data.&lt;Guid&gt;.txt (ad esempio: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).
   
   Per impostare **folderPath** e **fileName** dinamicamente in base all'ora **SliceStart**, usare la proprietà **partitionedBy**. Nell'esempio seguente folderPath usa Year, Month e Day dall'oggetto SliceStart (ora di inizio della sezione elaborata), mentre fileName usa Hour dall'oggetto SliceStart. Se viene generata una sezione per 2016-09-20T08:00:00, ad esempio, folderName è impostato su wikidatagateway/wikisampledataout/2016/09/20 e fileName è impostato su 08.csv. 
  
    ```json   
    "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy": 
    [
        { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
    ```
            
> [!NOTE]
> Per informazioni dettagliate sulle proprietà JSON, vedere la sezione relativa allo [spostamento di dati da o in BLOB di Azure](data-factory-azure-blob-connector.md#dataset-properties) .
> 
> 

### <a name="create-output-dataset"></a>Creare il set di dati di output
In questo passaggio si crea un set di dati di output denominato **OutputDataset**. Questo set di dati punta a una tabella SQL nel database SQL di Azure rappresentato da **AzureSqlLinkedService1**. 

1. Fare nuovamente clic con il pulsante destro del mouse su **Tabelle** in **Esplora soluzioni**, scegliere **Aggiungi** e fare clic su **Nuovo elemento**.
2. Nella finestra di dialogo **Aggiungi nuovo elemento** selezionare **Azure SQL** e fare clic su **Aggiungi**. 
3. Sostituire il testo JSON con il testo JSON seguente e salvare il file **AzureSqlTableLocation1.json** .

    ```json
    {
     "name": "OutputDataset",
     "properties": {
       "structure": [
         {
           "name": "FirstName",
           "type": "String"
         },
         {
           "name": "LastName",
           "type": "String"
         }
       ],
       "type": "AzureSqlTable",
       "linkedServiceName": "AzureSqlLinkedService1",
       "typeProperties": {
         "tableName": "emp"
       },
       "availability": {
         "frequency": "Hour",
         "interval": 1
       }
     }
    }
    ```
   
    Tenere presente quanto segue: 
   
   * Il set di dati **type** è impostato su **AzureSQLTable**.
   * L'oggetto **linkedServiceName** è impostato su **AzureSqlLinkedService**. Questo servizio collegato è stato creato nel passaggio 2.
   * L'oggetto **tablename** è impostato su **emp**.
   * La tabella emp del database include tre colonne: **ID**, **FirstName** e **LastName**. ID è una colonna Identity, quindi in questo caso è necessario specificare solo **FirstName** e **LastName**.
   * L'oggetto **availability** è impostato su **hourly**. L'oggetto **frequency** è impostato su **hour** e l'oggetto **interval** è impostato su **1**.  Il servizio Data Factory genera una porzione di dati di output ogni ora nella tabella **emp** nel database SQL di Azure.

> [!NOTE]
> Per informazioni dettagliate sulle proprietà JSON, vedere la sezione relativa allo [spostamento di dati da o nel database SQL di Azure](data-factory-azure-sql-connector.md#linked-service-properties) .
> 
> 

## <a name="create-pipeline"></a>Creare una pipeline
Finora sono stati creati i servizi collegati e le tabelle di input/output. Creare ora una pipeline con un' **attività di copia** per copiare i dati dal BLOB di Azure al database SQL di Azure. 

1. Fare clic con il pulsante destro del mouse su **Pipeline** in **Esplora soluzioni**, scegliere **Aggiungi** e fare clic su **Nuovo elemento**.  
2. Selezionare **Copia pipeline dati** nella finestra di dialogo **Aggiungi nuovo elemento** e fare clic su **Aggiungi**. 
3. Sostituire il codice JSON con il codice JSON seguente e salvare il file **CopyActivity1.json** .

    ```json   
    {
     "name": "ADFTutorialPipeline",
     "properties": {
       "description": "Copy data from a blob to Azure SQL table",
       "activities": [
         {
           "name": "CopyFromBlobToSQL",
           "type": "Copy",
           "inputs": [
             {
               "name": "InputDataset"
             }
           ],
           "outputs": [
             {
               "name": "OutputDataset"
             }
           ],
           "typeProperties": {
             "source": {
               "type": "BlobSource"
             },
             "sink": {
               "type": "SqlSink",
               "writeBatchSize": 10000,
               "writeBatchTimeout": "60:00:00"
             }
           },
           "Policy": {
             "concurrency": 1,
             "executionPriorityOrder": "NewestFirst",
             "style": "StartOfInterval",
             "retry": 0,
             "timeout": "01:00:00"
           }
         }
       ],
       "start": "2015-07-12T00:00:00Z",
       "end": "2015-07-13T00:00:00Z",
       "isPaused": false
     }
    }
    ```   
   Tenere presente quanto segue:
   
   * Nella sezione delle attività esiste una sola attività con l'oggetto **type** impostato su **Copy**.
   * L'input per l'attività è impostato su **InputDataset** e l'output è impostato su **OutputDataset**.
   * Nella sezione **typeProperties** vengono specificati **BlobSource** come tipo di origine e **SqlSink** come tipo di sink.
   
   Sostituire il valore della proprietà **start** con il giorno corrente e il valore di **end** con il giorno successivo. È possibile specificare solo la parte relativa alla data e ignorare la parte relativa all'ora, ad esempio "2016-02-03", che equivale a "2016-02-03T00:00:00Z".
   
   Per la data e ora di inizio è necessario usare il [formato ISO](http://en.wikipedia.org/wiki/ISO_8601), ad esempio 2016-10-14T16:32:41Z. Il valore di **end** è facoltativo, ma in questa esercitazione viene usato. 
   
   Se non si specifica alcun valore per la proprietà **end**, il valore verrà calcolato come "**start + 48 hours**". Per eseguire la pipeline illimitatamente, specificare **9999-09-09** come valore per la proprietà **end**.
   
   Nell'esempio precedente sono visualizzate 24 sezioni di dati, perché viene generata una sezione di dati ogni ora.

## <a name="publishdeploy-data-factory-entities"></a>Pubblicare/Distribuire le entità della data factory
In questo passaggio vengono pubblicate le entità di Data Factory, ovvero i servizi collegati, i set di dati e la pipeline, create in precedenza. Viene anche specificato il nome della nuova data factory da creare che dovrà contenere tali entità.  

1. Fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni e scegliere **Pubblica**. 
2. Se viene visualizzato **Sign in to your Microsoft account** (Accedere all'account Microsoft), nella finestra di dialogo immettere le credenziali per l'account associato alla sottoscrizione di Azure e fare clic su **Accedi**.
3. Verrà visualizzata la finestra di dialogo seguente:
   
   ![Finestra di dialogo Pubblica](./media/data-factory-copy-activity-tutorial-using-visual-studio/publish.png)
4. Nella pagina Configure data factory (Configura data factory), procedere come segue: 
   
   1. Selezionare l’opzione **Crea nuova data factory** .
   2. Immettere **VSTutorialFactory** per **Nome**.  
      
      > [!IMPORTANT]
      > È necessario specificare un nome univoco globale per l'istanza di Azure Data Factory. Se si riceve un messaggio di errore riguardante il nome della data factory durante la pubblicazione, è possibile modificare il nome della data factory, ad esempio, nomeutenteVSTutorialFactory, e provare di nuovo ad effettuare la pubblicazione. Per informazioni sulle regole di denominazione per gli elementi di Data Factory, vedere l'argomento [Azure Data Factory - Regole di denominazione](data-factory-naming-rules.md) .        
      > 
      > 
   3. Selezionare la sottoscrizione di Azure per il campo **Sottoscrizione** .
      
      > [!IMPORTANT]
      > Se non viene visualizzata alcuna sottoscrizione, verificare di aver eseguito l'accesso con un account amministratore o coamministratore della sottoscrizione.  
      > 
      > 
   4. Selezionare il **gruppo di risorse** per la data factory da creare. 
   5. Selezionare l' **area** per la data factory. Nell'elenco a discesa vengono visualizzate solo le aree supportate dal servizio Data Factory.
   6. Fare clic su **Avanti** per passare alla pagina **Pubblica elementi**.
      
       ![Pagina Configure data factory (Configura data factory)](media/data-factory-copy-activity-tutorial-using-visual-studio/configure-data-factory-page.png)   
5. Nella pagina **Pubblica elementi** assicurarsi che tutte le data factory siano selezionate e fare clic su **Avanti** per passare alla pagina **Riepilogo**.
   
   ![Pagina Publish Items (Pubblica elementi)](media/data-factory-copy-activity-tutorial-using-visual-studio/publish-items-page.png)     
6. Esaminare il riepilogo e fare clic su **Avanti** per avviare il processo di distribuzione e visualizzare lo **Stato della distribuzione**.
   
   ![Pagina Riepilogo pubblicazione](media/data-factory-copy-activity-tutorial-using-visual-studio/publish-summary-page.png)
7. Nella pagina **Stato della distribuzione** , è possibile visualizzare lo stato del processo di distribuzione. Fare clic su Finish (Fine) dopo il termine della distribuzione.
 
   ![Pagina Stato distribuzione](media/data-factory-copy-activity-tutorial-using-visual-studio/deployment-status.png)

Tenere presente quanto segue: 

* Se viene visualizzato l'errore "**La sottoscrizione non è registrata per l'uso dello spazio dei nomi Microsoft.DataFactory**", eseguire una di queste operazioni e provare a ripetere la pubblicazione: 
  
  * In Azure PowerShell eseguire questo comando per registrare il provider di Data Factory. 

    ```PowerShell    
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
    ```
    È possibile eseguire questo comando per verificare che il provider di Data Factory sia registrato. 
    
    ```PowerShell
    Get-AzureRmResourceProvider
    ```
  * Accedere usando la sottoscrizione di Azure nel [portale di Azure](https://portal.azure.com) e passare al pannello Data Factory oppure creare un'istanza di Data Factory nel portale di Azure. Questa azione registra automaticamente il provider.
* Il nome di Data Factory può essere registrato come un nome DNS in futuro e pertanto divenire visibile pubblicamente.

> [!IMPORTANT]
> Per creare istanze di Data Factory, è necessario essere un amministratore o un coamministratore della sottoscrizione di Azure.
> 
> 

## <a name="summary"></a>Riepilogo
In questa esercitazione è stata creata una data factory di Azure per copiare dati da un BLOB di Azure a un database SQL Azure. È stato usato Visual Studio per creare la data factory, i servizi collegati, i set di dati e una pipeline. Ecco i passaggi generali eseguiti in questa esercitazione:  

1. Creare un'istanza di Azure **Data Factory**.
2. Creare **servizi collegati**:
   1. Un servizio collegato di **Archiviazione di Azure** per collegare l'account di archiviazione di Azure che include i dati di input.     
   2. Un servizio collegato di **Azure SQL** per collegare il database SQL di Azure che contiene i dati di output. 
3. Creare **set di dati**che descrivono dati di input e dati di output per le pipeline.
4. Creare una **pipeline** con un'**attività di copia** con **BlobSource** come origine e **SqlSink** come sink. 

## <a name="use-server-explorer-to-view-data-factories"></a>Usare Esplora Server per visualizzare le data factory
1. In **Visual Studio** fare clic su **Visualizza** nel menu e fare clic su **Esplora server**.
2. Nella finestra Esplora server espandere **Azure** e **Data factory**. Se viene visualizzata la finestra **Accedi a Visual Studio**, immettere l'**account** associato alla sottoscrizione di Azure e fare clic su **Continua**. Immettere la **password** e fare clic su **Accedi**. Visual Studio cerca di ottenere le informazioni su tutte le data factory di Azure nella sottoscrizione. Lo stato di questa operazione viene visualizzato nella finestra **Data Factory Task List** (Elenco attività Data Factory).

    ![Esplora server](./media/data-factory-copy-activity-tutorial-using-visual-studio/server-explorer.png)
3. È possibile fare clic con il pulsante destro del mouse su una data factory e scegliere l'opzione per esportare la data factory in un nuovo progetto per creare un progetto di Visual Studio basato su una data factory esistente.

    ![Esportare la data factory in un progetto VS](./media/data-factory-copy-activity-tutorial-using-visual-studio/export-data-factory-menu.png)  

## <a name="update-data-factory-tools-for-visual-studio"></a>Aggiornare gli strumenti di Data factory di Azure per Visual Studio
Per aggiornare gli strumenti di Azure Data Factory per Visual Studio, eseguire queste operazioni:

1. Fare clic su **Strumenti**nel menu e selezionare **Estensioni e aggiornamenti**. 
2. Selezionare **Aggiornamenti** nel riquadro sinistro e quindi selezionare **Visual Studio Gallery**.
3. Selezionare **Azure Data Factory tools for Visual Studio** e fare clic su **Aggiorna**. Se questa voce non è visibile, si dispone già della versione più recente dello strumento. 

Per istruzioni su come usare il portale di Azure per monitorare la pipeline e i set di dati creati in questa esercitazione, vedere [Monitorare e gestire le pipeline di Azure Data Factory](data-factory-copy-activity-tutorial-using-azure-portal.md#monitor-pipeline) .

## <a name="see-also"></a>Vedere anche
| Argomento | Descrizione |
|:--- |:--- |
| [Pipeline](data-factory-create-pipelines.md) |Questo articolo fornisce informazioni sulle pipeline e le attività in Azure Data Factory. |
| [Set di dati](data-factory-create-datasets.md) |Questo articolo fornisce informazioni sui set di dati in Azure Data Factory. |
| [Pianificazione ed esecuzione](data-factory-scheduling-and-execution.md) |Questo articolo descrive gli aspetti di pianificazione ed esecuzione del modello applicativo di Data factory di Azure. |
| [Monitorare e gestire le pipeline con l'app di monitoraggio](data-factory-monitor-manage-app.md) |Questo articolo descrive come monitorare, gestire ed eseguire il debug delle pipeline usando l'app di monitoraggio e gestione. |


