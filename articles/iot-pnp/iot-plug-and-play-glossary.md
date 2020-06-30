---
title: 'Glossario terminologico: anteprima Plug and Play IoT | Microsoft Docs'
description: "Concetti: un glossario dei termini comuni relativi all'anteprima Plug and Play IoT."
author: Philmea
ms.author: philmea
ms.date: 12/23/2019
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: f0c21626c664f2d72b534ebae7f0a257620be07d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81767062"
---
# <a name="glossary-of-terms-for-iot-plug-and-play-preview"></a>Glossario terminologico per l'anteprima Plug and Play IoT

Definizioni dei termini comuni usati negli articoli Plug and Play IoT.

## <a name="azure-certified-for-iot-portal"></a>Portale Azure Certified per IoT

È possibile usare il sito Web del [portale Azure Certified per IoT](https://aka.ms/ACFI) per:

- Completare il [processo di certificazione](#device-certification) per il [dispositivo Plug and Play IoT](#iot-plug-and-play-device).
- Trovare [modelli di funzionalità di dispositivo](#device-capability-model).
- Pubblicare un modello di funzionalità di dispositivo nel [repository modelli pubblico ](#public-model-repository).

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

L'interfaccia della riga di comando di Azure è uno strumento da riga di comando multipiattaforma per la gestione delle risorse di Azure. L'estensione Azure IoT per l'interfaccia della riga di comando di Azure è uno strumento da riga di comando per interagire con e testare i [dispositivi Plug and Play IoT](#iot-plug-and-play-device). È possibile usare l'estensione per:

- Connettersi a un dispositivo Plug and Play IoT.
- Visualizzare i [dati di telemetria](#telemetry) inviati dal dispositivo.
- Lavorare con [proprietà](#properties) del dispositivo.
- Chiamare i [comandi](#commands) dispositivo.
- Gestire [repository modelli](#model-repository), [interfacce](#interface) e [modelli di funzionalità di dispositivo](#device-capability-model).

## <a name="azure-iot-central"></a>Azure IoT Central

Azure IoT Central è una soluzione Software as a Service completamente gestita che semplifica la connessione, il monitoraggio e la gestione dei [dispositivi Plug and Play IoT](#iot-plug-and-play-device). È possibile usare i [modelli di funzionalità di dispositivo](#device-capability-model) per configurare automaticamente un'applicazione IoT Central per monitorare e gestire i dispositivi.

## <a name="azure-iot-certification-service"></a>Servizio di certificazione Azure IoT

Il servizio di certificazione Azure IoT esegue un set di test di certificazione quando si invia un [dispositivo Plug and Play IoT](#iot-plug-and-play-device) per la certificazione tramite il [portale Azure Certified per IoT](#azure-certified-for-iot-portal). Prima di poter aggiungere un dispositivo al [catalogo dei dispositivi Certified per IoT](#certified-for-iot-device-catalog), il dispositivo deve essere certificato.

## <a name="azure-iot-tools-extension"></a>Estensione Azure IoT Tools

Azure IoT Tools è un insieme di estensioni in [codice Visual Studio](#visual-studio-code) che consente di interagire con l'hub IoT e sviluppare dispositivi IoT. Lo sviluppo di dispositivi Plug and Play IoT consente di:

- Autorizzare [modelli di funzionalità di dispositivo](#device-capability-model) e [interfacce](#interface).
- Pubblicare in [repository modelli](#model-repository).
- Generare lo scheletro di codice per implementare l'applicazione del dispositivo.

## <a name="azure-iot-explorer-tool"></a>Strumento Azure IoT Explorer

Azure IoT explorer è uno strumento grafico che è possibile usare per interagire con e testare i [dispositivi Plug and Play IoT](#iot-plug-and-play-device). Dopo aver installato lo strumento nel computer locale, è possibile usarlo per:

- Visualizzare i dispositivi connessi all'[hub IoT](#azure-iot-hub).
- Connettersi a un dispositivo Plug and Play IoT.
- Visualizzare i [dati di telemetria](#telemetry) inviati dal dispositivo.
- Lavorare con [proprietà](#properties) del dispositivo.
- Chiamare i [comandi](#commands) dispositivo.

## <a name="azure-iot-hub"></a>Hub IoT Azure

IoT Hub è un servizio gestito, ospitato nel cloud, che funge da hub del messaggio centrale per la comunicazione bidirezionale tra l'applicazione di IoT e i dispositivi gestiti. I [dispositivi Plug and Play IoT](#iot-plug-and-play-device) possono connettersi a un hub IoT. Una soluzione IoT usa un hub IoT per abilitare:

- Dispositivi per l'invio di dati di telemetria a una soluzione basata sul cloud.
- Una soluzione basata sul cloud per gestire i dispositivi connessi.

## <a name="azure-iot-device-sdk"></a>Azure IoT SDK per dispositivi

Sono disponibili SDK per dispositivi per più lingue che è possibile usare per compilare applicazioni client per dispositivi Plug and Play IoT. Uno dei requisiti per la [certificazione del dispositivo](#device-certification) è che il codice client del dispositivo usi uno degli Azure IoT SDK per dispositivi.

## <a name="certified-for-iot-device-catalog"></a>Catalogo dei dispositivi Certified per IoT

Il [catalogo dei dispositivi Certified per IoT](https://catalog.azureiotsolutions.com/) elenca i [dispositivi Plug and Play IoT](#iot-plug-and-play-device) che hanno superato i test di [certificazione del dispositivo](#device-certification). I [modelli di funzionalità di dispositivo](#device-capability-model) per i dispositivi Plug and Play IoT nel catalogo e pubblicati nel repository modelli pubblico.

## <a name="commands"></a>Comandi

I comandi definiti in un'[interfaccia](#interface) rappresentano i metodi che possono essere eseguiti nel [gemello digitale](#digital-twin). Ad esempio, un comando per riavviare un dispositivo.

## <a name="common-interface"></a>Interfaccia comune

Si prevede che tutti i [dispositivi Plug and Play IoT](#iot-plug-and-play-device) implementino alcune [interfacce](#interface) comuni. L'interfaccia informazioni sul dispositivo, ad esempio, definisce le informazioni relative all'hardware e al sistema operativo del dispositivo. La [certificazione del dispositivo](#device-certification) richiede che il dispositivo implementi diverse interfacce comuni. È possibile recuperare le definizioni di interfaccia comune dal repository modelli pubblico.

## <a name="company-model-repository"></a>Repository modelli aziendale

Un'organizzazione può usare un [repository modelli](#model-repository) aziendale come archivio privato per [modelli di funzionalità di dispositivo](#device-capability-model) e [interfacce ](#interface).

## <a name="connection-string"></a>Stringa di connessione

Una stringa di connessione incapsula le informazioni necessarie per connettersi a un endpoint. Una stringa di connessione contiene in genere l'indirizzo dell'endpoint e informazioni di sicurezza, ma il formato della stringa può variare nei diversi servizi. Esistono due tipi di stringa di connessione associato al servizio hub IoT:

- Stringhe di connessione dispositivo che consentono ai [dispositivi Plug and Play IoT](#iot-plug-and-play-device) di connettersi agli endpoint che usano dispositivi in un hub IoT. Il codice client in un dispositivo usa la stringa di connessione per stabilire una connessione sicura con un hub IoT.
- Le stringhe di connessione hub IoT consentono alle soluzioni e agli strumenti back-end di connettersi in sicurezza agli endpoint che usano i servizi in un hub IoT. Questi strumenti e soluzioni gestiscono l'hub IoT e i dispositivi connessi.
- Le stringhe di connessione del repository modelli aziendale consentono alle soluzioni e agli strumenti back-end di connettersi in sicurezza a un [repository modelli aziendale](#company-model-repository). Queste soluzioni e strumenti usano o gestiscono i [modelli di funzionalità di dispositivo](#device-capability-model) e le [interfacce](#interface) nel repository.

## <a name="device-capability-model"></a>Modello di funzionalità di dispositivo

Un modello di funzionalità del dispositivo descrive un [dispositivo Plug and Play IoT](#iot-plug-and-play-device) e definisce il set di [interfacce](#interface) implementate dal dispositivo. Un modello di funzionalità di dispositivo corrisponde in genere a un dispositivo fisico, un prodotto o uno SKU. Per definire un modello di funzionalità di dispositivo, usare il [linguaggio di definizione dei gemelli digitali](#digital-twin-definition-language).

## <a name="device-certification"></a>Certificato del dispositivo

Il certificato del dispositivo è il processo di certificazione di un dispositivo [Plug and Play IoT](#iot-plug-and-play-device) in modo che possa essere aggiunto al [Catalogo dei dispositivi Certified per IoT](#certified-for-iot-device-catalog), al suo [modello di funzionalità di dispositivo](#device-capability-model) e alle sue [interfacce](#interface) aggiunte al [repository modelli pubblico](#public-model-repository).

## <a name="device-developer"></a>Sviluppatore di dispositivi

Uno sviluppatore di dispositivi usa un [modello di funzionalità di dispositivo](#device-capability-model), [interfacce](#interface) e un [Azure IoT SDK per dispositivi](#azure-iot-device-sdk) per implementare il codice per l'esecuzione in un [dispositivo Plug and Play IoT](#iot-plug-and-play-device).

## <a name="device-modeling"></a>Modelli dispositivo

Uno [sviluppatore di dispositivi](#device-developer) usa il [linguaggio di definizione dei gemelli digitali](#digital-twin-definition-language) per modellare le funzionalità di un dispositivo [Plug and Play IoT](#iot-plug-and-play-device). Il modello può essere condiviso usando un repository modelli. Uno sviluppatore di dispositivi può generare lo scheletro del codice del dispositivo dal modello. Uno [sviluppatore di soluzioni](#solution-developer) può configurare una soluzione IoT dal modello.

## <a name="device-provisioning-service"></a>Servizio di provisioning di dispositivi

[Azure IoT Central](#azure-iot-central) usa il servizio Device Provisioning per gestire la registrazione e la connessione di tutti i dispositivi. Per altre informazioni, vedere [Connettività dei dispositivi in Azure IoT Central](../iot-central/core/concepts-get-connected.md). È anche possibile usare il servizio Device Provisioning per gestire la registrazione e la connessione dei dispositivi alla soluzione IoT basata su hub IoT. Per ulteriori informazioni, vedere [Provisioning di dispositivi con il servizio Device Provisioning in hub IoT di Azure](../iot-dps/about-iot-dps.md).

## <a name="device-registration"></a>Registrazione del dispositivo

Prima che un [dispositivo Plug and Play IoT](#iot-plug-and-play-device) possa connettersi a una soluzione IoT, deve essere registrato con la soluzione. [Azure IoT Central](#azure-iot-central) usa il [servizio Device Provisioning](#device-provisioning-service) per gestire la registrazione dei dispositivi. In una soluzione IoT personalizzata è possibile registrare i dispositivi con l'hub IoT nel portale di Azure o a livello di codice.

## <a name="device-first"></a>Priorità al dispositivo

[Azure IoT Central](#azure-iot-central) supporta uno scenario di registrazione e connessione che dà la priorità al dispositivo. In questo scenario, un [dispositivo Plug and Play IoT](#iot-plug-and-play-device) può connettersi a un'applicazione IoT Central senza essere registrato. La registrazione viene eseguita automaticamente quando un dispositivo si connette per la prima volta all'applicazione.

## <a name="digital-twin"></a>Gemelli digitali

Un gemello digitale è un modello di [dispositivo Plug and Play IoT](#iot-plug-and-play-device). Un gemello digitale è modellato usando il [linguaggio di definizione dei gemelli digitali](#digital-twin-definition-language). È possibile usare gli [Azure IoT SDK per dispositivi](#azure-iot-device-sdk) per interagire con i gemelli digitali in fase di esecuzione. Ad esempio, è possibile impostare un valore di proprietà in un gemello digitale in un dispositivo e l'SDK comunica questa modifica alla soluzione IoT nel cloud.

## <a name="digital-twin-change-events"></a>Eventi di modifica del gemello digitale

Quando un [dispositivo Plug and Play IoT](#iot-plug-and-play-device) è connesso a un [hub IoT](#azure-iot-hub), l'hub può usare la funzionalità di routing per inviare le notifiche delle modifiche del gemello digitale. Ad esempio, ogni volta che un valore della [proprietà](#properties) cambia in un dispositivo, l'hub IoT può inviare una notifica a un endpoint come la coda del bus di servizio.

## <a name="digital-twin-definition-language"></a>Linguaggio di definizione dei gemelli digitali

Un linguaggio per la descrizione dei modelli e delle interfacce per [dispositivi Plug and Play IoT](#iot-plug-and-play-device). Usare il [linguaggio di definizione dei gemelli digitali](https://aka.ms/DTDL) per descrivere le funzionalità di un [gemello digitale](#digital-twin) e abilitare la piattaforma IoT e le soluzioni IoT in modo da sfruttare la semantica dell'entità.

## <a name="digital-twin-route"></a>Route gemelli digitali

Una route configurata in un [hub IoT](#azure-iot-hub) per recapitare [eventi di modifica del dispositivo gemello digitale](#digital-twin-change-events) a un endpoint, ad esempio una coda del bus di servizio.

## <a name="interface"></a>Interfaccia

Un'interfaccia descrive le funzionalità correlate implementate da un [dispositivo Plug and Play IoT](#iot-plug-and-play-device) o [gemello digitale](#digital-twin). È possibile riusare le interfacce in diversi [modelli di funzionalità di dispositivo](#device-capability-model).

## <a name="iot-hub-query-language"></a>Linguaggio di query di Hub IoT

Il linguaggio di query dell'hub IoT viene usato per diversi scopi. Ad esempio, è possibile usare il linguaggio per cercare i [dispositivi registrati](#device-registration) con l'hub IoT o per perfezionare il comportamento del [routing gemello digitale](#digital-twin-route).

## <a name="iot-plug-and-play-device"></a>Dispositivo Plug and Play IoT

Un dispositivo Plug and Play IoT è in genere un dispositivo di elaborazione autonomo e di piccole dimensioni che raccoglie dati o controlla altri dispositivi e che esegue software o firmware che implementano un [modello di funzionalità di dispositivo](#device-capability-model).  Ad esempio, un dispositivo Plug and Play IoT potrebbe essere un dispositivo di monitoraggio ambientale o un controller per un sistema di irrigazione di un sistema agricolo intelligente. È possibile scrivere una soluzione IoT ospitata nel cloud per comandare, controllare e ricevere i dati da dispositivi Plug and Play IoT. Il [catalogo dei dispositivi Azure Certified per IoT](#certified-for-iot-device-catalog) elenca i dispositivi Plug and Play IoT disponibili. Ogni dispositivo Plug and Play IoT presente nel catalogo è stato convalidato e include un [modello di funzionalità di dispositivo](#device-capability-model).

## <a name="microsoft-partner-center"></a>Microsoft Partner Center

[Microsoft Partner Center](https://docs.microsoft.com/partner-center/) è il posto dove l'organizzazione gestisce la relazione end-to-end con Microsoft. È necessario un account del Centro per i partner Microsoft prima di poter certificare i [dispositivi Plug and Play IoT](#iot-plug-and-play-device) nel portale [Azure Certified per IoT](#azure-certified-for-iot-portal).

## <a name="model-discovery"></a>Individuazione dei modelli

Quando un [dispositivo Plug and Play IoT](#iot-plug-and-play-device) si connette a una soluzione IoT, la soluzione può individuare le funzionalità del dispositivo trovando il [modello di capacità di dispositivo](#device-capability-model). Un dispositivo può inviare il proprio modello di funzionalità alla soluzione oppure la soluzione può trovare un modello di funzionalità di dispositivo in un [repository modelli](#model-repository).

## <a name="model-repository"></a>Repository modelli

Un repository modelli archivia [modelli di funzionalità di dispositivo](#device-capability-model) e [interfacce](#interface). Esiste un unico [repository modelli pubblico](#public-model-repository). Le organizzazioni possono creare i propri repository modelli aziendali.

## <a name="model-repository-rest-api"></a>REST API del repository modelli

Un'API per la gestione e l'interazione con i repository modelli. Ad esempio, è possibile usare l'API per aggiungere [modelli di funzionalità di dispositivo](#device-capability-model) e cercare modelli di funzionalità.

## <a name="properties"></a>Proprietà

Le proprietà sono campi dati definiti in un'[interfaccia](#interface) che rappresentano uno stato di un gemello digitale. È possibile dichiarare le proprietà in sola lettura o in scrittura. Le proprietà di sola lettura, ad esempio il numero di serie, vengono impostate dal codice in esecuzione nel [dispositivo Plug and Play IoT](#iot-plug-and-play-device) stesso.  Le proprietà scrivibili, ad esempio una soglia di allarme, vengono in genere impostate dalla soluzione IoT basata sul cloud.

## <a name="public-model-repository"></a>Repository modelli pubblico

È disponibile un singolo repository modelli pubblico che archivia [modelli di funzionalità di dispositivo](#device-capability-model) e [interfacce](#interface) per [dispositivi certificati](#device-certification). Il repository modelli pubblico archivia anche le definizioni dell[interfaccia comune](#common-interface).

## <a name="registration-id"></a>ID di registrazione

Un ID di registrazione identifica in modo univoco un dispositivo nel [servizio Device Provisioning](#device-provisioning-service). Questo ID non corrisponde all'ID del dispositivo che è un identificatore univoco per un dispositivo in un [hub IoT](#azure-iot-hub).

## <a name="scope-id"></a>ID ambito

L'ID ambito identifica in modo univoco l'istanza di un [servizio Device Provisioning](#device-provisioning-service) in uso.

## <a name="shared-access-signature"></a>Firma di accesso condiviso

Le firme di accesso condiviso sono un meccanismo di autenticazione basato su hash sicuri SHA-256 o URI. L'autenticazione della firma di accesso condiviso presenta due componenti: i criteri di accesso condiviso e una firma di accesso condiviso, generalamente chiamata token. Per l'autenticazione con un [hub IoT](#azure-iot-hub), un [dispositivo Plug and Play IoT](#iot-plug-and-play-device) usa una firma di accesso condiviso.

## <a name="solution-developer"></a>Sviluppatore di soluzioni

Uno sviluppatore di soluzioni crea il back-end della soluzione. Uno sviluppatore di soluzioni in genere lavora con risorse di Azure, ad esempio [hub IoT](#azure-iot-hub) e [repository modelli](#model-repository) oppure con [IoT Central](#azure-iot-central).

## <a name="telemetry"></a>Telemetria

I campi di telemetria definiti in un'[interfaccia](#interface) rappresentano le misurazioni. Queste misurazioni sono in genere valori come le letture dei sensori che vengono inviate dal [dispositivo Plug and Play IoT](#iot-plug-and-play-device) come flusso di dati.

## <a name="visual-studio-code"></a>Visual Studio Code

Visual Studio Code è un editor di codice moderno disponibile per più piattaforme. Le estensioni, ad esempio quelle nel pacchetto [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools), consentono di personalizzare l'editor per supportare un'ampia gamma di scenari di sviluppo.
