---
title: Copiare dati da SQL Server ad Archiviazione BLOB con PowerShell
description: Informazioni su come copiare dati da un archivio dati locale al cloud di Azure usando un runtime di integrazione self-hosted in Azure Data Factory.
services: data-factory
author: nabhishek
ms.author: abnarain
manager: anandsub
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 01/22/2018
ms.openlocfilehash: b0e4fcf771f2441d9e1061ee57e83e26b6b1a241
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74922962"
---
# <a name="tutorial-copy-data-from-an-on-premises-sql-server-database-to-azure-blob-storage"></a>Esercitazione: Copiare dati da un database di SQL Server locale a un archivio BLOB di Azure

In questa esercitazione si usa Azure PowerShell per creare una pipeline di Data Factory che copia i dati da un database di SQL Server locale a un archivio BLOB di Azure. Si crea e si usa un runtime di integrazione self-hosted, che sposta i dati tra gli archivi dati locali e cloud. 

> [!NOTE]
> Questo articolo non offre una presentazione dettagliata del servizio Data Factory. Per altre informazioni, vedere l'[introduzione ad Azure Data Factory](introduction.md). 

In questa esercitazione si segue questa procedura:

> [!div class="checklist"]
> * Creare una data factory.
> * Creare un runtime di integrazione self-hosted.
> * Creare servizi collegati per SQL Server e Archiviazione di Azure. 
> * Creare set di dati per SQL Server e BLOB di Azure.
> * Creare una pipeline con attività di copia per trasferire i dati.
> * Avviare un'esecuzione della pipeline.
> * Monitorare l'esecuzione della pipeline.

## <a name="prerequisites"></a>Prerequisiti
### <a name="azure-subscription"></a>Sottoscrizione di Azure
Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

### <a name="azure-roles"></a>Ruoli di Azure
Per creare istanze di Data Factory, all'account utente usato per accedere ad Azure deve essere assegnato un ruolo *collaboratore* o *proprietario* oppure l'account utente deve essere un *amministratore* della sottoscrizione di Azure. 

Per visualizzare le autorizzazioni disponibili nella sottoscrizione, passare al portale di Azure, selezionare il nome utente nell'angolo in alto a destra e quindi **Autorizzazioni**. Se si accede a più sottoscrizioni, selezionare quella appropriata. Per istruzioni di esempio sull'aggiunta di un utente a un ruolo, vedere l'articolo [Gestire l'accesso usando il controllo degli accessi in base al ruolo e il portale di Azure](../role-based-access-control/role-assignments-portal.md).

### <a name="sql-server-2014-2016-and-2017"></a>SQL Server 2014, 2016 e 2017
In questa esercitazione si usa un database di SQL Server locale come archivio dati di *origine*. La pipeline nella data factory creata in questa esercitazione copia i dati da questo database di SQL Server locale (origine) all'archivio BLOB di Azure (sink). Si crea quindi una tabella denominata **emp** nel database di SQL Server e inserire una coppia di voci di esempio nella tabella. 

1. Avviare SQL Server Management Studio. Se non è già installato nel computer, passare a [Scaricare SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). 

1. Connettersi all'istanza di SQL Server usando le credenziali. 

1. Creare un database di esempio. Nella visualizzazione struttura ad albero fare clic con il pulsante destro del mouse su **Database** e scegliere **Nuovo database**. 
 
1. Nella finestra **Nuovo database** immettere un nome per il database e fare clic su **OK**. 

1. Per creare la tabella **emp** e inserirvi alcuni dati di esempio, eseguire questo script di query sul database. Nella visualizzazione struttura ad albero fare clic con il pulsante destro del mouse sul database creato e scegliere **Nuova query**.

    ```sql
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO
    
    INSERT INTO emp (FirstName, LastName) VALUES ('John', 'Doe')
    INSERT INTO emp (FirstName, LastName) VALUES ('Jane', 'Doe')
    GO
    ```


