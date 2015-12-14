<properties 
	pageTitle="Studio della soluzione di analisi dei dati di telemetria del veicolo: Approfondimento della soluzione | Microsoft Azure" 
	description="Usare le funzionalità di Cortana Analytics per ottenere informazioni dettagliate predittive e in tempo reale sul funzionamento del veicolo e sulle abitudini di guida." 
	services="machine-learning" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/02/2015" 
	ms.author="bradsev" />


# Studio della soluzione di analisi dei dati di telemetria del veicolo: Approfondimento della soluzione

Questo **menu** contiene i collegamenti alle sezioni dello studio:

[AZURE.INCLUDE [cap-vehicle-telemetry-playbook-selector](../../includes/cap-vehicle-telemetry-playbook-selector.md)]

Questa sezione approfondisce nel dettaglio ognuna delle fasi rappresentate nell'architettura della soluzione, con istruzioni e indicazioni per la personalizzazione.

## Origini dati

La soluzione usa due origini dati diverse:

- **set di dati di diagnostica e segnali del veicolo simulati** e 
- **catalogo dei veicoli**

Nella soluzione è incluso un simulatore di dati telematici relativi al veicolo. Questo genera informazioni di diagnostica e segnali corrispondenti allo stato del veicolo e allo stile di guida in un determinato momento. Fare clic [qui](http://go.microsoft.com/fwlink/?LinkId=717075) per scaricare la **soluzione Vehicle Telematics Simulator di Visual Studio** ed eseguire le personalizzazioni necessarie. Il catalogo dei veicoli contiene un set di dati di riferimento con il numero identificativo del veicolo (NIV) per il mapping del modello.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig2-vehicle-telematics-simulator.png)

*Figura 2: Vehicle Telematics Simulator*

Si tratta di un set di dati in formato JSON che contiene lo schema riportato di seguito.

Colonna | Descrizione | Valori |  
------- | ----------- | --------- | 
vin | Numero identificativo del veicolo generato in modo casuale | Viene ottenuto da un elenco master di 10.000 numeri identificativi di veicoli generati in modo casuale | 
outsideTemperature | Temperatura all'esterno durante la guida del veicolo | Numero da 0 a 100 generato in modo casuale | 
engineTemperature | Temperatura del motore del veicolo | Numero da 0 a 500 generato in modo casuale | 
speed | Velocità del motore durante la guida del veicolo | Numero da 0 a 100 generato in modo casuale | 
fuel | Livello di carburante del veicolo | Numero da 0 a 100 generato in modo casuale (indica la percentuale del livello di carburante) | 
engineoil | Livello dell'olio del motore del veicolo | Numero da 0 a 100 generato in modo casuale (indica la percentuale del livello di olio del motore) | 
tirepressure | Pressione degli pneumatici del veicolo | Numero da 0 a 50 generato in modo casuale (indica la percentuale del livello di pressione degli pneumatici) | 
odometer | Lettura del contachilometri del veicolo | Numero da 0 a 200000 generato in modo casuale | 
accelerator\_pedal\_position | Posizione del pedale dell'acceleratore del veicolo | Numero da 0 a 100 generato in modo casuale (indica la percentuale del livello dell'acceleratore) | 
parking\_brake\_status | Indica se il freno di stazionamento è attivato o meno | true o false | 
headlamp\_status | Indica se il fanale anteriore è acceso o meno | true o false | 
brake\_pedal\_status | Indica se il pedale del freno è premuto o meno | true o false | 
transmission\_gear\_position | Posizione del cambio del veicolo | Stati: first, second, third, fourth, fifth, sixth, seventh, eighth | 
ignition\_status | Indica se il veicolo è acceso o meno | true o false | 
windshield\_wiper\_status | Indica se il tergicristallo è attivato o meno | true o false | 
abs | Indica se l'ABS è attivato o meno | true o false | 
timestamp | Timestamp di creazione del punto dati | Data | 
city | Località in cui si trova il veicolo | 4 città in questa soluzione: Bellevue, Redmond, Sammamish, Seattle | 


Il set di dati di riferimento del modello di veicolo contiene il mapping del numero identificativo del veicolo al modello.

