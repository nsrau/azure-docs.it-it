---
title: Glossario dei termini Plug and Play anteprima | Microsoft Docs
description: 'Concetti: un glossario dei termini comuni relativi a Internet delle cose Plug and Play anteprima.'
author: ChrisGMsft
ms.author: chrisgre
ms.date: 10/10/2019
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: a5c9d2d54fcce1179e43c38027db23c3d7e557e8
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72249293"
---
# <a name="glossary-of-terms-for-iot-plug-and-play-preview"></a>Glossario dei termini per la Plug and Play di anteprima

Definizioni dei termini comuni usati negli articoli Plug and Play.

## <a name="azure-certified-for-iot-portal"></a>Portale Azure Certified per le cose

È possibile usare il sito Web del [portale Azure Certified per](https://aka.ms/ACFI) le cose per:

- Completare il [processo di certificazione](#device-certification) per il [dispositivo Plug and Play](#iot-plug-and-play-device).
- Trovare i [modelli di funzionalità del dispositivo](#device-capability-model).
- Pubblicare un modello di funzionalità del dispositivo nel [repository del modello pubblico](#public-model-repository).

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

L'interfaccia della riga di comando di Azure è uno strumento da riga di comando multipiattaforma per la gestione delle risorse di Azure. L'estensione Azure per l'interfaccia della riga di comando di Azure è uno strumento da riga di comando per l'interazione con e il testing dei [dispositivi Plug and Play](#iot-plug-and-play-device). È possibile usare l'estensione per:

- Connettersi a un dispositivo Plug and Play.
- Visualizzare i dati di [telemetria](#telemetry) inviati dal dispositivo.
- Usare le [Proprietà](#properties)del dispositivo.
- Chiamare i [comandi](#commands)del dispositivo.
- Gestire i [repository](#model-repository)di modelli, le [interfacce](#interface)e i modelli di [funzionalità del dispositivo](#device-capability-model).

## <a name="azure-iot-central"></a>Azure IoT Central

Azure IoT Central è una soluzione software distribuita come servizio completamente gestita che semplifica la connessione, il monitoraggio e la gestione dei tuoi [dispositivi Plug and Play](#iot-plug-and-play-device). È possibile usare i [modelli di funzionalità del dispositivo](#device-capability-model) per configurare automaticamente un'applicazione IoT Central per il monitoraggio e la gestione dei dispositivi.

## <a name="azure-iot-certification-service"></a>Servizio Azure Internet per la certificazione

Il servizio Azure Internet per la certificazione esegue un set di test di certificazione quando si invia un [plug and Play dispositivo](#iot-plug-and-play-device) per la certificazione tramite il [portale di Azure Certified per](#azure-certified-for-iot-portal)Internet. Prima di poter aggiungere un dispositivo al [Catalogo dei dispositivi Certified for](#certified-for-iot-device-catalog)Internet, il dispositivo deve essere certificato.

## <a name="azure-iot-tools-extension"></a>Estensione degli strumenti di Azure.

Azure Internet è una raccolta di estensioni in [Visual Studio Code](#visual-studio-code) che consentono di interagire con l'hub Internet e sviluppare dispositivi Internet. Per quanto riguarda Plug and Play lo sviluppo di dispositivi, consente di:

- Creare [modelli](#device-capability-model) e [interfacce](#interface)per le funzionalità del dispositivo.
- Pubblicare in [repository di modelli](#model-repository).
- Genera il codice di scheletro per implementare l'applicazione del dispositivo.

## <a name="azure-iot-explorer-tool"></a>Strumento Esplora risorse di Azure

Azure Internet Explorer è uno strumento grafico che è possibile usare per interagire e testare i [dispositivi Plug and Play](#iot-plug-and-play-device). Dopo aver installato lo strumento nel computer locale, è possibile usarlo per:

- Visualizzare i dispositivi connessi all' [Hub](#azure-iot-hub)Internet delle cose.
- Connettersi a un dispositivo Plug and Play.
- Visualizzare i dati di [telemetria](#telemetry) inviati dal dispositivo.
- Usare le [Proprietà](#properties)del dispositivo.
- Chiamare i [comandi](#commands)del dispositivo.

## <a name="azure-iot-hub"></a>Hub IoT Azure

IoT Hub è un servizio gestito, ospitato nel cloud, che funge da hub del messaggio centrale per la comunicazione bidirezionale tra l'applicazione di IoT e i dispositivi gestiti. I [dispositivi Plug and Play](#iot-plug-and-play-device) possono connettersi a un hub Internet. Una soluzione Internet delle cose usa un hub Internet delle cose per abilitare:

- Dispositivi per l'invio di dati di telemetria a una soluzione basata sul cloud.
- Una soluzione basata sul cloud per gestire i dispositivi connessi.

## <a name="azure-iot-device-sdk"></a>Azure IoT SDK per dispositivi

Sono disponibili SDK per dispositivi per più lingue che è possibile usare per compilare Plug and Play applicazioni client per dispositivi. Uno dei requisiti per la [certificazione del dispositivo](#device-certification) è che il codice client del dispositivo usa uno degli SDK per dispositivi Azure.

## <a name="certified-for-iot-device-catalog"></a>Catalogo dei dispositivi Certified per Internet

Il [Catalogo dei dispositivi Certified for](https://catalog.azureiotsolutions.com/) Internet è un elenco degli [plug and Play dispositivi](#iot-plug-and-play-device) che hanno superato i test di [certificazione del dispositivo](#device-certification) . I [modelli di funzionalità del dispositivo](#device-capability-model) per i dispositivi Plug and Play nel catalogo e pubblicati nel repository del modello pubblico.

## <a name="commands"></a>Comandi:

I comandi definiti in un' [interfaccia](#interface) rappresentano i metodi che possono essere eseguiti nel dispositivo [gemello digitale](#digital-twin). Ad esempio, un comando per riavviare un dispositivo.

## <a name="common-interface"></a>Interfaccia comune

Per tutti i [dispositivi Plug and Play](#iot-plug-and-play-device) si prevede di implementare alcune [interfacce](#interface)comuni. L'interfaccia informazioni sul dispositivo, ad esempio, definisce le informazioni relative all'hardware e al sistema operativo del dispositivo. Per la [certificazione del dispositivo](#device-certification) è necessario che il dispositivo implementi diverse interfacce comuni. È possibile recuperare le definizioni di interfaccia comuni dal repository del modello pubblico.

## <a name="company-model-repository"></a>Repository modello aziendale

Un'organizzazione può usare un [repository del modello](#model-repository) aziendale come archivio privato per i modelli e le [interfacce](#interface)di [funzionalità del dispositivo](#device-capability-model) .

## <a name="connection-string"></a>Stringa di connessione

Una stringa di connessione incapsula le informazioni necessarie per connettersi a un endpoint. Una stringa di connessione contiene in genere l'indirizzo dell'endpoint e informazioni di sicurezza, ma il formato della stringa può variare nei diversi servizi. Esistono due tipi di stringa di connessione associato al servizio hub IoT:

- Le stringhe di connessione del dispositivo consentono ai [dispositivi Plug and Play](#iot-plug-and-play-device) per la connessione agli endpoint per il dispositivo in un hub. Il codice client in un dispositivo usa la stringa di connessione per stabilire una connessione sicura con un hub.
- Le stringhe di connessione dell'hub Internet consentono di connettere soluzioni e strumenti back-end in modo sicuro agli endpoint per il servizio in un hub. Questi strumenti e soluzioni gestiscono l'hub Internet e i dispositivi connessi.
- Le stringhe di connessione del repository del modello aziendale consentono soluzioni e strumenti back-end per la connessione sicura a un [repository di modelli aziendali](#company-model-repository). Queste soluzioni e strumenti utilizzano o gestiscono i modelli e le [interfacce](#interface) di [funzionalità del dispositivo](#device-capability-model) nel repository.

## <a name="device-capability-model"></a>Modello di funzionalità di dispositivo

Un modello di funzionalità del dispositivo descrive un [dispositivo Plug and Play](#iot-plug-and-play-device) e definisce il set di [interfacce](#interface) implementato dal dispositivo. Un modello di funzionalità del dispositivo corrisponde in genere a un dispositivo fisico, un prodotto o uno SKU. Per definire un modello di funzionalità del dispositivo, è possibile usare il [linguaggio Digital Twin Definition](#digital-twin-definition-language) .

## <a name="device-certification"></a>Certificazione del dispositivo

La certificazione dei dispositivi è il processo di certificazione di un [dispositivo Plug and Play](#iot-plug-and-play-device) in modo che possa essere aggiunto al [Catalogo dei dispositivi Certified for](#certified-for-iot-device-catalog) Internet e al modello di [capacità del dispositivo](#device-capability-model) e alle [interfacce](#interface) aggiunte al [modello pubblico repository](#public-model-repository).

## <a name="device-developer"></a>Sviluppatore di dispositivi

Uno sviluppatore di dispositivi usa un [modello di funzionalità del dispositivo](#device-capability-model), [interfacce](#interface)e un [SDK per dispositivi Azure](#azure-iot-device-sdk) per implementare il codice per l'esecuzione su un [dispositivo Plug and Play](#iot-plug-and-play-device).

## <a name="device-modeling"></a>Modellazione del dispositivo

Uno [sviluppatore di dispositivi](#device-developer) usa il [linguaggio di definizione doppia digitale](#digital-twin-definition-language) per modellare le funzionalità di un [dispositivo Plug and Play](#iot-plug-and-play-device). Il modello può essere condiviso usando un repository di modelli. Uno sviluppatore di dispositivi può generare codice del dispositivo Skeleton dal modello. Uno [sviluppatore di soluzioni](#solution-developer) può configurare una soluzione Internet delle cose dal modello.

## <a name="device-provisioning-service"></a>Servizio di provisioning di dispositivi

[Azure IOT Central](#azure-iot-central) usa il servizio Device provisioning per gestire la registrazione e la connessione dei dispositivi. Per altre informazioni, vedere [connettività dei dispositivi in Azure IOT Central](../iot-central/concepts-connectivity-pnp.md). È anche possibile usare il servizio Device provisioning per gestire la registrazione e la connessione dei dispositivi alla soluzione Internet delle cose in base all'hub. Per altre informazioni, vedere [provisioning di dispositivi con il servizio Device provisioning in hub Azure](../iot-dps/about-iot-dps.md).

## <a name="device-registration"></a>Registrazione del dispositivo

Prima che un [dispositivo Plug and Play](#iot-plug-and-play-device) possa connettersi a una soluzione Internet delle cose, deve essere registrato con la soluzione. [Azure IOT Central](#azure-iot-central) usa il [servizio Device provisioning](#device-provisioning-service) per gestire la registrazione del dispositivo. In una soluzione di Internet delle cose personalizzata è possibile registrare i dispositivi con l'hub Internet nel portale di Azure o a livello di codice.

## <a name="device-first"></a>Prima dispositivo

[Azure IOT Central](#azure-iot-central) supporta uno scenario di registrazione e connessione prima del dispositivo. In questo scenario, un [dispositivo Plug and Play](#iot-plug-and-play-device) è in grado di connettersi a un'applicazione IoT Central senza essere registrato in anticipo. La registrazione viene eseguita automaticamente quando un dispositivo si connette per la prima volta all'applicazione.

## <a name="digital-twin"></a>Gemello digitale

Un dispositivo gemello digitale è un modello di un [dispositivo Plug and Play](#iot-plug-and-play-device). Un dispositivo gemello digitale è modellato con il [linguaggio di definizione gemello digitale](#digital-twin-definition-language). È possibile usare gli [SDK per dispositivi Azure](#azure-iot-device-sdk) per interagire con i dispositivi gemelli digitali in fase di esecuzione. Ad esempio, è possibile impostare un valore di proprietà in un dispositivo gemello digitale in un dispositivo e l'SDK comunica questa modifica alla soluzione Internet delle cose nel cloud.

## <a name="digital-twin-change-events"></a>Eventi di modifica del dispositivo gemello digitale

Quando un [dispositivo Plug and Play](#iot-plug-and-play-device) è connesso a un [Hub](#azure-iot-hub)Internet delle cose, l'hub può usare la funzionalità di routing per inviare notifiche delle modifiche del dispositivo gemello digitale. Ad esempio, ogni volta che un valore della [Proprietà](#properties) viene modificato in un dispositivo, l'hub Internet può inviare una notifica a un endpoint, ad esempio una coda del bus di servizio.

## <a name="digital-twin-definition-language"></a>Digital Twin Definition Language

Linguaggio per la descrizione dei modelli e delle interfacce per i [dispositivi Plug and Play](#iot-plug-and-play-device). Usa il [linguaggio di definizione doppia digitale](https://aka.ms/DTDL) per descrivere le funzionalità di un dispositivo [gemello digitale](#digital-twin) e abilitare la piattaforma Internet delle cose e le soluzioni Internet delle cose per sfruttare la semantica dell'entità.

## <a name="digital-twin-route"></a>Route del dispositivo gemello digitale

Una route configurata in un [Hub](#azure-iot-hub) Internet per il recapito di eventi di modifica del dispositivo [gemello digitale](#digital-twin-change-events) a un endpoint, ad esempio una coda del bus di servizio.

## <a name="interface"></a>Interfaccia

Un'interfaccia descrive le funzionalità correlate implementate da un [plug and Play dispositivi](#iot-plug-and-play-device) o un dispositivo [gemello digitale](#digital-twin). È possibile riutilizzare le interfacce in diversi [modelli di funzionalità del dispositivo](#device-capability-model).

## <a name="iot-hub-query-language"></a>Linguaggio di query di Hub IoT

Il linguaggio di query dell'hub Internet viene usato per diversi scopi. Ad esempio, è possibile usare il linguaggio per la ricerca di [dispositivi registrati](#device-registration) con l'hub Internet o per perfezionare il comportamento di [routing del gemello digitale](#digital-twin-route) .

## <a name="iot-plug-and-play-device"></a>Dispositivo Plug and Play

Un dispositivo Plug and Play è in genere un dispositivo di elaborazione autonomo di piccole dimensioni che raccoglie dati o controlla altri dispositivi e che esegue software o firmware che implementa un [modello di funzionalità del dispositivo](#device-capability-model).  Ad esempio, un dispositivo Plug and Play potrebbe essere un dispositivo di monitoraggio ambientale o un controller per un sistema di irrigazione con Smart Agriculture. È possibile scrivere una soluzione Internet delle cose ospitata nel cloud per i comandi, il controllo e la ricezione di dati da dispositivi Plug and Play. Il [Catalogo dei dispositivi Azure Certified per](#certified-for-iot-device-catalog) Internet è elencato plug and Play dispositivi. Ogni sacco Plug and Play dispositivo nel catalogo è stato convalidato e ha un [modello di capacità del dispositivo](#device-capability-model).

## <a name="microsoft-partner-center"></a>Centro per i partner Microsoft

Il centro per i [partner Microsoft](https://docs.microsoft.com/partner-center/) è il punto in cui l'organizzazione gestisce la relazione end-to-end con Microsoft. È necessario un account del centro per i partner Microsoft prima di poter certificare le cose [plug and Play dispositivo](#iot-plug-and-play-device) nel [portale di Azure Certified per](#azure-certified-for-iot-portal)Internet.

## <a name="model-discovery"></a>Individuazione dei modelli

Quando un [plug and Play dispositivo](#iot-plug-and-play-device) si connette a una soluzione Internet delle cose, la soluzione può individuare le funzionalità del dispositivo individuando il [modello di capacità del dispositivo](#device-capability-model). Un dispositivo può inviare il modello di funzionalità alla soluzione oppure la soluzione può trovare un modello di funzionalità del dispositivo in un [repository di modelli](#model-repository).

## <a name="model-repository"></a>Repository di modelli

Un repository di modelli archivia i modelli e le [interfacce](#interface)di [funzionalità del dispositivo](#device-capability-model) . È disponibile un unico [repository di modelli pubblici](#public-model-repository). Le organizzazioni possono creare repository di modelli organizzativi.

## <a name="model-repository-rest-api"></a>API REST del repository di modelli

API per la gestione e l'interazione con i repository del modello. Ad esempio, è possibile usare l'API per aggiungere [modelli di funzionalità del dispositivo](#device-capability-model) e cercare modelli di funzionalità.

## <a name="properties"></a>Proprietà

Le proprietà sono campi dati definiti in un' [interfaccia](#interface) che rappresentano uno stato di un gemello digitale. È possibile dichiarare le proprietà in sola lettura o in scrittura. Le proprietà di sola lettura, ad esempio il numero di serie, vengono impostate dal codice in esecuzione nel [plug and Play del dispositivo](#iot-plug-and-play-device) .  Le proprietà scrivibili, ad esempio una soglia di allarme, vengono in genere impostate dalla soluzione Internet delle cose.

## <a name="public-model-repository"></a>Repository del modello pubblico

È disponibile un unico repository del modello pubblico che archivia i modelli e le [interfacce](#interface) di [funzionalità del dispositivo](#device-capability-model) per i [dispositivi certificati](#device-certification). Il repository del modello pubblico archivia anche le definizioni dell' [interfaccia comune](#common-interface) .

## <a name="registration-id"></a>ID di registrazione

Un ID di registrazione identifica in modo univoco un dispositivo nel [servizio Device provisioning](#device-provisioning-service). Questo ID non corrisponde all'ID del dispositivo che rappresenta un identificatore univoco per un dispositivo in un [Hub](#azure-iot-hub)Internet.

## <a name="scope-id"></a>ID ambito

L'ambito ID ambito identifica in modo univoco un'istanza del [servizio Device provisioning](#device-provisioning-service) .

## <a name="shared-access-signature"></a>Firma di accesso condiviso

Le firme di accesso condiviso sono un meccanismo di autenticazione basato su hash sicuri SHA-256 o URI. L'autenticazione della firma di accesso condiviso include due componenti: un criterio di accesso condiviso e una firma di accesso condiviso (spesso denominata token). Un [dispositivo Plug and Play](#iot-plug-and-play-device) utilizza una firma di accesso condiviso per l'autenticazione con un [Hub](#azure-iot-hub)Internet.

## <a name="solution-developer"></a>Sviluppatore di soluzioni

Uno sviluppatore di soluzioni crea il back-end della soluzione. Uno sviluppatore di soluzioni in genere funziona con le risorse di Azure, ad esempio i [repository del modello](#model-repository)e dell' [Hub](#azure-iot-hub) degli oggetti, oppure funziona con [IOT Central](#azure-iot-central).

## <a name="telemetry"></a>Telemetria

I campi di telemetria definiti in un' [interfaccia](#interface) rappresentano le misurazioni. Queste misurazioni sono in genere valori come le letture dei sensori che vengono inviate dal [dispositivo Plug and Play](#iot-plug-and-play-device) come un flusso di dati.

## <a name="visual-studio-code"></a>Visual Studio Code

Visual Studio Code è un editor di codice moderno disponibile per più piattaforme. Le estensioni, ad esempio quelle presenti nel pacchetto Azure Internet degli [strumenti](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) , consentono di personalizzare l'editor per supportare un'ampia gamma di scenari di sviluppo.
