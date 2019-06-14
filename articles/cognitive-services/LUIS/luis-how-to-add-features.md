---
title: Elenchi di frasi
titleSuffix: Language Understanding - Azure Cognitive Services
description: Usare LUIS (Language Understanding) per aggiungere alle app funzionalità in grado di migliorare il rilevamento o la stima di finalità ed entità con cui creare categorie e criteri
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/16/2019
ms.author: diberry
ms.openlocfilehash: 0723c3730ca0ae6325d828fbb5f41698cb807dd3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60195910"
---
# <a name="use-phrase-lists-to-boost-signal-of-word-list"></a>Usare gli elenchi di frasi in segnali boost dell'elenco di parole

È possibile aggiungere funzionalità alla propria app LUIS per migliorarne l'accuratezza. Le funzionalità consentono a LUIS di suggerire determinate parole e frasi che fanno parte del vocabolario del dominio dell'app. 

Un [elenco di frasi](luis-concept-feature.md) comprende un gruppo di valori (parole o frasi) che appartengono alla stessa classe e devono essere trattati in modo analogo (ad esempio i nomi di città o prodotti). Ciò che LUIS apprende in merito a uno di essi viene applicato automaticamente anche agli altri. L'elenco non è un'entità elenco chiusa (esatte corrispondenze di testo) di parole associate.

Viene integrato un elenco di frasi al vocabolario del dominio dell'applicazione come secondo segnale di LUIS in merito a tali parole.

## <a name="add-phrase-list"></a>Aggiungere un elenco di frasi

LUIS consente fino a 10 elenchi di frasi per ogni app. 

1. Aprire l'app facendo doppio clic sul nome nella pagina **My Apps** (App personali) e quindi fare clic su **Build** (Compila) e poi su **Phrase lists** (Elenchi di frasi) nel riquadro sinistro dell'app. 

2. Nella pagina **Phrase lists** (Elenchi di frasi) fare clic su **Create new phrase list** (Crea nuovo elenco di frasi). 
 
3. Nella finestra di dialogo **Add Phrase List** (Aggiungi elenco di frasi) digitare "Città" come nome dell'elenco. Nella casella **Value** (Valore) digitare i valori dell'elenco di frasi. È possibile digitare un valore alla volta o un set di valori separati da virgole e quindi premere **INVIO**.

    ![Aggiungere città all'elenco di frasi](./media/luis-add-features/add-phrase-list-cities.png)

4. LUIS è in grado di proporre valori correlati da aggiungere all'elenco di frasi. Fare clic su **Recommend** (Consiglia) per ottenere un gruppo di valori proposti che sono correlati a livello semantico ai valori aggiunti. È possibile fare clic su uno qualsiasi dei valori proposti o fare clic su **Add All** (Aggiungi tutti) per aggiungerli tutti.

    ![Valori proposti per l'elenco di frasi - Comando Add All (Aggiungi tutti)](./media/luis-add-features/related-values.png)

5. Fare clic su **These values are interchangeable** (Questi valori sono intercambiabili) se i valori dell'elenco di frasi aggiunti sono alternative che possono essere usate indifferentemente.

    ![Valori proposti per l'elenco di frasi - Casella per la selezione di valori intercambiabili](./media/luis-add-features/interchangeable.png)

6. Fare clic su **Save**. L'elenco di frasi "Città" verrà aggiunto alla pagina **Phrase lists** (Elenchi di frasi).

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>

> [!Note]
> È possibile eliminare o disattivare un elenco di frasi nella barra degli strumenti contestuale nella pagina **Phrase lists** (Elenchi di frasi).

## <a name="next-steps"></a>Passaggi successivi

Dopo avere aggiunto, modificato, eliminato o disattivato un elenco di frasi, [eseguire il training e i test sull'app](luis-interactive-test.md) per vedere se le prestazioni migliorano.
