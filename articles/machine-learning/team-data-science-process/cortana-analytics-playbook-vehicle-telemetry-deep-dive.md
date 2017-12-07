---
title: "Approfondimento su come fare una previsione dell'integrità dei veicoli e delle abitudini di guida - Azure | Microsoft Docs"
description: "Usare le funzionalità di Cortana Intelligence per ottenere informazioni dettagliate predittive e in tempo reale sullo stato di integrità del veicolo e sulle abitudini di guida."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: d8866fa6-aba6-40e5-b3b3-33057393c1a8
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/24/2017
ms.author: bradsev
ms.openlocfilehash: a21316ef6ab05918f07a09243b5ce04950ecd9dc
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2017
---
# <a name="vehicle-telemetry-analytics-solution-playbook-deep-dive-into-the-solution"></a>Playbook della Soluzione di analisi dei dati di telemetria del veicolo: approfondimento della soluzione
Questo menu riporta alle sezioni del playbook: 

[!INCLUDE [cap-vehicle-telemetry-playbook-selector](../../../includes/cap-vehicle-telemetry-playbook-selector.md)]

Questo documento approfondisce tutti i passaggi descritti nell'architettura della soluzione. Sono inclusi istruzioni e puntatori per la personalizzazione. 

## <a name="data-sources"></a>Origini dati
La soluzione usa due origini dati diverse:

* Set di dati di diagnostica e segnali simulati del veicolo
* Catalogo dei veicoli

Nella soluzione è incluso un simulatore di dati telematici relativi al veicolo, come mostrato nella schermata seguente. Il simulatore genera informazioni di diagnostica e segnali corrispondenti allo stato del veicolo e allo schema di guida in un determinato momento. Per scaricare la soluzione Simulatore di dati telematici del veicolo di Visual Studio e personalizzarla in base alle esigenze, andare alla pagina Web del [Simulatore di dati telematici del veicolo](http://go.microsoft.com/fwlink/?LinkId=717075). Il catalogo dei veicoli contiene un set di dati di riferimento che esegue il mapping dei numeri di identificazione del veicolo in base ai modelli.

![Simulatore di dati telematici del veicolo](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig1-vehicle-telematics-simulator.png)


Questo set di dati in formato JSON contiene lo schema seguente.

| Colonna | Descrizione | Valori |
| --- | --- | --- |
| vin |Numero di identificazione del veicolo generato in modo casuale |Ottenuto da un elenco principale di 10.000 numeri di identificazione del veicolo generati in modo casuale |
| outsideTemperature |Temperatura all'esterno durante la guida del veicolo |Numero da 0 a 100 generato in modo casuale |
| engineTemperature |Temperatura del motore del veicolo |Numero da 0 a 500 generato in modo casuale |
| Velocità |Velocità del motore durante la guida del veicolo |Numero da 0 a 100 generato in modo casuale |
| fuel |Livello di carburante del veicolo |Numero da 0 a 100 generato in modo casuale (indica la percentuale del livello di carburante) |
| engineoil |Livello dell'olio del motore del veicolo |Numero da 0 a 100 generato in modo casuale (indica la percentuale del livello di olio nel motore) |
| Tire pressure |Pressione degli pneumatici del veicolo |Numero da 0 a 50 generato in modo casuale (indica la percentuale del livello di pressione degli pneumatici) |
| odometer |Lettura del contachilometri del veicolo |Numero da 0 a 200.000 generato in modo casuale |
| accelerator_pedal_position |Posizione del pedale dell'acceleratore del veicolo |Numero da 0 a 100 generato in modo casuale (indica la percentuale del livello dell'acceleratore) |
| parking_brake_status |Indica se il freno di stazionamento è attivato o meno |true o false |
| headlamp_status |Indica se il fanale anteriore è acceso o meno |true o false |
| brake_pedal_status |Indica se il pedale del freno è premuto o meno |true o false |
| transmission_gear_position |Posizione del cambio del veicolo |Stati: first, second, third, fourth, fifth, sixth, seventh, eighth |
| ignition_status |Indica se il veicolo è acceso o meno |true o false |
| windshield_wiper_status |Indica se il tergicristallo è attivato o meno |true o false |
| abs |Indica se l'ABS è attivato o meno |true o false |
| Timestamp |Timestamp di creazione del punto dati |Date |
| city |Località in cui si trova il veicolo |Quattro città in questa soluzione: Bellevue, Redmond, Sammamish, Seattle |

