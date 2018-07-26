---
title: Panoramica sull'acceleratore di soluzioni di monitoraggio remoto - Azure | Microsoft Docs
description: Una panoramica dell'acceleratore di soluzioni di monitoraggio remoto.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 11/10/2017
ms.author: dobett
ms.openlocfilehash: dfe584532efeab1dbc0d2928b7afb0a6695a21ee
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2018
ms.locfileid: "39184946"
---
# <a name="remote-monitoring-solution-accelerator-overview"></a>Panoramica dell'acceleratore di soluzioni di monitoraggio remoto

L'[acceleratore di soluzioni](../iot-accelerators/about-iot-accelerators.md) di monitoraggio remoto implementa una soluzione di monitoraggio end-to-end per più computer in località remote. La soluzione combina i servizi chiave di Azure per offrire un'implementazione generica dello scenario aziendale. È possibile usare la soluzione come punto di partenza per l'implementazione e [personalizzarla](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md) in base ai requisiti aziendali specifici.

Questo articolo illustra alcuni degli elementi principali della soluzione di monitoraggio remoto per comprenderne il funzionamento. Queste informazioni consentono di:

* Risolvere i problemi nella soluzione.
* Pianificare come personalizzare la soluzione per soddisfare requisiti specifici.
* Progettare la propria soluzione IoT che usa i servizi di Azure.

## <a name="logical-architecture"></a>Architettura logica

Il diagramma seguente illustra i componenti logici dell'acceleratore di soluzioni di monitoraggio remoto sovrapposti all'[architettura IoT](../iot-fundamentals/iot-introduction.md):

![Architettura logica](./media/iot-accelerators-remote-monitoring-sample-walkthrough/remote-monitoring-architecture.png)

## <a name="why-microservices"></a>Perché usare i microservizi?

Da quando Microsoft ha rilasciato i primi acceleratori di soluzioni, l'architettura cloud si è evoluta. I [microservizi](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/) rappresentano un metodo collaudato per ottenere scalabilità e flessibilità senza compromettere la velocità di sviluppo. Diversi servizi Microsoft usano questo modello di architettura internamente con risultati straordinari in termini di scalabilità e affidabilità. Gli acceleratori di soluzioni aggiornati mettono in pratica quanto appreso con l'esperienza, in modo che tutti possano trarne vantaggio.