NIV | Modello |
--------------|------------------
FHL3O1SA4IEHB4WU1 | Berlina |
8J0U8XCPRGW4Z3NQE | Ibrido |
WORG68Z2PLTNZDBI7 | Berlina familiare |
JTHMYHQTEPP4WBMRN | Berlina |
W9FTHG27LZN1YWO0Y | Ibrido |
MHTP9N792PHK08WJM | Berlina familiare |
EI4QXI2AXVQQING4I | Berlina |
5KKR2VB4WHQH97PF8 | Ibrido |
W9NSZ423XZHAONYXB | Berlina familiare |
26WJSGHX4MA5ROHNL | Decappottabile |
GHLUB6ONKMOSI7E77 | Station Wagon |
9C2RHVRVLMEJDBXLP | Utilitaria |
BRNHVMZOUJ6EOCP32 | SUV di piccole dimensioni |
VCYVW0WUZNBTM594J | Auto sportiva |
HNVCE6YFZSA5M82NY | SUV di medie dimensioni |
4R30FOR7NUOBL05GJ | Station Wagon |
WYNIIY42VKV6OQS1J | SUV di grandi dimensioni |
8Y5QKG27QET1RBK7I | SUV di grandi dimensioni |
DF6OX2WSRA6511BVG | Coupé |
Z2EOZWZBXAEW3E60T | Berlina |
M4TV6IEALD5QDS3IR | Ibrido |
VHRA1Y2TGTA84F00H | Berlina familiare |
R0JAUHT1L1R3BIKI0 | Berlina |
9230C202Z60XX84AU | Ibrido |
T8DNDN5UDCWL7M72H | Berlina familiare |
4WPYRUZII5YV7YA42 | Berlina |
D1ZVY26UV2BFGHZNO | Ibrido |
XUF99EW9OIQOMV7Q7 | Berlina familiare
8OMCL3LGI7XNCC21U | Decappottabile |
……. | |


### Per generare dati simulati
1.	Fare clic sulla freccia nell'angolo superiore destro sul nodo Vehicle Telematics Simulator per scaricare il pacchetto del simulatore di dati. Salvare ed estrarre i file in locale nel computer. ![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig3-vehicle-telemetry-blueprint.png) *Figura 3: Schema della soluzione di analisi dei dati di telemetria del veicolo*

2.	Nel computer locale, passare alla cartella in cui è stato estratto il pacchetto Vehicle Telematics Simulator. ![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig4-vehicle-telematics-simulator-folder.png)*Figura 4: Cartella Vehicle Telematics Simulator*

3.	Eseguire l'applicazione **CarEventGenerator.exe**.

### Riferimenti
[Soluzione Vehicle Telematics Simulator di Visual Studio](http://go.microsoft.com/fwlink/?LinkId=717075) [Hub eventi di Azure](http://azure.microsoft.com/services/event-hubs/) [Data factory di Azure](https://azure.microsoft.com/documentation/learning-paths/data-factory/)


## Inserimento
Vengono sfruttate combinazioni di Hub eventi, Analisi di flusso e Data factory di Azure per inserire i segnali del veicolo, gli eventi di diagnostica e le analisi in tempo reale e batch. Tutti questi componenti vengono creati e configurati durante la distribuzione della soluzione.

### Analisi in tempo reale
Gli eventi generati dal simulatore di dati telematici relativi al veicolo vengono pubblicati nell'Hub eventi usando l'SDK di Hub di eventi. Il processo di Analisi di flusso inserisce gli eventi dall'Hub eventi ed elabora i dati in tempo reale per analizzare l'integrità del veicolo.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig5-vehicle-telematics-event-hub-dashboard.png)

*Figura 5: Dashboard di Hub eventi*

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig6-vehicle-telematics-stream-analytics-job-processing-data.png)

*Figura 6: Elaborazione dei dati da parte del processo di Analisi di flusso*

Il processo di Analisi di flusso inserisce i dati dall'Hub eventi, crea un join con i dati di riferimento per il mapping del numero identificativo del veicolo al modello corrispondente e li rende persistenti nell'archivio BLOB di Azure per l'analisi batch avanzata. La query di Analisi di flusso riportata di seguito viene usata per rendere persistenti i dati nell'archivio BLOB di Azure.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig7-vehicle-telematics-stream-analytics-job-query-for-data-ingestion.png)

*Figura 7: Query del processo di Analisi di flusso per l'inserimento di dati*

### Analisi batch
Viene anche generato un volume aggiuntivo di set di dati di diagnostica e segnali del veicolo simulati per rendere più completa l'analisi batch. Questo è necessario per garantire un volume di dati rappresentativo per l'elaborazione batch. A questo scopo, viene usata una pipeline denominata "PrepareSampleDataPipeline" nel flusso di lavoro di Data factory di Azure per generare un set di dati di diagnostica e segnali del veicolo simulati equivalente a un anno. Fare clic [qui](http://go.microsoft.com/fwlink/?LinkId=717077) per scaricare la soluzione di Visual Studio per l'attività .Net personalizzata di Data factory ed eseguire le personalizzazioni necessarie.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig8-vehicle-telematics-prepare-sample-data-for-batch-processing.png)

*Figura 8: Preparazione dei dati di esempio per il flusso di lavoro dell'elaborazione batch*

La pipeline è costituita da un'attività .Net personalizzate di Data factory di Azure, mostrata di seguito:

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig9-vehicle-telematics-prepare-sample-data-pipeline.png)

*Figura 9: PrepareSampleDataPipeline*

