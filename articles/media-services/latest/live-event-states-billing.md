---
title: Stati e fatturazione LiveEvent in Servizi multimediali di Azure | Microsoft Docs
description: Questo argomento offre una panoramica degli stati e della fatturazione dei Servizi multimediali di Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 10/24/2019
ms.author: juliako
ms.openlocfilehash: e4eee3b9f3f97bf7cd7a7b61425ec5c9a3a198ef
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76543076"
---
# <a name="live-event-states-and-billing"></a>Stati e fatturazione di eventi live

In Servizi multimediali di Azure, la fatturazione di un evento live non appena lo stato dell'evento live passa a **In esecuzione**. Ti verrà addebitato anche se non è presente alcun video che scorre il servizio. Per interrompere la fatturazione dell'evento live, è necessario interrompere quest'ultimo. La trascrizione in tempo reale viene fatturata allo stesso modo dell'evento Live.

Quando **LiveEventEncodingType** nell' [evento Live](https://docs.microsoft.com/rest/api/media/liveevents) è impostato su standard o Premium1080p, servizi multimediali arresta automaticamente qualsiasi evento live che rimane nello stato di **esecuzione** 12 ore dopo la perdita del feed di input e non è in esecuzione alcun output in **tempo**reale. Tuttavia verrà comunque fatturato il tempo durante il quale lo stato dell'evento live è rimasto impostato su **In esecuzione**.

> [!NOTE]
> Gli eventi live pass-through non vengono arrestati automaticamente e devono essere interrotti in modo esplicito tramite l'API per evitare una fatturazione eccessiva. 

## <a name="states"></a>Stati

Di seguito sono riportati gli stati possibili per l'evento live.

|State|Descrizione|
|---|---|
|**Arrestato**| Si tratta dello stato iniziale dell'evento Live dopo la creazione, a meno che autostart non sia impostato su true. In questo stato non viene eseguita alcuna fatturazione. In questo stato è possibile aggiornare le proprietà dell'evento live ma lo streaming non è consentito.|
|**Avvio in corso**| L'avvio dell'evento live e l'allocazione delle risorse sono in corso. In questo stato non viene eseguita alcuna attività di fatturazione. Durante questo stato non sono consentiti aggiornamenti o streaming. Se si verifica un errore, l'evento live torna allo stato Interrotto.|
|**In esecuzione**| Le risorse dell'evento live sono state allocate, gli URL di inserimento e anteprima sono stati generati ed è possibile ricevere flussi live. A questo punto, la fatturazione è attiva. È necessario chiamare esplicitamente Stop sulla risorsa evento live per interrompere la fatturazione.|
|**Stopping**| L'interruzione dell'evento live e il deprovisioning delle risorse sono in corso. In questo stato di transizione non viene eseguita alcuna attività di fatturazione. Durante questo stato non sono consentiti aggiornamenti o streaming.|
|**Eliminazione**| L'eliminazione dell'evento live è in corso. In questo stato di transizione non viene eseguita alcuna attività di fatturazione. Durante questo stato non sono consentiti aggiornamenti o streaming.|

È possibile scegliere di abilitare le trascrizioni Live quando si crea l'evento Live. In tal caso, verranno addebitate le trascrizioni in tempo reale ogni volta che l'evento Live si trova nello stato in **esecuzione** . Si noti che verrà addebitato anche se non è presente alcun flusso audio attraverso l'evento Live.

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica dello streaming live](live-streaming-overview.md)
- [Esercitazione sullo streaming live](stream-live-tutorial-with-api.md)
