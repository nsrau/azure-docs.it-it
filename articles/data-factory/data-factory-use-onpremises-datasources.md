<properties 
	pageTitle="Consentire alle pipeline di usare dati locali | Data factory di Azure" 
	description="Informazioni su come registrare un'origine dati locale con un'istanza di Data factory di Azure e su come copiare i dati da e nell'origine dati." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/04/2015" 
	ms.author="spelluru"/>

# Consentire alle pipeline di usare dati locali

Per consentire alle pipeline in Data factory di Azure di usare un'origine dati locale, è necessario aggiungere l'origine dati locale come servizio collegato a un'istanza di Data factory usando il portale di gestione di Azure o Azure PowerShell.
 
Per aggiungere un'origine dati locale come servizio collegato a un'istanza di Data factory, è necessario scaricare e installare prima il Gateway di gestione dati di Microsoft in un computer locale e configurare il servizio collegato per l'origine dati locale per poter usare il gateway.


## <a href="DMG"></a> Gateway di gestione dati

Il **Gateway di gestione dati** è un software che connette le origini dati locali ai servizi cloud in modo sicuro e gestito. Con il Gateway di gestione dati, è possibile

- **Connettersi ai dati locali per l'accesso ai dati ibrido**: è possibile connettere i dati locali ai servizi cloud per sfruttare i vantaggi dei servizi cloud, mantenendo allo stesso tempo attiva la società con i dati locali.
- **Definire un proxy di dati sicuro**: è possibile definire quali origini dati locali esporre con il Gateway di gestione dati in modo che il gateway autentichi la richiesta di dati dai servizi cloud e protegga le origini dati locali.
- **Gestire il gateway per una governance completa**: vengono fornite funzionalità complete di monitoraggio e registrazione di tutte le attività all'interno del Gateway di gestione dati per la gestione e la governance.
- **Spostare i dati in modo efficace**: i dati vengono trasferiti in parallelo e sono resilienti ai problemi di rete intermittente grazie alla logica di ripetizione dei tentativi automatica.


Il Gateway di gestione dati ha una gamma completa di funzionalità di connessione dei dati locali che include:

- **Non invasività per il firewall aziendale**: il Gateway di gestione dati funziona solo dopo l'installazione, senza dover aprire una connessione al firewall o richiedere modifiche invasive all'infrastruttura di rete aziendale. 
- **Crittografia delle credenziali con il certificato**: le credenziali usate per connettersi alle origini dati sono crittografate tramite un certificato di totale proprietà dell'utente. Senza il certificato, nessuno può decrittografare le credenziali in testo normale, neanche Microsoft.

### Considerazioni per l'uso del Gateway di gestione dati
1.	Una singola istanza del Gateway di gestione dati può essere usata per più origini dati locali. Tenere presente, però, che un **gateway è associato a un'istanza di Data factory di Azure** e non può essere condiviso con altre istanze di Data factory.
2.	Nel computer può essere installata **una sola istanza del Gateway di gestione dati**. Si supponga di avere due istanze di Data factory che richiedono l'accesso alle origini dati locali: è necessario installare i gateway nei due computer locali in cui ogni gateway sia associato a un'istanza separata di Data factory.
3.	Il **gateway non deve trovarsi nello stesso computer dell'origine dati**; tuttavia, la prossimità all'origine dati riduce il tempo di connessione tra il gateway e l'origine dati. Si consiglia di installare il gateway in un computer diverso da quello che ospita l'origine dati locale in modo che il gateway non si contenda le risorse con l'origine dati.
4.	È possibile disporre di **più gateway su diversi computer che si connettono alla stessa origine dati locale**. Ad esempio, potrebbero essere disponibili due gateway che servono due data factory, ma la stessa origine dati locale viene registrata con entrambe le data factory. 
5.	Se un gateway è già installato nel computer per uno scenario **Power BI**, installare un **gateway separato per Data factory di Azure** in un altro computer.