Dopo aver eseguito correttamente la pipeline e aver contrassegnato come "Ready" il set di dati "RawCarEventsTable", viene prodotto un set di dati di diagnostica e segnali del veicolo simulati equivalente a un anno. La cartella e il file seguenti verranno creati nell'account di archiviazione sotto il contenitore "connectedcar".

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig10-vehicle-telematics-prepare-sample-data-pipeline-output.png)

*Figura 10: Output di PrepareSampleDataPipeline*

### Riferimenti
[SDK dell'Hub eventi di Azure per l'inserimento dei flussi](event-hubs-csharp-ephcs-getstarted.md), [funzionalità di spostamento dei dati di Data factory di Azure](data-factory-data-movement-activities.md), [attività .Net di Data factory di Azure](data-factory-use-custom-activities.md), [soluzione di Visual Studio per l'attività .Net di Data factory di Azure per la preparazione dei dati di esempio](http://go.microsoft.com/fwlink/?LinkId=717077).


## Preparazione
>[AZURE.ALERT]Questo passaggio della soluzione è applicabile solo all'elaborazione batch.

Il set di dati di diagnostica e segnali del veicolo semistrutturato non elaborato viene partizionato nel passaggio di preparazione dei dati in un formato YEAR/MONTH per l'esecuzione di query efficienti e l'archiviazione scalabile a lungo termine. Ciò significa che viene abilitato il passaggio da un account BLOB al successivo quando il primo è pieno. I dati di output (con etichetta *PartitionedCarEventsTable*) devono essere conservati per un lungo periodo di tempo nella forma di base meno elaborata dei dati nel "Data Lake" del cliente. I dati di input per questa pipeline vengono in genere eliminati perché i dati di output hanno la massima fedeltà all'input, sono semplicemente archiviati (partizionati) meglio per un uso successivo.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig11-vehicle-telematics-partition-car-events-workflow.png)

*Figura 11: Flusso di lavoro PartitionCarEvents*

I dati non elaborati vengono partizionati usando un'attività Hive HDInsight in "PartitionCarEventsPipeline". L'anno di dati di esempio generato nel passaggio 1 viene partizionato in formato YEAR/MONTH per generare partizioni di dati di diagnostica e segnali del veicolo corrispondenti a ogni mese dell'anno (per un totale di 12 partizioni).

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig12-vehicle-telematics-partition-car-events-pipeline.png)

*Figura 12: PartitionCarEventsPipeline*

Lo script Hive illustrato di seguito, denominato "partitioncarevents.hql", è usato per il partizionamento e si trova nella cartella "\\demo\\src\\connectedcar\\scripts" del file ZIP scaricato.

	SET hive.exec.dynamic.partition=true;
	SET hive.exec.dynamic.partition.mode = nonstrict;
	set hive.cli.print.header=true;

	DROP TABLE IF EXISTS RawCarEvents; 
	CREATE EXTERNAL TABLE RawCarEvents 
	(
            	vin								string,
				model							string,
				timestamp						string,
				outsidetemperature				string,
				enginetemperature				string,
				speed							string,
				fuel							string,
				engineoil						string,
				tirepressure					string,
				odometer						string,
				city							string,
				accelerator_pedal_position		string,
				parking_brake_status			string,
				headlamp_status					string,
				brake_pedal_status				string,
				transmission_gear_position		string,
				ignition_status					string,
				windshield_wiper_status			string,
				abs  							string,
				gendate							string
                
	) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:RAWINPUT}'; 

	DROP TABLE IF EXISTS PartitionedCarEvents; 
	CREATE EXTERNAL TABLE PartitionedCarEvents 
	(
            	vin								string,
				model							string,
				timestamp						string,
				outsidetemperature				string,
				enginetemperature				string,
				speed							string,
				fuel							string,
				engineoil						string,
				tirepressure					string,
				odometer						string,
				city							string,
				accelerator_pedal_position		string,
				parking_brake_status			string,
				headlamp_status					string,
				brake_pedal_status				string,
				transmission_gear_position		string,
				ignition_status					string,
				windshield_wiper_status			string,
				abs  							string,
				gendate							string
	) partitioned by (YearNo int, MonthNo int) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:PARTITIONEDOUTPUT}';

	DROP TABLE IF EXISTS Stage_RawCarEvents; 
	CREATE TABLE IF NOT EXISTS Stage_RawCarEvents 
	(
            	vin								string,
				model							string,
				timestamp						string,
				outsidetemperature				string,
				enginetemperature				string,
				speed							string,
				fuel							string,
				engineoil						string,
				tirepressure					string,
				odometer						string,
				city							string,
				accelerator_pedal_position		string,
				parking_brake_status			string,
				headlamp_status					string,
				brake_pedal_status				string,
				transmission_gear_position		string,
				ignition_status					string,
				windshield_wiper_status			string,
				abs  							string,
				gendate							string,
				YearNo 							int,
				MonthNo 						int) 
	ROW FORMAT delimited fields terminated by ',' LINES TERMINATED BY '10';

	INSERT OVERWRITE TABLE Stage_RawCarEvents
	SELECT
		vin,			
		model,
		timestamp,
		outsidetemperature,
		enginetemperature,
		speed,
		fuel,
		engineoil,
		tirepressure,
		odometer,
		city,
		accelerator_pedal_position,
		parking_brake_status,
		headlamp_status,
		brake_pedal_status,
		transmission_gear_position,
		ignition_status,
		windshield_wiper_status,
		abs,
		gendate,
		Year(gendate),
		Month(gendate)

	FROM RawCarEvents WHERE Year(gendate) = ${hiveconf:Year} AND Month(gendate) = ${hiveconf:Month}; 

	INSERT OVERWRITE TABLE PartitionedCarEvents PARTITION(YearNo, MonthNo) 
	SELECT
		vin,			
		model,
		timestamp,
		outsidetemperature,
		enginetemperature,
		speed,
		fuel,
		engineoil,
		tirepressure,
		odometer,
		city,
		accelerator_pedal_position,
		parking_brake_status,
		headlamp_status,
		brake_pedal_status,
		transmission_gear_position,
		ignition_status,
		windshield_wiper_status,
		abs,
		gendate,
		YearNo,
		MonthNo
	FROM Stage_RawCarEvents WHERE YearNo = ${hiveconf:Year} AND MonthNo = ${hiveconf:Month};

