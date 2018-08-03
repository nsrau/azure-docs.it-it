---
title: Aggiungere funzionalità alle applicazioni LUIS | Microsoft Docs
description: Usare LUIS (Language Understanding) per aggiungere alle app funzionalità in grado di migliorare il rilevamento o la stima di finalità ed entità con cui creare categorie e criteri
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/30/2018
ms.author: diberry
ms.openlocfilehash: 5ec75436c7df5c08f5507794229bec1f9adb2804
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2018
ms.locfileid: "39222954"
---
# <a name="use-features-to-improve-your-luis-apps-performance"></a>Usare funzionalità per migliorare le prestazioni dell'app LUIS  

È possibile aggiungere funzionalità alla propria app LUIS per migliorarne l'accuratezza. Le funzionalità contribuiscono al corretto funzionamento di LUIS suggerendo che determinate parole e frasi fanno parte di una categoria. Se LUIS impara a riconoscere un membro della categoria, può gestire gli altri membri in modo analogo.

## <a name="add-phrase-list"></a>Aggiungere un elenco di frasi

1. Aprire l'app facendo doppio clic sul nome nella pagina **My Apps** (App personali) e quindi fare clic su **Build** (Compila) e poi su **Phrase lists** (Elenchi di frasi) nel riquadro sinistro dell'app. 

    ![Navigazione nell'elenco di frasi](./media/luis-add-features/phrase-list-nav.png)

2. Nella pagina **Phrase lists** (Elenchi di frasi) fare clic su **Create new phrase list** (Crea nuovo elenco di frasi). 
 
    ![Creare un nuovo elenco di frasi](./media/luis-add-features/create-new-phrase-list.png)
    
3. Nella finestra di dialogo **Add Phrase List** (Aggiungi elenco di frasi) digitare "Città" come nome dell'elenco. Nella casella **Value** (Valore) digitare i valori dell'elenco di frasi. È possibile digitare un valore alla volta o un set di valori separati da virgole e quindi premere **INVIO**.

    ![Aggiungere città all'elenco di frasi](./media/luis-add-features/add-phrase-list-cities.png)

4. LUIS è in grado di proporre valori correlati da aggiungere all'elenco di frasi. Fare clic su **Recommend** (Consiglia) per ottenere un gruppo di valori proposti che sono correlati a livello semantico ai valori aggiunti. È possibile fare clic su uno qualsiasi dei valori proposti o fare clic su **Add All** (Aggiungi tutti) per aggiungerli tutti.

    ![Valori proposti per l'elenco di frasi](./media/luis-add-features/related-values.png)

5. Fare clic su **These values are interchangeable** (Questi valori sono intercambiabili) se i valori dell'elenco di frasi aggiunti sono alternative che possono essere usate indifferentemente.

    ![Valori proposti per l'elenco di frasi](./media/luis-add-features/interchangeable.png)

6. Fare clic su **Save**. L'elenco di frasi "Città" verrà aggiunto alla pagina **Phrase lists** (Elenchi di frasi).

    ![Elenco di frasi aggiunto](./media/luis-add-features/phrase-list-cities.png)

## <a name="edit-phrase-list"></a>Modificare un elenco di frasi

Fare clic sul nome dell'elenco di frasi nella pagina **Phrase lists** (Elenchi di frasi). Nella finestra di dialogo **Edit Phrase List** (Modifica elenco di frasi) che viene visualizzata apportare le modifiche necessarie e quindi fare clic su **Save** (Salva).

 ![Elenco di frasi aggiunto](./media/luis-add-features/edit-phrase-list.png)

## <a name="delete-phrase-list"></a>Eliminare un elenco di frasi 

Fare clic sui puntini di sospensione (***...*** ) alla fine della riga e selezionare **Delete** (Elimina).

 ![Eliminare l'elenco aggiunto](./media/luis-add-features/delete-phrase-list.png)

## <a name="deactivate-phrase-list"></a>Disattivare l'elenco di frasi 

Fare clic sui puntini di sospensione (***...*** ) alla fine della riga e selezionare **Deactivate** (Disattiva).

 ![Disattivare l'elenco aggiunto](./media/luis-add-features/deactivate-phrase-list.png)

## <a name="pattern-regular-expression-feature"></a>Funzioni di criterio (espressione regolare) 
**Questa funzionalità è deprecata**. Non è possibile aggiungere nuove funzionalità di criteri a LUIS. Tutte le funzionalità di criteri esistenti sono supportate fino a maggio 2018. Contribuire all'associazione di espressioni regolari di LUIS standard con un PR nel [repository GitHub Recognizers-Text](https://github.com/Microsoft/Recognizers-Text). 

## <a name="next-steps"></a>Passaggi successivi

Dopo avere aggiunto, modificato, eliminato o disattivato un elenco di frasi, [eseguire il training e i test sull'app](luis-interactive-test.md) per vedere se le prestazioni migliorano.
