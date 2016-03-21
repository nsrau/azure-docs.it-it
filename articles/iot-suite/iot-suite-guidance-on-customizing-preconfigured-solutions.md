<properties
	pageTitle="Personalizzazione di soluzioni preconfigurate | Microsoft Azure"
	description="Fornisce una guida alla personalizzazione delle soluzioni preconfigurate di Azure IoT Suite."
	services=""
    suite="iot-suite"
	documentationCenter=".net"
	authors="stevehob"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-suite"
     ms.devlang="dotnet"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="03/02/2016"
     ms.author="stevehob"/>

# Personalizzazione di soluzioni preconfigurate

Le soluzioni preconfigurate disponibili in Azure IoT Suite dimostrano come i servizi nella suite si integrano per fornire una soluzione end-to-end. Esistono poi diverse posizioni in cui è possibile personalizzare ed estendere la soluzione per adattarla a scenari specifici. Le sezioni seguenti descrivono questi punti di personalizzazione comuni.

## Ricerca del codice sorgente

Il codice sorgente per le soluzioni preconfigurate è disponibile in GitHub nei repository seguenti:

- Monitoraggio remoto: [https://www.github.com/Azure/azure-iot-remote-monitoring](https://github.com/Azure/azure-iot-remote-monitoring)
- Manutenzione predittiva: [https://github.com/Azure/azure-iot-predictive-maintenance](https://github.com/Azure/azure-iot-predictive-maintenance)

Il codice sorgente per le soluzioni preconfigurate viene fornito per illustrare i modelli e le procedure usate per implementare la funzionalità end-to-end di una soluzione IoT tramite Azure IoT Suite. Informazioni aggiuntive sulla creazione e distribuzione dalla riga di comando sono disponibili nella wiki di GitHub per ogni soluzione preconfigurata:

- [Wiki sul monitoraggio remoto](https://github.com/Azure/azure-iot-remote-monitoring/wiki)
- [Wiki sulla manutenzione predittiva](https://github.com/Azure/azure-iot-predictive-maintenance/wiki)

## Gestire le autorizzazioni in una soluzione preconfigurata
Il portale della soluzione per ogni soluzione preconfigurata viene creato come nuova applicazione Azure Active Directory. È possibile gestire le autorizzazioni per il portale della soluzione (applicazione AAD) come indicato di seguito:

1. Aprire il [portale di Azure classico](https://manage.windowsazure.com).
2. Passare all'applicazione AAD selezionando **Applicazioni di proprietà dell'azienda** e facendo clic sul segno di spunta.
3. Passare a **Utenti** e assegnare un ruolo ai membri nel tenant di Azure Active Directory. 

Per impostazione predefinita, l'applicazione ha ruoli **amministratore**, **in sola lettura** e **sola lettura implicita**. Il ruolo di **sola lettura implicita** viene concesso agli utenti che sono membri del tenant di Azure Active Directory, ma a cui non è stato assegnato un ruolo. È possibile modificare [RolePermissions.cs](https://github.com/Azure/azure-iot-remote-monitoring/blob/master/DeviceAdministration/Web/Security/RolePermissions.cs) dopo aver duplicato il repository GitHub e quindi ridistribuire la soluzione.

## Modifica delle regole preconfigurate

La soluzione per il monitoraggio remoto include tre processi di [Analisi di flusso di Azure](https://azure.microsoft.com/services/stream-analytics/) per implementare le informazioni sul dispositivo, la telemetria e la logica delle regole visualizzate per la soluzione.

I tre processi di analisi di flusso e la relativa sintassi sono descritti in dettaglio in [Procedura dettagliata della soluzione preconfigurata per il monitoraggio remoto](iot-suite-remote-monitoring-sample-walkthrough.md).

È possibile modificare questi processi direttamente per alterare la logica o aggiungere una logica specifica allo scenario. È possibile trovare i processi di analisi di flusso come segue:
 
1. Accedere al [portale di Azure](https://portal.azure.com).
2. Passare a un nuovo gruppo di risorse con lo stesso nome della soluzione IoT. 
3. Selezionare il processo di Analisi di flusso di Azure che si vuole modificare. 
4. Arrestare il processo selezionando **Arresta** nel set di comandi. 
5. Modificare i valori di input, query e output.

    Una modifica semplice consiste nel modificare la query per il processo **Regole** in modo da usare **"<"** anziché **">"**. Il portale della soluzione visualizzerà ancora **">"** quando si modifica una regola, ma si noterà che il comportamento viene capovolto a causa della modifica del processo sottostante.

6. Avviare il processo

> [AZURE.NOTE] Il dashboard per il monitoraggio remoto dipende da dati specifici, quindi la modifica dei processi può causare un errore del dashboard.

## Aggiunta di regole personalizzate

Oltre a modificare i processi preconfigurati di analisi di flusso di Azure, è possibile usare il portale di Azure per aggiungere nuovi processi o nuove query ai processi esistenti.

## Personalizzazione dei dispositivi

Una delle attività di estensione più comuni è l'uso di dispositivi specifici per lo scenario. Esistono diversi metodi per usare i dispositivi, tra cui modificare un dispositivo simulato in modo che corrisponda allo scenario o usare l'[SDK per dispositivi IoT][] per connettere il dispositivo fisico alla soluzione.

Per indicazioni dettagliate sull'aggiunta di dispositivi alla soluzione preconfigurata per il monitoraggio remoto, vedere il documento relativo ai [dispositivi di connessione a IoT Suite](iot-suite-connecting-devices.md).

### Creazione del dispositivo simulato

Nel codice sorgente della soluzione per il monitoraggio remoto (a cui viene fatto riferimento più indietro) è incluso un simulatore .NET. Il provisioning di questo simulatore viene eseguito nell'ambito della soluzione ed è possibile modificarlo per inviare metadati diversi e la telemetria o per rispondere a comandi diversi.

Il simulatore preconfigurato nella soluzione preconfigurata di monitoraggio remoto è un dispositivo più freddo che emette dati di telemetria su temperatura e umidità e che può essere modificato nel progetto [Simulator.WebJob](https://github.com/Azure/azure-iot-remote-monitoring/tree/master/Simulator/Simulator.WebJob) quando è stato duplicato il repository GitHub.

Azure IoT fornisce inoltre un [esempio di SDK C](https://github.com/Azure/azure-iot-sdks/c/serializer/samples/remote_monitoring) progettato per usare la soluzione preconfigurata per il monitoraggio remoto.

### Compilazione e uso del proprio dispositivo (fisico)

Gli [SDK Azure IoT](https://github.com/Azure/azure-iot-sdks) forniscono librerie per la connessione di numerosi tipi di dispositivi (linguaggi e sistemi operativi) alle soluzioni IoT.

## Passaggi successivi

Per altre informazioni relative a una personalizzazione, aggiungere suggerimenti sulle funzionalità in [Suggerimenti dell'utente](https://feedback.azure.com/forums/321918-azure-iot), inserire commenti su questo articolo nella parte finale della pagina o inviare un messaggio di posta elettronica all'indirizzo iotsolhelp@microsoft.com.

Per altre informazioni sui dispositivi IoT, vedere il [Sito per sviluppatori Azure IoT](https://azure.microsoft.com/develop/iot/) per trovare collegamenti e documentazione.

[SDK per dispositivi IoT]: https://azure.microsoft.com/documentation/articles/iot-hub-sdks-summary/

<!---HONumber=AcomDC_0309_2016-->