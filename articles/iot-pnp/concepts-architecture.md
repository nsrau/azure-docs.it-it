---
title: Architettura Plug and Play Microsoft Docs
description: In qualità di generatore di soluzioni, comprendere gli elementi chiave dell'architettura dei Plug and Play.
author: ridomin
ms.author: rmpablos
ms.date: 07/06/2020
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: f2d9407d9c7172839027e2650486695244f67889
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87352218"
---
# <a name="iot-plug-and-play-preview-architecture"></a>Architettura dell'anteprima Plug and Play

Il Plug and Play anteprima consente ai generatori di soluzioni di integrare Smart Device con le proprie soluzioni senza alcuna configurazione manuale. Il nucleo del Plug and Play Internet è un _modello_ di dispositivo che descrive le funzionalità di un dispositivo per un'applicazione con plug and Play Internet delle cose. Questo modello è strutturato come un set di interfacce che definiscono:

- _Proprietà_ che rappresentano lo stato di sola lettura o scrivibile di un dispositivo o di un'altra entità. Ad esempio, un numero di serie del dispositivo può essere una proprietà di sola lettura e una temperatura di destinazione in un termostato può essere una proprietà scrivibile.
- _Telemetria_ che rappresenta i dati emessi da un dispositivo, se i dati sono un flusso normale di letture di sensori, un errore occasionale o un messaggio informativo.
- _Comandi_ che descrivono una funzione o un'operazione che può essere eseguita su un dispositivo. Ad esempio, un comando può riavviare un gateway o scattare una foto usando una fotocamera remota.

Ogni modello e interfaccia ha un ID univoco.

Il diagramma seguente Mostra gli elementi chiave di una soluzione Plug and Play Internet:

:::image type="content" source="media/concepts-architecture/pnp-architecture.png" alt-text="Architettura Plug and Play":::

## <a name="model-repository"></a>Repository modelli

Il [repository del modello](./concepts-model-repository.md) è un archivio per le definizioni di modello e interfaccia. I modelli e le interfacce vengono definiti utilizzando il [linguaggio DTDL (Digital Gemini Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl).

L'interfaccia utente Web consente di gestire i modelli e le interfacce.

Il repository del modello usa il controllo degli accessi in base al ruolo per consentire l'accesso alle definizioni di interfaccia.

## <a name="devices"></a>Dispositivi

Un generatore di dispositivi implementa il codice per l'esecuzione su un sacco Smart Device usando uno degli [SDK per dispositivi Azure](./libraries-sdks.md). Gli SDK per dispositivi consentono al generatore di dispositivi di:

- Connettersi in modo sicuro a un hub Internet.
- Registrare il dispositivo con l'hub Internet e annunciare l'ID del modello che identifica la raccolta di interfacce implementate dal dispositivo.
- Aggiornare le proprietà definite nelle interfacce DTDL implementate dal dispositivo. Queste proprietà vengono implementate usando i dispositivi gemelli digitali che gestiscono la sincronizzazione con l'hub Internet.
- Aggiungere gestori di comandi per i comandi definiti nelle interfacce DTDL implementate dal dispositivo.
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
- [Processo di individuazione del modello](./concepts-model-discovery.md)
- [Sviluppo per Plug and Play](./concepts-developer-guide.md)
