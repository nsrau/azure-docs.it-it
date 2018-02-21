---
title: Espressioni condizionali del motore regole della rete CDN di Azure | Documentazione Microsoft
description: "Documentazione di riferimento per le funzionalità e condizioni di corrispondenza del motore regole della rete CDN di Azure."
services: cdn
documentationcenter: 
author: Lichard
manager: akucer
editor: 
ms.assetid: 669ef140-a6dd-4b62-9b9d-3f375a14215e
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: rli
ms.openlocfilehash: 57e56c38e003cb83dcf44f455c4451d159db8a59
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cdn-rules-engine-conditional-expressions"></a>Espressioni condizionali del motore regole della rete CDN
Questo argomento offre descrizioni dettagliate delle espressioni condizionali disponibili per il [motore regole](cdn-rules-engine.md) della rete per la distribuzione di contenuti (CDN, Content Delivery Network) di Azure.

La prima parte di una regola è l'espressione condizionale.

Espressione condizionale | DESCRIZIONE
-----------------------|-------------
IF | Un'espressione IF è sempre una parte della prima istruzione in una regola. Come tutte le altre espressioni condizionali, l'istruzione IF deve essere associata a una corrispondenza. Se non sono definite altre espressioni condizionali, questa corrispondenza determina il criterio che deve essere soddisfatto prima che sia possibile applicare un set di funzionalità a una richiesta.
AND IF | Un'espressione AND IF può essere aggiunta solo dopo i tipi di espressioni condizionali seguenti: IF e AND IF. Indica che esiste un'altra condizione che deve essere soddisfatta per l'istruzione IF iniziale.
ELSE IF| Un'espressione ELSE IF specifica una condizione alternativa che deve essere soddisfatta prima che venga eseguita una serie di funzionalità specifiche di questa istruzione ELSE IF. La presenza di un'istruzione ELSE IF indica la fine dell'istruzione precedente. L'unica espressione condizionale che può essere inserita dopo un'istruzione ELSE IF è un'altra istruzione ELSE IF. Ciò significa che un'istruzione ELSE IF può essere usata solo per specificare una sola condizione aggiuntiva da soddisfare.

**Esempio**: ![condizione di corrispondenza della rete CDN](./media/cdn-rules-engine-reference/cdn-rules-engine-conditional-expression.png)

 > [!TIP]
   > Una regola successiva potrebbe seguire l'override delle azioni specificate da una regola precedente. Esempio: una regola di catch-all protegge tutte le richieste tramite l'autenticazione basata su token. È possibile creare un'altra regola direttamente sotto questa per creare un'eccezione per alcuni tipi di richieste.

### <a name="next-steps"></a>Passaggi successivi
* [Panoramica della rete CDN di Azure](cdn-overview.md)
* [Informazioni di riferimento sul motore regole](cdn-rules-engine-reference.md)
* [Condizioni di corrispondenza del motore regole](cdn-rules-engine-reference-match-conditions.md)
* [Funzionalità del motore regole](cdn-rules-engine-reference-features.md)
* [Override del comportamento HTTP predefinito mediante il motore regole](cdn-rules-engine.md)
