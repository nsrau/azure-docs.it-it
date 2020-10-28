---
title: Stati degli eventi live e fatturazione in servizi multimediali di Azure | Microsoft Docs
description: Questo argomento offre una panoramica della fatturazione e degli Stati degli eventi live di servizi multimediali di Azure.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: conceptual
ms.date: 10/26/2020
ms.author: inhenkel
ms.openlocfilehash: 2d3d3f5c56bd42aeb148c19fefebc0e7d364cd1c
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92782366"
---
# <a name="live-event-states-and-billing"></a>Stati degli eventi live e fatturazione

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

In servizi multimediali di Azure un evento live inizia la fatturazione non appena il suo stato passa a **in esecuzione o in** **standby** . Ti verrà addebitato anche se non è presente alcun video che scorre il servizio. Per arrestare l'evento live dalla fatturazione, è necessario arrestare l'evento Live. La trascrizione in tempo reale viene fatturata allo stesso modo dell'evento Live.

Quando **LiveEventEncodingType** nell' [evento Live](/rest/api/media/liveevents) è impostato su standard o Premium1080p, servizi multimediali arresta automaticamente qualsiasi evento live che rimane nello stato di **esecuzione** 12 ore dopo la perdita del feed di input e non è in esecuzione alcun output in **tempo** reale. Tuttavia, verrà comunque addebitato il tempo in cui l'evento Live si trova nello stato in **esecuzione** .

> [!NOTE]
> Gli eventi live pass-through non vengono arrestati automaticamente e devono essere interrotti in modo esplicito tramite l'API per evitare una fatturazione eccessiva.

## <a name="states"></a>Stati

L'evento Live può trovarsi in uno degli Stati seguenti.

|State|Descrizione|
|---|---|
|**Arrestato**| Si tratta dello stato iniziale dell'evento Live dopo la creazione, a meno che autostart non sia impostato su true. In questo stato non viene eseguita alcuna fatturazione. L'evento Live non può ricevere input. |
|**Avvio in corso**| L'evento Live è in fase di avvio e le risorse vengono allocate. In questo stato non viene eseguita alcuna attività di fatturazione.  Se si verifica un errore, l'evento Live torna allo stato interrotto.|
| **Allocazione** | L'azione di allocazione è stata chiamata nell'evento Live ed è in corso il provisioning delle risorse per questo evento Live. Al termine dell'operazione, l'evento Live passerà allo stato StandBy.
|**StandBy**| è stato eseguito il provisioning delle risorse eventi live ed è pronto per l'avvio. La fatturazione avviene in questo stato.  La maggior parte delle proprietà può comunque essere aggiornata, ma l'inserimento o lo streaming non è consentito durante questo stato.
|**Running**| Le risorse eventi Live sono state allocate, sono stati generati URL di inserimento e anteprima e sono in grado di ricevere flussi live. A questo punto, la fatturazione è attiva. È necessario chiamare in modo esplicito stop sulla risorsa evento Live per interrompere ulteriormente la fatturazione.|
|**Stopping**| L'evento Live viene arrestato e viene effettuato il deprovisioning delle risorse. In questo stato di transizione non viene eseguita alcuna attività di fatturazione. |
|**Deleting**| È in corso l'eliminazione dell'evento Live. In questo stato di transizione non viene eseguita alcuna attività di fatturazione. |

È possibile scegliere di abilitare le trascrizioni Live quando si crea l'evento Live. In tal caso, verranno addebitate le trascrizioni in tempo reale ogni volta che l'evento Live si trova nello stato in **esecuzione** . Si noti che verrà addebitato anche se non è presente alcun flusso audio attraverso l'evento Live.

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica dello streaming live](live-streaming-overview.md)
- [Esercitazione sullo streaming live](stream-live-tutorial-with-api.md)
