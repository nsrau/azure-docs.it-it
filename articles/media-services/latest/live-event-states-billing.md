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
ms.openlocfilehash: 1a49f62d7b5e21fe9d6483f71b729a9100aff1a3
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/29/2018
ms.locfileid: "52585439"
---
# <a name="liveevent-states-and-billing"></a>Stati e fatturazione di LiveEvent

In Servizi multimediali di Azure, un LiveEvent avvia la fatturazione non appena il suo stato viene impostato su **In esecuzione**. Per impedire la fatturazione da parte del LiveEvent, è necessario arrestare il LiveEvent.

Quando **LiveEventEncodingType** nel [LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents) è impostato su Standard (Basic), Servizi multimediali arresta automaticamente qualsiasi LiveEvent ancora nello stato **In esecuzione** 12 ore dopo che il feed di input è andato perso e non ci sono più **LiveOutput** in esecuzione. Verrà tuttavia addebitato il tempo in cui il LiveEvent è rimasto nello stato **In esecuzione**.

## <a name="states"></a>Stati

Di seguito sono riportati gli stati possibili per il LiveEvent.

|Stato|DESCRIZIONE|
|---|---|
|**Stopped**| Lo stato iniziale del LiveEvent dopo la creazione (se l'opzione di avvio automatico non è impostata su Vero). In questo stato non viene eseguita alcuna attività di fatturazione. In questo stato le proprietà del LiveEvent possono essere aggiornate ma lo streaming non è consentito.|
|**Avvio**| L'avvio del LiveEvent e l'allocazione delle risorse sono in corso. In questo stato non viene eseguita alcuna attività di fatturazione. Durante questo stato non sono consentiti aggiornamenti o streaming. Se si verifica un errore, il LiveEvent torna allo stato Interrotto.|
|**Running**| Le risorse del LiveEvent sono state allocate, gli URL di inserimento e anteprima sono stati generati ed è possibile ricevere flussi live. A questo punto, la fatturazione è attiva. È necessario chiamare esplicitamente Stop sulla risorsa LiveEvent per interrompere la fatturazione.|
|**Arresto**| L'arresto del LiveEvent e il deprovisioning delle risorse sono in corso. In questo stato di transizione non viene eseguita alcuna attività di fatturazione. Durante questo stato non sono consentiti aggiornamenti o streaming.|
|**Eliminazione in corso**| Il LiveEvent viene eliminato. In questo stato di transizione non viene eseguita alcuna attività di fatturazione. Durante questo stato non sono consentiti aggiornamenti o streaming.|

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica dello streaming live](live-streaming-overview.md)
- [Esercitazione sullo streaming live](stream-live-tutorial-with-api.md)
