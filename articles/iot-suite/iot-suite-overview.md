<properties
	pageTitle="Panoramica di Microsoft Azure IoT Suite | Microsoft Azure"
	description="Fornisce una panoramica di Azure IoT Suite compresa la creazione di pacchetti e le soluzioni preconfigurate."
	services=""
	documentationCenter=""
	authors="aguilaaj"
	manager="timlt"
	editor=""/>

<tags
     ms.service="na"
     ms.devlang="na"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="10/06/2015"
     ms.author="araguila"/>

# Panoramica di Azure IoT Suite

Microsoft offre un'ampia gamma di funzionalità con i servizi Azure IoT. Questi servizi offrono funzionalità di livello aziendale per raccogliere dati dai dispositivi, analizzare i flussi di dati in movimento, archiviare e interrogare set di dati di grandi dimensioni, visualizzare dati cronologici e in tempo reale e integrarsi con sistemi back-office importanti. Azure IoT Suite raggruppa una serie di servizi comunemente usati e di funzionalità estese per ridurre il rapporto del tempo rispetto al valore per i clienti. Queste estensioni comprendono soluzioni preconfigurate che offrono un'implementazione di base dei modelli di soluzioni più comuni. In combinazione con i kit di sviluppo software IoT (SDK), i clienti sono in grado di personalizzare facilmente le soluzioni preconfigurate o di sfruttarle come esempi per lo sviluppo di nuove soluzioni.

Nel video seguente viene fornita un'introduzione ad Azure IoT Suite:

> [AZURE.VIDEO azurecon-2015-introducing-the-microsoft-azure-iot-suite]

## Servizi di Azure IoT in Azure IoT Suite

Il servizio principale di Azure IoT Suite è hub IoT. Questo servizio fornisce funzionalità di messaggistica dal dispositivo a cloud e dal cloud al servizio. Agisce da gateway nel cloud e negli altri servizi chiave di IoT Suite.

Analisi d flusso di Azure fornisce il servizio di analisi dei dati in movimento, che viene usato da IoT Suite per l'elaborazione dei messaggi di telemetria in ingresso, l'esecuzione dell'aggregazione e il rilevamento degli eventi. Tale servizio viene inoltre usato per elaborare messaggi informativi che possono essere usati per la risposta del comando o dei metadati del dispositivo.

Le funzionalità di archiviazione dei dati sono abilitate tramite una combinazione di Archiviazione di Azure e Azure DocumentDB. Archiviazione di Azure abilita l'archiviazione BLOB della telemetria per la conservazione e l'analisi futura. DocumentDB viene usato per le funzionalità di archiviazione indicizzate su dati semi strutturati in modo da gestire i metadati del dispositivo. In questo modo, viene abilitata la gestione di dispositivi eterogenei consentendo a più dispositivi di disporre di contenuti diversi.

Le funzionalità di visualizzazione dei dati vengono fornite da una combinazione di Siti Web di Azure e Microsoft Power BI. La flessibilità di Power BI consente ai clienti di realizzare rapidamente le proprie dashboard interattive a partire dai dati di IoT Suite.

Altri dettagli sull'architettura e su come vengono usati questi servizi sono disponibili nell'articolo relativo a [Microsoft Azure e Internet delle cose (IoT)](iot-suite-what-is-azure-iot.md).

## Soluzioni preconfigurate

In Azure IoT Suite sono incluse soluzioni preconfigurate per consentire ai clienti di iniziare rapidamente ed esplorare scenari resi possibili dalla Azure IoT Suite.

La prima soluzione preconfigurata disponibile è [Monitoraggio remoto](iot-suite-what-are-preconfigured-solutions.md).

<!---HONumber=Nov15_HO1-->