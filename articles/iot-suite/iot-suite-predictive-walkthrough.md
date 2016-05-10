<properties
 pageTitle="Procedura dettagliata per la manutenzione predittiva | Microsoft Azure"
 description="Una procedura dettagliata della soluzione preconfigurata di manutenzione predittiva di Azure IoT."
 services=""
 suite="iot-suite"
 documentationCenter=""
 authors="aguilaaj"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-suite"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="03/02/2016"
 ms.author="araguila"/>

# Procedura dettagliata della soluzione preconfigurata di manutenzione predittiva

## Introduzione

La soluzione preconfigurata di manutenzione predittiva di IoT Suite è una soluzione end-to-end per uno scenario aziendale che consente di stimare il punto in cui è probabile che si verifichino errori. È possibile sfruttare questa soluzione preconfigurata in modo proattivo per attività quale l'ottimizzazione della manutenzione. La soluzione combina servizi chiave di Azure IoT Suite , tra cui un'area di lavoro completa di [Azure Machine Learning][lnk_machine_learning] con esperimenti per stimare la vita utile rimanente di un motore di aereo in base a un set di dati di esempio pubblico. La soluzione fornisce un'implementazione completa dello scenario aziendale come punto di partenza per poter pianificare e implementare questo tipo di soluzione IoT per soddisfare i propri requisiti aziendali.

## Architettura logica

Il diagramma seguente illustra i componenti logici della soluzione preconfigurata:

![][img-architecture]

Gli elementi blu sono servizi di Azure di cui viene effettuato il provisioning nella posizione selezionata durante il provisioning della soluzione preconfigurata. È possibile effettuare il provisioning della soluzione preconfigurata nell'area Stati Uniti orientali, Europa settentrionale o Asia orientale.

Alcune risorse non sono disponibili nelle aree in cui si effettua il provisioning della soluzione preconfigurata. Gli elementi arancioni nel diagramma rappresentano i servizi di Azure con provisioning nell'area più vicina disponibile (Stati Uniti centro-meridionali, Europa occidentale o Asia sudorientale), data l'area selezionata.

L'elemento verde è un dispositivo simulato che rappresenta un motore di aereo. Altre informazioni su questi dispositivi simulati sono disponibili di seguito.

Gli elementi grigi rappresentano i componenti che implementano le funzionalità di *amministrazione del dispositivo*. La versione corrente della soluzione preconfigurata di manutenzione predittiva non effettua il provisioning di queste risorse. Per altre informazioni sull'amministrazione del dispositivo, vedere la [soluzione preconfigurata per il monitoraggio remoto][lnk-remote-monitoring].

## Dispositivi simulati

Nella soluzione preconfigurata un dispositivo simulato rappresenta un motore di aereo. La soluzione prende in esame due motori di 2 associati a un singolo aereo. Ogni motore genera 4 tipi di dati di telemetria: sensore 9, sensore 11, sensore 14 e sensore 15 che forniscono i dati necessari per il modello di Machine Learning che calcola la vita utile rimanente per il motore. Ogni dispositivo simulato invia i messaggi di telemetria seguenti all'hub IoT:

*Conteggio dei cicli*. Un ciclo rappresenta un volo completato di lunghezza variabile compresa tra 2-10 ore in cui i dati di telemetria vengono acquisiti ogni mezz'ora per la durata del volo.

*Telemetria* Sono presenti 4 sensori che rappresentano gli attributi del motore. I sensori sono indicati genericamente con l'etichetta Sensore 9, Sensore 11, Sensore 14 e Sensore 15. Questi 4 sensori rappresentano i dati di telemetria sufficienti per ottenere risultati utili dal modello di Machine Learning per la vita utile rimanente. Questo modello viene creato da un set di dati pubblico che include i dati reali dei sensori del motore. Per altre informazioni sulla modalità di creazione del modello dal set di dati originale, vedere il [modello di manutenzione predittiva in Cortana Intelligence Gallery][lnk-cortana-analytics].

I dispositivi simulati possono gestire i comandi seguenti inviati da un hub IoT:

| Comando | Descrizione |
|---------|-------------|
| StartTelemetry | Controlla lo stato della simulazione.<br/>Avvia il dispositivo che invia i dati di telemetria |
| StopTelemetry | Controlla lo stato della simulazione.<br/>Arresta il dispositivo che invia i dati di telemetria |

L'hub IoT fornisce il riconoscimento dei comandi del dispositivo.

## Processo di Analisi di flusso di Azure

**Processo: Telemetria** agisce sul flusso di dati di telemetria in ingresso dai dispositivi con due istruzioni. La prima seleziona tutti i dati di telemetria dai dispositivi e li invia all'archivio BLOB da dove vengono visualizzati nell'app Web. La seconda istruzione calcola i valori medi dei sensori in una finestra temporale scorrevole di due minuti e invia i dati tramite l'Hub eventi a un **processore di eventi**.