### Considerazioni su porte e sicurezza 
- Il programma di installazione di Gateway di gestione dati apre le porte **8050** e **8051** nel computer del gateway. Queste porte vengono usate per la **Gestione credenziali** (applicazione ClickOnce), la quale consente di impostare le credenziali per un servizio locale collegato e testare la connessione all'origine dati. Queste porte non sono raggiungibili da Internet e non è necessario tenerle aperte nel firewall aziendale.
- Quando si copiano dati da e verso un database SQL Server locale verso o da un database SQL Azure, verificare quanto segue:
 
	- Il firewall nel computer del gateway consente la comunicazione TCP in uscita sulla porta **TCP** **1433**
	- Configurare le [impostazioni del firewall SQL di Azure](https://msdn.microsoft.com/library/azure/jj553530.aspx) aggiungendo l'**indirizzo IP relativo al computer del gateway** agli **indirizzi IP consentiti**.

- Quando si copiano dati a/da SQL Server locale in qualsiasi destinazione e il gateway e i computer SQL Server sono diversi, eseguire le operazioni seguenti: [configurare Windows Firewall](https://msdn.microsoft.com/library/ms175043.aspx) sul computer SQL Server in modo che il gateway possa accedere al database con le porte su cui è in attesa l'istanza di SQL Server. Per l'istanza predefinita, la porta è 1433.

- È necessario avviare l'applicazione **Gestione credenziali** in un computer che possa connettersi al Gateway di gestione dati per poter impostare le credenziali per l'origine dati e testare la connessione all'origine dati.

### Installazione del gateway - Prerequisiti 

1.	Sono supportati i **sistemi operativi** Windows 7, Windows 8/8.1, Windows Server 2008 R2 e Windows Server 2012.
2.	La **configurazione** consigliata per il computer gateway è di almeno 2 GHz, 4 core, 8 GB di RAM e un disco da 80 GB.
3.	Se il computer host entra in stato di ibernazione, il gateway non può rispondere alla richiesta di dati. Pertanto, configurare una **combinazione per il risparmio di energia** appropriata nel computer prima di installare il gateway. L'installazione del gateway invia un messaggio se il computer è configurato per l'ibernazione.  


 

## Procedura dettagliata

In questa procedura dettagliata viene creata un'istanza di Data factory con una pipeline che sposta i dati da un database SQL Server locale a un BLOB di Azure.

## Passaggio 1: creare un'istanza di Data factory di Azure
In questo passaggio viene usato il portale di gestione di Azure per creare un'istanza di Data factory di Azure denominata **ADFTutorialOnPremDF**. È anche possibile creare un'istanza di Data factory con i cmdlet di Data factory di Azure.

1.	Dopo aver eseguito l'accesso al [portale di anteprima di Azure][azure-preview-portal], fare clic su **NUOVO** nell'angolo inferiore sinistro, selezionare **Analisi dei dati** nel pannello **Crea** e infine fare clic su **Data factory** nel pannello **Analisi dei dati**.

	![Nuovo->DataFactory][image-data-factory-new-datafactory-menu]
  
6. Nel pannello **Nuova data factory**:
	1. Immettere **ADFTutorialOnPremDF** nel campo **Nome**.
	2. Fare clic su **NOME GRUPPO DI RISORSE** e selezionare **ADFTutorialResourceGroup**. Tale passaggio è valido solo se l'esercitazione è stata completata da [Introduzione a Data factory di Azure][adfgetstarted]. È possibile selezionare un gruppo di risorse esistente o crearne uno nuovo. Per creare un nuovo gruppo di risorse:
		1. Fare clic su **Crea un nuovo gruppo di risorse**.
		2. Nel pannello **Crea gruppo di risorse**, immettere un **nome** per il gruppo di risorse, quindi fare clic su **OK**.

7. **Aggiungi alla Schermata iniziale** è selezionato nel pannello **Nuova data factory**.

	![Aggiungi a schermata iniziale][image-data-factory-add-to-startboard]

8. Nel pannello **Nuova data factory** fare clic su **Crea**.

	È necessario specificare un nome univoco globale per l'istanza di Data factory di Azure. Se viene visualizzato un errore simile a **Nome "ADFTutorialOnPremDF" per la data factory non disponibile**, cambiare il nome della data factory (ad esempio, nomeutenteADFTutorialOnPremDF) e provare di nuovo a crearla. Durante l'esecuzione dei passaggi rimanenti in questa esercitazione usare questo nome anziché ADFTutorialOnPremDF.

9. Cercare le notifiche dal processo di creazione nell'hub **NOTIFICHE** sulla sinistra. Fare clic sul simbolo **X** per chiudere il pannello **NOTIFICHE**, se aperto.

	![Hub NOTIFICHE][image-data-factory-notifications-hub]

11. Al termine della procedura di creazione, viene visualizzato il pannello di **Data factory** come mostrato di seguito:

	![Home page di Data factory][image-data-factory-datafactory-homepage]

## Passaggio 2: creare un gateway di gestione dati
5.	Nel pannello **Data factory** per **ADFTutorialOnPremDF**, fare clic su **Servizi collegati**. 

	![Home page di Data factory][image-data-factory-home-age]

2.	Nel pannello **Servizi collegati** fare clic su **+ Gateway dati**.

	![Servizi collegati - Pulsante Aggiungi gateway][image-data-factory-linkedservices-add-gateway-button]

2. Nel pannello **Crea**, immettere **adftutorialgateway** per il **nome** e fare clic su **OK**.

	![Pannello Crea gateway][image-data-factory-create-gateway-blade]

3. Nel pannello **Configura** fare clic su **Installa direttamente nel computer**. Viene scaricato il pacchetto di installazione per il gateway, che viene installato, configurato e registrato nel computer.

	> [AZURE.NOTE]Usare Internet Explorer o un Web browser compatibile con Microsoft ClickOnce.

	![Gateway - Pannello Configura][image-data-factory-gateway-configure-blade]

	Si tratta del metodo più semplice (con un clic) di scaricare, installare, configurare e registrare il gateway in un unico passaggio. È possibile vedere l'applicazione **Gateway di gestione dati di Microsoft Configuration Manager** installata nel computer. È anche possibile trovare l'eseguibile **ConfigManager.exe** nella cartella: **C:\Program Files\Microsoft Data Management Gateway\1.0\Shared**.

	È anche possibile scaricare e installare manualmente il gateway usando i collegamenti nel pannello e registrarlo usando il tasto visualizzato nella casella di testo **REGISTRA CON TASTO**.
	
	Vedere la sezione [Gateway di gestione dati](#DMG) per i dettagli sul gateway, incluse le procedure consigliate e altre considerazioni importanti.

	>[AZURE.NOTE]È necessario essere un amministratore nel computer locale per installare e configurare correttamente il gateway di gestione dati. È possibile aggiungere altri utenti al gruppo di Windows locale degli utenti del gateway di gestione dati. I membri di questo gruppo potranno usare lo strumento Gestione configurazione del gateway di gestione dati per configurare il gateway.

4. Fare clic sull'hub **NOTIFICHE** a sinistra. Attendere la visualizzazione del messaggio **L'installazione rapida per "adftutorialgateway'' è stata completata.** nel pannello **Notifiche**.

	![Installazione rapida riuscita][express-setup-succeeded]
5. Fare clic su **OK** nel pannello **Crea** e nel pannello **Nuovo gateway dati**.
6. Chiudere il pannello **Servizi collegati** (premendo il pulsante **X** in alto a destra) e aprire di nuovo il pannello **Servizi collegati** per visualizzare lo stato più recente del gateway. 
7. Verificare che lo **stato** del gateway sia **Online**. 

	![Stato del gateway][gateway-status]
5. Avviare l'applicazione **Gateway di gestione dati di Microsoft Configuration Manager** nel computer.

	![Gestione configurazione di gateway][image-data-factory-gateway-configuration-manager]

6. Attendere finché i valori non sono impostati come segue:
	1. Se lo **stato** del servizio non è impostato su **Avviato**, fare clic su **Avvia servizio** per avviare il servizio e attendere un minuto per consentire l'aggiornamento degli altri campi.
	2. **Nome gateway** è impostato su **adftutorialgateway**.
	3. **Nome istanza** è impostato su **adftutorialgateway**.
	4. **Stato della chiave del gateway** è impostato su **registrato**.
	5. La barra di stato visualizza **Connesso al servizio cloud del Gateway di gestione dati** insieme a un **segno di spunta verde**.
	
7. Nel pannello **Servizi collegati**, confermare che lo **stato** del gateway sia **Riuscito**.
8. Chiudere tutti i pannelli fino a raggiungere la home page **Data factory**. 

## Passaggio 2: creare servizi collegati 
In questo passaggio verranno creati due servizi collegati: **StorageLinkedService** e **SqlServerLinkedService**. Il servizio **SqlServerLinkedService** collega un database SQL Server locale, mentre il servizio collegato **StorageLinkedService** collega un archivio BLOB di Azure ad **ADFTutorialDataFactory**. Più avanti nella procedura dettagliata verrà creata una pipeline che copia i dati dal database SQL Server locale all'archivio BLOB di Azure.

### Aggiungere un servizio collegato a un database SQL Server locale
1.	Nel pannello **DATA FACTORY**, fare clic sul riquadro **Creare e distribuire** per avviare l'**Editor** per la data factory.

	![Riquadro Creare e distribuire][image-author-deploy-tile]

	Per una panoramica dettagliata dell'editor di Data factory, vedere l'argomento [Editor di Data factory di Azure][data-factory-editor].

2.	Nell'**Editor**, fare clic sul pulsante **Nuovo archivio dati** sulla barra degli strumenti e scegliere **Database SQL Server locale** dal menu a discesa.

	![Pulsante Nuovo archivio dati dell'editor][image-editor-newdatastore-onpremsql-button]
    
3.	Nel riquadro a destra verrà visualizzato il modello JSON per la creazione di un servizio collegato di SQL Server locale.![Servizio collegato di SQL locale - impostazioni][image-editor-newdatastore-onpremsql-settings]

4.	Nel riquadro JSON, eseguire le operazioni riportate di seguito:
	1.	Per la proprietà **gatewayName** immettere **adftutorialgateway** in modo da sostituire tutto il testo racchiuso tra virgolette.  
	2.	Se si usa **Autenticazione SQL**: 
		1.	Per la proprietà **connectionString**, sostituire **<servername>**, **<databasename>**, **<username>** e **<password>** con i nomi del server SQL locale, del database, l'account utente e la password.	
		2.	Rimuovere le ultime due proprietà (**username** e **password**) dal file JSON e il carattere della **virgola (,)** al termine dell'ultima riga dello script JSON rimanente.
		
				{
	    			"name": "SqlServerLinkedService",
	    			"properties": {
		        		"type": "OnPremisesSqlLinkedService",
		        		"ConnectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
		        		"gatewayName": "adftutorialgateway"
	    			}
				}
	3.	Se si usa **Autenticazione di Windows**:
		1. Per la proprietà **connectionString**, sostituire **<servername>** e **<databasename>** con i nomi del server SQL locale e del database. Impostare **Sicurezza integrata** su **True**. Rimuovere **ID** e **Password** dalla stringa di connessione.
			
				{
    				"name": "SqlServerLinkedService",
    				"properties": {
        				"type": "OnPremisesSqlLinkedService",
        				"ConnectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;",
		   				"gatewayName": "adftutorialgateway",
				        "username": "<Specify user name if you are using Windows Authentication>",
				        "password": "<Specify password for the user account>"
    				}
				}		
		
6. Fare clic su **Distribuisci** sulla barra degli strumenti per distribuire SqlServerLinkedService. Controllare che sulla barra del titolo sia visualizzato un messaggio analogo a **CREAZIONE DEL SERVIZIO COLLEGATO COMPLETATA**. Nella visualizzazione albero a sinistra dovrebbe essere visualizzato il servizio **SqlServerLinkedService**.
		   
	![Distribuzione di SqlServerLinkedService riuscita][image-editor-sql-linked-service-successful]
	
  
È anche possibile creare un servizio collegato di SQL Server facendo clic sul pulsante **Nuovo archivio dati** della barra degli strumenti sul pannello **Servizi collegati**. Se si effettua questa scelta, è possibile impostare le credenziali per l'origine dati usando l'applicazione ClickOnce di gestione credenziali in esecuzione nel computer da cui si accede al portale. Se si accede al portale da un computer diverso dal computer del gateway, è necessario assicurarsi che l'applicazione di gestione credenziali possa connettersi al computer del gateway. Se l'applicazione non riesce a raggiungere il computer del gateway, non sarà possibile impostare le credenziali per l'origine dati e per testare la connessione all'origine dati.

#### Aggiungere un servizio collegato per un account di archiviazione di Azure
 
1. Nell'**Editor**, fare clic sul pulsante **Nuovo archivio dati** sulla barra degli strumenti, quindi scegliere **Archiviazione di Azure** dal menu a discesa. Nel riquadro a destra verrà visualizzato il modello JSON per la creazione di un servizio collegato di archiviazione di Azure. 

	![Pulsante Nuovo archivio dati dell'editor][image-editor-newdatastore-button]
    
6. Sostituire **<accountname>** e **<accountkey>** con i valori relativi a nome e chiave dell'account di archiviazione di Azure.

	![JSON dell'archivio BLOB dell'editor][image-editor-blob-storage-json]
	
	Per dettagli sulle proprietà JSON, vedere il [riferimento sugli script JSON](http://go.microsoft.com/fwlink/?LinkId=516971).

6. Fare clic su **Distribuisci** sulla barra degli strumenti per distribuire StorageLinkedService. Controllare che sulla barra del titolo sia visualizzato un messaggio simile a **CREAZIONE DEL SERVIZIO COLLEGATO COMPLETATA**.

	![Distribuzione dell'archivio BLOB dell'editor][image-editor-blob-storage-deploy]

 
## Passaggio 3: creare set di dati di input e di output
In questo passaggio vengono creati i set di dati di input e di output che rappresentano i dati di input e di output per l'operazione di copia (database SQL Server locale => archiviazione BLOB di Azure). Prima di creare i set di dati o le tabelle (set di dati rettangolari), è necessario eseguire quanto segue (i passaggi dettagliati seguono l'elenco):

- Creare una tabella denominata **emp** nel database SQL Server aggiunto come servizio collegato all'istanza di Data factory e inserire una coppia di voci di esempio nella tabella.
- - Se non è stata completata l'esercitazione nell'articolo [Introduzione a Data factory di Azure][adfgetstarted], creare un contenitore BLOB denominato **adftutorial** nell'account di archiviazione BLOB di Azure aggiunto come servizio collegato all'istanza di Data factory.

### Preparare SQL Server locale per l'esercitazione

1. Nel database specificato per il servizio collegato di SQL Server locale (OnPremSqlLinkedService), usare il seguente script SQL per creare la tabella **emp** nel database.


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



### Creazione della tabella di input

1.	Nell'**Editor di Data factory**, fare clic su **Nuovo set di dati** sulla barra dei comandi e selezionare **SQL locale**. 
2.	Sostituire lo script JSON nel riquadro a destra con il testo seguente:    

        {
    		"name": "EmpOnPremSQLTable",
    		"properties":
    		{
        		"location":
        		{
            		"type": "OnPremisesSqlServerTableLocation",
            		"tableName": "emp",
            		"linkedServiceName": "SqlServerLinkedService"
        		},
        		"availability": 
        		{
            		"frequency": "Hour",
            		"interval": 1,       
	    			"waitOnExternal":
	    			{
        				"retryInterval": "00:01:00",
	        			"retryTimeout": "00:10:00",
	        			"maximumRetry": 3
	    			}
		  
        		}
    		}
		}

	Tenere presente quanto segue:
	
	- L'oggetto **type** di location è impostato su **OnPremisesSqlServerTableLocation**.
	- **tablename** è impostato su **emp**.
	- **linkedServiceName** è impostato su **SqlServerLinkedService**; questo servizio collegato è stato creato nel passaggio 2.
	- Per una tabella di input non generata da un'altra pipeline in Data factory di Azure, è necessario specificare la sezione **waitOnExternal** nel JSON. Indica che i dati di input sono generati al di fuori del servizio Data factory di Azure.   

	Per dettagli sulle proprietà JSON, vedere il [riferimento sugli script JSON][json-script-reference].

2. Fare clic su **Distribuisci** sulla barra dei comandi per distribuire la set di dati (la tabella è un set di dati rettangolare). Controllare che sulla barra del titolo sia visualizzato un messaggio analogo a **DISTRIBUZIONE TABELLA COMPLETATA**.


### Creazione della tabella di output

1.	Nell'**Editor di Data factory** fare clic su **Nuovo set di dati** sulla barra dei comandi e selezionare **Archiviazione BLOB di Azure**.
2.	Sostituire lo script JSON nel riquadro a destra con il testo seguente: 

		{
    		"name": "OutputBlobTable",
    		"properties":
    		{
        		"location": 
        		{
            		"type": "AzureBlobLocation",
            		"folderPath": "adftutorial/outfromonpremdf",
            		"format":
            		{
                		"type": "TextFormat",
                		"columnDelimiter": ","
            		},
            		"linkedServiceName": "StorageLinkedService"
        		},
        		"availability": 
        		{
            		"frequency": "Hour",
            		"interval": 1
        		}
    		}
		}
  
	Tenere presente quanto segue:
	
	- L'oggetto **type** di location è impostato su **AzureBlobLocation**.
	- **linkedServiceName** è impostato su **StorageLinkedService**; questo servizio collegato è stato creato nel passaggio 2.
	- **folderPath** è impostato su **adftutorial/outfromonpremdf** dove outfromonpremdf è la cartella nel contenitore adftutorial. È necessario creare solo il contenitore **adftutorial**.
	- L'oggetto **availability** è impostato su **hourly**; l'oggetto **frequency** è impostato su **hour** e l'oggetto **interval** è impostato su **1**. Il servizio Data factory genererà una porzione di dati di output ogni ora nella tabella **emp** nel database SQL di Azure. 

	Se non si specifica un oggetto **fileName** per una **tabella di input**, tutti i file/BLOB della cartella di input (**folderPath**) vengono considerati input. Se si specifica un oggetto fileName nel JSON, solo il file/BLOB specificato viene considerato un input. Per alcuni esempi, vedere i file nell'[esercitazione][adf-tutorial].
 
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

 

	Per dettagli sulle proprietà JSON, vedere il [riferimento sugli script JSON][json-script-reference].

2.	Fare clic su **Distribuisci** sulla barra dei comandi per distribuire la set di dati (la tabella è un set di dati rettangolare). Controllare che sulla barra del titolo sia visualizzato un messaggio analogo a **DISTRIBUZIONE TABELLA COMPLETATA**.
  

## Passaggio 4: creare ed eseguire una pipeline
In questo passaggio viene creata una **pipeline** con un'**attività di copia** che usa **EmpOnPremSQLTable** come input e **OutputBlobTable** come output.

1.	Fare clic su **Nuova pipeline** sulla barra dei comandi. Se il pulsante non è presente, fare clic su **...(puntini di sospensione)** per visualizzarlo.
2.	Sostituire lo script JSON nel riquadro a destra con il testo seguente:   


        {
			"name": "ADFTutorialPipelineOnPrem",
    		"properties":
    		{
        		"description" : "This pipeline has one Copy activity that copies data from an on-prem SQL to Azure blob",
	       		 "activities":
	        	[
			    	{
						"name": "CopyFromSQLtoBlob",
						"description": "Copy data from on-prem SQL server to blob",		
						"type": "CopyActivity",
						"inputs": [ {"name": "EmpOnPremSQLTable"} ],
						"outputs": [ {"name": "OutputBlobTable"} ],
						"transformation":
						{
							"source":
							{                               
								"type": "SqlSource",
								"sqlReaderQuery": "select * from emp"
							},
							"sink":
							{
								"type": "BlobSink"
							}	
						},
						"Policy":
						{
							"concurrency": 1,
							"executionPriorityOrder": "NewestFirst",
							"style": "StartOfInterval",
							"retry": 0,
							"timeout": "01:00:00"
						}		

				     }
	        	],
				"start": "2015-02-13T00:00:00Z",
        		"end": "2015-02-14T00:00:00Z",
        		"isPaused": false
			}
		}

	Tenere presente quanto segue:
 
	- Nella sezione delle attività esiste una sola attività con l'oggetto **type** impostato su **CopyActivity**.
	- **Input** per l'attività è impostato su **EmpOnPremSQLTable** e **output** per l'attività è impostato su **OutputBlobTable**.
	- Nella sezione **transformation**, **SqlSource** viene specificato come **tipo di origine** e **BlobSink **come **tipo di sink**. - La query SQL **select * from emp** viene specificata per la proprietà **sqlReaderQuery** di **SqlSource**.

	Sostituire il valore della proprietà **start** con il giorno corrente e il valore di **end** con il giorno successivo. Per la data e ora di inizio è necessario usare il [formato ISO](http://en.wikipedia.org/wiki/ISO_8601), ad esempio 2014-10-14T16:32:41Z. Il valore di **end** è facoltativo, ma in questa esercitazione verrà usato.
	
	Se non si specifica alcun valore per la proprietà **end**, il valore verrà calcolato come "**start + 48 hours**". Per eseguire la pipeline illimitatamente, specificare **9/9/9999** come valore per la proprietà **end**.
	
	Si definisce quanto tempo durerà l'elaborazione delle sezioni di dati in base alle proprietà della **disponibilità** definite per ogni tabella di Data factory di Azure.
	
	Nell'esempio precedente sono visualizzate 24 sezioni di dati perché viene generata una sezione di dati ogni ora.
	
2. Fare clic su **Distribuisci** sulla barra dei comandi per distribuire la set di dati (la tabella è un set di dati rettangolare). Controllare che sulla barra del titolo sia visualizzato un messaggio analogo a **DISTRIBUZIONE PIPELINE COMPLETATA**.
5. A questo punto, chiudere il pannello dell'**Editor** facendo clic su **X**. Fare di nuovo clic su **X** per chiudere il pannello ADFTutorialDataFactory con la barra degli strumenti e la visualizzazione ad albero. Se viene visualizzato un messaggio analogo a **Eventuali modifiche non salvate verranno rimosse**, fare clic su **OK**.
6. Dovrebbe essere visualizzato di nuovo il pannello **DATA FACTORY** per **ADFTutorialOnPremDF**.

**Congratulazioni**. Data factory di Azure, i servizi collegati, le tabelle e una pipeline sono stati creati correttamente e la pipeline è stata pianificata.

### Visualizzare la data factory in una vista Diagramma 
1. Nel **portale di anteprima di Azure**, fare clic sul riquadro **Diagramma** nella home page per l'istanza di Data factory **ADFTutorialOnPremDF**:

	![Collegamento al diagramma][image-data-factory-diagram-link]

2. Viene visualizzato un diagramma simile al seguente:

	![Vista Diagramma][image-data-factory-diagram-view]

	È possibile eseguire lo zoom avanti, lo zoom indietro e lo zoom al 100%, adattare alla finestra, posizionare automaticamente pipeline e tabelle e visualizzare le informazioni sulla derivazione, evidenziando gli elementi upstream e downstream degli elementi selezionati. È possibile fare doppio clic su un oggetto (tabella di input/output o pipeline) per visualizzare le rispettive proprietà.

## Passaggio 5: monitorare i set di dati e la pipeline
In questo passaggio viene usato il portale di Azure per monitorare le attività in un'istanza di Data factory di Azure. È anche possibile usare i cmdlet di PowerShell per monitorare i set di dati e le pipeline. Per dettagli sull'uso dei cmdlet per il monitoraggio, vedere la pagina relativa a [monitoraggio e gestione di Data factory di Azure con PowerShell][monitor-manage-powershell].

1. Passare al **portale di anteprima di Azure** (se è stato chiuso)
2. Se il pannello per **ADFTutorialOnPremDF** è chiuso, aprirlo facendo clic su **ADFTutorialOnPremDF** nella **Schermata iniziale**.
3. Vengono visualizzati il **numero** e i **nomi** delle tabelle e delle pipeline create nel pannello.

	![Home page di Data factory][image-data-factory-homepage-2]
4. A questo punto, fare clic sul riquadro **Set di dati**.
5. Nel pannello **Set di dati**, fare clic su **EmpOnPremSQLTable**.

	![Sezioni EmpOnPremSQLTable][image-data-factory-onprem-sqltable-slices]

6. Le sezioni di dati fino all'ora corrente sono state già generate e sono in stato **Pronto**. Ciò è dovuto al fatto che i dati sono stati inseriti nel database SQL Server database che è sempre presente. Verificare che non sia visualizzata alcuna sezione in **Sezioni con errori** nella parte inferiore della pagina.


	Gli elenchi **Sezioni aggiornate di recente** e **Sezioni non riuscite di recente** sono ordinati in base a **ORA ULTIMO AGGIORNAMENTO**. L'ora di aggiornamento di una sezione viene modificata nelle situazioni seguenti.
    

	-  Lo stato della sezione viene aggiornato manualmente, ad esempio usando **Set-AzureDataFactorySliceStatus** oppure facendo clic su **ESEGUI** nel pannello **SEZIONE** della sezione.
	-  Lo stato della sezione cambia a causa di un'esecuzione, ad esempio un'esecuzione avviata, un'esecuzione terminata con errore, un'esecuzione terminata correttamente e così via.
 
	Fare clic sul titolo degli elenchi oppure sui **... (puntini di sospensione)** per visualizzare un elenco più ampio delle sezioni. Fare clic su **Filtro** sulla barra degli strumenti per filtrare le sezioni.
	
	Per visualizzare le sezioni di dati ordinate invece in base agli orari di inizio/fine, fare clic sul riquadro **Sezioni dati (in base all'ora della sezione)**.

7. A questo punto, nel pannello **Set di dati**, fare clic su **OutputBlobTable**.

	![Sezioni OputputBlobTable][image-data-factory-output-blobtable-slices]
8. Controllare che le sezioni fino all'ora corrente siano state generate e siano in stato **Pronto**. Attendere finché lo stato delle sezioni fino all'ora corrente non viene impostato **Pronto**.
9. Fare clic su una qualsiasi sezione di dati dell'elenco per visualizzare il pannello **SEZIONE DI DATI**.

	![Pannello Sezione dati][image-data-factory-dataslice-blade]

	Se lo stato della sezione non è **Pronto**, sarà possibile visualizzare le sezioni upstream che non sono pronte e bloccano l'esecuzione della sezione corrente nell'elenco **Sezioni upstream non pronte**.

10. Fare clic sull'**esecuzione attività** dall'elenco nella parte inferiore della pagina per visualizzare i **dettagli dell'esecuzione attività**.

	![Pannello Dettagli esecuzione attività][image-data-factory-activity-run-details]

11. Fare clic su **X** per chiudere tutti i pannelli finché non viene visualizzato il pannello iniziale per **ADFTutorialOnPremDF**.
14. (Facoltativo) Fare clic su **Pipeline**, **ADFTutorialOnPremDF**, quindi eseguire il drill-through delle tabelle di input (usate) o delle tabelle di output (generate).
15. Usare strumenti come **Esplora archivi Azure** per verificare l'output.

	![Esplora archivi Azure][image-data-factory-stroage-explorer]


## Creazione e registrazione di un gateway con Azure PowerShell 
Questa sezione descrive come creare e registrare un gateway con i cmdlet di Azure PowerShell.

1. Avviare **Azure PowerShell** in modalità di amministrazione. 
2. I cmdlet di Data factory di Azure sono disponibili in modalità **AzureResourceManager**. Eseguire il seguente comando per passare in modalità **AzureResourceManager**.     

        switch-azuremode AzureResourceManager


2. Usare il cmdlet **New-AzureDataFactoryGateway** per creare un gateway logico come illustrato di seguito:

		New-AzureDataFactoryGateway -Name <gatewayName> -DataFactoryName <dataFactoryName> -ResourceGroupName ADF –Description <desc>

	**Comando di esempio e output**:


		PS C:\> New-AzureDataFactoryGateway -Name MyGateway -DataFactoryName $df -ResourceGroupName ADF –Description “gateway for walkthrough”

		Name              : MyGateway
		Description       : gateway for walkthrough
		Version           :
		Status            : NeedRegistration
		VersionStatus     : None
		CreateTime        : 9/28/2014 10:58:22
		RegisterTime      :
		LastConnectTime   :
		ExpiryTime        :
		ProvisioningState : Succeeded


3. Usare il cmdlet **New-AzureDataFactoryGatewayKey** per generare una chiave di registrazione per il gateway appena creato e archiviare la chiave in una variabile locale **$Key**:

		New-AzureDataFactoryGatewayKey -GatewayName <gatewayname> -ResourceGroupName ADF -DataFactoryName <dataFactoryName>

	
	**Output del comando di esempio**:


		PS C:\> $Key = New-AzureDataFactoryGatewayKey -GatewayName MyGateway -ResourceGroupName ADF -DataFactoryName $df 

	
4. In Azure PowerShell, passare alla cartella. **C:\Programmi\Microsoft Data Management Gateway\1.0\PowerShellScript** ed eseguire lo script **RegisterGateway.ps1** associato alla variabile locale **$Key** come mostrato nel seguente comando per registrare l'agente client installato nel computer con il gateway logico creato in precedenza.

		PS C:\> .\RegisterGateway.ps1 $Key.GatewayKey
		
		Agent registration is successful!

5. È possibile usare il cmdlet **Get-AzureDataFactoryGateway** per ottenere l'elenco di gateway nell'istanza di Data factory. Quando lo **stato** è **online**, il gateway è pronto per essere usato.

		Get-AzureDataFactoryGateway -DataFactoryName <dataFactoryName> -ResourceGroupName ADF

È possibile rimuovere un gateway con il cmdlet **Remove-AzureDataFactoryGateway** e aggiornare la descrizione per un gateway usando il cmdlet **Set-AzureDataFactoryGateway**. Per la sintassi e altri dettagli relativi a questi cmdlet, vedere Riferimento ai cmdlet di Data factory.



[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[adf-getstarted]: data-factory-get-started.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[use-pig-and-hive-with-data-factory]: data-factory-pig-hive-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[data-factory-editor]: data-factory-editor.md

[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[64bit-download-link]: http://go.microsoft.com/fwlink/?LinkId=517623
[32bit-download-link]: http://go.microsoft.com/fwlink/?LinkId=517624

[azure-preview-portal]: http://portal.azure.com
[adfgetstarted]: data-factory-get-started.md
[monitor-manage-powershell]: data-factory-monitor-manage-using-powershell.md





[json-script-reference]: http://go.microsoft.com/fwlink/?LinkId=516971
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456



[azure-powershell-install]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/

[image-author-deploy-tile]: ./media/data-factory-use-onpremises-datasources/author-deploy-tile.png
[image-editor-newdatastore-button]: ./media/data-factory-use-onpremises-datasources/editor-newdatastore-button.png
[image-editor-blob-storage-json]: ./media/data-factory-use-onpremises-datasources/editor-blob-storage-settings.png
[image-editor-blob-storage-deploy]: ./media/data-factory-use-onpremises-datasources/editor-deploy-blob-linked-service.png
[image-editor-newdatastore-onpremsql-button]: ./media/data-factory-use-onpremises-datasources/editor-newdatastore-onpremsql-button.png
[image-editor-newdatastore-onpremsql-settings]: ./media/data-factory-use-onpremises-datasources/editor-onprem-sql-settings.png
[image-editor-sql-linked-service-successful]: ./media/data-factory-use-onpremises-datasources/editor-sql-linked-service-successful.png

[gateway-status]: ./media/data-factory-use-onpremises-datasources/gateway-status.png
[express-setup-succeeded]: ./media/data-factory-use-onpremises-datasources/express-setup-succeeded.png
[image-data-factory-onprem-new-everything]: ./media/data-factory-use-onpremises-datasources/OnPremNewEverything.png

[image-data-factory-onprem-datastorage-cache-backup]: ./media/data-factory-use-onpremises-datasources/OnPremDataStorageCacheBackup.png

[image-data-factory-onprem-datastorage-see-all]: ./media/data-factory-use-onpremises-datasources/OnPremDataStorageSeeAll.png

[image-data-factory-onprem-dataservices-blade]: ./media/data-factory-use-onpremises-datasources/OnPremDataServicesBlade.png

[image-data-factory-onprem-datafactory-preview-blade]: ./media/data-factory-use-onpremises-datasources/DataFactoryPreviewBlade.png

[image-data-factory-onprem-create-resource-group]: ./media/data-factory-use-onpremises-datasources/OnPremCreateResourceGroup.png

[image-data-factory-add-to-startboard]: ./media/data-factory-use-onpremises-datasources/OnPremNewDataFactoryAddToStartboard.png

[image-data-factory-notifications-hub]: ./media/data-factory-use-onpremises-datasources/OnPremNotificationsHub.png

[image-data-factory-datafactory-homepage]: ./media/data-factory-use-onpremises-datasources/OnPremDataFactoryHomePage.png

[image-data-factory-startboard]: ./media/data-factory-use-onpremises-datasources/OnPremStartboard.png

[image-data-factory-linkedservices-blade]: ./media/data-factory-use-onpremises-datasources/OnPremLinkedServicesBlade.png

[image-data-factory-linkedservices-add-datastore-button]: ./media/data-factory-use-onpremises-datasources/ONPremLinkedServicesAddDataStoreButton.png

[image-data-factory-new-datastore-blade]: ./media/data-factory-use-onpremises-datasources/OnPremNewDataStoreBlade.png

[image-data-factory-get-storage-key]: ./media/data-factory-use-onpremises-datasources/OnPremGetStorageKey.png

[image-data-factory-linkedservices-add-gateway-button]: ./media/data-factory-use-onpremises-datasources/OnPremLinkedServicesAddGaewayButton.png

[image-data-factory-create-gateway-blade]: ./media/data-factory-use-onpremises-datasources/OnPremCreateGatewayBlade.png

[image-data-factory-gateway-configure-blade]: ./media/data-factory-use-onpremises-datasources/OnPremGatewayConfigureBlade.png

[image-data-factory-gateway-configuration-manager]: ./media/data-factory-use-onpremises-datasources/OnPremDMGConfigurationManager.png

[image-data-factory-oneclick-install]: ./media/data-factory-use-onpremises-datasources/OnPremOneClickInstall.png

[image-data-factory-diagram-link]: ./media/data-factory-use-onpremises-datasources/OnPremDiagramLink.png

[image-data-factory-diagram-view]: ./media/data-factory-use-onpremises-datasources/OnPremDiagramView.png

[image-data-factory-homepage-2]: ./media/data-factory-use-onpremises-datasources/OnPremDataFactoryHomePage2.png

[image-data-factory-stroage-explorer]: ./media/data-factory-use-onpremises-datasources/OnPremAzureStorageExplorer.png

[image-data-factory-home-age]: ./media/data-factory-use-onpremises-datasources/DataFactoryHomePage.png

[image-data-factory-onprem-sqltable-slices]: ./media/data-factory-use-onpremises-datasources/OnPremSQLTableSlicesBlade.png

[image-data-factory-output-blobtable-slices]: ./media/data-factory-use-onpremises-datasources/OutputBlobTableSlicesBlade.png

[image-data-factory-dataslice-blade]: ./media/data-factory-use-onpremises-datasources/DataSlice.png

[image-data-factory-activity-run-details]: ./media/data-factory-use-onpremises-datasources/ActivityRunDetailsBlade.png

[image-data-factory-new-datafactory-menu]: ./media/data-factory-use-onpremises-datasources/NewDataFactoryMenu.png

[image-data-factory-preview-portal-storage-key]: ./media/data-factory-get-started/PreviewPortalStorageKey.png

<!---HONumber=62-->