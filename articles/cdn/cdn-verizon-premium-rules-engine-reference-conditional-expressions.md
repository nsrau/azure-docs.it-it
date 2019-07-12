---
title: Espressioni condizionali del motore regole di rete CDN di Azure fornita da Verizon Premium | Microsoft Docs
description: Documentazione di riferimento per la rete CDN di Azure fornita da Verizon Premium regole di funzionalità e condizioni di corrispondenza del motore.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: f790e37ae876c0640d55ebfb51abb43c6a705f04
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593229"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-conditional-expressions"></a>Rete CDN di Azure da espressioni condizionali del motore regole di Verizon Premium

Questo articolo fornisce descrizioni dettagliate delle espressioni condizionali per contenuto Delivery Network (rete CDN di Azure) [motore regole di business](cdn-verizon-premium-rules-engine.md).

La prima parte di una regola è l'espressione condizionale.

Espressione condizionale | DESCRIZIONE
-----------------------|-------------
IF | Un'espressione IF è sempre una parte della prima istruzione in una regola. Come tutte le altre espressioni condizionali, l'istruzione IF deve essere associata a una corrispondenza. Se non è definite alcun altre espressioni condizionali, questa corrispondenza determina il criterio che deve essere soddisfatti prima di un set di funzionalità può essere applicato a una richiesta.
AND IF | Un'espressione AND IF può essere aggiunta solo dopo i tipi di espressioni condizionali seguenti: IF e AND IF. Indica che esiste un'altra condizione che deve essere soddisfatta per l'istruzione IF iniziale.
ELSE IF| Un'espressione ELSE IF specifica una condizione alternativa che deve essere soddisfatta prima che venga eseguita una serie di funzionalità specifiche di questa istruzione ELSE IF. La presenza di un'istruzione ELSE IF indica la fine dell'istruzione precedente. L'unica espressione condizionale che può essere inserita dopo un'istruzione ELSE IF è un'altra istruzione ELSE IF. Ciò significa che un'istruzione ELSE IF può essere usata solo per specificare una sola condizione aggiuntiva da soddisfare.

**Esempio**: ![Condizione di corrispondenza della rete CDN](./media/cdn-rules-engine-reference/cdn-rules-engine-conditional-expression.png)

 > [!TIP]
   > Una regola successiva potrebbe seguire l'override delle azioni specificate da una regola precedente.
   > Esempio: Una regola di catch-all protegge tutte le richieste tramite l'autenticazione basata su Token. È possibile creare un'altra regola direttamente sotto questa per creare un'eccezione per alcuni tipi di richieste.

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica della rete CDN di Azure](cdn-overview.md)
- [Informazioni di riferimento sul motore regole](cdn-verizon-premium-rules-engine-reference.md)
- [Condizioni di corrispondenza del motore regole](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Funzionalità del motore regole](cdn-verizon-premium-rules-engine-reference-features.md)
- [Override del comportamento HTTP predefinito mediante il motore regole](cdn-verizon-premium-rules-engine.md)