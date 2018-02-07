---
title: Personalizzare la soluzione di monitoraggio remoto - Azure | Microsoft Docs
description: Questo articolo offre informazioni su come accedere al codice sorgente per la soluzione preconfigurata di monitoraggio remoto.
services: 
suite: iot-suite
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-suite
ms.date: 01/17/2018
ms.topic: article
ms.devlang: NA
ms.tgt_pltfrm: NA
ms.workload: NA
ms.openlocfilehash: f5d38091b59110859d4376a5cd16a19f24dad65b
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/17/2018
---
# <a name="customize-the-remote-monitoring-preconfigured-solution"></a>Personalizzare la soluzione preconfigurata di monitoraggio remoto

Questo articolo offre informazioni su come accedere al codice sorgente e personalizzare la soluzione preconfigurata di monitoraggio remoto. L'articolo descrive:

* Repository GitHub che contengono il codice sorgente e le risorse per i microservizi che costituiscono la soluzione preconfigurata.
* Scenari di personalizzazione comuni, tra cui l'aggiunta di un nuovo tipo di dispositivo.

Il video seguente offre una panoramica delle opzioni di personalizzazione della soluzione preconfigurata di monitoraggio remoto:

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/How-to-customize-the-Remote-Monitoring-Preconfigured-Solution-for-Azure-IoT/Player]

## <a name="project-overview"></a>Panoramica del progetto

### <a name="implementations"></a>Implementazioni

La soluzione di monitoraggio remoto include implementazioni .NET e Java. Entrambe le implementazioni forniscono funzionalità simili e si basano sugli stessi servizi di Azure sottostanti. I repository GitHub principali sono disponibili qui:

