<properties 
	pageTitle="App Machine Learning: Servizio di rilevamento delle anomalie | Microsoft Azure" 
	description="API di rilevamento delle anomalie è un esempio compilato con Microsoft Azure Machine Learning che consente di rilevare anomalie nei dati della serie temporale con i valori numerici disposti in modo uniforme nel tempo." 
	services="machine-learning" 
	documentationCenter="" 
	authors="alokkirpal" 
	manager="jhubbard"
	editor="cgronlun" />  

<tags 
	ms.service="machine-learning" 
	ms.devlang="na" 
	ms.topic="reference" 
	ms.tgt_pltfrm="na" 
	ms.workload="multiple" 
	ms.date="07/22/2016" 
	ms.author="alokkirpal"/>


# Servizio di rilevamento delle anomalie Machine Learning#

##Panoramica

API di rilevamento delle anomalie è un esempio compilato con Azure Machine Learning che consente di rilevare anomalie nei dati della serie temporale con i valori numerici disposti in modo uniforme nel tempo.

Questa API può rilevare i seguenti tipi di modelli di anomalie nei dati delle serie temporali:

* **Tendenze positive e negative**: ad esempio, quando si monitora l'utilizzo della memoria di elaborazione, una tendenza verso l'alto può risultare interessante perché può indicare una perdita di memoria.

* **Modifiche dell'intervallo dinamico di valori**: ad esempio, quando si monitorano le eccezioni generate da un servizio cloud, le eventuali modifiche dell'intervallo dinamico di valori possono indicare un'instabilità dell'integrità del servizio.

* **Picchi e flessioni**: ad esempio, quando si monitora il numero di errori di accesso a un servizio o il numero di transazioni completate in un sito di e-commerce, i picchi o le flessioni possono indicare un comportamento anomalo.

Queste funzionalità di rilevamento di Machine Learning tengono traccia delle modifiche dei valori nel tempo e segnalano quelle in corso sotto forma di punteggi delle anomalie. Non richiedono la sintonizzazione della soglia ad hoc e i punteggi possono essere utilizzati per controllare la frequenza falsa positiva. L'API di rilevamento anomalie è utile in diversi scenari, come il monitoraggio del servizio che tiene traccia degli indicatori KPI nel tempo, il monitoraggio dell'utilizzo mediante metriche, come il numero di ricerche e i numeri di clic, il monitoraggio delle prestazioni nel tempo usando contatori come memoria, CPU, letture di file e così via.

L'offerta per il rilevamento anomalie include strumenti utili per iniziare.

* L'[applicazione Web](http://anomalydetection-aml.azurewebsites.net/) consente di valutare e visualizzare i risultati delle API di rilevamento anomalie sui dati.

