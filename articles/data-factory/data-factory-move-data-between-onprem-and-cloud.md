<properties 
    pageTitle="Spostare dati con Gateway di gestione dati | Microsoft Azure"
    description="Configurare un gateway dati per spostare dati tra origini locali e il cloud. Usare Gateway di gestione dati in Azure Data Factory per spostare dati." 
    keywords="gateway dati, integrazione di dati, spostare dati, credenziali del gateway"
    services="data-factory" 
    documentationCenter="" 
    authors="linda33wj" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/12/2016" 
    ms.author="jingwang"/>


# <a name="move-data-between-on-premises-sources-and-the-cloud-with-data-management-gateway"></a>Spostare dati tra origini locali e il cloud con Gateway di gestione dati
Questo articolo offre una panoramica sull'integrazione tra archivi dati locali e archivi dati cloud con Data Factory. Si basa sull'articolo [Attività di spostamento dei dati](data-factory-data-movement-activities.md) e su altri articoli che illustrano i concetti di base relativi a Data Factory: [set di dati](data-factory-create-datasets.md) e [pipeline](data-factory-create-pipelines.md). 

## <a name="data-management-gateway"></a>Gateway di gestione dati
È necessario installare il Gateway di gestione di dati sul computer locale per abilitare lo spostamento dei dati a/da un archivio dati locale. Il gateway può essere installato sullo stesso computer dell'archivio dati o su un computer diverso purché il gateway possa connettersi all'archivio dati. Leggere l’articolo [Gateway di gestione dati](data-factory-data-management-gateway.md) per tutti i dettagli sul Gateway di gestione dati.   

Questa procedura dettagliata illustra come creare un'istanza di Data Factory con una pipeline che sposta i dati da un database di SQL Server locale a un BLOB di Azure. Come parte della procedura dettagliata, viene installato e configurato il gateway di gestione dati nel computer. 

## <a name="walkthrough:-copy-on-prem-data-to-cloud"></a>Procedura dettagliata: copiare i dati locali nel cloud
  
## <a name="create-data-factory"></a>Creare un'istanza di Data Factory
In questo passaggio si usa il portale di Azure per creare un'istanza di Azure Data Factory denominata **ADFTutorialOnPremDF**. È anche possibile creare un'istanza di Data Factory con i cmdlet di Azure Data Factory. 

1.  Dopo l'accesso al [portale di Azure](https://portal.azure.com), fare clic su **NUOVO** nell'angolo inferiore sinistro, selezionare **Analisi dei dati** nel pannello **Crea** e quindi fare clic su **Data Factory** nel pannello **Analisi dei dati**.

    ![Nuovo->DataFactory](./media/data-factory-move-data-between-onprem-and-cloud/NewDataFactoryMenu.png) 
  
6. Nel pannello **Nuova data factory** :
    1. Immettere **ADFTutorialOnPremDF** for the **Nome**.
    2. Fare clic su **NOME GRUPPO DI RISORSE** e selezionare **ADFTutorialResourceGroup**. È possibile selezionare un gruppo di risorse esistente o crearne uno. Per creare un gruppo di risorse:
        1. Fare clic su **Crea un nuovo gruppo di risorse**.
        2. Nel pannello **Crea gruppo di risorse**, immettere un **nome** per il gruppo di risorse, quindi fare clic su **OK**.

7. Notare che viene visualizzato un segno di spunta accanto a **Aggiungi a schermata iniziale** nel pannello **Nuova data factory**.

    ![Aggiungi a schermata iniziale](./media/data-factory-move-data-between-onprem-and-cloud/OnPremNewDataFactoryAddToStartboard.png)

8. Nel pannello **Nuova data factory** fare clic su **Crea**.

    È necessario specificare un nome univoco globale per l'istanza di Azure Data Factory. Se viene visualizzato un errore simile a **Nome "ADFTutorialOnPremDF" per la data factory non disponibile**, cambiare il nome della data factory (ad esempio, nomeutenteADFTutorialOnPremDF) e provare di nuovo a crearla. Durante l'esecuzione dei passaggi rimanenti in questa esercitazione usare questo nome anziché ADFTutorialOnPremDF.  