*Figura 13: Script Hive PartitionConnectedCarEvents*

Dopo la corretta esecuzione della pipeline, vengono visualizzate le partizioni seguenti generate nell'account di archiviazione sotto il contenitore "connectedcar".

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig14-vehicle-telematics-partitioned-output.png)

*Figura 14: Output partizionato*

A questo punto, i dati sono ottimizzati, più gestibili e pronti per un'ulteriore elaborazione che permette di ottenere informazioni dettagliate sul batch.

## Analisi dei dati

In questa sezione viene illustrato l'uso combinato di Analisi di flusso di Azure, Azure Machine Learning, Data factory di Azure e Azure HDInsight per un'analisi avanzata dell'integrità del veicolo e delle abitudini di guida. La sezione è divisa in 3 sottosezioni:

1.	**Machine Learning**: questa sottosezione contiene informazioni sull'esperimento di rilevamento delle anomalie usato nella soluzione per eseguire una stima dei veicoli che richiedono interventi di manutenzione e dei veicoli da richiamare a causa di problemi di sicurezza.
2.	**Analisi in tempo reale**: questa sottosezione contiene informazioni riguardanti l'analisi in tempo reale con il linguaggio di query di Analisi di flusso e la messa in funzione dell'esperimento di Machine Learning in tempo reale con un'applicazione personalizzata.
3.	**Analisi batch**: questa sottosezione contiene informazioni sulla trasformazione e l'elaborazione dei dati batch con Azure HDInsight e Azure Machine Learning messi in funzione da Data factory di Azure.

### Machine Learning

L'obiettivo è eseguire una stima dei veicoli che richiedono interventi di manutenzione e dei veicoli da richiamare in base a determinate statistiche di integrità. Si parte dai presupposti seguenti:

- Il veicolo richiede un **intervento di manutenzione** se una delle tre condizioni seguenti è vera:
	- La pressione degli pneumatici è bassa
	- Il livello di olio del motore è basso
	- La temperatura del motore è elevata

- Il veicolo può avere un **problema di sicurezza** e può essere necessario il **richiamo** se una delle condizioni seguenti è vera:
	- La temperatura del motore è elevata, ma la temperatura esterna è bassa
	- La temperatura del motore è bassa, ma la temperatura esterna è elevata

In base ai suddetti requisiti, sono stati creati due modelli separati per il rilevamento delle anomalie, uno per gli interventi di manutenzione sul veicolo e uno per il richiamo del veicolo. In entrambi i modelli l'algoritmo di analisi in componenti principali (PCA) incorporato viene usato per il rilevamento di anomalie.

**Modello di rilevamento per la manutenzione**: il modello di rilevamento per la manutenzione segnala un'anomalia se uno dei tre indicatori (pressione degli pneumatici, olio del motore o temperatura del motore) soddisfa la condizione corrispondente. Di conseguenza, nella compilazione del modello è sufficiente prendere in considerazione queste tre variabili. Nell'esperimento in Azure Machine Learning viene usato prima di tutto il modulo **Project Columns** per estrarre queste tre variabili. Viene quindi usato il modulo di rilevamento delle anomalie basato su PCA per compilare il modello di rilevamento delle anomalie.