* Il [codice di esempio](http://adresultparser.codeplex.com/) illustra come accedere all'API a livello di codice e analizzare i risultati in C#.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]


##Definizione dell'API

Il servizio offre un’API basata su REST in HTTPS che può essere usata in modi diversi, ad esempio un’applicazione Web o mobile, R, Python, Excel, ecc. I dati delle serie temporali vengono inviati al servizio tramite una chiamata API REST che esegue una combinazione dei tre tipi di anomalie descritti sopra. Il servizio viene eseguito sulla piattaforma Azure Machine Learning, che consente il ridimensionamento in base alle esigenze aziendali in modo semplice e fornisce i contratti di servizio.

###Headers
Assicurarsi di includere le intestazioni corrette nella richiesta, che dovrebbe essere come la seguente:

	Authorization: Basic <creds>
	Accept: application/json
               
	Where <creds> = ConvertToBase64(“AccountKey:” + yourActualAccountKey);  

È possibile trovare la chiave dell'account relativa al proprio account in [Azure Marketplace](https://datamarket.azure.com/account/keys).

###API Score

Questa API viene usata per eseguire il rilevamento anomalie nei dati delle serie temporali non stagionali. L'API esegue una serie di funzionalità di rilevamento anomalie sui dati e restituisce i punteggi delle anomalie. La figura seguente illustra un esempio di anomalie che l'API Score può rilevare. Questa serie temporale presenta due modifiche di livello distinte e 3 picchi. I punti rossi mostrano il momento in cui viene rilevata la modifica di livello, mentre i punti neri indicano i picchi rilevati.

![API Score][1]
	
**URL**

	https://api.datamarket.azure.com/data.ashx/aml_labs/anomalydetection/v2/Score 

**Corpo della richiesta di esempio**

Nella richiesta seguente viene inviata all'API una serie temporale (appare troncata) con punti dati dall'1/03/2016 al 10/03/2016 e parametri dei rilevamenti di picco, per rilevare se alcuni di questi punti dati risultano anomali.

	{
	  "data": [
	    [ "9/21/2014 11:05:00 AM", "3" ],
	    [ "9/21/2014 11:10:00 AM", "9.09" ],
	    [ "9/21/2014 11:15:00 AM", "0" ]
	  ],
	  "params": {
	    "tspikedetector.sensitivity": "3",
	    "zspikedetector.sensitivity": "3",
	    "trenddetector.sensitivity": "3.25",
	    "bileveldetector.sensitivity": "3.25",
	    "postprocess.tailRows": "2"
	  }
	}

La risposta alla richiesta sarà:

	{
	  "odata.metadata":"https://api.datamarket.azure.com/data.ashx/aml_labs/anomalydetection/v2/$metadata#AnomalyDetection.FrontEndService.Models.AnomalyDetectionResult",
	  "ADOutput":"{
	    "ColumnNames":["Time","Data","TSpike","ZSpike","rpscore","rpalert","tscore","talert"],
		"ColumnTypes":["DateTime","Double","Double","Double","Double","Int32","Double","Int32"],
		"Values":[
		  ["9/21/2014 11:10:00 AM","9.09","0","0","-1.07030497733224","0","-0.884548154298423","0"],
		  ["9/21/2014 11:15:00 AM","0","0","0","-1.05186237440962","0","-1.173800281031","0"]
		]
	  }"
	}

###API ScoreWithSeasonality

L'API ScoreWithSeasonality viene usata per eseguire il rilevamento di anomalie in serie temporali che includono modelli stagionali. Questa API è utile per rilevare le deviazioni nei modelli stagionali.

La figura seguente illustra un esempio di anomalie rilevate in una serie temporale stagionale. La serie temporale presenta un picco (primo punto nero), due flessioni (secondo punto nero e uno alla fine) e una modifica di livello (punto rosso). Si noti che sia la flessione al centro della serie temporale sia la modifica di livello sono rilevabili solo dopo la rimozione dei componenti stagionali dalla serie.

![API Seasonality][2]

**URL**

	https://api.datamarket.azure.com/data.ashx/aml_labs/anomalydetection/v2/ScoreWithSeasonality 

**Corpo della richiesta di esempio**

Nella richiesta seguente viene inviata all'API una serie temporale (appare troncata) con punti dati dall'1/03/2016 al 10/03/2016 e parametri, per rilevare se alcuni di questi punti dati risultano anomali.

	{
	  "data": [
	    [ "9/21/2014 11:10:00 AM", "9" ],
	    [ "9/21/2014 11:15:00 AM", "12" ],
	    [ "9/21/2014 11:20:00 AM", "10" ]
	  ],
	  "params": {
	    "preprocess.aggregationInterval": "0",
	    "preprocess.aggregationFunc": "mean",
	    "preprocess.replaceMissing": "lkv",
	    "postprocess.tailRows": "1",
	    "zspikedetector.sensitivity": "3",
	    "tspikedetector.sensitivity": "3",
	    "upleveldetector.sensitivity": "3.25",
	    "bileveldetector.sensitivity": "3.25",
	    "trenddetector.sensitivity": "3.25",
	    "detectors.historywindow": "500",
	    "seasonality.enable": "true",
	    "seasonality.transform": "deseason",
	    "seasonality.numSeasonality": "1"
	  }
	}

La risposta alla richiesta sarà:

	{
		"odata.metadata": "https://api.datamarket.azure.com/data.ashx/aml_labs/anomalydetection/v2/$metadata#AnomalyDetection.FrontEndService.Models.AnomalyDetectionResult",
		"ADOutput": "{
			"ColumnNames":["Time","OriginalData","ProcessedData","TSpike","ZSpike","PScore","PAlert","RPScore","RPAlert","TScore","TAlert"],
		  	"ColumnTypes":["DateTime","Double","Double","Double","Double","Double","Int32","Double","Int32","Double","Int32"],
		  	"Values":[
		    	["9/21/201411: 20: 00AM","10","10","0","0","-1.30229513613974","0","-1.30229513613974","0","-1.173800281031","0"]
		  	]
		}"
	}

