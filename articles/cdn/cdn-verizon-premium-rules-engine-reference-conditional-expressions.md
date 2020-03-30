---
title: Espressioni condizionali per la rete CDN di Azure - Motore regole Verizon PremiumConditional expressions for Azure CDN - Verizon Premium rules engine
description: La documentazione di riferimento per la rete CDN di Azure dal motore regole di Verizon Premium soddisfa le condizioni e le funzionalità.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: d28bda28894477845c2a050666b3b4dd332b7d50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74082967"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-conditional-expressions"></a>La rete CDN di Azure da Verizon Premium motore espressioni condizionaliAzure CDN from Verizon Premium rules engine conditional expressions

Questo articolo elenca le descrizioni dettagliate del motore regole Espressioni condizionali per la rete per la distribuzione di contenuti di Azure.This article lists detailed descriptions of the Conditional Expressions for Azure Content Delivery Network (CDN) [Rules Engine](cdn-verizon-premium-rules-engine.md).

La prima parte di una regola è l'espressione condizionale.

Espressione condizionale | Descrizione
-----------------------|-------------
IF | Un'espressione IF è sempre una parte della prima istruzione in una regola. Come tutte le altre espressioni condizionali, l'istruzione IF deve essere associata a una corrispondenza. Se non sono definite espressioni condizionali aggiuntive, questa corrispondenza determina il criterio che deve essere soddisfatto prima di poter applicare un set di funzionalità a una richiesta.
AND IF | Un'espressione AND IF può essere aggiunta solo dopo i tipi di espressioni condizionali seguenti: IF e AND IF. Indica che esiste un'altra condizione che deve essere soddisfatta per l'istruzione IF iniziale.
ELSE IF| Un'espressione ELSE IF specifica una condizione alternativa che deve essere soddisfatta prima che venga eseguita una serie di funzionalità specifiche di questa istruzione ELSE IF. La presenza di un'istruzione ELSE IF indica la fine dell'istruzione precedente. L'unica espressione condizionale che può essere inserita dopo un'istruzione ELSE IF è un'altra istruzione ELSE IF. Ciò significa che un'istruzione ELSE IF può essere usata solo per specificare una sola condizione aggiuntiva da soddisfare.

**Esempio:** condizione di corrispondenza CDNExample : ![CDN match condition](./media/cdn-rules-engine-reference/cdn-rules-engine-conditional-expression.png)

 > [!TIP]
   > Una regola successiva potrebbe seguire l'override delle azioni specificate da una regola precedente.
   > Esempio: una regola di catch-all protegge tutte le richieste tramite l'autenticazione basata su token. È possibile creare un'altra regola direttamente sotto questa per creare un'eccezione per alcuni tipi di richieste.

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica della rete CDN di Azure](cdn-overview.md)
- [Informazioni di riferimento sul motore regole](cdn-verizon-premium-rules-engine-reference.md)
- [Condizioni di corrispondenza del motore regole](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Funzionalità del motore regole](cdn-verizon-premium-rules-engine-reference-features.md)
- [Override del comportamento HTTP predefinito mediante il motore regole](cdn-verizon-premium-rules-engine.md)