### <a name="azure-storage-account"></a>Account di archiviazione di Azure
In questa esercitazione si usa un account di archiviazione di Azure per utilizzo generico (specificamente, un archivio BLOB) come archivio dati di destinazione/sink. Se non si ha un account di archiviazione di Azure per utilizzo generico, vedere [Creare un account di archiviazione](../storage/common/storage-quickstart-create-account.md). La pipeline nella data factory creata in questa esercitazione copia i dati dal database di SQL Server locale (origine) a questo archivio BLOB di Azure (sink). 

#### <a name="get-storage-account-name-and-account-key"></a>Recuperare il nome e la chiave dell'account di archiviazione
In questa esercitazione si usano il nome e la chiave dell'account di archiviazione di Azure. Recuperare il nome e la chiave dell'account di archiviazione nel modo seguente: 

1. Accedere al [portale di Azure](https://portal.azure.com) con nome utente e password. 

1. Nel riquadro a sinistra selezionare **Altri servizi**, usare la parola chiave **Archiviazione** come filtro e selezionare **Account di archiviazione**.

    ![Cercare l'account di archiviazione](media/doc-common-process/search-storage-account.png)

1. Nell'elenco degli account di archiviazione filtrare, se necessario, e quindi selezionare il proprio account di archiviazione. 

1. Nella finestra **Account di archiviazione** selezionare **Chiavi di accesso**.

1. Nelle caselle **Nome account di archiviazione** e **key1** copiare i valori e incollarli nel Blocco note o in un altro editor per usarli in seguito nell'esercitazione. 

#### <a name="create-the-adftutorial-container"></a>Creare il contenitore adftutorial 
In questa sezione si crea un contenitore BLOB denominato **adftutorial** nell'archivio BLOB di Azure. 

1. Nella finestra **Account di archiviazione** passare a **Panoramica** e quindi selezionare **BLOB**. 

    ![Selezionare l'opzione BLOB](media/tutorial-hybrid-copy-powershell/select-blobs.png)

1. Nella finestra **Servizio BLOB** selezionare **Contenitore**. 

1. Nella finestra di dialogo **Nuovo contenitore** immettere **adftutorial** nella casella **Nome** e fare clic su **OK**. 

    ![Immettere il nome del contenitore](media/tutorial-hybrid-copy-powershell/new-container-dialog.png)

1. Selezionare **adftutorial** nell'elenco di contenitori.  

1. Tenere aperta la finestra **Contenitore** per **adftutorial** perché verrà usata per verificare l'output alla fine di questa esercitazione. Data Factory crea automaticamente la cartella di output in questo contenitore, quindi non è necessario crearne uno.


### <a name="windows-powershell"></a>Windows PowerShell

#### <a name="install-azure-powershell"></a>Installare Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Installare la versione più recente di Azure PowerShell, se non è già presente nel computer. Per istruzioni dettagliate, vedere [Come installare e configurare Azure PowerShell](/powershell/azure/install-Az-ps). 

#### <a name="log-in-to-powershell"></a>Accedere a PowerShell

1. Avviare PowerShell nel computer in uso e tenerlo aperto fino al completamento di questa esercitazione introduttiva. Se si chiude e si riapre, sarà necessario eseguire di nuovo questi comandi.

1. Eseguire questo comando e immettere il nome utente e la password di Azure usati per accedere al portale di Azure:
       
    ```powershell
    Connect-AzAccount
    ```        

1. Se si hanno più sottoscrizioni di Azure, eseguire questo comando per selezionare la sottoscrizione che si vuole usare. Sostituire **SubscriptionId** con l'ID della sottoscrizione di Azure:

    ```powershell
    Select-AzSubscription -SubscriptionId "<SubscriptionId>"    
    ```

## <a name="create-a-data-factory"></a>Creare una data factory

1. Definire una variabile per il nome del gruppo di risorse usato in seguito nei comandi di PowerShell. Copiare il comando seguente in PowerShell, specificare un nome per il [gruppo di risorse di Azure](../azure-resource-manager/resource-group-overview.md) tra virgolette doppie (ad esempio `"adfrg"`) e quindi eseguire il comando. 
   
    ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup"
    ```

1. Per creare il gruppo di risorse di Azure, eseguire questo comando: 

    ```powershell
    New-AzResourceGroup $resourceGroupName -location 'East US'
    ``` 

    Se il gruppo di risorse esiste già, potrebbe essere preferibile non sovrascriverlo. Assegnare un valore diverso alla variabile `$resourceGroupName` ed eseguire di nuovo il comando.

1. Definire una variabile per il nome della data factory utilizzabile in seguito nei comandi di PowerShell. Il nome deve iniziare con una lettera o un numero e può contenere solo lettere, numeri e il carattere trattino (-).

    > [!IMPORTANT]
    >  Aggiornare il nome della data factory in modo che sia univoco a livello globale, ad esempio ADFTutorialFactorySP1127. 

    ```powershell
    $dataFactoryName = "ADFTutorialFactory"
    ```

1. Definire una variabile per la località della data factory: 

    ```powershell
    $location = "East US"
    ```  

1. Per creare la data factory, eseguire il cmdlet `Set-AzDataFactoryV2` seguente: 
    
    ```powershell       
    Set-AzDataFactoryV2 -ResourceGroupName $resourceGroupName -Location $location -Name $dataFactoryName 
    ```

> [!NOTE]
> 
> * Il nome della data factory deve essere univoco a livello globale. Se viene visualizzato l'errore seguente, modificare il nome e riprovare.
>    ```
>    The specified data factory name 'ADFv2TutorialDataFactory' is already in use. Data factory names must be globally unique.
>    ```
> * Per creare istanze di Data Factory, all'account utente usato per accedere ad Azure deve essere assegnato un ruolo *collaboratore* o *proprietario* oppure l'account deve essere un *amministratore* della sottoscrizione di Azure.
> * Per un elenco di aree di Azure in cui Data Factory è attualmente disponibile, selezionare le aree di interesse nella pagina seguente, quindi espandere **Analytics** per individuare **Data Factory**: [Prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/). Gli archivi dati (Archiviazione di Azure, database SQL di Azure e così via) e le risorse di calcolo (Azure HDInsight e così via) usati dalla data factory possono trovarsi in altre aree.
> 
> 

## <a name="create-a-self-hosted-integration-runtime"></a>Creare un runtime di integrazione self-hosted

In questa sezione si crea un runtime di integrazione self-hosted e lo si associa a un computer locale con il database di SQL Server. Il runtime di integrazione self-hosted è il componente che copia i dati dal database SQL Server presente nel computer all'archivio BLOB di Azure. 

1. Creare una variabile per il nome del runtime di integrazione. Usare un nome univoco e annotarlo perché sarà usato più avanti in questa esercitazione. 

    ```powershell
   $integrationRuntimeName = "ADFTutorialIR"
    ```

1. Creare un runtime di integrazione self-hosted. 

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $integrationRuntimeName -Type SelfHosted -Description "selfhosted IR description"
    ``` 

    Di seguito è riportato l'output di esempio:

    ```json
    Name              : ADFTutorialIR
    Type              : SelfHosted
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Description       : selfhosted IR description
    Id                : /subscriptions/<subscription ID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>/integrationruntimes/<integrationRuntimeName>
    ```

1. Eseguire questo comando per recuperare lo stato del runtime di integrazione creato:

    ```powershell
   Get-AzDataFactoryV2IntegrationRuntime -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Status
    ```

    Di seguito è riportato l'output di esempio:
    
    ```json
    State                     : NeedRegistration
    Version                   : 
    CreateTime                : 9/10/2019 3:24:09 AM
    AutoUpdate                : On
    ScheduledUpdateDate       : 
    UpdateDelayOffset         : 
    LocalTimeZoneOffset       : 
    InternalChannelEncryption : 
    Capabilities              : {}
    ServiceUrls               : {eu.frontend.clouddatahub.net}
    Nodes                     : {}
    Links                     : {}
    Name                      : <Integration Runtime name>
    Type                      : SelfHosted
    ResourceGroupName         : <resourceGroup name>
    DataFactoryName           : <dataFactory name>
    Description               : selfhosted IR description
    Id                        : /subscriptions/<subscription ID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<dataFactoryName>/integrationruntimes/<integrationRuntimeName>
    ```

1. Eseguire questo comando per recuperare le *chiavi di autenticazione* per la registrazione del runtime di integrazione self-hosted nel servizio Data Factory nel cloud. Copiare una delle chiavi (escluse le virgolette) per la registrazione del runtime di integrazione self-hosted che si installerà nel computer nel passaggio successivo. 

    ```powershell
    Get-AzDataFactoryV2IntegrationRuntimeKey -Name $integrationRuntimeName -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName | ConvertTo-Json
    ```
    
    Di seguito è riportato l'output di esempio:
    
    ```json
    {
        "AuthKey1":  "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx=",
        "AuthKey2":  "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@yyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyy="
    }
    ```

## <a name="install-the-integration-runtime"></a>Installare il runtime di integrazione
1. Scaricare [Azure Data Factory Integration Runtime](https://www.microsoft.com/download/details.aspx?id=39717) in un computer Windows locale, quindi eseguire l'installazione. 

1. Nella pagina iniziale dell'**installazione guidata di Microsoft Integration Runtime** fare clic su **Avanti**.  

1. Nella finestra **Contratto di licenza con l'utente finale** accettare le condizioni e fare clic su **Avanti**. 

1. Nella finestra **Cartella di destinazione** fare clic su **Avanti**. 

1. Nella pagina **Pronto per l'installazione** fare clic su **Installa**. 

1. Nella pagina **Installazione di Microsoft Integration Runtime completata** fare clic su **Fine**.

1. Nella finestra **Registra Runtime di integrazione (self-hosted)** incollare la chiave salvata nella sezione precedente e fare clic su **Registra**. 

    ![Registrare il runtime di integrazione](media/tutorial-hybrid-copy-powershell/register-integration-runtime.png)

1. Nella finestra **Nuovo nodo di Runtime di integrazione (self-hosted)** fare clic su **Fine**. 

    ![Finestra Nuovo nodo di Runtime di integrazione](media/tutorial-hybrid-copy-powershell/new-integration-runtime-node-page.png)

 1. Al termine della registrazione del runtime di integrazione self-hosted viene visualizzato il messaggio seguente: 

    ![Registrazione completata](media/tutorial-hybrid-copy-powershell/registered-successfully.png)

1. Nella finestra **Registra Runtime di integrazione (self-hosted)** fare clic su **Avvia Configuration Manager**. 

1. Quando il nodo viene connesso al servizio cloud, viene visualizzato il messaggio seguente:

    ![Connessione del nodo](media/tutorial-hybrid-copy-powershell/node-is-connected.png)

1. Testare la connettività al database di SQL Server nel modo seguente:

    a. Nella finestra **Configuration Manager** passare alla scheda **Diagnostica**.

    b. Selezionare **SqlServer** in **Tipo di origine dati**.

    c. Immettere il nome del server.

    d. Immettere il nome del database. 

    e. Selezionare la modalità di autenticazione. 

    f. Immettere il nome utente. 

    g. Immettere la password associata al nome utente.

    h. Fare clic su **Test** per verificare che il runtime di integrazione possa connettersi a SQL Server.  
    ![Connettività riuscita](media/tutorial-hybrid-copy-powershell/config-manager-diagnostics-tab.png) 
  
    Se la connessione ha esito positivo, viene visualizzato un segno di spunta verde. In caso contrario, viene visualizzato un messaggio associato all'errore. Risolvere eventuali problemi e assicurarsi che il runtime di integrazione possa connettersi all'istanza di SQL Server.

    Annotare tutti i valori precedenti per usarli più avanti in questa esercitazione.
    
## <a name="create-linked-services"></a>Creare servizi collegati
Creare servizi collegati nella data factory per collegare gli archivi dati e i servizi di calcolo alla data factory. In questa esercitazione si collegano l'account di archiviazione di Azure e l'istanza di SQL Server locale all'archivio dati. I servizi collegati hanno le informazioni di connessione usate dal servizio Data Factory in fase di esecuzione per la connessione. 

### <a name="create-an-azure-storage-linked-service-destinationsink"></a>Creare un servizio collegato Archiviazione di Azure (destinazione/sink)
In questo passaggio, l'account di archiviazione di Azure viene collegato alla data factory.

1. Creare un file JSON denominato *AzureStorageLinkedService.json* nella cartella *C:\ADFv2Tutorial* con il codice seguente. Creare la cartella *ADFv2Tutorial*, se non esiste già.  

    > [!IMPORTANT]
    > Sostituire \<accountName> e \<accountKey> con il nome e la chiave dell'account di archiviazione di Azure prima di salvare il file. Questi valori sono stati annotati nella sezione [Prerequisiti](#get-storage-account-name-and-account-key).

   ```json
    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "annotations": [],
            "type": "AzureBlobStorage",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>;EndpointSuffix=core.windows.net"
            }
        }
    }
   ```

1. In PowerShell passare alla cartella *C:\ADFv2Tutorial*.
   ```powershell
   Set-Location 'C:\ADFv2Tutorial'    
   ```

1. Eseguire il cmdlet `Set-AzDataFactoryV2LinkedService` seguente per creare il servizio collegato denominato AzureStorageLinkedService: 

   ```powershell
   Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "AzureStorageLinkedService" -File ".\AzureStorageLinkedService.json"
   ```

   Di seguito è riportato un output di esempio:

    ```json
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : <resourceGroup name>
    DataFactoryName   : <dataFactory name>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobStorageLinkedService
    ```

    Se viene visualizzato un errore di tipo "File non trovato", verificare che il file esista eseguendo il comando `dir`. Se il nome del file ha estensione *txt*, ad esempio AzureStorageLinkedService.json.txt, rimuovere l'estensione ed eseguire di nuovo il comando di PowerShell. 

### <a name="create-and-encrypt-a-sql-server-linked-service-source"></a>Creare e crittografare un servizio collegato SQL Server (origine)
In questo passaggio si collega l'istanza di SQL Server locale alla data factory.

1. Creare un file JSON denominato *SqlServerLinkedService.json* nella cartella *C:\ADFv2Tutorial* usando il codice seguente:

    > [!IMPORTANT]
    > Selezionare la sezione in base all'autenticazione usata per connettersi a SQL Server.

    **Uso dell'autenticazione SQL (sa):**

    ```json
    {  
        "name":"SqlServerLinkedService",
        "type":"Microsoft.DataFactory/factories/linkedservices",
        "properties":{  
            "annotations":[  
    
            ],
            "type":"SqlServer",
            "typeProperties":{  
                "connectionString":"integrated security=False;data source=<serverName>;initial catalog=<databaseName>;user id=<userName>;password=<password>"
            },
            "connectVia":{  
                "referenceName":"<integration runtime name> ",
                "type":"IntegrationRuntimeReference"
            }
        }
    }
   ```    

    **Uso dell'autenticazione di Windows:**

    ```json
    {  
        "name":"SqlServerLinkedService",
        "type":"Microsoft.DataFactory/factories/linkedservices",
        "properties":{  
            "annotations":[  
    
            ],
            "type":"SqlServer",
            "typeProperties":{  
                "connectionString":"integrated security=True;data source=<serverName>;initial catalog=<databaseName>",
                "userName":"<username> or <domain>\\<username>",
                "password":{  
                    "type":"SecureString",
                    "value":"<password>"
                }
            },
            "connectVia":{  
                "referenceName":"<integration runtime name>",
                "type":"IntegrationRuntimeReference"
            }
        }
    } 
    ```

    > [!IMPORTANT]
    > - Selezionare la sezione in base all'autenticazione usata per connettersi all'istanza di SQL Server.
    > - Sostituire **\<integration runtime name>** con il nome del runtime di integrazione.
    > - Prima di salvare il file, sostituire **\<servername>** , **\<databasename>** , **\<username>** e **\<password>** con i valori dell'istanza di SQL Server.
    > - Se è necessario usare una barra rovesciata (\\) nell'account utente o nel nome del server, farla precedere dal carattere di escape (\\). Ad esempio, usare *mydomain\\\\myuser*. 

1. Per crittografare i dati sensibili (nome utente, password e così via), eseguire il cmdlet `New-AzDataFactoryV2LinkedServiceEncryptedCredential`.  
    Con questa crittografia, le credenziali vengono crittografate con Data Protection API (DPAPI). Le credenziali crittografate vengono archiviate in locale (computer locale) nel nodo del runtime di integrazione self-hosted. Il payload di output può essere reindirizzato a un altro file JSON (in questo caso *encryptedLinkedService.json*) che contiene le credenziali crittografate.
    
   ```powershell
   New-AzDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -IntegrationRuntimeName $integrationRuntimeName -File ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
   ```

1. Eseguire questo comando, che crea EncryptedSqlServerLinkedService:

   ```powershell
   Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -File ".\encryptedSqlServerLinkedService.json"
   ```


## <a name="create-datasets"></a>Creare set di dati
In questo passaggio vengono creati set di dati di input e di output, che rappresentano i dati di input e di output per l'operazione di copia dal database SQL Server locale all'archivio BLOB di Azure.

### <a name="create-a-dataset-for-the-source-sql-server-database"></a>Creare un set di dati per il database SQL Server di origine
In questo passaggio si definisce un set di dati che rappresenta i dati nell'istanza di database di SQL Server. Il set di dati è di tipo SqlServerTable. Fa riferimento al servizio collegato SQL Server creato nel passaggio precedente. Il servizio collegato ha le informazioni di connessione usate dal servizio Data Factory per connettersi all'istanza di SQL Server in fase di esecuzione. Questo set di dati specifica la tabella SQL nel database che contiene i dati. In questa esercitazione, la tabella **emp** contiene i dati di origine. 

1. Creare un file JSON denominato *SqlServerDataset.json* nella cartella *C:\ADFv2Tutorial* con il codice seguente:  
    ```json
    {  
        "name":"SqlServerDataset",
        "properties":{  
            "linkedServiceName":{  
                "referenceName":"EncryptedSqlServerLinkedService",
                "type":"LinkedServiceReference"
            },
            "annotations":[  
    
            ],
            "type":"SqlServerTable",
            "schema":[  
    
            ],
            "typeProperties":{  
                "schema":"dbo",
                "table":"emp"
            }
        }
    }
    ```

1. Per creare il set di dati SqlServerDataset, eseguire il cmdlet `Set-AzDataFactoryV2Dataset`.

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SqlServerDataset" -File ".\SqlServerDataset.json"
    ```

    Di seguito è riportato l'output di esempio:

    ```json
    DatasetName       : SqlServerDataset
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Structure         : 
    Properties        : Microsoft.Azure.Management.DataFactory.Models.SqlServerTableDataset
    ```

### <a name="create-a-dataset-for-azure-blob-storage-sink"></a>Creare un set di dati per l'archivio BLOB di Azure (sink)
In questo passaggio si definisce un set di dati che rappresenta i dati da copiare nell'archivio BLOB di Azure. Il set di dati è di tipo AzureBlob. Fa riferimento al servizio collegato Archiviazione di Azure creato in precedenza in questa esercitazione. 

Il servizio collegato ha le informazioni di connessione usate da Data Factory in fase di esecuzione per la connessione all'account di archiviazione di Azure. Questo set di dati specifica la cartella nella risorsa di archiviazione di Azure in cui vengono copiati i dati dal database di SQL Server. In questa esercitazione la cartella è *adftutorial/fromonprem* dove `adftutorial` è il contenitore BLOB e `fromonprem` è la cartella. 

1. Creare un file JSON denominato *AzureBlobDataset.json* nella cartella *C:\ADFv2Tutorial* con il codice seguente:

    ```json
    {  
        "name":"AzureBlobDataset",
        "properties":{  
            "linkedServiceName":{  
                "referenceName":"AzureStorageLinkedService",
                "type":"LinkedServiceReference"
            },
            "annotations":[  
    
            ],
            "type":"DelimitedText",
            "typeProperties":{  
                "location":{  
                    "type":"AzureBlobStorageLocation",
                    "folderPath":"fromonprem",
                    "container":"adftutorial"
                },
                "columnDelimiter":",",
                "escapeChar":"\\",
                "quoteChar":"\""
            },
            "schema":[  
    
            ]
        },
        "type":"Microsoft.DataFactory/factories/datasets"
    }
    ```

1. Per creare il set di dati AzureBlobDataset, eseguire il cmdlet `Set-AzDataFactoryV2Dataset`.

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureBlobDataset" -File ".\AzureBlobDataset.json"
    ```

    Di seguito è riportato l'output di esempio:

    ```json
    DatasetName       : AzureBlobDataset
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.DelimitedTextDataset
    ```

## <a name="create-a-pipeline"></a>Creare una pipeline
In questa esercitazione viene creata una pipeline con un'attività di copia. L'attività di copia usa SqlServerDataset come set di dati di input e AzureBlobDataset come set di dati di output. Il tipo di origine è impostato su *SqlSource* e il tipo di sink è impostato su *BlobSink*.

1. Creare un file JSON denominato *SqlServerToBlobPipeline.json* nella cartella *C:\ADFv2Tutorial* con il codice seguente:

    ```json
    {  
        "name":"SqlServerToBlobPipeline",
        "properties":{  
            "activities":[  
                {  
                    "name":"CopySqlServerToAzureBlobActivity",
                    "type":"Copy",
                    "dependsOn":[  
    
                    ],
                    "policy":{  
                        "timeout":"7.00:00:00",
                        "retry":0,
                        "retryIntervalInSeconds":30,
                        "secureOutput":false,
                        "secureInput":false
                    },
                    "userProperties":[  
    
                    ],
                    "typeProperties":{  
                        "source":{  
                            "type":"SqlServerSource"
                        },
                        "sink":{  
                            "type":"DelimitedTextSink",
                            "storeSettings":{  
                                "type":"AzureBlobStorageWriteSettings"
                            },
                            "formatSettings":{  
                                "type":"DelimitedTextWriteSettings",
                                "quoteAllText":true,
                                "fileExtension":".txt"
                            }
                        },
                        "enableStaging":false
                    },
                    "inputs":[  
                        {  
                            "referenceName":"SqlServerDataset",
                            "type":"DatasetReference"
                        }
                    ],
                    "outputs":[  
                        {  
                            "referenceName":"AzureBlobDataset",
                            "type":"DatasetReference"
                        }
                    ]
                }
            ],
            "annotations":[  
    
            ]
        }
    }
    ```

1. Eseguire il cmdlet `Set-AzDataFactoryV2Pipeline` per creare la pipeline SQLServerToBlobPipeline.

    ```powershell
    Set-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SQLServerToBlobPipeline" -File ".\SQLServerToBlobPipeline.json"
    ```

    Di seguito è riportato l'output di esempio:

    ```json
    PipelineName      : SQLServerToBlobPipeline
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {CopySqlServerToAzureBlobActivity}
    Parameters        :  
    ```

## <a name="create-a-pipeline-run"></a>Creare un'esecuzione della pipeline
Avviare un'esecuzione della pipeline SQLServerToBlobPipeline e acquisire l'ID di esecuzione della pipeline per il monitoraggio futuro.

```powershell
$runId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName 'SQLServerToBlobPipeline'
```

## <a name="monitor-the-pipeline-run"></a>Monitorare l'esecuzione della pipeline

1. Eseguire questo script in PowerShell per verificare continuamente lo stato di esecuzione della pipeline SQLServerToBlobPipeline e visualizzare il risultato finale:

    ```powershell
    while ($True) {
        $result = Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)

        if (($result | Where-Object { $_.Status -eq "InProgress" } | Measure-Object).count -ne 0) {
            Write-Host "Pipeline run status: In Progress" -foregroundcolor "Yellow"
            Start-Sleep -Seconds 30
        }
        else {
            Write-Host "Pipeline 'SQLServerToBlobPipeline' run finished. Result:" -foregroundcolor "Yellow"
            $result
            break
        }
    }
    ```

    Ecco l'output dell'esecuzione di esempio:

    ```JSON
    ResourceGroupName    : <resourceGroupName>
    DataFactoryName      : <dataFactoryName>
    ActivityRunId        : 24af7cf6-efca-4a95-931d-067c5c921c25
    ActivityName         : CopySqlServerToAzureBlobActivity
    ActivityType         : Copy
    PipelineRunId        : 7b538846-fd4e-409c-99ef-2475329f5729
    PipelineName         : SQLServerToBlobPipeline
    Input                : {source, sink, enableStaging}
    Output               : {dataRead, dataWritten, filesWritten, sourcePeakConnections...}
    LinkedServiceName    : 
    ActivityRunStart     : 9/11/2019 7:10:37 AM
    ActivityRunEnd       : 9/11/2019 7:10:58 AM
    DurationInMs         : 21094
    Status               : Succeeded
    Error                : {errorCode, message, failureType, target}
    AdditionalProperties : {[retryAttempt, ], [iterationHash, ], [userProperties, {}], [recoveryStatus, None]...}
    ```

1. È possibile ottenere l'ID di esecuzione della pipeline SQLServerToBlobPipeline e verificare il risultato dettagliato dell'esecuzione dell'attività usando il comando seguente: 

    ```powershell
    Write-Host "Pipeline 'SQLServerToBlobPipeline' run result:" -foregroundcolor "Yellow"
    ($result | Where-Object {$_.ActivityName -eq "CopySqlServerToAzureBlobActivity"}).Output.ToString()
    ```

    Ecco l'output dell'esecuzione di esempio:

    ```json
    {  
        "dataRead":36,
        "dataWritten":32,
        "filesWritten":1,
        "sourcePeakConnections":1,
        "sinkPeakConnections":1,
        "rowsRead":2,
        "rowsCopied":2,
        "copyDuration":18,
        "throughput":0.01,
        "errors":[  
    
        ],
        "effectiveIntegrationRuntime":"ADFTutorialIR",
        "usedParallelCopies":1,
        "executionDetails":[  
            {  
                "source":{  
                    "type":"SqlServer"
                },
                "sink":{  
                    "type":"AzureBlobStorage",
                    "region":"CentralUS"
                },
                "status":"Succeeded",
                "start":"2019-09-11T07:10:38.2342905Z",
                "duration":18,
                "usedParallelCopies":1,
                "detailedDurations":{  
                    "queuingDuration":6,
                    "timeToFirstByte":0,
                    "transferDuration":5
                }
            }
        ]
    }
    ```

## <a name="verify-the-output"></a>Verificare l'output
La pipeline crea automaticamente la cartella di output denominata *fromonprem* nel contenitore BLOB `adftutorial`. Assicurarsi che nella cartella di output sia presente il file *dbo.emp.txt*. 

1. Nella finestra del contenitore **adftutorial** del portale di Azure fare clic su **Aggiorna** per visualizzare la cartella di output.
1. Selezionare `fromonprem` nell'elenco di cartelle. 
1. Verificare che venga visualizzato un file denominato `dbo.emp.txt`.

    ![File di output](media/tutorial-hybrid-copy-powershell/fromonprem-file.png)


## <a name="next-steps"></a>Passaggi successivi
La pipeline in questo esempio copia i dati da una posizione a un'altra in un archivio BLOB di Azure. Si è appreso come:

> [!div class="checklist"]
> * Creare una data factory.
> * Creare un runtime di integrazione self-hosted.
> * Creare servizi collegati per SQL Server e Archiviazione di Azure. 
> * Creare set di dati per SQL Server e BLOB di Azure.
> * Creare una pipeline con attività di copia per trasferire i dati.
> * Avviare un'esecuzione della pipeline.
> * Monitorare l'esecuzione della pipeline.

Per un elenco degli archivi dati supportati da Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

Passare all'esercitazione successiva per ottenere informazioni sulla copia di dati in blocco da un'origine a una destinazione:

> [!div class="nextstepaction"]
>[Copiare dati in blocco](tutorial-bulk-copy.md)