L'analisi in componenti principali (PCA) è una tecnica consolidata in Machine Learning che può essere applicata alla selezione di funzionalità, alla classificazione e al rilevamento di anomalie. Converte un set di casi contenente variabili probabilmente correlate in un set di valori denominati componenti principali. Lo scopo primario del modello basato su PCA è la proiezione dei dati in uno spazio dimensionale inferiore in modo che caratteristiche e anomalie siano identificabili più facilmente.
 
Nel rilevamento delle anomalie, per ogni nuovo input, il rilevatore calcola prima di tutto la proiezione sugli autovettori e quindi calcola l'errore di ricostruzione normalizzato. L'errore normalizzato costituisce il punteggio dell'anomalia. A un punteggio maggiore corrisponde una maggiore anomalia dell'istanza.

Nel rilevamento per la manutenzione ogni record può essere considerato come un punto in uno spazio tridimensionale definito dalle coordinate pressione degli pneumatici, olio del motore e temperatura del motore. Per acquisire queste anomalie, è possibile usare l'analisi in componenti principali per proiettare i dati originali tridimensionali in uno spazio bidimensionale. Il parametro relativo al numero di componenti da usare in PCA viene quindi impostato su 2. Questo parametro ha un ruolo importante nell'applicazione del rilevamento delle anomalie basato su PCA. Dopo aver eseguito la proiezione dei dati con l'analisi PCA, è possibile identificare più facilmente queste anomalie.

**Modello di rilevamento delle anomalie per il richiamo**: in questo modello i moduli di rilevamento delle anomalie basati su PCA e Project Columns vengono usati in modo analogo. Nello specifico, vengono prima di tutto estratte le tre variabili (temperatura del motore, temperatura esterna e velocità) usando il modulo **Project Columns**. Viene inclusa anche la variabile velocità perché la temperatura del motore è in genere correlata alla velocità. Viene quindi usato il modulo di rilevamento delle anomalie basato su PCA per proiettare i dati da uno spazio tridimensionale a uno spazio bidimensionale. I criteri di richiamo sono soddisfatti ed è quindi necessario richiamare il veicolo quando la temperatura del motore e quella esterna sono correlate negativamente. Con un algoritmo di rilevamento delle anomalie basato su PCA è possibile acquisire le anomalie dopo l'analisi PCA.

Si noti che per il training del modello di rilevamento delle anomalie basato su PCA è sempre necessario usare come dati di input dati normali che non richiedano il richiamo o interventi di manutenzione. Nell'esperimento di assegnazione dei punteggi viene usato il modello di rilevamento di anomalie sottoposto a training per rilevare se il veicolo richiede la manutenzione o il richiamo.


### Analisi in tempo reale

La query SQL di Analisi di flusso riportata di seguito viene usata per ottenere la media di tutti i parametri importanti del veicolo, come la velocità, il livello di carburante, la temperatura del motore, la lettura del contachilometri, la pressione degli pneumatici, il livello di olio del motore e così via, per rilevare anomalie, emettere avvisi e determinare le condizioni generali di integrità dei veicoli usati in un'area specifica correlando tali informazioni a dati demografici.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig15-vehicle-telematics-stream-analytics-query-for-real-time-processing.png)

Figura 15: Query di Analisi di flusso per l'elaborazione in tempo reale

Tutte le medie vengono calcolate in una finestra a cascata di 3 secondi. In questo caso viene usata la finestra a cascata perché sono necessari intervalli di tempo contigui e non sovrapposti.

