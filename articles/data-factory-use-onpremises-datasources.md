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
	ms.date="05/06/2015" 
	ms.author="spelluru"/>

# Consentire alle pipeline di usare dati locali

Per consentire alle pipeline in Data factory di Azure di usare un'origine dati locale, è necessario aggiungere l'origine dati locale come servizio collegato a un'istanza di Data factory usando il portale di gestione di Azure o Azure PowerShell.
 
Per aggiungere un'origine dati locale come servizio collegato a un'istanza di Data factory, è necessario scaricare e installare prima il Gateway di gestione dati di Microsoft in un computer locale e configurare il servizio collegato per l'origine dati locale per poter usare il gateway.


## <a href="DMG"></a> Gateway di gestione dati

**Gateway di gestione dati** è un software che si connette origini dati locali per servizi cloud in modo protetto e gestito. Con il Gateway di gestione dati, è possibile

- **Connessione ai dati locali per l'accesso ai dati ibrido** – è possibile connettere i dati locali a servizi cloud per usufruire dei servizi cloud mantenendo business in esecuzione con dati locali.
- **Definire un proxy per la protezione dei dati** – è possibile definire le origini dati locali vengono esposte con Gateway di gestione dati, pertanto tale gateway autentica la richiesta di dati dai servizi cloud e protegge le origini dati locali.
- **Gestire il gateway per la governance completo** – viene fornito completo di monitoraggio e registrazione di tutte le attività all'interno di Gateway di gestione dati per la gestione e controllo.
- **Spostare i dati in modo efficiente** : i dati vengono trasferiti in parallelo, problemi di rete resiliente a intermittente con automaticamente la logica di riesecuzione.


Il Gateway di gestione dati ha una gamma completa di funzionalità di connessione dei dati locali che include:

- **Non intrusivo per firewall aziendale** : Gateway di gestione dati funziona solo dopo l'installazione, senza dover aprire un firewall connessione o richiedere intrusivo modifiche all'infrastruttura di rete aziendale. 
- **Crittografare le credenziali con il certificato** : le credenziali utilizzate per connettersi alle origini dati vengono crittografate con un certificato di proprietà da un utente. Senza il certificato, nessuno può decrittografare le credenziali in testo normale, neanche Microsoft.

### Considerazioni per l'uso del Gateway di gestione dati
1.	Una singola istanza del Gateway di gestione dati può essere utilizzato per più origini dati locali, ma tenere presente che un **gateway è associata a una factory di dati di Azure** e non può essere condivisa con un altro produttore di dati.
2.	È possibile **solo un'istanza del Gateway di gestione dati** installato sul computer. Si supponga di avere due istanze di Data factory che richiedono l'accesso alle origini dati locali: è necessario installare i gateway nei due computer locali in cui ogni gateway sia associato a un'istanza separata di Data factory.
3.	Il **gateway non deve essere nello stesso computer come origine dati**, ma sempre più da vicino all'origine dati riduce il tempo per il gateway per la connessione all'origine dati. È consigliabile installare il gateway in un computer diverso da quello che ospita l'origine dati locale in modo che il gateway non si contendono le risorse con origine dati.
4.	È possibile **più gateway su diversi computer che si connettono alla stessa origine dati locale**. Ad esempio, potrebbe essere due gateway che servono due factory di dati, ma la stessa origine dati locale viene registrata con factory dati. 
5.	Se si dispone già di un gateway installato nel server del computer una **Power BI** scenario, installare un **gateway separato per Azure dati Factory** su un altro computer.

