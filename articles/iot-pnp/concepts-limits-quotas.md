---
title: Limiti e quote Plug and Play anteprima | Microsoft Docs
description: Comprendere i limiti, le quote e le limitazioni che si applicano quando si usa la Plug and Play di anteprima.
author: prashmo
ms.author: prashmo
ms.date: 07/21/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 5c4377120f61792b580225a22b9f5ff51b5e1b64
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87337399"
---
# <a name="iot-plug-and-play-preview-limits-quotas-and-throttles"></a>Limiti, quote e limitazioni dell'anteprima Plug and Play

In questo articolo vengono illustrati i limiti, le quote e le limitazioni specifici Plug and Play per l'anteprima pubblica. Sono disponibili [quote e limitazioni dell'hub](../iot-hub/iot-hub-devguide-quotas-throttling.md) delle cose esistenti che si applicano anche.

## <a name="iot-hub"></a>Hub IoT

Per l'anteprima pubblica, i limiti e le quote seguenti si applicano a un hub Internet delle cose:

| Limiti, restrizioni e limitazioni | valore | Note |
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
