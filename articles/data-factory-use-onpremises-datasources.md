<properties title="Enable your pipelines to work with on-premises data" pageTitle="Consentire alle pipeline di usare dati locali | Data factory di Azure" description="Learn how to register an on-premises data source with an Azure data factory and copy data to/from the data source." metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="spelluru" />

# Consentire alle pipeline di usare dati locali

Per consentire alle pipeline in Data factory di Azure di usare un'origine dati locale, è necessario aggiungere l'origine dati locale come servizio collegato a un'istanza di Data factory usando il portale di gestione di Azure o Azure PowerShell.
 
Per aggiungere un'origine dati locale come servizio collegato a un'istanza di Data factory, è necessario scaricare e installare prima il Gateway di gestione dati di Microsoft in un computer locale e configurare il servizio collegato per l'origine dati locale per poter usare il gateway.
 
> [WACOM.NOTE] Attualmente, l'origine dati locale supportata è solo SQL Server.

## <a href="DMG"></a> Gateway di gestione dati

Il **Gateway di gestione dati** è un software che connette le origini dati locali ai servizi cloud in modo sicuro e gestito. Con il Gateway di gestione dati, è possibile

- **Connettersi ai dati locali per l'accesso ai dati ibrido**: è possibile connettere i dati locali ai servizi cloud per sfruttare i vantaggi dei servizi cloud, mantenendo allo stesso tempo attiva la società con i dati locali.
- **Definire un proxy di dati sicuro**: è possibile definire quali origini dati locali esporre con il Gateway di gestione dati in modo che il gateway autentichi la richiesta di dati dai servizi cloud e protegga le origini dati locali.
- **Gestire il gateway per una governance completa**: vengono fornite funzionalità complete di monitoraggio e registrazione di tutte le attività all'interno del Gateway di gestione dati per la gestione e la governance.
- **Spostare i dati in modo efficace**: i dati vengono trasferiti in parallelo e sono resilienti ai problemi di rete intermittente grazie alla logica di ripetizione dei tentativi automatica.


Il Gateway di gestione dati ha una gamma completa di funzionalità di connessione dei dati locali che include:

- **Non invasività per il firewall aziendale**: il Gateway di gestione dati funziona solo dopo l'installazione senza dover aprire una connessione al firewall o richiedere modifiche invasive all'infrastruttura di rete aziendale.
- **Crittografia delle credenziali con il certificato**: le credenziali usate per connettersi alle origini dati sono crittografate con un certificato di totale proprietà dell'utente. Senza il certificato, nessuno può decrittografare le credenziali in testo normale, neanche Microsoft.

### Installazione del gateway - Procedure consigliate

1.	Se il computer host entra in stato di ibernazione, il gateway non è in grado di rispondere alla richiesta di dati. Quindi, configurare una **combinazione per il risparmio di energia** appropriata nel computer prima di installare il gateway. 
2.	Il Gateway di gestione dati deve poter connettersi alle origini dati locali registrate con il servizio Data factory di Azure. Non deve trovarsi nello stesso computer dell'origine dati, ma la **prossimità all'origine dati** riduce il tempo di connessione tra il gateway e l'origine dati.

### Considerazioni per l'uso del Gateway di gestione dati
1.	Un'unica istanza del Gateway di gestione dati può essere usata per più origini dati locali. Tenere presente, però, che un gateway **è associato a un'istanza di Data factory di Azure** e non può essere condiviso con altre istanze di Data factory.
2.	Nel computer può essere installata una **sola istanza del Gateway di gestione dati**. Si supponga di avere due istanze di Data factory che richiedono l'accesso alle origini dati locali: è necessario installare i gateway nei due computer locali in cui ogni gateway sia associato a un'istanza separata di Data factory.
3.	Se un gateway è già installato nel computer per uno scenario **Power BI**, installare un **gateway separato per Data factory di Azure** in un altro computer. 
 

## Procedura dettagliata

In questa procedura dettagliata viene creata un'istanza di Data factory con una pipeline che sposta i dati da un database SQL Server locale a un BLOB di Azure. 

