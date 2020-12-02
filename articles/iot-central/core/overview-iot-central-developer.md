---
title: Sviluppo di dispositivi per Azure IoT Central | Microsoft Docs
description: Azure IoT Central è una piattaforma di applicazioni IoT che semplifica la creazione di soluzioni Internet. Questo articolo contiene una panoramica dello sviluppo di dispositivi per la connessione all'applicazione IoT Central. I dispositivi usano la telemetria per inviare i dati in streaming e le proprietà per segnalare lo stato del dispositivo. IoT Central può impostare lo stato del dispositivo usando le proprietà scrivibili e chiamare i comandi in un dispositivo.
author: dominicbetts
ms.author: dobett
ms.date: 05/05/2020
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom:
- mvc
- device-developer
ms.openlocfilehash: 82818c8db326889079948cd2b32b2ed0be6ab50d
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94990755"
---
# <a name="iot-central-device-development-overview"></a>Panoramica sullo sviluppo di dispositivi IoT Central

*Questo articolo si applica agli sviluppatori di dispositivi.*

Un'applicazione IoT Central consente di monitorare e gestire milioni di dispositivi durante il relativo ciclo di vita. Questa panoramica è destinata agli sviluppatori di dispositivi che implementano il codice da eseguire nei dispositivi che si connettono a IoT Central.

I dispositivi interagiscono con un'applicazione IoT Central tramite le primitive seguenti:

- _Telemetria_, ovvero i dati che un dispositivo invia a IoT Central. Può trattarsi ad esempio di un flusso di valori di temperatura da un sensore a bordo.
- _Proprietà_, ovvero valori di stato che un dispositivo segnala a IoT Central. Può trattarsi ad esempio della versione corrente del firmware del dispositivo. Sono anche disponibili proprietà scrivibili che IoT Central può aggiornare nel dispositivo, ad esempio un obiettivo di temperatura.
- I _comandi_ vengono chiamati da IoT Central per controllare il comportamento di un dispositivo. L'applicazione IoT Central, ad esempio, può chiamare un comando per riavviare un dispositivo.

Un compilatore di soluzioni è responsabile della configurazione di dashboard e visualizzazioni nell'interfaccia utente Web di IoT Central per visualizzare i dati di telemetria, gestire le proprietà e chiamare i comandi.

## <a name="types-of-device"></a>Tipi di dispositivo

Le sezioni seguenti descrivono i tipi principali di dispositivo che è possibile connettere a un'applicazione IoT Central:

### <a name="standalone-device"></a>Dispositivo autonomo

Un dispositivo autonomo si connette direttamente a IoT Central. Un dispositivo autonomo invia in genere i propri dati di telemetria o quelli dai sensori connessi all'applicazione IoT Central. I dispositivi autonomi possono anche segnalare i valori delle proprietà, ricevere i valori delle proprietà scrivibili e rispondere ai comandi.

### <a name="gateway-device"></a>Dispositivo gateway

Un dispositivo gateway gestisce uno o più dispositivi downstream che si connettono all'applicazione IoT Central. Usare IoT Central per configurare le relazioni tra i dispositivi downstream e il dispositivo gateway. Per altre informazioni, vedere [Definire un nuovo tipo di dispositivo gateway IoT nell'applicazione Azure IoT Central](./tutorial-define-gateway-device-type.md).

### <a name="edge-device"></a>Dispositivo perimetrale

Un dispositivo perimetrale si connette direttamente a IoT Central, ma funge da intermediario per altri dispositivi noti come _dispositivi foglia_. Un dispositivo perimetrale si trova in genere vicino ai dispositivi foglia per i quali funge da intermediario. Gli scenari in cui vengono usati i dispositivi Edge includono i seguenti:

- Abilitazione dei dispositivi che non possono connettersi direttamente a IoT Central per eseguire la connessione tramite il dispositivo perimetrale. Un dispositivo foglia può usare la modalità Bluetooth per connettersi al dispositivo perimetrale, che quindi si connette a IoT Central tramite Internet.
- Aggregazione dei di telemetria prima che vengano inviati a IoT Central. Questo approccio può aiutare a ridurre i costi di invio dei dati a IoT Central.
- Controllo locale dei dispositivi foglia per evitare la latenza associata alla connessione a IoT Central tramite Internet.

