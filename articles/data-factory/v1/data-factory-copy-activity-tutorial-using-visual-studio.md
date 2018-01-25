---
title: "Esercitazione: Creare una pipeline con l'attività di copia usando Visual Studio | Documentazione Microsoft"
description: "In questa esercitazione viene creata una pipeline di Azure Data Factory con un'attività di copia usando Visual Studio."
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
ms.date: 01/22/2018
ms.author: spelluru
robots: noindex
ms.openlocfilehash: 4f517a4be012f88d9b7a5e19042ce22493cfe5f3
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2018
---
# <a name="tutorial-create-a-pipeline-with-copy-activity-using-visual-studio"></a>Esercitazione: Creare una pipeline con l’attività Copia utilizzando Visual Studio
> [!div class="op_single_selector"]
> * [Panoramica e prerequisiti](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Copia guidata](data-factory-copy-data-wizard-tutorial.md)
> * [Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Modello di Azure Resource Manager](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [API REST](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [API .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)
> 
> 

> [!NOTE]
> Questo articolo si applica alla versione 1 del servizio Data Factory, disponibile a livello generale (GA). Se si usa la versione 2 del servizio Data Factory, disponibile in anteprima, vedere l'[esercitazione sull'attività di copia nella documentazione sulla versione 2](../quickstart-create-data-factory-dot-net.md). 

Questo articolo illustra l'uso di Microsoft Visual Studio per creare una data factory con una pipeline che copia i dati da un archivio BLOB di Azure a un database SQL di Azure. Se non si ha familiarità con Azure Data Factory, prima di eseguire questa esercitazione vedere l'articolo [Introduzione ad Azure Data Factory](data-factory-introduction.md).   

In questa esercitazione si crea una pipeline contenente una sola attività: un'attività di copia che copia i dati da un archivio dati supportato a un archivio dati sink supportato. Per un elenco degli archivi dati supportati come origini e sink, vedere gli [archivi dati supportati](data-factory-data-movement-activities.md#supported-data-stores-and-formats). e si basa su un servizio disponibile a livello globale che può copiare dati tra diversi archivi dati in modo sicuro, affidabile e scalabile. Per altre informazioni sull'attività di copia, vedere le [attività di spostamento dei dati](data-factory-data-movement-activities.md).

Una pipeline può includere più attività ed è possibile concatenarne due, ovvero eseguire un'attività dopo l'altra, impostando il set di dati di output di un'attività come set di dati di input dell'altra. Per altre informazioni, vedere [Attività multiple in una pipeline](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline).

> [!NOTE] 
> La pipeline di dati in questa esercitazione copia i dati da un archivio dati di origine a un archivio dati di destinazione. Per un'esercitazione su come trasformare i dati usando Azure Data Factory, vedere [Esercitazione: Creare una pipeline per trasformare i dati usando un cluster Hadoop](data-factory-build-your-first-pipeline.md).

## <a name="prerequisites"></a>prerequisiti
1. Vedere la [panoramica dell'esercitazione](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) ed eseguire i passaggi relativi ai **prerequisiti** .       
2. Per creare istanze di data factory, è necessario essere membri del ruolo [Collaboratore Data factory](../../active-directory/role-based-access-built-in-roles.md#data-factory-contributor) a livello di sottoscrizione/gruppo di risorse.
3. È necessario disporre dei seguenti prodotti installati nel computer in uso: 
   * Visual Studio 2013 o Visual Studio 2015
   * Download di Azure SDK per Visual Studio 2013 o Visual Studio 2015. Passare alla [pagina di download di Azure](https://azure.microsoft.com/downloads/) e fare clic su **VS 2013** o **VS 2015** nella sezione **.NET**.
   * Scaricare il plug-in Azure Data Factory più recente per Visual Studio: [VS 2013](https://visualstudiogallery.msdn.microsoft.com/754d998c-8f92-4aa7-835b-e89c8c954aa5) o [VS 2015](https://visualstudiogallery.msdn.microsoft.com/371a4cf9-0093-40fa-b7dd-be3c74f49005). È anche possibile aggiornare il plug-in nel modo seguente: dal menu scegliere **Strumenti** -> **Estensioni e aggiornamenti** -> **Online** -> **Visual Studio Gallery** -> **Microsoft Azure Data Factory Tools for Visual Studio** -> **Aggiorna**.

## <a name="steps"></a>Passaggi
Di seguito sono elencati i passaggi da eseguire in questa esercitazione:

1. Creare **servizi collegati** nella data factory. In questo passaggio si creano due servizi collegati di tipo Archiviazione di Azure e database SQL di Azure. 
    
    AzureStorageLinkedService collega l'account di archiviazione di Azure alla data factory. Come parte dei [prerequisiti](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) è stato creato un contenitore e sono stati caricati i dati in questo account di archiviazione.   

    AzureSqlLinkedService collega il database SQL di Azure alla data factory. I dati copiati dall'archivio BLOB vengono archiviati in questo database. Come parte dei [prerequisiti](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) è stata creata una tabella SQL in questo database.     
2. Creare **set di dati** di input e di output nella data factory.  
    
    Il servizio collegato Archiviazione di Azure specifica la stringa di connessione usata dal servizio Data Factory in fase di esecuzione per connettersi all'account di archiviazione di Azure. Il set di dati del BLOB di input specifica il contenitore e la cartella che contiene i dati di input.  

    Analogamente, il servizio collegato per il database SQL di Azure specifica la stringa di connessione usata dal servizio Data Factory in fase di esecuzione per connettersi al database SQL di Azure e il set di dati della tabella SQL di output specifica la tabella del database in cui vengono copiati i dati dell'archivio BLOB.
3. Creare una **pipeline** nella data factory. In questo passaggio viene creata una pipeline con un'attività di copia.   
    
    L'attività di copia esegue la copia dei dati da un BLOB nell'archivio BLOB di Azure a una tabella nel database SQL di Azure. È possibile usare un'attività di copia in una pipeline per copiare i dati da qualsiasi origine supportata a qualsiasi destinazione supportata. Per un elenco degli archivi dati supportati, vedere l'articolo sulle [attività di spostamento dei dati](data-factory-data-movement-activities.md#supported-data-stores-and-formats). 
4. Creare una **data factory** di Azure durante la distribuzione delle entità di Data Factory (servizi collegati, set di dati/tabelle e pipeline). 

## <a name="create-visual-studio-project"></a>Creare un progetto di Visual Studio
1. Avviare **Visual Studio 2015**. Fare clic su **File**, scegliere **Nuovo** e quindi fare clic su **Progetto**. Si dovrebbe vedere la finestra di dialogo **Nuovo progetto** .  
2. Nella finestra di dialogo **Nuovo progetto** selezionare il modello **DataFactory** e fare clic su **Progetto data factory vuoto**.  
   
    ![Finestra di dialogo Nuovo progetto](./media/data-factory-copy-activity-tutorial-using-visual-studio/new-project-dialog.png)
3. Specificare il nome del progetto e il percorso e il nome della soluzione e quindi fare clic su **OK**.
   
    ![Controllers\HomeController.cs](./media/data-factory-copy-activity-tutorial-using-visual-studio/solution-explorer.png)    

## <a name="create-linked-services"></a>Creare servizi collegati
Si creano servizi collegati in una data factory per collegare gli archivi dati e i servizi di calcolo alla data factory. In questa esercitazione non si usano servizi di calcolo come Azure HDInsight o Azure Data Lake Analytics, ma due archivi dati di tipo Archiviazione di Azure (origine) e database SQL di Azure (destinazione). 

Di conseguenza, si creano due servizi collegati di tipo AzureStorage e AzureSqlDatabase.  

Il servizio collegato Archiviazione di Azure collega l'account di archiviazione di Azure alla data factory. L'account di archiviazione è quello in cui, come parte dei [prerequisiti](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md), è stato creato un contenitore e sono stati caricati i dati.   

Il servizio collegato SQL di Azure collega il database SQL di Azure alla data factory. I dati copiati dall'archivio BLOB vengono archiviati in questo database. Come parte dei [prerequisiti](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) è stata creata la tabella emp in questo database.

I servizi collegati collegano archivi dati o servizi di calcolo a una data factory di Azure. Vedere gli [archivi dati supportati](data-factory-data-movement-activities.md#supported-data-stores-and-formats) per tutte le origini e i sink supportati dall'attività di copia. Per l'elenco di servizi di calcolo supportati da Data Factory, vedere [Servizi collegati di calcolo](data-factory-compute-linked-services.md) . Questa esercitazione non prevede l'uso di servizi di calcolo. 

### <a name="create-the-azure-storage-linked-service"></a>Creare il servizio collegato di archiviazione di Azure
1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **Servizi collegati**, scegliere **Aggiungi** e quindi fare clic su **Nuovo elemento**.      
2. Nella finestra di dialogo **Aggiungi nuovo elemento** selezionare **Servizio collegato Archiviazione di Azure** nell'elenco e fare clic su **Aggiungi**. 
   
    ![Nuovo servizio collegato](./media/data-factory-copy-activity-tutorial-using-visual-studio/new-linked-service-dialog.png)
3. Sostituire `<accountname>` e `<accountkey>`* con il nome dell'account di archiviazione di Azure e la relativa chiave. 
   
    ![Servizio collegato Archiviazione di Azure](./media/data-factory-copy-activity-tutorial-using-visual-studio/azure-storage-linked-service.png)
4. Salvare il file **AzureStorageLinkedService1.json** .

    Per altre informazioni sulle proprietà JSON nella definizione dei servizi collegati, vedere l'articolo relativo al [connettore di Archiviazione BLOB di Azure](data-factory-azure-blob-connector.md#linked-service-properties).

### <a name="create-the-azure-sql-linked-service"></a>Creare il servizio collegato SQL di Azure
1. Fare nuovamente clic con il pulsante destro del mouse sul nodo **Servizi collegati** in **Esplora soluzioni**, scegliere **Aggiungi** e fare clic su **Nuovo elemento**. 
2. Questa volta selezionare **Servizio collegato SQL Azure** e fare clic su **Aggiungi**. 
3. Nel file **AzureSqlLinkedService1.json** sostituire `<servername>`, `<databasename>`, `<username@servername>` e `<password>` con i nomi del server di Azure SQL e del database, l'account utente e la password.    
4. Salvare il file **AzureSqlLinkedService1.json** . 
    
    Per altre informazioni su queste proprietà JSON, vedere l'articolo relativo al [connettore del database SQL di Azure](data-factory-azure-sql-connector.md#linked-service-properties).


## <a name="create-datasets"></a>Creare set di dati
Nel passaggio precedente sono stati creati servizi collegati per collegare l'account di archiviazione di Azure e un database SQL di Azure alla data factory. In questo passaggio vengono definiti due set di dati denominati InputDataset e OutputDataset, che rappresentano i dati di input e di output memorizzati negli archivi dati a cui fanno riferimento rispettivamente AzureStorageLinkedService1 e AzureSqlLinkedService1.

Il servizio collegato Archiviazione di Azure specifica la stringa di connessione usata dal servizio Data Factory in fase di esecuzione per connettersi all'account di archiviazione di Azure. Il set di dati del BLOB di input (InputDataset) specifica il contenitore e la cartella che contiene i dati di input.  

Analogamente, il servizio collegato per il database SQL di Azure specifica la stringa di connessione usata dal servizio Data Factory in fase di esecuzione per connettersi al database SQL di Azure e il set di dati della tabella SQL di output (OututDataset) specifica la tabella del database in cui vengono copiati i dati dell'archivio BLOB. 

### <a name="create-input-dataset"></a>Creare set di dati di input
In questo passaggio viene creato un set di dati denominato InputDataset che punta a un file BLOB (emp.txt) nella cartella radice di un contenitore BLOB (adftutorial) nella risorsa di archiviazione di Azure rappresentata dal servizio collegato AzureStorageLinkedService1. Se non si specifica un valore per fileName (o lo si ignora), i dati di tutti i BLOB della cartella di input vengono copiati nella destinazione. In questa esercitazione si specifica un valore per fileName. 

In questo contesto si usa il termine "tabelle" invece di "set di dati". Una tabella è un set di dati rettangolare e attualmente è l'unico tipo di set di dati supportato. 

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
    La tabella seguente fornisce le descrizioni delle proprietà JSON usate nel frammento di codice:

    | Proprietà | DESCRIZIONE |
    |:--- |:--- |
    | type | La proprietà type è impostata su **AzureBlob** perché i dati risiedono in un archivio BLOB di Azure. |
    | linkedServiceName | Fa riferimento all'oggetto **AzureStorageLinkedService** creato in precedenza. |
    | folderPath | Specifica il **contenitore** BLOB e la **cartella** che contiene i BLOB di input. In questa esercitazione adftutorial è il contenitore BLOB e la cartella è la cartella radice. | 
    | fileName | Questa proprietà è facoltativa. Se si omette questa proprietà, vengono selezionati tutti i file da folderPath. In questa esercitazione come fileName si specifica **emp.txt** e viene quindi selezionato per l'elaborazione solo tale file. |
    | format -> type |Il file di input è in formato testo, quindi viene usato **TextFormat**. |
    | columnDelimiter | Le colonne nel file di input sono delimitate da **virgola (`,`)**. |
    | frequenza/intervallo | La frequenza è impostata su **Hour** e l'intervallo è impostato su **1**, quindi le sezioni di input sono disponibili con cadenza **oraria**. In altre parole, il servizio Data Factory cerca i dati di input ogni ora nella cartella radice del contenitore BLOB specificato (**adftutorial**). Cerca i dati compresi tra l'ora di inizio e di fine della pipeline e non prima o dopo queste ore.  |
    | external | Questa proprietà è impostata su **true** se i dati non vengono generati da questa pipeline. I dati di input in questa esercitazione sono nel file emp.txt, che non viene generato da questa pipeline, quindi questa proprietà viene impostata su true. |

    Per altre informazioni su queste proprietà JSON, vedere l'articolo relativo al [connettore BLOB di Azure](data-factory-azure-blob-connector.md#dataset-properties).   

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
    La tabella seguente fornisce le descrizioni delle proprietà JSON usate nel frammento di codice:

    | Proprietà | DESCRIZIONE |
    |:--- |:--- |
    | type | La proprietà type viene impostata su **AzureSqlTable** perché i dati vengono copiati in una tabella in un database SQL di Azure. |
    | linkedServiceName | Fa riferimento all'oggetto **AzureSqlLinkedService** creato in precedenza. |
    | tableName | Specifica la **tabella** in cui i dati vengono copiati. | 
    | frequenza/intervallo | La frequenza viene impostata su **Hour** e l'intervallo è **1**, quindi le sezioni di output vengono generate **ogni ora** tra l'ora di inizio e di fine della pipeline e non prima o dopo queste ore.  |

    La tabella emp del database include tre colonne: **ID**, **FirstName** e **LastName**. ID è una colonna Identity, quindi in questo caso è necessario specificare solo **FirstName** e **LastName**.

    Per altre informazioni su queste proprietà JSON, vedere l'articolo relativo al [connettore SQL di Azure](data-factory-azure-sql-connector.md#dataset-properties).

## <a name="create-pipeline"></a>Creare una pipeline
In questo passaggio viene creata una pipeline con un'**attività di copia** che usa **InputDataset** come input e **OutputDataset** come output.

Attualmente, è il set di dati di output a determinare la pianificazione. In questa esercitazione il set di dati di output viene configurato per generare una sezione una volta ogni ora. La pipeline ha un'ora di inizio e un'ora di fine intervallate da un giorno, ovvero 24 ore. Vengono quindi generate dalla pipeline 24 sezioni di set di dati di output. 

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
       "start": "2017-05-11T00:00:00Z",
       "end": "2017-05-12T00:00:00Z",
       "isPaused": false
     }
    }
    ```   
    - Nella sezione delle attività esiste una sola attività con l'oggetto **type** impostato su **Copy**. Per altre informazioni sull'attività di copia, vedere le [attività di spostamento dei dati](data-factory-data-movement-activities.md). Nelle soluzioni Data Factory è anche possibile usare le [attività di trasformazione dei dati](data-factory-data-transformation-activities.md).
    - L'input per l'attività è impostato su **InputDataset** e l'output è impostato su **OutputDataset**. 
    - Nella sezione **typeProperties** vengono specificati **BlobSource** come tipo di origine e **SqlSink** come tipo di sink. Per un elenco completo degli archivi dati supportati dall'attività di copia come origini e sink, vedere gli [archivi dati supportati](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Per informazioni su come usare uno specifico archivio dati supportato come origine/sink, fare clic sul collegamento nella tabella.  
     
    Sostituire il valore della proprietà **start** con il giorno corrente e il valore di **end** con il giorno successivo. È possibile specificare solo la parte relativa alla data e ignorare la parte relativa all'ora, ad esempio "2016-02-03", che equivale a "2016-02-03T00:00:00Z".
     
    Per la data e ora di inizio è necessario usare il [formato ISO](http://en.wikipedia.org/wiki/ISO_8601), ad esempio 2016-10-14T16:32:41Z. Il valore di **end** è facoltativo, ma in questa esercitazione viene usato. 
     
    Se non si specifica alcun valore per la proprietà **end**, il valore verrà calcolato come "**start + 48 hours**". Per eseguire la pipeline illimitatamente, specificare **9999-09-09** come valore per la proprietà **end**.
     
    Nell'esempio precedente sono visualizzate 24 sezioni di dati, perché viene generata una sezione di dati ogni ora.

    Per le descrizioni delle proprietà JSON nella definizione di una pipeline, vedere l'articolo su come [creare pipeline](data-factory-create-pipelines.md). Per le descrizioni delle proprietà JSON nella definizione di un'attività di copia, vedere le [attività di spostamento dei dati](data-factory-data-movement-activities.md). Per le descrizioni delle proprietà JSON supportate da BlobSource, vedere l'articolo relativo al [connettore BLOB di Azure](data-factory-azure-blob-connector.md). Per le descrizioni delle proprietà JSON supportate da SqlSink, vedere l'articolo relativo al [connettore del database SQL di Azure](data-factory-azure-sql-connector.md).

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
      > È necessario specificare un nome univoco globale per l'istanza di Azure Data Factory. Se si riceve un messaggio di errore riguardante il nome della data factory durante la pubblicazione, è possibile modificare il nome della data factory, ad esempio, nomeutenteVSTutorialFactory, e provare di nuovo ad effettuare la pubblicazione. Per informazioni sulle regole di denominazione per gli elementi di Data factory, vedere l'argomento relativo alle [regole di denominazione di Data factory](data-factory-naming-rules.md) .        
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

* Se viene visualizzato l'errore "La sottoscrizione non è registrata per l'uso dello spazio dei nomi Microsoft.DataFactory", eseguire una di queste operazioni e provare a ripetere la pubblicazione: 
  
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

## <a name="monitor-pipeline"></a>Monitorare la pipeline
Passare alla home page della data factory:

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Fare clic su **Altri servizi** nel menu a sinistra e quindi su **Data factory**.

    ![Esplora data factory](media/data-factory-copy-activity-tutorial-using-visual-studio/browse-data-factories.png)
3. Iniziare a digitare il nome della data factory.

    ![Nome della data factory](media/data-factory-copy-activity-tutorial-using-visual-studio/enter-data-factory-name.png) 
4. Fare clic sulla data factory nell'elenco dei risultati per visualizzarne la home page.

    ![Home page di Data factory](media/data-factory-copy-activity-tutorial-using-visual-studio/data-factory-home-page.png)
5. Per monitorare la pipeline e i set di dati creati in questa esercitazione, seguire le istruzioni riportate nella sezione su come [monitorare set di dati e pipeline](data-factory-copy-activity-tutorial-using-azure-portal.md#monitor-pipeline). Attualmente, Visual Studio non supporta il monitoraggio delle pipeline di Data Factory. 

## <a name="summary"></a>Summary
In questa esercitazione è stata creata una data factory di Azure per copiare dati da un BLOB di Azure a un database SQL Azure. È stato usato Visual Studio per creare la data factory, i servizi collegati, i set di dati e una pipeline. Ecco i passaggi generali eseguiti in questa esercitazione:  

1. Creare un'istanza di Azure **Data Factory**.
2. Creare **servizi collegati**:
   1. Un servizio collegato di **Archiviazione di Azure** per collegare l'account di archiviazione di Azure che include i dati di input.     
   2. Un servizio collegato di **Azure SQL** per collegare il database SQL di Azure che contiene i dati di output. 
3. Creare **set di dati**che descrivono dati di input e dati di output per le pipeline.
4. Creare una **pipeline** con un'**attività di copia** con **BlobSource** come origine e **SqlSink** come sink. 

Per informazioni su come usare un'attività Hive di HDInsight per trasformare dati usando un cluster Azure HDInsight, vedere [Esercitazione: Creare la prima pipeline per trasformare i dati usando il cluster Hadoop](data-factory-build-your-first-pipeline.md).

È possibile concatenare due attività, ovvero eseguire un'attività dopo l'altra, impostando il set di dati di output di un'attività come set di dati di input di altre attività. Per informazioni dettagliate, vedere [Pianificazione ed esecuzione con Data Factory](data-factory-scheduling-and-execution.md). 

## <a name="view-all-data-factories-in-server-explorer"></a>Visualizzare tutte le data factory in Esplora server
Questa sezione illustra come usare Esplora server in Visual Studio per visualizzare tutte le data factory nella sottoscrizione di Azure e creare un progetto di Visual Studio in base a una data factory esistente. 

1. In **Visual Studio** fare clic su **Visualizza** nel menu e fare clic su **Esplora server**.
2. Nella finestra Esplora server espandere **Azure** e **Data factory**. Se viene visualizzata la finestra **Accedi a Visual Studio**, immettere l'**account** associato alla sottoscrizione di Azure e fare clic su **Continua**. Immettere la **password** e fare clic su **Accedi**. Visual Studio cerca di ottenere le informazioni su tutte le data factory di Azure nella sottoscrizione. Lo stato di questa operazione viene visualizzato nella finestra **Data Factory Task List** (Elenco attività Data Factory).

    ![Esplora server](./media/data-factory-copy-activity-tutorial-using-visual-studio/server-explorer.png)

## <a name="create-a-visual-studio-project-for-an-existing-data-factory"></a>Creare un progetto di Visual Studio per una data factory esistente

- Fare clic con il pulsante destro del mouse su una data factory in Esplora server e scegliere **Export Data Factory to New Project** (Esporta la data factory in un nuovo progetto) per creare un progetto di Visual Studio in base a una data factory esistente.

    ![Esportare la data factory in un progetto VS](./media/data-factory-copy-activity-tutorial-using-visual-studio/export-data-factory-menu.png)  

## <a name="update-data-factory-tools-for-visual-studio"></a>Aggiornare gli strumenti di Data factory di Azure per Visual Studio
Per aggiornare gli strumenti di Azure Data Factory per Visual Studio, eseguire queste operazioni:

1. Fare clic su **Strumenti**nel menu e selezionare **Estensioni e aggiornamenti**. 
2. Selezionare **Aggiornamenti** nel riquadro sinistro e quindi selezionare **Visual Studio Gallery**.
3. Selezionare **Azure Data Factory tools for Visual Studio** e fare clic su **Aggiorna**. Se questa voce non è visibile, si dispone già della versione più recente dello strumento. 

## <a name="use-configuration-files"></a>Usare i file di configurazione
È possibile usare i file di configurazione in Visual Studio per configurare le proprietà di pipeline/tabelle/servizi collegati in modo diverso a seconda dell'ambiente.

Considerare la definizione JSON seguente per un servizio collegato Archiviazione di Azure. Per specificare **connectionString** con valori diversi per accountname e accountkey in base all'ambiente di sviluppo, di test o di produzione, in cui vengono distribuite le entità di Data Factory, usare un file di configurazione separato per ogni ambiente.

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "description": "",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

### <a name="add-a-configuration-file"></a>Aggiungere un file di configurazione
Per aggiungere un file di configurazione per ogni ambiente, seguire questa procedura:   

1. Fare clic con il pulsante destro del mouse sul progetto Data Factory nella soluzione di Visual Studio, scegliere **Aggiungi** e fare clic su **Nuovo elemento**.
2. Selezionare **Config** nell'elenco di modelli installati a sinistra, selezionare **File di configurazione**, immettere un **nome** per il file di configurazione e fare clic su **Aggiungi**.

    ![Aggiungere un file di configurazione](./media/data-factory-build-your-first-pipeline-using-vs/add-config-file.png)
3. Aggiungere i parametri di configurazione e i relativi valori nel formato seguente:

    ```json
    {
        "$schema": "http://datafactories.schema.management.azure.com/vsschemas/V1/Microsoft.DataFactory.Config.json",
        "AzureStorageLinkedService1": [
            {
                "name": "$.properties.typeProperties.connectionString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        ],
        "AzureSqlLinkedService1": [
            {
                "name": "$.properties.typeProperties.connectionString",
                "value":  "Server=tcp:<Azure SQL server name>.database.windows.net,1433;Database=<Azure SQL datbase>;User ID=<Username>;Password=<Password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        ]
    }
    ```

    Questo esempio configura la proprietà connectionString di un servizio collegato Archiviazione di Azure e di un servizio collegato Azure SQL. Si noti che la sintassi per specificare il nome è [JsonPath](http://goessner.net/articles/JsonPath/).   

    Se JSON ha una proprietà con una matrice di valori come indicato nel codice seguente:  

    ```json
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
    ```

    Configurare le proprietà come illustrato nel file di configurazione seguente, ovvero usare l'indicizzazione in base zero:

    ```json
    {
        "name": "$.properties.structure[0].name",
        "value": "FirstName"
    }
    {
        "name": "$.properties.structure[0].type",
        "value": "String"
    }
    {
        "name": "$.properties.structure[1].name",
        "value": "LastName"
    }
    {
        "name": "$.properties.structure[1].type",
        "value": "String"
    }
    ```

### <a name="property-names-with-spaces"></a>Nomi delle proprietà con spazi
Se il nome di una proprietà contiene spazi, usare le parentesi quadre come illustrato nell'esempio seguente per il nome del server di database:

```json
 {
     "name": "$.properties.activities[1].typeProperties.webServiceParameters.['Database server name']",
     "value": "MyAsqlServer.database.windows.net"
 }
```

### <a name="deploy-solution-using-a-configuration"></a>Distribuire la soluzione usando una configurazione
Quando si pubblicano entità di Data factory di Azure in VS, è possibile specificare la configurazione che si vuole usare per tale operazione di pubblicazione.

Per pubblicare entità in un progetto di Data factory di Azure usando il file di configurazione:   

1. Fare clic con il pulsante destro del mouse sul progetto Data Factory e scegliere **Pubblica** per visualizzare la finestra di dialogo **Publish Items** (Pubblica elementi).
2. Selezionare una data factory esistente o specificare i valori per crearne una nella pagina **Configure data factory** (Configura data factory) e fare clic su **Avanti**.   
3. Nella pagina **Publish Items** (Pubblica elementi) è presente un elenco a discesa con le configurazioni disponibili per il campo **Select Deployment Config** (Seleziona configurazione di distribuzione).

    ![Selezionare un file di configurazione](./media/data-factory-build-your-first-pipeline-using-vs/select-config-file.png)
4. Selezionare il **file di configurazione** che si vuole usare e fare clic su **Avanti**.
5. Verificare che il nome del file JSON sia presente nella pagina **Riepilogo** e fare clic su **Avanti**.
6. Fare clic su **Fine** al termine dell'operazione di distribuzione.

Al momento della distribuzione, i valori del file di configurazione vengono usati per impostare i valori per le proprietà nei file JSON prima che le entità vengano distribuite nel servizio Azure Data Factory.   

## <a name="use-azure-key-vault"></a>Usare l'Insieme di credenziali delle chiavi di Azure
Non è consigliabile ed è spesso contrario ai criteri di sicurezza eseguire il commit di dati sensibili come le stringhe di connessione nel repository del codice. Per altre informazioni sull'archiviazione di informazioni riservate in Azure Key Vault e sul relativo uso durante la pubblicazione di entità di Data Factory, vedere l'esempio di [ADF Secure Publish](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/ADFSecurePublish) in GitHub. L'estensione Secure Publish per Visual Studio permette di archiviare i segreti in Key Vault, mentre in servizi collegati o configurazioni di distribuzione vengono specificati solo riferimenti a tali segreti. I riferimenti vengono risolti quando si pubblicano entità di Data Factory di Azure. È possibile eseguire il commit di questi file al repository di origine senza esporre i segreti.


## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione sono stati usati l'archivio BLOB di Azure come archivio dati di origine e un database SQL di Azure come archivio dati di destinazione in un'operazione di copia. La tabella seguente contiene un elenco degli archivi dati supportati come origini e come destinazioni dall'attività di copia: 

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

Per informazioni su come copiare dati da/in un archivio dati, fare clic sul collegamento relativo all'archivio dati nella tabella.