###Funzionalità di rilevamento

L'API di rilevamento anomalie supporta funzionalità di rilevamento in 3 categorie generali. Informazioni dettagliate su specifici parametri di input e output per ogni funzionalità di rilevamento sono disponibili nella tabella seguente.

|Categoria di rilevamento|Funzionalità di rilevamento|Descrizione|Parametri di input|Output
|---|---|---|---|---|
|Rilevamento picchi|Rilevamento picchi TSpike|Rileva picchi e flessioni in base alla sensibilità impostata.|*tspikedetector.sensitivity:* accetta un valore intero compreso nell'intervallo da 1 a 10; predefinito: 3. Maggiore è il valore, minore è la sensibilità.|TSpike: valori binari, '1' se viene rilevato un picco o una flessione. In caso contrario '0'.|
||Rilevamento picchi ZSpike|Rileva picchi e flessioni in base alla sensibilità impostata.|*zspikedetector.sensitivity:* accetta un valore intero compreso nell'intervallo da 1 a 10; predefinito: 3. Maggiore è il valore, minore è la sensibilità|ZSpike: valori binari, '1' se viene rilevato un picco o una flessione. In caso contrario '0'.|
|Rilevamento di tendenza lenta|Rilevamento di tendenza lenta|Rileva la tendenza positiva lenta in base alla sensibilità impostata.|*trenddetector.sensitivity:* soglia relativa al punteggio di rilevamento. Impostazione predefinita: 3,25. Da 3,25 a 5 rappresenta un intervallo di selezione ragionevole. Maggiore è il valore, minore è la sensibilità|tscore: numero mobile che rappresenta il punteggio dell'anomalia nella tendenza|
|Rilevamento della modifica di livello|Rilevamento unidirezionale della modifica di livello|Rileva la modifica di livello verso l'alto in base alla sensibilità impostata.|*upleveldetector.sensitivity:* soglia relativa al punteggio di rilevamento. Impostazione predefinita: 3,25. Da 3,25 a 5 rappresenta un intervallo di selezione ragionevole. Maggiore è il valore, minore è la sensibilità|pscore: numero mobile che rappresenta il punteggio dell'anomalia nella modifica di livello verso l'alto|
||Rilevamento bidirezionale della modifica di livello|Rileva la modifica di livello verso l'alto e verso il basso in base alla sensibilità impostata.|*bileveldetector.sensitivity:* soglia relativa al punteggio di rilevamento. Impostazione predefinita: 3,25. Da 3,25 a 5 rappresenta un intervallo di selezione ragionevole. Maggiore è il valore, minore è la sensibilità|RPScore: numero mobile che rappresenta il punteggio dell'anomalia nella modifica di livello verso l'alto e verso il basso

###Parametri

Informazioni più dettagliate su questi parametri di input sono elencate nella tabella seguente:

