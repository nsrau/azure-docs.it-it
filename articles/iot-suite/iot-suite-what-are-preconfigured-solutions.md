---
title: Panoramica delle soluzioni preconfigurate di Azure IoT Suite | Microsoft Docs
description: Descrizione delle soluzioni preconfigurate di Azure IoT Suite e della relativa architettura, con collegamenti a risorse aggiuntive.
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 59009f37-9ba0-4e17-a189-7ea354a858a2
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 17/01/2018
ms.author: dobett
ms.openlocfilehash: b29c5fe9543c5fe6da9ff52da6549fdcacff868f
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2018
---
# <a name="what-is-azure-iot-suite"></a>Che cos'è Azure IoT Suite?

Azure IoT Suite è un set di *soluzioni preconfigurate* con le caratteristiche seguenti:

* Possono essere distribuite in pochi minuti
* Consentono di essere subito operativi
* Possono essere personalizzate in base a requisiti specifici

Le soluzioni preconfigurate di *IoT Suite* sono tutte progettate in base agli stessi principi e obiettivi.

Il video seguente offre una panoramica della soluzione preconfigurata di monitoraggio remoto:

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Meet-the-new-Remote-Monitoring-accelerator-for-Azure-IoT/Player]

## <a name="preconfigured-solutions-overview"></a>Panoramica delle soluzioni preconfigurate

Una soluzione preconfigurata è l'implementazione open source di un modello di soluzione IoT comune che è possibile distribuire in Azure usando la propria sottoscrizione. Ogni soluzione preconfigurata combina codice personalizzato e servizi di Azure per implementare uno o più scenari IoT specifici. Qualsiasi scenario può essere personalizzato per soddisfare requisiti specifici. Tali scenari includono:

* Visualizzazione dei dati in un dashboard avanzato per ottenere informazioni approfondite e lo stato della soluzione.
* Configurazione di regole e avvisi per i dati di telemetria attivi dei dispositivi IoT.
* Pianificazione dei processi di gestione dei dispositivi, ad esempio di aggiornamenti del software e della configurazione.
* Provisioning di dispositivi fisici o simulati personalizzati.
* Risoluzione e correzione dei problemi nei gruppi di dispositivi IoT.

Ogni soluzione preconfigurata è un'implementazione end-to-end completa che può usare dispositivi simulati o fisici per generare dati di telemetria. È possibile usare le soluzioni preconfigurate come acceleratori di soluzioni per:

* Offrire un punto di partenza per le proprie soluzioni IoT.
* Apprendere modelli comuni nello sviluppo e nella progettazione di soluzioni IoT.

Sono attualmente disponibili tre soluzioni preconfigurate:

* [Monitoraggio remoto](iot-suite-remote-monitoring-explore.md)
* [Manutenzione predittiva](iot-suite-predictive-overview.md)
* [Connected factory](iot-suite-connected-factory-overview.md)

La tabella seguente mostra quali funzionalità IoT specifiche sono disponibili per le soluzioni:

| Soluzione | Inserimento di dati | Identità del dispositivo | Gestione dei dispositivi | Elaborazione perimetrale | Comando e controllo | Regole e azioni | Analisi predittiva |
| ------------------------------------------------------------ | -- | -- | -- | -- | -- | -- | -- |
| [Monitoraggio remoto](iot-suite-remote-monitoring-explore.md)  |Sì |Sì |Sì |-   |Sì |Sì |-   |
| [Manutenzione predittiva](iot-suite-predictive-overview.md)   |Sì |Sì |-   |-   |Sì |Sì |Sì |
| [Connected factory](iot-suite-connected-factory-overview.md) |Sì |- |- |Sì |Sì |Sì |-   |

