---
title: Panoramica sull'acceleratore di soluzioni di monitoraggio remoto - Azure | Microsoft Docs
description: Una panoramica dell'acceleratore di soluzioni di monitoraggio remoto.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: af09ea39f373d518d5600e3fa46adc378fd9236d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61442538"
---
# <a name="remote-monitoring-solution-accelerator-overview"></a>Panoramica dell'acceleratore di soluzioni di monitoraggio remoto

L'[acceleratore di soluzioni](../iot-accelerators/about-iot-accelerators.md) di monitoraggio remoto implementa una soluzione di monitoraggio end-to-end per più computer in località remote. La soluzione combina i servizi chiave di Azure per offrire un'implementazione generica dello scenario aziendale. È possibile usare la soluzione come punto di partenza per l'implementazione e [personalizzarla](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md) in base ai requisiti aziendali specifici.

Questo articolo illustra alcuni degli elementi principali della soluzione di monitoraggio remoto per comprenderne il funzionamento. Queste informazioni consentono di:

* Risolvere i problemi nella soluzione.
* Pianificare come personalizzare la soluzione per soddisfare requisiti specifici.
* Progettare la propria soluzione IoT che usa i servizi di Azure.

Il codice dell'acceleratore di soluzione di monitoraggio remoto è disponibile su GitHub:

* [.NET](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)

## <a name="logical-architecture"></a>Architettura logica

Il diagramma seguente illustra i componenti logici dell'acceleratore di soluzioni di monitoraggio remoto sovrapposti all'[architettura IoT](../iot-fundamentals/iot-introduction.md):

![Architettura logica](./media/iot-accelerators-remote-monitoring-sample-walkthrough/remote-monitoring-architecture.png)

## <a name="why-microservices"></a>Perché usare i microservizi?

Da quando Microsoft ha rilasciato i primi acceleratori di soluzioni, l'architettura cloud si è evoluta. I [microservizi](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/) rappresentano un metodo collaudato per ottenere scalabilità e flessibilità senza compromettere la velocità di sviluppo. Diversi servizi Microsoft usano questo modello di architettura internamente con risultati straordinari in termini di scalabilità e affidabilità. Gli acceleratori di soluzioni aggiornati mettono in pratica quanto appreso con l'esperienza, in modo che tutti possano trarne vantaggio.

