<properties
	pageTitle="Panoramica di Microsoft Azure IoT Suite | Microsoft Azure"
	description="Fornisce una panoramica di Azure IoT Suite compresa la creazione di pacchetti e le soluzioni preconfigurate."
	services=""
    suite="iot-suite"
	documentationCenter=""
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-suite"
     ms.devlang="na"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="11/27/2015"
     ms.author="dobett"/>

# Panoramica di Azure IoT Suite

I servizi di Azure IoT offrono una vasta gamma di funzionalità. Questi servizi di livello aziendale consentono di:

- Raccogliere dati dai dispositivi
- Analizzare i flussi dei dati in movimento
- Archiviare ed eseguire query su set di dati di grandi dimensioni
- Visualizzare i dati in tempo reale e cronologici
- Eseguire l'integrazione con i sistemi back-office

I pacchetti Azure IoT Suite insieme ottimizzano comunemente i servizi di Azure con estensioni personalizzate come soluzioni preconfigurate. Queste soluzioni preconfigurate sono implementazioni di base dei modelli di soluzione IoT comuni che contribuiscono a ridurre i tempi di distribuzione delle soluzioni IoT. Con gli [IoT Software Development Kit][lnk-sdks] è possibile personalizzare facilmente queste soluzioni preconfigurate per soddisfare i requisiti o per usarle come esempi durante lo sviluppo di nuove soluzioni.

Nel video seguente viene fornita un'introduzione ad Azure IoT Suite:

> [AZURE.VIDEO azurecon-2015-introducing-the-microsoft-azure-iot-suite]

## Servizi di Azure IoT in Azure IoT Suite

Il servizio principale di Azure IoT Suite è [Hub IoT di Azure][lnk-iot-hub]. Questo servizio fornisce funzionalità di messaggistica da dispositivo a cloud e da cloud a dispositivo e funge da gateway per il cloud e gli altri servizi chiave di IoT Suite.

[Analisi d flusso di Azure][lnk-asa] fornisce l'analisi dei dati in movimento. IoT Suite si basa su questo servizio per elaborare i dati di telemetria in entrata, eseguire operazioni di aggregazione e rilevare gli eventi. Le soluzioni preconfigurate usando anche l'analisi di flusso per elaborare i messaggi informativi che contengono dati, ad esempio i metadati o le risposte ai comandi dai dispositivi.

[Archiviazione di Azure][lnk-azure-storage] e [Azure DocumentDB][lnk-document-db] forniscono le funzionalità di archiviazione dei dati. Le soluzioni preconfigurate usano l'archivio BLOB per archiviare i dati di telemetria e renderli disponibili per l'analisi. Le soluzioni preconfigurate usano l'archiviazione indicizzata nella funzionalità dati semistrutturati di DocumentDB per gestire i metadati dei dispositivi. In questo modo le soluzioni possono gestire dispositivi eterogenei con diversi requisiti di archiviazione del contenuto.

[App Web di azure][lnk-web-apps] e [Microsoft Power BI][lnk-power-bi] forniscono le funzionalità di visualizzazione dei dati. La flessibilità di Power BI consente di creare rapidamente i propri dashboard interattivi che usano i dati di IoT Suite.

Per una panoramica dell'architettura di una tipica soluzione IoT, vedere [Microsoft Azure e Internet of Things (IoT)][iot-suite-what-is-azure-iot].

## Soluzioni preconfigurate

IoT Suite include soluzioni preconfigurate che consentono di iniziare rapidamente ed esplorare gli scenari comuni di IoT resi possibili da Azure IoT Suite. È possibile distribuire le soluzioni preconfigurate per la sottoscrizione di Azure e quindi eseguire una soluzione IoT end-to-end completa.

## Passaggi successivi

Per altre informazioni sulle soluzioni preconfigurate in IoT Suite, vedere [Che cosa sono le soluzioni preconfigurate di Azure IoT?][lnk-what-are-preconfig]

Per iniziare a usare una delle soluzioni preconfigurate, vedere [Introduzione alle soluzioni preconfigurate IoT][lnk-preconfig-start].

Per altre informazioni sul servizio IoT Hub di Azure, vedere la [Documentazione sull'hub IoT][lnk-iot-hub].


[lnk-sdks]: https://azure.microsoft.com/documentation/articles/iot-hub-sdks-summary/
[lnk-iot-hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-azure-storage]: https://azure.microsoft.com/documentation/services/storage/
[lnk-document-db]: https://azure.microsoft.com/documentation/services/documentdb/
[lnk-power-bi]: https://powerbi.microsoft.com/
[lnk-web-apps]: https://azure.microsoft.com/documentation/services/app-service/web/
[iot-suite-what-is-azure-iot]: iot-suite-what-is-azure-iot.md
[lnk-what-are-preconfig]: iot-suite-what-are-preconfigured-solutions.md
[lnk-preconfig-start]: iot-suite-getstarted-preconfigured-solutions/
[lnk-iot-hub]: https://azure.microsoft.com/documentation/services/iot-hub/

<!---HONumber=AcomDC_0128_2016-->