* [Soluzione .NET](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [Soluzione Java](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)

### <a name="microservices"></a>Microservizi

Per informazioni su una funzionalità specifica della soluzione, è possibile accedere ai repository GitHub per ogni singolo microservizio. Ogni microservizio implementa una parte diversa della funzionalità della soluzione. Per altre informazioni sull'architettura complessiva, vedere [Architettura della soluzione preconfigurata di monitoraggio remoto](iot-suite-remote-monitoring-sample-walkthrough.md).

Questa tabella contiene il riepilogo delle informazioni sulla disponibilità corrente di ogni microservizio per ogni linguaggio:

<!-- please add links for each of the repos in the table, you can find them here https://github.com/Azure/azure-iot-pcs-team/wiki/Repositories-->

| Microservizio      | DESCRIZIONE | Java | .NET |
| ----------------- | ----------- | ---- | ---- |
| Interfaccia utente Web            | App Web per la soluzione di monitoraggio remoto. Implementa l'interfaccia utente usando il framework React.js. | [N/D (React.js)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) | [N/D (React.js)](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) |
| Gestione dell'hub IoT   | Gestisce la comunicazione con l'hub IoT.        | [Disponibile](https://github.com/Azure/iothub-manager-java) | [Disponibile](https://github.com/Azure/iothub-manager-dotnet)   |
| Authentication    |  Gestisce l'integrazione con Azure Active Directory.  | Non ancora disponibile | [Disponibile](https://github.com/Azure/pcs-auth-dotnet)   |
| Simulazione dei dispositivi | Gestisce un pool di dispositivi simulati. | Non ancora disponibile | [Disponibile](https://github.com/Azure/device-simulation-dotnet)   |
| Telemetria         | Rende i dati di telemetria dei dispositivi disponibili all'interfaccia utente. | [Disponibile](https://github.com/Azure/device-telemetry-java) | [Disponibile](https://github.com/Azure/device-telemetry-dotnet)   |
| Agente di telemetria   | Analizza il flusso di telemetria, archivia i messaggi dall'hub IoT di Azure e genera gli avvisi in base alle regole definite.  | [Disponibile](https://github.com/Azure/telemetry-agent-java) | [Disponibile](https://github.com/Azure/telemetry-agent-dotnet)   |
| Configurazione dell'interfaccia utente         | Gestisce i dati di configurazione dall'interfaccia utente. | [Disponibile](https://github.com/azure/pcs-ui-config-java) | [Disponibile](https://github.com/azure/pcs-ui-config-dotnet)   |
| Adattatore di archiviazione   |  Gestisce le interazioni con il servizio di archiviazione.   | [Disponibile](https://github.com/azure/pcs-storage-adapter-java) | [Disponibile](https://github.com/azure/pcs-storage-adapter-dotnet)   |
| Proxy inverso     | Espone risorse private in modo gestito tramite un endpoint univoco. | Non ancora disponibile | [Disponibile](https://github.com/Azure/reverse-proxy-dotnet)   |

La soluzione Java usa attualmente i microservizi di autenticazione, simulazione e proxy inverso .NET. Questi microservizi verranno sostituiti dalle versioni Java non appena si renderanno disponibili.

## <a name="presentation-and-visualization"></a>Presentazione e visualizzazione

Le sezioni seguenti descrivono le opzioni per personalizzare i livelli di presentazione e visualizzazione nella soluzione di monitoraggio remoto:

### <a name="change-the-logo-in-the-ui"></a>Modificare il logo nell'interfaccia utente

La distribuzione predefinita usa il nome e il logo della società Contoso nell'interfaccia utente. Per modificare questi elementi dell'interfaccia utente in modo da visualizzare il nome e il logo della società:

1. Usare il comando seguente per clonare il repository dell'interfaccia utente Web:

    ```cmd/sh
    git clone https://github.com/Azure/pcs-remote-monitoring-webui.git
    ```

1. Per modificare il nome della società, aprire il file `src/common/lang.js` in un editor di testo.

1. Individuare la riga seguente nel file:

    ```js
    CONTOSO: 'Contoso',
    ```

1. Sostituire `Contoso` con il nome della società. Ad esempio: 

    ```js
    CONTOSO: 'YourCo',
    ```

1. Salvare il file.

1. Per aggiornare il logo, aggiungere un nuovo file SVG alla cartella `assets/icons`. Il logo esistente si trova nel file `assets/icons/Contoso.svg`.

1. Aprire il file `src/components/layout/leftNav/leftNav.js` in un editor di testo.

1. Individuare la riga seguente nel file:

    ```js
    import ContosoIcon from '../../../assets/icons/Contoso.svg';
    ```

1. Sostituire `Contoso.svg` con il nome del file del logo. Ad esempio: 

    ```js
    import ContosoIcon from '../../../assets/icons/YourCo.svg';
    ```

1. Individuare la riga seguente nel file:

    ```js
    alt="ContosoIcon"
    ```

1. Sostituire `ContosoIcon` con il testo `alt` personalizzato. Ad esempio: 

    ```js
    alt="YourCoIcon"
    ```

1. Salvare il file.

1. Per testare le modifiche, è possibile eseguire la soluzione `webui` aggiornata nel computer locale. Per informazioni su come compilare ed eseguire la soluzione `webui` in locale, vedere [Build, run and test locally](https://github.com/Azure/pcs-remote-monitoring-webui/blob/master/README.md#build-run-and-test-locally) (Compilare, eseguire e testare la soluzione in locale) nel file leggimi del repository GitHub `webui`.

1. Per distribuire le modifiche, vedere [Developer Reference Guide](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide) (Guida di riferimento per sviluppatori).

<!--

### Add a new KPI to the Dashboard page

The following steps describe how to add a new KPI to display on the **Dashboard** page. The new KPI shows information about the number of alarms with specific status values as a pie chart:

1. Step 1

1. Step 2
-->

### <a name="customize-the-map"></a>Personalizzare la mappa

Per informazioni dettagliate sui componenti della mappa nella soluzione, vedere la pagina [Customize map](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map) (Personalizzare la mappa) in GitHub.

<!--
### Customize the telemetry chart

See the [Customize telemetry chart](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of the telemetry chart components in the solution.

### Connect an external visualization tool

See the [Connect an external visualization tool](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to connect an external visualization tool.

### Duplicate an existing control

To duplicate an existing UI element such as a chart or alert, see the [Duplicate a control](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub.

-->

### <a name="other-customization-options"></a>Altre opzioni di personalizzazione

Per modificare ulteriormente i livelli di presentazione e visualizzazione nella soluzione di monitoraggio remoto, è possibile modificare il codice. I repository GitHub rilevanti sono i seguenti:

* [UIConfig (.NET)](https://github.com/Azure/pcs-ui-config-dotnet/)
* [UIConfig (Java)](https://github.com/Azure/pcs-ui-config-java/)
* [Azure PCS Remote Monitoring WebUI](https://github.com/Azure/pcs-remote-monitoring-webui)

## <a name="device-connectivity-and-streaming"></a>Connettività e streaming dei dispositivi

Le sezioni seguenti descrivono le opzioni per personalizzare i livelli di connettività e streaming nella soluzione di monitoraggio remoto. La pagina [Device models](https://github.com/Azure/device-simulation-dotnet/wiki/Device-Models) (Modelli di dispositivo) descrive i tipi di dispositivo e i dati di telemetria nella soluzione. Usare i modelli di dispositivo per dispositivi sia simulati sia fisici.

Per un esempio di implementazione di dispositivi fisici, vedere [Connettere il dispositivo alla soluzione preconfigurata di monitoraggio remoto](iot-suite-connecting-devices-node.md).

Se si usa un _dispositivo fisico_, è necessario specificare l'applicazione client con un modello di dispositivo che contenga la specifica dei metadati e di telemetria del dispositivo.

Le sezioni seguenti descrivono l'uso di modelli di dispositivo con dispositivi simulati:

### <a name="add-a-telemetry-type"></a>Aggiungere un tipo di dati di telemetria

I tipi di dispositivo nella soluzione demo Contoso specificano i dati di telemetria inviati da ogni tipo di dispositivo. Per specificare i tipi di telemetria aggiuntivi, un dispositivo può inviare definizioni di telemetria come metadati alla soluzione. Se si usa questo formato, il dashboard utilizza in modo dinamico i dati di telemetria del dispositivo e i metodi disponibili e non è necessario modificare l'interfaccia utente. In alternativa, è possibile modificare la definizione del tipo di dispositivo nella soluzione.

Per informazioni su come aggiungere dati di telemetria personalizzati nel microservizio del _simulatore di dispositivi_, vedere [Testare la soluzione con dispositivi simulati](iot-suite-remote-monitoring-test.md).

### <a name="add-a-device-type"></a>Aggiungere un tipo di dispositivo

La soluzione demo Contoso definisce alcuni tipi di dispositivo di esempio. La soluzione consente di definire tipi di dispositivo personalizzati per soddisfare requisiti specifici dell'applicazione. Ad esempio, la società può usare un gateway industriale come dispositivo primario connesso alla soluzione.

Per creare una rappresentazione accurata del dispositivo, è necessario modificare l'applicazione in esecuzione nel dispositivo in modo che corrisponda ai requisiti del dispositivo.

Per informazioni su come aggiungere un nuovo tipo di dispositivo nel microservizio del _simulatore di dispositivi_, vedere [Testare la soluzione con dispositivi simulati](iot-suite-remote-monitoring-test.md).

### <a name="define-custom-methods-for-simulated-devices"></a>Definire metodi personalizzati per i dispositivi simulati

Per informazioni su come definire metodi personalizzati per i dispositivi simulati nella soluzione di monitoraggio remoto, vedere [Device Models](https://github.com/Azure/device-simulation-dotnet/wiki/%5BAPI-Specifications%5D-Device-Models) (Modelli di dispositivo) nel repository GitHub.

<!--
#### Using the simulator service

TODO: add steps for the simulator microservice here
-->

#### <a name="using-a-physical-device"></a>Uso di un dispositivo fisico

Per implementare metodi e processi nei dispositivi fisici, vedere gli articoli sull'hub IoT seguenti:

* [Comprendere e richiamare metodi diretti dall'hub IoT](../iot-hub/iot-hub-devguide-direct-methods.md).
* [Pianificare processi in più dispositivi](../iot-hub/iot-hub-devguide-jobs.md).

### <a name="other-customization-options"></a>Altre opzioni di personalizzazione

Per modificare ulteriormente i livelli di connettività e streaming dei dispositivi nella soluzione di monitoraggio remoto, è possibile modificare il codice. I repository GitHub rilevanti sono i seguenti:

* [Device Telemetry (.NET)](https://github.com/Azure/device-telemetry-dotnet) (Telemetria dei dispositivi - .NET)
* [Device Telemetry (Java)](https://github.com/Azure/device-telemetry-java) (Telemetria dei dispositivi - Java)
* [Telemetry Agent (.NET)](https://github.com/Azure/telemetry-agent-dotnet) (Agente di telemetria - .NET)
* [Telemetry Agent (Java)](https://github.com/Azure/telemetry-agent-java) (Agente di telemetria - Java)

## <a name="data-processing-and-analytics"></a>e analisi dei dati

<!--
The following sections describe options to customize the data processing and analytics layer in the remote monitoring solution:

### Rules and actions

See the [Customize rules and actions](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to customize the rules and actions in solution.


### Other customization options
-->

Per modificare ulteriormente i livelli di elaborazione e analisi dei dati nella soluzione di monitoraggio remoto, è possibile modificare il codice. I repository GitHub rilevanti sono i seguenti:

* [Telemetry Agent (.NET)](https://github.com/Azure/telemetry-agent-dotnet) (Agente di telemetria - .NET)
* [Telemetry Agent (Java)](https://github.com/Azure/telemetry-agent-java) (Agente di telemetria - Java)

## <a name="infrastructure"></a>Infrastruttura

<!--
The following sections describe options for customizing the infrastructure services in the remote monitoring solution:

### Change storage

The default storage service for the remote monitoring solution is Cosmos DB. See the [Customize storage service](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to change the storage service the solution uses.

### Change log storage

The default storage service for logs is Cosmos DB. See the [Customize log storage service](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to change the storage service the solution uses for logging.

### Other customization options
-->

Per modificare l'infrastruttura nella soluzione di monitoraggio remoto, è possibile modificare il codice. I repository GitHub rilevanti sono i seguenti:

* [IoTHub Manager (.NET)](https://github.com/Azure/iothub-manager-dotnet) (Gestione dell'hub IoT - .NET)
* [IoTHub Manager (Java)](https://github.com/Azure/iothub-manager-java) (Gestione dell'hub IoT - Java)
* [Storage Adapter (.NET)](https://github.com/Azure/pcs-storage-adapter-dotnet) (Adattatore di archiviazione - .NET)
* [Storage Adapter (Java)](https://github.com/Azure/pcs-storage-adapter-java) (Adattatore di archiviazione - Java)

## <a name="next-steps"></a>Passaggi successivi

Questo articolo ha fornito informazioni sulle risorse disponibili per personalizzare la soluzione preconfigurata.

Per altre informazioni concettuali sulla soluzione preconfigurata di monitoraggio remoto, vedere [Architettura della soluzione di monitoraggio remoto](iot-suite-remote-monitoring-sample-walkthrough.md)

Per altre informazioni sulla personalizzazione della soluzione di monitoraggio remoto, vedere:

* [Guida di riferimento per gli sviluppatori](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [Guida per la risoluzione dei problemi per gli sviluppatori](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->