---
title: Limiti del servizio di anteprima pubblica-gemelli di Azure Digital | Microsoft Docs
description: Informazioni sul servizio di anteprima pubblica, sulla sottoscrizione, sull'istanza e sui limiti di velocità per i dispositivi gemelli digitali di Azure.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/17/2020
ms.openlocfilehash: 5e323d8faa19ceb0712aa6183df17740ce2a0a1d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79370381"
---
# <a name="public-preview-service-limits"></a>Limiti del servizio durante l'anteprima pubblica

[!INCLUDE [digital-twins-preview-limit-alert](../../includes/digital-twins-preview-limit-alert.md)]

Durante l'anteprima pubblica, i dispositivi gemelli digitali di Azure hanno i limiti di sottoscrizione, istanza e frequenza temporanei seguenti per i clienti esistenti. Si tratta di vincoli che consentono di semplificare l'apprendimento del nuovo servizio e delle sue numerose funzionalità, che verranno aumentate o rimosse da disponibilità generale (GA).

## <a name="per-subscription-limits"></a>Limiti relativi alle sottoscrizioni

Durante l'anteprima pubblica, ogni sottoscrizione di Azure può creare o eseguire una sola istanza di Gemelli digitali di Azure per volta. Se si elimina l'istanza, sarà possibile crearne una nuova.

## <a name="per-instance-limits"></a>Limiti relativi alle istanze

A sua volta, ogni istanza di Gemelli digitali di Azure può avere:

- Esattamente una risorsa **IoTHub** incorporata che viene creata automaticamente durante il provisioning del servizio.
- Esattamente un endpoint **EventHub** per il tipo di evento **DeviceMessage**.
- Fino a tre endpoint **EventHub**, **ServiceBus** o **EventGrid** per il tipo di evento **SensorChange**, **SpaceChange**, **TopologyOperation** o **UdfCustom**.

> [!NOTE]
> Alcuni parametri che vengono in genere definiti durante la creazione delle entità di Azure IoT indicate sopra non sono necessari durante l'anteprima pubblica.
> - Consultare la [documentazione di riferimento di Swagger](./how-to-use-swagger.md) per le specifiche di API più recenti.

## <a name="azure-digital-twins-management-api-limits"></a>Limiti dell'API di gestione di Gemelli digitali di Azure

I limiti per il numero di richieste per l'API di gestione di Gemelli digitali di Azure sono i seguenti:

- 100 richieste al secondo all'API di gestione di Gemelli digitali di Azure.
- Fino a 1000 oggetti restituiti da una singola query sull'API di gestione di Gemelli digitali di Azure.

> [!IMPORTANT]
> Se si supera il limite di 1.000 oggetti, si riceverà un errore e sarà necessario semplificare la query.

## <a name="user-defined-functions-rate-limits"></a>Limiti al numero di funzioni definite dall'utente

I limiti seguenti definiscono il numero totale di tutte le chiamate delle funzioni definite dall'utente inviate all'istanza di Gemelli digitali di Azure:

- 400 chiamate alla libreria client al secondo
- 100 chiamate **SendNotification** al secondo

> [!NOTE]
> Le operazioni seguenti potrebbero generare l'applicazione temporanea di altri limiti:
> - Modifiche ai metadati di oggetti della topologia
> - Aggiornamenti apportati alla definizione di funzione definita dall'utente
> - Dispositivi che inviano dati di telemetria per la prima volta

## <a name="device-telemetry-limits"></a>Limiti relativi alla telemetria dei dispositivi

I limiti seguenti si applicano al numero totale di tutti i messaggi che i dispositivi possono inviare all'istanza di Gemelli digitali di Azure:

- 100 messaggi al secondo in tutti i dispositivi
-    25 messaggi al secondo per dispositivo

## <a name="next-steps"></a>Passaggi successivi

- Per provare un esempio di Gemelli digitali di Azure, vedere [Guida introduttiva per la ricerca di stanze disponibili](./quickstart-view-occupancy-dotnet.md).
