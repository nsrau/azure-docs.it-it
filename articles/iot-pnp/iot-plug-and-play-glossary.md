---
title: 'Glossario terminologico: anteprima Plug and Play IoT | Microsoft Docs'
description: "Concetti: un glossario dei termini comuni relativi all'anteprima Plug and Play IoT."
author: dominicbetts
ms.author: dobett
ms.date: 07/22/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 7d88ac62d9117df9d24c6d865e684a0972c87dae
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87337314"
---
# <a name="glossary-of-terms-for-iot-plug-and-play-preview"></a>Glossario terminologico per l'anteprima Plug and Play IoT

Definizioni dei termini comuni usati negli articoli Plug and Play IoT.

## <a name="azure-iot-explorer-tool"></a>Strumento Azure IoT Explorer

Azure IoT explorer è uno strumento grafico che è possibile usare per interagire con e testare i [dispositivi Plug and Play IoT](#iot-plug-and-play-device). Dopo aver installato lo strumento nel computer locale, è possibile usarlo per:

- Visualizzare i dispositivi connessi all'[hub IoT](#azure-iot-hub).
- Connettersi a un dispositivo Plug and Play IoT.
- Visualizzare i [componenti](#component)del dispositivo.
- Visualizzare i [dati di telemetria](#telemetry) inviati dal dispositivo.
- Lavorare con [proprietà](#properties) del dispositivo.
- Chiamare i [comandi](#commands) dispositivo.

## <a name="azure-iot-hub"></a>Hub IoT Azure

IoT Hub è un servizio gestito, ospitato nel cloud, che funge da hub del messaggio centrale per la comunicazione bidirezionale tra l'applicazione di IoT e i dispositivi gestiti. I [dispositivi Plug and Play IoT](#iot-plug-and-play-device) possono connettersi a un hub IoT. Una soluzione IoT usa un hub IoT per abilitare:

- Dispositivi per l'invio di dati di telemetria a una soluzione basata sul cloud.
- Una soluzione basata sul cloud per gestire i dispositivi connessi.

## <a name="azure-iot-device-sdk"></a>Azure IoT SDK per dispositivi

Sono disponibili SDK per dispositivi per più lingue che è possibile usare per compilare applicazioni client per dispositivi Plug and Play IoT.

## <a name="commands"></a>Comandi

I comandi definiti in un'[interfaccia](#interface) rappresentano i metodi che possono essere eseguiti nel [gemello digitale](#digital-twin). Ad esempio, un comando per riavviare un dispositivo.

## <a name="component"></a>Componente

I componenti consentono di creare un' [interfaccia](#interface) del modello come assembly di altre interfacce. Un [modello di dispositivo](#device-model) può combinare più interfacce come componenti. Un modello, ad esempio, può includere un componente switch e un componente termostato. Più componenti in un modello possono usare anche lo stesso tipo di interfaccia. Un modello, ad esempio, può includere due componenti termostato.

## <a name="connection-string"></a>Stringa di connessione

Una stringa di connessione incapsula le informazioni necessarie per connettersi a un endpoint. Una stringa di connessione contiene in genere l'indirizzo dell'endpoint e informazioni di sicurezza, ma il formato della stringa può variare nei diversi servizi. Esistono due tipi di stringa di connessione associato al servizio hub IoT:

- Stringhe di connessione dispositivo che consentono ai [dispositivi Plug and Play IoT](#iot-plug-and-play-device) di connettersi agli endpoint che usano dispositivi in un hub IoT. Il codice client in un dispositivo usa la stringa di connessione per stabilire una connessione sicura con un hub IoT.
- Le stringhe di connessione hub IoT consentono alle soluzioni e agli strumenti back-end di connettersi in sicurezza agli endpoint che usano i servizi in un hub IoT. Questi strumenti e soluzioni gestiscono l'hub IoT e i dispositivi connessi.

## <a name="device-model"></a>Modello del dispositivo

Un modello di dispositivo descrive un [dispositivo Plug and Play](#iot-plug-and-play-device) e definisce i [componenti](#component) che costituiscono il dispositivo. Un modello di dispositivo semplice non ha componenti distinti e contiene una definizione per una singola interfaccia a livello radice. Un modello di dispositivo più complesso include più componenti. Un modello di dispositivo corrisponde in genere a un dispositivo fisico, un prodotto o uno SKU. Per definire un modello di dispositivo, è possibile usare la [versione 2 del linguaggio Digital Twins Definition](#digital-twins-definition-language) .

## <a name="device-builder"></a>Generatore di dispositivi

Un generatore di dispositivi usa un [modello di dispositivo](#device-model) e le [interfacce](#interface) quando si implementa il codice per l'esecuzione su un [dispositivo Plug and Play](#iot-plug-and-play-device). I generatori di dispositivi usano in genere uno degli [SDK per dispositivi Azure](#azure-iot-device-sdk) per implementare il client del dispositivo, ma ciò non è obbligatorio.

## <a name="device-modeling"></a>Modelli dispositivo

Un [Generatore di dispositivi](#device-builder) usa il [linguaggio di definizione delle gemelle digitali](#digital-twins-definition-language) per modellare le funzionalità di un [dispositivo Plug and Play](#iot-plug-and-play-device). Un [Generatore di soluzioni](#solution-builder) può configurare una soluzione Internet delle cose dal modello.

## <a name="digital-twin"></a>Gemelli digitali

Un gemello digitale è un modello di [dispositivo Plug and Play IoT](#iot-plug-and-play-device). Un dispositivo gemello digitale è modellato con il [linguaggio di definizione dei gemelli digitali](#digital-twins-definition-language). È possibile usare gli [Azure IoT SDK per dispositivi](#azure-iot-device-sdk) per interagire con i gemelli digitali in fase di esecuzione. Ad esempio, è possibile impostare un valore di proprietà in un gemello digitale in un dispositivo e l'SDK comunica questa modifica alla soluzione IoT nel cloud.

## <a name="digital-twin-change-events"></a>Eventi di modifica del gemello digitale

Quando un [dispositivo Plug and Play IoT](#iot-plug-and-play-device) è connesso a un [hub IoT](#azure-iot-hub), l'hub può usare la funzionalità di routing per inviare le notifiche delle modifiche del gemello digitale. Ad esempio, ogni volta che un valore della [Proprietà](#properties) viene modificato in un dispositivo, l'hub Internet può inviare una notifica a un endpoint, ad esempio un hub eventi.

## <a name="digital-twins-definition-language"></a>Linguaggio di definizione gemelli digitali

Un linguaggio per la descrizione dei modelli e delle interfacce per [dispositivi Plug and Play IoT](#iot-plug-and-play-device). Usa la [versione 2 del linguaggio Digital Twins Definition](https://github.com/Azure/opendigitaltwins-dtdl) per descrivere le funzionalità di un dispositivo [gemello digitale](#digital-twin) e abilitare la piattaforma Internet delle cose e le soluzioni Internet delle cose per sfruttare la semantica dell'entità.

## <a name="digital-twin-route"></a>Route gemelli digitali

Una route configurata in un [Hub](#azure-iot-hub) Internet per la distribuzione di eventi di modifica di dispositivi [gemelli digitali](#digital-twin-change-events) a un endpoint, ad esempio un hub eventi.

## <a name="interface"></a>Interfaccia

Un'interfaccia descrive le funzionalità correlate implementate da un [dispositivo Plug and Play IoT](#iot-plug-and-play-device) o [gemello digitale](#digital-twin). È possibile riutilizzare le interfacce in diversi [modelli di dispositivo](#device-model). Quando un'interfaccia viene usata in un modello di dispositivo, definisce un [componente](#component) del dispositivo.

## <a name="iot-hub-query-language"></a>Linguaggio di query di Hub IoT

Il linguaggio di query dell'hub IoT viene usato per diversi scopi. Ad esempio, è possibile usare il linguaggio per cercare i dispositivi registrati con l'hub IoT o per perfezionare il comportamento del [routing gemello digitale](#digital-twin-route).

## <a name="iot-plug-and-play-device"></a>Dispositivo Plug and Play IoT

Un dispositivo Plug and Play è in genere un dispositivo di elaborazione autonomo di piccole dimensioni che raccoglie dati o controlla altri dispositivi e che esegue software o firmware che implementa un [modello di dispositivo](#device-model).  Ad esempio, un dispositivo Plug and Play IoT potrebbe essere un dispositivo di monitoraggio ambientale o un controller per un sistema di irrigazione di un sistema agricolo intelligente. È possibile scrivere una soluzione IoT ospitata nel cloud per comandare, controllare e ricevere i dati da dispositivi Plug and Play IoT.

## <a name="iot-plug-and-play-conventions"></a>Convenzioni Plug and Play

I [dispositivi](#iot-plug-and-play-device) plug and Play dovrebbero seguire un set di [convenzioni](concepts-convention.md) quando scambiano dati con una soluzione.

## <a name="model-discovery"></a>Individuazione dei modelli

Quando un [plug and Play dispositivo](#iot-plug-and-play-device) si connette a una soluzione Internet delle cose, la soluzione può individuare le funzionalità del dispositivo individuando il [modello di dispositivo](#device-model). La soluzione può trovare un modello di dispositivo in un [repository di modelli](#model-repository) o in un'altra posizione.

## <a name="model-id"></a>ID modello

Quando un Plug and Play dispositivo si connette a un hub Internet, invia l' **ID modello** del modello [DTDL](#digital-twins-definition-language) che implementa. Questo consente alla soluzione di trovare il modello di dispositivo.

## <a name="model-repository"></a>Repository modelli

Un [repository](concepts-model-repository.md) di modelli archivia i modelli e le [interfacce](#interface)del [dispositivo](#device-model) .

## <a name="model-repository-rest-api"></a>REST API del repository modelli

API per la gestione e l'interazione con il repository del modello. Ad esempio, è possibile usare l'API per aggiungere e cercare i [modelli di dispositivo](#device-model).

## <a name="properties"></a>Proprietà

Le proprietà sono campi dati definiti in un'[interfaccia](#interface) che rappresentano uno stato di un gemello digitale. È possibile dichiarare le proprietà in sola lettura o in scrittura. Le proprietà di sola lettura, ad esempio il numero di serie, vengono impostate dal codice in esecuzione nel [dispositivo Plug and Play IoT](#iot-plug-and-play-device) stesso.  Le proprietà scrivibili, ad esempio una soglia di allarme, vengono in genere impostate dalla soluzione IoT basata sul cloud.

## <a name="shared-access-signature"></a>Firma di accesso condiviso

Le firme di accesso condiviso sono un meccanismo di autenticazione basato su hash sicuri SHA-256 o URI. L'autenticazione della firma di accesso condiviso presenta due componenti: i criteri di accesso condiviso e una firma di accesso condiviso, generalamente chiamata token. Per l'autenticazione con un [hub IoT](#azure-iot-hub), un [dispositivo Plug and Play IoT](#iot-plug-and-play-device) usa una firma di accesso condiviso.

## <a name="solution-builder"></a>Generatore di soluzioni

Un generatore di soluzioni crea il back-end della soluzione. Un generatore di soluzioni funziona in genere con le risorse di Azure, ad esempio i repository dell' [Hub](#azure-iot-hub) e del [modello](#model-repository).

## <a name="telemetry"></a>Telemetria

I campi di telemetria definiti in un'[interfaccia](#interface) rappresentano le misurazioni. Queste misurazioni sono in genere valori come le letture dei sensori che vengono inviate dal [dispositivo Plug and Play IoT](#iot-plug-and-play-device) come flusso di dati.
