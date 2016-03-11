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
     ms.topic="get-started-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="02/19/2016"
     ms.author="dobett"/>

# Panoramica di Azure IoT Suite

I servizi di Azure IoT offrono una vasta gamma di funzionalità. Questi servizi di livello aziendale consentono di:

- Raccogliere dati dai dispositivi
- Analizzare i flussi dei dati in movimento
- Archiviare ed eseguire query su set di dati di grandi dimensioni
- Visualizzare i dati in tempo reale e cronologici
- Eseguire l'integrazione con i sistemi back-office

Per offrire queste funzionalità, Azure IoT Suite include più servizi di Azure con estensioni personalizzate come *soluzioni preconfigurate*. Queste soluzioni preconfigurate sono implementazioni di base dei modelli di soluzione IoT comuni che contribuiscono a ridurre i tempi di distribuzione delle soluzioni IoT. I [kit di sviluppo software IoT][lnk-sdks] consentono di personalizzare ed estendere le soluzioni per soddisfare le proprie esigenze. È anche possibile usare queste soluzioni come esempi o modelli durante lo sviluppo di nuove soluzioni IoT.

Nel video seguente viene fornita un'introduzione ad Azure IoT Suite:

> [AZURE.VIDEO azurecon-2015-introducing-the-microsoft-azure-iot-suite]

## Servizi di Azure IoT in Azure IoT Suite

Le soluzioni preconfigurate usano in genere i servizi seguenti:

- Il servizio principale di Azure IoT Suite è [Hub IoT di Azure][lnk-iot-hub]. Questo servizio fornisce funzionalità di messaggistica da dispositivo a cloud e da cloud a dispositivo e funge da gateway per il cloud e gli altri servizi chiave di IoT Suite. Il servizio consente di ricevere messaggi dai dispositivi su vasta scala e inviare i comandi ai dispositivi.

- [Analisi d flusso di Azure][lnk-asa] fornisce l'analisi dei dati in movimento. IoT Suite si basa su questo servizio per elaborare i dati di telemetria in entrata, eseguire operazioni di aggregazione e rilevare gli eventi. Le soluzioni preconfigurate usando anche l'analisi di flusso per elaborare i messaggi informativi che contengono dati, ad esempio i metadati o le risposte ai comandi dai dispositivi. Le soluzioni usano Analisi di flusso per elaborare i messaggi dei dispositivi e inviare i messaggi ad altri servizi.

- [Archiviazione di Azure][lnk-azure-storage] e [Azure DocumentDB][lnk-document-db] forniscono le funzionalità di archiviazione dei dati. Le soluzioni preconfigurate usano l'archivio BLOB per archiviare i dati di telemetria e renderli disponibili per l'analisi. Le soluzioni usano DocumentDB per archiviare i metadati e abilitare le funzionalità di gestione di dispositivi delle soluzioni.

- [App Web di azure][lnk-web-apps] e [Microsoft Power BI][lnk-power-bi] forniscono le funzionalità di visualizzazione dei dati. La flessibilità di Power BI consente di creare rapidamente i propri dashboard interattivi che usano i dati di IoT Suite.

Per una panoramica dell'architettura di una tipica soluzione IoT, vedere [Microsoft Azure e Internet of Things (IoT)][iot-suite-what-is-azure-iot].

## Soluzioni preconfigurate

IoT Suite include soluzioni preconfigurate che consentono di iniziare rapidamente e di esplorare gli scenari comuni di IoT resi possibili da Azure IoT Suite, come il *monitoraggio remoto* e la *manutenzione predittiva*. È possibile distribuire queste soluzioni nella sottoscrizione di Azure e quindi eseguire uno scenario IoT end-to-end completo.

## Passaggi successivi

Dopo aver individuato cosa è possibile fare con IoT Suite e quali sono i componenti principali, è possibile:

- Trovare altre informazioni sulle soluzioni preconfigurate in IoT Suite, nell'articolo [Informazioni sulle soluzioni preconfigurate di Azure IoT Suite][lnk-what-are-preconfig].

- Iniziare a usare una delle soluzioni preconfigurate, come descritto nell'[Esercitazione: Introduzione alle soluzioni preconfigurate IoT][lnk-preconfig-start].

- Trovare altre informazioni sul servizio hub IoT di Azure, nella pagina relativa alla [documentazione sull'hub IoT][lnk-iot-hub].


[lnk-sdks]: https://azure.microsoft.com/documentation/articles/iot-hub-sdks-summary/
[lnk-iot-hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-azure-storage]: https://azure.microsoft.com/documentation/services/storage/
[lnk-document-db]: https://azure.microsoft.com/documentation/services/documentdb/
[lnk-power-bi]: https://powerbi.microsoft.com/
[lnk-web-apps]: https://azure.microsoft.com/documentation/services/app-service/web/
[iot-suite-what-is-azure-iot]: iot-suite-what-is-azure-iot.md
[lnk-what-are-preconfig]: iot-suite-what-are-preconfigured-solutions.md
[lnk-preconfig-start]: iot-suite-getstarted-preconfigured-solutions.md
[lnk-iot-hub]: https://azure.microsoft.com/documentation/services/iot-hub/

<!----HONumber=AcomDC_0224_2016-->