---
title: Spostare dati con Gateway di gestione dati | Microsoft Docs
description: Configurare un gateway dati per spostare dati tra origini locali e il cloud. Usare Gateway di gestione dati in Azure Data Factory per spostare dati.
keywords: gateway dati, integrazione di dati, spostare dati, credenziali del gateway
services: data-factory
documentationcenter: 
author: nabhishek
manager: jhubbard
editor: monicar
ms.assetid: 7bf6d8fd-04b5-499d-bd19-eff217aa4a9c
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: abnarain
ms.translationtype: Human Translation
ms.sourcegitcommit: e1d44f85b36d08944351a79d7a4b39cc8de61201
ms.openlocfilehash: 13044cc92a1577185b2aebc3a0ff8be0ec5eca60
ms.contentlocale: it-it
ms.lasthandoff: 11/17/2016

---
# <a name="move-data-between-on-premises-sources-and-the-cloud-with-data-management-gateway"></a>Spostare dati tra origini locali e il cloud con Gateway di gestione dati
Questo articolo offre una panoramica sull'integrazione tra archivi dati locali e archivi dati cloud con Data Factory. Si basa sull'articolo [Attività di spostamento dei dati](data-factory-data-movement-activities.md) e su altri articoli che illustrano i concetti di base relativi a Data Factory: [set di dati](data-factory-create-datasets.md) e [pipeline](data-factory-create-pipelines.md).

## <a name="data-management-gateway"></a>Gateway di gestione dati
È necessario installare il Gateway di gestione di dati sul computer locale per abilitare lo spostamento dei dati a/da un archivio dati locale. Il gateway può essere installato sullo stesso computer dell'archivio dati o su un computer diverso purché il gateway possa connettersi all'archivio dati.

> [!IMPORTANT]
> Leggere l'articolo [Gateway di gestione dati](data-factory-data-management-gateway.md) per i dettagli sul Gateway di gestione dati.   
>
>

Questa procedura dettagliata illustra come creare un'istanza di Data Factory con una pipeline che sposta i dati da un database di **SQL Server** locale a un archivio BLOB di Azure. Come parte della procedura dettagliata, viene installato e configurato il gateway di gestione dati nel computer.

## <a name="walkthrough-copy-on-premises-data-to-cloud"></a>Procedura dettagliata: Copiare i dati locali nel cloud
## <a name="create-data-factory"></a>Creare un'istanza di Data Factory
In questo passaggio si usa il portale di Azure per creare un'istanza di Azure Data Factory denominata **ADFTutorialOnPremDF**.