### Passaggio 1: Creare un'istanza di Data factory di Azure
In questo passaggio viene usato il portale di gestione di Azure per creare un'istanza di Data factory di Azure denominata **ADFTutorialOnPremDF**. È anche possibile creare un'istanza di Data factory con i cmdlet di Data factory di Azure. 

1.	Dopo la registrazione al [portale di anteprima di Azure][
2.	azure-preview-portal], fare clic su **NUOVO** dall'angolo in basso a sinistra, quindi selezionare **Data factory** nel pannello **Nuovo**.

	![New->DataFactory][image-data-factory-new-datafactory-menu] 
	
	Se **Data factory** non è visualizzato nel pannello **Nuovo**, scorrere verso il basso.  
6. Nel pannello **Nuova istanza di Data factory**:
	1. Immettere **ADFTutorialOnPremDF** per il **nome**.
	2. Fare clic su **NOME GRUPPO DI RISORSE** e selezionare **ADFTutorialResourceGroup** (se l'esercitazione è stata completata da [Introduzione a Data factory di Azure][adfgetstarted]. È possibile selezionare un gruppo di risorse esistente o crearne uno nuovo. Per creare un nuovo gruppo di risorse:
		1. Fare clic su **Crea un nuovo gruppo di risorse**.
		2. Nel pannello **Crea gruppo di risorse**, immettere un **nome** per il gruppo di risorse, quindi fare clic su **OK**.

7. **Aggiungi alla Schermata iniziale** è selezionato nel pannello **Nuova istanza di Data factory**.

	![Add to Startboard][image-data-factory-add-to-startboard]

8. Nel pannello **Nuova istanza di Data factory** fare clic su **Crea**.
9. Cercare le notifiche dal processo di creazione nell'hub **NOTIFICHE** sulla sinistra.

	![NOTIFICATIONS hub][image-data-factory-notifications-hub]

11. Dopo aver completato la creazione, viene visualizzato il pannello di Data factory come mostrato di seguito:

	![Data Factory Home Page][image-data-factory-datafactory-homepage]

12. Viene visualizzato anche nella **Schermata iniziale** come mostrato di seguito. Fare clic per aprire il **pannello di Data factory** se non è già aperto.

	![Startboard][image-data-factory-startboard]

### Passaggio 2: Creare servizi collegati 
In questo passaggio vengono creati due servizi collegati: **MyBlobStore** e **OnPremSqlLinkedService**. **OnPremSqlLinkedService** collega un database SQL Server locale e il servizio collegato **MyBlobStore** collega un archivio BLOB di Azure a **ADFTutorialDataFactory**. Più avanti nella procedura dettagliata verrà creata una pipeline che copia i dati dal database SQL Server locale all'archivio BLOB di Azure. 

### Aggiungere un servizio collegato a un database SQL Server locale
1.	Nel pannello **Data factory** per **ADFTutorialOnPremDF**, fare clic su **Servizi collegati**. 

	![Data Factory Home Page][image-data-factory-home-age]

2.	Nel pannello **Servizi collegati** fare clic su **+ Gateway dati**.

	![Linked Services - Add a Gateway button][image-data-factory-linkedservices-add-gateway-button]

2. Nel pannello **Crea** immettere **adftutorialgateway** per il **nome** e fare clic su **OK**. 	

	![Create Gateway blade][image-data-factory-create-gateway-blade]

3. Nel pannello **Configura** fare clic su **Installa direttamente nel computer**. Viene scaricato il pacchetto di installazione per il gateway, che viene installato, configurato e registrato nel computer.

	![Gateway - Configure blade][image-data-factory-gateway-configure-blade]

	Si tratta del metodo più semplice (con un clic) di scaricare, installare, configurare e registrare il gateway in un unico passaggio. È possibile vedere l'applicazione **Gateway di gestione dati di Microsoft Configuration Manager** installata nel computer. È anche possibile trovare l'eseguibile **ConfigManager.exe** nella cartella: **C:\Programmi\Microsoft Data Management Gateway\1.0\Shared**.

	È anche possibile scaricare e installare manualmente il gateway usando i collegamenti nel pannello e registrarlo usando il tasto visualizzato nella casella di testo **REGISTRA CON TASTO**.
	
	Vedere la sezione [Gateway di gestione dati](#DMG) per i dettagli sul gateway, incluse le procedure consigliate e altre considerazioni importanti. 

4. Fare clic su **OK** nel pannello **Nuovo gateway dati**.
5. Avviare l'applicazione **Gateway di gestione dati di Microsoft Configuration Manager**   nel computer.

	![Gateway Configuration Manager][image-data-factory-gateway-configuration-manager]

6. Attendere finché i valori non sono impostati come segue:
	1. Se lo **stato** del servizio non è impostato su **Avviato**, fare clic su **Avvia servizio** per avviare il servizio e attendere un minuto per consentire l'aggiornamento degli altri campi.
	2. **Nome gateway** è impostato su **adftutorialgateway**.
	3. **Nome istanza** è impostato su **adftutorialgateway**.
	4. **Lo stato della chiave del gateway** è impostato su **registrato**.
	5. La barra di stato visualizza **Connesso al servizio cloud del Gateway di gestione dati** insieme a un **segno di spunta verde**.  

7. Nel pannello **Servizi collegati** confermare che lo **stato** del gateway è **Buono** e fare clic su **+ archivio dati**. Potrebbe essere necessario chiudere e riaprire il pannello per aggiornarlo. 

	![Add Data Store button][image-data-factory-add-datastore-SQL-button]

8. Nel pannello **Nuovo archivio dati** immettere **OnPremSqlLinkedService** come **nome**.
9. Fare clic su **TIPO (Impostazioni obbligatorie)** e selezionare **SQL Server**. 
10. Nel pannello **Nuovo archivio dati** fare clic su **GATEWAY DATI (Configurare le impostazioni necessarie)**.

	![Data Gateway Configure link][image-data-factory-gateway-configure-link]
  
11. Selezionare **adftutorialgateway** creato in precedenza. 
12.	Nel pannello **Nuovo archivio dati**, fare clic su **CREDENZIALI** per visualizzare il pannello **Credenziali**.

	![Data Source Credentials Blade][image-data-factory-credentials-blade]

13.	Nel pannello **Credenziali** fare clic su **Fare clic qui per impostare le credenziali in modo sicuro**. Viene avviata la seguente finestra di dialogo popup.

	![One Click application install][image-data-factory-oneclick-install]

14. Fare clic su **Esegui** per installare l'applicazione **Gestione credenziali** e visualizzare la finestra di dialogo Impostazione credenziali. 
15.	Nella finestra di dialogo **Impostazione credenziali** immettere il **nome utente** e la **password** che il servizio può usare per accedere al database SQL Server locale, quindi fare clic su **OK**. In questa finestra di dialogo è supportata solo l'**autenticazione SQL**. Attendere che la finestra di dialogo si chiuda.
16.	Fare clic su **OK** nel pannello **Credenziali**, quindi fare clic su **OK** nel pannello **Nuovo archivio dati**. Viene visualizzato il servizio collegato **OnPremSqlLinkedService** aggiunto al pannello Servizi collegati.

	![Linked Services Blade with OnPrem Store][image-data-factory-linkedservices-blade-onprem]

	
   


#### Aggiungere un servizio collegato per un account di archiviazione di Azure

1.	Nel pannello **Data factory**, fare clic sul riquadro **Servizi collegati** per avviare il pannello **Servizi collegati**.
2. Nel pannello **Servizi collegati** fare clic su **Aggiungi archivio dati**.	
3. Nel pannello **Nuovo archivio dati**:
	1. Immettere un **nome** per l'archivio dati. Ai fini dell'esercitazione, immettere **MyBlobStore** per il **nome**.
	2. Immettere la **descrizione (facoltativa)** per l'archivio dati.
	3. Fare clic su **Tipo**.
	4. Selezionare **Account di archiviazione di Azure** e fare clic su **OK**.
		
		![Azure Storage Account][image-data-factory-azure-storage-account]

4. A questo punto viene visualizzato di nuovo il pannello **Nuovo archivio dati**, simile a questo riportato di seguito:

	![Azure Storage settings][image-data-factory-azure-storage-settings]

5. Immettere il **Nome account** e la **Chiave account** per l'account di archiviazione di Azure, quindi fare clic su **OK**.

  
6. Dopo aver fatto clic su **OK** nel pannello **Nuovo archivio dati** viene visualizzato **MyBlobStore** nell'elenco di **archivi dati** nel pannello **Servizi collegati**. Controllare l'hub **NOTIFICHE** sulla sinistra per eventuali messaggi.

	![Linked Services blade with MyBlobStore][image-data-factory-linkedservices-with-storage]

 
## Passaggio 3: Creare set di dati di input e di output
In questo passaggio vengono creati i set di dati di input e di output che rappresentano i dati di input e di output per l'operazione di copia (database SQL Server locale => archiviazione BLOB di Azure). La creazione di set di dati e pipeline non è ancora supportata dal portale di Azure, quindi verranno usati i cmdlet di Azure PowerShell per creare tabelle e pipeline. Prima di creare i set di dati o le tabelle (set di dati rettangolari), è necessario eseguire quanto segue (i passaggi dettagliati seguono l'elenco):

- Creare una tabella denominata **emp** nel database SQL Server aggiunto come servizio collegato all'istanza di Data factory e inserire una coppia di voci di esempio nella tabella.
- - Se non è stata completata l'esercitazione nell'articolo [Introduzione a Data factory di Azure][adfgetstarted], creare un contenitore BLOB denominato **adftutorial** nell'account di archiviazione BLOB di Azure aggiunto come servizio collegato all'istanza di Data factory.

### Preparare SQL Server locale per l'esercitazione

1. Nel database specificato per il servizio collegato di SQL Server locale (**OnPremSqlLinkedService**), usare il seguente script SQL per creare la tabella **emp** nel database.


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



### Creare la tabella di input

1.	Creare un file JSON per una tabella di Data factory che rappresenta i dati dalla tabella **emp** nel database SQL Server. Avviare il **Blocco note**, copiare il seguente script JSON e salvarlo come **EmpOnPremSQLTable.json** nella cartella C:\ADFGetStarted\**OnPrem**. Creare la sottocartella **OnPrem** nella cartella **C:\ADFGetStarted** se non esiste. 


        {
    		"name": "EmpOnPremSQLTable",
    		"properties":
    		{
        		"location":
        		{
            		"type": "OnPremisesSqlServerTableLocation",
            		"tableName": "emp",
            		"linkedServiceName": "OnPremSqlLinkedService"
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
	- **tableName** è impostato su **emp**.
	- **linkedServiceName** è impostato su **OnPremSqlLinkedService** (questo servizio collegato è stato creato nel passaggio 2).
	- Per una tabella di input non generata da un'altra pipeline in Data factory di Azure, è necessario specificare la sezione **waitOnExternal** nel JSON. Indica che i dati di input sono generati al di fuori del servizio Data factory di Azure.   

	Vedere il [riferimento agli script JSON][json-script-reference] per dettagli sulle proprietà JSON.

2. I cmdlet di Data factory di Azure sono disponibili in modalità **AzureResourceManager**. Avviare **Azure PowerShell** ed eseguire il seguente comando per passare in modalità **AzureResourceManager**.     

        switch-azuremode AzureResourceManager

	Scaricare [Azure PowerShell][azure-powershell-install] se non è già installato nel computer.
3. Usare il cmdlet **New-AzureDataFactoryTable** per creare la tabella di input con il file **EmpOnPremSQLTable.json**.

		New-AzureDataFactoryTable -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialOnPremDF -File C:\ADFGetStarted\OnPrem\EmpOnPremSQLTable.json  
	
	Aggiornare il comando se si usa un gruppo di risorse diverso.

### Creare la tabella di output

1.	Creare un file JSON per una tabella di Data factory da usare come output per la pipeline che verrà creata nel passaggio successivo. Avviare il Blocco note, copiare il seguente script JSON e salvarlo come **OutputBlobTable.json** nella cartella **C:\ADFGetStarted\OnPrem**.

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
            		"linkedServiceName": "MyBlobStore"
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
	- **linkedServiceName** è impostato su **MyBlobStore** (questo servizio collegato è stato creato nel passaggio 2).
	- **folderPath** è impostato su **adftutorial/outfromonpremdf** dove outfromonpremdf è la cartella nel contenitore adftutorial. È necessario creare solo il contenitore **adftutorial**.
	- L'oggetto **availability** è impostato su un **valore orario** (**frequency** impostato su **Hour** e **interval** impostato su **1**).  Il servizio Data factory genera una sezione di dati di output ogni ora nella tabella **emp** nel database SQL di Azure. 

	Se non si specifica un oggetto **fileName** per una **tabella di input**, tutti i file/BLOB della cartella di input (**folderPath**) vengono considerati input. Se si specifica un oggetto fileName nel JSON, solo il file/BLOB specificato viene considerato un input. Vedere i file di esempio nell'[esercitazione][adf-tutorial] per gli esempi.
 
	Se non è stato specificato **fileName** per una **tabella di output**, i file generati in **folderPath** vengono denominati con il seguente formato: Data.<Guid>.txt (ad esempio:Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

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

2.	In **Azure PowerShell**, eseguire il seguente comando per creare un'altra tabella di Data factory per rappresentare la tabella nel database SQL di Azure.

		New-AzureDataFactoryTable -DataFactoryName ADFTutorialOnPremDF -File C:\ADFGetStarted\OnPrem\OutputBlobTable.json -ResourceGroupName ADFTutorialResourceGroup  

## Passaggio 4: Creare ed eseguire una pipeline
In questo passaggio viene creata una **pipeline** con un'**attività di copia** che usa **EmpOnPremSQLTable** come input e **OutputBlobTable** come output.

1.	Creare un file JSON per la pipeline. Avviare il Blocco note, copiare il seguente script JSON e salvarlo come **ADFTutorialPipelineOnPrem.json** nella cartella **C:\ADFGetStarted\OnPrem**.
	 

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
	        	]
			}
		}

	Tenere presente quanto segue:
 
	- Nella sezione delle attività esiste una sola attività con l'oggetto **type** impostato su **CopyActivity**.
	- **Input** per l'attività è impostato su **EmpOnPremSQLTable** e **output** per l'attività è impostato su **OutputBlobTable**.
	- Nella sezione **transformation** **SqlSource** viene specificato come **tipo di origine** e **BlobSink **come **tipo di sink**.
	- La query SQL **select * from emp** viene specificata per la proprietà **sqlReaderQuery** di **SqlSource**.

2. Usare il cmdlet **New-AzureDataFactoryPipeline** per creare una pipeline con il file **ADFTutorialPipeline.json** creato.

		New-AzureDataFactoryPipeline  -DataFactoryName ADFTutorialOnPremDF -File C:\ADFGetStarted\OnPrem\ADFTutorialPipelineOnPrem.json -ResourceGroupName ADFTutorialResourceGroup  

3. Dopo aver creato la pipeline è possibile specificare la durata dell'elaborazione dati. Specificando il periodo attivo per una pipeline si definisce la durata dell'elaborazione delle sezioni di dati in base alle proprietà di **disponibilità** definite per ciascuna tabella di Data factory di Azure.  Eseguire il seguente comando PowerShell per impostare il periodo attivo nella pipeline e immettere S per confermare. 


		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialOnPremDF -StartDateTime 2014-09-29 -EndDateTime 2014-09-30 -Name ADFTutorialPipelineOnPrem  

	> [WACOM.NOTE] Sostituire il valore di **StartDateTime** con il giorno corrente e il valore di **EndDateTime** con il giorno seguente. StartDateTime ed EndDateTime sono orari UTC e devono essere in formato [ISO](http://en.wikipedia.org/wiki/ISO_8601). Ad esempio: 2014-10-14T16:32:41Z. EndDateTime è facoltativo, ma verrà usato in questa esercitazione.
	> Se non si specifica **EndDateTime**, viene calcolato come "**StartDateTime + 48 ore**". Per eseguire la pipeline illimitatamente, specificare **9/9/9999** come **EndDateTime**.

	Nell'esempio precedente sono visualizzate 24 sezioni di dati perché viene generata una sezione di dati ogni ora.
4. Nel **portale di anteprima di Azure**, fare clic sul riquadro **Diagramma** nella home page per l'istanza di Data factory **ADFTutorialOnPremDF**. :

	![Diagram Link][image-data-factory-diagram-link]

5. Viene visualizzato un diagramma simile al seguente:

	![Diagram View][image-data-factory-diagram-view]

	**Congratulazioni.** Data factory di Azure, i servizi collegati, le tabelle e una  	pipeline sono stati creati correttamente e il flusso di lavoro è stato avviato.

## Passaggio 5: Monitorare i set di dati e le pipeline
In questo passaggio viene usato il portale di Azure per monitorare le attività in un'istanza di Data factory di Azure. È anche possibile usare i cmdlet di PowerShell per monitorare i set di dati e le pipeline. Per dettagli sull'uso dei cmdlet per il monitoraggio, vedere [Monitorare e gestire Data factory di Azure con Azure PowerShell][monitor-manage-powershell].

1. Passare al **portale di anteprima di Azure** (se è stato chiuso)
2. Se il pannello per **ADFTutorialOnPremDF** non è aperto, aprirlo facendo clic su **ADFTutorialOnPremDF** nella **Schermata iniziale**.
3. Viene visualizzato il **numero** e i **nomi** delle tabelle e delle pipeline create nel pannello.

	![Data Factory Home Page][image-data-factory-homepage-2]
4. A questo punto fare clic sul riquadro **Set di dati**.
5. Nel pannello **Set di dati**, fare clic su **EmpOnPremSQLTable**.

	![EmpOnPremSQLTable slices][image-data-factory-onprem-sqltable-slices]

6. Le sezioni di dati fino all'ora corrente sono state già generate e sono in stato **Pronto**. Ciò è dovuto al fatto che i dati sono stati inseriti nel database SQL Server database che è sempre presente. Controllare che non ci sono sezioni visualizzate nella sezione **Sezioni problematiche** nella parte inferiore della pagina.
7. A questo punto, nel pannello **Set di dati**, fare clic su **OutputBlobTable**.

	![OputputBlobTable slices][image-data-factory-output-blobtable-slices]
8. Controllare che le sezioni fino all'ora corrente sono state generate e sono in stato **Pronto**.
9. Fare clic su una qualsiasi sezione di dati dell'elenco per visualizzare il pannello **SEZIONE DI DATI**.

	![Data Slice Blade][image-data-factory-dataslice-blade]
10. Fare clic sull'**esecuzione attività** dall'elenco nella parte inferiore della pagina per visualizzare i **dettagli dell'esecuzione attività**.

	![Activity Run Details blade][image-data-factory-activity-run-details]

11. Fare clic su **X** per chiudere tutti i pannelli finché non viene visualizzato il pannello iniziale per **ADFTutorialOnPremDF**.
14. (Facoltativo) Fare clic su **Pipeline**, **ADFTutorialOnPremDF**, quindi eseguire il drill-through delle tabelle di input (**usate**) o delle tabelle di output (**generate**).
15. Usare strumenti come **Azure Storage Explorer** per verificare l'output.

	![Azure Storage Explorer][image-data-factory-stroage-explorer]


## Creazione e registrazione di un gateway con i cmdlet di Azure PowerShell
Questa sezione descrive come creare e registrare un gateway con i cmdlet di Azure PowerShell. 

1. Avviare **Azure PowerShell** in modalità di amministrazione. 
2. I cmdlet di Data factory di Azure sono disponibili in modalità **AzureResourceManager**. Eseguire il seguente comando per passare in modalità **AzureResourceManager**.     

        switch-azuremode AzureResourceManager


2. Usare il cmdlet **New-AzureDataFactoryGateway** per creare un gateway logico come illustrato di seguito:

		New-AzureDataFactoryGateway -Name <gatewayName> -DataFactoryName $df -Location "West US" -ResourceGroupName ADF -Description <desc>

	**Comando di esempio e output**:


		PS C:\> New-AzureDataFactoryGateway -Name MyGateway -DataFactoryName $df -Location "West US" -ResourceGroupName ADF -Description "gateway for walkthrough"

		Name            : MyGateway
		Location        : West US
		Description     : gateway for walkthrough
		Version         :
		Status          : NeedRegistration
		VersionStatus   : None
		CreateTime      : 9/28/2014 10:58:22
		RegisterTime    :
		LastConnectTime :
		ExpiryTime      :


3. Usare il cmdlet **New-AzureDataFactoryGatewayKey** per generare una chiave di registrazione per il gateway appena creato e archiviare la chiave in una variabile locale **$Key**:

		New-AzureDataFactoryGatewayKey -GatewayName <gatewayname> -ResourceGroupName ADF -DataFactoryName $df 

	
	**Output del comando di esempio:**


		PS C:\> $Key = New-AzureDataFactoryGatewayKey -GatewayName MyGateway -ResourceGroupName ADF -DataFactoryName $df 

	
4. In Azure PowerShell, passare alla cartella: **C:\Programmi\Microsoft Data Management Gateway\1.0\PowerShellScript\** ed eseguire lo script **RegisterGateway.ps1** associato alla variabile locale **$Key** come mostrato nel seguente comando per registrare l'agente client installato nel computer con il gateway logico creato in precedenza.

		PS C:\> .\RegisterGateway.ps1 $Key.GatewayKey
		
		Agent registration is successful!

5. È possibile usare il cmdlet **Get-AzureDataFactoryGateway** per ottenere l'elenco di gateway nell'istanza di Data factory. Quando lo **stato** visualizza **online**, il gateway è pronto per essere usato.

		Get-AzureDataFactoryGateway -DataFactoryName $df -ResourceGroupName ADF

È possibile rimuovere un gateway con il cmdlet **Remove-AzureDataFactoryGateway** e aggiornare la descrizione per un gateway usando il cmdlet **Set-AzureDataFactoryGateway**. Per la sintassi e altri dettagli relativi a questi cmdlet, vedere Riferimento ai cmdlet di Data factory.  




## Vedere anche

Articolo | Descrizione
------ | ---------------
[Introduzione a Data factory di Azure][adf-getstarted] | Questo articolo fornisce un'esercitazione end-to-end che mostra come creare un'istanza di Data factory di Azure di esempio che copia i dati da un BLOB di Azure a un database SQL di Azure.
[Usare Pig e Hive con Data factory][use-pig-and-hive-with-data-factory] | Questo articolo contiene una procedura dettagliata che mostra come usare l'attività HDInsight per eseguire uno script hive/pig per elaborare i dati di input in modo da generare i dati di output. 
[Esercitazione: Spostare ed elaborare i file di log con Data factory][adf-tutorial] | Questo articolo fornisce una procedura dettagliata end-to-end che mostra come implementare uno scenario quasi reale usando Data factory di Azure per trasformare i dati da file di log a informazioni accurate.
[Usare attività personalizzate in un'istanza di Data factory][use-custom-activities] | Questo articolo fornisce una procedura dettagliata con le istruzioni precise per creare un'attività personalizzata e usarla in una pipeline. 
[Risolvere i problemi di Data factory][troubleshoot] | Questo articolo descrive come risolvere i problemi di Data factory di Azure.  
[Guida di riferimento per gli sviluppatori di Data factory di Azure][developer-reference] | La guida di riferimento per gli sviluppatori comprende informazioni di riferimento complete per cmdlet, script JSON, funzioni e così via. 



[monitor-manage-using-powershell]: ../data-factory-monitor-manage-using-powershell
[adf-getstarted]: ../data-factory-get-started
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[use-pig-and-hive-with-data-factory]: ../data-factory-pig-hive-activities
[troubleshoot]: ../data-factory-troubleshoot
[data-factory-introduction]: ../data-factory-introduction

[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[64bit-download-link]: http://go.microsoft.com/fwlink/?LinkId=517623
[32bit-download-link]: http://go.microsoft.com/fwlink/?LinkId=517624

[azure-preview-portal]: http://portal.azure.com
[adfgetstarted]: ../data-factory-get-started
[monitor-manage-powershell]: ../data-factory-monitor-manage-using-powershell





[json-script-reference]: http://go.microsoft.com/fwlink/?LinkId=516971
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456



[azure-powershell-install]: http://azure.microsoft.com/it-it/documentation/articles/install-configure-powershell/


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

[image-data-factory-azure-storage-account]: ./media/data-factory-use-onpremises-datasources/OnPremAzureStorageAccount.png

[image-data-factory-azure-storage-settings]: ./media/data-factory-use-onpremises-datasources/OnPremAzureStorageSettings.png

[image-data-factory-get-storage-key]: ./media/data-factory-use-onpremises-datasources/OnPremGetStorageKey.png

[image-data-factory-linkedservices-with-storage]: ./media/data-factory-use-onpremises-datasources/OnPremLinkedServicesWithMyBlobStore.png

[image-data-factory-linkedservices-add-gateway-button]: ./media/data-factory-use-onpremises-datasources/OnPremLinkedServicesAddGaewayButton.png

[image-data-factory-create-gateway-blade]: ./media/data-factory-use-onpremises-datasources/OnPremCreateGatewayBlade.png

[image-data-factory-gateway-configure-blade]: ./media/data-factory-use-onpremises-datasources/OnPremGatewayConfigureBlade.png

[image-data-factory-gateway-configuration-manager]: ./media/data-factory-use-onpremises-datasources/OnPremDMGConfigurationManager.png

[image-data-factory-add-datastore-SQL-button]: ./media/data-factory-use-onpremises-datasources/OnPremLinkedServicesAddSQLDataStoreButton.png

[image-data-factory-gateway-configure-link]: ./media/data-factory-use-onpremises-datasources/OnPremNewDataStoreDataGatewayConfigureLink.png

[image-data-factory-credentials-blade]: ./media/data-factory-use-onpremises-datasources/OnPremCredentionlsBlade.png

[image-data-factory-oneclick-install]: ./media/data-factory-use-onpremises-datasources/OnPremOneClickInstall.png

[image-data-factory-diagram-link]: ./media/data-factory-use-onpremises-datasources/OnPremDiagramLink.png

[image-data-factory-diagram-view]: ./media/data-factory-use-onpremises-datasources/OnPremDiagramView.png

[image-data-factory-homepage-2]: ./media/data-factory-use-onpremises-datasources/OnPremDataFactoryHomePage2.png

[image-data-factory-stroage-explorer]: ./media/data-factory-use-onpremises-datasources/OnPremAzureStorageExplorer.png

[image-data-factory-home-age]: ./media/data-factory-use-onpremises-datasources/DataFactoryHomePage.png

[image-data-factory-linkedservices-blade-onprem]: ./media/data-factory-use-onpremises-datasources/LinkedServiceBladeWithOnPremSql.png

[image-data-factory-onprem-sqltable-slices]: ./media/data-factory-use-onpremises-datasources/OnPremSQLTableSlicesBlade.png

[image-data-factory-output-blobtable-slices]: ./media/data-factory-use-onpremises-datasources/OutputBlobTableSlicesBlade.png

[image-data-factory-dataslice-blade]: ./media/data-factory-use-onpremises-datasources/DataSlice.png

[image-data-factory-activity-run-details]: ./media/data-factory-use-onpremises-datasources/ActivityRunDetailsBlade.png

[image-data-factory-new-datafactory-menu]: ./media/data-factory-use-onpremises-datasources/NewDataFactoryMenu.png

[image-data-factory-preview-portal-storage-key]: ./media/data-factory-get-started/PreviewPortalStorageKey.png