Per altre informazioni sulle funzionalità delle finestre in Analisi di flusso di Azure, fare clic [qui](https://msdn.microsoft.com/library/azure/dn835019.aspx).

**Stima in tempo reale**

Nella soluzione è inclusa un'applicazione per la messa in funzione del modello di Machine Learning in tempo reale. L'applicazione, denominata "RealTimeDashboardApp" viene creata e configurata nell'ambito della distribuzione della soluzione ed esegue le operazioni descritte di seguito:

1.	È in ascolto di un'istanza di Hub eventi in cui Analisi di flusso pubblica gli eventi in modo continuo. ![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig16-vehicle-telematics-stream-analytics-query-for-publishing.png)*Figura 16: Query di Analisi di flusso per la pubblicazione dei dati in un'istanza di output di Hub eventi* 

2.	Per ogni evento ricevuto dall'applicazione:

	- i dati vengono elaborati usando l'endpoint del servizio di richiesta-risposta (RRS) di Machine Learning per l'assegnazione dei punteggi, l'endpoint RRS viene pubblicato automaticamente nell'ambito della distribuzione.
	- L'output RRS viene pubblicato in un set di dati di Power BI con le API push.

Questo modello è anche applicabile a scenari di integrazione di un'applicazione line-of-business con il flusso di analisi in tempo reale per avvisi, notifiche, messaggistica immediata e così via.

Fare clic [qui](http://go.microsoft.com/fwlink/?LinkId=717078) per scaricare la soluzione RealtimeDashboardApp di Visual Studio per le personalizzazioni.

****Per eseguire l'applicazione dashboard in tempo reale**

1.	Fare clic sul nodo Power BI nella vista diagramma e fare clic sul collegamento "Download Real-time Dashboard Application" nel riquadro delle proprietà. ![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig17-vehicle-telematics-powerbi-dashboard-setup.png) *Figura 17: Istruzioni per la configurazione del dashboard di Power BI*
2.	Estrarre e salvare in locale. ![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig18-vehicle-telematics-realtimedashboardapp-folder.png) *Figura 18: Cartella RealtimeDashboardApp*
3.	Eseguire l'applicazione RealtimeDashboardApp.exe.
4.	Fornire credenziali Power BI valide, accedere e fare clic su Accept. ![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig19a-vehicle-telematics-realtimedashboardapp-sign-in-to-powerbi.png) ![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig19b-vehicle-telematics-realtimedashboardapp-sign-in-to-powerbi.png) 

*Figura 19: Accesso a Power BI da RealtimeDashboardApp*

>[AZURE.NOTE]Nota: per scaricare il set di dati di Power BI, eseguire RealtimeDashboardApp con il parametro "flushdata":

	RealtimeDashboardApp.exe -flushdata

### Analisi batch

L'obiettivo di questa sezione è mostrare come Contoso Motors usa le capacità di calcolo di Azure per sfruttare i Big Data e ottenere informazioni dettagliate sugli stili di guida, sull'utilizzo e sull'integrità del veicolo per:

- Migliorare l'esperienza del cliente e abbassare i costi fornendo informazioni dettagliate sulle abitudini e sui comportamenti di guida attenti ai consumi.
- Acquisire informazioni sui clienti e sui relativi stili di guida per gestire il processo decisionale e fornire prodotti e servizi di altissimo livello.

In questa soluzione vengono esaminate le metriche seguenti:

1.	**Stile di guida aggressivo**: identifica la tendenza di modelli, località, condizioni di guida e periodo dell'anno per ottenere informazioni dettagliate sul comportamento di guida aggressivo che Contoso Motors può usare per creare campagne di marketing, nuove funzionalità personalizzate e assicurazioni basate sull'utilizzo.
2.	**Stile di guida attento ai consumi**: identifica la tendenza di modelli, località, condizioni di guida e periodo dell'anno per ottenere informazioni dettagliate sul comportamento di guida attento ai consumi che Contoso Motors può usare per creare campagne di marketing e nuove funzionalità personalizzate e per segnalare al guidatore abitudini di guida attente ai consumi e all'ambiente. 
3.	**Previsioni di richiamo**: identifica i modelli che è necessario richiamare in base all'esperimento di Machine Learning per il rilevamento delle anomalie.

Di seguito vengono esaminate le singole metriche nel dettaglio.


**Stile di guida aggressivo**

I segnali del veicolo e i dati di diagnostica partizionati vengono elaborati nella pipeline denominata "AggresiveDrivingPatternPipeline" che usa Hive per determinare modelli, località, condizioni di guida e dei veicoli e così via, che mostrano uno stile di guida aggressivo.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig20-vehicle-telematics-aggressive-driving-pattern.png) *Figura 20: Flusso di lavoro per lo stile di guida aggressivo*

Lo script Hive denominato "aggresivedriving.hql" usato per l'analisi dello stile di guida aggressivo si trova nella cartella "\\demo\\src\\connectedcar\\scripts" del file ZIP scaricato.

	DROP TABLE IF EXISTS PartitionedCarEvents; 
	CREATE EXTERNAL TABLE PartitionedCarEvents
	(
            	vin								string,
				model							string,
				timestamp						string,
				outsidetemperature				string,
				enginetemperature				string,
				speed							string,
				fuel							string,
				engineoil						string,
				tirepressure					string,
				odometer						string,
				city							string,
				accelerator_pedal_position		string,
				parking_brake_status			string,
				headlamp_status					string,
				brake_pedal_status				string,
				transmission_gear_position		string,
				ignition_status					string,
				windshield_wiper_status			string,
				abs  							string,
				gendate							string
                                
	) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:PARTITIONEDINPUT}';

	DROP TABLE IF EXISTS CarEventsAggresive; 
	CREATE EXTERNAL TABLE CarEventsAggresive
	(
               	vin 						string, 
				model						string,
                timestamp					string,
				city						string,
				speed 			 			string,
				transmission_gear_position	string,
				brake_pedal_status			string,
				Year						string,
				Month						string
                                
	) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:AGGRESIVEOUTPUT}';



	INSERT OVERWRITE TABLE CarEventsAggresive
	select
	vin,
	model,
	timestamp,
	city,
	speed,
	transmission_gear_position,
	brake_pedal_status,
	"${hiveconf:Year}" as Year,
	"${hiveconf:Month}" as Month
	from PartitionedCarEvents
	where transmission_gear_position IN ('fourth', 'fifth', 'sixth', 'seventh', 'eight') AND brake_pedal_status = '1' AND speed >= '50'