9. Cercare le notifiche generate dal processo di creazione facendo clic sul pulsante **Notifiche** nella barra del titolo, come illustrato nella seguente figura. Fare di nuovo clic per chiudere la finestra delle notifiche. 

    ![Hub NOTIFICHE](./media/data-factory-move-data-between-onprem-and-cloud/OnPremNotificationsHub.png)

11. Al termine della creazione viene visualizzato il pannello **Data Factory** , come illustrato nell'immagine seguente:

    ![Home page di Data Factory](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDataFactoryHomePage.png)

## <a name="create-gateway"></a>Creare il gateway
5. Nel pannello **DATA FACTORY** fare clic sul riquadro **Creare e distribuire** per avviare l'**Editor** per la data factory.

    ![Riquadro Creare e distribuire](./media/data-factory-move-data-between-onprem-and-cloud/author-deploy-tile.png) 
6.  Nell'editor di Data Factory fare clic su **... (puntini di sospensione)** nella barra degli strumenti e quindi su **Nuovo gateway dati**. 

    ![Nuovo gateway di dati nella barra degli strumenti](./media/data-factory-move-data-between-onprem-and-cloud/NewDataGateway.png)
2. Nel pannello **Crea** immettere **adftutorialgateway** per il **nome** e fare clic su **OK**.    

    ![Pannello Crea gateway](./media/data-factory-move-data-between-onprem-and-cloud/OnPremCreateGatewayBlade.png)

