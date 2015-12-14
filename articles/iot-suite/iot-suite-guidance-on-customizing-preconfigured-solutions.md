<properties
	pageTitle="Personalizzazione di soluzioni preconfigurate | Microsoft Azure"
	description="Fornisce una guida alla personalizzazione delle soluzioni preconfigurate di Azure IoT Suite."
	services=""
	documentationCenter=".net"
	authors="stevehob"
	manager="timlt"
	editor=""/>

<tags
     ms.service="na"
     ms.devlang="dotnet"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/29/2015"
     ms.author="stevehob"/>

# Personalizzazione di soluzioni preconfigurate

Le soluzioni preconfigurate disponibili in Azure IoT Suite dimostrano come i servizi nella suite si integrano per fornire una soluzione end-to-end. Esistono poi diverse posizioni in cui è possibile personalizzare ed estendere la soluzione per adattarla a scenari specifici. Le sezioni seguenti descrivono questi punti di personalizzazione comuni.

## Ricerca del codice sorgente

Il codice sorgente per la soluzione preconfigurata è disponibile in GitHub nei repository seguenti:

- Monitoraggio remoto: [https://www.github.com/Azure/azure-iot-remote-monitoring](https://github.com/Azure/azure-iot-remote-monitoring)

Questa origine viene fornita per illustrare un modello di implementazione delle funzionalità principali di monitoraggio remoto con Azure IoT Suite.

## Modifica delle regole preconfigurate

La soluzione per il monitoraggio remoto include due processi di [Analisi di flusso di Azure](http://azure.microsoft.com/services/stream-analytics) per implementare la telemetria e la logica di avviso visualizzate nel dashboard.

Il primo processo seleziona tutti i dati del flusso in ingresso della telemetria e crea due diversi output. Il processo viene denominato **[nome soluzione]-Telemetry**.

- Il primo output accetta tutti i dati usando `SELECT *` e li memorizza nell'archivio BLOB. In questo archivio BLOB il dashboard legge i valori non elaborati per creare i grafici.
- Il secondo output esegue un calcolo `AVG()`, `MIN()` e `MAX()` in una finestra temporale scorrevole di 5 minuti. Questi dati vengono mostrati nelle composizioni nel dashboard.

Con l'interfaccia utente di analisi di flusso, è possibile modificare questi processi direttamente per alterare la logica o aggiungere una logica specifica allo scenario.

Il secondo processo interviene sui valori Device-to-Threshold creati nella pagina **Regole** della soluzione. Questo processo utilizza come dati di riferimento il valore soglia impostato per ogni dispositivo. Confronta il valore soglia per verificare se è maggiore (`>`) del valore effettivo. Questo processo può essere modificato, ad esempio, per cambiare l'operatore di confronto.

> [AZURE.NOTE]Il dashboard per il monitoraggio remoto dipende da dati specifici, quindi la modifica dei processi può causare un errore del dashboard.

## Aggiunta di regole personalizzate

Oltre a modificare i processi preconfigurati di analisi di flusso di Azure, è possibile usare il portale di Azure per aggiungere nuovi processi o nuove query ai processi esistenti.

## Personalizzazione dei dispositivi

Una delle attività di estensione più comuni è l'uso di dispositivi specifici per lo scenario. Esistono diversi metodi per usare i dispositivi, tra cui modificare un dispositivo simulato in modo che corrisponda allo scenario o usare l'[SDK per dispositivi IoT][] per connettere il dispositivo fisico alla soluzione.

Per indicazioni dettagliate sull'aggiunta di dispositivi alla soluzione preconfigurata per il monitoraggio remoto, vedere il documento relativo ai [dispositivi di connessione a IoT Suite](iot-suite-connecting-devices.md).

### Creazione del dispositivo simulato

Nel codice sorgente della soluzione per il monitoraggio remoto (a cui viene fatto riferimento più indietro) è incluso un simulatore .NET. Il provisioning di questo simulatore viene eseguito nell'ambito della soluzione ed è possibile modificarlo per inviare metadati diversi e la telemetria o per rispondere a comandi diversi.

Azure IoT fornisce inoltre un [esempio di SDK C](https://github.com/Azure/azure-iot-sdks/c/serializer/samples/remote_monitoring) progettato per usare la soluzione preconfigurata per il monitoraggio remoto.

### Compilazione e uso del proprio dispositivo (fisico)

Gli [SDK Azure IoT](https://github.com/Azure/azure-iot-sdks) forniscono librerie per la connessione di numerosi tipi di dispositivi (linguaggi e sistemi operativi) alle soluzioni IoT.

## Passaggi successivi

Per altre informazioni sui dispositivi IoT, vedere il [Sito per sviluppatori Azure IoT](http://azure.microsoft.com/develop/iot) per trovare collegamenti e documentazione.

[SDK per dispositivi IoT]: https://azure.microsoft.com/documentation/articles/iot-hub-sdks-summary/

<!---HONumber=AcomDC_1203_2015-->