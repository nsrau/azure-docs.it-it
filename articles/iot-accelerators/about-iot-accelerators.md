---
title: Introduzione agli acceleratori di soluzioni Azure IoT | Microsoft Docs
description: Informazioni sugli acceleratori di soluzioni di Azure IoT. Gli acceleratori di soluzioni IoT sono soluzioni complete, end-to-end, pronte per la distribuzione.
author: dominicbetts
ms.author: dobett
ms.date: 07/24/2018
ms.topic: overview
ms.custom: mvc
ms.service: iot-accelerators
services: iot-accelerators
manager: timlt
ms.openlocfilehash: 7020d8a1756702d8c2b1998eef5a3fc64809ca5e
ms.sourcegitcommit: cfff72e240193b5a802532de12651162c31778b6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/27/2018
ms.locfileid: "39308113"
---
# <a name="what-are-azure-iot-solution-accelerators"></a>Informazioni sugli acceleratori di soluzioni Azure IoT

Una soluzione IoT basata su cloud usa in genere codice personalizzato e più servizi cloud per gestire la connettività dei dispositivi, l'elaborazione e l'analisi dei dati e la presentazione.

Gli acceleratori di soluzioni IoT sono un insieme di soluzioni IoT complete e pronte per la distribuzione che implementano scenari comuni di IoT come il monitoraggio remoto, la fabbrica connessa, la manutenzione predittiva e la simulazione di dispositivi. Quando si implementa un acceleratore di soluzioni, la distribuzione include tutti i servizi basati su cloud necessari insieme a qualsiasi codice applicativo richiesto.

Gli acceleratori di soluzioni sono punti di partenza per le soluzioni IoT. Il codice sorgente per tutti gli acceleratori di soluzioni è open source ed è disponibile in GitHub. Si consiglia di scaricare e personalizzare acceleratori di soluzioni adatti alle proprie esigenze.

Gli acceleratori di soluzioni possono essere usati anche come strumenti di apprendimento prima di compilare una soluzione IoT personalizzata ex novo. Gli acceleratori di soluzioni implementano pratiche collaudate per le soluzioni IoT basate su cloud da seguire.

Il codice dell'applicazione in ogni acceleratore di soluzioni include un'app Web che consente di gestire l'acceleratore di soluzioni.

## <a name="supported-iot-scenarios"></a>Scenari IoT supportati

Attualmente, sono disponibili per la distribuzione quattro acceleratori di soluzioni:

### <a name="remote-monitoring"></a>Monitoraggio remoto

Usare questo acceleratore di soluzioni per raccogliere la telemetria da più dispositivi remoti e per controllarli. I dispositivi di esempio includono sistemi di raffreddamento installati presso i clienti o valvole installate in stazioni di pompaggio remote.

È possibile usare un dashboard di monitoraggio remoto per visualizzare la telemetria dai dispositivi connessi, effettuare il provisioning di nuovi dispositivi o aggiornare il firmware sui dispositivi connessi:

