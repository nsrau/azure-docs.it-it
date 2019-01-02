---
title: Opzioni di architettura nella soluzione di monitoraggio remoto - Azure | Microsoft Docs
description: Questo articolo descrive le opzioni a livello tecnico e di architettura scelte nella soluzione di monitoraggio remoto
author: timlaverty
manager: camerons
ms.author: timlav
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/20/2018
ms.topic: conceptual
ms.openlocfilehash: 20af014e5a59cb526d5b96e543b10d5b2b6d6937
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2018
ms.locfileid: "52679597"
---
# <a name="remote-monitoring-architectural-choices"></a>Opzioni di architettura nella soluzione di monitoraggio remoto

L'acceleratore della soluzione di monitoraggio remoto di Azure IoT è un acceleratore di soluzioni open source con licenza MIT. Per velocizzare il processo di sviluppo di soluzioni IoT, presenta alcuni scenari IoT comuni, ad esempio:

- Connettività dei dispositivi
- Gestione dei dispositivi
- Elaborazione del flusso

La soluzione di monitoraggio remoto segue l'[architettura di riferimento di Azure IoT](https://aka.ms/iotrefarchitecture) consigliata.

Questo articolo descrive le principali opzioni a livello tecnico e di architettura scelte in ogni sottosistema per la soluzione di monitoraggio remoto. Le opzioni in ambito tecnico scelte nella soluzione di monitoraggio remoto, tuttavia, non sono l'unico modo per implementare una soluzione IoT di monitoraggio remoto. L'implementazione tecnica deve essere considerata come la baseline per la compilazione di applicazioni efficaci ed è opportuno modificarla per:

- Adattare le competenze e le esperienze disponibili all'interno dell'organizzazione.
- Soddisfare esigenze di applicazioni verticali.

## <a name="architectural-choices"></a>Scelte a livello di architettura

Per un'applicazione IoT, Microsoft consiglia un'architettura cloud nativa, basata su microservizi e senza server. I diversi sottosistemi di un'applicazione IoT devono essere compilati come servizi discreti distribuibili e scalabili in modo indipendente. Questi attributi assicurano maggiore scalabilità e flessibilità di aggiornamento dei singoli sottosistemi e la flessibilità di scegliere la tecnologia più appropriata per ogni sottosistema.

È possibile implementare i microservizi usando più di una tecnologia. È possibile scegliere, ad esempio, una delle opzioni seguenti:

- Usare una tecnologia basata su contenitori come Docker con una tecnologia senza server come Funzioni di Azure.
- Eseguire l'hosting dei microservizi nei servizi PaaS, ad esempio Servizi app di Azure.

## <a name="technology-choices"></a>Scelte di tecnologia

Questa sezione illustra in dettaglio le opzioni a livello di tecnologia scelte nella soluzione di monitoraggio remoto per ogni sottosistema principale.

![Diagramma dei componenti principali](./media/iot-accelerators-remote-monitoring-architectural-choices/subsystem.png)

### <a name="cloud-gateway"></a>Gateway cloud

L'hub IoT di Azure viene usato come gateway cloud della soluzione di monitoraggio remoto. L'[hub IoT](https://azure.microsoft.com/services/iot-hub/) offre comunicazioni bidirezionali sicure con i dispositivi.

Per la connettività dei dispositivi IoT, è possibile usare:

- Gli [Azure IoT Hub SDK per dispositivi](../iot-hub/iot-hub-devguide-sdks.md#azure-iot-device-sdks) per implementare un'applicazione client nativa per il dispositivo. Gli SDK offrono i wrapper per l'API REST dell'hub IoT e gestiscono scenari come le ripetizioni.
- L'integrazione con Azure IoT Edge per distribuire e gestire moduli personalizzati in esecuzione nei contenitori dei dispositivi.
- L'integrazione con la gestione automatica dei dispositivi in hub IoT per gestire i dispositivi connessi in blocco.

### <a name="stream-processing"></a>Elaborazione del flusso

Per l'elaborazione del flusso la soluzione di monitoraggio remoto usa Analisi di flusso di Azure per l'elaborazione di regole complesse. Se si vuole usare regole più semplici, è disponibile un microservizio personalizzato con il supporto per l'elaborazione di regole semplici, sebbene questa configurazione non sia inclusa nella distribuzione predefinita. L'architettura di riferimento consiglia l'utilizzo di Funzioni di Azure per l'elaborazione di regole semplici e Analisi di flusso di Azure per le regole complesse.

### <a name="storage"></a>Archiviazione

Per l'archiviazione, l'acceleratore di soluzioni per il monitoraggio remoto usa sia Azure Time Series Insights sia Azure Cosmos DB. Azure Time Series Insights archivia i messaggi in arrivo dai dispositivi connessi tramite l'Hub IoT. L'acceleratore di soluzioni usa Azure Cosmos DB per tutte le altre attività connesse all'archiviazione, ad esempio l'archiviazione offline sicura, la definizione delle regole, gli avvisi e le impostazioni di configurazione.

Azure Cosmos DB è la soluzione consigliata per l'archiviazione ad accesso frequente per utilizzo generico per le applicazioni IoT. Tuttavia, per molti casi d'uso possono essere appropriate soluzioni come Azure Time Series Insights e Azure Data Lake. Con Azure Time Series Insights è possibile ottenere informazioni più dettagliate sui dati dei sensori delle serie temporali individuando rapidamente tendenze e anomalie, Questa funzionalità consente di eseguire analisi delle cause radice ed evitare costosi tempi di inattività.

> [!NOTE]
> Azure Time Series Insights non è attualmente disponibile nel cloud Azure Cina. Le nuove distribuzioni dell'acceleratore di soluzioni per il monitoraggio remoto nel cloud Azure Cina usano Cosmos DB per tutte le operazioni di archiviazione.

### <a name="business-integration"></a>Integrazione aziendale

L'integrazione aziendale nella soluzione di monitoraggio remoto si limita alla generazione di avvisi che vengono inseriti nell'archiviazione ad accesso frequente. Connettere la soluzione con App per la logica di Azure per implementare scenari di integrazione aziendali più approfonditi.

### <a name="user-interface"></a>Interfaccia utente

L'interfaccia utente Web è stata compilata con JavaScript React. React offre un framework di interfaccia utente Web di uso comune nel settore ed è simile ad altri framework noti come Angular.

### <a name="runtime-and-orchestration"></a>Runtime e orchestrazione

La soluzione di monitoraggio remoto usa contenitori Docker per eseguire i sottosistemi con Kubernetes come agente di orchestrazione per la scalabilità orizzontale. Questa architettura consente una definizione di scala individuale per ogni sottosistema, ma implica costi DevOps per mantenere aggiornati e sicuri i contenitori e le macchine virtuali.

Le alternative a Docker includono l'hosting di microservizi nei servizi PaaS, come Servizio app di Azure, mentre le alternative a Kubernetes includono agenti di orchestrazione, come Service Fabric, DC/OS o Swarm.

## <a name="next-steps"></a>Passaggi successivi

* Distribuire la soluzione di monitoraggio remoto [qui](https://www.azureiotsolutions.com/).
* Esplorare il codice GitHub in [C#](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) e [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java/).  
* Altre informazioni sull'architettura di riferimento di IoT sono disponibili [qui](https://aka.ms/iotrefarchitecture).
