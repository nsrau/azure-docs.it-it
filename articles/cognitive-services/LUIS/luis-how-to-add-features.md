---
title: Funzionalità-LUIS
titleSuffix: Azure Cognitive Services
description: Usare LUIS (Language Understanding) per aggiungere alle app funzionalità in grado di migliorare il rilevamento o la stima di finalità ed entità con cui creare categorie e criteri
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/17/2020
ms.author: diberry
ms.openlocfilehash: 7d9f1e520ab5c96b9c5288383324b165a4cd74f0
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/03/2020
ms.locfileid: "84344442"
---
# <a name="use-features-to-boost-signal-of-word-list"></a>Usare le funzionalità per aumentare il segnale dell'elenco di parole

È possibile aggiungere funzionalità alla propria app LUIS per migliorarne l'accuratezza. Le funzionalità consentono a LUIS di suggerire determinate parole e frasi che fanno parte del vocabolario del dominio dell'app.

Esaminare i [concetti](luis-concept-feature.md) per comprendere quando e perché usare una funzionalità di.

## <a name="add-phrase-list-as-a-feature"></a>Aggiungere un elenco di frasi come funzionalità

1. Accedere al [portale LUIS](https://www.luis.ai) e quindi selezionare la **sottoscrizione** e la **risorsa di creazione** per vedere le app assegnate a tale risorsa.
1. Per aprire l'app, selezionarne il nome nella pagina **app personali** .
1. Selezionare **Compila**, quindi selezionare **funzionalità** nel pannello a sinistra dell'app.

1. Nella pagina **funzionalità** selezionare **+ Crea**.

1. Nella finestra di dialogo **Crea nuova frase elenco** immettere un nome, ad esempio `Cities` . Nella casella **valore** immettere esempi di città, ad esempio `Seattle` . È possibile digitare un valore alla volta o un set di valori separati da virgole e quindi premere **INVIO**.

    > [!div class="mx-imgBorder"]
    > ![Screenshot della pagina relativa all'aggiunta di funzionalità (elenco di frasi)](./media/luis-add-features/add-phrase-list-cities.png)

    Dopo aver immesso i valori sufficienti per LUIS, vengono visualizzati i suggerimenti. È possibile **+ aggiungere tutti** i valori proposti oppure selezionare singoli termini.

1. Se le frasi possono essere utilizzate interscambiabili, è necessario verificare che **tali valori siano interscambiabili** .

1. L'elenco di frasi può essere applicato all'intera app con l'impostazione **globale** o a un modello specifico (finalità o entità). Se si crea l'elenco di frasi, come _funzionalità_ di uno scopo o di un'entità, l'interruttore non è impostato per il livello globale. In questo caso, il significato dell'interruttore è che la funzionalità è locale solo per tale modello, pertanto _non è globale_ per l'applicazione.

1. Selezionare **Operazione completata**. La nuova funzionalità viene aggiunta alla pagina delle **funzionalità di ml** .

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>


> [!Note]
> È possibile eliminare o disattivare un elenco di frasi dalla barra degli strumenti contestuale nella pagina delle **funzionalità di ml** .

## <a name="global-phrase-list-applies-to-entire-app"></a>L'elenco di frasi globali si applica all'intera app

Un elenco di frasi deve essere applicato allo scopo o all'entità a cui è destinato, ma in alcuni casi può essere necessario applicare un elenco di frasi all'intera app come funzionalità **globale** .

Nella pagina funzionalità ML selezionare l'elenco di frasi, quindi fare clic su **Rendi globale** nella barra degli strumenti contestuale superiore.

## <a name="model-as-a-feature"></a>Modello come funzionalità

Un'entità può essere una [funzionalità per uno scopo o un'entità](luis-concept-feature.md).

Per aggiungere un'entità come funzionalità a uno scopo, selezionare lo scopo dalla pagina Intent, quindi selezionare **+ Aggiungi funzionalità** sopra la barra degli strumenti contestuale. L'elenco includerà tutti gli elenchi di frasi e le entità che possono essere applicati come funzionalità.

Per aggiungere un'entità come funzionalità a un'altra entità, è possibile aggiungere la funzionalità nella pagina Dettagli finalità usando la [tavolozza delle entità](label-entity-example-utterance.md#adding-entity-as-a-feature-from-the-entity-palette) oppure è possibile [aggiungere la funzionalità](luis-how-to-add-entities.md#add-a-feature-to-a-machine-learned-entity) nella pagina dei dettagli dell'entità.

## <a name="next-steps"></a>Passaggi successivi

Dopo l'aggiunta, la modifica, l'eliminazione o la disattivazione di una funzionalità, eseguire [il training e il test dell'app](luis-interactive-test.md) per verificare se le prestazioni sono migliorate.
