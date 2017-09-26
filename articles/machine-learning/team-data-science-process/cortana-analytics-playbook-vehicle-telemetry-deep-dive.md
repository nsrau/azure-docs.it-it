---
title: "Approfondimento sulla previsione dell'integrità dei veicoli e sulle abitudini di guida - Azure | Documentazione Microsoft"
description: "Usare le funzionalità di Cortana Intelligence per ottenere informazioni dettagliate predittive e in tempo reale sullo stato di integrità del veicolo e sulle abitudini di guida."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: d8866fa6-aba6-40e5-b3b3-33057393c1a8
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: bradsev
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 4050fdc2056df395bbcc37e3783f61eebd90f80a
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---
# <a name="vehicle-telemetry-analytics-solution-playbook-deep-dive-into-the-solution"></a>Studio della soluzione di analisi dei dati di telemetria del veicolo: Approfondimento della soluzione
Questo **menu** contiene i collegamenti alle sezioni dello studio: 

[!INCLUDE [cap-vehicle-telemetry-playbook-selector](../../../includes/cap-vehicle-telemetry-playbook-selector.md)]

Questa sezione approfondisce ognuna delle fasi rappresentate nell'architettura della soluzione, con istruzioni e indicazioni per la personalizzazione. 

## <a name="data-sources"></a>Origini dati
La soluzione usa due origini dati diverse:

* **set di dati di diagnostica e segnali del veicolo simulati** e 
* **catalogo dei veicoli**

Nella soluzione è incluso un simulatore di dati telematici relativi al veicolo. Il simulatore genera informazioni di diagnostica e segnali corrispondenti allo stato del veicolo e allo schema di guida in un determinato momento. Fare clic su [Vehicle Telematics Simulator](http://go.microsoft.com/fwlink/?LinkId=717075) per scaricare la **soluzione Vehicle Telematics Simulator di Visual Studio** ed eseguire le personalizzazioni necessarie. Il catalogo dei veicoli contiene un set di dati di riferimento con il numero identificativo del veicolo (NIV) per il mapping del modello.

![Simulatore di dati telematici del veicolo](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig1-vehicle-telematics-simulator.png)

*Figura 1: Simulatore di dati telematici del veicolo*

Questo è un set di dati in formato JSON contenente lo schema seguente.

| Colonna | Descrizione | Valori |
| --- | --- | --- |
| vin |Numero identificativo del veicolo generato in modo casuale |Viene ottenuto da un elenco master di 10.000 numeri identificativi di veicoli generati in modo casuale. |
| outsideTemperature |Temperatura all'esterno durante la guida del veicolo |Numero da 0 a 100 generato in modo casuale |
| engineTemperature |Temperatura del motore del veicolo |Numero da 0 a 500 generato in modo casuale |
| speed |Velocità del motore durante la guida del veicolo |Numero da 0 a 100 generato in modo casuale |
| fuel |Livello di carburante del veicolo |Numero da 0 a 100 generato in modo casuale (indica la percentuale del livello di carburante) |
| engineoil |Livello dell'olio del motore del veicolo |Numero da 0 a 100 generato in modo casuale (indica la percentuale del livello di olio del motore) |
| Tire pressure |Pressione degli pneumatici del veicolo |Numero da 0 a 50 generato in modo casuale (indica la percentuale del livello di pressione degli pneumatici) |
| odometer |Lettura del contachilometri del veicolo |Numero da 0 a 200000 generato in modo casuale |
| accelerator_pedal_position |Posizione del pedale dell'acceleratore del veicolo |Numero da 0 a 100 generato in modo casuale (indica la percentuale del livello dell'acceleratore) |
| parking_brake_status |Indica se il freno di stazionamento è attivato o meno |true o false |
| headlamp_status |Indica se il fanale anteriore è acceso o meno |true o false |
| brake_pedal_status |Indica se il pedale del freno è premuto o meno |true o false |
| transmission_gear_position |Posizione del cambio del veicolo |Stati: first, second, third, fourth, fifth, sixth, seventh, eighth |
| ignition_status |Indica se il veicolo è acceso o meno |true o false |
| windshield_wiper_status |Indica se il tergicristallo è attivato o meno |true o false |
| abs |Indica se l'ABS è attivato o meno |true o false |
| timestamp |Timestamp di creazione del punto dati |Data |
| city |Località in cui si trova il veicolo |4 città in questa soluzione: Bellevue, Redmond, Sammamish, Seattle |

