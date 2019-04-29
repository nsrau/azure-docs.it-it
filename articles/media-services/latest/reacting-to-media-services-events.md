---
title: Reazione a eventi di Servizi multimediali di Azure | Microsoft Docs
description: Usare Griglia di eventi di Azure per sottoscrivere eventi di Servizi multimediali.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/12/2019
ms.author: juliako
ms.openlocfilehash: cb5d6474a0c830933c712e1008015b5220617c96
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60996158"
---
# <a name="handling-event-grid-events"></a>Gestione degli eventi di Griglia di eventi

Gli eventi di Servizi multimediali consentono alle applicazioni di reagire a numerosi eventi (ad esempio, l'evento di modifica dello stato del processo) tramite moderne architetture senza server e senza la necessità di usare codice complesso o servizi di polling costosi e inefficienti. Gli eventi vengono invece inviati attraverso [Griglia di eventi di Azure](https://azure.microsoft.com/services/event-grid/) ai gestori degli eventi, ad esempio [Funzioni di Azure](https://azure.microsoft.com/services/functions/), [App per la logica di Azure](https://azure.microsoft.com/services/logic-apps/) o anche al webhook in uso, con pagamento in base all'utilizzo. Per informazioni sui prezzi, vedere [Prezzi di Griglia di eventi](https://azure.microsoft.com/pricing/details/event-grid/).

La disponibilità degli eventi di Servizi multimediali è legata alla [disponibilità](../../event-grid/overview.md) di Griglia di eventi. Gli eventi saranno disponibili nelle aree geografiche in cui si renderà disponibile Griglia di eventi.  

## <a name="media-services-events-and-schemas"></a>Gli schemi e gli eventi di servizi multimediali

Griglia di eventi usa le [sottoscrizioni di eventi](../../event-grid/concepts.md#event-subscriptions) per instradare i messaggi di evento ai sottoscrittori. Gli eventi di Servizi multimediali contengono tutte le informazioni necessarie per rispondere alle modifiche dei dati. Un evento di Servizi multimediali è riconoscibile perché la proprietà eventType inizia con "Microsoft.Media".

Per altre informazioni, vedere [Schemi di eventi di Servizi multimediali](media-services-event-schemas.md).

## <a name="practices-for-consuming-events"></a>Procedure consigliate per l'utilizzo di eventi

Per le applicazioni che gestiscono gli eventi di Servizi multimediali è consigliabile seguire alcune procedure:

* Dal momento che vi possono essere più sottoscrizioni configurate per instradare gli eventi allo stesso gestore eventi, è importante non presupporre che gli eventi derivino da un'origine specifica, ma controllare l'argomento del messaggio per assicurarsi che provengano dall'account di archiviazione previsto.
* Controllare anche che il tipo di evento che si verifica, eventType, sia un oggetto che si è preparati a elaborare e non presupporre che tutti gli eventi che si ricevono siano dei tipi previsti.
* Ignorare i campi che non si conoscono.  Questa procedura consentirà di rimanere flessibili alle nuove funzionalità che potrebbero essere aggiunte in futuro.
* Usare le corrispondenze di prefisso e suffisso "subject" per limitare gli eventi a un determinato evento.

## <a name="next-steps"></a>Passaggi successivi

[Get job state events](job-state-events-cli-how-to.md) (Ottenere gli eventi relativi allo stato del processo)