*Figura 21: Query Hive per lo stile di guida aggressivo*

Usa una combinazione di posizione del cambio, stato del pedale del freno e velocità del veicolo per rilevare un comportamento di guida spericolato/aggressivo in base allo stile di frenata ad alta velocità.

Dopo la corretta esecuzione della pipeline, vengono visualizzate le partizioni seguenti generate nell'account di archiviazione sotto il contenitore "connectedcar".

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig22-vehicle-telematics-aggressive-driving-pattern-output.png)

*Figura 22: Output di AggressiveDrivingPatternPipeline*


**Stile di guida attento ai consumi**

I segnali del veicolo e i dati di diagnostica partizionati vengono elaborati nella pipeline denominata "FuelEfficientDrivingPatternPipeline" che usa Hive per determinare modelli, località, condizioni di guida e dei veicoli e così via, che mostrano uno stile di guida attento ai consumi.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig23-vehicle-telematics-fuel-efficient-driving-pattern.png)

*Figura 23: Flusso di lavoro per lo stile di guida attento ai consumi*

Lo script Hive denominato "fuelefficientdriving.hql" usato per l'analisi dello stile di guida attento ai consumi si trova nella cartella "\\demo\\src\\connectedcar\\scripts" del file ZIP scaricato.

	DROP TABLE IF EXISTS PartitionedCarEvents; 
	CREATE EXTERNAL TABLE PartitionedCarEvents
	(
            	vin								string,
				model							string,
				timestamp						string,
				outsidetemperature				string,
				enginetemperature				string,
				speed							string,
				fuel							string,
				engineoil						string,
				tirepressure					string,
				odometer						string,
				city							string,
				accelerator_pedal_position		string,
				parking_brake_status			string,
				headlamp_status					string,
				brake_pedal_status				string,
				transmission_gear_position		string,
				ignition_status					string,
				windshield_wiper_status			string,
				abs  							string,
				gendate							string
                                
	) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:PARTITIONEDINPUT}';

	DROP TABLE IF EXISTS FuelEfficientDriving; 
	CREATE EXTERNAL TABLE FuelEfficientDriving
	(
               	vin 						string, 
				model						string,
               	city						string,
				speed 			 			string,
				transmission_gear_position	string,                
				brake_pedal_status			string,            
				accelerator_pedal_position	string,                             
				Year						string,
				Month						string
                                
	) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:FUELEFFICIENTOUTPUT}';



	INSERT OVERWRITE TABLE FuelEfficientDriving
	select
	vin,
	model,
	city,
	speed,
	transmission_gear_position,
	brake_pedal_status,
	accelerator_pedal_position,
	"${hiveconf:Year}" as Year,
	"${hiveconf:Month}" as Month
	from PartitionedCarEvents
	where transmission_gear_position IN ('fourth', 'fifth', 'sixth', 'seventh', 'eight') AND parking_brake_status = '0' AND brake_pedal_status = '0' AND speed <= '60' AND accelerator_pedal_position >= '50'


*Figura 24: Query Hive per lo stile di guida attento ai consumi*

Usa una combinazione di posizione del cambio, stato del pedale del freno, velocità del veicolo e posizione del pedale dell'acceleratore per rilevare un comportamento di guida attento ai consumi in base allo stile di accelerazione e frenata e in base alla velocità.

Dopo la corretta esecuzione della pipeline, vengono visualizzate le partizioni seguenti generate nell'account di archiviazione sotto il contenitore "connectedcar".

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig25-vehicle-telematics-fuel-efficient-driving-pattern-output.png)

*Figura 25: Output di FuelEfficientDrivingPatternPipeline*


**Previsioni di richiamo**

L'esperimento di Machine Learning è stato sottoposto a provisioning e pubblicato come servizio Web nell'ambito della distribuzione della soluzione. L'endpoint di assegnazione punteggio batch viene usato all'interno di questo flusso di lavoro, registrato come servizio collegato di Data factory e messo in funzione con l'attività di assegnazione punteggio batch di Data factory.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig26-vehicle-telematics-machine-learning-endpoint.png)

*Figura 26: Endpoint di Machine Learning registrato come servizio collegato in Data factory*

Il servizio collegato registrato viene usato in DetectAnomalyPipeline per assegnare un punteggio ai dati con il modello di rilevamento delle anomalie.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig27-vehicle-telematics-aml-batch-scoring.png)

*Figura 27: Attività di assegnazione punteggio batch di Azure Machine Learning in Data factory*

