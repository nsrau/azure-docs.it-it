---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/12/2019
ms.author: pafarley
ms.openlocfilehash: f0761847c3677b324ef16c5987eb9a1561dbcbe0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75379293"
---
Esaminare i valori di `"confidence"` per ogni risultato di chiave-valore nel nodo `"pageResults"`. È inoltre consigliabile esaminare i punteggi di attendibilità nel nodo `"readResults"`, che corrispondono all'operazione di lettura del testo. L'attendibilità dei risultati della lettura non influisce su quella dei risultati dell'estrazione di coppie chiave-valore, quindi è necessario controllare entrambi.
* Se i punteggi di attendibilità dell'operazione di lettura sono bassi, provare a migliorare la qualità dei documenti di input. Vedere [Requisiti di input](../overview.md#input-requirements).
* Se i punteggi di attendibilità per l'operazione di estrazione di coppie chiave-valore sono bassi, assicurarsi che i documenti da analizzare siano dello stesso tipo di quelli usati nel set di training. Se i documenti del set di training hanno aspetti diversi, è consigliabile dividerli in cartelle diverse ed eseguire il training di un modello per ogni variante.

I punteggi di attendibilità obiettivo dipendono dal caso d'uso, ma in genere è consigliabile avere come obiettivo un punteggio di almeno l'80%. Per i casi più sensibili, come la lettura di record medici o gli estratti conto, è consigliabile un punteggio del 100%.