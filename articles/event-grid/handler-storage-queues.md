---
title: Coda di archiviazione come gestore eventi per gli eventi di Griglia di eventi di Azure
description: Descrive come usare le code di archiviazione di Azure come gestori eventi di Griglia di eventi di Azure.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: spelluru
ms.openlocfilehash: 9b767caa1041f865d8e15cd57796b186f7a4a6bb
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83596270"
---
# <a name="storage-queue-as-an-event-handler-for-azure-event-grid-events"></a>Coda di archiviazione come gestore eventi per gli eventi di Griglia di eventi di Azure
Un gestore eventi è la posizione in cui l'evento viene inviato. Il gestore esegue altre azioni per elaborare l'evento. Per gestire gli eventi vengono configurati automaticamente diversi servizi di Azure, tra cui l'**archiviazione code di Azure**. 

Usare l'**archiviazione code** per ricevere gli eventi di cui è necessario eseguire il pull. È possibile usare l'archiviazione code quando si dispone di un processo a esecuzione prolungata che impiega troppo tempo per rispondere. Mediante l'invio di eventi all'archiviazione code, l'app può eseguire il pull ed elaborare eventi in base alla propria pianificazione.

## <a name="tutorials"></a>Esercitazioni
Per un esempio relativo all'uso dell'archiviazione code come gestore eventi, vedere l'esercitazione seguente. 

|Titolo  |Descrizione  |
|---------|---------|
| [Avvio rapido: Instradare eventi personalizzati ad Archiviazione code di Azure con l'interfaccia della riga di comando di Azure e Griglia di eventi](custom-event-to-queue-storage.md) | Descrive come inviare eventi personalizzati a un archivio code. |

## <a name="next-steps"></a>Passaggi successivi
Per un elenco di gestori eventi supportati, vedere l'articolo [Gestori eventi](event-handlers.md). 