## Processore di eventi

Il **processore di eventi** accetta i valori medi dei sensori per un ciclo completo e li passa a un'API che espone il modello con training di Machine Learning per calcolare la vita utile rimanente per un motore.

## Azure Machine Learning

Per altre informazioni sulla modalità di creazione del modello dal set di dati originale, vedere il [modello di manutenzione predittiva in Cortana Intelligence Gallery][lnk-cortana-analytics].

## Ecco come iniziare

In questa sezione illustra i componenti della soluzione, viene descritto il caso di utilizzo previsto e fornisce esempi.

### Dashboard di manutenzione predittiva

Questa pagina dell'applicazione Web usa i controlli JavaScript di Power BI (vedere il [repository di oggetti visivi di Power BI][lnk-powerbi]) per visualizzare:

- I dati di output dei processi di Analisi di flusso nell'archivio BLOB.
- La vita utile rimanente Il conteggio dei cicli per motore di aereo.

### Osservare il comportamento della soluzione cloud

È possibile visualizzare le risorse con provisioning visitando il portale di Azure e passando al gruppo di risorse con il nome della soluzione scelto.

![][img-resource-group]

Quando si esegue il provisioning della soluzione preconfigurata, viene visualizzato un messaggio di posta elettronica con un collegamento all'area di lavoro di Machine Learning. È anche possibile passare all'area di lavoro di Machine Learning dalla pagina [azureiotsuite.com][lnk-azureiotsuite] per la soluzione fornita quando il relativo stato è **Pronto**.

![][img-machine-learning]

Nel portale della soluzione si noterà che l'esempio include quattro dispositivi simulati per rappresentare 2 aerei con 2 motori per aereo e 4 sensori per ogni motore. Quando si accede per la prima volta al portale della soluzione, la simulazione viene arrestata.

![][img-simulation-stopped]

Fare clic su **Start simulation** per iniziare la simulazione in cui si potrà osservare che la cronologia dei sensori, la vita utile rimanente, i cicli e la cronologia della vita utile rimanente sono riportati nel dashboard.

![][img-simulation-running]

Quando la vita utile rimanente è inferiore a 160, una soglia arbitraria scelta a scopo dimostrativo, il portale della soluzione mostra un simbolo di avviso accanto alla visualizzazione della vita utile rimanente e colora in giallo il motore dell'aereo nell'immagine. Si noterà che i valori della vita utile rimanente hanno complessivamente una tendenza generale al ribasso, ma tendono a oscillare in alto o in basso. Ciò è dovuto alle diverse lunghezze dei cicli e all'accuratezza del modello.

![][img-simulation-warning]

La simulazione completa richiede circa 35 minuti per completare 148 cicli. La soglia di 160 per la vita utile rimanente viene raggiunta per la prima volta dopo circa 5 minuti ed entrambi i motori la raggiungono a circa 8 minuti.

La simulazione viene eseguita sul set di dati completo per 148 cicli e si stabilizza sui valori finali dei cicli e della vita utile rimanente.

È possibile arrestare la simulazione in qualsiasi punto, ma facendo clic su **Start Simulation** viene riprodotta la simulazione dall'inizio del set di dati.

## Passaggi successivi

Dopo aver eseguito la soluzione di manutenzione predittiva preconfigurata è consigliabile modificarla. Vedere le indicazioni in [Personalizzazione di soluzioni preconfigurate][lnk-customize].

Il post del blog TechNet di [approfondimento sulla manutenzione predittiva in IoT Suite](http://social.technet.microsoft.com/wiki/contents/articles/33527.iot-suite-under-the-hood-predictive-maintenance.aspx) fornisce altri dettagli sulla soluzione di manutenzione predittiva preconfigurata.

  
[img-architecture]: media/iot-suite-predictive-walkthrough/architecture.png
[img-resource-group]: media/iot-suite-predictive-walkthrough/resource-group.png
[img-machine-learning]: media/iot-suite-predictive-walkthrough/machine-learning.png
[img-simulation-stopped]: media/iot-suite-predictive-walkthrough/simulation-stopped.png
[img-simulation-running]: media/iot-suite-predictive-walkthrough/simulation-running.png
[img-simulation-warning]: media/iot-suite-predictive-walkthrough/simulation-warning.png

[lnk-powerbi]: https://www.github.com/Microsoft/PowerBI-visuals
[lnk_machine_learning]: https://azure.microsoft.com/services/machine-learning/
[lnk-remote-monitoring]: iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-cortana-analytics]: http://gallery.cortanaintelligence.com/Collection/Predictive-Maintenance-Template-3
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md

<!---HONumber=AcomDC_0504_2016-->