|Parametri di input|Descrizione|Impostazione predefinita|Tipo|Intervallo valido|Intervallo consigliato|
|---|---|---|---|---|---|
|preprocess.aggregationInterval|Intervallo in secondi per l'aggregazione di serie temporali di input.|0, non viene eseguita alcuna aggregazione.|integer|0: ignora l'aggregazione. In caso contrario > 0.|Da 5 minuti a 1 giorno, in base alle serie temporali.
|preprocess.aggregationFunc|Funzione usata per aggregare i dati nel parametro AggregationInterval specificato.|mean|enumerato|mean, sum, length|N/D|
|preprocess.replaceMissing|Valori usati per l'attribuzione dei dati mancanti.|lkv (ultimo valore noto)|enumerato|zero, lkv, mean|N/D|
|detectors.historyWindow|Cronologia, in numero di punti dati, usata per il calcolo del punteggio delle anomalie.|500|integer|10-2000|In base alle serie temporali.|
|upleveldetector.sensitivity|Sensibilità per il rilevamento delle modifiche di livello verso l'alto. |3\.25|double|None|Da 3,25 a 5. Minori sono i valori, maggiore è la sensibilità.|
|bileveldetector.sensitivity|Sensibilità per il rilevamento delle modifiche di livello bidirezionali. |3\.25|double|None|Da 3,25 a 5. Minori sono i valori, maggiore è la sensibilità.|
|trenddetector.sensitivity|Sensibilità per il rilevamento di tendenza positiva. |3\.25|double|None|Da 3,25 a 5. Minori sono i valori, maggiore è la sensibilità.|
|tspikedetector.sensitivity |Sensibilità per il rilevamento di picchi TSpike.|3|integer|1-10|Da 3 a 5. Minori sono i valori, maggiore è la sensibilità.|
|zspikedetector.sensitivity |Sensibilità per il rilevamento di picchi ZSpike|3|integer|1-10 |Da 3 a 5. Minori sono i valori, maggiore è la sensibilità.|
|seasonality.enable|Se è necessario eseguire analisi di stagionalità.|true|boolean|true, false|In base alle serie temporali.|
|seasonality.numSeasonality |Numero massimo di cicli periodici da rilevare.|1|integer|1, 2|Da 1 a 2|
|seasonality.transform |Se i componenti stagionali (e) di tendenza devono essere rimossi prima di applicare il rilevamento delle anomalie.|deseason|enumerato|none, deseason, deseasontrend|N/D|
|postprocess.tailRows |Numero di punti dati più recenti da mantenere nei risultati di output.|0|integer|0 (mantiene tutti i punti dati) o specificare il numero di punti da mantenere nei risultati.|N/D|

###Output
L'API esegue tutte le funzionalità di rilevamento sui dati delle serie temporali e restituisce i punteggi delle anomalie e gli indicatori di picco binari per ogni punto temporizzato. La tabella seguente include l'elenco di output dell'API.

|Output|Descrizione|
|---|---|
|Time|Timestamp di dati non elaborati o dati aggregati (e/o) attribuiti se viene applicata l'aggregazione (e/o) l'attribuzione di dati mancanti.|
|OriginalData|Valori di dati non elaborati o dati aggregati (e/o) attribuiti se viene applicata l'aggregazione (e/o) l'attribuzione di dati mancanti.|
|ProcessedData|Uno dei seguenti: <ul><li>Serie temporali regolate in base alla stagionalità, se è stata rilevata una stagionalità significativa ed è selezionata l'opzione deseason.</li><li>Serie temporali regolate in base alla stagionalità e senza tendenza, se è stata rilevata una stagionalità significativa ed è selezionata l'opzione deseasontrend.</li><li>In caso contrario, è uguale a OriginalData.</li>|
|Tspike|Indicatore binario per indicare se viene rilevato un picco dalla funzionalità di rilevamento di TSpike.|
|Zspike|Indicatore binario per indicare se viene rilevato un picco dalla funzionalità di rilevamento di ZSpike.|
|Pscore|Numero mobile che rappresenta il punteggio dell'anomalia nella modifica di livello verso l'alto.|
|Palert|Valore 1/0 che indica la presenza di un'anomalia nella modifica di livello verso l'alto, in base alla sensibilità di input.|
|RPScore|Numero mobile che rappresenta il punteggio dell'anomalia nella modifica di livello bidirezionale.|
|RPAlert|Valore 1/0 che indica la presenza di un'anomalia nella modifica di livello bidirezionale, in base alla sensibilità di input.|
|TScore|Numero mobile che rappresenta il punteggio dell'anomalia nella tendenza positiva.|
|TAlert|Valore 1/0 che indica la presenza di un'anomalia nella tendenza positiva, in base alla sensibilità di input.|


Questo output può essere analizzato con un [semplice parser](https://adresultparser.codeplex.com/), che include codice di esempio che illustra come connettersi all'API e analizzare l'output. Le anomalie rilevate possono essere visualizzate in un dashboard e/o passate a utenti esperti per l'esecuzione di azioni correttive o integrate in sistemi di creazione di ticket.


[1]: ./media/machine-learning-apps-anomaly-detection/anomaly-detection-score.png
[2]: ./media/machine-learning-apps-anomaly-detection/anomaly-detection-seasonal.png

 

 

<!---HONumber=AcomDC_0914_2016-->