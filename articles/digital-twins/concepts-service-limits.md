---
title: Limiti del servizio durante l'anteprima pubblica di Gemelli digitali di Azure | Microsoft Docs
description: Informazioni sui limiti del servizio durante l'anteprima pubblica di Gemelli digitali di Azure
author: dwalthermsft
manager: deshner
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: dwalthermsft
ms.openlocfilehash: aa5f6053bf1c98d2b84c02617da30f5d856ed3fc
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2018
ms.locfileid: "49323790"
---
# <a name="public-preview-service-limits"></a>Limiti del servizio durante l'anteprima pubblica

Durante l'anteprima pubblica, a Gemelli digitali di Azure verranno applicati limiti temporanei relativi a sottoscrizioni, istanze e quantità, descritti di seguito.

Questi vincoli sono stati definiti per semplificare l'apprendimento del nuovo servizio e delle sue numerose funzionalità.

> [!NOTE]
> Questi limiti verranno aumentati e/o rimossi a partire dalla disponibilità generale.

## <a name="per-subscription-limits"></a>Limiti relativi alle sottoscrizioni

Durante l'anteprima pubblica, ogni sottoscrizione di Azure può creare o eseguire esattamente una sola istanza di Gemelli digitali di Azure per volta.

> [!TIP]
> L'eliminazione dell'istanza permetterà di crearne una nuova.

## <a name="per-instance-limits"></a>Limiti relativi alle istanze

A sua volta, ogni istanza di Gemelli digitali di Azure può avere:

- Una risorsa `IoTHub`
- Un endpoint `EventHub` per il tipo di evento DeviceMessage
- Fino a tre endpoint `EventHub`, `ServiceBus` o `EventGrid` con tipo di evento `SensorChange`, `SpaceChange`, `TopologyOperation` o `UdfCustom`

## <a name="management-api-limits"></a>Limiti relativi all'API di gestione

I limiti per il numero di richieste per l'API di gestione sono i seguenti:

- 100 richieste al secondo all'API di gestione
- Una singola query sull'API di gestione può restituire fino a 1000 oggetti

> [!IMPORTANT]
> Se si supera il limite di 1000 oggetti, si riceverà un errore e sarà necessario semplificare la query.

## <a name="udf-rate-limits"></a>Limiti relativi al numero di funzioni definite dall'utente

I limiti seguenti definiscono il numero totale di tutte le chiamate delle funzioni definite dall'utente inviate all'istanza di Gemelli digitali di Azure:

- 400 chiamate alla libreria client al secondo
- 100 chiamate SendNotification al secondo

> [!NOTE]
> Le operazioni seguenti possono generare l'applicazione temporanea di altri limiti:
> - Modifiche ai metadati di oggetti della topologia
> - Aggiornamenti alle definizioni delle funzioni definite dall'utente
> - Dispositivi che inviano dati di telemetria per la prima volta

## <a name="device-telemetry-limits"></a>Limiti relativi alla telemetria dei dispositivi

I limiti indicati di seguito si applicano al numero totale di tutti i messaggi che i dispositivi possono inviare all'istanza di Gemelli digitali di Azure:

- 100 messaggi al secondo

## <a name="next-steps"></a>Passaggi successivi

Per provare un esempio di Gemelli digitali di Azure, vedere [Guida introduttiva per la ricerca di stanze disponibili](./quickstart-view-occupancy-dotnet.md).