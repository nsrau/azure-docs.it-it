---
title: Espressioni condizionali per la rete CDN di Azure-motore regole Verizon Premium
description: La documentazione di riferimento per le condizioni e le funzionalità del motore regole di rete CDN di Azure da Verizon Premium è.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: d28bda28894477845c2a050666b3b4dd332b7d50
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74082967"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-conditional-expressions"></a>Espressioni condizionali del motore regole di rete CDN di Azure di Verizon Premium

Questo articolo elenca le descrizioni dettagliate delle espressioni condizionali per il [motore regole](cdn-verizon-premium-rules-engine.md)della rete per la distribuzione di contenuti (CDN) di Azure.

La prima parte di una regola è l'espressione condizionale.

Espressione condizionale | DESCRIZIONE
-----------------------|-------------
IF | Un'espressione IF è sempre una parte della prima istruzione in una regola. Come tutte le altre espressioni condizionali, l'istruzione IF deve essere associata a una corrispondenza. Se non vengono definite espressioni condizionali aggiuntive, questa corrispondenza determina il criterio che deve essere soddisfatto prima che un set di funzionalità possa essere applicato a una richiesta.
AND IF | Un'espressione AND IF può essere aggiunta solo dopo i tipi di espressioni condizionali seguenti: IF e AND IF. Indica che esiste un'altra condizione che deve essere soddisfatta per l'istruzione IF iniziale.
ELSE IF| Un'espressione ELSE IF specifica una condizione alternativa che deve essere soddisfatta prima che venga eseguita una serie di funzionalità specifiche di questa istruzione ELSE IF. La presenza di un'istruzione ELSE IF indica la fine dell'istruzione precedente. L'unica espressione condizionale che può essere inserita dopo un'istruzione ELSE IF è un'altra istruzione ELSE IF. Ciò significa che un'istruzione ELSE IF può essere usata solo per specificare una sola condizione aggiuntiva da soddisfare.

**Esempio**: ![condizione di corrispondenza della rete CDN](./media/cdn-rules-engine-reference/cdn-rules-engine-conditional-expression.png)

 > [!TIP]
   > Una regola successiva potrebbe seguire l’override delle azioni specificate da una regola precedente.
   > Esempio: una regola di catch-all protegge tutte le richieste tramite l'autenticazione basata su token. È possibile creare un'altra regola direttamente sotto questa per creare un'eccezione per alcuni tipi di richieste.

## <a name="next-steps"></a>Passaggi successivi

- [Panoramica della rete CDN di Azure](cdn-overview.md)
- [Informazioni di riferimento sul motore regole](cdn-verizon-premium-rules-engine-reference.md)
- [Condizioni di corrispondenza del motore regole](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Funzionalità del motore regole](cdn-verizon-premium-rules-engine-reference-features.md)
- [Override del comportamento HTTP predefinito mediante il motore regole](cdn-verizon-premium-rules-engine.md)