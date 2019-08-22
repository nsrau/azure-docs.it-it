---
title: Limiti e quote Plug and Play anteprima | Microsoft Docs
description: Comprendere i limiti, le quote e le limitazioni che si applicano quando si usa la Plug and Play di anteprima.
author: miagdp
ms.author: miag
ms.date: 08/01/2019
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 2df8a8820422a22b0512e24c4b052377cb0e61e0
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69879565"
---
# <a name="iot-plug-and-play-preview-limits-quotas-and-throttles"></a>Limiti, quote e limitazioni dell'anteprima Plug and Play

In questo articolo vengono illustrati i limiti, le quote e le limitazioni specifici Plug and Play per l'anteprima pubblica. Sono disponibili [quote e limitazioni dell'hub](../iot-hub/iot-hub-devguide-quotas-throttling.md) delle cose esistenti che si applicano anche.

## <a name="iot-hub"></a>Hub IoT

Per l'anteprima pubblica, i limiti e le quote seguenti si applicano a un hub Internet delle cose:

| Limiti, restrizioni e limitazioni | Valore | Note |
|-----|-----|-----|
| Numero di modelli di funzionalità del dispositivo (DCMs) o interfacce che possono essere registrate per hub | 1500 ||
| Numero massimo di interfacce che è possibile registrare per ogni dispositivo | 40 ||
| Numero massimo di DCMs che possono essere registrati per ogni dispositivo | 1 ||
| Dimensioni massime del file di interfaccia/DCM | 512 caratteri ||
| Dimensione massima del nome di un'interfaccia | 256 caratteri ||
| Dimensione massima del nome di una proprietà  | 64 byte, 7 livelli di profondità (e il primo livello è riservato per `$iotin`) | Caratteri consentiti: a-z, A-Z, 0-9 (non come primo carattere) e carattere di sottolineatura. |
| Dimensioni massime di un valore di proprietà | 512 byte ||
| Dimensione massima del nome di un comando | 100 byte ||
| Dimensioni del dispositivo gemello | Uguale ai [limiti dell'hub](../iot-hub/iot-hub-devguide-device-twins.md#device-twin-size) Internet ||
| Chiamate API di risoluzione tra SKU (indipendentemente dalle unità) | 100 richieste/secondo ||

## <a name="model-repository"></a>Repository di modelli

Per l'anteprima pubblica, i limiti e le quote seguenti si applicano a un repository di modelli:

| Limiti, restrizioni e limitazioni| Valore |
|-----|-----|
| Numero di repository del modello aziendale per ogni tenant di Azure Active Directory | 1 |
| Numero di chiavi di autorizzazione per repository di modelli | 10  |
| Numero di modelli (DCMs o Interfaces) per ogni repository di modelli aziendali| 1500  |
| Numero di modelli (DCMs o Interfaces) nel repository del modello pubblico per ogni tenant di Azure Active Directory| 1500  |
| Numero di DCMs o interfacce da eliminare in un repository del modello aziendale | 10 query al secondo (query al secondo)|
| Numero di repository del modello creati/aggiornati da un tenant| 1 QUERY AL SECONDO |
| Numero di chiavi di autorizzazione create/aggiornate/eliminate in un repository di modelli | 1 QUERY AL SECONDO|
| Numero di DCMs da creare in un repository del modello aziendale | 10 QUERY AL SECONDO |
| Numero di interfacce da creare in un repository del modello aziendale | 10 QUERY AL SECONDO|
| Numero di DCMs da creare nel repository del modello pubblico | 10 QUERY AL SECONDO|
| Numero di interfacce create nel repository del modello pubblico | 10 QUERY AL SECONDO|

## <a name="parser-library"></a>Libreria parser

La libreria del parser segue i limiti che si applicano al [linguaggio Digital Twin Definition](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL).

## <a name="next-steps"></a>Passaggi successivi

Un passaggio successivo suggerito consiste nell'apprendere come [connettersi e interagire con un plug and Play dispositivo](./howto-develop-solution.md).
