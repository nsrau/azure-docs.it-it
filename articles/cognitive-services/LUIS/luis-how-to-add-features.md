---
title: Elenchi di frasi per migliorare il rilevamento di entità
titleSuffix: Azure Cognitive Services
description: Usare LUIS (Language Understanding) per aggiungere alle app funzionalità in grado di migliorare il rilevamento o la stima di finalità ed entità con cui creare categorie e criteri
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/06/2018
ms.author: diberry
ms.openlocfilehash: 6831f67a33d114ca5c42ddacf8ef4de704e21711
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "47036957"
---
# <a name="use-phrase-lists-to-boost-signal-of-word-list"></a>Usare gli elenchi di frasi in segnali boost dell'elenco di parole

È possibile aggiungere funzionalità alla propria app LUIS per migliorarne l'accuratezza. Le funzionalità consentono a LUIS di suggerire determinate parole e frasi che fanno parte del vocabolario del dominio dell'app. 

## <a name="add-phrase-list"></a>Aggiungere un elenco di frasi

1. Aprire l'app facendo doppio clic sul nome nella pagina **My Apps** (App personali) e quindi fare clic su **Build** (Compila) e poi su **Phrase lists** (Elenchi di frasi) nel riquadro sinistro dell'app. 

2. Nella pagina **Phrase lists** (Elenchi di frasi) fare clic su **Create new phrase list** (Crea nuovo elenco di frasi). 
 
3. Nella finestra di dialogo **Add Phrase List** (Aggiungi elenco di frasi) digitare "Città" come nome dell'elenco. Nella casella **Value** (Valore) digitare i valori dell'elenco di frasi. È possibile digitare un valore alla volta o un set di valori separati da virgole e quindi premere **INVIO**.

    ![Aggiungere città all'elenco di frasi](./media/luis-add-features/add-phrase-list-cities.png)

4. LUIS è in grado di proporre valori correlati da aggiungere all'elenco di frasi. Fare clic su **Recommend** (Consiglia) per ottenere un gruppo di valori proposti che sono correlati a livello semantico ai valori aggiunti. È possibile fare clic su uno qualsiasi dei valori proposti o fare clic su **Add All** (Aggiungi tutti) per aggiungerli tutti.

    ![Valori proposti per l'elenco di frasi](./media/luis-add-features/related-values.png)

5. Fare clic su **These values are interchangeable** (Questi valori sono intercambiabili) se i valori dell'elenco di frasi aggiunti sono alternative che possono essere usate indifferentemente.

    ![Valori proposti per l'elenco di frasi](./media/luis-add-features/interchangeable.png)

6. Fare clic su **Save**. L'elenco di frasi "Città" verrà aggiunto alla pagina **Phrase lists** (Elenchi di frasi).

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>

> [!Note]
> È possibile modificare, eliminare o disattivare un elenco di frasi dal pulsante dei puntini di sospensione (***...***) alla fine della riga di ogni elenco di frasi.

## <a name="pattern-regular-expression-feature"></a>Funzioni di criterio (espressione regolare) 
**Questa funzionalità è deprecata**. Non è possibile aggiungere nuove funzionalità di criteri a LUIS. Tutte le funzionalità di criteri esistenti sono supportate fino a maggio 2018. Contribuire all'associazione di espressioni regolari di LUIS standard con un PR nel [repository GitHub Recognizers-Text](https://github.com/Microsoft/Recognizers-Text). 

## <a name="next-steps"></a>Passaggi successivi

Dopo avere aggiunto, modificato, eliminato o disattivato un elenco di frasi, [eseguire il training e i test sull'app](luis-interactive-test.md) per vedere se le prestazioni migliorano.
