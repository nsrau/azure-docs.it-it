---
title: Glossario dei termini - IoT Plug and Play Preview Documenti Microsoft
description: Concetti - un glossario di termini comuni relativi a IoT Plug and Play Preview.
author: ChrisGMsft
ms.author: chrisgre
ms.date: 12/23/2019
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: ef5ce9cc1cda7f1ff6b1985771e20cb20123e264
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025640"
---
# <a name="glossary-of-terms-for-iot-plug-and-play-preview"></a>Glossario dei termini per IoT Plug and Play Preview

Definizioni dei termini comuni utilizzati negli articoli IoT Plug and Play.

## <a name="azure-certified-for-iot-portal"></a>Azure Certified per il portale IoT

È possibile usare il sito Web [del portale Azure Certified for IoT](https://aka.ms/ACFI) per:You can use the Azure Certified for IoT portal website to:

- Completa il processo di [certificazione](#device-certification) per il tuo [dispositivo IoT Plug and Play](#iot-plug-and-play-device).
- Trovare [i modelli di funzionalità del dispositivo](#device-capability-model).
- Pubblicare un modello di funzionalità del dispositivo nel repository dei [modelli pubblici.](#public-model-repository)

## <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

L'interfaccia della riga di comando di Azure è uno strumento da riga di comando multipiattaforma per la gestione delle risorse di Azure.The Azure CLI is a cross-platform, command-line tool for managing Azure resources. L'estensione IoT di Azure per l'interfaccia della riga di comando di Azure è uno strumento della riga di comando per interagire con i [dispositivi Plug and Play IoT](#iot-plug-and-play-device). È possibile utilizzare l'estensione per:

- Connettersi a un dispositivo IoT Plug and Play.
- Visualizzare i [dati di telemetria](#telemetry) inviati dal dispositivo.
- Utilizzare [le proprietà](#properties)del dispositivo .
- Chiamare [i comandi](#commands)del dispositivo .
- Gestire archivi [di modelli,](#model-repository) [interfacce](#interface)e modelli di [funzionalità dei dispositivi.](#device-capability-model)

## <a name="azure-iot-central"></a>Azure IoT Central

Azure IoT Central è una soluzione software-as-a-service completamente gestita che semplifica la connessione, il monitoraggio e la gestione dei [dispositivi Plug and Play IoT.](#iot-plug-and-play-device) È possibile usare i modelli di [funzionalità dei dispositivi](#device-capability-model) per configurare automaticamente un'applicazione IoT Central per monitorare e gestire i dispositivi.

## <a name="azure-iot-certification-service"></a>Servizio di certificazione IoT di AzureAzure IoT certification service

Il servizio di certificazione IoT di Azure esegue una serie di test di certificazione quando si invia un [dispositivo Plug and Play IoT](#iot-plug-and-play-device) per la certificazione tramite il portale Azure Certified for [IoT](#azure-certified-for-iot-portal). Prima di poter aggiungere un dispositivo al catalogo dei dispositivi [Certified for IoT](#certified-for-iot-device-catalog), il dispositivo deve essere certificato.

## <a name="azure-iot-tools-extension"></a>Estensione Strumenti IoT di AzureAzure IoT Tools extension

Strumenti IoT di Azure è una raccolta di estensioni nel codice di Visual Studio che consentono di interagire con l'hub IoT e sviluppare dispositivi IoT.Azure IoT Tools is a collection of extensions in [Visual Studio code](#visual-studio-code) that help you interact with IoT Hub and develop IoT devices. Per lo sviluppo di dispositivi IoT Plug and Play, ti aiuta a:

- Creare modelli e [interfacce](#interface)di [funzionalità del dispositivo.](#device-capability-model)
- Pubblica in [repository modello](#model-repository).
- Generare lo scheletro di codice per implementare l'applicazione del dispositivo.

## <a name="azure-iot-explorer-tool"></a>Strumento Esplora IoT di AzureAzure IoT explorer tool

Azure IoT Explorer è uno strumento grafico che è possibile usare per interagire e testare i [dispositivi Plug and Play IoT](#iot-plug-and-play-device). Dopo aver installato lo strumento nel computer locale, è possibile utilizzarlo per:

- Visualizzare i dispositivi connessi [all'hub IoT.](#azure-iot-hub)
- Connettersi a un dispositivo IoT Plug and Play.
- Visualizzare i [dati di telemetria](#telemetry) inviati dal dispositivo.
- Utilizzare [le proprietà](#properties)del dispositivo .
- Chiamare [i comandi](#commands)del dispositivo .

## <a name="azure-iot-hub"></a>Hub IoT Azure

IoT Hub è un servizio gestito, ospitato nel cloud, che funge da hub del messaggio centrale per la comunicazione bidirezionale tra l'applicazione di IoT e i dispositivi gestiti. [I dispositivi IoT Plug and Play](#iot-plug-and-play-device) possono connettersi a un hub IoT. Una soluzione IoT usa un hub IoT per abilitare:An IoT solution uses an IoT hub to enable:

- Dispositivi per l'invio di dati di telemetria a una soluzione basata su cloud.
- Una soluzione basata su cloud per gestire i dispositivi connessi.

## <a name="azure-iot-device-sdk"></a>Azure IoT SDK per dispositivi

Esistono SDK per più lingue che è possibile utilizzare per creare applicazioni client ioT Plug and Play per dispositivi. Uno dei requisiti per la [certificazione](#device-certification) dei dispositivi è che il codice client del dispositivo usi uno degli SDK del dispositivo Azure IoT.One of the requirements for device certification is that the device client code uses one of the Azure IoT device SDKs.

## <a name="certified-for-iot-device-catalog"></a>Certificato per il catalogo dei dispositivi IoT

Il catalogo dei [dispositivi Certified for IoT](https://catalog.azureiotsolutions.com/) elenca i [dispositivi Plug and Play IoT](#iot-plug-and-play-device) che hanno superato i test di certificazione dei [dispositivi.](#device-certification) I modelli di [funzionalità del dispositivo](#device-capability-model) per i dispositivi IoT Plug and Play nel catalogo e pubblicati nel repository dei modelli pubblici.

## <a name="commands"></a>Comandi:

I comandi definiti in [un'interfaccia](#interface) rappresentano metodi che possono essere eseguiti sul [file digital twin](#digital-twin). Ad esempio, un comando per riavviare un dispositivo.

## <a name="common-interface"></a>Interfaccia comune

Tutti i [dispositivi IoT Plug and Play](#iot-plug-and-play-device) sono tenuti a implementare alcune [interfacce](#interface)comuni . Ad esempio, l'interfaccia delle informazioni sul dispositivo definisce le informazioni hardware e del sistema operativo relative al dispositivo. [La certificazione del dispositivo](#device-certification) richiede che il dispositivo implementi diverse interfacce comuni. È possibile recuperare le definizioni di interfaccia comuni dal repository dei modelli pubblici.

## <a name="company-model-repository"></a>Repository dei modelli aziendali

Un'organizzazione può utilizzare un repository di [modelli](#model-repository) aziendali come archivio privato per i modelli e [le interfacce](#interface)di funzionalità dei [dispositivi.](#device-capability-model)

## <a name="connection-string"></a>Stringa di connessione

Una stringa di connessione incapsula le informazioni necessarie per connettersi a un endpoint. Una stringa di connessione contiene in genere l'indirizzo dell'endpoint e informazioni di sicurezza, ma il formato della stringa può variare nei diversi servizi. Esistono due tipi di stringa di connessione associato al servizio hub IoT:

- Le stringhe di connessione del dispositivo consentono ai [dispositivi Plug and Play IoT](#iot-plug-and-play-device) di connettersi agli endpoint rivolti al dispositivo in un hub IoT.Device connection strings enable IoT Plug and Play devices to connect to the device-facing endpoints on an IoT hub. Il codice client in un dispositivo usa la stringa di connessione per stabilire una connessione sicura con un hub IoT.Client code on a device uses the connection string to establish a secure connection with an IoT hub.
- Le stringhe di connessione dell'hub IoT consentono alle soluzioni e agli strumenti back-end di connettersi in modo sicuro agli endpoint rivolti al servizio in un hub IoT.IoT Hub connection strings enable back-end solutions and tools to connect securely to the service-facing endpoints on an IoT hub. Queste soluzioni e strumenti gestiscono l'hub IoT e i dispositivi ad esso connessi.
- Le stringhe di connessione del repository dei modelli aziendali consentono alle soluzioni e agli strumenti back-end di connettersi in modo sicuro a un repository di [modelli aziendali.](#company-model-repository) Queste soluzioni e strumenti utilizzano o gestiscono i modelli e [le interfacce](#interface) di funzionalità del [dispositivo](#device-capability-model) nel repository.

## <a name="device-capability-model"></a>Modello di funzionalità di dispositivo

Un modello di funzionalità del dispositivo descrive un [dispositivo Plug and Play IoT](#iot-plug-and-play-device) e definisce il set di [interfacce](#interface) implementate dal dispositivo. Un modello di funzionalità del dispositivo corrisponde in genere a un dispositivo fisico, un prodotto o uno SKU. Utilizzare il linguaggio di [definizione gemello digitale](#digital-twin-definition-language) per definire un modello di funzionalità del dispositivo.

## <a name="device-certification"></a>Certificazione del dispositivo

La certificazione del dispositivo è il processo di certificazione di un [dispositivo IoT Plug and Play](#iot-plug-and-play-device) in modo che possa essere aggiunto al catalogo dei dispositivi Certified for [IoT](#certified-for-iot-device-catalog) e al modello di funzionalità del [dispositivo](#device-capability-model) e alle [interfacce](#interface) aggiunte all'archivio dei [modelli pubblici.](#public-model-repository)

## <a name="device-developer"></a>Sviluppatore di dispositivi

Uno sviluppatore di dispositivi usa un modello di [funzionalità del dispositivo,](#device-capability-model) [interfacce](#interface)e un SDK del [dispositivo Azure IoT](#azure-iot-device-sdk) per implementare il codice da eseguire in un [dispositivo IoT Plug and Play.](#iot-plug-and-play-device)

## <a name="device-modeling"></a>Modellazione del dispositivo

Uno [sviluppatore](#device-developer) di dispositivi utilizza il linguaggio di [definizione Digital Twin](#digital-twin-definition-language) per modellare le funzionalità di un dispositivo [IoT Plug and Play](#iot-plug-and-play-device). Il modello può essere condiviso utilizzando un repository di modelli. Uno sviluppatore di dispositivi può generare scheletro di codice del dispositivo dal modello. Uno sviluppatore di [soluzioni](#solution-developer) può configurare una soluzione IoT dal modello.

## <a name="device-provisioning-service"></a>Servizio di provisioning di dispositivi

[Azure IoT Central](#azure-iot-central) usa il servizio di provisioning dei dispositivi per gestire tutte le registrazioni e la connessione dei dispositivi. Per altre informazioni, vedere [Connettività dei dispositivi in Azure IoT Central.For](../iot-central/core/concepts-get-connected.md)more information, see Device connectivity in Azure IoT Central . È anche possibile usare il servizio Device Provisioning per gestire la registrazione e la connessione del dispositivo alla soluzione IoT basata su Hub IoT.You can also use the Device Provisioning Service to manage device registration and connection to your IoT Hub-based IoT solution. Per altre informazioni, vedere Provisioning di dispositivi con il servizio di provisioning dei dispositivi hub IoT di Azure.For more information, see [Provisioning devices with Azure IoT Hub Device Provisioning Service](../iot-dps/about-iot-dps.md).

## <a name="device-registration"></a>Registrazione del dispositivo

Prima che un [dispositivo IoT Plug and Play](#iot-plug-and-play-device) possa connettersi a una soluzione IoT, deve essere registrato con la soluzione. [Azure IoT Central](#azure-iot-central) usa il [servizio Device Provisioning](#device-provisioning-service) per gestire la registrazione dei dispositivi. In una soluzione IoT personalizzata è possibile registrare i dispositivi con l'hub IoT nel portale di Azure o a livello di codice.

## <a name="device-first"></a>Dispositivo-primo

[Azure IoT Central](#azure-iot-central) supporta uno scenario di registrazione e connessione basata sul dispositivo. In questo scenario, un [dispositivo Plug and Play IoT](#iot-plug-and-play-device) può connettersi a un'applicazione IoT Central senza essere registrato in anticipo. La registrazione avviene automaticamente quando un dispositivo si connette per la prima volta all'applicazione.

## <a name="digital-twin"></a>Gemello digitale

Un gemello digitale è un modello di un [dispositivo IoT Plug and Play](#iot-plug-and-play-device). Un gemello digitale è modellato utilizzando il [Digital Twin Definition Language](#digital-twin-definition-language). È possibile usare gli SDK del [dispositivo Azure IoT](#azure-iot-device-sdk) per interagire con i gemelli digitali in fase di esecuzione. Ad esempio, è possibile impostare un valore di proprietà in un gemello digitale in un dispositivo e l'SDK comunica questa modifica alla soluzione IoT nel cloud.

## <a name="digital-twin-change-events"></a>Eventi di cambiamento gemelli digitali

Quando un [dispositivo IoT Plug and Play](#iot-plug-and-play-device) è connesso a un hub [IoT](#azure-iot-hub), l'hub può utilizzare la funzionalità di routing per inviare notifiche di modifiche digitali gemelle. Ad esempio, ogni volta che il valore di una [proprietà](#properties) viene modificato in un dispositivo, l'hub IoT può inviare una notifica a un endpoint, ad esempio una coda del bus di servizio.

## <a name="digital-twin-definition-language"></a>Linguaggio di definizione Digital Twin

Un linguaggio per descrivere modelli e interfacce per [i dispositivi IoT Plug and Play](#iot-plug-and-play-device). Utilizzare [il linguaggio](https://aka.ms/DTDL) di definizione Digital Twin per descrivere le funzionalità di un [gemello digitale](#digital-twin) e abilitare la piattaforma IoT e le soluzioni IoT per sfruttare la semantica dell'entità.

## <a name="digital-twin-route"></a>Percorso gemello digitale

Una route configurata in un [hub IoT](#azure-iot-hub) per fornire eventi di [modifica gemelle digitali](#digital-twin-change-events) in un endpoint ed endpoint, ad esempio una coda del bus di servizio.

## <a name="interface"></a>Interfaccia

Un'interfaccia descrive le funzionalità correlate implementate da un [dispositivo IoT Plug and Play](#iot-plug-and-play-device) o digital [twin](#digital-twin). È possibile riutilizzare le interfacce tra diversi modelli di [funzionalità del dispositivo.](#device-capability-model)

## <a name="iot-hub-query-language"></a>Linguaggio di query di Hub IoT

Il linguaggio di query dell'hub IoT viene usato per più scopi. Ad esempio, è possibile usare la lingua per cercare [i dispositivi registrati](#device-registration) con l'hub IoT o perfezionare il comportamento di routing [gemello digitale.](#digital-twin-route)

## <a name="iot-plug-and-play-device"></a>Dispositivo Plug and Play IoT

Un dispositivo IoT Plug and Play è in genere un dispositivo di elaborazione autonomo su piccola scala che raccoglie dati o controlla altri dispositivi e che esegue software o firmware che implementa un modello di [funzionalità del dispositivo.](#device-capability-model)  Ad esempio, un dispositivo IoT Plug and Play potrebbe essere un dispositivo di monitoraggio ambientale o un controller per un sistema di irrigazione per l'agricoltura intelligente. È possibile scrivere una soluzione IoT ospitata nel cloud per comandi, controlli e ricevere dati dai dispositivi IoT Plug and Play. Il catalogo dei [dispositivi Azure Certified for IoT](#certified-for-iot-device-catalog) elenca i dispositivi Plug and Play IoT disponibili. Ogni dispositivo IoT Plug and Play nel catalogo è stato convalidato e dispone di un modello di [funzionalità del dispositivo.](#device-capability-model)

## <a name="microsoft-partner-center"></a>Centro per i partner Microsoft

[Microsoft Partner Center](https://docs.microsoft.com/partner-center/) è dove l'organizzazione gestisce la sua relazione end-to-end con Microsoft. È necessario un account del Centro per i partner Microsoft prima di poter certificare il [dispositivo IoT Plug and Play](#iot-plug-and-play-device) nel portale Azure Certified for [IoT](#azure-certified-for-iot-portal).

## <a name="model-discovery"></a>Individuazione dei modelli

Quando un [dispositivo IoT Plug and Play](#iot-plug-and-play-device) si connette a una soluzione IoT, la soluzione è in grado di individuare le funzionalità del dispositivo individuando il modello di funzionalità del [dispositivo.](#device-capability-model) Un dispositivo può inviare il proprio modello di funzionalità alla soluzione oppure la soluzione può trovare un modello di funzionalità del dispositivo in un repository di [modelli.](#model-repository)

## <a name="model-repository"></a>Repository dei modelli

Un repository di modelli archivia i modelli e [le interfacce](#interface)dei [dispositivi.](#device-capability-model) C'è un unico [repository di modelli pubblici](#public-model-repository). Le organizzazioni possono creare i propri repository di modelli organizzativi.

## <a name="model-repository-rest-api"></a>API REST del repository di modelli

Un'API per la gestione e l'interazione con i repository di modelli. Ad esempio, è possibile usare l'API per aggiungere modelli di funzionalità del [dispositivo](#device-capability-model) e cercare modelli di funzionalità.

## <a name="properties"></a>Proprietà

Le proprietà sono campi dati definiti in [un'interfaccia](#interface) che rappresentano uno stato di un gemello digitale. È possibile dichiarare le proprietà come di sola lettura o scrivibili. Le proprietà di sola lettura, ad esempio il numero di serie, vengono impostate dal codice in esecuzione sul [dispositivo Plug and Play IoT](#iot-plug-and-play-device) stesso.  Le proprietà scrivibili, ad esempio una soglia di allarme, vengono in genere impostate dalla soluzione IoT basata su cloud.

## <a name="public-model-repository"></a>Repository dei modelli pubblici

Esiste un unico repository di modelli pubblici che archivia i modelli di funzionalità dei [dispositivi](#device-capability-model) e [le interfacce](#interface) per i [dispositivi certificati.](#device-certification) Il repository dei modelli pubblici archivia anche le definizioni di [interfaccia comuni.](#common-interface)

## <a name="registration-id"></a>ID di registrazione

Un ID di registrazione identifica in modo univoco un dispositivo nel [servizio Device Provisioning.](#device-provisioning-service) Questo ID non corrisponde all'ID del dispositivo che è un identificatore univoco per un dispositivo in un [hub IoT](#azure-iot-hub).

## <a name="scope-id"></a>ID ambito

L'ambito ID ambito identifica in modo univoco un'istanza del [servizio Device Provisioning.The Scope](#device-provisioning-service) ID scope uniquely identifies a Device Provisioning Service instance.

## <a name="shared-access-signature"></a>Firma di accesso condiviso

Le firme di accesso condiviso sono un meccanismo di autenticazione basato su hash sicuri SHA-256 o URI. L'autenticazione della firma di accesso condiviso è composta da due componenti: criteri di accesso condiviso e firma di accesso condiviso (spesso denominata token). Un [dispositivo IoT Plug and Play](#iot-plug-and-play-device) utilizza una firma di accesso condiviso per l'autenticazione con un hub [IoT](#azure-iot-hub).

## <a name="solution-developer"></a>Sviluppatore di soluzioni

Uno sviluppatore di soluzioni crea il back-end della soluzione. Uno sviluppatore di soluzioni in genere lavora con le risorse di Azure, ad esempio [Hub IoT](#azure-iot-hub) e [repository](#model-repository)di modelli, oppure con [IoT Central](#azure-iot-central).

## <a name="telemetry"></a>Telemetria

I campi di telemetria definiti in [un'interfaccia](#interface) rappresentano le misure. Queste misurazioni sono in genere valori, ad esempio le letture dei sensori inviati dal [dispositivo IoT Plug and Play](#iot-plug-and-play-device) come flusso di dati.

## <a name="visual-studio-code"></a>Codice di Visual Studio

Il codice di Visual Studio è un editor di codice moderno disponibile per più piattaforme. Le estensioni, ad esempio quelle nel pacchetto degli strumenti [IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) di Azure, consentono di personalizzare l'editor per supportare un'ampia gamma di scenari di sviluppo.