Il set di dati di riferimento del modello di veicolo contiene il mapping del numero identificativo del veicolo al modello. 

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

### <a name="references"></a>Riferimenti
[Soluzione Vehicle Telematics Simulator di Visual Studio](http://go.microsoft.com/fwlink/?LinkId=717075) 

[Hub eventi di Azure](https://azure.microsoft.com/services/event-hubs/)

[Data factory di Azure](https://azure.microsoft.com/documentation/learning-paths/data-factory/)

## <a name="ingestion"></a>Ingestion
Vengono sfruttate combinazioni di Hub eventi di Azure, Analisi di flusso di Azure e Azure Data Factory per inserire i segnali del veicolo, gli eventi di diagnostica e le analisi in tempo reale e batch. Tutti questi componenti vengono creati e configurati durante la distribuzione della soluzione. 

### <a name="real-time-analysis"></a>Analisi in tempo reale
Gli eventi generati dal simulatore di dati telematici relativi al veicolo vengono pubblicati nell'Hub eventi usando l'SDK di Hub di eventi. Il processo di Analisi di flusso inserisce gli eventi dall'hub eventi ed elabora i dati in tempo reale per analizzare l'integrità del veicolo. 

![Dashboard di Hub eventi](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig4-vehicle-telematics-event-hub-dashboard.png) 

*Figura 4: Dashboard di Hub eventi*

![Elaborazione dei dati da parte del processo di Analisi di flusso](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig5-vehicle-telematics-stream-analytics-job-processing-data.png) 

*Figura 5: Elaborazione dei dati da parte del processo di Analisi di flusso*

Il processo di Analisi di flusso:

* Inserisce i dati dall'hub eventi 
* Esegue un join con i dati di riferimento per eseguire il mapping del numero identificativo del veicolo al modello corrispondente 
* Li rende persistenti nell'archivio BLOB di Azure per l'analisi in batch avanzata 

La query di Analisi di flusso riportata di seguito viene usata per rendere persistenti i dati nell'archivio BLOB di Azure. 

![Query del processo di Analisi di flusso per l'inserimento di dati](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig6-vehicle-telematics-stream-analytics-job-query-for-data-ingestion.png) 

*Figura 6: Query del processo di Analisi di flusso per l'inserimento di dati*

### <a name="batch-analysis"></a>Analisi batch
Viene anche generato un volume aggiuntivo di set di dati di diagnostica e segnali del veicolo simulati per rendere più completa l'analisi batch. Questo è necessario per garantire un volume di dati rappresentativo per l'elaborazione batch. A questo scopo, viene usata una pipeline denominata "PrepareSampleDataPipeline" nel flusso di lavoro di Azure Data Factory per generare una simulazione di segnali e un set di dati di diagnostica del veicolo equivalenti a un anno. Fare clic su [Attività personalizzata di Data Factory](http://go.microsoft.com/fwlink/?LinkId=717077) per scaricare la soluzione di Visual Studio per l'attività .Net personalizzata di Data factory ed eseguire le personalizzazioni necessarie. 

![Preparazione dei dati di esempio per il flusso di lavoro dell'elaborazione batch](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig7-vehicle-telematics-prepare-sample-data-for-batch-processing.png) 

*Figura 7: Preparazione dei dati di esempio per il flusso di lavoro dell'elaborazione batch*

La pipeline è costituita da un'attività .NET personalizzata di Azure Data Factory, illustrata qui:

![Attività PrepareSampleDataPipeline](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig8-vehicle-telematics-prepare-sample-data-pipeline.png) 

*Figure 8: PrepareSampleDataPipeline*

Dopo aver eseguito correttamente la pipeline e aver contrassegnato come "Ready" il set di dati "RawCarEventsTable", viene generato l'equivalente di un anno di dati di diagnostica e segnali del veicolo. La cartella e il file seguenti creati nell'account di archiviazione vengono visualizzati all'interno del contenitore "connectedcar":

![Output di PrepareSampleDataPipeline](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig9-vehicle-telematics-prepare-sample-data-pipeline-output.png) 

*Figura 9: Output di PrepareSampleDataPipeline*

### <a name="references"></a>Riferimenti
[Azure Event Hub SDK per l'inserimento di flussi](../../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

[Funzionalità di spostamento dei dati di Azure Data Factory](../../data-factory/v1/data-factory-data-movement-activities.md)
[Attività DotNet di Azure Data Factory](../../data-factory/v1/data-factory-use-custom-activities.md)

[Soluzione di Visual Studio per l'attività DotNet di Data factory di Azure per la preparazione dei dati di esempio](http://go.microsoft.com/fwlink/?LinkId=717077) 

## <a name="partition-the-dataset"></a>Partizionare il set di dati
Il set di dati di diagnostica e segnali del veicolo semistrutturati non elaborati viene partizionato durante la fase di preparazione dei dati in un formato ANNO/MESE. Questo partizionamento favorisce l'esecuzione di query più efficienti e un'archiviazione scalabile a lungo termine abilitando il failover da un account BLOB a quello successivo quando il primo account si riempie. 

>[!NOTE] 
>Questo passaggio della soluzione è applicabile solo all'elaborazione batch.

Gestione dati di input e di output:

* I **dati di output** (con etichetta *PartitionedCarEventsTable*) devono essere mantenuti per un lungo periodo di tempo nella forma di base "meno elaborata" dei dati nel "Data Lake" del cliente. 
* I **dati di input** per questa pipeline vengono in genere eliminati perché i dati di output hanno la massima fedeltà all'input, sono semplicemente archiviati (partizionati) meglio per un uso successivo.

![Flusso di lavoro PartitionCarEvents](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig10-vehicle-telematics-partition-car-events-workflow.png)

*Figura 10: Flusso di lavoro PartitionCarEvents*

I dati non elaborati vengono partizionati usando un'attività Hive HDInsight in "PartitionCarEventsPipeline". I dati di esempio generati nel passaggio 1 per un anno vengono partizionati per ANNO/MESE. Le partizioni vengono usate per generare i dati di diagnostica e segnali del veicolo per ogni mese (12 partizioni in totale) di un anno. 

![Attività PartitionCarEventsPipeline](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig11-vehicle-telematics-partition-car-events-pipeline.png)

*Figura 11: PartitionCarEventsPipeline*

***Script Hive PartitionConnectedCarEvents***

Lo script Hive seguente, denominato "partitioncarevents.hql", è usato per il partizionamento e si trova nella cartella "\demo\src\connectedcar\scripts" del file ZIP scaricato. 
    
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

Dopo la corretta esecuzione della pipeline, vengono visualizzate le partizioni seguenti generate nell'account di archiviazione nel contenitore "connectedcar".

![Output partizionato](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig12-vehicle-telematics-partitioned-output.png)

*Figura 12: Output partizionato*

A questo punto, i dati sono ottimizzati, più gestibili e pronti per un'ulteriore elaborazione che permette di ottenere informazioni dettagliate sul batch. 

## <a name="data-analysis"></a>Analisi dei dati
In questa sezione viene illustrato come combinare Analisi di flusso di Azure, Azure Machine Learning, Azure Data Factory e Azure HDInsight per un'analisi avanzata dell'integrità del veicolo e delle abitudini di guida. Sono presenti tre sottosezioni:

1. **Machine Learning**: questa sottosezione contiene informazioni sull'esperimento di rilevamento anomalie usato nella soluzione per eseguire una stima dei veicoli che richiedono interventi di manutenzione e dei veicoli da richiamare a causa di problemi di sicurezza.
2. **Analisi in tempo reale**: questa sottosezione contiene informazioni riguardanti l'analisi in tempo reale con il linguaggio di query di Analisi di flusso e la messa in funzione dell'esperimento di Machine Learning in tempo reale con un'applicazione personalizzata.
3. **Analisi batch**: questa sottosezione contiene informazioni sulla trasformazione e l'elaborazione dei dati batch con Azure HDInsight e Azure Machine Learning messi in funzione da Azure Data Factory.

### <a name="machine-learning"></a>Machine Learning
L'obiettivo è eseguire una stima dei veicoli che richiedono interventi di manutenzione e dei veicoli da richiamare in base a determinate statistiche di integrità. Si parte dai presupposti seguenti:

* Se una delle tre condizioni seguenti è vera, il veicolo richiede un **intervento di manutenzione**:
  
  * La pressione degli pneumatici è bassa
  * Il livello di olio del motore è basso
  * La temperatura del motore è elevata
* Se una delle condizioni seguenti è vera, il veicolo può avere un **problema di sicurezza** e può essere necessario il **richiamo**:
  
  * La temperatura del motore è elevata, ma la temperatura esterna è bassa
  * La temperatura del motore è bassa, ma la temperatura esterna è elevata

In base ai requisiti precedenti, sono stati creati due modelli separati per il rilevamento delle anomalie, uno per gli interventi di manutenzione sul veicolo e uno per il richiamo del veicolo. In entrambi i modelli l'algoritmo di analisi in componenti principali (PCA) predefinito viene usato per il rilevamento anomalie. 

**Modello di rilevamento per la manutenzione**

Se uno dei tre indicatori (pressione degli pneumatici, olio del motore o temperatura del motore) soddisfa la condizione corrispondente, il modello di rilevamento per la manutenzione segnala un'anomalia. Di conseguenza, nella compilazione del modello è sufficiente prendere in considerazione queste tre variabili. Nell'esperimento in Azure Machine Learning viene usato prima un modulo **Seleziona colonne in set di dati** per estrarre queste tre variabili. Viene quindi usato il modulo di rilevamento delle anomalie basato su PCA per compilare il modello di rilevamento delle anomalie. 

L'analisi in componenti principali (PCA) è una tecnica consolidata in Machine Learning che può essere applicata alla selezione di funzionalità, alla classificazione e al rilevamento di anomalie. Converte un set di casi contenente variabili probabilmente correlate in un set di valori denominati componenti principali. Lo scopo primario del modello basato su PCA è la proiezione dei dati in uno spazio dimensionale inferiore in modo che caratteristiche e anomalie siano identificabili più facilmente.

Per ogni nuovo input nel modello di rilevamento, il rilevatore di anomalie calcola prima di tutto la proiezione sugli autovettori e quindi l'errore di ricostruzione normalizzato. L'errore normalizzato costituisce il punteggio dell'anomalia. A un punteggio maggiore corrisponde una maggiore anomalia dell'istanza. 

Nel rilevamento per la manutenzione ogni record può essere considerato come un punto in uno spazio tridimensionale definito dalle coordinate pressione degli pneumatici, olio del motore e temperatura del motore. Per acquisire queste anomalie, è possibile usare l'analisi in componenti principali per proiettare i dati originali nello spazio tridimensionale in uno spazio bidimensionale. Il parametro relativo al numero di componenti da usare in PCA viene quindi impostato su 2. Questo parametro ha un ruolo importante nell'applicazione del rilevamento delle anomalie basato su PCA. Dopo aver eseguito la proiezione dei dati con l'analisi PCA, è possibile identificare più facilmente queste anomalie.

**Modello di rilevamento anomalie per il richiamo** : in questo modello i moduli di rilevamento anomalie basati su PCA e Seleziona colonne in set di dati vengono usati in modo simile. Nello specifico, vengono prima di tutto estratte le tre variabili (temperatura del motore, temperatura esterna e velocità) usando il modulo **Seleziona colonne in set di dati** . Viene inclusa anche la variabile velocità perché la temperatura del motore è in genere correlata alla velocità. Viene quindi usato il modulo di rilevamento delle anomalie basato su PCA per proiettare i dati da uno spazio tridimensionale a uno spazio bidimensionale. I criteri di richiamo sono soddisfatti ed è quindi necessario richiamare il veicolo quando la temperatura del motore e quella esterna sono correlate negativamente. Con un algoritmo di rilevamento delle anomalie basato su PCA è possibile acquisire le anomalie dopo l'analisi PCA. 

Per il training del modello di rilevamento anomalie basato su PCA è sempre necessario usare come dati di input dati normali che non richiedano il richiamo o interventi di manutenzione. Nell'esperimento di assegnazione dei punteggi viene usato il modello di rilevamento anomalie sottoposto a training per rilevare se il veicolo richiede o meno la manutenzione o il richiamo. 

### <a name="real-time-analysis"></a>Analisi in tempo reale
La query SQL di Analisi di flusso seguente viene usata per ottenere la media di tutti i parametri importanti del veicolo, ad esempio la velocità, il livello di carburante, la temperatura del motore, la lettura del contachilometri, la pressione degli pneumatici, il livello di olio del motore e altri. Le medie vengono usate per rilevare anomalie, emettere avvisi e determinare le condizioni generali di integrità dei veicoli usati in un'area specifica correlando tali informazioni a dati demografici. 

![Query di Analisi di flusso per l'elaborazione in tempo reale](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig13-vehicle-telematics-stream-analytics-query-for-real-time-processing.png)

*Figura 13: Query di Analisi di flusso per l'elaborazione in tempo reale*

Tutte le medie vengono calcolate in una finestra a cascata di 3 secondi. In questo caso viene usata la finestra a cascata perché sono necessari intervalli di tempo contigui e non sovrapposti. 

Per altre informazioni sulle funzionalità di "windowing" in Analisi di flusso di Azure, fare clic su [Windowing (Analisi di flusso di Azure)](https://msdn.microsoft.com/library/azure/dn835019.aspx).

**Stima in tempo reale**

Nella soluzione è inclusa un'applicazione per la messa in funzione del modello di Machine Learning in tempo reale. L'applicazione, denominata "RealTimeDashboardApp" viene creata e configurata nell'ambito della distribuzione della soluzione ed esegue le operazioni descritte di seguito:

1. È in attesa di un'istanza di Hub eventi in cui Analisi di flusso pubblica gli eventi in un modello eseguito in modo continuo. ![Query di Analisi di flusso per la pubblicazione di dati](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig14-vehicle-telematics-stream-analytics-query-for-publishing.png) *Figura 14: Query di analisi di flusso per la pubblicazione di dati in un'istanza di Hub eventi di output* 
2. Per ogni evento ricevuto dall'applicazione: 
   
   * i dati vengono elaborati usando l'endpoint del servizio di richiesta-risposta (RRS) di Machine Learning per l'assegnazione dei punteggi, l'endpoint RRS viene pubblicato automaticamente nell'ambito della distribuzione.
   * L'output RRS viene pubblicato in un set di dati di Power BI con le API push.

Questo modello è anche applicabile a scenari di integrazione di un'applicazione line-of-business con il flusso di analisi in tempo reale per avvisi, notifiche e messaggistica immediata.

Fare clic su [Download di RealtimeDashboardApp](http://go.microsoft.com/fwlink/?LinkId=717078) per scaricare la soluzione RealtimeDashboardApp di Visual Studio per le personalizzazioni. 

**Per eseguire l'applicazione dashboard in tempo reale**
1. Estrarre e salvare in locale. ![Cartella RealtimeDashboardApp](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig16-vehicle-telematics-realtimedashboardapp-folder.png) *Figura 16: Cartella RealtimeDashboardApp*  
2. Eseguire l'applicazione RealtimeDashboardApp.exe.
3. Fornire credenziali di Power BI valide, accedere e fare clic su Accetta. ![Accesso dell'app dashboard in tempo reale a Power BI](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig17a-vehicle-telematics-realtimedashboardapp-sign-in-to-powerbi.png) ![Accesso finale dell'app dashboard in tempo reale a Power BI](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig17b-vehicle-telematics-realtimedashboardapp-sign-in-to-powerbi.png) 

*Figura 17: Accesso a Power BI da RealtimeDashboardApp*

>[!NOTE] 
>Per scaricare il set di dati di Power BI, eseguire RealtimeDashboardApp con il parametro "flushdata": 

    RealtimeDashboardApp.exe -flushdata


### <a name="batch-analysis"></a>Analisi batch
L'obiettivo di questa sezione è mostrare come Contoso Motors utilizza le funzionalità di calcolo di Azure per sfruttare i Big Data e ottenere informazioni dettagliate sugli stili di guida, sull'utilizzo e sull'integrità del veicolo. In questo modo è possibile:

* Migliorare l'esperienza del cliente e abbassare i costi fornendo informazioni dettagliate sulle abitudini e sui comportamenti di guida attenti ai consumi.
* Acquisire informazioni sui clienti e sui relativi stili di guida per gestire il processo decisionale e fornire prodotti e servizi di altissimo livello.

In questa soluzione vengono esaminate le metriche seguenti:

1. **Stile di guida aggressivo**: identifica la tendenza di modelli, località, condizioni di guida e periodo dell'anno per ottenere informazioni dettagliate sugli stili di guida aggressivi. Contoso Motors può usare queste informazioni per creare campagne di marketing, nuove funzionalità personalizzate e assicurazioni basate sull'utilizzo.
2. **Stile di guida attento ai consumi**: identifica la tendenza dei modelli, località, condizioni di guida e periodo dell'anno per ottenere informazioni dettagliate sugli stili di guida attenti ai consumi. Contoso Motors può usare queste informazioni per creare campagne di marketing, nuove funzionalità personalizzate e per segnalare al guidatore abitudini di guida attente ai consumi e all'ambiente. 
3. **Previsioni di richiamo**: identifica i modelli che è necessario richiamare in base all'esperimento di Machine Learning per il rilevamento anomalie.

Di seguito vengono esaminate le singole metriche nel dettaglio.

**Stile di guida aggressivo**

I segnali del veicolo e i dati di diagnostica partizionati vengono elaborati nella pipeline denominata "AggresiveDrivingPatternPipeline" che usa Hive per determinare modelli, località, veicoli, condizioni di guida e altri parametri che mostrano uno stile di guida aggressivo.

![Flusso di lavoro per lo stile di guida aggressivo](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig18-vehicle-telematics-aggressive-driving-pattern.png) 
*Figura 18: Flusso di lavoro per lo stile di guida aggressivo*


***Query Hive per lo stile di guida aggressivo***

Lo script Hive denominato "aggresivedriving.hql" usato per l'analisi dello stile di guida aggressivo si trova nella cartella "\demo\src\connectedcar\scripts" del file ZIP scaricato. 

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


Usa una combinazione di posizione del cambio, stato del pedale del freno e velocità del veicolo per rilevare un comportamento di guida spericolato/aggressivo in base allo stile di frenata ad alta velocità. 

Dopo la corretta esecuzione della pipeline, vengono visualizzate le partizioni seguenti generate nell'account di archiviazione nel contenitore "connectedcar".

![Output di AggressiveDrivingPatternPipeline](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig19-vehicle-telematics-aggressive-driving-pattern-output.png) 

*Figura 19: Output di AggressiveDrivingPatternPipeline*

**Stile di guida attento ai consumi**

I segnali del veicolo e i dati di diagnostica partizionati vengono elaborati nella pipeline denominata "FuelEfficientDrivingPatternPipeline". Hive viene usato per determinare modelli, località, veicoli, condizioni di guida e così via, che mostrano uno stile di guida attento ai consumi.

![Stile di guida attento ai consumi](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig19-vehicle-telematics-fuel-efficient-driving-pattern.png) 

*Figura 20: Flusso di lavoro per lo stile di guida attento ai consumi*

***Query Hive per lo stile di guida attento ai consumi***

Lo script Hive denominato "fuelefficientdriving.hql" usato per l'analisi dello stile di guida attento ai consumi si trova nella cartella "\demo\src\connectedcar\scripts" del file ZIP scaricato. 

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


Usa una combinazione di posizione del cambio, stato del pedale del freno, velocità del veicolo e posizione del pedale dell'acceleratore per rilevare un comportamento di guida attento ai consumi in base allo stile di accelerazione e frenata e in base alla velocità. 

Dopo la corretta esecuzione della pipeline, vengono visualizzate le partizioni seguenti generate nell'account di archiviazione nel contenitore "connectedcar".

![Output di FuelEfficientDrivingPatternPipeline](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig20-vehicle-telematics-fuel-efficient-driving-pattern-output.png) 

*Figura 21: Output di FuelEfficientDrivingPatternPipeline*

**Previsioni di richiamo**

L'esperimento di Machine Learning è stato sottoposto a provisioning e pubblicato come servizio Web nell'ambito della distribuzione della soluzione. L'endpoint di assegnazione punteggio batch viene usato all'interno di questo flusso di lavoro, registrato come servizio collegato di Data factory e messo in funzione con l'attività di assegnazione punteggio batch di Data factory.

![Endpoint di Machine Learning](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig21-vehicle-telematics-machine-learning-endpoint.png) 

*Figura 22: Endpoint di Machine Learning registrato come servizio collegato in Data factory*

Il servizio collegato registrato viene usato in DetectAnomalyPipeline per assegnare un punteggio ai dati con il modello di rilevamento delle anomalie. 

![Attività di assegnazione punteggio batch di Machine Learning in Data factory](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig22-vehicle-telematics-aml-batch-scoring.png) 

*Figura 23: Attività di assegnazione punteggio batch di Azure Machine Learning in Data factory* 

Alcuni dei passaggi eseguiti in questa pipeline per la preparazione dei dati ne consentono l'uso con il servizio Web di assegnazione punteggio batch. 

![DetectAnomalyPipeline per la stima dei veicoli da richiamare](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig23-vehicle-telematics-pipeline-predicting-recalls.png) 

*Figura 24: DetectAnomalyPipeline per la stima dei veicoli da richiamare* 

***Query Hive per il rilevamento delle anomalie***

Dopo aver completato l'assegnazione dei punteggi, viene usata un'attività HDInsight per elaborare e aggregare i dati categorizzati come anomalie dal modello con un punteggio minimo di probabilità di 0,60.

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


Dopo la corretta esecuzione della pipeline, vengono visualizzate le partizioni seguenti generate nell'account di archiviazione nel contenitore "connectedcar".

![Output di DetectAnomalyPipeline](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig24-vehicle-telematics-detect-anamoly-pipeline-output.png) 

*Figura 25: Output di DetectAnomalyPipeline*

## <a name="publish"></a>Pubblica

### <a name="real-time-analysis"></a>Analisi in tempo reale
Una delle query del processo di Analisi di flusso pubblica gli eventi in un'istanza di Hub eventi di output. 

![Il processo di Analisi di flusso esegue la pubblicazione in un'istanza di Hub eventi di output](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig25-vehicle-telematics-stream-analytics-job-publishes-output-event-hub.png)

*Figura 26: Il processo di Analisi di flusso esegue la pubblicazione in un'istanza di Hub eventi di output*

![Query di Analisi di flusso per la pubblicazione in un'istanza di Hub eventi di output](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig26-vehicle-telematics-stream-analytics-query-publish-output-event-hub.png)

*Figura 27: Query di Analisi di flusso per la pubblicazione in un'istanza di Hub eventi di output*

Questo flusso di eventi viene utilizzato dall'applicazione RealTimeDashboardApp inclusa nella soluzione. L'applicazione sfrutta il servizio Web di richiesta-risposta di Machine Learning per l'assegnazione dei punteggi in tempo reale e pubblica i dati risultanti in un set di dati di Power BI per l'utilizzo. 

### <a name="batch-analysis"></a>Analisi batch
I risultati dell'elaborazione batch e in tempo reale vengono pubblicati nelle tabelle di database SQL di Azure per l'utilizzo. Il server SQL Azure, il database e le tabelle vengono creati automaticamente con lo script di installazione. 

![Copia dei risultati dell'elaborazione batch nel flusso di lavoro di data mart](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig27-vehicle-telematics-batch-processing-results-copy-to-data-mart.png)

*Figura 28: Copia dei risultati dell'elaborazione batch nel flusso di lavoro di data mart*

![Il processo di Analisi di flusso esegue la pubblicazione in data mart](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig28-vehicle-telematics-stream-analytics-job-publishes-to-data-mart.png)

*Figura 29: Il processo di Analisi di flusso esegue la pubblicazione in data mart*

![Impostazione di data mart nel processo di Analisi di flusso](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig29-vehicle-telematics-data-mart-setting-in-stream-analytics-job.png)

*Figura 30: Impostazione di data mart nel processo di Analisi di flusso*

## <a name="consume"></a>Utilizzo
Power BI offre alla soluzione un dashboard completo per la visualizzazione di dati in tempo reale e di analisi predittive. 

Fare clic qui per informazioni dettagliate su come configurare i report e il dashboard di Power BI. L'aspetto finale del dashboard è questo:

![Dashboard di Power BI](./media/cortana-analytics-playbook-vehicle-telemetry-deep-dive/fig30-vehicle-telematics-powerbi-dashboard.png)

*Figura 31: Dashboard di Power BI*

## <a name="summary"></a>Riepilogo
Questo documento contiene un'analisi dettagliata e approfondita della soluzione di analisi dei dati di telemetria del veicolo. Questa presenta un modello di architettura lambda per l'analisi batch e in tempo reale completa di stime e azioni. Il modello si applica a una vasta gamma di casi d'uso che richiedono l'analisi del percorso critico (in tempo reale) e di quello non critico (batch). 