* *Inserimento di dati*: inserimento di dati su vasta scala nel cloud.
* *Identità del dispositivo*: gestione delle identità univoche del dispositivo e controllo dell'accesso del dispositivo alla soluzione.
* *Gestione dei dispositivi*: gestione dei metadati del dispositivo ed esecuzione delle operazioni quali il riavvio del dispositivo e gli aggiornamenti del firmware.
* *Comando e controllo*: per far eseguire un'azione al dispositivo, inviare messaggi a un dispositivo dal cloud.
* *Regole e azioni*: per agire su specifici dati da dispositivo a cloud, il back-end della soluzione usa le regole.
* *Analisi predittiva*: il back-end della soluzione analizza i dati da dispositivo a cloud per determinare quando eseguire specifiche azioni. Ad esempio, usa l'analisi di dati di telemetria per il motore di un aereo per determinare eseguire la manutenzione.

> [!NOTE]
> Per distribuire una soluzione preconfigurata e saperne di più sulla relativa personalizzazione, visitare [Microsoft Azure IoT Suite](https://www.azureiotsuite.com/).

## <a name="azure-services"></a>Servizi di Azure

Quando si distribuisce una soluzione preconfigurata, il processo di provisioning configura diversi servizi di Azure. La tabella seguente illustra i servizi usati nelle soluzioni preconfigurate.

|                      | Monitoraggio remoto  | Manutenzione predittiva | Connected factory |
| -------------------- | ------------------ | ---------------------- | ----------------- |
| Hub IoT              | Sì                | Sì                    | Sì               |
| Hub eventi           |                    | Sì                    |                   |
| Time Series Insights |                    |                        | Sì               |
| Servizi contenitore   | Sì                |                        |                   |
| Analisi dei flussi     |                    | Sì                    |                   |
| App Web             | Sì                | Sì                    | Sì               |
| Cosmos DB            | Sì                | Sì                    |                    |
| Archiviazione di Azure         |                    | Sì                    | Sì               |

> [!NOTE]
> Per altre informazioni sulle risorse distribuite nella soluzione preconfigurata per il monitoraggio remoto, vedere questo [articolo](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/blob/master/README.md#basic-vs-standard-deployments) in GitHub.

* [Hub IoT di Azure](../iot-hub/index.md). Questo servizio fornisce funzionalità di messaggistica da dispositivo a cloud e da cloud a dispositivo e funge da gateway per il cloud e gli altri servizi chiave di IoT Suite. Il servizio consente di ricevere messaggi dai dispositivi su vasta scala e inviare i comandi ai dispositivi. Il servizio consente anche di [gestire i dispositivi](../iot-hub/iot-hub-device-management-overview.md). È possibile, ad esempio, configurare, riavviare o eseguire un ripristino delle impostazioni predefinite per uno o più dispositivi connessi all'hub.
* [Hub eventi di Azure](../event-hubs/index.md). Questo servizio supporta l'inserimento di volumi elevati di eventi nel cloud. Vedere [Confronto tra l'hub IoT e Hub eventi di Azure](../iot-hub/iot-hub-compare-event-hubs.md).
* [Azure Time Series Insights](../time-series-insights/index.yml). Le soluzioni preconfigurate usano questo servizio per analizzare e visualizzare i dati di telemetria provenienti dai dispositivi.
* [Servizio contenitore di Azure](../container-service/index.yml). Questo servizio ospita e gestisce i microservizi nelle soluzioni preconfigurate.
* [Azure Cosmos DB](../cosmos-db/index.yml) e [Archiviazione di Azure](../storage/index.yml), per l'archiviazione dei dati.
* [Analisi di flusso di Azure](../stream-analytics/index.md). La soluzione preconfigurata di manutenzione predittiva usa questo servizio per elaborare i dati di telemetria in ingresso, eseguire operazioni di aggregazione e rilevare gli eventi. Questa soluzione preconfigurata usa l'analisi di flusso anche per elaborare i messaggi informativi contenenti dati come i metadati o le risposte ai comandi dai dispositivi.
* [App Web di Azure](../app-service/index.yml), per ospitare il codice di applicazioni personalizzate nelle soluzioni preconfigurate.

Per una panoramica dell'architettura di una tipica soluzione IoT, vedere [Microsoft Azure e Internet delle cose (IoT)](iot-suite-what-is-azure-iot.md).

## <a name="whats-new-in-preconfigured-solutions"></a>Novità delle soluzioni preconfigurate

Microsoft sta aggiornando le soluzioni preconfigurate a una nuova architettura basata su microservizi. La tabella seguente illustra lo stato corrente delle soluzioni preconfigurate.

| Soluzione preconfigurata | Architecture  | Lingue     |
| ---------------------- | ------------- | ------------- |
| Monitoraggio remoto      | Microservizi | Java e .NET |
| Manutenzione predittiva | MVC           | .NET          |
| Connected factory      | MVC           | .NET          |

Le sezioni seguenti descrivono le novità delle soluzioni preconfigurate basate su microservizi.

### <a name="microservices"></a>Microservizi

La nuova versione della soluzione preconfigurata di monitoraggio remoto usa un'architettura di microservizi. Questa soluzione preconfigurata è costituita da più microservizi, ad esempio un *gestore di hub IoT* e un *gestore di archiviazione*. Sono disponibili per il download le versioni sia Java che .NET di ogni microservizio, insieme alla relativa documentazione per gli sviluppatori. Per altre informazioni sui microservizi, vedere l'articolo relativo all'[architettura del monitoraggio remoto](iot-suite-remote-monitoring-sample-walkthrough.md).

Questa architettura di microservizi è un modello collaudato per soluzioni cloud con le caratteristiche seguenti:

* Scalabilità
* Estendibilità
* Facilità di comprensione
* Possibilità di scambio di singoli servizi per diverse alternative

> [!TIP]
> Per altre informazioni sulle architetture di microservizi, vedere [.NET Application Architecture](https://www.microsoft.com/net/learn/architecture) (Architettura delle applicazioni .NET) e [Microservices: An application revolution powered by the cloud](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/) (Microservizi: una rivoluzione delle applicazioni basata sul cloud).

Quando si distribuisce la nuova versione del monitoraggio remoto, è necessario selezionare una delle opzioni di distribuzione seguenti.

* **Basic:** versione a costo ridotto a scopo di dimostrazione o per testare una distribuzione. Tutti i microservizi vengono distribuiti in un'unica macchina virtuale di Azure.
* **Standard:** distribuzione di infrastruttura estesa per lo sviluppo di una distribuzione di produzione. Il servizio contenitore di Azure distribuisce i microservizi in più macchine virtuali di Azure. Kubernetes orchestra i contenitori Docker che ospitano i singoli microservizi.

### <a name="language-choices-java-and-net"></a>Linguaggi disponibili: Java e .NET

Le implementazione di ogni microservizio sono disponibili sia in Java che in .NET. Così come il codice .NET, il codice sorgente Java è open source e personalizzabile in base a requisiti specifici:

* [Repository GitHub .NET per il monitoraggio remoto](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [Repository GitHub Java per il monitoraggio remoto](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)

Per ottenere implementazioni in altri linguaggi, aggiungere una richiesta nel forum di [suggerimenti degli utenti per Azure IoT](https://feedback.azure.com/forums/321918-azure-iot).

### <a name="react-user-interface-framework"></a>Framework per interfacce utente React

L'interfaccia utente è basata sulla libreria JavaScript [React](https://facebook.github.io/react/). Il codice sorgente è open source ed è scaricabile e personalizzabile.

## <a name="next-steps"></a>Passaggi successivi

Dopo questa panoramica delle soluzioni preconfigurate di IoT Suite, i passaggi successivi consigliati per ognuna delle soluzioni preconfigurate sono i seguenti:

* [Esplorare il modello di distribuzione Resource Manager per la soluzione di monitoraggio remoto di Azure IoT Suite](iot-suite-remote-monitoring-explore.md).
* [Panoramica della soluzione preconfigurata di manutenzione predittiva](iot-suite-predictive-overview.md).
* [Introduzione alla soluzione preconfigurata di connected factory](iot-suite-connected-factory-overview.md).

Per altre informazioni sulle architetture delle soluzioni IoT, vedere il documento relativo all'[architettura di riferimento dei servizi Microsoft Azure IoT](http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf).
