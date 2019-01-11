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
ms.date: 11/16/2018
ms.author: juliako
ms.openlocfilehash: 588aeede123848900fac6fab663dd1f6c6c169b6
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2018
ms.locfileid: "53719422"
---
# <a name="liveevent-states-and-billing"></a>Stati e fatturazione di LiveEvent

In Servizi multimediali di Azure, un LiveEvent avvia la fatturazione non appena il suo stato viene impostato su **In esecuzione**. Per impedire la fatturazione da parte del LiveEvent, è necessario arrestare il LiveEvent.

Quando **LiveEventEncodingType** nel [LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents) è impostato su Standard, Servizi multimediali arresta automaticamente qualsiasi LiveEvent che è ancora nello stato **In esecuzione** 12 ore dopo che il feed di input è andato perso e nessun **LiveOutput** è in esecuzione. Verrà tuttavia addebitato il tempo in cui il LiveEvent è rimasto nello stato **In esecuzione**.

## <a name="states"></a>Stati

Di seguito sono riportati gli stati possibili per il LiveEvent.

|Stato|DESCRIZIONE|
|---|---|
|**Stopped**| Lo stato iniziale del LiveEvent dopo la creazione (se l'opzione di avvio automatico non è impostata su Vero). In questo stato non viene eseguita alcuna attività di fatturazione. In questo stato le proprietà del LiveEvent possono essere aggiornate ma lo streaming non è consentito.|
|**Starting**| L'avvio del LiveEvent e l'allocazione delle risorse sono in corso. In questo stato non viene eseguita alcuna attività di fatturazione. Durante questo stato non sono consentiti aggiornamenti o streaming. Se si verifica un errore, il LiveEvent torna allo stato Interrotto.|
|**Running**| Le risorse del LiveEvent sono state allocate, gli URL di inserimento e anteprima sono stati generati ed è possibile ricevere flussi live. A questo punto, la fatturazione è attiva. È necessario chiamare esplicitamente Stop sulla risorsa LiveEvent per interrompere la fatturazione.|
|**Stopping**| L'arresto del LiveEvent e il deprovisioning delle risorse sono in corso. In questo stato di transizione non viene eseguita alcuna attività di fatturazione. Durante questo stato non sono consentiti aggiornamenti o streaming.|
|**Deleting**| Il LiveEvent viene eliminato. In questo stato di transizione non viene eseguita alcuna attività di fatturazione. Durante questo stato non sono consentiti aggiornamenti o streaming.|

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica dello streaming live](live-streaming-overview.md)
- [Esercitazione sullo streaming live](stream-live-tutorial-with-api.md)