3. Nel pannello **Configura** fare clic su **Installa direttamente nel computer**. Con questa azione viene scaricato il pacchetto di installazione per il gateway, che viene installato, configurato e registrato nel computer.  

    > [AZURE.NOTE] 
    > Usare Internet Explorer o un Web browser compatibile con Microsoft ClickOnce.
    > 
    > Se si usa Chrome, accedere al [Chrome Web Store](https://chrome.google.com/webstore/), eseguire una ricerca con la parola chiave "ClickOnce", scegliere una delle estensioni ClickOnce e installarla. 
    >  
    > Seguire la stessa procedura per Firefox (installazione di un componente aggiuntivo). Fare clic sul pulsante **Apri menu** sulla barra degli strumenti (**tre righe orizzontali** nell'angolo superiore destro), fare clic su **Componenti aggiuntivi**, eseguire una ricerca con la parola chiave "ClickOnce", scegliere un'estensione ClickOnce e installarla.    

    ![Gateway - Pannello Configura](./media/data-factory-move-data-between-onprem-and-cloud/OnPremGatewayConfigureBlade.png)

    Si tratta del metodo più semplice (con un clic) per scaricare, installare, configurare e registrare il gateway in un unico passaggio. È possibile vedere l'applicazione **Gateway di gestione dati di Microsoft Configuration Manager** installata nel computer. È anche possibile trovare l'eseguibile **ConfigManager.exe** nella cartella: **C:\Program Files\Microsoft Data Management Gateway\2.0\Shared**.

    È anche possibile scaricare e installare manualmente il gateway usando i collegamenti nel pannello e registrarlo usando il tasto visualizzato nella casella di testo **NUOVA CHIAVE** .
    
    Leggere l’articolo [Gateway di gestione dati](data-factory-data-management-gateway.md) per tutti i dettagli sul gateway.

    >[AZURE.NOTE] È necessario essere un amministratore nel computer locale per installare e configurare correttamente il gateway di gestione dati. È possibile aggiungere altri utenti al gruppo di Windows locale degli utenti del gateway di gestione dati. I membri di questo gruppo possono usare lo strumento Gestione configurazione del gateway di gestione dati per configurare il gateway. 

5. Attendere un paio di minuti e quindi avviare l'applicazione **Gestione configurazione di Gateway di gestione dati** nel computer. Nella finestra **Cerca** digitare **Gateway di gestione dati** per accedere a questa utilità. È anche possibile trovare l'eseguibile **ConfigManager.exe** nella cartella: **C:\Program Files\Microsoft Data Management Gateway\2.0\Shared** 

    ![Gestione configurazione di gateway](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDMGConfigurationManager.png)

6. Attendere fino a quando:
    2. **Nome gateway** è impostato su **adftutorialgateway**.
    4. La barra di stato visualizza **Connesso al servizio cloud** insieme a un **segno di spunta verde**.

    Nella scheda **Home** è possibile anche eseguire le operazioni seguenti:      - **registrare** un gateway con una chiave dal portale di Azure con il pulsante di registrazione. 
        - **Interrompere** il servizio host del Gateway di gestione dati in esecuzione sul computer del gateway. 
        - **Pianificare gli aggiornamenti** in modo che vengano installati in un preciso momento della giornata. 
        - Visualizzare la data dell' **ultimo aggiornamento**del gateway. 

8. Passare alla scheda **Impostazioni** . Il certificato specificato nella sezione **Certificato** viene usato per crittografare e decrittografare le credenziali per l'archivio dati locale specificato nel portale (facoltativo). Fare clic su **Modifica** per usare il proprio certificato. Per impostazione predefinita, il gateway usa il certificato generato automaticamente dal servizio Data Factory.

    ![Configurazione certificati del gateway](./media/data-factory-move-data-between-onprem-and-cloud/gateway-certificate.png)

    È inoltre possibile eseguire le azioni seguenti nella scheda Impostazioni: - Verificare o esportare il certificato usato dal gateway.
        - Modificare l'endpoint HTTPS usato dal gateway - 
9. (facoltativo) Passare alla scheda **Diagnostica**, selezionare l'opzione **Abilita la registrazione dettagliata** se si vuole abilitare la registrazione dettagliata che è possibile usare per risolvere i problemi del gateway. Le informazioni sulla registrazione si trovano nel **Visualizzatore eventi**, nel nodo **Registri applicazioni e servizi** -> **Gateway di gestione dati**. 

    ![Scheda Diagnostica](./media/data-factory-move-data-between-onprem-and-cloud/diagnostics-tab.png)

    È inoltre possibile eseguire le azioni seguenti nella scheda **Diagnostica** : 
    
    - Usare la sezione **Connessione di test** su un'origine dati locale con il gateway.
    - Fare clic su **Visualizza registri** per vedere il registro del gateway di gestione dati in una finestra del Visualizzatore eventi. 
    - Fare clic su **Invia registri** per caricare un file zip dei registri degli ultimi sette giorni sul sito Microsoft al fine di facilitare la risoluzione dei problemi. 
10. Nel portale di Azure fare clic su **OK** nel pannello **Configura** e quindi nel pannello **Nuovo gateway dati**.
6. Verrà visualizzato **adftutorialgateway** in **Gateway dati** nella visualizzazione albero a sinistra.  Se si fa clic, viene visualizzato l'oggetto JSON associato. 
    

## <a name="create-linked-services"></a>Creare servizi collegati 
In questo passaggio vengono creati due servizi collegati: **AzureStorageLinkedService** e **SqlServerLinkedService**. Il servizio **SqlServerLinkedService** collega un database SQL Server locale, mentre il servizio collegato **AzureStorageLinkedService** collega un'archiviazione BLOB di Azure alla data factory. Più avanti nella procedura dettagliata viene creata una pipeline che copia i dati dal database SQL Server locale all'archiviazione BLOB di Azure. 

#### <a name="add-a-linked-service-to-an-on-premises-sql-server-database"></a>Aggiungere un servizio collegato a un database di SQL Server locale
1.  Nell'**editor di Data Factory** fare clic su **Nuovo archivio dati** sulla barra degli strumenti e selezionare **SQL Server**. 

    ![Nuovo servizio collegato di SQL Server](./media/data-factory-move-data-between-onprem-and-cloud/NewSQLServer.png) 
3.  Nell' **editor JSON**attenersi a questa procedura: 
    1. Per **gatewayName** specificare **adftutorialgateway**. 
    2. Se si usa l'autenticazione di Windows:
        1. In **connectionString**, impostare **Integrated Security** su **true**, specificare il **nome server** del database e il **nome del database**, quindi rimuovere **ID utente** e **password**. 
        3. Specificare il nome utente e la password per le proprietà **userName** e **password**.  
        
                "typeProperties": {
                    "connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;",
                    "gatewayName": "adftutorialgateway",
                    "userName": "<Specify user name if you are using Windows Authentication. Example: <domain>\\<user>",
                    "password": "<Specify password for the user account>"
                }

    4. Se si usa l'autenticazione SQL:
        1. Per il database specificare il **nome del server**, il **nome del database**, l'**ID utente** e la **password** in **connectionString**.       
        2. Rimuovere le ultime due proprietà JSON, **userName** e **password**, da JSON.
        3. Rimuovere il carattere finale `,` (virgola)** alla fine della riga che specifica il valore della proprietà **gatewayName**. 

                "typeProperties": {
                    "connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
                    "gatewayName": "<Name of the gateway that the Data Factory service should use to connect to the on-premises SQL Server database>"
                }
    
        Le credenziali sono **crittografate** con un certificato di proprietà del servizio Data Factory. Se invece si intende usare il certificato associato al gateway di gestione dati, vedere le informazioni su come [impostare le credenziali in modo sicuro](#set-credentials-and-security). 
    
2.  Fare clic su **Distribuisci** nella barra dei comandi per distribuire il servizio collegato di SQL Server. 

#### <a name="add-a-linked-service-for-an-azure-storage-account"></a>Aggiungere un servizio collegato per un account di archiviazione di Azure
 
1. Nell'**editor di Data factory** fare clic su **Nuovo archivio dati** nella barra dei comandi e quindi su **Archiviazione di Azure**.
2. Nel campo **Nome account**immettere il nome dell'account di archiviazione di Azure.
3. Nel campo **Chiave account**immettere la chiave per l'account di archiviazione di Azure.
4. Fare clic su **Distribuisci** per distribuire **AzureStorageLinkedService**.
   
 
## <a name="create-datasets"></a>Creare i set di dati
In questo passaggio vengono creati i set di dati di input e di output che rappresentano i dati di input e di output per l'operazione di copia (database SQL Server locale => archiviazione BLOB di Azure). Prima di creare i set di dati o le tabelle (set di dati rettangolari), è necessario attenersi alla seguente procedura (i passaggi vengono descritti in modo dettagliato dopo l'elenco):

- Creare una tabella denominata **emp** nel database SQL Server aggiunto come servizio collegato all'istanza di Data factory e inserire una coppia di voci di esempio nella tabella.
- Creare un contenitore BLOB denominato **adftutorial** nell'account di archiviazione BLOB di Azure aggiunto come servizio collegato alla data factory.

### <a name="prepare-on-premises-sql-server-for-the-tutorial"></a>Preparare SQL Server locale per l'esercitazione

1. Nel database specificato per il servizio collegato di SQL Server locale (**SqlServerLinkedService**) usare lo script SQL seguente per creare la tabella **emp** nel database.


        CREATE TABLE dbo.emp
        (
            ID int IDENTITY(1,1) NOT NULL, 
            FirstName varchar(50),
            LastName varchar(50),
            CONSTRAINT PK_emp PRIMARY KEY (ID)
        )
        GO
 

2. Inserire un esempio nella tabella: 


        INSERT INTO emp VALUES ('John', 'Doe')
        INSERT INTO emp VALUES ('Jane', 'Doe')



### <a name="create-input-table"></a>Creare la tabella di input

1. Nell'**editor di Data Factory** fare clic su **Nuovo set di dati** nella barra dei comandi e quindi su **Tabella SQL Server**. 
2.  Sostituire lo script JSON nel riquadro a destra con il testo seguente:    

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

    Tenere presente quanto segue: 
    
    - L'oggetto **type** è impostato su **SqlServerTable**.
    - L'oggetto **tableName** è impostato su **emp**.
    - **linkedServiceName** è impostato su **SqlServerLinkedService**; questo servizio collegato è stato creato nel passaggio 2.
    - Per una tabella di input non generata da un'altra pipeline in Azure Data Factory, è necessario impostare **external** su **true**. Indica che i dati di input sono generati al di fuori del servizio Azure Data Factory. È possibile specificare i criteri di dati esterni necessari usando l'elemento **externalData** nella sezione **Policy**.    

    Vedere il [riferimento agli script JSON][json-script-reference] per dettagli sulle proprietà JSON.

2. Fare clic su **Distribuisci** sulla barra dei comandi per distribuire la set di dati (la tabella è un set di dati rettangolare). Controllare che sulla barra del titolo sia visualizzato un messaggio analogo a **DISTRIBUZIONE TABELLA COMPLETATA**. 


### <a name="create-output-table"></a>Creazione della tabella di output

1.  Nell'**Editor di Data factory** fare clic su **Nuovo set di dati** sulla barra dei comandi e selezionare **Archiviazione BLOB di Azure**.
2.  Sostituire lo script JSON nel riquadro a destra con il testo seguente: 

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
  
    Tenere presente quanto segue: 
    
    - L'oggetto **type** è impostato su **AzureBlob**.
    - **linkedServiceName** è impostato su **AzureStorageLinkedService**. Questo servizio collegato è stato creato al passaggio 2.
    - **folderPath** è impostato su **adftutorial/outfromonpremdf** dove outfromonpremdf è la cartella nel contenitore adftutorial. Se non esiste ancora, creare il contenitore **adftutorial** . 
    - L'oggetto **availability** è impostato su **hourly**. L'oggetto **frequency** è impostato su **hour** e l'oggetto **interval** è impostato su **1**.  Il servizio Data Factory genera una sezione di dati di output ogni ora nella tabella **emp** nel database SQL di Azure. 

    Se non si specifica un oggetto **fileName** per una **tabella di input**, tutti i file e i BLOB della cartella di input (**folderPath**) vengono considerati input. Se si specifica un oggetto fileName nel JSON, solo il file/BLOB specificato viene considerato un input. Vedere i file di esempio nell'[esercitazione][adf-tutorial] per gli esempi.
 
    Se non è stato specificato **fileName** per una **tabella di output**, i file generati in **folderPath** vengono denominati con il seguente formato: Data.<Guid>.txt (ad esempio: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

    Per impostare **folderPath** e **fileName** dinamicamente in base all'ora **SliceStart**, usare la proprietà partitionedBy. Nell'esempio seguente folderPath usa Year, Month e Day dall'oggetto SliceStart (ora di inizio della sezione elaborata), mentre fileName usa Hour dall'oggetto SliceStart. Ad esempio, se una sezione viene generata per 2014-10-20T08:00:00, folderName è impostato su wikidatagateway/wikisampledataout/2014/10/20 e fileName è impostato su 08.csv. 

        "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
        "fileName": "{Hour}.csv",
        "partitionedBy": 
        [
            { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
            { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
            { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
            { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
        ],

 

    Vedere il [riferimento agli script JSON][json-script-reference] per dettagli sulle proprietà JSON.

2.  Fare clic su **Distribuisci** sulla barra dei comandi per distribuire la set di dati (la tabella è un set di dati rettangolare). Controllare che sulla barra del titolo sia visualizzato un messaggio analogo a **DISTRIBUZIONE TABELLA COMPLETATA**.
  

## <a name="create-pipeline"></a>Creare una pipeline
In questo passaggio viene creata una **pipeline** con un'**attività di copia** che usa **EmpOnPremSQLTable** come input e **OutputBlobTable** come output.

1.  Nel pannello **DATA FACTORY** fare clic sul riquadro **Creare e distribuire** per avviare l'**Editor** per la data factory.

    ![Riquadro Creare e distribuire](./media/data-factory-move-data-between-onprem-and-cloud/author-deploy-tile.png) 
2.  Fare clic su **Nuova pipeline** sulla barra dei comandi. Se il pulsante non è presente, fare clic su **...(puntini di sospensione)** per visualizzarlo.
2.  Sostituire lo script JSON nel riquadro a destra con il testo seguente: 
    
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

    Tenere presente quanto segue:
 
    - Nella sezione delle attività esiste una sola attività con **type** impostato su **Copy**.
    - **Input** for per l'attività è impostato su **EmpOnPremSQLTable** e **output** per l'attività è impostato su **OutputBlobTable**.
    - Nella sezione **transformation** sono specificati **SqlSource** come **tipo di origine** e **BlobSink** come **tipo di sink**.
    - La query SQL `select * from emp` è specificata per la proprietà **sqlReaderQuery** di **SqlSource**.

    Sostituire il valore della proprietà **start** con il giorno corrente e il valore di **end** con il giorno successivo. Per la data e l'ora di inizio è necessario usare il [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). ad esempio 2014-10-14T16:32:41Z. Il valore di **end** è facoltativo, ma in questa esercitazione viene usato. 
    
    Se non si specifica alcun valore per la proprietà **end**, il valore verrà calcolato come "**start + 48 hours**". Per eseguire la pipeline illimitatamente, specificare **9/9/9999** come valore per la proprietà **end**. 
    
    Si definisce la durata dell'elaborazione delle sezioni di dati in base alle proprietà di **disponibilità** definite per ogni tabella di Azure Data Factory.
    
    Nell'esempio sono visualizzate 24 sezioni di dati perché viene generata una sezione di dati ogni ora.  

    
2. Fare clic su **Distribuisci** sulla barra dei comandi per distribuire la set di dati (la tabella è un set di dati rettangolare). Controllare che sulla barra del titolo sia visualizzato un messaggio analogo a **DISTRIBUZIONE PIPELINE COMPLETATA**.  
5. Chiudere quindi il pannello dell'**editor** facendo clic su **X**. Fare di nuovo clic su **X** per chiudere il pannello ADFTutorialDataFactory con la barra degli strumenti e la visualizzazione ad albero. Se viene visualizzato un messaggio analogo a **Eventuali modifiche non salvate verranno rimosse**, fare clic su **OK**.
6. Dovrebbe essere visualizzato di nuovo il pannello **DATA FACTORY** per **ADFTutorialOnPremDF**.

**Congratulazioni.**  Azure Data Factory, i servizi collegati, le tabelle e una pipeline sono stati creati correttamente e la pipeline è stata pianificata.

#### <a name="view-the-data-factory-in-a-diagram-view"></a>Visualizzare la data factory in una vista diagramma 
1. Nel **portale di Azure** fare clic sul riquadro **Diagramma** nella home page per l'istanza della data factory **ADFTutorialOnPremDF**. :

    ![Collegamento al diagramma](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramLink.png)

2. Verrà visualizzato un diagramma simile all'immagine seguente:

    ![Vista Diagramma](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramView.png)

    È possibile eseguire lo zoom avanti, lo zoom indietro e lo zoom al 100%, adattare alla finestra, posizionare automaticamente pipeline e tabelle e visualizzare le informazioni sulla derivazione, evidenziando gli elementi upstream e downstream degli elementi selezionati.  È possibile fare doppio clic su un oggetto (tabella di input/output o pipeline) per visualizzare le relative proprietà. 

## <a name="monitor-pipeline"></a>Monitorare la pipeline
In questo passaggio viene usato il portale di Azure per monitorare le attività in un'istanza di Azure Data Factory. È anche possibile usare i cmdlet di PowerShell per monitorare i set di dati e le pipeline. Per altre informazioni sul monitoraggio, vedere [Monitorare e gestire le pipeline](data-factory-monitor-manage-pipelines.md).

1. Passare al **portale di Azure** (se è stato chiuso)
2. Se il pannello per **ADFTutorialOnPremDF** è chiuso, aprirlo facendo clic su **ADFTutorialOnPremDF** nella **Schermata iniziale**.
3. Viene visualizzato il **numero** e i **nomi** delle tabelle e delle pipeline create nel pannello.

    ![Home page di Data Factory](./media/data-factory-move-data-between-onprem-and-cloud/OnPremDiagramView.png)
4. A questo punto, fare clic sul riquadro **Set di dati** .
5. Nel pannello **Set di dati**, fare clic su **EmpOnPremSQLTable**.

    ![Sezioni EmpOnPremSQLTable](./media/data-factory-move-data-between-onprem-and-cloud/OnPremSQLTableSlicesBlade.png)

6. Le sezioni di dati fino all'ora corrente sono state già generate e sono in stato **Pronto**. Ciò è dovuto al fatto che i dati sono stati inseriti nel database SQL Server database che è sempre presente. Verificare che non sia visualizzata alcuna sezione in **Sezioni con errori** nella parte inferiore della pagina.
    
    Gli elenchi **Sezioni aggiornate di recente** e **Sezioni non riuscite di recente** sono ordinati in base a **ORA ULTIMO AGGIORNAMENTO**. L'ora di aggiornamento di una sezione viene modificata nelle situazioni seguenti.

    Lo stato della sezione viene aggiornato manualmente, ad esempio usando **Set-AzureRmDataFactorySliceStatus** oppure facendo clic su **ESEGUI** nel pannello **SEZIONE** della sezione.
    
    Lo stato della sezione cambia a causa di un'esecuzione, ad esempio un'esecuzione avviata, un'esecuzione terminata con errore, un'esecuzione terminata correttamente e così via.

    Fare clic sul titolo degli elenchi oppure su **... (puntini di sospensione)** per visualizzare un elenco di sezioni più ampio. Fare clic su **Filtro** sulla barra degli strumenti per filtrare le sezioni.

    Per visualizzare invece le sezioni di dati ordinate in base alle ore di inizio/fine, fare clic sul riquadro **Sezioni dati (in base all'ora della sezione)** .
7. A questo punto, nel pannello **Set di dati**, fare clic su **OutputBlobTable**.

    ![Sezioni OputputBlobTable](./media/data-factory-move-data-between-onprem-and-cloud/OutputBlobTableSlicesBlade.png)
8. Confermare che le sezioni fino all'ora corrente sono state generate e hanno lo stato **Pronto**. Attendere finché lo stato delle sezioni fino all'ora corrente non viene impostato **Pronto**.
9. Fare clic su una qualsiasi sezione dati dell'elenco per visualizzare il pannello **SEZIONE DATI** .

    ![Pannello Sezione dati](./media/data-factory-move-data-between-onprem-and-cloud/DataSlice.png)

    Se lo stato della sezione non è **Pronto**, è possibile visualizzare le sezioni upstream che non sono pronte e bloccano l'esecuzione della sezione corrente nell'elenco **Sezioni upstream non pronte**.

10. Fare clic sull'**esecuzione attività** dall'elenco nella parte inferiore della pagina per visualizzare i **dettagli dell'esecuzione attività**.

    ![Pannello Dettagli esecuzione attività](./media/data-factory-move-data-between-onprem-and-cloud/ActivityRunDetailsBlade.png)

11. Fare clic su **X** per chiudere tutti i pannelli fino 
12. a tornare al pannello iniziale di **ADFTutorialOnPremDF**.
14. (Facoltativo) Fare clic su **Pipeline** e su **ADFTutorialOnPremDF**, quindi eseguire il drill-through delle tabelle di input (**utilizzate**) o delle tabelle di output (**generate**).
15. Usare strumenti come **Esplora archivi Azure** per verificare l'output.

    ![Esplora archivi Azure](./media/data-factory-move-data-between-onprem-and-cloud/OnPremAzureStorageExplorer.png)

## <a name="next-steps"></a>Passaggi successivi

- Leggere l’articolo [Gateway di gestione dati](data-factory-data-management-gateway.md) per tutti i dettagli sul gateway di gestione dati.
- Per informazioni su come usare l'attività di copia per spostare i dati da un archivio dati di origine a un archivio dati sink, vedere l'articolo [Copiare dati dal BLOB di Azure in SQL Azure](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) . 



<!--HONumber=Oct16_HO2-->