[![Dashboard della soluzione di monitoraggio remoto](./media/about-iot-accelerators/rm-dashboard-inline.png)](./media/about-iot-accelerators/rm-dashboard-expanded.png#lightbox)

### <a name="connected-factory"></a>Connected Factory

Usare questo acceleratore di soluzioni per raccogliere la telemetria da asset industriali con un'interfaccia [OPC Unified Architecture](https://opcfoundation.org/about/opc-technologies/opc-ua/) e per controllarli. Gli asset industriali possono includere stazioni di assemblaggio e prova su una linea di produzione factory.

È possibile usare il dashboard di fabbrica connessa per monitorare e gestire i dispositivi industriali:

[![Dashboard della soluzione di fabbrica connessa](./media/about-iot-accelerators/cf-dashboard-inline.png)](./media/about-iot-accelerators/cf-dashboard-expanded.png#lightbox)

### <a name="predictive-maintenance"></a>Manutenzione predittiva

Usare questo acceleratore di soluzioni per presumere quando si prevede il guasto di un dispositivo remoto, in modo da poter eseguire la manutenzione prima che si verifichi il guasto previsto. Questo acceleratore di soluzioni usa algoritmi di apprendimento macchina per prevedere i guasti a partire dalla telemetria del dispositivo. I dispositivi di esempio possono essere motori di aereo o ascensori.

È possibile usare il dashboard di manutenzione predittiva per visualizzare le analisi di manutenzione predittiva:

[![Dashboard della soluzione di fabbrica connessa](./media/about-iot-accelerators/pm-dashboard-inline.png)](./media/about-iot-accelerators/pm-dashboard-expanded.png#lightbox)

### <a name="device-simulation"></a>Simulazione dei dispositivi

Usare questo acceleratore di soluzioni per eseguire più dispositivi simulati che generano una telemetria realistica. È possibile usare questo acceleratore di soluzioni per testare il comportamento degli altri acceleratori di soluzioni o per testare le proprie soluzioni IoT personalizzate.

È possibile usare l'app Web di simulazione dei dispositivi per configurare ed eseguire simulazioni:

[![Dashboard della soluzione di fabbrica connessa](./media/about-iot-accelerators/ds-dashboard-inline.png)](./media/about-iot-accelerators/ds-dashboard-expanded.png#lightbox)

## <a name="design-principles"></a>Principi di progettazione

Tutti gli acceleratori di soluzioni seguono gli stessi principi e obiettivi di progettazione. Sono progettati per essere:

* **Scalabili**, ovvero consentono di connettere e gestire milioni di dispositivi connessi.
* **Estendibili**, in quanto consentono di personalizzarli in modo da adattarli alle proprie esigenze.
* **Comprensibili**, in quanto consentono di capire come funzionano e come vengono implementati.
* **Modulari**, perché consentono di scambiare servizi con alternative.
* **Sicuri**, in quanto combinano la sicurezza di Azure con le funzioni di connettività integrata e di sicurezza del dispositivo.

## <a name="architectures-and-languages"></a>Architetture e lingue

Gli acceleratori di soluzioni originali sono stati scritti tramite .NET e un'architettura MVC (model-view-controller). Microsoft sta aggiornando gli acceleratori di soluzioni preconfigurati a una nuova architettura di microservizi. La tabella seguente illustra lo stato corrente degli acceleratori di soluzioni con collegamenti ai repository di GitHub:

| Acceleratore di soluzioni   | Architettura  | Lingue     |
| ---------------------- | ------------- | ------------- |
| Monitoraggio remoto      | Microservizi | [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java) e [.NET](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet) |
| Manutenzione predittiva | MVC           | [.NET](https://github.com/Azure/azure-iot-predictive-maintenance)          |
| Connected Factory      | MVC           | [.NET](https://github.com/Azure/azure-iot-connected-factory)          |
| Simulazione dei dispositivi      | Microservizi | [.NET](https://github.com/Azure/device-simulation-dotnet)          |

Per altre informazioni sulle architetture dei microservizi, vedere [.NET Application Architecture](https://www.microsoft.com/net/learn/architecture) (Architettura delle applicazioni .NET) e [Microservices: An application revolution powered by the cloud](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/) (Microservizi: una rivoluzione delle applicazioni basata sul cloud).

## <a name="deployment-options"></a>Opzioni di distribuzione

È possibile distribuire gli acceleratori di soluzioni dal sito [Acceleratori di soluzioni Microsoft Azure IoT](https://www.azureiotsolutions.com/Accelerators#) o tramite la riga di comando.

È possibile distribuire l'acceleratore di soluzioni Monitoraggio remoto nelle configurazioni seguenti:

* **Standard:** distribuzione di infrastruttura estesa per lo sviluppo di una distribuzione di produzione. Il servizio contenitore di Azure distribuisce i microservizi in più macchine virtuali di Azure. Kubernetes orchestra i contenitori Docker che ospitano i singoli microservizi.
* **Basic:** versione a costo ridotto a scopo di dimostrazione o per testare una distribuzione. Tutti i microservizi vengono distribuiti in un'unica macchina virtuale di Azure.
* **Locale:** distribuzione della macchina locale a scopo di test e sviluppo. Questo approccio prevede la distribuzione dei microservizi in un contenitore Docker locale e la connessione a Hub IoT, Cosmos DB e ai servizi di archiviazione di Azure nel cloud.

Il costo di esecuzione di un acceleratore di soluzione è un'aggregazione del [costo dei servizi Azure sottostanti](https://azure.microsoft.com/pricing). Vengono visualizzati i dettagli dei servizi Azure usati quando si scelgono le opzioni di distribuzione.

## <a name="next-steps"></a>Passaggi successivi

Per provare uno degli acceleratori di soluzioni IoT, vedere le guide introduttive:

* [Provare una soluzione di monitoraggio remoto](quickstart-remote-monitoring-deploy.md)
* [Provare una soluzione di fabbrica connessa](quickstart-connected-factory-deploy.md)
* [Provare una soluzione di manutenzione predittiva](quickstart-predictive-maintenance-deploy.md)
* [Provare una soluzione di simulazione dei dispositivi](quickstart-device-simulation-deploy.md)