Il set di dati di riferimento del modello del veicolo esegue il mapping dei numeri di identificazione del veicolo con i modelli. 

| vin | Modello |
| --- | --- |
| FHL3O1SA4IEHB4WU1 |Berlina |
| 8J0U8XCPRGW4Z3NQE |Ibrido |
| WORG68Z2PLTNZDBI7 |Berlina familiare |
| JTHMYHQTEPP4WBMRN |Berlina |
| W9FTHG27LZN1YWO0Y |Ibrido |
| MHTP9N792PHK08WJM |Berlina familiare |
| EI4QXI2AXVQQING4I |Berlina |
| 5KKR2VB4WHQH97PF8 |Ibrido |
| W9NSZ423XZHAONYXB |Berlina familiare |
| 26WJSGHX4MA5ROHNL |Decappottabile |
| GHLUB6ONKMOSI7E77 |Station Wagon |
| 9C2RHVRVLMEJDBXLP |Utilitaria |
| BRNHVMZOUJ6EOCP32 |SUV di piccole dimensioni |
| VCYVW0WUZNBTM594J |Auto sportiva |
| HNVCE6YFZSA5M82NY |SUV di medie dimensioni |
| 4R30FOR7NUOBL05GJ |Station Wagon |
| WYNIIY42VKV6OQS1J |SUV di grandi dimensioni |
| 8Y5QKG27QET1RBK7I |SUV di grandi dimensioni |
| DF6OX2WSRA6511BVG |Coupé |
| Z2EOZWZBXAEW3E60T |Berlina |
| M4TV6IEALD5QDS3IR |Ibrido |
| VHRA1Y2TGTA84F00H |Berlina familiare |
| R0JAUHT1L1R3BIKI0 |Berlina |
| 9230C202Z60XX84AU |Ibrido |
| T8DNDN5UDCWL7M72H |Berlina familiare |
| 4WPYRUZII5YV7YA42 |Berlina |
| D1ZVY26UV2BFGHZNO |Ibrido |
| XUF99EW9OIQOMV7Q7 |Berlina familiare |
| 8OMCL3LGI7XNCC21U |Decappottabile |
| ……. | |

## <a name="ingestion"></a>Ingestion
Vengono usate combinazioni di Hub eventi di Azure, Analisi di flusso di Azure e Azure Data Factory per inserire i segnali del veicolo, gli eventi di diagnostica e le analisi in tempo reale e batch. Tutti questi componenti vengono creati e configurati durante la distribuzione della soluzione. 

### <a name="real-time-analysis"></a>Analisi in tempo reale
Gli eventi generati dal simulatore di dati telematici del veicolo vengono pubblicati nell'hub eventi usando l'SDK dell'hub eventi.  

![Dashboard di Hub eventi](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig4-vehicle-telematics-event-hub-dashboard.png) 

Il processo di Analisi di flusso di Azure inserisce gli eventi dall'hub eventi ed elabora i dati in tempo reale per analizzare l'integrità del veicolo.

![Elaborazione dei dati da parte del processo di Analisi di flusso](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig5-vehicle-telematics-stream-analytics-job-processing-data.png) 


Il processo di Analisi di flusso di Azure:

* Inserisce i dati dall'hub eventi.
* Esegue un join con i dati di riferimento per eseguire il mapping del numero identificativo del veicolo al modello corrispondente. 
* Li rende persistenti nell'archivio BLOB di Azure per l'analisi in batch avanzata. 

La query di Analisi di flusso di Azure riportata di seguito viene usata per rendere persistenti i dati nell'archivio BLOB: 

