<properties 
	pageTitle="Processo di analisi scientifica dei dati Azure in esecuzione: utilizzo di SQL Sever | Azure" 
	description="Processo di analisi scientifica dei dati di Azure in esecuzione" 
	metaKeywords="" 
	services="machine-learning" 
	solutions="" 
	documentationCenter="" 
	authors="msolhab" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/19/2015" 
	ms.author="mohabib;fashah"/> 

                
# Processo di analisi scientifica dei dati di Azure in esecuzione: utilizzo di SQL Sever

In questa esercitazione, si seguirà la mappa completa del processo di analisi scientifica dei dati di Azure per creare e distribuire un modello tramite un set di dati disponibile pubblicamente, vale a dire il set di dati [Corse dei taxi di NYC](http://www.andresmh.com/nyctaxitrips/). 


## <a name="dataset"></a>Descrizione del set di dati Corse dei taxi di NYC

I dati relativi alle corse dei taxi a NYC hanno dimensioni di circa 20 GB sotto forma di file CSV compressi(circa 48 GB senza compressione), e includono oltre 173 milioni di corse singole nonché le tariffe pagate per ciascuna corsa. Il record di ogni corsa include la località di partenza e di arrivo, il numero di patente anonimo (del tassista) e il numero di licenza (ID univoco del taxi). I dati sono relativi a tutte le corse per l'anno 2013 e vengono forniti nei due set di dati seguenti per ciascun mese:

1. Il file CSV 'trip_data' contiene i dettagli delle corse, ad esempio il numero dei passeggeri, i punti partenza e arrivo, la durata e la lunghezza della corsa. Di seguito vengono forniti alcuni record di esempio:

		medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
		89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868

2. Il file 'trip_fare' CSV contiene i dettagli della tariffa pagata per ciascuna corsa, ad esempio tipo di pagamento, importo, soprattassa e tasse, mance e pedaggi e l'importo totale pagato. Di seguito vengono forniti alcuni record di esempio:

		medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
		89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
		0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
		DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

La chiave univoca che consente di unire trip\_data e trip\_fare è composta dai campi: medallion, hack\_licence e pickup\_datetime.

## <a name="mltasks"></a>Esempi di attività predittive

Verranno formulati tre problemi di stima, basati rispettivamente *tip\_amount* su:

1. Classificazione binaria: consente di stabilire se sia stata lasciata o meno una mancia per la corsa, vale a dire che un valore *tip\_amount* superiore a $ 0 rappresenta un esempio positivo, mentre un valore pari a *tip\_amount* $ 0 rappresenta un esempio negativo.

2. Classificazione multiclasse: consente di prevedere l'intervallo di mance lasciato per la corsa. Il valore *tip\_amount* viene suddiviso in cinque contenitori o classi:
	
		Class 0 : tip_amount = $0
		Class 1 : tip_amount > $0 and tip_amount <= $5
		Class 2 : tip_amount > $5 and tip_amount <= $10
		Class 3 : tip_amount > $10 and tip_amount <= $20
		Class 4 : tip_amount > $20

3. Attività di regressione: consente di prevedere l'importo della mancia lasciata per una corsa.  


## <a name="setup"></a>Configurazione dell'ambiente di analisi scientifica dei dati Azure

Come è possibile vedere nella guida [di pianificazione dell'ambiente](machine-learning-data-science-plan-your-environment.md), sono disponibili diverse opzioni per utilizzare il set di dati Corse dei taxi di NYC in Azure:

- Utilizzare i dati nei BLOB di Azure, quindi creare un modello in Azure Machine Learning
- Caricare i dati in un database SQL Server, quindi creare un modello in Azure Machine Learning

In questa esercitazione verrà illustrato come eseguire l'importazione in blocco in parallelo dei dati in SQL Server, l'esplorazione dei dati, la progettazione delle funzionalità e il sottocampionamento tramite SQL Server Management Studio e mediante IPython Notebook. [Gli script](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) e [i blocchi di appunti IPython](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks) di esempio vengono condivisi in GitHub. È inoltre disponibile nello stesso percorso un blocco di appunti IPython di esempio per gestire i dati nei blob.

Per configurare l'ambiente di analisi scientifica dei dati di Azure:

1. [Creare un account di archiviazione](storage-create-storage-account.md)

2. [Creare un'area di lavoro Azure ML](machine-learning-create-workspace.md)

3. [Eseguire il provisioning di una macchina virtuale Data Science](machine-learning-data-science-setup-sql-server-virtual-machine.md), che servirà come SQL Server e server IPython Notebook.

	> [AZURE.NOTE] Gli script e i blocchi di appunti IPython di esempio verranno scaricati nella macchina virtuale Data Science durante il processo di configurazione. Una volta completato lo script di post installazione della VM, gli esempi saranno disponibili nella libreria dei documenti della VM:  
	> - Script di esempio: `C:\Users\<user_name>\Documents\Data Science Scripts`  
	> - Blocchi di appunti di esempio IPython: `C:\Users\<user_name>\Documents\IPython Notebooks\DataScienceSamples`  
	> dove `<user_name>` è il nome di accesso Windows della VM. Le cartelle di esempio saranno denominate **Script di esempio** e **Blocchi di appunti IPython di esempio**.


In base alle dimensioni del set di dati, al percorso dell'origine dati e all'ambiente di destinazione Azure selezionato, questo scenario è simile allo [Scenario \n.5: set di dati di grandi dimensioni in file locali, con ambiente di destinazione SQL Server nella VM Azure](machine-learning-data-science-plan-sample-scenarios.md#largelocaltodb).

## <a name="getdata"></a>Acquisizione dei dati da un'origine pubblica

Per visualizzare il set di dati [Corse dei taxi di NYC](http://www.andresmh.com/nyctaxitrips/) dal relativo percorso pubblico, è possibile usare uno qualsiasi dei metodi descritti in [Spostamento dei dati da e verso l'archivio BLOB di Azure](machine-learning-data-science-move-azure-blob.md) e copiare i dati nella nuova macchina virtuale.

Per copiare i dati usando AzCopy:

1. Accedere alla macchina virtuale (VM)

2. Creare una nuova directory nel disco dati della macchina virtuale (nota: non utilizzare come disco dati il disco temporaneo fornito con la macchina virtuale).

3. In una finestra di prompt dei comandi, eseguire la seguente riga di comando Azcopy, sostituendo <path_to_data_folder> con la cartella dei dati creata al passaggio (2):

		"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S

	Una volta completata l'operazione in AzCopy, nella cartella dei dati dovrebbero essere presenti in totale 24 CSV compressi (12 per trip\_data e 12 per trip\_fare).

4. Decomprimere i file scaricati. Prendere nota della cartella in cui si trovano i dati non compressi. Il riferimento di questa cartella sarà <path\_to\_data\_files\>.

## <a name="dbload"></a>Importazione in blocco dei dati nel database SQL Server

È possibile migliorare le prestazioni relative al caricamento/trasferimento di grandi quantità di dati in un database SQL e le successive query utilizzando _Partitioned Tables and Views_. In questa sezione, verranno seguite le istruzioni fornite in [Importazione in blocco dei dati in parallelo tramite le tabelle delle partizioni di SQL](machine-learning-data-science-parallel-load-sql-partitioned-tables.md) per creare un nuovo database e caricare i dati nelle tabelle partizionate in parallelo.

1. Dopo aver effettuato l'accesso alla VM, avviare **SQL Server Management Studio**.

2. Effettuare la connessione mediante Autenticazione di Windows.

	![SSMS Connect][12]

3. Se non è stata ancora modificata la modalità di autenticazione di SQL Server e non è stato creato un nuovo utente di accesso SQL, aprire il file di script denominato **change\_auth.sql** nella cartella **Script di esempio**. Modificare il nome utente e la password predefiniti. Fare clic su **!Esegui** nella barra degli strumenti per eseguire lo script.

	![Execute Script][13]

4. Verificare e/o modificare le cartelle di database e log predefinite di SQL Server per essere certi che i database appena creati saranno archiviati nel disco dati. L'immagine della VM SQL Server che viene ottimizzata per i carichi data warehouse viene preconfigurata con i dischi dati e di registro. Se nella VM in uso non è incluso un disco dati e durante il processo di configurazione della VM sono stati aggiunti dei nuovi dischi virtuali, modificare le cartelle predefinite nel modo indicato di seguito:

	- Fare clic con il pulsante destro del mouse sul nome dell'SQL Server nel pannello a sinistra e scegliere **Proprietà**.

		![SQL Server Properties][14]

	- Selezionare **Impostazioni database** dall'elenco **Seleziona pagina** sulla sinistra.

	- Verificare e/o modificare **Percorsi predefiniti database** nei percorsi **Disco dati** di preferenza. È questa la posizione in cui si trovano i nuovi database se vengono creati con le impostazioni di percorso predefinite.
	
		![SQL Database Defaults][15]  

5. Per creare un nuovo database e un set di filegroup in cui conservare le tabelle partizionate, aprire lo script di esempio **create\_db\_default.sql**. Mediante questo script verranno creati un nuovo database denominato **TaxiNYC** e 12 filegroup nel percorso dei dati predefinito. In ogni gruppo sarà presente un mese di dati trip\_data e trip\_fare. Se lo si desidera, modificare il nome del database. Fare clic su **!Esegui** per eseguire lo script.

6. Successivamente, creare due tabelle delle partizioni, una per trip\_data e l'altra per trip\_fare. Aprire lo script di esempio **create\_partitioned\_table.sql**, in modo da effettuare le seguenti operazioni:

	- Creare una funzione di partizione per suddividere i dati per mese.
	- Creare uno schema di partizione per eseguire il mapping dei dati di ciascun mese a un filegroup diverso.
	- Creare due tabelle partizionate di cui è stato eseguito il mapping allo schema di partizione: **nyctaxi\_trip** conterrà i dati trip\_data e **nyctaxi\_fare** conterrà i dati trip\_fare.

	Fare clic su **!Esegui** per eseguire lo script e creare le tabelle partizionate.

7. Nella cartella **Script di esempio**, sono presenti due script PowerShell di esempio, forniti per illustrare le importazioni in blocco in parallelo dei dati nelle tabelle SQL Server.

	- **bcp\_parallel\_generic.ps1** è uno script generico per l'importazione in blocco in parallelo dei dati in una tabella. Modificare lo script per impostare le variabili di input e di destinazione come indicato nelle righe di commento dello script.
	- **bcp\_parallel\_nyctaxi.ps1** è una versione preconfigurata dello script generico e può essere utilizzata per caricare entrambe le tabelle per i dati Corse dei taxi di NYC.  

8. Fare clic con il pulsante destro del mouse sul nome dello script **bcp\_parallel\_nyctaxi.ps1** e fare clic su **Modifica** per aprirlo in PowerShell. Esaminare le variabili preimpostate e modificarle in base al nome di database selezionato, alla cartella dei dati di input, alla cartella del log di destinazione e ai percorsi dei file di formato di esempio **nyctaxi_trip.xml** e **nyctaxi\_fare.xml** (forniti nella cartella **Script di esempio**). 

	![Bulk Import Data][16]

	È inoltre possibile selezionare la modalità di autenticazione. La modalità predefinita è Autenticazione di Windows. Fare clic sulla freccia verde nella barra degli strumenti per eseguire. Mediante lo script verranno avviate 24 operazioni in blocco in parallelo, 12 per ogni tabella partizionata. È possibile controllare lo stato dell'importazione dei dati aprendo la cartella dei dati predefinita di SQL Server come impostato in precedenza.

9. Nello script PowerShell vengono segnalate l'ora di inizio e l'ora di fine. Al completamento di tutte le importazioni in blocco, viene indicata l'ora finale. Verificare nella cartella del log di destinazione che le importazioni in blocco siano state completate correttamente, vale a dire che non siano stati segnalati errori nella cartella del log di destinazione.

10. Il database ora è pronto per l'esplorazione, la progettazione di funzionalità e altre operazioni che si desidera eseguire. Poiché le tabelle sono partizionate in base al campo **pickup\_datetime**, le query che includono le condizioni **pickup\_datetime** nella clausola **WHERE** si avvarranno dello schema di partizione.

11. In **SQL Server Management Studio**, esplorare lo script di esempio fornito **sample\_queries.sql**. Per eseguire una qualsiasi query di esempio, evidenziare le righe della query, quindi fare clic su **!Esegui** nella barra degli strumenti.

12. I dati di Corse dei taxi di NYC vengono caricati in due tabelle distinte. Per migliorare le operazioni di unione, è consigliabile indicizzare le tabelle. Lo script di esempio **create\_partitioned\_index.sql** consente di creare indici partizionati sulla chiave di join composita **medallion, hack\_license e pickup\_datetime**.

## <a name="dbexplore"></a>Esplorazione dei dati e progettazione di funzionalità in SQL Server

In questa sezione, verranno eseguite l'esplorazione dei dati e la generazione di funzionalità mediante l'esecuzione di query SQL direttamente in **SQL Server Management Studio** tramite un database SQL Server creato in precedenza. Nella cartella **Script di esempio** viene fornito uno script di esempio denominato **sample\_queries.sql**. Modificare lo script per cambiare il nome del database, se è diverso da quello predefinito: **TaxiNYC**.

In questo esercizio, verranno effettuate le seguenti operazioni:

- Connessione a **SQL Server Management Studio** tramite Autenticazione di Windows o mediante Autenticazione SQL e il nome di accesso e la password SQL.
- Esplorazione delle distribuzioni di dati di un numero ridotto di campi in diverse finestre temporali.
- Indagine sulla qualità dei dati dei campi di longitudine e latitudine.
- Generazione di etichette di classificazione binaria e multiclasse basata su **tip\_amount**.
- Generazione di funzionalità calcolo/confronto delle distanze delle corse.
- Unione di due tabelle ed estrazione di un campione casuale che verrà utilizzato per la creazione di modelli.

Una volta pronti a proseguire con Azure Machine Learning, è possibile effettuare una delle seguenti operazioni:  

1. Salvare la query SQL per estrarre e campionare i dati e copiare e incollare la query direttamente in un modulo Lettore in Azure Machine Learning 
2. Salvare in modo definitivo i dati campionati e compilati che si pianifica di utilizzare per la creazione di modelli in una nuova tabella di database e utilizzare la nuova tabella nel modulo Lettore in Azure Machine Learning.

In questa sezione verrà salvata la query finale per estrarre e campionare i dati. Il secondo metodo viene illustrato nella sezione [Esplorazione dei dati e progettazione di funzionalità in IPython Notebook](#ipnb).

Per una verifica rapida del numero di righe e di colonne nelle tabelle popolate in precedenza tramite l'importazione in blocco in parallelo,

	-- Segnalare il numero di righe nella tabella nyctaxi_trip senza l'analisi della tabella
	SELEZIONARE SOMMA(righe) DA sys.partitions DOVE object_id = OBJECT_ID('nyctaxi_trip')

	-- Segnalare il numero di colonne nella tabella nyctaxi_trip
	SELEZIONARE CONTEGGIO(*) DA information_schema.columns DOVE table_name = 'nyctaxi_trip' 

#### Esplorazione: distribuzione delle corse per licenza

In questo esempio viene identificata la licenza (numero del taxi) che ha eseguito più di 100 corse in un determinato periodo. Per la query verrà utilizzata la tabella partizionata poiché è condizionata dallo schema di partizione di **pickup\_datetime**. Per la query del set di dati completo verrà inoltre utilizzata la tabella partizionata e/o l'analisi dell'indice.

	SELECT medallion, COUNT(*)
	FROM nyctaxi_fare
	WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
	GROUP BY medallion
	HAVING COUNT(*) > 100

#### Esplorazione: distribuzione delle corse per licenza e hack_license

	SELECT medallion, hack_license, COUNT(*)
	FROM nyctaxi_fare
	WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
	GROUP BY medallion, hack_license
	HAVING COUNT(*) > 100

#### Valutazione della qualità dei dati: Verifica dei record con longitudine o latitudine errate

In questo esempio viene esaminato se uno dei campi relativi alla longitudine o alla latitudine contiene un valore non valido (i gradi radianti devono essere compresi tra -90 e 90) o presenta le coordinate (0, 0).

	SELECT COUNT(*) FROM nyctaxi_trip
	WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
	AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
	OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
	OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
	OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
	OR    (pickup_longitude = '0' AND pickup_latitude = '0')
	OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))

#### Esplorazione: distribuzione delle corse per le quali è stata lasciata una mancia e di quelle per le quali non è stata lasciata una mancia

In questo esempio viene individuato il numero di corse per le quali è stata lasciata una mancia e il numero di corse per le quali non è stata lasciata una mancia in un determinato periodo (o nel set di dati completo, in caso di un anno intero). Questa distribuzione riflette la distribuzione delle etichette binarie che dovranno essere utilizzate in seguito per la creazione di modelli di classificazione binaria.

	SELECT tipped, COUNT(*) AS tip_freq FROM (
	  SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
	  FROM nyctaxi_fare
	  WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
	GROUP BY tipped

#### Esplorazione: distribuzione della classe o dell'intervallo delle mance

In questo esempio viene calcolata la distribuzione degli intervalli delle mance in un determinato periodo (o nel set di dati completo, in caso di un anno intero). Si tratta della distribuzione delle classi di etichette che verranno utilizzate in seguito per la creazione di modelli di classificazione multiclasse.

	SELECT tip_class, COUNT(*) AS tip_freq FROM (
		SELECT CASE 
			WHEN (tip_amount = 0) THEN 0
			WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
			WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
			WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
			ELSE 4 
		END AS tip_class
	FROM nyctaxi_fare
	WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
	GROUP BY tip_class

#### Esplorazione: calcolo e confronto della distanza delle corse

In questo esempio, la longitudine e la latitudine del prelievo e dello scarico vengono convertite in punti geografici SQL, viene calcolata la distanza della corsa mediante la differenza dei punti geografici di SQL e viene restituito un campione casuale dei risultati per il confronto. Nell'esempio i risultati vengono limitati unicamente alle coordinate valide tramite la query per la valutazione della qualità dei dati illustrata in precedenza.

	SELECT 
	pickup_location=geography::STPointFromText('POINT(' + pickup_longitude + ' ' + pickup_latitude + ')', 4326)
	,dropoff_location=geography::STPointFromText('POINT(' + dropoff_longitude + ' ' + dropoff_latitude + ')', 4326)
	,trip_distance
	,computedist=round(geography::STPointFromText('POINT(' + pickup_longitude + ' ' + pickup_latitude + ')', 4326).STDistance(geography::STPointFromText('POINT(' + dropoff_longitude + ' ' + dropoff_latitude + ')', 4326))/1000, 2)
	FROM nyctaxi_trip
	tablesample(0.01 percent)
	WHERE CAST(pickup_latitude AS float) BETWEEN -90 AND 90
	AND   CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
	AND   pickup_longitude != '0' AND dropoff_longitude != '0'

#### Progettazione di funzionalità nelle query SQL

Le query di esplorazione per la generazione delle etichette e la conversione geografica possono inoltre essere utilizzate per generare etichette/funzionalità se si rimuove la parte relativa al conteggio. Ulteriori esempi SQL di progettazione di funzionalità vengono forniti nella sezione [Esplorazione dei dati e progettazione di funzionalità in IPython Notebook](#ipnb). È più efficiente eseguire le query di generazione di funzionalità sull'intero set di dati o su un subset di grandi dimensioni tramite query SQL che si eseguono direttamente sull'istanza di database SQL Server. Le query possono essere eseguite in **SQL Server Management Studio**, IPython Notebook o in qualsiasi strumento o ambiente di sviluppo in grado di accedere al database localmente o in remoto.

#### Preparazione dei dati per la creazione di modelli

Le query riportate di seguito consentono di unire le tabelle **nyctaxi\_trip** e **nyctaxi\_fare**, generare un'etichetta di classificazione binaria **tipped**, un'etichetta di classificazione multiclasse **tip\_class** e di estrarre un campione casuale dell'1% dall'intero set di dati unito. La query può essere copiata e incollata direttamente nel modulo Lettore di [Azure Machine Learning Studio](https://studio.azureml.net) per l'inserimento diretto dei dati dall'istanza di database SQL Server in Azure. La query esclude i record con le coordinate errate (0, 0).

	SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount, 	f.total_amount, f.tip_amount,
	    CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped,
	    CASE WHEN (tip_amount = 0) THEN 0
	        WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
	        WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
	        WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
	        ELSE 4
	    END AS tip_class
	FROM nyctaxi_trip t, nyctaxi_fare f
	TABLESAMPLE (1 percent)
	WHERE t.medallion = f.medallion
	AND   t.hack_license = f.hack_license
	AND   t.pickup_datetime = f.pickup_datetime
	AND   pickup_longitude != '0' AND dropoff_longitude != '0'


## <a name="ipnb"></a>Esplorazione dei dati e progettazione di funzionalità in IPython Notebook

In questa sezione, verranno eseguite l'esplorazione dei dati e la generazione di funzionalità
tramite query Python e SQL al database SQL Server creato in precedenza. Nella cartella **Blocchi di appunti di esempio IPython** viene fornito un blocco di appunti IPython di esempio denominato **machine-Learning-data-science-process-sql-story.ipynb**. Tale blocco di appunti è disponibile anche in [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks).

La sequenza consigliata quando si utilizzano i Big Data è la seguente:

- Leggere un piccolo campione di dati in un frame di dati in memoria.
- Eseguire alcune visualizzazioni ed esplorazioni tramite i dati campionati.
- Sperimentare la progettazione di funzionalità utilizzando i dati campionati. 
- Per operazioni più estese di esplorazione dei dati, manipolazione dei dati e progettazione di funzionalità, utilizzare Python per inviare query SQL direttamente al database SQL Server nella VM Azure.
- Decidere la dimensione del campione da utilizzare per la creazione di modelli Azure Machine Learning.

Una volta pronti a proseguire con Azure Machine Learning, è possibile effettuare una delle seguenti operazioni:  

1. Salvare la query SQL query finale per estrarre e ottenere un campione dei dati e copiare e incollare direttamente la query in un modello Lettore in Azure Machine Learning. Questo metodo è illustrato nella sezione [Creazione di modelli in Azure Machine Learning](#mlmodel).    
2. Salvare in modo definitivo i dati campionati e compilati che si pianifica di utilizzare per la creazione di modelli in una nuova tabella di database, quindi utilizzare la nuova tabella nel modulo Lettore.

Di seguito vengono forniti alcuni esempi di esplorazione dei dati, visualizzazione dei dati e progettazione di funzionalità. Per ulteriori esempi, vedere il blocco di appunti di esempio SQL IPython nella cartella **Blocchi di appunti IPython di esempio**.

#### Inizializzazione delle credenziali di database

Inizializzare le impostazioni di connessione del database nelle seguenti variabili:

    SERVER_NAME=<server name>
    DATABASE_NAME=<database name>
    USERID=<user name>
    PASSWORD=<password>
    DB_DRIVER = <database server>

#### Creazione della connessione di database

    CONNECTION_STRING = 'DRIVER={'+DRIVER+'};SERVER='+SERVER_NAME+';DATABASE='+DATABASE_NAME+';UID='+USERID+';PWD='+PASSWORD
    conn = pyodbc.connect(CONNECTION_STRING)

#### Segnalazione del numero di righe e di colonne nella tabella nyctaxi_trip

    nrows = pd.read_sql('''
		SELECT SUM(rows) FROM sys.partitions 
		WHERE object_id = OBJECT_ID('nyctaxi_trip')
	''', conn)
    
	print 'Total number of rows = %d' % nrows.iloc[0,0]
    
    ncols = pd.read_sql('''
		SELECT COUNT(*) FROM information_schema.columns 
		WHERE table_name = ('nyctaxi_trip')
	''', conn)
    
	print 'Total number of columns = %d' % ncols.iloc[0,0]

- Numero di righe totali = 173179759  
- Numero di colonne totali = 14
    
#### Effettuazione della lettura di un piccolo campione di dati dal database SQL Server

    t0 = time.time()
    
	query = '''
		SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, 
			f.tolls_amount, f.total_amount, f.tip_amount 
		FROM nyctaxi_trip t, nyctaxi_fare f 
		TABLESAMPLE (0.05 PERCENT)
		WHERE t.medallion = f.medallion 
		AND   t.hack_license = f.hack_license 
		AND   t.pickup_datetime = f.pickup_datetime
	'''

    df1 = pd.read_sql(query, conn)
    
    t1 = time.time()
    print 'Time to read the sample table is %f seconds' % (t1-t0)
    
    print 'Number of rows and columns retrieved = (%d, %d)' % (df1.shape[0], df1.shape[1])

Il tempo per la lettura della tabella di esempio è di 6,492000 secondi  
Numero di righe e di colonne recuperate = (8.4952, 21)
    
#### Statistiche descrittive

Ora è possibile esplorare i dati campionati. Si inizierà con uno sguardo
alle statistiche descrittive per i campi **trip\_distance** (o qualsiasi altro):

    df1['trip_distance'].describe()

#### Visualizzazione: esempio di box plot

Successivamente si consulterà il box plot per la distanza delle corse, per visualizzare le quantità

    df1.boxplot(column='trip_distance',return_type='dict')

![Plot #1][1]

#### Visualizzazione: esempio di tracciato di distribuzione

    fig = plt.figure()
    ax1 = fig.add_subplot(1,2,1)
    ax2 = fig.add_subplot(1,2,2)
    df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
    df1['trip_distance'].hist(ax=ax2, bins=100, color='k')

![Plot #2][2]

#### Visualizzazione: tracciati a barre e linee

In questo esempio, la distanza delle corse viene suddivisa in cinque contenitori e vengono visualizzati i risultati di questa suddivisione.

    trip_dist_bins = [0, 1, 2, 4, 10, 1000]
    df1['trip_distance']
    trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
    trip_dist_bin_id

La distribuzione precedente può essere rappresentata in un tracciato a barre o linee come illustrato di seguito

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')

![Plot #3][3]

    pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')

![Plot #4][4]

#### Visualizzazione: esempio di grafico a dispersione

Viene eseguito un grafico a dispersione tra **trip\_time\_in\_secs** e **trip\_distance** per verificare se
esiste una correlazione

    plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])

![Plot #6][6]

Allo stesso modo, è possibile verificare la relazione tra **rate\_code** e **trip\_distance**.

    plt.scatter(df1['passenger_count'], df1['trip_distance'])

![Plot #8][8]

### Sottocampionamento dei dati in SQL

Quando si preparano i dati per la creazione dei modelli in [Azure Machine Learning Studio](https://studio.azureml.net), è possibile decidere la query **SQL da utilizzare direttamente nel modulo Lettore** o salvare in modo definitivo i dati compilati e campionati in una nuova tabella, che è possibile utilizzare nel modulo Lettore con un semplice **SELECT * FROM <your\_new\_table\_name>**.

In questa sezione verrà creata una nuova tabella per contenere i dati campionati e compilati. Un esempio di query SQL diretta per la creazione di modelli viene fornita nella sezione [Esplorazione dei dati e progettazione di funzionalità in SQL Server](#dbexplore).

#### Creazione di una tabella di esempio e popolamento con l'1% delle tabelle unite. Innanzitutto eliminare la tabella, se presente.

In questa sezione, saranno unite le tabelle **nyctaxi\_trip** e **nyctaxi\_fare**, verrà estratto un campione casuale dell'1% e i dati campionati verranno salvati definitivamente in una nuova tabella denominata **nyctaxi\_one\_percent**:

    cursor = conn.cursor()
    
    drop_table_if_exists = '''
        IF OBJECT_ID('nyctaxi_one_percent', 'U') IS NOT NULL DROP TABLE nyctaxi_one_percent
    '''
    
    nyctaxi_one_percent_insert = '''
        SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount, f.total_amount, f.tip_amount
		INTO nyctaxi_one_percent 
		FROM nyctaxi_trip t, nyctaxi_fare f
		TABLESAMPLE (1 PERCENT)
		WHERE t.medallion = f.medallion
		AND   t.hack_license = f.hack_license
		AND   t.pickup_datetime = f.pickup_datetime
		AND   pickup_longitude <> '0' AND dropoff_longitude <> '0'
    '''
    
    cursor.execute(drop_table_if_exists)
    cursor.execute(nyctaxi_one_percent_insert)
    cursor.commit()
    
### Esplorazione dei dati mediante query SQL in IPython Notebook

In questa sezione, verranno esplorate le distribuzioni di dati tramite l'1% dei dati campionati salvati in modo definitivo nella nuova tabella creata in precedenza. Si noti che esplorazioni simili possono essere eseguite utilizzando le tabelle originali, avvalendosi, se lo si desidera, di **TABLESAMPLE** per limitare l'esempio di esplorazione o limitando i risultati a un determinato periodo tramite le partizioni **pickup\_datetime**, come illustrato nella sezione [Esplorazione dei dati e progettazione di funzionalità in SQL Server](#dbexplore).

#### Esplorazione: distribuzione giornaliera delle corse

    query = '''
		SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c 
		FROM nyctaxi_one_percent 
		GROUP BY CONVERT(date, dropoff_datetime)
	'''

    pd.read_sql(query,conn)

#### Esplorazione: distribuzione delle corse per licenza

    query = '''
		SELECT medallion,count(*) AS c 
		FROM nyctaxi_one_percent 
		GROUP BY medallion
	'''
    
	pd.read_sql(query,conn)

### Generazione di funzionalità mediante query SQL in IPython Notebook

In questa sezione verranno generate nuove etichette e funzionalità direttamente mediante query SQL, effettuando operazioni nella tabella di esempio dell'1% creata nella sezione precedente.

#### Generazione di etichette: generazione di etichette di classe

Nell'esempio seguente, verranno generati due set di etichette da utilizzare per la creazione dei modelli:

1. Etichette di classe binaria **tipped** (che forniscono una stima sull'elargizione di una mancia)
2. Etichette multiclasse **tip\_class** (che forniscono una stima sul contenitore delle mance o sull'intervallo di queste)

		nyctaxi_one_percent_add_col = '''
			ALTER TABLE nyctaxi_one_percent ADD tipped bit, tip_class int
		'''
		
		cursor.execute(nyctaxi_one_percent_add_col)
		cursor.commit()
    
    	nyctaxi_one_percent_update_col = '''
        	UPDATE nyctaxi_one_percent 
            SET 
               tipped = CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END,
               tip_class = CASE WHEN (tip_amount = 0) THEN 0
                                WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
                                WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
                                WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
                                ELSE 4
                            END
        '''

    	cursor.execute(nyctaxi_one_percent_update_col)
		cursor.commit()

#### Progettazione di funzionalità: funzionalità di conteggio per le colonne relative alle categorie

In questo esempio un campo di categoria viene trasformato in un campo numerico mediante la sostituzione di ciascuna categoria con il conteggio delle relative occorrenze nei dati.

    nyctaxi_one_percent_insert_col = '''
		ALTER TABLE nyctaxi_one_percent ADD cmt_count int, vts_count int
	'''

    cursor.execute(nyctaxi_one_percent_insert_col)
    cursor.commit()

    nyctaxi_one_percent_update_col = '''
		WITH B AS 
		(
			SELECT medallion, hack_license, 
				SUM(CASE WHEN vendor_id = 'cmt' THEN 1 ELSE 0 END) AS cmt_count,
				SUM(CASE WHEN vendor_id = 'vts' THEN 1 ELSE 0 END) AS vts_count
			FROM nyctaxi_one_percent 
			GROUP BY medallion, hack_license
		) 
    
		UPDATE nyctaxi_one_percent 
		SET nyctaxi_one_percent.cmt_count = B.cmt_count,
			nyctaxi_one_percent.vts_count = B.vts_count
		FROM nyctaxi_one_percent A INNER JOIN B 
		ON A.medallion = B.medallion AND A.hack_license = B.hack_license
	'''
    
    cursor.execute(nyctaxi_one_percent_update_col)
    cursor.commit()

#### Progettazione di funzionalità: funzionalità di collocazione per le colonne numeriche

In questo esempio, un campo numerico continuo viene trasformato in intervalli di categorie predefiniti, vale a dire che il campo numerico verrà trasformato in un campo di categoria.

    nyctaxi_one_percent_insert_col = '''
		ALTER TABLE nyctaxi_one_percent ADD trip_time_bin int
	'''

    cursor.execute(nyctaxi_one_percent_insert_col)
    cursor.commit()

    nyctaxi_one_percent_update_col = '''
		WITH B(medallion,hack_license,pickup_datetime,trip_time_in_secs, BinNumber ) AS 
		(
			SELECT medallion,hack_license,pickup_datetime,trip_time_in_secs, 
			NTILE(5) OVER (ORDER BY trip_time_in_secs) AS BinNumber from nyctaxi_one_percent
		)
    
		UPDATE nyctaxi_one_percent 
		SET trip_time_bin = B.BinNumber
		FROM nyctaxi_one_percent A INNER JOIN B 
		ON A.medallion = B.medallion
		AND A.hack_license = B.hack_license
		AND A.pickup_datetime = B.pickup_datetime
	'''
    
    cursor.execute(nyctaxi_one_percent_update_col)
    cursor.commit()

#### Progettazione di funzionalità: funzionalità di estrazione della posizione dalla longitudine/latitudine decimale

In questo esempio, la rappresentazione decimale di un campo di latitudine o longitudine viene suddivisa in più campi area di diversa granularità, ad esempio, paese, città, paese, quartiere e così via. Si noti che non viene eseguito il mapping dei nuovi campi geografici alle località effettive. Per informazioni sul mapping delle località con codice geografico, vedere [Servizi REST di Bing Mappe](https://msdn.microsoft.com/library/ff701710.aspx). 

    nyctaxi_one_percent_insert_col = '''
		ALTER TABLE nyctaxi_one_percent 
		ADD l1 varchar(6), l2 varchar(3), l3 varchar(3), l4 varchar(3),
			l5 varchar(3), l6 varchar(3), l7 varchar(3)
	'''

    cursor.execute(nyctaxi_one_percent_insert_col)
    cursor.commit()

    nyctaxi_one_percent_update_col = '''
		UPDATE nyctaxi_one_percent
		SET l1=round(pickup_longitude,0) 
			, l2 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 1 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),1,1) ELSE '0' END     
			, l3 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 2 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),2,1) ELSE '0' END     
			, l4 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 3 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),3,1) ELSE '0' END     
			, l5 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 4 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),4,1) ELSE '0' END     
			, l6 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 5 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),5,1) ELSE '0' END     
			, l7 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 6 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),6,1) ELSE '0' END 
	'''

    cursor.execute(nyctaxi_one_percent_update_col)
    cursor.commit()

#### Verificare l'aspetto finale della tabella con funzionalità

    query = '''SELECT TOP 100 * FROM nyctaxi_one_percent'''
    pd.read_sql(query,conn)

A questo punto è possibile procedere con la creazione e la distribuzione di modelli in [Azure Machine Learning](https://studio.azureml.net). I dati sono pronti per eventuali problemi di stima identificati in precedenza, in modo specifico:

1. Classificazione binaria: consente di prevedere se per la corsa è stata lasciata o meno una mancia.

2. Classificazione multiclasse: consente di eseguire una stima dell'intervallo di mance pagato, in base alle classi definite in precedenza.

3. Attività di regressione: consente di prevedere l'importo della mancia lasciata per una corsa.  


## <a name="mlmodel"></a>Creazione di modelli in Azure Machine Learning

Per iniziare l'esercizio relativo alla creazione di modelli, accedere all'area di lavoro Azure Machine Learning. Se non è stata ancora creata un'area di lavoro per Machine Learning, vedere [Creazione di un'area di lavoro Azure ML](machine-learning-create-workspace.md).

1. Per iniziare a utilizzare Azure Machine Learning, vedere [Informazioni su Azure Machine Learning Studio](machine-learning-what-is-ml-studio.md)

2. Effettuare l'accesso ad [Azure Machine Learning Studio](https://studio.azureml.net).

3. Nella pagina iniziale vengono fornite moltissime informazioni, video, esercitazioni, collegamenti al Riferimento ai moduli e altre risorse. Per ulteriori informazioni su Azure Machine Learning, consultare il [Centro documentazione di Azure Machine Learning](http://azure.microsoft.com/documentation/services/machine-learning/).

Un tipico esperimento training consiste nelle seguenti operazioni:

1. Creazione di un esperimento **+NEW**.
2. Inserimento dei dati in Azure ML.
3. Pre-elaborazione, trasformazione e manipolazione dei dati secondo le esigenze.
4. Generazione di funzionalità, se necessario.
5. Suddivisione dei dati in set di dati di training, convalida o test(o creazione di set di dati distinti per ciascuna tipologia).
6. Selezione di uno o più algoritmi Machine Learning, a seconda del problema di apprendimento da risolvere. Ad esempio, classificazione binaria, classificazione multiclasse, regressione.
7. Training di uno o più modelli tramite il set di dati di training.
8. Calcolo del punteggio del set di dati di convalida mediante i modelli sottoposti a training.
9. Valutazione dei modelli per calcolare la metrica rilevante per il problema di apprendimento.
10. Ottimizzazione dei modelli e selezione del modello migliore per la distribuzione.

In questo esercizio, i dati sono già stati esplorati e compilati in SQL Server, ed è stata decisa la dimensione del campione da inserire in Azure ML. Per creare uno o più modelli di stima è stato deciso di effettuare le seguenti operazioni:

1. Inserimento dei dati in Azure ML tramite il modulo **Lettore**, disponibile nella sezione **Input e output dei dati**. Per ulteriori informazioni, vedere la pagina di riferimento sul modulo [Lettore](https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004).

	![Azure ML Reader][17]

2. Selezione del **Database SQL Azure** come **Origine dati** nel pannello **Proprietà**.

3. Immissione del nome DNS del database nel campo **Nome server database**. Formato: `tcp:<your_virtual_machine_DNS_name>,1433`

4. Immissione del **Nome database** nel campo corrispondente.

5. Immissione del **Nome utente SQL** in **Nome account utente server, e della password in **Password account utente server**.

6. Selezione dell'opzione **Accetta qualsiasi certificato server**.

7. Nell'area di testo di modifica **Query database**, incollare la query che consente di estrarre i campi di database necessari (inclusi i campi calcolati come le etichette) e sottocampionare i dati nella dimensione campione desiderata.

Nella figura seguente viene fornito un esempio di un esperimento di classificazione binaria in cui si esegue la lettura dei dati direttamente dal database SQL Server. È possibile creare esperimenti dello stesso tipo per i problemi di classificazione multiclasse e di regressione.

![Azure ML Train][10]

> [AZURE.IMPORTANT] Negli esempi di estrazione dei dati di modellazione e di query di campionamento forniti nelle sezioni precedenti, **tutte le etichette per i tre esercizi sulla creazione dei modelli sono incluse nella query**. Un passaggio importante (richiesto) in ciascun esercizio sulla modellazione consiste nell'**escludere** le etichette non necessarie per gli altri due problemi ed eventuali **perdite di destinazione**. Ad esempio, nell'utilizzo della classificazione binaria, utilizzare l'etichetta **tipped** ed escludere i campi **tip\_class**, **tip\_amount** e **total\_amount**. Questi ultimi sono perdite di destinazione in quanto implicano la mancia pagata.
>
> Per escludere le colonne non necessarie e/o le perdite di destinazione, è possibile utilizzare il modulo **Colonne progetto o l'** **Editor metadati**. Per ulteriori informazioni, vedere le pagine di riferimento [Colonne progetto](https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223) ed [Editor metadati](https://msdn.microsoft.com/library/azure/370b6676-c11c-486f-bf73-35349f842a66).

## <a name="mldeploy"></a>Distribuzione di modelli in Azure Machine Learning

Quando il modello è pronto, è possibile distribuirlo in modo semplice come servizio Web direttamente dall'esperimento. Per ulteriori informazioni sulla pubblicazione di servizi Web Azure ML, vedere [Pubblicazione di un servizio Web Azure Machine Learning](machine-learning-publish-a-machine-learning-web-service.md).

Per distribuire un nuovo servizio Web, è necessario effettuare le seguenti operazioni:

1. Creare un esperimento di assegnazione di punteggio.
2. Pubblicare il servizio Web.

Per creare un esperimento di assegnazione di punteggio da un esperimento di training **Completato**, fare clic su **CREA ESPERIMENTO DI ASSEGNAZIONE PUNTEGGIO** nella barra delle azioni inferiore.

![Azure Scoring][18]

Azure Machine Learning tenterà di creare un esperimento di assegnazione di punteggio basato sui componenti dell'esperimento di training. In particolare, verranno effettuate le seguenti operazioni:

1. Salvataggio del modello sottoposto a training e rimozione dei moduli di training del modello.
2. Identificazione di una **porta di input** logica per rappresentare lo schema di dati di input previsto.
3. Identificazione di una **porta di output** logica per rappresentare lo schema di output del servizio Web previsto.

Una volta creato l'esperimento di assegnazione del punteggio, esaminarlo e regolarlo in base alle esigenze. Una regolazione tipica consiste nel sostituire il set di dati di input e/o la query con uno che escluda i campi etichetta, in quanto questi non saranno disponibili quando si chiama il servizio. È inoltre buona norma ridurre la dimensione del set di dati di input e/o della query a pochi record, sufficienti a indicare lo schema di input. Per la porta di output, di solito vengono esclusi tutti i campi di input e inclusi soltanto **Etichette con punteggio** e **Probabilità con punteggio** nell'output, mediante il modulo **Colonne progetto**.

Nella figura di seguito viene fornito un esperimento di assegnazione di punteggio di esempio. Quando si è pronti per la pubblicazione, fare clic sul pulsante **PUBBLICA SERVIZIO WEB** nella barra delle azioni inferiore.

![Azure ML Publish][11]

Ricapitolando, in questa esercitazione dettagliata è stato creato un ambiente di analisi scientifica dei dati Azure, è stato utilizzato un set di dati pubblico di grandi dimensioni dall'acquisizione dei dati al training del modello e alla pubblicazione di un servizio Web Azure Machine Learning.

### Informazioni sulla licenza

Questa procedura dettagliata di esempio e gli script e i blocchi di appunti IPython che la accompagnano sono condivisi da Microsoft nella licenza MIT. Selezionare il file LICENSE.txt nella directory del codice di esempio in GitHub per ulteriori informazioni.

### Riferimenti

*	[Pagina di Andrés Monroy per scaricare i dati sulle corse dei taxi di NYC](http://www.andresmh.com/nyctaxitrips/)  
*	[Complemento ai dati sulle corse dei taxi di NYC di Chris Whong](http://chriswhong.com/open-data/foil_nyc_taxi/)   
*	[Ricerche e statistiche su NYC Taxi and Limousine Commission](https://www1.nyc.gov/html/tlc/html/about/statistics.shtml)


[1]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_26_1.png
[2]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_28_1.png
[3]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_35_1.png
[4]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_36_1.png
[5]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_39_1.png
[6]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_42_1.png
[7]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_44_1.png
[8]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_46_1.png
[9]: ./media/machine-learning-data-science-process-sql-walkthrough/sql-walkthrough_71_1.png
[10]: ./media/machine-learning-data-science-process-sql-walkthrough/azuremltrain.png
[11]: ./media/machine-learning-data-science-process-sql-walkthrough/azuremlpublish.png
[12]: ./media/machine-learning-data-science-process-sql-walkthrough/ssmsconnect.png
[13]: ./media/machine-learning-data-science-process-sql-walkthrough/executescript.png
[14]: ./media/machine-learning-data-science-process-sql-walkthrough/sqlserverproperties.png
[15]: ./media/machine-learning-data-science-process-sql-walkthrough/sqldefaultdirs.png
[16]: ./media/machine-learning-data-science-process-sql-walkthrough/bulkimport.png
[17]: ./media/machine-learning-data-science-process-sql-walkthrough/amlreader.png
[18]: ./media/machine-learning-data-science-process-sql-walkthrough/amlscoring.png

<!--HONumber=49-->