1. Accedere al [Portale di Azure](https://portal.azure.com).
2. Fare clic su **+ Nuovo**, su **Intelligence e analisi** e quindi su **Data factory**.

   ![Nuovo->DataFactory](./media/data-factory-move-data-between-onprem-and-cloud/NewDataFactoryMenu.png)  
3. Nel pannello **Nuova data factory** immettere **ADFTutorialOnPremDF** come nome.

    ![Aggiungi a schermata iniziale](./media/data-factory-move-data-between-onprem-and-cloud/OnPremNewDataFactoryAddToStartboard.png)

   > [!IMPORTANT]
   > È necessario specificare un nome univoco globale per l'istanza di Azure Data Factory. Se viene visualizzato un errore simile a **Nome "ADFTutorialOnPremDF" per la data factory non disponibile**, cambiare il nome della data factory (ad esempio, nomeutenteADFTutorialOnPremDF) e provare di nuovo a crearla. Durante l'esecuzione dei passaggi rimanenti in questa esercitazione usare questo nome anziché ADFTutorialOnPremDF.
   >
   > Il nome della data factory può essere registrato in futuro come nome **DNS** e diventare quindi visibile pubblicamente.
   >
   >
4. Selezionare la **sottoscrizione di Azure** in cui creare la data factory.
5. Selezionare un **gruppo di risorse** esistente o crearne uno. Per l'esercitazione creare un gruppo di risorse denominato **ADFTutorialResourceGroup**.
6. Fare clic su **Crea** nel pannello **Nuova data factory**.

   > [!IMPORTANT]
   > Per creare istanze di data factory, è necessario essere membri del ruolo [Collaboratore Data factory](../active-directory/role-based-access-built-in-roles.md#data-factory-contributor) a livello di sottoscrizione/gruppo di risorse.
   >
   >
7. Al termine della creazione viene visualizzato il pannello **Data Factory** , come illustrato nell'immagine seguente:

   ![Home page di Data Factory](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDataFactoryHomePage.png)

## <a name="create-gateway"></a>Creare il gateway
1. Nel pannello **Data Factory** fare clic sul riquadro **Creare e distribuire** per avviare l'**Editor** per la data factory.

    ![Riquadro Creare e distribuire](./media/data-factory-move-data-between-onprem-and-cloud/author-deploy-tile.png)
2. Nell'editor di Data Factory fare clic su **... Altro** sulla barra degli strumenti e quindi fare clic su **Nuovo gateway dati**. In alternativa, è possibile fare clic con il pulsante destro del mouse su **Gateway dati** nella visualizzazione ad albero e fare clic su **Nuovo gateway dati**.

   ![Nuovo gateway di dati nella barra degli strumenti](./media/data-factory-move-data-between-onprem-and-cloud/NewDataGateway.png)
3. Nel pannello **Crea** immettere **adftutorialgateway** per il **nome** e fare clic su **OK**.     

    ![Pannello Crea gateway](./media/data-factory-move-data-between-onprem-and-cloud/OnPremCreateGatewayBlade.png)
4. Nel pannello **Configura** fare clic su **Installa direttamente nel computer**. Con questa azione viene scaricato il pacchetto di installazione per il gateway, che viene installato, configurato e registrato nel computer.  

   > [!NOTE]
   > Usare Internet Explorer o un Web browser compatibile con Microsoft ClickOnce.
   >
   > Se si usa Chrome, accedere al [Chrome Web Store](https://chrome.google.com/webstore/), eseguire una ricerca con la parola chiave "ClickOnce", scegliere una delle estensioni ClickOnce e installarla.
   >
   > Seguire la stessa procedura per Firefox (installazione di un componente aggiuntivo). Fare clic sul pulsante **Apri menu** sulla barra degli strumenti (**tre righe orizzontali** nell'angolo superiore destro), fare clic su **Componenti aggiuntivi**, eseguire una ricerca con la parola chiave "ClickOnce", scegliere un'estensione ClickOnce e installarla.    
   >
   >

    ![Gateway - Pannello Configura](./media/data-factory-move-data-between-onprem-and-cloud/OnPremGatewayConfigureBlade.png)

    Si tratta del metodo più semplice (con un clic) per scaricare, installare, configurare e registrare il gateway in un unico passaggio. È possibile vedere l'applicazione **Gateway di gestione dati di Microsoft Configuration Manager** installata nel computer. È anche possibile trovare l'eseguibile **ConfigManager.exe** nella cartella: **C:\Program Files\Microsoft Data Management Gateway\2.0\Shared**.

    È anche possibile scaricare e installare manualmente il gateway usando i collegamenti nel pannello e registrarlo usando il tasto visualizzato nella casella di testo **NUOVA CHIAVE** .

    Leggere l’articolo [Gateway di gestione dati](data-factory-data-management-gateway.md) per tutti i dettagli sul gateway.

   > [!NOTE]
   > È necessario essere un amministratore nel computer locale per installare e configurare correttamente il gateway di gestione dati. È possibile aggiungere altri utenti al gruppo di Windows locale degli **utenti del gateway di gestione dati** . I membri di questo gruppo possono usare lo strumento Gestione configurazione del gateway di gestione dati per configurare il gateway.
   >
   >
5. Attendere qualche minuto o finché non viene visualizzato il messaggio di notifica seguente:

    ![Installazione del gateway riuscita](./media/data-factory-move-data-between-onprem-and-cloud/gateway-install-success.png)
6. Avviare l'applicazione **Gateway di gestione dati di Configuration Manager** nel computer. Nella finestra **Cerca** digitare **Gateway di gestione dati** per accedere a questa utilità. È anche possibile trovare l'eseguibile **ConfigManager.exe** nella cartella: **C:\Program Files\Microsoft Data Management Gateway\2.0\Shared**

    ![Gestione configurazione di gateway](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDMGConfigurationManager.png)
7. Verificare che venga visualizzato il messaggio `adftutorialgateway is connected to the cloud service`. La barra di stato visualizza **Connesso al servizio cloud** insieme a un **segno di spunta verde**.

    Nella scheda **Home** è anche possibile eseguire queste operazioni:

   * **Registrare** un gateway con una chiave dal portale di Azure usando il pulsante Registra.
   * **Interrompere** il servizio host del gateway di gestione dati in esecuzione nel computer gateway.
   * **Pianificare gli aggiornamenti** in modo che vengano installati in un preciso momento della giornata.
   * Visualizzare la data dell'**ultimo aggiornamento** del gateway.
   * Specificare l'ora in cui è possibile installare un aggiornamento per il gateway.
8. Passare alla scheda **Impostazioni** . Il certificato specificato nella sezione **Certificato** viene usato per crittografare e decrittografare le credenziali per l'archivio dati locale specificato nel portale (facoltativo). Fare clic su **Modifica** per usare il proprio certificato. Per impostazione predefinita, il gateway usa il certificato generato automaticamente dal servizio Data Factory.

    ![Configurazione certificati del gateway](./media/data-factory-move-data-between-onprem-and-cloud/gateway-certificate.png)

    È anche possibile eseguire queste azioni nella scheda **Impostazioni**:

   * Visualizzare o esportare il certificato usato dal gateway.
   * Modificare l'endpoint HTTPS usato dal gateway.    
   * Impostare un proxy HTTP che verrà usato dal gateway.     
9. (facoltativo) Passare alla scheda **Diagnostica**, selezionare l'opzione **Abilita la registrazione dettagliata** se si vuole abilitare la registrazione dettagliata che è possibile usare per risolvere i problemi del gateway. Le informazioni sulla registrazione si trovano nel **Visualizzatore eventi**, nel nodo **Registri applicazioni e servizi** -> **Gateway di gestione dati**.

    ![Scheda Diagnostica](./media/data-factory-move-data-between-onprem-and-cloud/diagnostics-tab.png)

    È inoltre possibile eseguire le azioni seguenti nella scheda **Diagnostica** :

   * Usare la sezione **Connessione di test** su un'origine dati locale con il gateway.
   * Fare clic su **Visualizza registri** per vedere il registro del gateway di gestione dati in una finestra del Visualizzatore eventi.
   * Fare clic su **Invia registri** per caricare un file zip dei registri degli ultimi sette giorni sul sito Microsoft al fine di facilitare la risoluzione dei problemi.
10. Nella scheda **Diagnostica**, nella sezione **Test connessione** selezionare **SqlServer** come tipo di archivio dati, immettere il nome del server di database, il nome del database, specificare il tipo di autenticazione, immettere il nome utente e la password e fare clic su **Test** per verificare se il gateway può connettersi al database.
11. Passare al Web browser e nel **portale di Azure** fare clic su **OK** nel pannello **Configura** e quindi nel pannello **Nuovo gateway dati**.
12. Verrà visualizzato **adftutorialgateway** in **Gateway dati** nella visualizzazione albero a sinistra.  Se si fa clic, viene visualizzato l'oggetto JSON associato.

## <a name="create-linked-services"></a>Creare servizi collegati
In questo passaggio vengono creati due servizi collegati: **AzureStorageLinkedService** e **SqlServerLinkedService**. Il servizio **SqlServerLinkedService** collega un database SQL Server locale, mentre il servizio collegato **AzureStorageLinkedService** collega un'archiviazione BLOB di Azure alla data factory. Più avanti nella procedura dettagliata viene creata una pipeline che copia i dati dal database SQL Server locale all'archiviazione BLOB di Azure.

#### <a name="add-a-linked-service-to-an-on-premises-sql-server-database"></a>Aggiungere un servizio collegato a un database di SQL Server locale
1. Nell'**editor di Data Factory** fare clic su **Nuovo archivio dati** sulla barra degli strumenti e selezionare **SQL Server**.

   ![Nuovo servizio collegato di SQL Server](./media/data-factory-move-data-between-onprem-and-cloud/NewSQLServer.png)
2. Nell'**editor JSON** a destra seguire a questa procedura:

   1. Per **gatewayName** specificare **adftutorialgateway**.    
   2. In **connectionString** seguire a questa procedura:    

      1. Per **servername** immettere il nome del server che ospita il database SQL Server.
      2. Per **databasename** immettere il nome del database.
      3. Fare clic sul pulsante **Crittografa** sulla barra dei comandi per scaricare e avviare l'applicazione Gestione credenziali.

         ![Applicazione Gestione credenziali](./media/data-factory-move-data-between-onprem-and-cloud/credentials-manager-application.png)
      4. Nella finestra di dialogo **Impostazione credenziali** specificare il tipo di autenticazione, il nome utente e la password e fare clic su **OK**. Se la connessione viene stabilita correttamente, le credenziali crittografate vengono archiviate nel file JSON e la finestra di dialogo si chiude.
      5. Chiudere la scheda del browser vuota usata per avviare la finestra di dialogo se non viene chiusa automaticamente e tornare alla scheda con il portale di Azure.

         Nel computer gateway queste credenziali vengono **crittografate** con un certificato di proprietà del servizio Data Factory. Se invece si intende usare il certificato associato al gateway di gestione dati, vedere le informazioni su come [impostare le credenziali in modo sicuro](#set-credentials-and-security).    
   3. Fare clic su **Distribuisci** nella barra dei comandi per distribuire il servizio collegato di SQL Server. Verrà visualizzato il servizio collegato nella visualizzazione albero.

      ![Servizio collegato SQL Server nella visualizzazione albero](./media/data-factory-move-data-between-onprem-and-cloud/sql-linked-service-in-tree-view.png)    

#### <a name="add-a-linked-service-for-an-azure-storage-account"></a>Aggiungere un servizio collegato per un account di archiviazione di Azure
1. Nell'**editor di Data factory** fare clic su **Nuovo archivio dati** nella barra dei comandi e quindi su **Archiviazione di Azure**.
2. Nel campo **Nome account**immettere il nome dell'account di archiviazione di Azure.
3. Nel campo **Chiave account**immettere la chiave per l'account di archiviazione di Azure.
4. Fare clic su **Distribuisci** per distribuire **AzureStorageLinkedService**.

## <a name="create-datasets"></a>Creare i set di dati
In questo passaggio vengono creati i set di dati di input e di output che rappresentano i dati di input e di output per l'operazione di copia (database SQL Server locale => archiviazione BLOB di Azure). Prima di creare i set di dati, eseguire questa procedura (la procedura dettagliata segue l'elenco):

* Creare una tabella denominata **emp** nel database SQL Server aggiunto come servizio collegato all'istanza di Data factory e inserire una coppia di voci di esempio nella tabella.
* Creare un contenitore BLOB denominato **adftutorial** nell'account di archiviazione BLOB di Azure aggiunto come servizio collegato alla data factory.

### <a name="prepare-on-premises-sql-server-for-the-tutorial"></a>Preparare SQL Server locale per l'esercitazione
1. Nel database specificato per il servizio collegato di SQL Server locale (**SqlServerLinkedService**) usare lo script SQL seguente per creare la tabella **emp** nel database.

    ```SQL   
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50),
        CONSTRAINT PK_emp PRIMARY KEY (ID)
    )
    GO
    ```
2. Inserire un esempio nella tabella:

    ```SQL
    INSERT INTO emp VALUES ('John', 'Doe')
    INSERT INTO emp VALUES ('Jane', 'Doe')
    ```

### <a name="create-input-dataset"></a>Creare set di dati di input

1. Nell'**editor di Data Factory** fare clic su **... Altro**, fare clic su **Nuovo set di dati** sulla barra dei comandi e quindi su **Tabella SQL Server**.
2. Sostituire lo script JSON nel riquadro a destra con il testo seguente:

    ```JSON   
    {        
        "name": "EmpOnPremSQLTable",
        "properties": {
            "type": "SqlServerTable",
            "linkedServiceName": "SqlServerLinkedService",
            "typeProperties": {
                "tableName": "emp"
            },
            "external": true,
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "externalData": {
                    "retryInterval": "00:01:00",
                    "retryTimeout": "00:10:00",
                    "maximumRetry": 3
                }
            }
        }
    }     
    ```     
   Tenere presente quanto segue:

   * L'oggetto **type** è impostato su **SqlServerTable**.
   * L'oggetto **tableName** è impostato su **emp**.
   * **linkedServiceName** è impostato su **SqlServerLinkedService**. Questo servizio collegato è stato creato prima nel corso di questa procedura dettagliata.
   * Per un set di dati di input non generato da un'altra pipeline in Azure Data Factory, è necessario impostare **external** su **true**. Indica che i dati di input sono generati al di fuori del servizio Azure Data Factory. È possibile specificare i criteri di dati esterni necessari usando l'elemento **externalData** nella sezione **Policy**.    

   Per informazioni dettagliate sulle proprietà JSON, vedere [Spostare dati da/verso SQL Server](data-factory-sqlserver-connector.md).
3. Fare clic su **Distribuisci** sulla barra dei comandi per distribuire il set di dati.  

### <a name="create-output-dataset"></a>Creare il set di dati di output

1. Nell'**Editor di Data factory** fare clic su **Nuovo set di dati** sulla barra dei comandi e selezionare **Archiviazione BLOB di Azure**.
2. Sostituire lo script JSON nel riquadro a destra con il testo seguente:

    ```JSON   
    {
        "name": "OutputBlobTable",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "adftutorial/outfromonpremdf",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
     }
    ```   
   Tenere presente quanto segue:

   * L'oggetto **type** è impostato su **AzureBlob**.
   * **linkedServiceName** è impostato su **AzureStorageLinkedService**. Questo servizio collegato è stato creato al passaggio 2.
   * **folderPath** è impostato su **adftutorial/outfromonpremdf** dove outfromonpremdf è la cartella nel contenitore adftutorial. Se non esiste ancora, creare il contenitore **adftutorial** .
   * L'oggetto **availability** è impostato su **hourly**. L'oggetto **frequency** è impostato su **hour** e l'oggetto **interval** è impostato su **1**.  Il servizio Data Factory genera una sezione di dati di output ogni ora nella tabella **emp** nel database SQL di Azure.

   Se non è stato specificato **fileName** per una **tabella di output**, i file generati in **folderPath** vengono denominati con il seguente formato: Data.<Guid>.txt (ad esempio: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

   Per impostare **folderPath** e **fileName** dinamicamente in base all'ora **SliceStart**, usare la proprietà partitionedBy. Nell'esempio seguente folderPath usa Year, Month e Day dall'oggetto SliceStart (ora di inizio della sezione elaborata), mentre fileName usa Hour dall'oggetto SliceStart. Ad esempio, se una sezione viene generata per 2014-10-20T08:00:00, folderName è impostato su wikidatagateway/wikisampledataout/2014/10/20 e fileName è impostato su 08.csv.

    ```JSON
    "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy":
    [

        { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
    ],
    ```

    Per informazioni dettagliate sulle proprietà JSON, vedere [Spostare dati da/verso l'archivio BLOB di Azure](data-factory-azure-blob-connector.md).
3. Fare clic su **Distribuisci** sulla barra dei comandi per distribuire il set di dati. Assicurarsi che entrambi i set di dati siano visibili nella visualizzazione albero.  

## <a name="create-pipeline"></a>Creare una pipeline
In questo passaggio viene creata una **pipeline** con un'**attività di copia** che usa **EmpOnPremSQLTable** come input e **OutputBlobTable** come output.

1. Nell'editor di Data Factory fare clic su **... Altro** e quindi su **Nuova pipeline**.
2. Sostituire lo script JSON nel riquadro a destra con il testo seguente:    

    ```JSON   
     {
         "name": "ADFTutorialPipelineOnPrem",
         "properties": {
         "description": "This pipeline has one Copy activity that copies data from an on-prem SQL to Azure blob",
         "activities": [
           {
             "name": "CopyFromSQLtoBlob",
             "description": "Copy data from on-prem SQL server to blob",
             "type": "Copy",
             "inputs": [
               {
                 "name": "EmpOnPremSQLTable"
               }
             ],
             "outputs": [
               {
                 "name": "OutputBlobTable"
               }
             ],
             "typeProperties": {
               "source": {
                 "type": "SqlSource",
                 "sqlReaderQuery": "select * from emp"
               },
               "sink": {
                 "type": "BlobSink"
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
         "start": "2016-07-05T00:00:00Z",
         "end": "2016-07-06T00:00:00Z",
         "isPaused": false
       }
     }
    ```   
   > [!IMPORTANT]
   > Sostituire il valore della proprietà **start** con il giorno corrente e il valore di **end** con il giorno successivo.
   >
   >

   Tenere presente quanto segue:

   * Nella sezione delle attività esiste una sola attività con **type** impostato su **Copy**.
   * **Input** for per l'attività è impostato su **EmpOnPremSQLTable** e **output** per l'attività è impostato su **OutputBlobTable**.
   * Nella sezione **typeProperties** vengono specificati **SqlSource** come **tipo di origine** e **BlobSink** come **tipo di sink**.
   * La query SQL `select * from emp` è specificata per la proprietà **sqlReaderQuery** di **SqlSource**.

   Per la data e l'ora di inizio è necessario usare il [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). ad esempio 2014-10-14T16:32:41Z. Il valore di **end** è facoltativo, ma in questa esercitazione viene usato.

   Se non si specifica alcun valore per la proprietà **end**, il valore verrà calcolato come "**start + 48 hours**". Per eseguire la pipeline illimitatamente, specificare **9/9/9999** come valore per la proprietà **end**.

   Si definisce la durata dell'elaborazione delle sezioni di dati in base alle proprietà di **disponibilità** definite per ogni set di dati di Azure Data Factory.

   Nell'esempio sono visualizzate 24 sezioni di dati perché viene generata una sezione di dati ogni ora.        
3. Fare clic su **Distribuisci** sulla barra dei comandi per distribuire la set di dati (la tabella è un set di dati rettangolare). Verificare che la pipeline venga visualizzata nella visualizzazione albero sotto il nodo **Pipeline**.  
4. Ora fare clic su **X** due volte per chiudere i pannelli e tornare al pannello **Data Factory** per **ADFTutorialOnPremDF**.

**Congratulazioni.** Una data factory di Azure, i servizi collegati, i set di dati e una pipeline sono stati creati correttamente e la pipeline è stata pianificata.

#### <a name="view-the-data-factory-in-a-diagram-view"></a>Visualizzare la data factory in una vista diagramma
1. Nel **portale di Azure** fare clic sul riquadro **Diagramma** nella home page per l'istanza della data factory **ADFTutorialOnPremDF**. :

    ![Collegamento al diagramma](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramLink.png)
2. Verrà visualizzato un diagramma simile all'immagine seguente:

    ![Vista Diagramma](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramView.png)

    È possibile eseguire lo zoom avanti, lo zoom indietro e lo zoom al 100%, adattare alla finestra, posizionare automaticamente pipeline e set di dati e visualizzare le informazioni sulla derivazione, evidenziando gli elementi upstream e downstream degli elementi selezionati.  È possibile fare doppio clic su un oggetto (set di dati di input/output o pipeline) per visualizzare le relative proprietà.

## <a name="monitor-pipeline"></a>Monitorare la pipeline
In questo passaggio viene usato il portale di Azure per monitorare le attività in un'istanza di Azure Data Factory. È anche possibile usare i cmdlet di PowerShell per monitorare i set di dati e le pipeline. Per altre informazioni sul monitoraggio, vedere [Monitorare e gestire le pipeline](data-factory-monitor-manage-pipelines.md).

1. Nel diagramma fare doppio clic su **EmpOnPremSQLTable**.  

    ![Sezioni EmpOnPremSQLTable](./media/data-factory-move-data-between-onprem-and-cloud/OnPremSQLTableSlicesBlade.png)
2. Si noti che tutte le sezioni di dati aggiornate hanno lo stato **Pronta** perché la durata della pipeline (dall'ora di inizio all'ora di fine) è nel passato, ma anche perché i dati sono stati inseriti nel database di SQL Server dove sono rimasti. Verificare che non sia visualizzata alcuna sezione in **Sezioni con errori** nella parte inferiore della pagina. Per visualizzare tutte le sezioni, fare clic su **Vedi altre** nella parte inferiore dell'elenco di sezioni.
3. A questo punto, nel pannello **Set di dati**, fare clic su **OutputBlobTable**.

    ![Sezioni OputputBlobTable](./media/data-factory-move-data-between-onprem-and-cloud/OutputBlobTableSlicesBlade.png)
4. Fare clic su una qualsiasi sezione dati dell'elenco per visualizzare il pannello **Sezione dati**. Verranno visualizzate le esecuzioni di attività per la sezione. In genere viene visualizzata una sola esecuzione di attività.  

    ![Pannello Sezione dati](./media/data-factory-move-data-between-onprem-and-cloud/DataSlice.png)

    Se lo stato della sezione non è **Pronto**, è possibile visualizzare le sezioni upstream che non sono pronte e bloccano l'esecuzione della sezione corrente nell'elenco **Sezioni upstream non pronte**.
5. Fare clic sull'**esecuzione attività** dall'elenco nella parte inferiore della pagina per visualizzare i **dettagli dell'esecuzione attività**.

   ![Pannello Dettagli esecuzione attività](./media/data-factory-move-data-between-onprem-and-cloud/ActivityRunDetailsBlade.png)

   Verranno visualizzate informazioni come la velocità effettiva, la durata e il gateway usato per trasferire i dati.
6. Fare clic su **X** per chiudere tutti i pannelli fino
7. a tornare al pannello iniziale di **ADFTutorialOnPremDF**.
8. (Facoltativo) Fare clic su **Pipeline** e su **ADFTutorialOnPremDF**, quindi eseguire il drill-through delle tabelle di input (**utilizzate**) o dei set di dati di output (**generati**).
9. Usare uno strumento come [Microsoft Storage Explorer](http://storageexplorer.com/) per verificare che venga creato un BLOB/file ogni ora.

   ![Esplora archivi Azure](./media/data-factory-move-data-between-onprem-and-cloud/OnPremAzureStorageExplorer.png)

## <a name="next-steps"></a>Passaggi successivi
* Leggere l’articolo [Gateway di gestione dati](data-factory-data-management-gateway.md) per tutti i dettagli sul gateway di gestione dati.
* Per informazioni su come usare l'attività di copia per spostare i dati da un archivio dati di origine a un archivio dati sink, vedere l'articolo [Copiare dati dal BLOB di Azure in SQL Azure](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) .