![Query del processo di Analisi di flusso per l'inserimento di dati](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig6-vehicle-telematics-stream-analytics-job-query-for-data-ingestion.png) 


### <a name="batch-analysis"></a>Analisi batch
Viene anche generato un volume aggiuntivo di set di dati di diagnostica e segnali del veicolo simulati per rendere più completa l'analisi batch. Questo volume aggiuntivo è necessario per garantire un volume di dati rappresentativo per l'elaborazione batch. A questo scopo, viene usata PrepareSampleDataPipeline nel flusso di lavoro di Data Factory per generare una simulazione di segnali e un set di dati di diagnostica del veicolo equivalenti a un anno. Andare alla pagina Web [Attività personalizzata di Data Factory](http://go.microsoft.com/fwlink/?LinkId=717077) per scaricare la soluzione di Visual Studio per l'attività .NET personalizzata di Data factory ed eseguire le personalizzazioni necessarie. 

Questo flusso di lavoro mostra dati di esempio preparati per l'elaborazione batch.

![Dati di esempio preparati per il flusso di lavoro dell'elaborazione batch](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig7-vehicle-telematics-prepare-sample-data-for-batch-processing.png) 


La pipeline è costituita da un'attività .NET personalizzata di Data Factory.

![Attività PrepareSampleDataPipeline](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig8-vehicle-telematics-prepare-sample-data-pipeline.png) 

Dopo aver eseguito correttamente la pipeline e aver contrassegnato come "Pronto" il set di dati RawCarEventsTable, viene generato l'equivalente di un anno di dati di diagnostica e segnali del veicolo simulati. La cartella e il file seguenti creati nell'account di archiviazione vengono visualizzati all'interno del contenitore connectedcar:

![Output di PrepareSampleDataPipeline](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig9-vehicle-telematics-prepare-sample-data-pipeline-output.png) 

## <a name="partition-the-data-set"></a>Eseguire la partizione del set di dati
Il set di dati di diagnostica e i segnali del veicolo semistrutturati non elaborati vengono partizionati durante la fase di preparazione dei dati in un formato ANNO/MESE. Questo partizionamento favorisce l'esecuzione di query più efficienti e un'archiviazione scalabile a lungo termine abilitando il failover. Ad esempio, quando il primo account BLOB si riempie, gli errori si riversano nell'account successivo. 

>[!NOTE] 
>Questo passaggio della soluzione si applica solo all'elaborazione batch.

Gestione dati di input e di output:

* I **dati di output**, con etichetta PartitionedCarEventsTable, devono essere mantenuti per un lungo periodo di tempo nella forma di base "meno elaborata" dei dati nel Data Lake del cliente. 
* I **dati di input** per questa pipeline vengono in genere eliminati perché i dati di output sono totalmente fedeli ai dati di input. Vengono meglio archiviati, o partizionati, per l'uso successivo.

Flusso di lavoro degli eventi per la partizione della macchina.

![Flusso di lavoro PartitionCarEvents](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig10-vehicle-telematics-partition-car-events-workflow.png)


I dati non elaborati vengono partizionati tramite un'attività Hive di Azure HDInsight in PartitionCarEventsPipeline, come illustrato nella schermata seguente. I dati di esempio generati per un anno durante la fase di preparazione dei dati vengono partizionati per ANNO/MESE. Le partizioni vengono usate per generare i dati di diagnostica e i segnali del veicolo per ogni mese di un anno, per un totale di 12 partizioni. 

![Attività PartitionCarEventsPipeline](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig11-vehicle-telematics-partition-car-events-pipeline.png)


**Script Hive PartitionConnectedCarEvents**

Lo script di Hive partitioncarevents.hql viene usato per il partizionamento. Si trova nella cartella \demo\src\connectedcar\scripts del file zip scaricato. 
    
    SET hive.exec.dynamic.partition=true;
    SET hive.exec.dynamic.partition.mode = nonstrict;
    set hive.cli.print.header=true;

    DROP TABLE IF EXISTS RawCarEvents; 
    CREATE EXTERNAL TABLE RawCarEvents 
    (
                vin                                string,
                model                            string,
                timestamp                        string,
                outsidetemperature                string,
                enginetemperature                string,
                speed                            string,
                fuel                            string,
                engineoil                        string,
                tirepressure                    string,
                odometer                        string,
                city                            string,
                accelerator_pedal_position        string,
                parking_brake_status            string,
                headlamp_status                    string,
                brake_pedal_status                string,
                transmission_gear_position        string,
                ignition_status                    string,
                windshield_wiper_status            string,
                abs                              string,
                gendate                            string

    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:RAWINPUT}'; 

    DROP TABLE IF EXISTS PartitionedCarEvents; 
    CREATE EXTERNAL TABLE PartitionedCarEvents 
    (
                vin                                string,
                model                            string,
                timestamp                        string,
                outsidetemperature                string,
                enginetemperature                string,
                speed                            string,
                fuel                            string,
                engineoil                        string,
                tirepressure                    string,
                odometer                        string,
                city                            string,
                accelerator_pedal_position        string,
                parking_brake_status            string,
                headlamp_status                    string,
                brake_pedal_status                string,
                transmission_gear_position        string,
                ignition_status                    string,
                windshield_wiper_status            string,
                abs                              string,
                gendate                            string
    ) partitioned by (YearNo int, MonthNo int) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:PARTITIONEDOUTPUT}';

    DROP TABLE IF EXISTS Stage_RawCarEvents; 
    CREATE TABLE IF NOT EXISTS Stage_RawCarEvents 
    (
                vin                                string,
                model                            string,
                timestamp                        string,
                outsidetemperature                string,
                enginetemperature                string,
                speed                            string,
                fuel                            string,
                engineoil                        string,
                tirepressure                    string,
                odometer                        string,
                city                            string,
                accelerator_pedal_position        string,
                parking_brake_status            string,
                headlamp_status                    string,
                brake_pedal_status                string,
                transmission_gear_position        string,
                ignition_status                    string,
                windshield_wiper_status            string,
                abs                              string,
                gendate                            string,
                YearNo                             int,
                MonthNo                         int) 
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

Dopo la corretta esecuzione della pipeline, vengono visualizzate le partizioni seguenti generate nell'account di archiviazione del contenitore connectedcar:

![Output partizionato](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig12-vehicle-telematics-partitioned-output.png)

A questo punto, i dati sono ottimizzati, più gestibili e pronti per un'ulteriore elaborazione che permette di ottenere informazioni dettagliate sul batch. 

## <a name="data-analysis"></a>Analisi dei dati
In questa sezione viene illustrato come combinare Analisi di flusso, Azure Machine Learning, Data Factory e HDInsight per un'analisi avanzata e approfondita dell'integrità del veicolo e delle abitudini di guida.

### <a name="machine-learning"></a>Machine learning
L'obiettivo è eseguire una stima dei veicoli che richiedono interventi di manutenzione e dei veicoli da richiamare in base a determinate statistiche di integrità e in base alle presupposizioni seguenti:

* Se una delle tre condizioni seguenti è vera, il veicolo richiede un intervento di manutenzione:
  
  * La pressione degli pneumatici è bassa.
  * Il livello di olio del motore è basso.
  * La temperatura del motore è elevata.

* Se una delle condizioni seguenti è vera, il veicolo può avere un problema di sicurezza e può essere necessario il richiamo:
  
  * La temperatura del motore è elevata, ma la temperatura esterna è bassa.
  * La temperatura del motore è bassa, ma la temperatura esterna è alta.

In base ai requisiti precedenti, i due modelli separati rilevano anomalie. Un modello è per il rilevamento della manutenzione del veicolo e l'altro è per il rilevamento del richiamo del veicolo. In entrambi i modelli viene usato l'algoritmo di analisi in componenti principali predefinito per il rilevamento anomalie. 

#### <a name="maintenance-detection-model"></a>**Modello di rilevamento per la manutenzione**

Se uno dei tre indicatori, pressione degli pneumatici, olio del motore o temperatura del motore, soddisfa la condizione corrispondente, il modello di rilevamento della manutenzione segnala un'anomalia. Di conseguenza, nella compilazione del modello è sufficiente prendere in considerazione solo queste tre variabili. Nell'esperimento di Machine Learning viene usato il modulo **Seleziona colonne in set di dati** per estrarre queste tre variabili. Viene quindi usato il modulo di rilevamento delle anomalie basato su analisi in componenti principali per compilare il modello di rilevamento delle anomalie. 

L'analisi in componenti principali è una tecnica consolidata in Machine Learning che può essere applicata alla selezione di funzionalità, alla classificazione e al rilevamento di anomalie. Converte un set di casi contenente variabili probabilmente correlate in un set di valori denominati componenti principali. Lo scopo primario del modello basato su analisi in componenti principali è la proiezione dei dati in uno spazio dimensionale inferiore in modo che caratteristiche e anomalie siano identificabili più facilmente.

Per ogni nuovo input nel modello di rilevamento, il rilevatore di anomalie calcola prima di tutto la proiezione sugli autovettori, quindi l'errore di ricostruzione normalizzato. Questo errore normalizzato è il punteggio di anomalie: maggiore è l'errore, maggiori anomalie presenterà l'istanza. 

Nel rilevamento per la manutenzione ogni record può essere considerato come un punto in uno spazio tridimensionale definito dalle coordinate pressione degli pneumatici, olio del motore e temperatura del motore. Per acquisire queste anomalie, viene usata l'analisi in componenti principali per proiettare i dati originali dello spazio tridimensionale in uno spazio bidimensionale. Il parametro relativo al numero di componenti da usare nell'analisi in componenti principali viene quindi impostato su 2. Questo parametro ha un ruolo importante nell'applicazione del rilevamento delle anomalie basato su PCA. Dopo aver usato l'analisi in componenti principali per proiettare i dati, le anomalie vengono identificate più facilmente.

#### <a name="recall-anomaly-detection-model"></a>**Modello per richiamare il rilevamento anomalie**

Nel modello per richiamare il rilevamento anomalie i moduli di rilevamento anomalie basati su analisi in componenti principali e **Seleziona colonne in set di dati** vengono usati in modo simile. Nello specifico, vengono prima di tutto estratte le tre variabili, temperatura del motore, temperatura esterna e velocità, usando il modulo **Seleziona colonne in set di dati**. Viene inclusa anche la variabile velocità perché la temperatura del motore è in genere correlata alla velocità. Viene quindi usato il modulo di rilevamento delle anomalie basato su analisi in componenti principali per proiettare i dati da uno spazio tridimensionale a uno spazio bidimensionale. I criteri di richiamo sono soddisfatti. È quindi necessario richiamare il veicolo quando la temperatura del motore e quella esterna sono correlate negativamente. Dopo aver eseguito l'analisi in componenti principali viene usato l'algoritmo di rilevamento delle anomalie basato su analisi in componenti principali per acquisire le anomalie. 

Quando si esegue il training di uno dei modelli, vengono usati dati normali come dati di input per eseguire il training del modello di rilevamento delle anomalie basato su analisi in componenti principali. I dati normali non richiedono manutenzione o richiamo. Nell'esperimento di assegnazione dei punteggi viene usato il modello di rilevamento anomalie sottoposto a training per rilevare se il veicolo richiede la manutenzione o il richiamo. 

### <a name="real-time-analysis"></a>Analisi in tempo reale
La query SQL di Analisi di flusso seguente viene usata per ottenere la media di tutti i parametri del veicolo importanti. Questi parametri includono velocità del veicolo, livello di carburante, temperatura del motore, lettura chilometraggio, pressione degli pneumatici, livello di olio nel motore e altri. Le medie vengono usate per rilevare anomalie, emettere avvisi e determinare le condizioni generali di integrità dei veicoli usati in un'area specifica, correlando tali informazioni a dati demografici. 

![Query di Analisi di flusso per l'elaborazione in tempo reale](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig13-vehicle-telematics-stream-analytics-query-for-real-time-processing.png)

Tutte le medie vengono calcolate in una finestra a cascata di 3 secondi. Viene usata la finestra a cascata poiché sono necessari gli intervalli di tempo contigui e non sovrapposti. 

Per altre informazioni sulle funzionalità delle finestre in Analisi di flusso, vedere [Windowing (Azure Stream Analytics)](https://msdn.microsoft.com/library/azure/dn835019.aspx) (Windowing: Analisi di flusso di Azure).

#### <a name="real-time-prediction"></a>**Stima in tempo reale**

Nella soluzione è inclusa un'applicazione per la messa in funzione del modello di Machine Learning in tempo reale. L'applicazione RealTimeDashboardApp viene creata e configurata nell'ambito della distribuzione della soluzione. L'applicazione:

* È in attesa di un'istanza dell'hub eventi in cui Analisi di flusso pubblica gli eventi in un modello eseguito in modo continuo.

    ![Query di Analisi di flusso per la pubblicazione dei dati](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig14-vehicle-telematics-stream-analytics-query-for-publishing.png) 

* Riceve gli eventi. Per ogni evento ricevuto dall'applicazione: 
   
   * i dati vengono elaborati usando l'endpoint del servizio di richiesta-risposta di Machine Learning per l'assegnazione dei punteggi, l'endpoint RRS viene pubblicato automaticamente nell'ambito della distribuzione.
   * L'output RRS viene pubblicato in un set di dati di Power BI con le API push.

Questo modello è anche applicabile a scenari in cui si desidera integrare un'applicazione line-of-business con il flusso di analisi in tempo reale. Questi scenari includono avvisi, notifiche e messaggistica.

Consultare la pagina Web di [download di RealtimeDashboardApp](http://go.microsoft.com/fwlink/?LinkId=717078) per scaricare la soluzione RealtimeDashboardApp di Visual Studio per le personalizzazioni. 

#### <a name="execute-the-real-time-dashboard-application"></a>**Eseguire l'applicazione dashboard in tempo reale**
1. Estrarre RealtimeDashboardApp e salvarlo in locale.

    ![Cartella RealTimeDashboardApp](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig16-vehicle-telematics-realtimedashboardapp-folder.png) 

2. Eseguire l'applicazione RealtimeDashboardApp.exe.

3. Inserire le credenziali di Power BI valide e selezionare **Accedi**.  

    ![Finestra di accesso dell'app dashboard in tempo reale](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig17a-vehicle-telematics-realtimedashboardapp-sign-in-to-powerbi.png) 
    
4. Selezionare **Accetto**.

    ![Finestra di accesso finale dell'app dashboard in tempo reale](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig17b-vehicle-telematics-realtimedashboardapp-sign-in-to-powerbi.png) 

>[!NOTE] 
>Per scaricare il set di dati di Power BI, eseguire RealtimeDashboardApp con il parametro "flushdata". 

    RealtimeDashboardApp.exe -flushdata


### <a name="batch-analysis"></a>Analisi batch
L'obiettivo è mostrare come Contoso Motors usa le funzionalità di calcolo di Azure per sfruttare i Big Data. Questi dati rivelano informazioni dettagliate su schemi guida, comportamento d'uso e integrità del veicolo. Queste informazioni consentono di:

* Migliorare l'esperienza del cliente e abbassare i costi fornendo informazioni dettagliate sulle abitudini e sui comportamenti di guida attenti ai consumi.
* Acquisire informazioni in modo attivo sui clienti e sui relativi stili di guida per gestire il processo decisionale aziendale e offrire prodotti e servizi di altissimo livello.

In questa soluzione vengono esaminate le metriche seguenti:

* **Stile di guida aggressivo**: identifica la tendenza di modelli, località, condizioni di guida e periodo dell'anno per ottenere informazioni dettagliate sugli stili di guida aggressivi. Contoso Motors può usare queste informazioni per creare campagne di marketing al fine di introdurre nuove funzionalità personalizzate e assicurazioni basate sull'uso.
* **Stile di guida attento ai consumi**: identifica la tendenza di modelli, località, condizioni di guida e periodo dell'anno per ottenere informazioni dettagliate sugli stili di guida attenti ai consumi. Contoso Motors può usare queste informazioni per creare campagne di marketing, al fine di introdurre nuove funzionalità personalizzate e per segnalare al guidatore abitudini di guida attente ai consumi e all'ambiente.
* **Modelli di richiamo**: identifica i modelli che è necessario richiamare in base all'esperimento di Machine Learning per il rilevamento anomalie.

Di seguito vengono esaminate le singole metriche nel dettaglio.

#### <a name="aggressive-driving-behavior-patterns"></a>**Modelli di comportamento di guida aggressiva**

I segnali del veicolo e i dati di diagnostica partizionati vengono elaborati in AggresiveDrivingPatternPipeline, come mostrato nel flusso di lavoro seguente. Hive viene usato per determinare modelli, località, veicoli, condizioni di guida e altri parametri, che mostrano uno stile di guida aggressivo.

![Flusso di lavoro per lo stile di guida aggressivo](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig18-vehicle-telematics-aggressive-driving-pattern.png) 

***Query Hive per lo stile di guida aggressivo***

Lo script di Hive aggresivedriving.hql viene usato per analizzare i modelli per le condizioni di guida aggressiva. Si trova nella cartella \demo\src\connectedcar\scripts del file zip scaricato. 

    DROP TABLE IF EXISTS PartitionedCarEvents; 
    CREATE EXTERNAL TABLE PartitionedCarEvents
    (
                vin                                string,
                model                            string,
                timestamp                        string,
                outsidetemperature                string,
                enginetemperature                string,
                speed                            string,
                fuel                            string,
                engineoil                        string,
                tirepressure                    string,
                odometer                        string,
                city                            string,
                accelerator_pedal_position        string,
                parking_brake_status            string,
                headlamp_status                    string,
                brake_pedal_status                string,
                transmission_gear_position        string,
                ignition_status                    string,
                windshield_wiper_status            string,
                abs                              string,
                gendate                            string

    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:PARTITIONEDINPUT}';

    DROP TABLE IF EXISTS CarEventsAggresive; 
    CREATE EXTERNAL TABLE CarEventsAggresive
    (
                   vin                         string, 
                model                        string,
                timestamp                    string,
                city                        string,
                speed                          string,
                transmission_gear_position    string,
                brake_pedal_status            string,
                Year                        string,
                Month                        string

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


Lo script usa una combinazione di posizione del cambio, stato del pedale del freno e velocità del veicolo per rilevare un comportamento di guida spericolato/aggressivo in base allo stile di frenata ad alta velocità. 

Dopo la corretta esecuzione della pipeline, vengono visualizzate le partizioni seguenti generate nell'account di archiviazione del contenitore connectedcar:

![Output di AggressiveDrivingPatternPipeline](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig19-vehicle-telematics-aggressive-driving-pattern-output.png) 


#### <a name="fuel-efficient-driving-behavior-patterns"></a>**Modello di comportamento di guida attento ai consumi**

I segnali del veicolo e i dati di diagnostica partizionati vengono elaborati in FuelEfficientDrivingPatternPipeline, come mostrato nel flusso di lavoro seguente. Hive viene usato per determinare modelli, località, veicoli, condizioni di guida e altre proprietà, che mostrano uno stile di guida attento ai consumi.

![Stile di guida attento ai consumi](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig19-vehicle-telematics-fuel-efficient-driving-pattern.png) 

***Query Hive per lo stile di guida attento ai consumi***

Lo script di Hive fuelefficientdriving.hql viene usato per analizzare i modelli per le condizioni di guida attenta ai consumi. Si trova nella cartella \demo\src\connectedcar\scripts del file zip scaricato. 

    DROP TABLE IF EXISTS PartitionedCarEvents; 
    CREATE EXTERNAL TABLE PartitionedCarEvents
    (
                vin                                string,
                model                            string,
                timestamp                        string,
                outsidetemperature                string,
                enginetemperature                string,
                speed                            string,
                fuel                            string,
                engineoil                        string,
                tirepressure                    string,
                odometer                        string,
                city                            string,
                accelerator_pedal_position        string,
                parking_brake_status            string,
                headlamp_status                    string,
                brake_pedal_status                string,
                transmission_gear_position        string,
                ignition_status                    string,
                windshield_wiper_status            string,
                abs                              string,
                gendate                            string

    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:PARTITIONEDINPUT}';

    DROP TABLE IF EXISTS FuelEfficientDriving; 
    CREATE EXTERNAL TABLE FuelEfficientDriving
    (
                   vin                         string, 
                model                        string,
                   city                        string,
                speed                          string,
                transmission_gear_position    string,                
                brake_pedal_status            string,            
                accelerator_pedal_position    string,                             
                Year                        string,
                Month                        string

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


Lo script usa una combinazione di posizione del cambio, stato del pedale del freno, velocità del veicolo e posizione del pedale dell'acceleratore per rilevare un comportamento di guida attento ai consumi in base allo stile di accelerazione e frenata e in base alla velocità. 

Dopo la corretta esecuzione della pipeline, vengono visualizzate le partizioni seguenti generate nell'account di archiviazione del contenitore connectedcar:

![Output di FuelEfficientDrivingPatternPipeline](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig20-vehicle-telematics-fuel-efficient-driving-pattern-output.png) 

**Previsioni del modello di richiamo**

L'esperimento di Machine Learning è stato sottoposto a provisioning e pubblicato come servizio Web nell'ambito della distribuzione della soluzione. In questo flusso di lavoro viene usato l'endpoint di punteggio del batch. Viene registrato come un servizio collegato di data factory e operativo usando l'attività di assegnazione del punteggio batch di data factory.

![Endpoint di Machine Learning](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig21-vehicle-telematics-machine-learning-endpoint.png) 

Il servizio collegato registrato viene usato in DetectAnomalyPipeline per assegnare un punteggio ai dati con il modello di rilevamento delle anomalie. 

![Attività di assegnazione punteggio batch di Machine Learning in Data factory](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig22-vehicle-telematics-aml-batch-scoring.png)  

Alcuni dei passaggi eseguiti in questa pipeline per la preparazione dei dati ne consentono l'uso con il servizio Web di assegnazione punteggio batch. 

![DetectAnomalyPipeline per la previsione di richiamo](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig23-vehicle-telematics-pipeline-predicting-recalls.png)  

***Query Hive per il rilevamento delle anomalie***

Dopo aver assegnato il punteggio, un'attività di HDInsight elabora e aggrega i dati che il modello è ha classificato come anomalie. Il modello usa un punteggio di probabilità di 0,60 o superiore.

    DROP TABLE IF EXISTS CarEventsAnomaly; 
    CREATE EXTERNAL TABLE CarEventsAnomaly 
    (
                vin                            string,
                model                        string,
                gendate                        string,
                outsidetemperature            string,
                enginetemperature            string,
                speed                        string,
                fuel                        string,
                engineoil                    string,
                tirepressure                string,
                odometer                    string,
                city                        string,
                accelerator_pedal_position    string,
                parking_brake_status        string,
                headlamp_status                string,
                brake_pedal_status            string,
                transmission_gear_position    string,
                ignition_status                string,
                windshield_wiper_status        string,
                abs                          string,
                maintenanceLabel            string,
                maintenanceProbability        string,
                RecallLabel                    string,
                RecallProbability            string

    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:ANOMALYOUTPUT}';

    DROP TABLE IF EXISTS RecallModel; 
    CREATE EXTERNAL TABLE RecallModel 
    (

                vin                            string,
                model                        string,
                city                        string,
                outsidetemperature            string,
                enginetemperature            string,
                speed                        string,
                Year                        string,
                Month                        string                

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


Dopo la corretta esecuzione della pipeline, vengono visualizzate le partizioni seguenti generate nell'account di archiviazione del contenitore connectedcar:

![Output di DetectAnomalyPipeline](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig24-vehicle-telematics-detect-anamoly-pipeline-output.png) 

## <a name="publish"></a>Pubblica

### <a name="real-time-analysis"></a>Analisi in tempo reale
Una delle query del processo di Analisi di flusso pubblica gli eventi in un'istanza di hub eventi di output. 

![Processo di Analisi di flusso pubblicato in un'istanza dell'hub eventi di output](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig25-vehicle-telematics-stream-analytics-job-publishes-output-event-hub.png)

La query di Analisi di flusso seguente viene usata per pubblicare in un'istanza dell'hub eventi di output:

![Query di Analisi di flusso per la pubblicazione in un'istanza dell'hub eventi di output](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig26-vehicle-telematics-stream-analytics-query-publish-output-event-hub.png)

Questo flusso di eventi viene usato da RealTimeDashboardApp inclusa nella soluzione. Questa applicazione usa il servizio di richiesta-risposta Web di Machine Learning per l'assegnazione del punteggio in tempo reale. Pubblica i dati risultanti in un set di dati di Power BI per l'uso. 

### <a name="batch-analysis"></a>Analisi batch
I risultati dell'elaborazione batch e in tempo reale vengono pubblicati nelle tabelle di database SQL di Azure per l'uso. Il server SQL, il database e le tabelle vengono creati automaticamente con lo script di installazione. 

I risultati di elaborazione batch vengono copiati nel flusso di lavoro data mart.

![Copia dei risultati dell'elaborazione batch nel flusso di lavoro di data mart](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig27-vehicle-telematics-batch-processing-results-copy-to-data-mart.png)

Il processo di Analisi di flusso viene pubblicato in data mart.

![Processo di Analisi di flusso pubblicato in data mart](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig28-vehicle-telematics-stream-analytics-job-publishes-to-data-mart.png)

Le impostazione di data mart si trovano nel processo di Analisi di flusso.

![Impostazione di data mart nel processo di Analisi di flusso](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig29-vehicle-telematics-data-mart-setting-in-stream-analytics-job.png)

## <a name="consume"></a>Utilizzo
Power BI offre alla soluzione un dashboard completo per la visualizzazione di dati in tempo reale e di analisi predittive. 

L'aspetto finale del dashboard appare come nell'esempio seguente:

![Dashboard di Power BI](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig30-vehicle-telematics-powerbi-dashboard.png)

## <a name="summary"></a>Riepilogo
Questo documento contiene un'analisi dettagliata e approfondita della soluzione di analisi dei dati di telemetria del veicolo. Il modello di architettura lambda viene usato per l'analisi batch e in tempo reale completa di stime e azioni. Il modello si applica a una vasta gamma di casi d'uso che richiedono l'analisi del percorso critico (in tempo reale) e di quello non critico (batch). 

### <a name="references"></a>Riferimenti

* [Soluzione Vehicle Telematics Simulator di Visual Studio](http://go.microsoft.com/fwlink/?LinkId=717075) 
* [Hub eventi di Azure](https://azure.microsoft.com/services/event-hubs/)
* [Data factory di Azure](https://azure.microsoft.com/documentation/learning-paths/data-factory/)
* [SDK di Hub eventi di Azure per l'inserimento di flussi](../../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
* [Funzionalità di spostamento dei dati di Azure Data Factory](../../data-factory/v1/data-factory-data-movement-activities.md)
* [Attività .NET di Azure Data Factory](../../data-factory/v1/data-factory-use-custom-activities.md)
* [Soluzione di Visual Studio per l'attività .NET di Azure Data Factory usata per la preparazione dei dati di esempio](http://go.microsoft.com/fwlink/?LinkId=717077) 
