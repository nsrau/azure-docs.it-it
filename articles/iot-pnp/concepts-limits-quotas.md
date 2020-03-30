---
title: Limiti e quote IoT Plug and Play Preview Documenti Microsoft
description: Comprendere i limiti, le quote e le limitazioni applicabili quando si utilizza Plug and Play Preview.Understand the limits, quotas, and throttling that apply when you use IoT Plug and Play Preview.
author: miagdp
ms.author: miag
ms.date: 12/26/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 48ecaaba6d956efd9da75d0582fa06d231cb3f80
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75531378"
---
# <a name="iot-plug-and-play-preview-limits-quotas-and-throttles"></a>Limiti, quote e limitazioni di Plug and Play Preview IoT

Questo articolo illustra i limiti, le quote e le limitazioni specifici di IoT Plug e Play che si applicano nell'anteprima pubblica. Esistono [quote e limitazioni dell'hub IoT](../iot-hub/iot-hub-devguide-quotas-throttling.md) esistenti che si applicano anche.

## <a name="iot-hub"></a>Hub IoT

Per l'anteprima pubblica, i limiti e le quote seguenti si applicano a un hub IoT:

| Limiti, restrizioni e limitazioni | valore | Note |
|-----|-----|-----|
| Numero di modelli di funzionalità del dispositivo (DPM) o interfacce che possono essere registrati per hub | 1500 ||
| Numero massimo di interfacce che possono essere registrate per dispositivo | 40 ||
| Numero massimo di MMM che possono essere registrati per dispositivo | 1 ||
| Dimensione massima del file interfaccia/DCM | 512 caratteri ||
| Dimensione massima del nome di un'interfaccia | 256 caratteri ||
| Dimensione massima del nome di una proprietà  | 64 byte, 7 livelli di profondità (e il primo livello è riservato per `$iotin`) | Caratteri consentiti: a-z, A-z, 0-9 (non come primo carattere) e sottolineatura. |
| Dimensione massima del valore di una proprietà | 512 byte ||
| Dimensione massima del nome di un comando | 100 byte ||
| Dimensioni del dispositivo gemello | Uguale [ai limiti dell'hub IoT](../iot-hub/iot-hub-devguide-device-twins.md#device-twin-size) ||
| Chiamate API di risoluzione tra SKU (indipendentemente dalle unità)Resolution API calls across SKU (regardless of units) | 100 richiesta/secondo ||

## <a name="model-repository"></a>Repository dei modelli

Per l'anteprima pubblica, i limiti e le quote seguenti si applicano a un repository di modelli:

| Limiti, restrizioni e limitazioni| valore |
|-----|-----|
| Numero di repository di modelli aziendali per tenant di Azure Active Directory | 1 |
| Numero di chiavi di autorizzazione per repository di modelli | 10  |
| Numero di modelli (DPM o interfacce) per repository di modelli aziendali| 1500  |
| Numero di modelli (DPM o interfacce) nel repository dei modelli pubblici per ogni tenant di Azure Active Directory| 1500  |
| Numero di DPM o interfacce eliminate in un repository di modelli aziendali | 10 query al secondo (QPS)|
| Numero di archivi di modelli creati/aggiornati da un tenant| 1 QPS |
| Numero di chiavi di autorizzazione create/aggiornate/eliminate in un repository di modelli | 1 QPS|
| Numero di DPM creati in un repository di modelli aziendali | 10 QPS |
| Numero di interfacce create in un repository di modelli aziendali | 10 QPS|
| Numero di DPM creati nell'archivio modelli pubblici | 10 QPS|
| Numero di interfacce create nel repository dei modelli pubblici | 10 QPS|

## <a name="parser-library"></a>Libreria parser

La libreria del parser segue i limiti che si applicano al [Digital Twin Definition Language](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL).

## <a name="next-steps"></a>Passaggi successivi

Un passaggio successivo consigliato consiste nell'imparare a [connettersi e interagire con un dispositivo IoT Plug and Play](./howto-develop-solution.md).
