---
title: Connessione ibrida di Inoltro di Azure come gestore eventi per gli eventi di Griglia di eventi di Azure
description: Descrive come usare connessioni ibride di Inoltro di Azure come gestori eventi di Griglia di eventi di Azure.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: spelluru
ms.openlocfilehash: 0631724e688a71d7e9685f5f0ad738d81e2a8034
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83596110"
---
# <a name="relay-hybrid-connection-as-an-event-handler-for-azure-event-grid-events"></a>Connessione ibrida di Inoltro di Azure come gestore eventi per gli eventi di Griglia di eventi di Azure
Un gestore eventi è la posizione in cui l'evento viene inviato. Il gestore esegue altre azioni per elaborare l'evento. Per gestire gli eventi vengono configurati automaticamente diversi servizi di Azure, tra cui **Inoltro di Azure**. 

Usare **connessioni ibride di Inoltro di Azure** per inviare eventi alle applicazioni all'interno di una rete aziendale che non hanno un endpoint accessibile pubblicamente.

## <a name="tutorials"></a>Esercitazioni
Per un esempio relativo all'uso delle connessioni ibride di Inoltro di Azure come gestore eventi, vedere l'esercitazione seguente. 

|Titolo  |Descrizione  |
|---------|---------|
| [Esercitazione: Instradare eventi a Connessioni ibride](custom-event-to-hybrid-connection.md) | Invia un evento personalizzato a una connessione ibrida esistente per l'elaborazione da parte dell'applicazione listener. |

## <a name="next-steps"></a>Passaggi successivi
Per un elenco di gestori eventi supportati, vedere l'articolo [Gestori eventi](event-handlers.md). 