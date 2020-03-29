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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76543076"
---
# <a name="live-event-states-and-billing"></a>Stati e fatturazione di eventi live

In Servizi multimediali di Azure, la fatturazione di un evento live non appena lo stato dell'evento live passa a **In esecuzione**. Ti verrà addebitato anche se non c'è nessun video che scorre attraverso il servizio. Per interrompere la fatturazione dell'evento live, è necessario interrompere quest'ultimo. La trascrizione dal vivo viene fatturata allo stesso modo dell'evento live.

Quando **LiveEventEncodingType** [sull'evento live](https://docs.microsoft.com/rest/api/media/liveevents) è impostato su Standard o Premium1080p, Servizi multimediali disattiva automaticamente qualsiasi evento live che si trova nello stato **In esecuzione** 12 ore dopo la perdita del feed di input e non sono in esecuzione alcun evento **Live Output.** Tuttavia verrà comunque fatturato il tempo durante il quale lo stato dell'evento live è rimasto impostato su **In esecuzione**.

> [!NOTE]
> Gli eventi live pass-through non vengono chiusi automaticamente e devono essere arrestati in modo esplicito tramite l'API per evitare un'eccessiva fatturazione. 

## <a name="states"></a>Stati

Di seguito sono riportati gli stati possibili per l'evento live.

|State|Descrizione|
|---|---|
|**Fermato**| Questo è lo stato iniziale dell'evento live dopo la creazione (a meno che l'avvio automatico non sia stato impostato su true). In questo stato non viene eseguita alcuna fatturazione. In questo stato è possibile aggiornare le proprietà dell'evento live ma lo streaming non è consentito.|
|**A partire**| L'avvio dell'evento live e l'allocazione delle risorse sono in corso. In questo stato non viene eseguita alcuna attività di fatturazione. Durante questo stato non sono consentiti aggiornamenti o streaming. Se si verifica un errore, l'evento live torna allo stato Interrotto.|
|**In esecuzione**| Le risorse dell'evento live sono state allocate, gli URL di inserimento e anteprima sono stati generati ed è possibile ricevere flussi live. A questo punto, la fatturazione è attiva. È necessario chiamare esplicitamente Stop sulla risorsa evento live per interrompere la fatturazione.|
|**Arresto**| L'interruzione dell'evento live e il deprovisioning delle risorse sono in corso. In questo stato di transizione non viene eseguita alcuna attività di fatturazione. Durante questo stato non sono consentiti aggiornamenti o streaming.|
|**Eliminazione**| L'eliminazione dell'evento live è in corso. In questo stato di transizione non viene eseguita alcuna attività di fatturazione. Durante questo stato non sono consentiti aggiornamenti o streaming.|

Potete scegliere di attivare le trascrizioni live quando create l'evento dal vivo. In questo caso, ti verranno addebitate le trascrizioni dal vivo ogni volta che l'evento live è nello stato **In esecuzione.** Si noti che verrà addebitato anche se non vi è alcun audio che scorre attraverso l'evento dal vivo.

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica dello streaming live](live-streaming-overview.md)
- [Esercitazione sullo streaming live](stream-live-tutorial-with-api.md)