> [!TIP]
> Per altre informazioni sulle architetture dei microservizi, vedere [.NET Application Architecture](https://www.microsoft.com/net/learn/architecture) (Architettura delle applicazioni .NET) e [Microservices: An application revolution powered by the cloud](https://azure.microsoft.com/blog/microservices-an-application-revolution-powered-by-the-cloud/) (Microservizi: una rivoluzione delle applicazioni basata sul cloud).

## <a name="device-connectivity"></a>Connettività dei dispositivi

La soluzione include i componenti seguenti nella parte dell'architettura logica relativa alla connettività dei dispositivi:

### <a name="real-devices"></a>Dispositivi reali

È possibile connettere dispositivi reali alla soluzione. È possibile implementare il comportamento dei dispositivi simulati usando Azure IoT SDK per dispositivi.

È possibile effettuare il provisioning di dispositivi reali dal dashboard nel portale della soluzione.

### <a name="device-simulation-microservice"></a>Microservizio di simulazione dispositivi

La soluzione include il [microservizio di simulazione dispositivi](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/device-simulation) che consente di gestire un pool di dispositivi simulati dal portale della soluzione per testare il flusso end-to-end nella soluzione. I dispositivi simulati:

* Generano dati di telemetria da dispositivo a cloud.
* Rispondono alle chiamate ai metodi da cloud a dispositivo dall'hub IoT.

Il microservizio fornisce un endpoint RESTful per consentire di creare, avviare e arrestare le simulazioni. Ogni simulazione è costituita da un set di dispositivi virtuali di diversi tipi, che inviano dati di telemetria e rispondono alle chiamate ai metodi.

È possibile effettuare il provisioning di dispositivi simulati dal dashboard nel portale della soluzione.

### <a name="iot-hub"></a>Hub IoT

L'[hub IoT](../iot-hub/index.yml) inserisce i dati di telemetria inviati dai dispositivi reali e simulati nel cloud. L'hub IoT rende i dati di telemetria disponibili per l'elaborazione da parte dei servizi nel back-end della soluzione IoT.

L'hub IoT nella soluzione esegue anche le operazioni seguenti:

* Gestisce un registro delle identità in cui vengono archiviati gli ID e le chiavi di autenticazione di tutti i dispositivi autorizzati a connettersi al portale.
* Richiama i metodi nei dispositivi per conto dell'acceleratore di soluzioni.
* Gestisce i dispositivi gemelli per tutti i dispositivi registrati. Nel dispositivo gemello vengono archiviati i valori delle proprietà segnalati da un dispositivo. Qui vengono archiviate anche le proprietà desiderate, impostate nel portale della soluzione, in modo che il dispositivo possa recuperarle alla connessione successiva.
* Pianifica processi per impostare proprietà per più dispositivi o richiamare metodi in più dispositivi.

## <a name="data-processing-and-analytics"></a>e analisi dei dati

La soluzione include i componenti seguenti nella parte dell'architettura logica relativa all'analisi e all'elaborazione dei dati:

### <a name="iot-hub-manager-microservice"></a>Microservizio gestione di hub IoT

La soluzione include il [microservizio gestione di hub IoT](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/iothub-manager) per la gestione delle interazioni con l'hub IoT, ad esempio:

* Creazione e gestione di dispositivi IoT.
* Gestione di dispositivi gemelli.
* Chiamata di metodi nei dispositivi.
* Gestione di credenziali IoT.

Questo servizio esegue anche query sull'hub IoT per recuperare i dispositivi appartenenti a gruppi definiti dall'utente.

Il microservizio fornisce un endpoint RESTful per gestire i dispositivi e i dispositivi gemelli, richiamare metodi ed eseguire query sull'hub IoT.

### <a name="device-telemetry-microservice"></a>Microservizio telemetria

Il [microservizio telemetria del dispositivo](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/device-telemetry) fornisce un endpoint RESTful per l'accesso in lettura ai dati di telemetria del dispositivo archiviati in Time Series Insights. L'endpoint RESTful abilita inoltre operazioni CRUD sulle regole e l'accesso in lettura/scrittura alle definizioni di avviso dall'archiviazione.

### <a name="storage-adapter-microservice"></a>Microservizio adattatore di archiviazione

Il [microservizio adattatore di archiviazione](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/storage-adapter) gestisce coppie chiave-valore, astrae la semantica del servizio di archiviazione e presenta un'interfaccia semplice per archiviare i dati di qualsiasi formato usando Azure Cosmos DB.

I valori vengono organizzati in raccolte. È possibile operare su valori singoli o recuperare intere raccolte. Le strutture dati complesse vengono serializzate dai client e gestite come payload di testo semplice.

Il servizio fornisce un endpoint RESTful per le operazioni CRUD su coppie chiave-valore. valori

### <a name="azure-cosmos-db"></a>Azure Cosmos DB

Le distribuzioni dell'acceleratore di soluzioni usano [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/) per l'archiviazione di regole, avvisi e impostazioni di configurazione e per tutte le altre attività di archiviazione offline sicura.

### <a name="azure-stream-analytics-manager-microservice"></a>Microservizio gestione di Analisi di flusso di Azure

Il [microservizio gestione di Analisi di flusso di Azure](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/asa-manager) gestisce i processi di Analisi di flusso di Azure, inclusa l'impostazione della configurazione, il loro avvio o arresto e il monitoraggio dello stato.

Il processo Analisi di flusso di Azure è supportato da due set di dati di riferimento. Un set di dati definisce le regole e uno definisce i gruppi di dispositivi. I dati di riferimento delle regole vengono generati dalle informazioni gestite dal microservizio telemetria del dispositivo. Il microservizio gestione di Analisi di flusso di Azure trasforma le regole dei dati di telemetria in logica di elaborazione del flusso.

I dati di riferimento dei gruppi di dispositivi vengano usati per identificare il gruppo di regole da applicare a un messaggio di telemetria in ingresso. I gruppi di dispositivi sono gestiti dal microservizio configurazione e usano query gemelle del dispositivo hub IoT di Azure.

I processi Analisi di flusso di Azure recapitano i dati di telemetria provenienti dai dispositivi connessi a Time Series Insights per l'archiviazione e analisi.

### <a name="azure-stream-analytics"></a>Analisi di flusso di Azure

[Analisi di flusso di Azure](https://docs.microsoft.com/azure/stream-analytics/) è un motore di elaborazione di eventi che permette di esaminare volumi elevati di streaming di dati per i dispositivi.

### <a name="azure-time-series-insights"></a>Azure Time Series Insights

[Azure Time Series Insights](https://docs.microsoft.com/azure/time-series-insights/) archivia i dati di telemetria provenienti dai dispositivi connessi all'acceleratore di soluzioni. Consente inoltre di visualizzare e interrogare i dati di telemetria del dispositivo nell'interfaccia utente Web della soluzione.

> [!NOTE]
> Azure Time Series Insights non è attualmente disponibile nel cloud Azure Cina. Le nuove distribuzioni dell'acceleratore di soluzioni di monitoraggio remoto nel cloud Azure Cina usano Cosmos DB per tutte le operazioni di archiviazione.

### <a name="configuration-microservice"></a>Microservizio configurazione

Il [microservizio configurazione](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/config) fornisce un endpoint RESTful per svolgere operazioni CRUD su gruppi di dispositivi, impostazioni della soluzione e impostazioni utente nell'acceleratore di soluzioni. Funziona con il microservizio adattatore di archiviazione per rendere persistenti i dati di configurazione.

### <a name="authentication-and-authorization-microservice"></a>Microservizio autenticazione e autorizzazione

Il [microservizio autenticazione e autorizzazione](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/auth) gestisce gli utenti autorizzati ad accedere all'acceleratore di soluzioni. La gestione degli utenti può essere eseguita usando qualsiasi provider di servizi di identità che supporta [OpenId Connect](https://openid.net/connect/).

### <a name="azure-active-directory"></a>Azure Active Directory

Le distribuzioni dell'acceleratore di soluzioni usano [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/) come provider di OpenID Connect. Azure Active Directory archivia le informazioni sugli utenti e fornisce i certificati per convalidare la firma dei token JWT.

## <a name="presentation"></a>Presentazione

La soluzione include i componenti seguenti nella parte dell'architettura logica relativa alla presentazione:

L'[interfaccia utente Web è un'applicazione Javascript React](https://github.com/Azure/pcs-remote-monitoring-webui). L'applicazione:

* Usa solo Javascript React e viene eseguita interamente nel browser.
* Ha uno stile definito con CSS.
* Interagisce con i microservizi pubblici tramite chiamate AJAX.

L'interfaccia utente include tutte le funzionalità dell'acceleratore di soluzioni e interagisce con altri microservizi, ad esempio:

* Il microservizio autenticazione e autorizzazione per proteggere i dati degli utenti.
* Il microservizio gestione hub IoT per elencare e gestire i dispositivi IoT.

Azure Time Series Insights Explorer è integrato nell'interfaccia utente per consentire l'interrogazione e l'analisi dei dati di telemetria del dispositivo.

Il microservizio configurazione consente all'interfaccia utente di archiviare e recuperare le impostazioni di configurazione.

## <a name="next-steps"></a>Passaggi successivi

Se si vuole esplorare il codice sorgente e la documentazione per sviluppatori, iniziare con uno dei due repository GitHub:

* [Acceleratore di soluzioni di monitoraggio remoto con Azure IoT (.NET)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet).
* [Acceleratore di soluzioni di monitoraggio remoto con Azure IoT (Java)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java).

Diagrammi dettagliati delle architetture delle soluzioni:
* [Acceleratore di soluzioni per l'architettura di monitoraggio remoto](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Architecture).

Per altre informazioni concettuali sull'acceleratore di soluzioni di monitoraggio remoto, vedere [Customize the solution accelerator](../iot-accelerators/iot-accelerators-remote-monitoring-customize.md) (Personalizzare l'acceleratore di soluzioni).