Alcuni dei passaggi eseguiti in questa pipeline per la preparazione dei dati ne consentono l'uso con il servizio Web di assegnazione punteggio batch.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig28-vehicle-telematics-pipeline-predicting-recalls.png)

*Figura 28: DetectAnomalyPipeline per la stima dei veicoli da richiamare*

Dopo aver completato l'assegnazione dei punteggi, viene usata un'attività HDInsight per elaborare e aggregare i dati categorizzati come anomalie dal modello con un punteggio minimo di probabilità di 0,60.

	DROP TABLE IF EXISTS CarEventsAnomaly; 
	CREATE EXTERNAL TABLE CarEventsAnomaly 
	(
            	vin							string,
				model						string,
				gendate						string,
				outsidetemperature			string,
				enginetemperature			string,
				speed						string,
				fuel						string,
				engineoil					string,
				tirepressure				string,
				odometer					string,
				city						string,
				accelerator_pedal_position	string,
				parking_brake_status		string,
				headlamp_status				string,
				brake_pedal_status			string,
				transmission_gear_position	string,
				ignition_status				string,
				windshield_wiper_status		string,
				abs  						string,
				maintenanceLabel			string,
				maintenanceProbability		string,
				RecallLabel					string,
				RecallProbability			string
                                
	) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:ANOMALYOUTPUT}';

	DROP TABLE IF EXISTS RecallModel; 
	CREATE EXTERNAL TABLE RecallModel 
	(

				vin							string,
				model						string,
				city						string,
				outsidetemperature			string,
				enginetemperature			string,
				speed						string,
            	Year						string,
				Month						string				
                                
	) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:RECALLMODELOUTPUT}';

	INSERT OVERWRITE TABLE RecallModel
	select
	vin,
	model,
	city,
	outsidetemperature,
	enginetemperature,
	speed,
	"${hiveconf:Year}" as Year,
	"${hiveconf:Month}" as Month
	from CarEventsAnomaly
	where RecallLabel = '1' AND RecallProbability >= '0.60'

*Figura 29: Query Hive di aggregazione dei richiami*

Dopo la corretta esecuzione della pipeline, vengono visualizzate le partizioni seguenti generate nell'account di archiviazione sotto il contenitore "connectedcar".

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig30-vehicle-telematics-detect-anamoly-pipeline-output.png)

*Figura 30: Output di DetectAnomalyPipeline*


## Pubblicazione

### Analisi in tempo reale

Una delle query del processo di Analisi di flusso pubblica gli eventi in un'istanza di Hub eventi di output.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig31-vehicle-telematics-stream-analytics-job-publishes-output-event-hub.png)

*Figura 31: Il processo di Analisi di flusso pubblica in un'istanza di Hub eventi di output*

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig32-vehicle-telematics-stream-analytics-query-publish-output-event-hub.png)

*Figura 32: Query di Analisi di flusso per la pubblicazione in un'istanza di Hub eventi di output*

Questo flusso di eventi viene utilizzato dall'applicazione RealTimeDashboardApp inclusa nella soluzione. L'applicazione sfrutta il servizio Web di richiesta-risposta di Machine Learning per l'assegnazione dei punteggi in tempo reale e pubblica i dati risultanti in un set di dati di Power BI per l'utilizzo.

### Analisi batch

I risultati dell'elaborazione batch e in tempo reale vengono pubblicati nelle tabelle di database SQL di Azure per l'utilizzo. Il server SQL Azure, il database e le tabelle vengono creati automaticamente con lo script di installazione.

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig33-vehicle-telematics-batch-processing-results-copy-to-data-mart.png)

*Figura 33: Copia dei risultati dell'elaborazione batch nel flusso di lavoro di data mart*

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig34-vehicle-telematics-stream-analytics-job-publishes-to-data-mart.png)

*Figura 34: Il processo di Analisi di flusso pubblica in data mart*

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig35-vehicle-telematics-data-mart-setting-in-stream-analytics-job.png)

*Figura 35: Impostazione di data mart nel processo di Analisi di flusso*


## Utilizzo

Power BI offre alla soluzione un dashboard completo per la visualizzazione di dati in tempo reale e di analisi predittive.

Fare clic qui per informazioni dettagliate su come configurare i report e il dashboard di Power BI. L'aspetto finale del dashboard è questo:

![](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig36-vehicle-telematics-powerbi-dashboard.png)

*Figura 36: Dashboard di PowerBI*

## Riepilogo

Questo documento contiene un'analisi dettagliata e approfondita della soluzione di analisi dei dati di telemetria del veicolo. Questa presenta un modello di architettura lambda per l'analisi batch e in tempo reale completa di stime e azioni. Il modello si applica a una vasta gamma di casi d'uso che richiedono l'analisi del percorso critico (in tempo reale) e di quello non critico (batch).

<!---HONumber=AcomDC_1203_2015-->