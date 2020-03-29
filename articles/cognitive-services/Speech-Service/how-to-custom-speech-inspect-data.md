---
title: Controllare la qualità dei dati per il riconoscimento vocale personalizzato - Servizio di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Il riconoscimento vocale personalizzato fornisce strumenti che consentono di controllare visivamente la qualità di riconoscimento di un modello confrontando i dati audio con il risultato del riconoscimento corrispondente. È possibile riprodurre l'audio caricato e determinare se il risultato del riconoscimento fornito è corretto.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: erhopf
ms.openlocfilehash: 60b415f69800885f37ee53cc4f090098f5ebf1f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "74806080"
---
# <a name="inspect-custom-speech-data"></a>Esaminare i dati vocali personalizzati

> [!NOTE]
> In questa pagina si presuppone che sia stata letta Preparare i dati di test per la sintesi [vocale personalizzata](how-to-custom-speech-test-data.md) e che sia stato caricato un set di dati per l'ispezione.

Il riconoscimento vocale personalizzato fornisce strumenti che consentono di controllare visivamente la qualità di riconoscimento di un modello confrontando i dati audio con il risultato del riconoscimento corrispondente. Dal [portale di riconoscimento personalizzato](https://speech.microsoft.com/customspeech), è possibile riprodurre l'audio caricato e determinare se il risultato del riconoscimento fornito è corretto. Questo strumento consente di controllare rapidamente la qualità del modello di sintesi vocale di Microsoft o di un modello personalizzato di cui è stato eseguito il training senza dover trascrivere dati audio.

In questo documento verrà illustrato come controllare visivamente la qualità di un modello usando i dati di training caricati in precedenza.

In questa pagina imparerai a controllare visivamente la qualità del modello di sintesi vocale di base di Microsoft e/o di un modello personalizzato di cui hai eseguito il training. Utilizzerai i dati caricati nella scheda **Dati** per il test.

## <a name="create-a-test"></a>Creare un testCreate a test

Seguire queste istruzioni per creare un test:Follow these instructions to create a test:

1. Accedere al [portale di riconoscimento vocale personalizzato](https://speech.microsoft.com/customspeech).
2. Passare a **Sintesi vocale > sintesi vocale personalizzato > Testing**.
3. Fare clic su **Aggiungi test**.
4. Selezionare **Controlla qualità (dati solo audio)**. Assegnare al test un nome, una descrizione e selezionare il set di dati audio.
5. Seleziona fino a due modelli che desideri testare.
6. Fare clic su **Crea**.

Dopo aver creato correttamente un test, è possibile confrontare i modelli affiancati.

[!INCLUDE [service-pricing-advisory](includes/service-pricing-advisory.md)]

## <a name="side-by-side-model-comparisons"></a>Confronti di modelli affiancati

Quando lo stato del test è _Riuscito_, fare clic sul nome dell'elemento di test per visualizzare i dettagli del test. In questa pagina dettaglio sono elencate tutte le espressioni nel set di dati, indicando i risultati del riconoscimento dei due modelli insieme alla trascrizione del set di dati inviato.

Per controllare il confronto affiancato, è possibile attivare o disattivare vari tipi di errore, tra cui inserimento, eliminazione e sostituzione. Ascoltando l'audio e confrontando i risultati del riconoscimento in ogni colonna (che mostra la trascrizione con etichetta umana e i risultati di due modelli di sintesi vocale), è possibile decidere quale modello soddisfa le proprie esigenze e dove sono necessari miglioramenti.

L'ispezione dei test di qualità è utile per verificare se la qualità di un endpoint di riconoscimento vocale è sufficiente per un'applicazione. Per una misura oggettiva della precisione, che richiede l'audio trascritto, seguire le istruzioni riportate in [Valutare l'accuratezza](how-to-custom-speech-evaluate-data.md).

## <a name="next-steps"></a>Passaggi successivi

- [Valutare i dati](how-to-custom-speech-evaluate-data.md)
- [Eseguire il training del modello](how-to-custom-speech-train-model.md)
- [Distribuire il modello](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>Risorse aggiuntive

- [Preparare i dati di test per la sintesi vocale personalizzataPrepare test data for Custom Speech](how-to-custom-speech-test-data.md)