Un dispositivo perimetrale può anche inviare i propri dati di telemetria, segnalarne le proprietà e rispondere ai comandi e agli aggiornamenti delle proprietà scrivibili.

IoT Central visualizza solo il dispositivo perimetrale, non i dispositivi foglia connessi.

Per altre informazioni, vedere [Aggiungere un dispositivo Azure IoT Edge nell'applicazione Azure IoT Central](./tutorial-add-edge-as-leaf-device.md).

## <a name="connect-a-device"></a>Collegare un dispositivo

Azure IoT Central usa il [servizio Device Provisioning in hub IoT di Azure](../../iot-dps/about-iot-dps.md) per gestire la registrazione e la connessione di tutti i dispositivi.

Con il servizio Device Provisioning:

- IoT Central supporta l'onboarding e la connessione dei dispositivi su larga scala.
- È possibile generare credenziali dei dispositivi e configurarli offline senza registrarli tramite l'interfaccia utente di IoT Central.
- È possibile usare ID dispositivo personalizzati per registrare i dispositivi in IoT Central. L'uso di ID dispositivo personalizzati semplifica l'integrazione con i sistemi back-office esistenti.
- È disponibile un singolo modo coerente per connettere i dispositivi a IoT Central.

Per altre informazioni, vedere [Connettersi ad Azure IoT Central](./concepts-get-connected.md).

### <a name="security"></a>Sicurezza

La connessione tra un dispositivo e l'applicazione IoT Central è protetta tramite [firme di accesso condiviso](./concepts-get-connected.md#sas-group-enrollment) o [certificati X.509](./concepts-get-connected.md#x509-group-enrollment) standard del settore.

### <a name="communication-protocols"></a>Protocolli di comunicazione

I protocolli di comunicazione che un dispositivo può usare per connettersi a IoT Central includono MQTT, AMQP e HTTPS. Internamente, IoT Central usa un hub IoT per abilitare la connettività dei dispositivi. Per altre informazioni sui protocolli di comunicazione supportati dall'hub IoT per la connettività dei dispositivi, vedere [Scegliere un protocollo di comunicazione](../../iot-hub/iot-hub-devguide-protocols.md).

## <a name="implement-the-device"></a>Implementare il dispositivo

Un modello di dispositivo di IoT Central include un _modello_ che specifica i comportamenti che dovranno essere implementati da un dispositivo di questo tipo. I comportamenti includono dati di telemetria, proprietà e comandi.

> [!TIP]
> È possibile esportare il modello da IoT Central come file JSON [DTDL (Digital Twins Definition Language) v2](https://github.com/Azure/opendigitaltwins-dtdl).

Ogni modello ha un identificatore _DTMI (Device Twin Model Identifier)_ univoco, ad esempio `dtmi:com:example:Thermostat;1`. Quando un dispositivo si connette a IoT Central, invia l'identificatore DTMI del modello che implementa. IoT Central può quindi associare il modello di dispositivo corretto con il dispositivo.

[Plug and Play IoT](../../iot-pnp/overview-iot-plug-and-play.md) definisce una serie di convenzioni che il dispositivo deve seguire quando implementa un modello DTDL.

Gli [Azure IoT SDK per dispositivi](#languages-and-sdks) includono il supporto per le convenzioni di Plug and Play IoT.

### <a name="device-model"></a>Modello del dispositivo

Un modello di dispositivo viene definito tramite [DTDL](https://github.com/Azure/opendigitaltwins-dtdl). Questo linguaggio consente di definire:

- I dati di telemetria inviati dal dispositivo. La definizione include il nome e il tipo di dati di telemetria. Ad esempio, un dispositivo invia dati di telemetria sulla temperatura come valore double.
- Le proprietà che il dispositivo segnala a IoT Central. Una definizione di proprietà ne include il nome e il tipo di dati. Ad esempio, un dispositivo segnala lo stato di una valvola come valore booleano.
- Le proprietà che il dispositivo può ricevere da IoT Central. Facoltativamente, è possibile contrassegnare una proprietà come scrivibile. Ad esempio, IoT Central invia una temperatura obiettivo come valore double a un dispositivo.
- I comandi a cui il dispositivo risponde. La definizione include il nome del comando, oltre ai nomi e ai tipi di dati di eventuali parametri. Ad esempio, un dispositivo risponde a un comando di riavvio che specifica quanti secondi aspettare prima del riavvio.

Un modello DTDL può essere _senza componenti_ o a _più componenti_:

- Modello senza componenti: un modello semplice non usa componenti incorporati o a cascata. Tutti i dati di telemetria, le proprietà e i comandi vengono definiti come singolo _componente predefinito_. Per un esempio, vedere il modello di [Termostato](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json).
- Modello a più componenti: un modello più complesso che include due o più componenti. Questi componenti includono un singolo componente predefinito e uno o più componenti annidati aggiuntivi. Per un esempio, vedere il modello di [Temperature Controller](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json).

Per altre informazioni, vedere [Componenti Plug and Play IoT nei modelli](../../iot-pnp/concepts-components.md)

### <a name="conventions"></a>Convenzioni

Un dispositivo deve seguire le convenzioni di Plug and Play IoT quando scambia dati con IoT Central. Le convenzioni includono:

- Inviare l'identificatore DTMI quando il dispositivo si connette a IoT Central.
- Inviare payload e metadati JSON correttamente formattati a IoT Central.
- Rispondere correttamente alle proprietà e ai comandi scrivibili inviati da IoT Central.
- Seguire le convenzioni di denominazione per i comandi dei componenti.

> [!NOTE]
> Attualmente, IoT Central non supporta completamente i dati DTDL di tipo **matrice** e **geospaziali**.

Per altre informazioni sul formato dei messaggi JSON che un dispositivo scambia con IoT Central, vedere [Payload di telemetria, proprietà e comandi](concepts-telemetry-properties-commands.md).

Per altre informazioni sulle convenzioni di Plug and Play IoT, vedere [Convenzioni di Plug and Play IoT](../../iot-pnp/concepts-convention.md).

### <a name="device-sdks"></a>SDK del dispositivo

Usare uno degli [Azure IoT SDK per dispositivi](#languages-and-sdks) per implementare il comportamento del dispositivo. Il codice deve:

- Registrare il dispositivo con DPM e usare le informazioni di DPM per connettersi all'hub IoT interno nell'applicazione IoT Central.
- Annunciare l'identificatore DTMI del modello implementato dal dispositivo.
- Inviare dati di telemetria nel formato specificato dal modello di dispositivo. IoT Central usa il modello nel modello di dispositivo per determinare come usare i dati di telemetria per le visualizzazioni e l'analisi.
- Sincronizzare i valori delle proprietà tra il dispositivo e IoT Central. Il modello specifica i nomi delle proprietà e i tipi di dati in modo che IoT Central possa visualizzare le informazioni.
- Implementare i gestori per i comandi specificati nel modello. Il modello specifica i nomi e i parametri dei comandi che il dispositivo deve usare.

Per altre informazioni sul ruolo dei modelli di dispositivo, vedere [Che cosa sono i modelli di dispositivo?](./concepts-device-templates.md).

Per il codice di esempio, vedere [Creare e connettere un'applicazione client Node.js](./tutorial-connect-device-nodejs.md) oppure [Creare e connettere un'applicazione client Python](./tutorial-connect-device-python.md).

### <a name="languages-and-sdks"></a>Linguaggi e SDK

Per altre informazioni sui linguaggi e sugli SDK supportati, vedere [Comprendere e usare gli SDK per l'hub IoT di Azure](../../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks).

## <a name="next-steps"></a>Passaggi successivi

Per gli sviluppatori di dispositivi che desiderano approfondire il codice, il passaggio successivo suggerito consiste nel [Creare e connettere un'applicazione client all'applicazione Azure IoT Central](./tutorial-connect-device-nodejs.md).

Per altre informazioni sull'uso di IoT Central, vedere le guide introduttive, a partire da [Creare un'applicazione Azure IoT Central](./quick-deploy-iot-central.md).
