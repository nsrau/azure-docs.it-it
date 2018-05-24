---
title: Opzioni di architettura nella soluzione di monitoraggio remoto - Azure | Microsoft Docs
description: Questo articolo descrive le opzioni a livello tecnico e di architettura scelte nella soluzione di monitoraggio remoto
services: iot-suite
suite: iot-suite
author: timlaverty
manager: camerons
ms.author: timlav
ms.service: iot-suite
ms.date: 04/30/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: 607b8aeb2f986eebddf8fe13b88e7f3bc7b4494b
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/18/2018
---
# <a name="remote-monitoring-architectural-choices"></a>Opzioni di architettura nella soluzione di monitoraggio remoto

La soluzione di monitoraggio remoto di Azure IoT è un acceleratore di soluzioni open source con licenza MIT che introduce scenari IoT comuni, come la connettività e la gestione dei dispositivi e l'elaborazione del flusso, per consentire ai clienti un processo di sviluppo più rapido.  La soluzione segue l'architettura di riferimento di Azure IoT consigliata pubblicata [qui](https://azure.microsoft.com/updates/microsoft-azure-iot-reference-architecture-available/).  

Questo articolo descrive le opzioni a livello tecnico e di architettura scelte in ogni sottosistema per la soluzione di monitoraggio remoto e illustra le alternative prese in considerazione.  È importante notare che le opzioni in ambito tecnico scelte nella soluzione di monitoraggio remoto non sono l'unico modo per implementare una soluzione IoT di monitoraggio remoto.  L'implementazione tecnica è la baseline per la compilazione di applicazioni efficaci e deve essere modificata in base alle competenze, all'esperienza e alle esigenze di applicazioni verticali per l'implementazione della soluzione del cliente.

## <a name="architectural-choices"></a>Scelte a livello di architettura

### <a name="microservices-serverless-and-cloud-native"></a>Approccio basato su microservizi, senza server e cloud native

L'architettura consigliata per le applicazioni IoT si basa su un approccio basato su microservizi, senza server e cloud native.  I diversi sottosistemi di un'applicazione IoT devono essere compilati come servizi discreti distribuibili e scalabili in modo indipendente.  Questi attributi assicurano maggiore scalabilità e flessibilità di aggiornamento dei singoli sottosistemi e la possibilità di scegliere la tecnologia più appropriata per ogni sottosistema in modo flessibile.  I microservizi possono essere implementati con più tecnologie. È possibile ad esempio usare la tecnologia basata su contenitori come Docker con la tecnologia senza server come Funzioni di Azure o eseguire l'hosting dei microservizi nei servizi PaaS, ad esempio Servizi app di Azure.

## <a name="core-subsystem-technology-choices"></a>Opzioni tecnologiche per i sottosistemi principali

Questa sezione illustra in dettaglio le opzioni a livello di tecnologia scelte nella soluzione di monitoraggio remoto per ogni sottosistema principale.

![Diagramma dei componenti principali](./media/iot-accelerators-remote-monitoring-architectural-choices/subsystem.png) 

### <a name="cloud-gateway"></a>Gateway cloud
L'hub IoT di Azure viene usato come gateway cloud per la soluzione di monitoraggio remoto.  L'hub IoT offre comunicazioni bidirezionali sicure con i dispositivi. Per altre informazioni sull'hub IoT fare clic [qui](https://azure.microsoft.com/services/iot-hub/). Per la connettività dei dispositivi IoT vengono usati .NET Core SDK e Java IoT Hub SDK.  Gli SDK offrono i wrapper per l'API REST dell'hub IoT e gestiscono scenari come le ripetizioni. 

### <a name="stream-processing"></a>Elaborazione del flusso
Per l'elaborazione del flusso la soluzione di monitoraggio remoto usa Analisi di flusso di Azure per le regole complesse.  Per i clienti che vogliono regole più semplici è disponibile anche un microservizio personalizzato con il supporto per l'elaborazione di regole semplici, sebbene questa configurazione non sia inclusa nella distribuzione predefinita. L'architettura di riferimento consiglia l'utilizzo di Funzioni di Azure per l'elaborazione di regole semplici e Analisi di flusso di Azure (ASA) per le regole complesse.  

### <a name="storage"></a>Archiviazione
Per tutte le esigenze di archiviazione: offline sicura, di dati warm, d regole e avvisi si usa Cosmos DB. È attualmente in corso il passaggio ad Archiviazione BLOB di Azure, come consigliato dall'architettura di riferimento.  Cosmos DB è la soluzione di archiviazione di dati warm generica consigliata per le applicazioni IoT, anche se in molti casi d'uso sono appropriate soluzioni come Azure Time Series Insights e Azure Data Lake.

### <a name="business-integration"></a>Integrazione aziendale
L'integrazione aziendale nella soluzione di monitoraggio remoto si limita alla generazione di avvisi che vengono inseriti nell'archiviazione di dati warm. Altre integrazioni di questo tipo possono essere eseguite mediante l'integrazione della soluzione con App per la logica di Azure.

### <a name="user-interface"></a>Interfaccia utente
L'interfaccia utente Web è stata compilata con JavaScript React.  React offre un framework di interfaccia utente Web di uso comune nel settore ed è simile ad altri framework noti come Angular.  

### <a name="runtime-and-orchestration"></a>Runtime e orchestrazione
Il runtime di applicazione scelto per l'implementazione dei sottosistemi nella soluzione di monitoraggio remoto è rappresentato dai contenitori Docker con Kubernetes (K8s) come agente di orchestrazione per la scalabilità orizzontale.  Questa architettura consente una definizione di scala individuale per ogni sottosistema, ma implica costi DevOps per mantenere aggiornati i contenitori e le macchine virtuali dal punto di vista della sicurezza.  Le alternative a Docker e K8s includono l'hosting di microservizi nei servizi PaaS, come Servizio app di Azure, o l'utilizzo di Service Fabric, DCOS, Swarm e così via come agente di orchestrazione.

## <a name="next-steps"></a>Passaggi successivi
* Distribuire la soluzione di monitoraggio remoto [qui](https://www.azureiotsuite.com/).
* Esplorare il codice GitHub in [C#](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) e [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java/).  
* Altre informazioni sull'architettura di riferimento di IoT sono disponibili [qui](https://azure.microsoft.com/updates/microsoft-azure-iot-reference-architecture-available/).
