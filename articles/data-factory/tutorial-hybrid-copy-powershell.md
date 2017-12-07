---
title: Copiare dati da SQL Server a un archivio BLOB usando Azure Data Factory | Microsoft Docs
description: Informazioni su come copiare dati da un archivio dati locale al cloud di Azure usando un runtime di integrazione self-hosted di Azure Data Factory.
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/30/2017
ms.author: jingwang
ms.openlocfilehash: ba47f3e3f331929b884f27f49bf6e484ff363765
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2017
---
# <a name="tutorial-copy-data-from-on-premises-sql-server-to-azure-blob-storage"></a>Esercitazione: Copiare i dati da un'istanza di SQL Server locale a un archivio BLOB di Azure
In questa esercitazione si usa Azure PowerShell per creare una pipeline di Data Factory che copia i dati da un database di SQL Server locale a un archivio BLOB di Azure. Si crea e si usa un runtime di integrazione self-hosted, che sposta i dati tra gli archivi dati locali e cloud. 

> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere la [documentazione su Data Factory versione 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
> 
> Questo articolo non offre una presentazione dettagliata del servizio Data Factory. Per un'introduzione al servizio Azure Data Factory, vedere [Introduzione ad Azure Data Factory](introduction.md). 

In questa esercitazione vengono completati i passaggi seguenti:

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
Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

### <a name="azure-roles"></a>Ruoli di Azure
Per creare istanze di Data Factory, l'account utente usato per accedere ad Azure deve essere un membro dei ruoli **collaboratore** o **proprietario** oppure un **amministratore** della sottoscrizione di Azure. Nel portale di Azure fare clic sul **nome utente** nell'angolo in alto a destra e selezionare **Autorizzazioni** per visualizzare le autorizzazioni disponibili nella sottoscrizione. Se si accede a più sottoscrizioni, selezionare quella appropriata. Per istruzioni di esempio sull'aggiunta di un utente a un ruolo, vedere l'articolo [Aggiungere i ruoli](../billing/billing-add-change-azure-subscription-administrator.md).

### <a name="sql-server-201420162017"></a>SQL Server 2014/2016/2017
Usare un database di SQL Server locale come archivio dati di **origine** in questa esercitazione. La pipeline nella data factory creata in questa esercitazione copia i dati da questo database di SQL Server locale (origine) a un archivio BLOB di Azure (sink). Creare una tabella denominata **emp** nel database SQL Server e inserire una coppia di voci di esempio nella tabella. 

1. Avviare **SQL Server Management Studio** sul computer. Se SQL Server Management Studio non disponibile sul computer, installarlo dall'[Area download](https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms). 
2. Connettersi all'istanza di SQL Server usando le credenziali. 
3. Creare un database di esempio. Nella visualizzazione struttura ad albero fare clic con il pulsante destro del mouse su **Database** e scegliere **Nuovo database**. Nella finestra di dialogo **Nuovo database** immettere un **nome** per il database e fare clic su **OK**. 
4. Eseguire questo script di query sul database per creare la tabella **emp** e inserirvi alcuni dati di esempio. Nella visualizzazione struttura ad albero fare clic con il pulsante destro del mouse sul **database** creato e scegliere **Nuova query**. 

    ```sql   
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50),
        CONSTRAINT PK_emp PRIMARY KEY (ID)
    )

    INSERT INTO emp VALUES ('John', 'Doe')
    INSERT INTO emp VALUES ('Jane', 'Doe')
    GO
    ```

### <a name="azure-storage-account"></a>Account di archiviazione di Azure
In questa esercitazione si usa un account di archiviazione di Azure per utilizzo generico (specificamente, un archivio BLOB) come archivio dati di **destinazione/sink**. Se non si ha un account di archiviazione di Azure per utilizzo generico, vedere [Creare un account di archiviazione](../storage/common/storage-create-storage-account.md#create-a-storage-account) per informazioni su come crearne uno. La pipeline nella data factory creata in questa esercitazione copia i dati dal database di SQL Server locale (origine) a questo archivio BLOB di Azure (sink). 

#### <a name="get-storage-account-name-and-account-key"></a>Recuperare il nome e la chiave dell'account di archiviazione
In questa esercitazione si usano il nome e la chiave dell'account di archiviazione di Azure. La procedura seguente illustra i passaggi per recuperare il nome e la chiave dell'account di archiviazione. 

1. Avviare un Web browser e passare al [portale di Azure](https://portal.azure.com). Eseguire l'accesso con il nome utente e la password di Azure. 
2. Fare clic su **Altri servizi** nel menu a sinistra, usare la parola chiave **Archiviazione** come filtro e selezionare **Account di archiviazione**.

    ![Cercare l'account di archiviazione](media/tutorial-hybrid-copy-powershell/search-storage-account.png)
3. Nell'elenco degli account di archiviazione filtrare, se necessario, e quindi selezionare il **proprio account di archiviazione**. 
4. Nella pagina **Account di archiviazione** selezionare **Chiavi di accesso** nel menu.

    ![Recuperare il nome e la chiave dell'account di archiviazione](media/tutorial-hybrid-copy-powershell/storage-account-name-key.png)
5. Copiare i valori dei campi **Nome account di archiviazione** e **key1** negli Appunti. Incollarli in un blocco note o in qualsiasi altro editor e salvare. Nell'esercitazione si usano il nome e la chiave dell'account di archiviazione. 

#### <a name="create-the-adftutorial-container"></a>Creare il contenitore adftutorial 
In questa sezione si crea un contenitore BLOB denominato **adftutorial** nell'archivio BLOB di Azure. 

1. Nella pagina **Account di archiviazione** passare a **Panoramica** e quindi fare clic su **BLOB**. 

    ![Selezionare l'opzione BLOB](media/tutorial-hybrid-copy-powershell/select-blobs.png)
1. Nella pagina **Servizio BLOB** fare clic su **+ Contenitore** sulla barra degli strumenti. 

    ![Pulsante Aggiungi contenitore](media/tutorial-hybrid-copy-powershell/add-container-button.png)
3. Nella finestra di dialogo **Nuovo contenitore** immettere **adftutorial** come nome e fare clic su **OK**. 

    ![Immettere il nome del contenitore](media/tutorial-hybrid-copy-powershell/new-container-dialog.png)
4. Fare clic su **adftutorial** nell'elenco di contenitori.  

    ![Selezionare il contenitore](media/tutorial-hybrid-copy-powershell/seelct-adftutorial-container.png)
5. Tenere aperta la pagina **Contenitore** per **adftutorial** perché verrà usata per verificare l'output alla fine di questa esercitazione. Data Factory crea automaticamente la cartella di output in questo contenitore, quindi non è necessario crearne uno.

    ![Pagina Contenitore](media/tutorial-hybrid-copy-powershell/container-page.png)

### <a name="windows-powershell"></a>Windows PowerShell

#### <a name="install-powershell"></a>Installare PowerShell
Installare l'ultima versione di PowerShell, se non è già presente nel computer. 

1. Nel Web browser passare alla pagina relativa a [SDK e download per Azure SDK](https://azure.microsoft.com/downloads/). 
2. Fare clic su **Windows - Installazione** nella sezione **Strumenti da riga di comando** -> **PowerShell**. 
3. Per installare Azure PowerShell, eseguire il file **MSI**. 

Per istruzioni dettagliate, vedere [Come installare e configurare Azure PowerShell](/powershell/azure/install-azurerm-ps). 

#### <a name="log-in-to-powershell"></a>Accedere a PowerShell

1. Avviare **PowerShell** nel computer. Tenere aperta la finestra di PowerShell fino alla fine della guida introduttiva. Se si chiude e si riapre, sarà necessario eseguire di nuovo questi comandi.

    ![Avviare PowerShell](media/tutorial-hybrid-copy-powershell/search-powershell.png)
1. Eseguire questo comando e immettere il nome utente e la password di Azure usati per accedere al portale di Azure:
       
    ```powershell
    Login-AzureRmAccount
    ```        
2. Se si hanno più sottoscrizioni di Azure, eseguire questo comando per visualizzare tutte le sottoscrizioni per l'account:

    ```powershell
    Get-AzureRmSubscription
    ```
3. Se si hanno più sottoscrizioni di Azure, eseguire questo comando per selezionare la sottoscrizione che si vuole usare. Sostituire **SubscriptionId** con l'ID della sottoscrizione di Azure:

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<SubscriptionId>"       
    ```

## <a name="create-a-data-factory"></a>Creare un'istanza di Data factory

1. Definire una variabile per il nome del gruppo di risorse usato in seguito nei comandi di PowerShell. Copiare il testo del comando seguente in PowerShell, specificare un nome per il [gruppo di risorse di Azure](../azure-resource-manager/resource-group-overview.md) tra virgolette doppie e quindi eseguire il comando. Ad esempio: `"adfrg"`. 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup"
    ```
2. Per creare il gruppo di risorse di Azure, eseguire questo comando: 

    ```powershell
    New-AzureRmResourceGroup $resourceGroupName $location
    ``` 

    Se il gruppo di risorse esiste già, potrebbe essere preferibile non sovrascriverlo. Assegnare un valore diverso alla variabile `$resourceGroupName` ed eseguire di nuovo il comando.
3. Definire una variabile per il nome della data factory utilizzabile in seguito nei comandi di PowerShell. Il nome deve iniziare con una lettera o un numero e può contenere solo lettere, numeri e il carattere trattino (-).

    > [!IMPORTANT]
    >  Aggiornare il nome della data factory in modo che sia univoco a livello globale. Ad esempio, ADFTutorialFactorySP1127. 

    ```powershell
    $dataFactoryName = "ADFTutorialFactory"
    ```
1. Definire una variabile per la località della data factory: 

    ```powershell
    $location = "East US"
    ```  
5. Per creare la data factory, eseguire questo cmdlet **Set-AzureRmDataFactoryV2**: 
    
    ```powershell       
    Set-AzureRmDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName 
    ```

Tenere presente quanto segue:

* È necessario specificare un nome univoco globale per l'istanza di Azure Data Factory. Se viene visualizzato l'errore seguente, modificare il nome e riprovare.

    ```
    The specified Data Factory name 'ADFv2TutorialDataFactory' is already in use. Data Factory names must be globally unique.
    ```

* Per creare istanze di Data Factory, l'account utente usato per accedere ad Azure deve essere un membro dei ruoli **collaboratore** o **proprietario** oppure un **amministratore** della sottoscrizione di Azure.
* Data Factory versione 2 consente attualmente di creare data factory solo nelle aree Stati Uniti orientali, Stati Uniti orientali 2 ed Europa occidentale. Gli archivi dati (Archiviazione di Azure, database SQL di Azure e così via) e le risorse di calcolo (HDInsight e così via) usati dalla data factory possono trovarsi in altre aree.

## <a name="create-a-self-hosted-ir"></a>Creare un runtime di integrazione self-hosted

In questa sezione si crea un runtime di integrazione self-hosted e lo si associa a un computer locale con il database di SQL Server. Il runtime di integrazione self-hosted è il componente che copia i dati da SQL Server sul computer all'archivio BLOB di Azure. 

1. Creare una variabile per il nome del runtime di integrazione. Usare un nome univoco e annotarlo. perché sarà usato più avanti in questa esercitazione. 

    ```powershell
   $integrationRuntimeName = "ADFTutorialIR"
    ```
1. Creare un runtime di integrazione self-hosted. 

   ```powershell
   Set-AzureRmDataFactoryV2IntegrationRuntime -Name $integrationRuntimeName -Type SelfHosted -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName
   ```

   Di seguito è riportato l'output di esempio:

   ```json
    Id                : /subscriptions/<subscription ID>/resourceGroups/ADFTutorialResourceGroup/providers/Microsoft.DataFactory/factories/onpremdf0914/integrationruntimes/myonpremirsp0914
    Type              : SelfHosted
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Name              : myonpremirsp0914
    Description       :
    ```
 

2. Eseguire questo comando per recuperare lo stato del runtime di integrazione creato.

   ```powershell
   Get-AzureRmDataFactoryV2IntegrationRuntime -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Status
   ```

   Di seguito è riportato l'output di esempio:

   ```json
   Nodes                     : {}
   CreateTime                : 9/14/2017 10:01:21 AM
   InternalChannelEncryption :
   Version                   :
   Capabilities              : {}
   ScheduledUpdateDate       :
   UpdateDelayOffset         :
   LocalTimeZoneOffset       :
   AutoUpdate                :
   ServiceUrls               : {eu.frontend.clouddatahub.net, *.servicebus.windows.net}
   ResourceGroupName         : <ResourceGroup name>
   DataFactoryName           : <DataFactory name>
   Name                      : <Integration Runtime name>
   State                     : NeedRegistration
   ```

3. Eseguire questo comando per recuperare le **chiavi di autenticazione** per la registrazione del runtime di integrazione self-hosted nel servizio Data Factory nel cloud. 

   ```powershell
   Get-AzureRmDataFactoryV2IntegrationRuntimeKey -Name $integrationRuntimeName -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName | ConvertTo-Json
   ```

   Di seguito è riportato l'output di esempio:

   ```json
   {
       "AuthKey1":  "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx=",
       "AuthKey2":  "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@yyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyy="
   }
   ```    
4. Copiare una delle chiavi (escludere le virgolette doppie) per la registrazione del runtime di integrazione self-hosted che si installerà nel computer nel passaggio successivo.  

## <a name="install-integration-runtime"></a>Installare il runtime di integrazione
1. [Scaricare](https://www.microsoft.com/download/details.aspx?id=39717) il runtime di integrazione self-hosted in un computer Windows locale, quindi eseguire l'installazione. 
2. Nella pagina iniziale dell'**installazione guidata di Microsoft Integration Runtime** fare clic su **Avanti**.  
3. Nella pagina **Contratto di licenza con l'utente finale** accettare le condizioni e fare clic su **Avanti**. 
4. Nella pagina **Cartella di destinazione** fare clic su **Avanti**. 
5. Nella pagina **Pronto per l'installazione** fare clic su **Installa**. 
6. Se viene visualizzato un messaggio di avviso relativo al computer configurato per passare alla modalità di sospensione o di ibernazione quando non è in uso, fare clic su **OK**. 
7. Se viene visualizzata la finestra **Opzioni risparmio energia**, chiuderla e passare alla finestra di configurazione. 
8. Nella pagina **Installazione di Microsoft Integration Runtime completata** fare clic su **Fine**.
9. Nella pagina **Registra Runtime di integrazione (self-hosted)** incollare la chiave salvata nella sezione precedente e fare clic su **Registra**. 

   ![Registrare il runtime di integrazione](media/tutorial-hybrid-copy-powershell/register-integration-runtime.png)
2. Al termine della registrazione del runtime di integrazione self-hosted viene visualizzato il messaggio seguente:

   ![Registrazione completata](media/tutorial-hybrid-copy-powershell/registered-successfully.png)

3. Nella pagina **Nuovo nodo di Runtime di integrazione (self-hosted)** fare clic su **Avanti**. 

    ![Pagina Nuovo nodo di Runtime di integrazione](media/tutorial-hybrid-copy-powershell/new-integration-runtime-node-page.png)
4. Nella pagina **Canale di comunicazione Intranet** fare clic su **Ignora**. È possibile selezionare un certificato TLS/SSL per proteggere la comunicazione tra nodi in un ambiente di runtime di integrazione di più nodi. 

    ![Pagina Canale di comunicazione Intranet](media/tutorial-hybrid-copy-powershell/intranet-communication-channel-page.png)
5. Nella pagina **Registra Runtime di integrazione (self-hosted)** fare clic su **Avvia Configuration Manager**. 
6. Quando il nodo viene connesso al servizio cloud, viene visualizzata la pagina seguente:

   ![Connessione del nodo](media/tutorial-hybrid-copy-powershell/node-is-connected.png)
7. Testare ora la connettività al database di SQL Server.

    ![Scheda Diagnostica](media/tutorial-hybrid-copy-powershell/config-manager-diagnostics-tab.png)   

    - Nella finestra **Gestione configurazione** passare alla scheda **Diagnostica**.
    - Selezionare **SqlServer** in **Tipo di origine dati**.
    - Immettere il nome del **server**.
    - Immettere il nome del **database**. 
    - Selezionare la modalità di **autenticazione**. 
    - Immettere il nome **utente**. 
    - Immettere la **password** per il nome utente.
    - Fare clic su **Test** per verificare che il runtime di integrazione possa connettersi a SQL Server. Se la connessione ha esito positivo, viene visualizzato un segno di spunta verde. In caso contrario, viene visualizzato un messaggio associato all'errore. Risolvere eventuali problemi e assicurarsi che il runtime di integrazione possa connettesi a SQL Server.
    - Annotare questi valori, ovvero tipo di autenticazione, server, database, utente, password, perché saranno usati più avanti in questa esercitazione. 
    
      
## <a name="create-linked-services"></a>Creare servizi collegati
Creare servizi collegati in una data factory per collegare gli archivi dati e i servizi di calcolo alla data factory. In questa esercitazione si collegano l'account di archiviazione di Azure e SQL Server locale all'archivio dati. I servizi collegati hanno le informazioni di connessione usate dal servizio Data Factory in fase di esecuzione per la connessione. 

### <a name="create-an-azure-storage-linked-service-destinationsink"></a>Creare un servizio collegato Archiviazione di Azure (destinazione/sink)
In questo passaggio l'account di archiviazione di Azure viene collegato alla data factory.

1. Creare un file JSON denominato **AzureStorageLinkedService.json** nella cartella **C:\ADFv2Tutorial** con il contenuto seguente. Creare la cartella ADFv2Tutorial se non esiste già.  

    > [!IMPORTANT]
    > Sostituire &lt;accountName&gt; e &lt;accountKey&gt; con il nome e la chiave dell'**account di archiviazione di Azure** prima di salvare il file. Questi valori sono stati annotati come parte dei [prerequisiti](#get-storage-account-name-and-account-key).

   ```json
    {
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>;EndpointSuffix=core.windows.net"
                }
            }
        },
        "name": "AzureStorageLinkedService"
    }
   ```

    Se si usa Blocco note, selezionare **Tutti i file** per il campo **Tipo file** nella finestra di dialogo **Salva con nome**. In caso contrario, è possibile che venga aggiunta l'estensione `.txt` al file. ad esempio `AzureStorageLinkedService.json.txt`. Se si crea il file in Esplora file prima di aprirlo in Blocco note, è possibile che l'estensione `.txt` non venga visualizzata perché l'opzione **Nascondi estensioni per i tipi di file conosciuti** è selezionata per impostazione predefinita. Rimuovere l'estensione `.txt` prima di procedere al passaggio successivo. 
2. In **Azure PowerShell** passare alla cartella **C:\ADFv2Tutorial**.

   Eseguire il cmdlet **Set-AzureRmDataFactoryV2LinkedService** per creare il servizio collegato **AzureStorageLinkedService**. 

   ```powershell
   Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "AzureStorageLinkedService" -File ".\AzureStorageLinkedService.json"
   ```

   Di seguito è riportato un output di esempio:

    ```json
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureStorageLinkedService
    ```

    Se viene visualizzato un errore di tipo "File non trovato". Eseguire il comando `dir` per confermare che il file esiste. Se il nome del file ha estensione `.txt`, ad esempio AzureStorageLinkedService.json.txt, rimuovere l'estensione ed eseguire di nuovo il comando di PowerShell. 

### <a name="create-and-encrypt-a-sql-server-linked-service-source"></a>Creare e crittografare un servizio collegato SQL Server (origine)
In questo passaggio si collega SQL Server locale alla data factory.

1. Creare un file JSON denominato **SqlServerLinkedService.json** nella cartella **C:\ADFv2Tutorial** con il contenuto seguente. Selezionare la sezione corretta in base all'**autenticazione** usata per connettersi a SQL Server.  

    > [!IMPORTANT]
    > Selezionare la sezione corretta in base all'**autenticazione** usata per connettersi a SQL Server.

    **Se si usa l'autenticazione SQL, copiare la definizione JSON seguente:**

    ```json
    {
        "properties": {
            "type": "SqlServer",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "Server=<servername>;Database=<databasename>;User ID=<username>;Password=<password>;Timeout=60"
                }
            },
            "connectVia": {
                "type": "integrationRuntimeReference",
                "referenceName": "<integration runtime name>"
            }
        },
        "name": "SqlServerLinkedService"
    }
   ```    
    **Se si usa l'autenticazione Windows, copiare la definizione JSON seguente:**

    ```json
    {
        "properties": {
            "type": "SqlServer",
            "typeProperties": {
                "connectionString": {
                    "type": "SecureString",
                    "value": "Server=<server>;Database=<database>;Integrated Security=True"
                },
                "userName": "<user> or <domain>\\<user>",
                "password": {
                    "type": "SecureString",
                    "value": "<password>"
                }
            },
            "connectVia": {
                "type": "integrationRuntimeReference",
                "referenceName": "<integration runtime name>"
            }
        },
        "name": "SqlServerLinkedService"
    }    
    ```
    > [!IMPORTANT]
    > - Selezionare la sezione corretta in base all'**autenticazione** usata per connettersi a SQL Server.
    > - Sostituire **&lt;integration** **runtime** **name>** con il nome del runtime di integrazione.
    > - Sostituire **&lt;servername>**, **&lt;databasename>**, **&lt;username>** e **&lt;password>** con i valori del server SQL prima di salvare il file.
    > - Se è necessario usare una barra (`\`) nell'account utente o nel nome del server, usare il carattere di escape (`\`). ad esempio `mydomain\\myuser`. 

2. Per crittografare i dati sensibili (nome utente, password e così via), eseguire il cmdlet **New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential**. Con questa crittografia, le credenziali vengono crittografate con Data Protection API (DPAPI). Le credenziali crittografate vengono archiviate in locale (computer locale) nel nodo del runtime di integrazione self-hosted. Il payload di output può essere reindirizzato a un altro file JSON (in questo caso, encryptedLinkedService.json) che contiene le credenziali crittografate.
    
   ```powershell
   New-AzureRmDataFactoryV2LinkedServiceEncryptedCredential -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -IntegrationRuntimeName $integrationRuntimeName -File ".\SQLServerLinkedService.json" > encryptedSQLServerLinkedService.json
   ```

3. Eseguire il comando seguente, che crea **EncryptedSqlServerLinkedService**:

   ```powershell
   Set-AzureRmDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $ResourceGroupName -Name "EncryptedSqlServerLinkedService" -File ".\encryptedSqlServerLinkedService.json"
   ```


## <a name="create-datasets"></a>Creare set di dati
In questo passaggio vengono creati i set di dati di input e di output che rappresentano i dati di input e di output per l'operazione di copia (database SQL Server locale => archiviazione BLOB di Azure).

### <a name="create-a-dataset-for-source-sql-database"></a>Creare un set di dati per il database SQL di origine
In questo passaggio si definisce un set di dati che rappresenta i dati nel database di SQL Server. Il set di dati è di tipo **SqlServerTable**. Fa riferimento al **servizio collegato SQL Server** creato nel passaggio precedente. Il servizio collegato ha le **informazioni di connessione** usate dal servizio Data Factory per connettersi al server SQL in fase di esecuzione. Questo set di dati specifica la **tabella SQL** nel database che contiene i dati. In questa esercitazione si tratta della tabella `emp` che contiene l'origine dati. 

1. Creare un file JSON denominato **SqlServerDataset.json** nella cartella **C:\ADFv2Tutorial** con il contenuto seguente:  

    ```json
    {
       "properties": {
            "type": "SqlServerTable",
            "typeProperties": {
                "tableName": "dbo.emp"
            },
            "structure": [
                 {
                    "name": "ID",
                    "type": "String"
                },
                {
                    "name": "FirstName",
                    "type": "String"
                },
                {
                    "name": "LastName",
                    "type": "String"
                }
            ],
            "linkedServiceName": {
                "referenceName": "EncryptedSqlServerLinkedService",
                "type": "LinkedServiceReference"
            }
        },
        "name": "SqlServerDataset"
    }
    ```

2. Per creare il set di dati **SqlServerDataset**, eseguire il cmdlet **Set-AzureRmDataFactoryV2Dataset**.

    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SqlServerDataset" -File ".\SqlServerDataset.json"
    ```

    Di seguito è riportato l'output di esempio:

    ```json
    DatasetName       : SqlServerDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Structure         : {"name": "ID" "type": "String", "name": "FirstName" "type": "String", "name": "LastName" "type": "String"}
    Properties        : Microsoft.Azure.Management.DataFactory.Models.SqlServerTableDataset
    ```

### <a name="create-a-dataset-for-sink-azure-blob-storage"></a>Creare un set di dati per l'archivio BLOB di Azure sink
In questo passaggio si definisce un set di dati che rappresenta i dati da copiare nell'archivio BLOB di Azure. Il set di dati è di tipo **AzureBlob**. Fa riferimento al **servizio collegato Archiviazione di Azure** creato prima in questa esercitazione. Il servizio collegato ha le **informazioni di connessione** usate da Data Factory in fase di esecuzione per la connessione all'account di archiviazione di Azure. Questo **set di dati** specifica la **cartella** nella risorsa di archiviazione di Azure in cui vengono copiati i dati dal database di SQL Server. In questa esercitazione la cartella è `adftutorial/fromonprem` dove `adftutorial` è il contenitore BLOB e `fromonprem` è la cartella. 

1. Creare un file JSON denominato **AzureBlobDataset.json** nella cartella **C:\ADFv2Tutorial** con il contenuto seguente:

    ```json
    {
        "properties": {
            "type": "AzureBlob",
            "typeProperties": {
                "folderPath": "adftutorial/fromonprem",
                "format": {
                    "type": "TextFormat"
                }
            },
            "linkedServiceName": {
                "referenceName": "AzureStorageLinkedService",
                "type": "LinkedServiceReference"
            }
        },
        "name": "AzureBlobDataset"
    }
    ```

2. Per creare il set di dati **AzureBlobDataset**, eseguire il cmdlet **Set-AzureRmDataFactoryV2Dataset**.

    ```powershell
    Set-AzureRmDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureBlobDataset" -File ".\AzureBlobDataset.json"
    ```

    Di seguito è riportato l'output di esempio:

    ```json
    DatasetName       : AzureBlobDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobDataset
    ```

## <a name="create-a-pipeline"></a>Creare una pipeline
In questa esercitazione viene creata una pipeline con un'attività di copia. L'attività di copia usa **SqlServerDataset** come set di dati di input e **AzureBlobDataset** come set di dati di output. Il tipo di origine è impostato su **SqlSource** e il tipo di sink è impostato su **BlobSink**.

1. Creare un file JSON denominato **SqlServerToBlobPipeline.json** nella cartella **C:\ADFv2Tutorial** con il contenuto seguente:

    ```json
    {
       "name": "SQLServerToBlobPipeline",
        "properties": {
            "activities": [       
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource"
                        },
                        "sink": {
                            "type":"BlobSink"
                        }
                    },
                    "name": "CopySqlServerToAzureBlobActivity",
                    "inputs": [
                        {
                            "referenceName": "SqlServerDataset",
                            "type": "DatasetReference"
                        }
                    ],
                    "outputs": [
                        {
                            "referenceName": "AzureBlobDataset",
                            "type": "DatasetReference"
                        }
                    ]
                }
            ]
        }
    }
    ```

2. Per creare la pipeline **SQLServerToBlobPipeline**, eseguire il cmdlet **Set-AzureRmDataFactoryV2Pipeline**.

    ```powershell
    Set-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SQLServerToBlobPipeline" -File ".\SQLServerToBlobPipeline.json"
    ```

    Di seguito è riportato l'output di esempio:

    ```json
    PipelineName      : SQLServerToBlobPipeline
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : onpremdf0914
    Activities        : {CopySqlServerToAzureBlobActivity}
    Parameters        :  
    ```


## <a name="create-a-pipeline-run"></a>Creare un'esecuzione della pipeline
Avviare un'esecuzione della pipeline "SQLServerToBlobPipeline" e acquisire l'ID di esecuzione della pipeline per il monitoraggio futuro.

```powershell
$runId = Invoke-AzureRmDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName 'SQLServerToBlobPipeline'
```

## <a name="monitor-the-pipeline-run"></a>Monitorare l'esecuzione della pipeline

1. Eseguire questo script per verificare continuamente lo stato di esecuzione della pipeline **SQLServerToBlobPipeline** e visualizzare il risultato finale. Copiare/Incollare lo script seguente nella finestra di PowerShell e premere INVIO.

    ```powershell
    while ($True) {
        $result = Get-AzureRmDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)

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

    ```jdon
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    ActivityName      : copy
    PipelineRunId     : 4ec8980c-62f6-466f-92fa-e69b10f33640
    PipelineName      : SQLServerToBlobPipeline
    Input             :  
    Output            :  
    LinkedServiceName :
    ActivityRunStart  : 9/13/2017 1:35:22 PM
    ActivityRunEnd    : 9/13/2017 1:35:42 PM
    DurationInMs      : 20824
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    ```

3. È possibile ottenere l'ID di esecuzione della pipeline **SQLServerToBlobPipeline** e verificare il risultato dettagliato dell'esecuzione dell'attività usando il comando seguente: 

    ```powershell
    Write-Host "Pipeline 'SQLServerToBlobPipeline' run result:" -foregroundcolor "Yellow"
    ($result | Where-Object {$_.ActivityName -eq "CopySqlServerToAzureBlobActivity"}).Output.ToString()
    ```

    Ecco l'output dell'esecuzione di esempio:

    ```json
    {
      "dataRead": 36,
      "dataWritten": 24,
      "rowsCopied": 2,
      "copyDuration": 3,
      "throughput": 0.01171875,
      "errors": [],
      "effectiveIntegrationRuntime": "MyIntegrationRuntime",
      "billedDuration": 3
    }
    ```

## <a name="verify-the-output"></a>Verificare l'output
La pipeline crea automaticamente la cartella di output denominata `fromonprem` nel contenitore BLOB `adftutorial`. Assicurarsi che nella cartella di output sia presente il file **dbo.emp.txt**. 

1. Nella pagina del contenitore **adftutorial** del portale di Azure fare clic su **Aggiorna** per visualizzare la cartella di output.

    ![Cartella di output creata](media/tutorial-hybrid-copy-powershell/fromonprem-folder.png)
2. Fare clic su `fromonprem` nell'elenco di cartelle. 
3. Verificare che venga visualizzato un file denominato `dbo.emp.txt`.

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

Per un elenco degli archivi dati supportati da Azure Data Factory, gli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

Passare all'esercitazione successiva per ottenere informazioni sulla copia di dati in blocco da un'origine a una destinazione:

> [!div class="nextstepaction"]
>[Copiare dati in blocco](tutorial-bulk-copy.md)