### Porte e considerazioni sulla sicurezza 
- Il programma di installazione di Gateway di gestione dati apre **8050** e **8051** porte nel computer del gateway. Queste porte vengono utilizzate per la **Gestione credenziali** (applicazione ClickOnce), che consente di impostare le credenziali per un servizio locale collegati e testare la connessione all'origine dati. Queste porte non è raggiungibile da internet e non è necessario necessario questi aperta nel firewall aziendale.
- Quando si copiano dati da e verso un database di SQL Server locale da un database SQL Azure, verificare quanto segue:
 
	- Firewall nel computer del gateway consente la comunicazione TCP in uscita in **TCP** porta **1433**
	- Configurare [le impostazioni del firewall SQL Azure](https://msdn.microsoft.com/library/azure/jj553530.aspx) per aggiungere il **indirizzo IP del computer del gateway** per il **indirizzi IP consentiti**.

- Durante la copia dei dati a/da SQL Server locale a qualsiasi destinazione e il gateway e il computer SQL Server sono diversi, eseguire le operazioni seguenti: [configurare Windows Firewall](https://msdn.microsoft.com/library/ms175043.aspx) in SQL Server del computer in modo che il gateway può accedere al database tramite la porta è in attesa l'istanza di SQL Server su. Per l'istanza predefinita è la porta 1433.

- È necessario avviare il **Gestione credenziali** dell'applicazione in un computer è in grado di connettersi al Gateway di gestione dati sia in grado di impostare le credenziali per l'origine dati e per testare la connessione all'origine dati.

### Installazione del gateway - prerequisiti 

1.	Supportato **sistema operativo** versioni sono Windows 7, Windows 8 o 8.1, Windows Server 2008 R2, Windows Server 2012.
2.	L'elemento consigliato **configurazione** per il gateway macchina è almeno 2 GHz, 4 core, 8 GB di RAM e 80 GB di disco.
3.	Se il computer host entra in ibernazione, il gateway non potrà rispondere alle richieste di dati. Pertanto, configurare un'apposita **il risparmio di energia** nel computer prima di installare il gateway. L'installazione del gateway richiede un messaggio se il computer è configurato per la sospensione.  


 

## Procedura dettagliata

In questa procedura dettagliata viene creata un'istanza di Data factory con una pipeline che sposta i dati da un database SQL Server locale a un BLOB di Azure.

## Passaggio 1: Creare una factory di dati di Azure
In questo passaggio, è possibile utilizzare il portale di gestione di Azure per creare un'istanza della Factory di dati di Azure denominata **ADFTutorialOnPremDF**. È anche possibile creare un'istanza di Data factory con i cmdlet di Data factory di Azure.

1.	Accedendo alla [portale Azure Preview][azure-preview-portal], fare clic su **nuovo** nell'angolo inferiore sinistro, selezionare **analisi di dati** nel **Crea** blade e fare clic su **dati Factory** sul **analisi di dati** blade.

	![Nuovo -> DataFactory][image-data-factory-new-datafactory-menu]
  
6. Nel **nuova factory di dati** blade:
	1. Immettere **ADFTutorialOnPremDF** per il **nome**.
	2. Fare clic su **nome gruppo di risorse** e selezionare **ADFTutorialResourceGroup** (se è stato fatto l'esercitazione dal [iniziare a utilizzare Azure dati Factory][adfgetstarted]. È possibile selezionare un gruppo di risorse esistente o crearne uno nuovo. Per creare un nuovo gruppo di risorse:
		1. Fare clic su **creare un nuovo gruppo di risorse**.
		2. Nel **blade del gruppo di risorse Create**, immettere un **nome** per il gruppo di risorse, scegliere **OK**.

7. Si noti che **aggiungere alla schermata iniziale** sia selezionata la **nuova factory di dati** blade.

	![Aggiungi a schermata iniziale][image-data-factory-add-to-startboard]

8. Nel **nuova factory di dati** blade, fare clic su **Crea**.

	> [AZURE.NOTE]**Nome del produttore di dati "ADFTutorialOnPremDF" non è disponibile**  
9. Cercare le notifiche dal processo di creazione nel **notifiche** hub sul lato sinistro. Fare clic su **X** per chiudere la **come origine delle notifiche** blade se è aperto.

	![Hub notifiche][image-data-factory-notifications-hub]

11. Dopo aver completato la creazione, verrà visualizzato il **dati Factory** blade, come illustrato di seguito:

	![Dati Factory Home Page][image-data-factory-datafactory-homepage]

## Passaggio 2: Creare un gateway di gestione dati
5.	Nel **dati Factory** blade per **ADFTutorialOnPremDF**, fare clic su **servizi collegati**. 

	![Dati Factory Home Page][image-data-factory-home-age]

2.	Nel **servizi collegati** blade, fare clic su **+ gateway dati**.

	![Servizi collegati - aggiungere un pulsante di Gateway][image-data-factory-linkedservices-add-gateway-button]

2. Nel **Crea** blade, immettere **adftutorialgateway** per il **nome**, e fare clic su **OK**.

	![Creare blade Gateway][image-data-factory-create-gateway-blade]

3. Nel pannello **Configura** fare clic su **Installa direttamente nel computer**. Viene scaricato il pacchetto di installazione per il gateway, che viene installato, configurato e registrato nel computer.

	> [AzURE.NOTE]Utilizzare Internet Explorer o un browser compatibile con Microsoft ClickOnce.

	![Gateway - configurare blade][image-data-factory-gateway-configure-blade]

	Si tratta del metodo più semplice (con un clic) di scaricare, installare, configurare e registrare il gateway in un unico passaggio. È possibile visualizzare il **Gestione configurazione di Gateway di gestione dati di Microsoft** applicazione è installata nel computer. È inoltre possibile trovare il file eseguibile **ConfigManager.exe** nella cartella: **C:\Program Files\Microsoft Data Management Gateway\1.0\Shared**.

	È inoltre possibile scaricare e installare gateway manualmente utilizzando i collegamenti in questo pannello e registrarlo utilizzando la chiave indicata nella **con codice di registrazione** casella di testo.
	
	Vedere [Gateway di gestione dati](#DMG) per ulteriori informazioni sui gateway incluse importanti considerazioni e procedure consigliate.

	>[AZURE.NOTE]È necessario essere un amministratore nel computer locale per installare e configurare correttamente il Gateway di gestione dati. È possibile aggiungere altri utenti al gruppo di Windows locale Data Management Gateway Users. I membri di questo gruppo sarà in grado di utilizzare lo strumento Gestione configurazione Gateway di gestione dati per configurare il gateway.

4. Scegliere il **notifiche** hub sul lato sinistro. Attendere la visualizzazione **Express per 'adftutorialgateway' ha avuto esito positivo del programma di installazione** dei messaggi nel **notifiche** blade.

	![Installazione rapida ha avuto esito positivo][express-setup-succeeded]
5. Fare clic su **OK** sul **Crea** blade e quindi la **Nuovo gateway dati** blade.
6. Chiudi il **servizi collegati** blade (premendo **X** pulsante nell'angolo in alto a destra) e riaprire il **servizi collegati** blade per visualizzare lo stato più recente del gateway. 
7. Verificare che il **stato** del gateway è **Online**. 

	![Stato del gateway][gateway-status]
5. Avviare **Gestione configurazione di Gateway di gestione dati di Microsoft** applicazione nel computer.

	![Gestione configurazione di gateway][image-data-factory-gateway-configuration-manager]

6. Attendere finché i valori non sono impostati come segue:
	1. Se il servizio **stato** non è impostata su **avviato**, fare clic su **avvio del servizio** per avviare il servizio e attendere un minuto per gli altri campi da aggiornare.
	2. **Nome gateway** è impostato su **adftutorialgateway**.
	3. **Nome dell'istanza** è impostato su **adftutorialgateway**.
	4. **Stato chiave del gateway** è impostato su **registrati**.
	5. La barra consente di visualizzare la parte inferiore di stato **connesso al servizio Cloud Gateway di gestione dati** insieme a un **segno di spunta verde**.
	
7. Nel **servizi collegati** blade, verificare che il **stato** del gateway è **buona**.
8. Chiudere tutti i server blade fino a visualizzare il **Factory dati** homepage. 

## Passaggio 2: Creare servizi collegati 
In questo passaggio si creerà due servizi collegati: **StorageLinkedService** e **SqlServerLinkedService**. Il **SqlServerLinkedService** collega un database di SQL Server locale e **StorageLinkedService** servizi collegati collega un archivio blob di Azure per la **ADFTutorialDataFactory**. Più avanti nella procedura dettagliata verrà creata una pipeline che copia i dati dal database SQL Server locale all'archivio BLOB di Azure.

### Aggiungere un servizio collegato a un database SQL Server locale
1.	Nel **dati FACTORY** blade, fare clic sulla **autore e distribuire** riquadro per avviare il **Editor** per la factory di dati.

	![Riquadro Creare e distribuire][image-author-deploy-tile]

	> [AZURE.NOTE][Factory Editor dei dati][data-factory-editor]
2.	Nel **Editor**, fare clic su **nuovo archivio dati** pulsante sulla barra degli strumenti e selezionare **database del server SQL locale** dal menu a discesa. 

	![Editor nuovo pulsante di archivio dati][image-editor-newdatastore-onpremsql-button]
    
3.	Verrà visualizzato il modello JSON per la creazione di un servizio di SQL Server collegati in locale nel riquadro di destra. ![Servizi collegati locale SQL - impostazioni][image-editor-newdatastore-onpremsql-settings]

4.	eseguire le operazioni seguenti nel riquadro di JSON:
	1.	Per il **gatewayName** proprietà, immettere **adftutorialgateway** di sostituire tutto il testo all'interno delle virgolette doppie.  
	2.	Se si utilizza **l'autenticazione di SQL**: 
		1.	Per il **connectionString** proprietà, sostituire **< nomeserver >**, **< databasename >**, **< nomeutente >**, e **< password >** con nomi del server SQL locale, database, account utente e password.	
		2.	Rimuovere le ultime due proprietà (* * * nome utente e **password**) da JSON file e rimuovere il **virgola (,)** caratteri alla fine dell'ultima riga dello script JSON rimanenti.
		
				{
	    			"name": "SqlServerLinkedService",
	    			"properties": {
		        		"type": "OnPremisesSqlLinkedService",
		        		"ConnectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;",
		        		"gatewayName": "adftutorialgateway"
	    			}
				}
	3.	Se si utilizza **l'autenticazione di Windows**:
		1. Per il **connectionString** proprietà, sostituire **< nomeserver >** e **< databasename >** con nomi di database e SQL server locale. Impostare **la sicurezza integrata di** a **True**. Rimuovere **ID** e **Password** dalla stringa di connessione.
			
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
		
6. Fare clic su **Distribuisci** sulla barra degli strumenti per distribuire il SqlServerLinkedService. Verificare che sia visualizzato il messaggio **collegato servizio creato correttamente** sulla barra del titolo. È inoltre necessario verificare il **SqlServerLinkedService** nella visualizzazione albero a sinistra.
		   
	![Distribuzione SqlServerLinkedService riuscita][image-editor-sql-linked-service-successful]
	
  
> [AZURE.NOTE]È inoltre possibile creare un servizio di SQL Server collegato facendo clic su **nuovo archivio dati** sulla barra il **servizi collegati** blade. Se si effettua questa scelta, è possibile impostare le credenziali per l'origine dati utilizzando l'applicazione ClickOnce di gestione di credenziali in esecuzione nel computer l'accesso al portale. Se si accede al portale da un computer diverso dal computer del gateway, è necessario assicurarsi che l'applicazione di gestione credenziali può connettersi al computer del gateway. Se l'applicazione non riesce a raggiungere il computer del gateway, non sarà possibile impostare le credenziali per l'origine dati e di test della connessione all'origine dati.

#### Aggiungere un servizio collegato per un account di archiviazione di Azure
 
1. Nel **Editor**, fare clic su **nuovo archivio dati** pulsante sulla barra degli strumenti e selezionare **di archiviazione Azure** dal menu a discesa. Verrà visualizzato il modello JSON per la creazione di un servizio di archiviazione Azure collegato nel riquadro di destra. 

	![Editor nuovo pulsante di archivio dati][image-editor-newdatastore-button]
    
6. Sostituire **< nome account >** e **< accountkey >** con il nome di account e i valori di chiave di account per l'account di archiviazione di Azure.

	![Archiviazione Blob editor JSON][image-editor-blob-storage-json]
	
	> [AZURE.NOTE]Vedere [JSON Scripting Reference](http://go.microsoft.com/fwlink/?LinkId=516971) per informazioni dettagliate sulle proprietà JSON.

6. Fare clic su **Distribuisci** sulla barra degli strumenti per distribuire il StorageLinkedService. Verificare che sia visualizzato il messaggio **collegato servizio creato correttamente** sulla barra del titolo.

	![Distribuire archiviazione Blob di editor][image-editor-blob-storage-deploy]

 
## Passaggio 3: Creare input e output di set di dati
In questo passaggio vengono creati i set di dati di input e di output che rappresentano i dati di input e di output per l'operazione di copia (database SQL Server locale => archiviazione BLOB di Azure). Prima di creare i set di dati o le tabelle (set di dati rettangolari), è necessario eseguire quanto segue (i passaggi dettagliati seguono l'elenco):

- Creare una tabella denominata **emp** nel Database di SQL Server è stato aggiunto come servizio collegato ai dati factory e inserimento paio di voci di esempio nella tabella.
- - Se non hai completato l'esercitazione dal [iniziare a utilizzare Azure dati Factory][adfgetstarted] articolo, creare un contenitore blob denominato **adftutorial** in Azure account di archiviazione è stato aggiunto come servizio collegato alla factory dati blob.

### Preparare SQL Server locale per l'esercitazione

1. Nel database specificato per on-premise SQL Server collegati di servizio (* * SqlServerLinkedService * *), utilizzare il seguente script SQL per creare il **emp** tabella nel database.


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

1.	Nel **dati Factory Editor**, fare clic su **nuovo set di dati** sulla barra dei comandi, scegliere **On-Premise SQL**. 
2.	Sostituire il JSON nel riquadro di destra con il testo seguente:    

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
	
	- percorso **tipo** è impostato su **OnPremisesSqlServerTableLocation**.
	- **tableName** è impostato su **emp**.
	- **linkedServiceName** è impostato su **SqlServerLinkedService** (è stato creato questo servizio collegato nel passaggio 2).
	- Per una tabella di input non viene generata da un'altra pipeline nella Factory di dati di Azure, è necessario specificare **waitOnExternal** sezione in JSON. Indica che i dati di input sono generati al di fuori del servizio Data factory di Azure.   

	Vedere [JSON Scripting Reference][json-script-reference] per informazioni dettagliate sulle proprietà JSON.

2. Fare clic su **Distribuisci** sulla barra dei comandi per distribuire il set di dati (tabella è un set di dati rettangolare). Verificare che sia visualizzato un messaggio sulla barra del titolo indica **tabella distribuito correttamente**.


### Creazione della tabella di output

1.	Nel **dati Factory Editor**, fare clic su **nuovo set di dati** sulla barra dei comandi, scegliere **archiviazione Blob di Azure**.
2.	Sostituire il JSON nel riquadro di destra con il testo seguente: 

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
	
	- percorso **tipo** è impostato su **AzureBlobLocation**.
	- **linkedServiceName** è impostato su **StorageLinkedService** (è stato creato questo servizio collegato nel passaggio 2).
	- **folderPath** è impostato su **adftutorial/outfromonpremdf** dove outfromonpremdf è la cartella nel contenitore adftutorial. È sufficiente creare il **adftutorial** contenitore.
	- Il **disponibilità** è impostato su **oraria** (* * frequenza * * impostato su **ora** e **intervallo** impostato su **1**). Il servizio dati Factory genererà una porzione di dati di output ogni ora il **emp** tabella nel Database di SQL Azure. 

	Se non si specifica un **fileName** per un **tabella di input**, tutti i BLOB/file dalla cartella di input (* * folderPath * *) vengono considerati come input. Se si specifica un oggetto fileName nel JSON, solo il file/BLOB specificato viene considerato un input. Visualizzare i file di esempio nel [esercitazione][adf-tutorial] per gli esempi.
 
	Se non è stato specificato **fileName** per una **tabella di output**, i file generati in **folderPath** vengono denominati con il seguente formato: Data.<Guid>.txt (ad esempio: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

	Per impostare **folderPath** e **fileName** in modo dinamico in base al **SliceStart** tempo, utilizzare la proprietà partitionedBy. Nell'esempio seguente folderPath usa Year, Month e Day dall'oggetto SliceStart (ora di inizio della sezione elaborata), mentre fileName usa Hour dall'oggetto SliceStart. Ad esempio, se una sezione viene generata per 2014-10-20T08:00:00, folderName è impostato su wikidatagateway/wikisampledataout/2014/10/20 e fileName è impostato su 08.csv.

	  	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
        "fileName": "{Hour}.csv",
        "partitionedBy": 
        [
        	{ "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
            { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
            { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
            { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
        ],

 

	Vedere [JSON Scripting Reference][json-script-reference] per informazioni dettagliate sulle proprietà JSON.

2.	Fare clic su **Distribuisci** sulla barra dei comandi per distribuire il set di dati (tabella è un set di dati rettangolare). Verificare che sia visualizzato un messaggio sulla barra del titolo indica **tabella distribuito correttamente**.
  

## Passaggio 4: Creare ed eseguire una pipeline
In questo passaggio verrà creata una **pipeline** con uno **attività Copia** che utilizza **EmpOnPremSQLTable** come input e **OutputBlobTable** come output.

1.	Fare clic su **nuova pipeline** sulla barra dei comandi. Se il pulsante non è visualizzato, fare clic su **... (puntini di sospensione)** Per espandere la barra dei comandi.
2.	Sostituire il JSON nel riquadro di destra con il testo seguente:   


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
 
	- Nella sezione attività esiste solo attività di cui **tipo** è impostato su **CopyActivity**.
	- **Input** per l'attività è impostata su **EmpOnPremSQLTable** e **output** per l'attività è impostata su **OutputBlobTable**.
	- Nel **trasformazione** sezione **SqlSource** è specificato come il **tipo di origine** e **BlobSink **è specificato come il **sink tipo**. - Query SQL **Selezionare * da emp** specificato per il **sqlReaderQuery** proprietà di **SqlSource**.

	> [AZURE.NOTE]Sostituire il valore del **avviare** proprietà con il giorno corrente e **end** valore con il giorno successivo. Avviare entrambi e date e ore di fine deve essere [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Ad esempio: 2014-10-14T16:32:41Z. Il **end** ora è facoltativo, ma si utilizzerà in questa esercitazione. Se non si specifica il valore per il **end** proprietà, viene calcolato come "* * inizio + 48 ore * *". Per eseguire la pipeline a tempo indeterminato, specificare **9/9/9999** come valore per il **end** proprietà. Si definisce l'intervallo di tempo in cui verranno elaborati gli intervalli di dati in base alla **disponibilità** proprietà che sono state definite per ogni tabella Factory di dati di Azure. Nell'esempio precedente sono visualizzate 24 sezioni di dati perché viene generata una sezione di dati ogni ora.
	
2. Fare clic su **Distribuisci** sulla barra dei comandi per distribuire il set di dati (tabella è un set di dati rettangolare). Verificare che sia visualizzato un messaggio sulla barra del titolo indica **PIPELINE distribuito correttamente**.
5. A questo punto, chiudere il **Editor** blade facendo clic su **X**. Fare clic su **X** per chiudere il blade ADFTutorialDataFactory con la visualizzazione della barra degli strumenti e struttura ad albero. Se viene visualizzato **verranno annullate le modifiche non salvate** dei messaggi, fare clic su **OK**.
6. Dovrebbe essere verso il **dati FACTORY** blade per il **ADFTutorialOnPremDF**.

**Congratulazioni**. Data factory di Azure, i servizi collegati, le tabelle e una pipeline sono stati creati correttamente e la pipeline è stata pianificata.

### Visualizzare la factory di dati in una visualizzazione Diagramma 
1. Nel **portale Azure Preview**, fare clic su **diagramma** riquadro nella home page per il **ADFTutorialOnPremDF** factory di dati.:

	![Collegamento di diagramma][image-data-factory-diagram-link]

2. Viene visualizzato un diagramma simile al seguente:

	![Vista diagramma][image-data-factory-diagram-view]

	È possibile eseguire lo zoom avanti, eseguire lo zoom indietro, zoom al 100%, zoom per adattarsi, automaticamente posizionare pipeline e le tabelle e visualizzare le informazioni di derivazione (evidenzia gli elementi padre e figlio degli elementi selezionati). È possibile double blick su un oggetto (tabella di input/output o pipeline) per visualizzare le proprietà per esso.

## Passaggio 5: Monitorare i set di dati e delle pipeline
In questo passaggio viene usato il portale di Azure per monitorare le attività in un'istanza di Data factory di Azure. È anche possibile usare i cmdlet di PowerShell per monitorare i set di dati e le pipeline. Per ulteriori informazioni sull'utilizzo dei cmdlet per il monitoraggio, vedere [monitoraggio e gestione di Azure dati Factory tramite PowerShell][monitor-manage-powershell].

1. Passare a **portale Azure Preview** (se già chiusa)
2. Se il blade per **ADFTutorialOnPremDF** non è aperto, aprirlo scegliendo **ADFTutorialOnPremDF** sul **occhio**.
3. Verrà visualizzato il **conteggio** e **nomi** delle tabelle e delle pipeline creato in questo pannello.

	![Dati Factory Home Page][image-data-factory-homepage-2]
4. Fare clic su **set di dati** riquadro.
5. Nel **set di dati** blade, scegliere il **EmpOnPremSQLTable**.

	![Sezioni EmpOnPremSQLTable][image-data-factory-onprem-sqltable-slices]

6. Si noti che gli intervalli di dati fino all'ora corrente sono già stati prodotti e sono **Pronto**. Ciò è dovuto al fatto che i dati sono stati inseriti nel database SQL Server database che è sempre presente. Verificare che nessun sezioni vengono visualizzate nel **sezioni problema** nella parte inferiore.


	Entrambi **aggiornato di recente sezioni** e **Impossibile recentemente sezioni** gli elenchi vengono ordinati in base la **ora ultimo aggiornamento**. I tempi di aggiornamento di una sezione viene modificato nelle situazioni seguenti.
    

	-  È aggiornare lo stato della sezione manualmente, ad esempio, utilizzando il **Set AzureDataFactorySliceStatus** (o) facendo clic su **eseguire** sul **sezione** blade della sezione.
	-  La sezione Cambia stato a causa di un'esecuzione (ad esempio, una sequenza di avvio, un'esecuzione terminata e non è riuscita, un'esecuzione terminata e ha avuto esito positivo, ecc).
 
	Fare clic sul titolo degli elenchi o **... (puntini di sospensione)** Per visualizzare l'elenco di intervalli di dimensioni maggiore. Fare clic su **filtro** sulla barra degli strumenti per filtrare le sezioni.
	
	Per visualizzare le sezioni di dati ordinati in base alle ore di inizio e fine sezione invece, fare clic su **sezioni di dati (per ora slice)** riquadro.

7. In questo punto, il **set di dati** blade, fare clic su **OutputBlobTable**.

	![Sezioni OputputBlobTable][image-data-factory-output-blobtable-slices]
8. Verificare che le sezioni fino all'ora corrente vengono create e **Pronto**. Attendere finché non viene impostato lo stato delle sezioni fino all'ora corrente **Pronto**.
9. Fare clic su una sezione di dati dall'elenco e verrà visualizzato il **sezione di dati** blade.

	![Blade porzione di dati][image-data-factory-dataslice-blade]

	Se non è inclusa nella sezione il **Pronto** stato, è possibile visualizzare le sezioni a monte che non si è pronti e bloccano l'intervallo corrente da eseguito nel **sezioni a monte che non sono pronte** elenco.

10. Fare clic sui **attività eseguire** dall'elenco nella parte inferiore per visualizzare **dettagli di esecuzione di attività**.

	![Blade Dettagli esecuzione di attività][image-data-factory-activity-run-details]

11. Fare clic su **X** per chiudere tutti i server blade fino a tornare alla home blade per il **ADFTutorialOnPremDF**.
14. (facoltativo) Fare clic su **pipeline**, fare clic su **ADFTutorialOnPremDF**, e il drill-through tabelle di input (* * utilizzato * *) o tabelle di output (* * prodotte * *).
15. Utilizzare strumenti come **Esplora archivi Azure** per verificare l'output.

	![Esplora archivi Azure][image-data-factory-stroage-explorer]


## Creazione e registrazione di un gateway con Azure PowerShell 
Questa sezione descrive come creare e registrare un gateway con i cmdlet di Azure PowerShell.

1. Avviare **Azure PowerShell** in modalità amministratore. 
2. I cmdlet di Azure dati Factory sono disponibili nel **AzureResourceManager** modalità. Eseguire il comando seguente per passare al **AzureResourceManager** modalità.     

        switch-azuremode AzureResourceManager


2. Utilizzare il **Nuovo AzureDataFactoryGateway** cmdlet per creare un gateway logico come segue:

		New-AzureDataFactoryGateway -Name <gatewayName> -DataFactoryName <dataFactoryName> -ResourceGroupName ADF –Description <desc>

	**Comando di esempio e di output**:


		PS C:> New-AzureDataFactoryGateway -Name MyGateway -DataFactoryName $df -ResourceGroupName ADF –Description “gateway for walkthrough”

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


3. Utilizzare il **Nuovo AzureDataFactoryGatewayKey** cmdlet per generare una chiave di registrazione per il gateway appena creato e archiviare la chiave in una variabile locale **$Key**:

		New-AzureDataFactoryGatewayKey -GatewayName <gatewayname> -ResourceGroupName ADF -DataFactoryName <dataFactoryName>

	
	**Output del comando di esempio:**


		PS C:> $Key = New-AzureDataFactoryGatewayKey -GatewayName MyGateway -ResourceGroupName ADF -DataFactoryName $df 

	
4. In Azure PowerShell, passare alla cartella: **C:\Program Files\Microsoft Data Management Gateway\1.0\PowerShellScript** ed eseguire **RegisterGateway.ps1** script associato alla variabile locale **$Key** come illustrato nel comando seguente per registrare l'agente client installato nel computer con il gateway logico create in precedenza.

		PS C:> .\RegisterGateway.ps1 $Key.GatewayKey
		
		Agent registration is successful!

5. È possibile utilizzare il **Get AzureDataFactoryGateway** per ottenere l'elenco di gateway nell'ambiente di produzione di dati. Quando il **stato** Mostra **online**, significa che è possibile utilizzare il gateway.

		Get-AzureDataFactoryGateway -DataFactoryName <dataFactoryName> -ResourceGroupName ADF

È possibile rimuovere un gateway tramite il **Remove AzureDataFactoryGateway** cmdlet e aggiornamento della descrizione per un gateway utilizzando il **AzureDataFactoryGateway Set** cmdlet. Per la sintassi e altri dettagli relativi a questi cmdlet, vedere Riferimento ai cmdlet di Data factory.



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

<!---HONumber=GIT-SubDir-->