---
title: Limiti e quote Plug and Play | Microsoft Docs
description: Comprendere i limiti, le quote e le limitazioni che si applicano quando si usano le Plug and Play.
author: prashmo
ms.author: prashmo
ms.date: 07/21/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: d965d9cb8b87ce0b67f4fe0c07b660fdfd69cc07
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91577986"
---
# <a name="iot-plug-and-play-limits-quotas-and-throttles"></a>Limiti, quote e limitazioni Plug and Play

Questo articolo illustra i limiti, le quote e la limitazione specifici Plug and Play. Sono disponibili [quote e limitazioni dell'hub](../iot-hub/iot-hub-devguide-quotas-throttling.md) delle cose esistenti che si applicano anche.

## <a name="iot-hub"></a>Hub IoT

I limiti e le quote seguenti si applicano a un hub Internet delle cose:

| Limiti, restrizioni e limitazioni | Valore | Note |
|-----|-----|-----|
| Numero di interfacce che è possibile registrare per hub | 1500 ||
| Dimensione massima del nome di un componente | 1-64 caratteri | Caratteri consentiti: a-z, A-Z, 0-9 (non come primo carattere) e carattere di sottolineatura (non come primo o ultimo carattere). |
| Dimensione massima del nome di una proprietà | 1-64 caratteri | Caratteri consentiti: a-z, A-Z, 0-9 (non come primo carattere) e carattere di sottolineatura (non come primo o ultimo carattere). |
| Dimensioni massime di un valore di proprietà | Uguale alla [Proprietà](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md#property) del linguaggio di definizione delle gemelle digitali | 5 livelli di profondità e non possono essere matrici o schemi complessi che contengono una matrice |
| Dimensione massima del nome di un comando | 1-64 caratteri | Caratteri consentiti: a-z, A-Z, 0-9 (non come primo carattere) e carattere di sottolineatura (non come primo o ultimo carattere).|
| Dimensioni del dispositivo gemello | Uguale ai [limiti dell'hub](../iot-hub/iot-hub-devguide-device-twins.md#device-twin-size) Internet ||

## <a name="parser-library"></a>Libreria parser

La libreria del parser segue i limiti che si applicano al [linguaggio di definizione dei gemelli digitali](https://github.com/Azure/opendigitaltwins-dtdl).

## <a name="next-steps"></a>Passaggi successivi

Il passaggio successivo suggerito consiste nel rivedere l' [architettura plug and Play](concepts-architecture.md).
