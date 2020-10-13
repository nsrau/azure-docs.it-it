---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/20/2020
ms.author: glenga
ms.openlocfilehash: 574756aa9d9bac4aa42febf6a4fca4c62014db3f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84732473"
---
1. In Visual Studio Code premere F1 per aprire il riquadro comandi. Nel riquadro comandi cercare e selezionare **Azure Functions: Deploy to function app** (Funzioni di Azure: distribuisci nell'app per le funzioni).

1. Se non è stato effettuato l'accesso, viene richiesto di **accedere ad Azure**. Dopo l'accesso dal browser, tornare a Visual Studio Code. Se sono disponibili più sottoscrizioni, **selezionare una sottoscrizione** che contiene l'app per le funzioni.

1. Selezionare l'app per le funzioni esistente in Azure. Quando viene visualizzato un avviso relativo alla sovrascrittura di tutti i file nell'app per le funzioni, selezionare **Distribuisci** per confermare l'avviso e continuare.

Il progetto viene ricompilato, riassemblato in un nuovo pacchetto e caricato in Azure. Il progetto esistente viene sostituito da quello nuovo e l'app per le funzioni viene riavviata.