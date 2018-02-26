---
title: 'Esercitazione: Creare una pipeline di Azure Data Factory per copiare dati (portale di Azure) | Microsoft Docs'
description: "In questa esercitazione si usa il portale di Azure per creare una pipeline di Azure Data Factory con un'attività di copia per copiare i dati da un archivio BLOB di Azure a un database SQL di Azure."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: d9317652-0170-4fd3-b9b2-37711272162b
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/22/2018
ms.author: spelluru
robots: noindex
ms.openlocfilehash: 555422356ef16319716a6f54d3ba90082ee25838
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="tutorial-use-azure-portal-to-create-a-data-factory-pipeline-to-copy-data"></a>Esercitazione: Usare il portale di Azure per creare una pipeline di Data Factory pipeline per copiare dati 
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

> [!NOTE]
> Questo articolo si applica alla versione 1 del servizio Data Factory, disponibile a livello generale (GA). Se si usa la versione 2 del servizio Data Factory, disponibile in anteprima, vedere l'[esercitazione sull'attività di copia nella documentazione sulla versione 2](../quickstart-create-data-factory-dot-net.md). 

In questo articolo viene illustrato come usare il [portale di Azure](https://portal.azure.com) per creare una data factory con una pipeline che copia dati da un archivio BLOB di Azure a un database SQL di Azure. Se non si ha familiarità con Azure Data Factory, prima di eseguire questa esercitazione vedere l'articolo [Introduzione ad Azure Data Factory](data-factory-introduction.md).   

In questa esercitazione si crea una pipeline contenente una sola attività: un'attività di copia che copia i dati da un archivio dati supportato a un archivio dati sink supportato. Per un elenco degli archivi dati supportati come origini e sink, vedere gli [archivi dati supportati](data-factory-data-movement-activities.md#supported-data-stores-and-formats). e si basa su un servizio disponibile a livello globale che può copiare dati tra diversi archivi dati in modo sicuro, affidabile e scalabile. Per altre informazioni sull'attività di copia, vedere le [attività di spostamento dei dati](data-factory-data-movement-activities.md).

Una pipeline può includere più attività ed è possibile concatenarne due, ovvero eseguire un'attività dopo l'altra, impostando il set di dati di output di un'attività come set di dati di input dell'altra. Per altre informazioni, vedere [Attività multiple in una pipeline](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline). 

> [!NOTE] 
> La pipeline di dati in questa esercitazione copia i dati da un archivio dati di origine a un archivio dati di destinazione. Per un'esercitazione su come trasformare i dati usando Azure Data Factory, vedere [Esercitazione: Creare una pipeline per trasformare i dati usando un cluster Hadoop](data-factory-build-your-first-pipeline.md).

## <a name="prerequisites"></a>prerequisiti
Prima di eseguire questa esercitazione, completare i prerequisiti indicati nell'articolo sui [prerequisiti dell'esercitazione](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .

## <a name="steps"></a>Passaggi
Di seguito sono elencati i passaggi da eseguire in questa esercitazione:

1. Creare una **data factory** di Azure. In questo passaggio viene creata una data factory denominata ADFTutorialDataFactory. 
2. Creare **servizi collegati** nella data factory. In questo passaggio si creano due servizi collegati di tipo Archiviazione di Azure e database SQL di Azure. 
    
    AzureStorageLinkedService collega l'account di archiviazione di Azure alla data factory. Come parte dei [prerequisiti](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) è stato creato un contenitore e sono stati caricati i dati in questo account di archiviazione.   

    AzureSqlLinkedService collega il database SQL di Azure alla data factory. I dati copiati dall'archivio BLOB vengono archiviati in questo database. Come parte dei [prerequisiti](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) è stata creata una tabella SQL in questo database.   
3. Creare **set di dati** di input e di output nella data factory.  
    
    Il servizio collegato Archiviazione di Azure specifica la stringa di connessione usata dal servizio Data Factory in fase di esecuzione per connettersi all'account di archiviazione di Azure. Il set di dati del BLOB di input specifica il contenitore e la cartella che contiene i dati di input.  

    Analogamente, il servizio collegato per il database SQL di Azure specifica la stringa di connessione usata dal servizio Data Factory in fase di esecuzione per connettersi al database SQL di Azure e il set di dati della tabella SQL di output specifica la tabella del database in cui vengono copiati i dati dell'archivio BLOB.
4. Creare una **pipeline** nella data factory. In questo passaggio viene creata una pipeline con un'attività di copia.   
    
    L'attività di copia esegue la copia dei dati da un BLOB nell'archivio BLOB di Azure a una tabella nel database SQL di Azure. È possibile usare un'attività di copia in una pipeline per copiare i dati da qualsiasi origine supportata a qualsiasi destinazione supportata. Per un elenco degli archivi dati supportati, vedere l'articolo sulle [attività di spostamento dei dati](data-factory-data-movement-activities.md#supported-data-stores-and-formats). 
5. Monitorare la pipeline. In questo passaggio vengono **monitorate** le sezioni dei set di dati di input e di output usando il portale di Azure. 

## <a name="create-data-factory"></a>Creare un'istanza di Data Factory
> [!IMPORTANT]
> Completare i [prerequisiti per l'esercitazione](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) se non è già stato fatto.   

Una data factory può comprendere una o più pipeline. Una pipeline può comprendere una o più attività. Ad esempio, un'attività di copia per copiare dati da un archivio dati di origine a uno di destinazione e un'attività Hive HDInsight per eseguire uno script Hive e trasformare i dati di input in dati di output di prodotto. In questo passaggio iniziale viene creata la data factory.

1. Dopo aver eseguito l'accesso al [portale di Azure](https://portal.azure.com/), fare clic su **Crea una risorsa** nel menu a sinistra, quindi su **Dati e analisi** e infine su **Data Factory**. 
   
   ![Nuovo->DataFactory](./media/data-factory-copy-activity-tutorial-using-azure-portal/NewDataFactoryMenu.png)    
2. Nel pannello **Nuova data factory** :
   
   1. Immettere **ADFTutorialDataFactory** come **nome**. 
      
         ![Pannello Nuova data factory](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-new-data-factory.png)
      
       Il nome della data factory di Azure deve essere **univoco a livello globale**. Se viene visualizzato l'errore seguente, modificare il nome della data factory, ad esempio, nomeutenteADFTutorialDataFactory, e provare di nuovo a crearla. Per informazioni sulle regole di denominazione per gli elementi di Data factory, vedere l'argomento relativo alle [regole di denominazione di Data factory](data-factory-naming-rules.md) .
      
           Data factory name “ADFTutorialDataFactory” is not available  
      
       ![Nome di data factory non disponibile](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-data-factory-not-available.png)
   2. Selezionare la **sottoscrizione** di Azure in cui creare la data factory. 
   3. Per il **gruppo di risorse**, eseguire una di queste operazioni:
      
      - Selezionare **Usa esistente**e scegliere un gruppo di risorse esistente dall'elenco a discesa. 
      - Selezionare **Crea nuovo**e immettere un nome per il gruppo di risorse.   
         
          Alcuni dei passaggi di questa esercitazione presuppongono l'uso del nome **ADFTutorialResourceGroup** per il gruppo di risorse. Per informazioni sui gruppi di risorse, vedere l'articolo relativo all' [uso di gruppi di risorse per la gestione delle risorse di Azure](../../azure-resource-manager/resource-group-overview.md).  
   4. Selezionare la **località** per la data factory. Nell'elenco a discesa vengono visualizzate solo le aree supportate dal servizio Data Factory.
   5. Selezionare **Aggiungi al dashboard**.     
   6. Fare clic su **Crea**.
      
      > [!IMPORTANT]
      > Per creare istanze di data factory, è necessario essere membri del ruolo [Collaboratore Data factory](../../active-directory/role-based-access-built-in-roles.md#data-factory-contributor) a livello di sottoscrizione/gruppo di risorse.
      > 
      > Il nome di Data Factory può essere registrato come un nome DNS in futuro e pertanto divenire visibile pubblicamente.                
      > 
      > 
3. Nel dashboard viene visualizzato il riquadro seguente con lo stato: **Deploying data factory** (Distribuzione della data factory). 

    ![Riquadro Deploying data factory (Distribuzione della data factory)](media/data-factory-copy-activity-tutorial-using-azure-portal/deploying-data-factory.png)
4. Al termine della creazione verrà visualizzato il pannello **Data Factory** , come illustrato nell'immagine.
   
   ![Home page di Data factory](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-data-factory-home-page.png)

## <a name="create-linked-services"></a>Creare servizi collegati
Si creano servizi collegati in una data factory per collegare gli archivi dati e i servizi di calcolo alla data factory. In questa esercitazione non si usano servizi di calcolo come Azure HDInsight o Azure Data Lake Analytics, ma due archivi dati di tipo Archiviazione di Azure (origine) e database SQL di Azure (destinazione). 

Si creano quindi due servizi collegati denominati AzureStorageLinkedService e AzureSqlLinkedService di tipo AzureStorage e AzureSqlDatabase.  

AzureStorageLinkedService collega l'account di archiviazione di Azure alla data factory. L'account di archiviazione è quello in cui, come parte dei [prerequisiti](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md), è stato creato un contenitore e sono stati caricati i dati.   

AzureSqlLinkedService collega il database SQL di Azure alla data factory. I dati copiati dall'archivio BLOB vengono archiviati in questo database. Come parte dei [prerequisiti](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) è stata creata la tabella emp in questo database.  

### <a name="create-azure-storage-linked-service"></a>Creare il servizio collegato Archiviazione di Azure
In questo passaggio l'account di archiviazione di Azure viene collegato alla data factory. In questa sezione si specificano il nome e la chiave dell'account di archiviazione di Azure.  

1. Nel pannello **Data Factory** fare clic sul riquadro **Creare e distribuire**.
   
   ![Riquadro Creare e distribuire](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-author-deploy-tile.png) 
2. L'**editor di Data factory** viene visualizzato come nella figura seguente: 

    ![Editor di Data factory](./media/data-factory-copy-activity-tutorial-using-azure-portal/data-factory-editor.png)
3. Nell'editor fare clic sul pulsante **Nuovo archivio dati** sulla barra degli strumenti, quindi scegliere **Archiviazione di Azure** dal menu a discesa. Nel riquadro a destra verrà visualizzato il modello JSON per la creazione di un servizio collegato di archiviazione di Azure. 
   
    ![Pulsante Nuovo archivio dati dell'editor](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-newdatastore-button.png)    
3. Sostituire `<accountname>` e `<accountkey>` con i valori del nome e della chiave dell'account di archiviazione di Azure. 
   
    ![JSON dell'archivio BLOB dell'editor](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-blob-storage-json.png)    
4. Fare clic su **Distribuisci** sulla barra degli strumenti. Nella visualizzazione albero dovrebbe essere visibile il servizio **AzureStorageLinkedService** distribuito. 
   
    ![Distribuzione dell'archivio BLOB dell'editor](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-editor-blob-storage-deploy.png)

    Per altre informazioni sulle proprietà JSON nella definizione dei servizi collegati, vedere l'articolo relativo al [connettore di Archiviazione BLOB di Azure](data-factory-azure-blob-connector.md#linked-service-properties).

### <a name="create-a-linked-service-for-the-azure-sql-database"></a>Creare un servizio collegato per il database SQL di Azure
In questo passaggio il database SQL di Azure viene collegato alla data factory. In questa sezione si specificano il nome del server di Azure SQL, il nome del database, il nome utente e la password utente. 

1. Nell'**editor di Data Factory** fare clic sul pulsante **Nuovo archivio dati** sulla barra degli strumenti e scegliere **Database SQL di Azure** dal menu a discesa. Nel riquadro a destra verrà visualizzato il modello JSON per la creazione di un servizio collegato SQL di Azure.
2. Sostituire `<servername>`, `<databasename>`, `<username>@<servername>` e `<password>` con i nomi del server di Azure SQL e del database, l'account utente e la password. 
3. Fare clic su **Distribuisci** sulla barra degli strumenti per creare e distribuire **AzureSqlLinkedService**.
4. Assicurarsi che **AzureSqlLinkedService** sia visibile nella visualizzazione albero in **Servizi collegati**.  

    Per altre informazioni su queste proprietà JSON, vedere l'articolo relativo al [connettore del database SQL di Azure](data-factory-azure-sql-connector.md#linked-service-properties).

## <a name="create-datasets"></a>Creare set di dati
Nel passaggio precedente sono stati creati servizi collegati per collegare l'account di archiviazione di Azure e un database SQL di Azure alla data factory. In questo passaggio vengono definiti due set di dati denominati InputDataset e OutputDataset, che rappresentano i dati di input e di output memorizzati negli archivi dati a cui fanno riferimento rispettivamente AzureStorageLinkedService e AzureSqlLinkedService.

Il servizio collegato Archiviazione di Azure specifica la stringa di connessione usata dal servizio Data Factory in fase di esecuzione per connettersi all'account di archiviazione di Azure. Il set di dati del BLOB di input (InputDataset) specifica il contenitore e la cartella che contiene i dati di input.  

Analogamente, il servizio collegato per il database SQL di Azure specifica la stringa di connessione usata dal servizio Data Factory in fase di esecuzione per connettersi al database SQL di Azure e il set di dati della tabella SQL di output (OututDataset) specifica la tabella del database in cui vengono copiati i dati dell'archivio BLOB. 

### <a name="create-input-dataset"></a>Creare set di dati di input
In questo passaggio viene creato un set di dati denominato InputDataset che punta a un file BLOB (emp.txt) nella cartella radice di un contenitore BLOB (adftutorial) nella risorsa di archiviazione di Azure rappresentata dal servizio collegato AzureStorageLinkedService. Se non si specifica un valore per fileName (o lo si ignora), i dati di tutti i BLOB della cartella di input vengono copiati nella destinazione. In questa esercitazione si specifica un valore per fileName. 

1. Nell'**editor** della data factory fare clic su **... Altro**, fare clic su **Nuovo set di dati** e quindi scegliere **Archivio BLOB di Azure** dal menu a discesa. 
   
    ![Menu Nuovo set di dati](./media/data-factory-copy-activity-tutorial-using-azure-portal/new-dataset-menu.png)
2. Sostituire il codice JSON nel riquadro a destra con il frammento di codice JSON seguente: 
   
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
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
          "folderPath": "adftutorial/",
          "fileName": "emp.txt",
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
3. Fare clic su **Distribuisci** sulla barra degli strumenti per creare e distribuire il set di dati **InputDataset**. Assicurarsi che **InputDataset** sia visibile nella visualizzazione albero.

### <a name="create-output-dataset"></a>Creare il set di dati di output
Il servizio collegato per il database SQL di Azure specifica la stringa di connessione usata dal servizio Data Factory in fase di esecuzione per connettersi al database SQL di Azure. Il set di dati della tabella SQL di output (OututDataset) creato in questo passaggio specifica la tabella del database in cui vengono copiati i dati dell'archivio BLOB.

1. Nell'**editor** della data factory fare clic su **... Altro**, fare clic su **Nuovo set di dati** e quindi scegliere **Azure SQL** dal menu a discesa. 
2. Sostituire il codice JSON nel riquadro a destra con il frammento di codice JSON seguente:

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
        "linkedServiceName": "AzureSqlLinkedService",
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
3. Fare clic su **Distribuisci** sulla barra degli strumenti per creare e distribuire il set di dati **OutputDataset**. Assicurarsi che **OutputDataset** sia visibile nella visualizzazione albero in **Datasets**. 

## <a name="create-pipeline"></a>Creare una pipeline
In questo passaggio viene creata una pipeline con un'**attività di copia** che usa **InputDataset** come input e **OutputDataset** come output.

Attualmente, è il set di dati di output a determinare la pianificazione. In questa esercitazione il set di dati di output viene configurato per generare una sezione una volta ogni ora. La pipeline ha un'ora di inizio e un'ora di fine intervallate da un giorno, ovvero 24 ore. Vengono quindi generate dalla pipeline 24 sezioni di set di dati di output. 

1. Nell'**editor** della data factory fare clic su **... Altro** e quindi su **Nuova pipeline**. In alternativa, è possibile fare clic con il pulsante destro del mouse su **Pipeline** nella visualizzazione ad albero e fare clic su **Nuova pipeline**.
2. Sostituire il codice JSON nel riquadro a destra con il frammento di codice JSON seguente: 

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
              "retry": 0,
              "timeout": "01:00:00"
            }
          }
        ],
        "start": "2017-05-11T00:00:00Z",
        "end": "2017-05-12T00:00:00Z"
      }
    } 
    ```   
    
    Tenere presente quanto segue:
   
    - Nella sezione delle attività esiste una sola attività con l'oggetto **type** impostato su **Copy**. Per altre informazioni sull'attività di copia, vedere le [attività di spostamento dei dati](data-factory-data-movement-activities.md). Nelle soluzioni Data Factory è anche possibile usare le [attività di trasformazione dei dati](data-factory-data-transformation-activities.md).
    - L'input per l'attività è impostato su **InputDataset** e l'output è impostato su **OutputDataset**. 
    - Nella sezione **typeProperties** vengono specificati **BlobSource** come tipo di origine e **SqlSink** come tipo di sink. Per un elenco completo degli archivi dati supportati dall'attività di copia come origini e sink, vedere gli [archivi dati supportati](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Per informazioni su come usare uno specifico archivio dati supportato come origine/sink, fare clic sul collegamento nella tabella.
    - Per la data e ora di inizio è necessario usare il [formato ISO](http://en.wikipedia.org/wiki/ISO_8601), ad esempio 2016-10-14T16:32:41Z. Il valore di **end** è facoltativo, ma in questa esercitazione viene usato. Se non si specifica alcun valore per la proprietà **end**, il valore verrà calcolato come "**start + 48 hours**". Per eseguire la pipeline illimitatamente, specificare **9999-09-09** come valore per la proprietà **end**.
     
    Nell'esempio precedente sono visualizzate 24 sezioni di dati, perché viene generata una sezione di dati ogni ora.

    Per le descrizioni delle proprietà JSON nella definizione di una pipeline, vedere l'articolo su come [creare pipeline](data-factory-create-pipelines.md). Per le descrizioni delle proprietà JSON nella definizione di un'attività di copia, vedere le [attività di spostamento dei dati](data-factory-data-movement-activities.md). Per le descrizioni delle proprietà JSON supportate da BlobSource, vedere l'articolo relativo al [connettore BLOB di Azure](data-factory-azure-blob-connector.md). Per le descrizioni delle proprietà JSON supportate da SqlSink, vedere l'articolo relativo al [connettore del database SQL di Azure](data-factory-azure-sql-connector.md).
3. Fare clic su **Distribuisci** sulla barra degli strumenti per creare e distribuire **ADFTutorialPipeline**. Verificare che la pipeline sia visibile nella visualizzazione albero. 
4. Chiudere quindi il pannello dell'**editor** facendo clic su **X**. Fare clic di nuovo su **X** per visualizzare la home page di **Data Factory** per **ADFTutorialDataFactory**.

**Congratulazioni** È stata creata una data factory di Azure con una pipeline per copiare dati da un archivio BLOB di Azure a un database SQL di Azure. 


## <a name="monitor-pipeline"></a>Monitorare la pipeline
In questo passaggio viene usato il portale di Azure per monitorare le attività in un'istanza di Azure Data Factory.    

### <a name="monitor-pipeline-using-monitor--manage-app"></a>Monitorare la pipeline con l'app Monitoraggio e gestione
La procedura seguente illustra come monitorare le pipeline nella data factory usando l'applicazione Monitoraggio e gestione: 

1. Fare clic sul riquadro **Monitoraggio e gestione** nella home page della data factory.
   
    ![Riquadro Monitoraggio e gestione](./media/data-factory-copy-activity-tutorial-using-azure-portal/monitor-manage-tile.png) 
2. L'**applicazione Monitoraggio e gestione** verrà visualizzata in una scheda separata. 

    > [!NOTE]
    > Se il Web browser è bloccato su "Concessione autorizzazioni in corso...", eseguire una di queste operazioni: deselezionare la casella di controllo **Block third-party cookies and site data** (Blocco dei cookie di terze parti e dei dati dei siti) oppure creare un'eccezione per **login.microsoftonline.com** e quindi provare di nuovo ad aprire l'app.

    ![App Monitoraggio e gestione](./media/data-factory-copy-activity-tutorial-using-azure-portal/monitor-and-manage-app.png)
3. Modificare l'**Ora di inizio** e l'**Ora di fine** in modo da includere l'inizio (11/05/2017) e la fine (12/05/2017) della pipeline e fare clic su **Applica**.       
3. Vengono visualizzate le **finestre attività** associate a ogni ora compresa tra l'ora di inizio e l'ora di fine della pipeline nell'elenco del riquadro centrale. 
4. Per visualizzare i dettagli di una finestra attività, selezionarla nell'elenco **Activity Windows** (Finestre attività). 
    ![Dettagli finestra attività](./media/data-factory-copy-activity-tutorial-using-azure-portal/activity-window-details.png)

    In Activity Window Explorer (Esplora finestre attività) sulla destra è possibile osservare che le sezioni fino all'ora UTC corrente (20:12) sono state tutte elaborate (in verde). Le sezioni 20-21, 21-22, 22-23, 23-00 non sono ancora state elaborate.

    La sezione **Tentativi** nel riquadro a destra contiene informazioni sull'attività eseguita per la sezione dati. Se si è verificato un errore, fornisce dettagli sull'errore. Se ad esempio la cartella o il contenitore di input non esiste e l'elaborazione della sezione non riesce, viene visualizzato un messaggio di errore che informa che il contenitore o la cartella non esiste.

    ![Tentativi di esecuzione dell'attività](./media/data-factory-copy-activity-tutorial-using-azure-portal/activity-run-attempts.png) 
4. Avviare **SQL Server Management Studio**, connettersi al database SQL di Azure e verificare che le righe vengano inserite nella tabella **emp** nel database.
    
    ![Risultati della query SQL](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-sql-query-results.png)

Per informazioni dettagliate sull'uso di questa applicazione, vedere [Monitorare e gestire le pipeline di Azure Data Factory con la nuova app di monitoraggio e gestione](data-factory-monitor-manage-app.md).

### <a name="monitor-pipeline-using-diagram-view"></a>Monitorare la pipeline con la vista diagramma
È anche possibile monitorare le pipeline di dati usando la vista diagramma.  

1. Nel pannello **Data Factory** fare clic su **Diagramma**.
   
    ![Pannello Data factory - Riquadro Diagramma](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-datafactoryblade-diagramtile.png)
2. Verrà visualizzato un diagramma simile all'immagine seguente: 
   
    ![Vista diagramma](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-diagram-blade.png)  
5. Nella vista diagramma fare doppio clic su **InputDataset** per visualizzare le sezioni per il set di dati.  
   
    ![Set di dati con InputDataset selezionato](./media/data-factory-copy-activity-tutorial-using-azure-portal/DataSetsWithInputDatasetFromBlobSelected.png)   
5. Fare clic sul collegamento **Altre informazioni** per visualizzare tutte le sezioni dati. Vengono visualizzate 24 sezioni orarie tra l'ora di inizio e di fine della pipeline. 
   
    ![Tutte le sezioni dati di input](./media/data-factory-copy-activity-tutorial-using-azure-portal/all-input-slices.png)  
   
    Si noti che le sezioni di dati fino all'ora UTC corrente sono nello stato **Pronto**, perché il file **emp.txt** è sempre presente nel contenitore BLOB **adftutorial\input**. Le sezioni per le ore successive non sono ancora nello stato Pronto. Verificare che non sia visualizzata alcuna sezione in **Sezioni non riuscite di recente** nella parte inferiore della pagina.
6. Chiudere i pannelli fino a visualizzare la vista diagramma oppure scorrere verso sinistra per visualizzare la vista diagramma, quindi fare doppio clic su **OutputDataset**. 
8. Fare clic sul collegamento **Altre informazioni** nel pannello **Tabella** per **OutputDataset** per visualizzare tutte le sezioni.

    ![Pannello Sezioni dati](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-dataslices-blade.png) 
9. Si noti che tutte le sezioni fino all'ora UTC corrente passano dallo stato **L'esecuzione è in sospeso** allo stato => **In corso** ==> **Pronto**. Per impostazione predefinita, le sezioni precedenti (prima dell'ora corrente) vengono elaborate dalla più recente alla meno recente. Se ad esempio l'ora corrente è 20:12 UTC, la sezione 19 - 20 viene elaborata prima della sezione 18 - 19. Per impostazione predefinita, la sezione 20 - 21 viene elaborata alla fine dell'intervallo di tempo, ovvero dopo le 21.  
10. Fare clic su una qualsiasi sezione dati dell'elenco per visualizzare il pannello **Sezione dati** . I dati associati a una finestra attività vengono chiamati sezione. Una sezione può essere costituita da uno o più file.  
    
     ![Pannello Sezione di dati](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-dataslice-blade.png)
    
     Se lo stato della sezione non è **Pronto**, sarà possibile visualizzare le sezioni upstream che non sono pronte e bloccano l'esecuzione della sezione corrente nell'elenco **Sezioni upstream non pronte**.
11. Nel pannello **SEZIONE DI DATI** è possibile visualizzare tutte le esecuzioni di attività nell'elenco in basso. Fare clic su un'**esecuzione di attività** per visualizzare il pannello **Dettagli esecuzione attività**. 
    
    ![Dettagli esecuzione attività](./media/data-factory-copy-activity-tutorial-using-azure-portal/ActivityRunDetails.png)

    In questo pannello vengono visualizzati la durata dell'operazione di copia, la velocità effettiva, il numero di byte di dati letti e scritti, l'ora di inizio dell'esecuzione, l'ora di fine dell'esecuzione e così via.  
12. Fare clic su **X** per chiudere tutti i pannelli finché non viene visualizzato il pannello iniziale per **ADFTutorialDataFactory**.
13. (Facoltativo) Fare clic sul riquadro **Set di dati** o sul riquadro **Pipeline** per ottenere i pannelli visualizzati nei passaggi precedenti. 
14. Avviare **SQL Server Management Studio**, connettersi al database SQL di Azure e verificare che le righe vengano inserite nella tabella **emp** nel database.
    
    ![Risultati della query SQL](./media/data-factory-copy-activity-tutorial-using-azure-portal/getstarted-sql-query-results.png)


## <a name="summary"></a>Summary
In questa esercitazione è stata creata una data factory di Azure per copiare dati da un BLOB di Azure a un database SQL Azure. È stato usato il portale di Azure per creare la data factory, i servizi collegati, i set di dati e una pipeline. Ecco i passaggi generali eseguiti in questa esercitazione:  

1. Creare un'istanza di Azure **Data Factory**.
2. Creare **servizi collegati**:
   1. Un servizio collegato di **Archiviazione di Azure** per collegare l'account di archiviazione di Azure che include i dati di input.     
   2. Un servizio collegato di **Azure SQL** per collegare il database SQL di Azure che contiene i dati di output. 
3. Creare **set di dati** che descrivono dati di input e di output per le pipeline.
4. Creare una **pipeline** con un'**attività di copia** con **BlobSource** come origine e **SqlSink** come sink.  

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione sono stati usati l'archivio BLOB di Azure come archivio dati di origine e un database SQL di Azure come archivio dati di destinazione in un'operazione di copia. La tabella seguente contiene un elenco degli archivi dati supportati come origini e come destinazioni dall'attività di copia: 

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

Per informazioni su come copiare dati da/in un archivio dati, fare clic sul collegamento relativo all'archivio dati nella tabella.