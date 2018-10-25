---
title: Routing di eventi e messaggi con Gemelli digitali di Azure | Microsoft Docs
description: Panoramica del routing di eventi e messaggi agli endpoint di servizio con Gemelli digitali di Azure
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: alinast
ms.openlocfilehash: 9b861f0991b11637c7b27b645d4506e658ea53b3
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2018
ms.locfileid: "49323900"
---
# <a name="routing-events-and-messages"></a>Routing di eventi e messaggi

Le soluzioni IoT spesso riuniscono diversi potenti servizi, tra cui archiviazione, analisi e altro ancora. Questo articolo descrive come connettere app di Gemelli digitali di Azure a servizi di analisi, intelligenza artificiale e archiviazione di Azure per arricchirle con informazioni e funzionalità più avanzate.

## <a name="route-types"></a>Tipi di routing

Gemelli digitali offre due metodi per integrare eventi IoT in altri servizi o applicazioni aziendali di Azure:

* **Routing di eventi di Gemelli digitali**: gli eventi di Gemelli digitali di Azure possono essere attivati quando un oggetto nel grafico spaziale cambia, quando vengono ricevuti dati di telemetria o quando una funzione definita dall'utente crea una notifica in base a condizioni predefinite. Gli utenti possono inviare questi eventi a [hub eventi](https://azure.microsoft.com/services/event-hubs/),ad [argomenti del bus di servizio](https://azure.microsoft.com/services/service-bus/), o a [Griglia di eventi](https://azure.microsoft.com/services/event-grid/) per l'ulteriore elaborazione.

* **Routing dei dati di telemetria dei dispositivi**: oltre al routing di eventi, Gemelli digitali di Azure può instradare anche messaggi di telemetria dei dispositivi non elaborati a hub eventi per ulteriori analisi e recupero di informazioni. Questi tipi di messaggi non vengono elaborati da Gemelli digitali, ma vengono solo inoltrati all'**hub eventi**.

Gli utenti possono specificare uno o più endpoint in uscita per inviare eventi o inoltrare messaggi. Eventi e messaggi verranno inviati agli endpoint in base a queste preferenze di routing predefinite. In altre parole, gli utenti possono specificare un determinato endpoint per ricevere gli eventi delle operazioni del grafico e un altro per ricevere gli eventi di telemetria dei dispositivi e così via.

![Routing di eventi di Gemelli digitali][1]

Il routing a **Hub eventi** manterrà l'ordine in cui vengono inviati i messaggi di telemetria, in modo che possano arrivare all'endpoint nella stessa sequenza in cui sono stati ricevuti in origine. **Griglia di eventi** e il **bus di servizio** non garantiscono che gli endpoint riceveranno gli eventi nello stesso ordine in cui si sono verificati. Lo schema degli eventi include tuttavia un timestamp che può essere usato per identificare l'ordine dopo che gli eventi raggiungono l'endpoint.

## <a name="route-implementation"></a>Implementazione del routing

Attualmente il servizio Gemelli digitali di Azure supporta gli elementi **EndpointType** seguenti:

* **EventHub**: endpoint della stringa di connessione dell'hub eventi.
* **ServiceBus**: endpoint della stringa di connessione del bus di servizio.
* **EventGrid**: endpoint della stringa di connessione di Griglia di eventi.

Attualmente Gemelli digitali di Azure supporta gli elementi **EventType** seguenti da inviare all'endpoint scelto:

* **DeviceMessages**: messaggi di telemetria inviati dai dispositivi degli utenti e inoltrati dal sistema.
* **TopologyOperation**: operazioni che modificano il grafico o i metadati del grafico. Ad esempio, l'aggiunta o l'eliminazione di un'entità, come uno spazio.
* **SpaceChange**: modifiche in un valore calcolato di uno spazio come risultato di un messaggio di telemetria di un dispositivo.
* **SpaceChange**: modifiche in un valore calcolato di un sensore come risultato di un messaggio di telemetria di un dispositivo.
* **UdfCustom**: notifiche personalizzate provenienti da una funzione definita dall'utente.

> [!IMPORTANT]
> Non tutti gli elementi **EndpointType** supportano tutti gli **EventType**.
> Vedere la tabella seguente per gli elementi **EventType** consentiti per ogni elemento **EndpointType**.

|             | DeviceMessages | TopologyOperation | SpaceChange | SensorChange | UdfCustom |
| ----------- | -------------- | ----------------- | ----------- | ------------ | --------- |
| **EventHub**|     X          |         X         |     X       |      X       |   X       |
| **Bus di servizio**|              |         X         |     X       |      X       |   X       |
| **EventGrid**|               |         X         |     X       |      X       |   X       |

>[!NOTE]
>Per altre informazioni su come creare endpoint e per esempi di schema degli eventi, vedere [Endpoint e traffico in uscita](how-to-egress-endpoints.md).

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sui limiti dell'anteprima pubblica, vedere [Limiti durante l'anteprima di Gemelli digitali di Azure](concepts-service-limits.md).

Per provare un esempio di Gemelli digitali di Azure, vedere [Guida introduttiva per la ricerca di stanze disponibili](quickstart-view-occupancy-dotnet.md).

<!-- Images -->
[1]: media/concepts/digital-twins-events-routing.png