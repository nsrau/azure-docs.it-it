---
title: Descrittori - LUIS
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
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631455"
---
# <a name="use-descriptors-to-boost-signal-of-word-list"></a>Utilizzare i descrittori per aumentare il segnale dell'elenco di parole

È possibile aggiungere funzionalità alla propria app LUIS per migliorarne l'accuratezza. Le funzionalità consentono a LUIS di suggerire determinate parole e frasi che fanno parte del vocabolario del dominio dell'app.

Un [descrittore](luis-concept-feature.md) (elenco di frasi) include un gruppo di valori (parole o frasi) che appartengono alla stessa classe e devono essere trattati in modo analogo (ad esempio, nomi di città o prodotti). Ciò che LUIS apprende in merito a uno di essi viene applicato automaticamente anche agli altri. Questo elenco non è la stessa cosa di [un'entità di elenco](reference-entity-list.md) (corrispondenze di testo esatte) di parole corrispondenti.

Un descrittore aggiunge al vocabolario del dominio applicazione come secondo segnale a LUIS su tali parole.

Esaminare i [concetti relativi](luis-concept-feature.md) alle funzionalità per comprendere quando e perché utilizzare un descrittore.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="add-descriptor"></a>Aggiungi descrittore

1. Aprire l'app facendo clic sul **relativo** nome nella pagina App personali e quindi su **Compila**, quindi su **Descrittori** nel riquadro sinistro dell'app.

1. Nella pagina **Descrittori** , fare clic su **Aggiungi descrittore**.

1. Nella finestra di dialogo **Crea nuovo descrittore dell'elenco** di frasi, immettere un nome, ad `Cities` esempio per il descrittore. Nella casella **Valore** digitare i valori dei `Seattle`descrittori, ad esempio . È possibile digitare un valore alla volta o un set di valori separati da virgole e quindi premere **INVIO**.

    > [!div class="mx-imgBorder"]
    > ![Aggiungi descrittore Città](./media/luis-add-features/add-phrase-list-cities.png)

    Dopo aver immesso un numero sufficiente di valori per LUIS, vengono visualizzati suggerimenti. È possibile **aggiungere tutti i** valori proposti o selezionare singoli termini.

1. Mantieni **Questi valori sono intercambiabili** controllati se i valori del descrittore aggiunto sono alternative che possono essere utilizzate in modo intercambiabile.

1. L'elenco di frasi può essere applicato all'intera app con l'impostazione **Globale** o a un modello specifico (intento o entità). Se si crea l'elenco di frasi, come _descrittore_ da una finalità o un'entità, l'interruttore è impostato su non globale. In questo caso, il significato dell'interruttore è che il descrittore è una funzionalità solo per quel modello, pertanto, _non globale_ per l'applicazione.

1. Selezionare **Fatto**. Il nuovo descrittore viene aggiunto alla pagina **Descrittori.**

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>

> [!Note]
> È possibile eliminare o disattivare un descrittore dalla barra degli strumenti contestuale nella pagina **Descrittori.**

## <a name="next-steps"></a>Passaggi successivi

Dopo aver aggiunto, modificato, eliminato o disattivato un descrittore, [addestrare e testare nuovamente l'app](luis-interactive-test.md) per verificare se le prestazioni migliorano.