> [!TIP]
> Per altre informazioni sulle architetture dei microservizi, vedere [.NET Application Architecture](https://www.microsoft.com/net/learn/architecture) (Architettura delle applicazioni .NET) e [Microservices: An application revolution powered by the cloud](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/) (Microservizi: una rivoluzione delle applicazioni basata sul cloud).

## <a name="device-connectivity"></a>Connettività dei dispositivi

La soluzione include i componenti seguenti nella parte dell'architettura logica relativa alla connettività dei dispositivi:

### <a name="simulated-devices"></a>Dispositivi simulati

La soluzione include un microservizio che consente di gestire un pool di dispositivi simulati per testare il flusso end-to-end nella soluzione. I dispositivi simulati:

* Generano dati di telemetria da dispositivo a cloud.
* Rispondono alle chiamate ai metodi da cloud a dispositivo dall'hub IoT.

Il microservizio fornisce un endpoint RESTful per consentire di creare, avviare e arrestare le simulazioni. Ogni simulazione è costituita da un set di dispositivi virtuali di diversi tipi, che inviano dati di telemetria e rispondono alle chiamate ai metodi.

È possibile effettuare il provisioning di dispositivi simulati dal dashboard nel portale della soluzione.

### <a name="physical-devices"></a>Dispositivi fisici

È possibile connettere dispositivi fisici alla soluzione. È possibile implementare il comportamento dei dispositivi simulati usando Azure IoT SDK per dispositivi.

È possibile effettuare il provisioning di dispositivi fisici dal dashboard nel portale della soluzione.

### <a name="iot-hub-and-the-iot-manager-microservice"></a>Hub IoT e microservizio di gestione IoT

L'[hub IoT](../iot-hub/index.yml) inserisce i dati inviati dai dispositivi nel cloud e li rende disponibili per il microservizio `telemetry-agent`.

L'hub IoT nella soluzione esegue anche le operazioni seguenti:

* Gestisce un registro delle identità in cui vengono archiviati gli ID e le chiavi di autenticazione di tutti i dispositivi autorizzati a connettersi al portale. Il registro delle identità permette di abilitare e disabilitare i dispositivi.
* Richiama i metodi nei dispositivi per conto del portale della soluzione.
* Gestisce i dispositivi gemelli per tutti i dispositivi registrati. Nel dispositivo gemello vengono archiviati i valori delle proprietà segnalati da un dispositivo. Qui vengono archiviate anche le proprietà desiderate, impostate nel portale della soluzione, in modo che il dispositivo possa recuperarle alla connessione successiva.
* Pianifica processi per impostare proprietà per più dispositivi o richiamare metodi in più dispositivi.

La soluzione include il microservizio `iot-manager` per la gestione delle interazioni con l'hub IoT, ad esempio:

* Creazione e gestione di dispositivi IoT.
* Gestione di dispositivi gemelli.
* Chiamata di metodi nei dispositivi.
* Gestione di credenziali IoT.

Questo servizio esegue anche query sull'hub IoT per recuperare i dispositivi appartenenti a gruppi definiti dall'utente.

Il microservizio fornisce un endpoint RESTful per gestire i dispositivi e i dispositivi gemelli, richiamare metodi ed eseguire query sull'hub IoT.

## <a name="data-processing-and-analytics"></a>e analisi dei dati

La soluzione include i componenti seguenti nella parte dell'architettura logica relativa all'analisi e all'elaborazione dei dati:

### <a name="device-telemetry"></a>Telemetria dei dispositivi

La soluzione include due microservizi per gestire i dati di telemetria dei dispositivi.

Il microservizio [telemetry-agent](https://github.com/Azure/telemetry-agent-dotnet):

* Archivia i dati di telemetria in Azure Cosmos DB.
* Analizza il flusso di dati di telemetria dai dispositivi.
* Genera allarmi in base alle regole definite.

Gli allarmi vengono archiviati in Azure Cosmos DB.

Il microservizio [telemetry-agent](https://github.com/Azure/telemetry-agent-dotnet)a consente al portale della soluzione di leggere i dati di telemetria inviati dai dispositivi. Il portale della soluzione usa anche questo servizio per:

* Definire regole di monitoraggio, ad esempio le soglie che attivano gli allarmi.
* Recuperare l'elenco di allarmi passati.

Usare l'endpoint RESTful fornito da questo microservizio per gestire telemetria, regole e allarmi.

### <a name="storage"></a>Archiviazione

Il microservizio [storage-adapter](https://github.com/Azure/pcs-storage-adapter-dotnet) è un adapter posto davanti al servizio di archiviazione principale usato per l'acceleratore di soluzioni. Fornisce semplice archiviazione di raccolte e coppie chiave-valore.

La distribuzione standard dell'acceleratore di soluzioni usa Azure Cosmos DB come servizio di archiviazione principale.

Il database di Azure Cosmos DB archivia i dati nell'acceleratore di soluzioni. Il microservizio **storage-adapter** funge da adapter per consentire agli altri microservizi nella soluzione di accedere ai servizi di archiviazione.

## <a name="presentation"></a>Presentazione

La soluzione include i componenti seguenti nella parte dell'architettura logica relativa alla presentazione:

L'[interfaccia utente Web è un'applicazione Javascript React](https://github.com/Azure/pcs-remote-monitoring-webui). L'applicazione:

* Usa solo Javascript React e viene eseguita interamente nel browser.
* Ha uno stile definito con CSS.
* Interagisce con i microservizi pubblici tramite chiamate AJAX.

L'interfaccia utente presenta tutte le funzionalità dell'acceleratore di soluzioni e interagisce con altri servizi, ad esempio:

* Il microservizio [authentication](https://github.com/Azure/pcs-auth-dotnet) per proteggere i dati degli utenti.
* Il microservizio [iothub-manager](https://github.com/Azure/iothub-manager-dotnet) per elencare e gestire i dispositivi IoT.

Il microservizio [ui-config](https://github.com/Azure/pcs-config-dotnet) consente all'interfaccia utente di archiviare e recuperare le impostazioni di configurazione.

## <a name="next-steps"></a>Passaggi successivi

Se si vuole esplorare il codice sorgente e la documentazione per sviluppatori, iniziare con uno dei due repository GitHub principali:

* [Acceleratore di soluzioni di monitoraggio remoto con Azure IoT (.NET)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/).
* [Acceleratore di soluzioni di monitoraggio remoto con Azure IoT (Java)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java).

Diagrammi dettagliati delle architetture delle soluzioni:
* [Acceleratore di soluzioni per l'architettura di monitoraggio remoto](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Architecture).

Per altre informazioni concettuali sull'acceleratore di soluzioni di monitoraggio remoto, vedere [Customize the solution accelerator](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md) (Personalizzare l'acceleratore di soluzioni).
