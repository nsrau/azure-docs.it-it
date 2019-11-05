---
title: Elenchi di frasi-LUIS
titleSuffix: Azure Cognitive Services
description: Usare LUIS (Language Understanding) per aggiungere alle app funzionalità in grado di migliorare il rilevamento o la stima di finalità ed entità con cui creare categorie e criteri
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: diberry
ms.openlocfilehash: 0e3e4226eaaa0505eea96d8b3aca820f2327349e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467625"
---
# <a name="use-phrase-lists-to-boost-signal-of-word-list"></a>Usare gli elenchi di frasi in segnali boost dell'elenco di parole

È possibile aggiungere funzionalità alla propria app LUIS per migliorarne l'accuratezza. Le funzionalità consentono a LUIS di suggerire determinate parole e frasi che fanno parte del vocabolario del dominio dell'app. 

Un [elenco di frasi](luis-concept-feature.md) comprende un gruppo di valori (parole o frasi) che appartengono alla stessa classe e devono essere trattati in modo analogo (ad esempio i nomi di città o prodotti). Ciò che LUIS apprende in merito a uno di essi viene applicato automaticamente anche agli altri. Questo elenco non corrisponde a un' [entità di elenco](reference-entity-list.md) (corrispondenze esatte del testo) di parole corrispondenti.

Viene integrato un elenco di frasi al vocabolario del dominio dell'applicazione come secondo segnale di LUIS in merito a tali parole.

Esaminare i [concetti relativi alle funzionalità](luis-concept-feature.md) per comprendere quando e perché usare un elenco di frasi. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="add-phrase-list"></a>Aggiungere un elenco di frasi

LUIS consente fino a 10 elenchi di frasi per ogni app. 

1. Aprire l'app facendo doppio clic sul nome nella pagina **My Apps** (App personali) e quindi fare clic su **Build** (Compila) e poi su **Phrase lists** (Elenchi di frasi) nel riquadro sinistro dell'app. 

1. Nella pagina **Phrase lists** (Elenchi di frasi) fare clic su **Create new phrase list** (Crea nuovo elenco di frasi). 
 
1. Nella finestra di dialogo **Aggiungi elenco di frasi** Digitare `Cities` come nome dell'elenco di frasi. Nella casella **Value** (Valore) digitare i valori dell'elenco di frasi. È possibile digitare un valore alla volta o un set di valori separati da virgole e quindi premere **INVIO**.

    ![Aggiungere città all'elenco di frasi](./media/luis-add-features/add-phrase-list-cities.png)

1. LUIS è in grado di proporre valori correlati da aggiungere all'elenco di frasi. Fare clic su **Recommend** (Consiglia) per ottenere un gruppo di valori proposti che sono correlati a livello semantico ai valori aggiunti. È possibile fare clic su uno qualsiasi dei valori proposti o fare clic su **Add All** (Aggiungi tutti) per aggiungerli tutti.

    ![Valori proposti per l'elenco di frasi - Comando Add All (Aggiungi tutti)](./media/luis-add-features/related-values.png)

1. Fare clic su **These values are interchangeable** (Questi valori sono intercambiabili) se i valori dell'elenco di frasi aggiunti sono alternative che possono essere usate indifferentemente.

    ![Valori proposti per l'elenco di frasi - Casella per la selezione di valori intercambiabili](./media/luis-add-features/interchangeable.png)

1. Fare clic su **Done**. L'elenco di frasi "Città" verrà aggiunto alla pagina **Phrase lists** (Elenchi di frasi).

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>

> [!Note]
> È possibile eliminare o disattivare un elenco di frasi nella barra degli strumenti contestuale nella pagina **Phrase lists** (Elenchi di frasi).

## <a name="next-steps"></a>Passaggi successivi

Dopo avere aggiunto, modificato, eliminato o disattivato un elenco di frasi, [eseguire il training e i test sull'app](luis-interactive-test.md) per vedere se le prestazioni migliorano.
