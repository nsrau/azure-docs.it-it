---
title: Architettura Plug and Play Microsoft Docs
description: In qualità di generatore di soluzioni, comprendere gli elementi chiave dell'architettura dei Plug and Play.
author: ridomin
ms.author: rmpablos
ms.date: 09/15/2020
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: a87617e1cc6910b4c807aa90f413efe94b6e6953
ms.sourcegitcommit: 03c0a713f602e671b278f5a6101c54c75d87658d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2020
ms.locfileid: "94920305"
---
# <a name="iot-plug-and-play-architecture"></a>Architettura di Plug and Play IoT

Plug and Play IoT Plug and Play consente agli sviluppatori di soluzioni di integrare dispositivi intelligenti con le rispettive soluzioni senza configurazione manuale. Il nucleo del Plug and Play Internet è un _modello_ di dispositivo che descrive le funzionalità di un dispositivo per un'applicazione con plug and Play Internet delle cose. Questo modello è strutturato come un set di interfacce che definiscono:

- _Proprietà_ che rappresentano lo stato di sola lettura e di scrittura di un dispositivo o di un'altra entità. Ad esempio, il numero di serie del dispositivo può essere una proprietà di sola lettura, mentre la temperatura di destinazione di un termostato può essere una proprietà scrivibile.
- _Dati di telemetria_ generati da un dispositivo, siano essi un normale flusso di letture di sensori, un errore occasionale o un messaggio informativo.
- _Comandi_ che descrivono una funzione o un'operazione che può essere eseguita su un dispositivo. Ad esempio, un comando può riavviare un gateway o scattare una foto usando una fotocamera remota.

Ogni modello e interfaccia ha un ID univoco.

Il diagramma seguente Mostra gli elementi chiave di una soluzione Plug and Play Internet:

:::image type="content" source="media/concepts-architecture/pnp-architecture.png" alt-text="Architettura di Plug and Play IoT":::

## <a name="model-repository"></a>Repository modelli

Il [repository del modello](./concepts-model-repository.md) è un archivio per le definizioni di modello e interfaccia. I modelli e le interfacce vengono definiti utilizzando il [linguaggio DTDL (Digital Gemini Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl).

L'interfaccia utente Web consente di gestire i modelli e le interfacce.

Il repository del modello dispone di controlli di accesso in base al ruolo predefiniti che consentono di limitare l'accesso alle definizioni di interfaccia.

## <a name="devices"></a>Dispositivi

Un generatore di dispositivi implementa il codice per l'esecuzione su un sacco Smart Device usando uno degli [SDK per dispositivi Azure](./libraries-sdks.md). Gli SDK per dispositivi consentono al generatore di dispositivi di:

- Connettersi in modo sicuro a un hub Internet.
- Registrare il dispositivo con l'hub Internet e annunciare l'ID del modello che identifica la raccolta di interfacce DTDL implementate dal dispositivo.
- Sincronizzare le proprietà definite nelle interfacce DTDL tra il dispositivo e l'hub Internet.
- Aggiungere gestori di comandi per i comandi definiti nelle interfacce DTDL.
- Inviare dati di telemetria all'hub Internet.

## <a name="iot-edge-gateway"></a>Gateway IoT Edge

Un gateway IoT Edge funge da intermediario per connettere le cose Plug and Play i dispositivi che non possono connettersi direttamente a un hub Internet. Per altre informazioni, vedere [come è possibile usare un dispositivo IOT Edge come gateway](../iot-edge/iot-edge-as-gateway.md).

## <a name="iot-edge-modules"></a>Moduli di IoT Edge

Un _modulo IOT Edge_ consente di distribuire e gestire la logica di business sul perimetro. I moduli di Azure IoT Edge sono l'unità più piccola di calcolo gestita da IoT Edge e possono contenere i servizi di Azure, ad esempio Analisi di flusso di Azure, o il codice specifico per la soluzione.

Il _IOT Edge Hub_ è uno dei moduli che costituiscono il runtime di Azure IOT Edge. Opera come un proxy locale per l'hub IoT esponendo gli stessi endpoint del protocollo dell'hub IoT. Questa coerenza implica che i client, siano essi dispositivi o moduli, possono connettersi al runtime di IoT Edge esattamente come si connetterebbero all'hub IoT.

Gli SDK per dispositivi aiutano un generatore di moduli a:

- Usare l'hub IoT Edge per connettersi in modo sicuro all'hub Internet delle cose.
- Registrare il modulo con l'hub Internet e annunciare l'ID del modello che identifica la raccolta di interfacce DTDL implementate dal dispositivo.
- Sincronizzare le proprietà definite nelle interfacce DTDL tra il dispositivo e l'hub Internet.
- Aggiungere gestori di comandi per i comandi definiti nelle interfacce DTDL.
- Inviare dati di telemetria all'hub Internet.

## <a name="iot-hub"></a>Hub IoT

L' [Hub](../iot-hub/about-iot-hub.md) Internet è un servizio ospitato nel cloud che funge da Hub messaggi centrale per la comunicazione bidirezionale tra la soluzione Internet delle cose e i dispositivi gestiti.

Un hub Internet delle cose:

- Rende l'ID modello implementato da un dispositivo disponibile per una soluzione back-end.
- Gestisce il dispositivo gemello digitale associato a ogni dispositivo Plug and Play connesso all'hub.
- Trasmette i flussi di telemetria ad altri servizi per l'elaborazione o l'archiviazione.
- Instrada gli eventi di modifica di dispositivi gemelli digitali ad altri servizi per abilitare il monitoraggio dei dispositivi.

## <a name="backend-solution"></a>Soluzione back-end

Una soluzione back-end monitora e controlla i dispositivi connessi interagendo con i dispositivi gemelli digitali nell'hub Internet delle cose. Usare uno degli SDK del servizio per implementare la soluzione back-end. Per comprendere le funzionalità di un dispositivo connesso, il back-end della soluzione:

1. Recupera l'ID del modello registrato con l'hub Internet.
1. Usa l'ID modello per recuperare le definizioni di interfaccia da qualsiasi repository del modello.
1. Usa il parser del modello per estrarre informazioni dalle definizioni di interfaccia.

La soluzione back-end può usare le informazioni dalle definizioni di interfaccia per:

- Leggere i valori delle proprietà segnalati dai dispositivi.
- Aggiornare le proprietà scrivibili in un dispositivo.
- Chiamare i comandi implementati da un dispositivo.
- Comprendere il formato dei dati di telemetria inviati da un dispositivo.

## <a name="next-steps"></a>Passaggi successivi

Ora che si dispone di una panoramica dell'architettura di un Plug and Play soluzione, i passaggi successivi sono ulteriori informazioni su:

- [Repository del modello](./concepts-model-repository.md)
- [Integrazione di modelli digitali gemelli](./concepts-model-discovery.md)
- [Sviluppo per Plug and Play](./concepts-developer-guide-device-csharp.md)
