---
title: Controllare la qualità dei dati per il riconoscimento vocale personalizzato - servizi di riconoscimento vocale
titlesuffix: Azure Cognitive Services
description: Riconoscimento vocale personalizzato fornisce strumenti che consentono di controllare visivamente la qualità di riconoscimento di un modello tramite il confronto dei dati audio con il risultato del riconoscimento corrispondente. Dal portale di riconoscimento vocale personalizzato, è possibile riprodurre l'audio caricato e determinare se il risultato del riconoscimento fornito sia corretto.  Questo strumento consente di analizzare rapidamente di qualità del modello di riconoscimento vocale della linea di base di Microsoft o un modello con training personalizzato senza la necessità di trascrivere i dati audio.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 900d9b032b4ed121589d904a8ad18059b3283661
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67603112"
---
# <a name="inspect-custom-speech-data"></a>Esaminare i dati di riconoscimento vocale personalizzato

> [!NOTE]
> Qui si presume che l'utente abbia letto [prepara i dati di test per riconoscimento vocale personalizzato](how-to-custom-speech-test-data.md) e avere caricato un set di dati per l'ispezione.

Riconoscimento vocale personalizzato fornisce strumenti che consentono di controllare visivamente la qualità di riconoscimento di un modello tramite il confronto dei dati audio con il risultato del riconoscimento corrispondente. Dal portale di riconoscimento vocale personalizzato, è possibile riprodurre l'audio caricato e determinare se il risultato del riconoscimento fornito sia corretto. Questo strumento consente di analizzare rapidamente di qualità del modello di riconoscimento vocale della linea di base di Microsoft o un modello con training personalizzato senza la necessità di trascrivere i dati audio.

In questo documento verrà illustrato come esaminare visivamente la qualità di un modello utilizzando i dati di training che è stato caricato in precedenza.

In questa pagina, verrà illustrato come esaminare visivamente la qualità del modello di riconoscimento vocale della linea di base di Microsoft e/o un modello personalizzato che è stato eseguito il training. Si userà i dati caricati per il **dati** scheda per il test.

## <a name="create-a-test"></a>Creare un test

Seguire queste istruzioni per creare un test:

1. Passare a **vocale-> riconoscimento vocale personalizzato > test**.
2. Fare clic su **aggiungere Test**.
3. Selezionare **ispezionare qualità (dati Audio-only)** . Assegnare un nome, descrizione, il test e selezionare il set di dati audio.
4. Selezionare fino a due modelli che si desidera testare.
5. Fare clic su **Create**(Crea).

Dopo aver creato correttamente un test, è possibile confrontare i modelli affiancati.

## <a name="side-by-side-model-comparisons"></a>Confronti di modello side-by-side

Quando lo stato del test è *Succeeded*, fare clic sul nome di elemento di test per visualizzare i dettagli del test. Questa pagina di dettaglio Elenca tutte le espressioni nel set di dati, che indica i risultati di riconoscimento dei due modelli insieme la trascrizione del set di dati inviati.

Per consentire di controllare il confronto side-by-side, è possibile passare diversi tipi di errore, tra cui inserimento, eliminazione e sostituzione. Ascoltare l'audio e confrontando i risultati del riconoscimento in ogni colonna (rappresentato da un'etichetta umane trascrizione e i risultati dei due modelli di riconoscimento vocale), è possibile decidere quale modello soddisfa le proprie esigenze e in cui sono necessari miglioramenti.

Controllo qualità di test è utile per verificare se la qualità di un endpoint di riconoscimento vocale è sufficiente per un'applicazione.  Per una misura obiettiva di accuratezza, che richiedono trascritto audio, seguire le istruzioni disponibili nel [valutare accuratezza](how-to-custom-speech-evaluate-data.md).

## <a name="next-steps"></a>Passaggi successivi

* [Valutare i dati](how-to-custom-speech-evaluate-data.md)
* [Il training del modello](how-to-custom-speech-train-model.md)
* [Distribuire il modello](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>Risorse aggiuntive

* [Preparare i dati di test per riconoscimento vocale personalizzato](how-to-custom-speech-test-data.md)
