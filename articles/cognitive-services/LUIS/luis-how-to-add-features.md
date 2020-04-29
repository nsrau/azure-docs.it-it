---
title: Descrittori-LUIS
titleSuffix: Azure Cognitive Services
description: Usare LUIS (Language Understanding) per aggiungere alle app funzionalità in grado di migliorare il rilevamento o la stima di finalità ed entità con cui creare categorie e criteri
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/02/2020
ms.author: diberry
ms.openlocfilehash: 7560fdcbfc77ea2655e8af641794478ead4c11c7
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80631455"
---
# <a name="use-descriptors-to-boost-signal-of-word-list"></a>Usare i descrittori per aumentare il segnale dell'elenco di parole

È possibile aggiungere funzionalità alla propria app LUIS per migliorarne l'accuratezza. Le funzionalità consentono a LUIS di suggerire determinate parole e frasi che fanno parte del vocabolario del dominio dell'app.

Un [descrittore](luis-concept-feature.md) (elenco di frasi) include un gruppo di valori (parole o frasi) che appartengono alla stessa classe e che devono essere trattati in modo analogo (ad esempio, nomi di città o prodotti). Ciò che LUIS apprende in merito a uno di essi viene applicato automaticamente anche agli altri. Questo elenco non corrisponde a un' [entità di elenco](reference-entity-list.md) (corrispondenze esatte del testo) di parole corrispondenti.

Un descrittore aggiunge al vocabolario del dominio dell'applicazione come secondo segnale a LUIS su tali parole.

Esaminare i [concetti relativi alle funzionalità](luis-concept-feature.md) per comprendere quando e perché usare un descrittore.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="add-descriptor"></a>Aggiungi descrittore

1. Aprire l'app facendo clic sul relativo nome nella pagina **app personali** e quindi su **Compila**, quindi su **descrittori** nel pannello a sinistra dell'app.

1. Nella pagina **descrittori** fare clic su **+ Aggiungi descrittore**.

1. Nella finestra di dialogo **Crea nuova frase elenco descrittore** immettere un nome, `Cities` ad esempio per il descrittore. Nella casella **valore** Digitare i valori dei descrittori, ad esempio `Seattle`. È possibile digitare un valore alla volta o un set di valori separati da virgole e quindi premere **INVIO**.

    > [!div class="mx-imgBorder"]
    > ![Aggiungi città descrittore](./media/luis-add-features/add-phrase-list-cities.png)

    Dopo aver immesso i valori sufficienti per LUIS, vengono visualizzati i suggerimenti. È possibile **+ aggiungere tutti** i valori proposti oppure selezionare singoli termini.

1. Se i valori del descrittore aggiunto sono alternative che possono essere usati in modo intercambiabile, è necessario tenere **questi valori** .

1. L'elenco di frasi può essere applicato all'intera app con l'impostazione **globale** o a un modello specifico (finalità o entità). Se si crea l'elenco di frasi, come _descrittore_ da uno scopo o da un'entità, l'interruttore viene impostato su non globale. In questo caso, il significato dell'elemento interruttore è che il descrittore è una funzionalità solo per tale modello, pertanto _non è globale_ per l'applicazione.

1. Seleziona **Chiudi**. Il nuovo descrittore viene aggiunto **alla pagina descrittori** .

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>

> [!Note]
> È possibile eliminare o disattivare un descrittore dalla barra degli strumenti contestuale nella pagina **descrittori** .

## <a name="next-steps"></a>Passaggi successivi

Dopo l'aggiunta, la modifica, l'eliminazione o la disattivazione di un descrittore, eseguire [il training e il test dell'app](luis-interactive-test.md) per verificare se le prestazioni sono migliori.
