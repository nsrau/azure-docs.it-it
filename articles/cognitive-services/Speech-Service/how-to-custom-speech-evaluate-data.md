---
title: Valutare l'accuratezza per il riconoscimento vocale personalizzato - Servizio di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: "In questo documento imparerai a misurare quantitativamente la qualità del nostro modello di sintesi vocale o del tuo modello personalizzato. Audio: i dati di trascrizione con etichetta umana sono necessari per testare l'accuratezza e devono essere forniti da 30 minuti a 5 ore di audio rappresentativo."
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: f710b8bfdd4dcfd3b7a63aa0b457036ab7037016
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "74806097"
---
# <a name="evaluate-custom-speech-accuracy"></a>Valutare la precisione del riconoscimento vocale personalizzato

In questo documento imparerai a misurare quantitativamente la qualità del modello di sintesi vocale di Microsoft o del modello personalizzato. Audio: i dati di trascrizione con etichetta umana sono necessari per testare l'accuratezza e devono essere forniti da 30 minuti a 5 ore di audio rappresentativo.

## <a name="what-is-word-error-rate-wer"></a>Che cos'è il tasso di errore di parola (WER)?

Lo standard di settore per misurare l'accuratezza del modello è *Word Error Rate* (WER). WER conta il numero di parole errate identificate durante il riconoscimento, quindi divide per il numero totale di parole fornite nella trascrizione con etichetta umana. Infine, tale numero viene moltiplicato per 100% per calcolare il WER.

![Formula WER](./media/custom-speech/custom-speech-wer-formula.png)

Le parole identificate in modo errato rientrano in tre categorie:

* Inserimento (I): parole aggiunte in modo errato nella trascrizione dell'ipotesi
* Eliminazione (D): parole non rilevate nella trascrizione dell'ipotesi
* Sostituzione (S): parole che sono state sostituite tra riferimento e ipotesi

Ad esempio:

![Esempio di parole identificate in modo errato](./media/custom-speech/custom-speech-dis-words.png)

## <a name="resolve-errors-and-improve-wer"></a>Risolvere gli errori e migliorare WER

È possibile utilizzare il WER dai risultati del riconoscimento macchina per valutare la qualità del modello in uso con l'app, lo strumento o il prodotto. Un WER del 5%-10% è considerato di buona qualità ed è pronto all'uso. Un WER del 20% è accettabile, tuttavia si consiglia di prendere in considerazione una formazione aggiuntiva. Un WER del 30% o più segnala una qualità scadente e richiede personalizzazione e formazione.

La modalità di distribuzione degli errori è importante. Quando si verificano molti errori di cancellazione, di solito è a causa della scarsa potenza del segnale audio. Per risolvere questo problema, è necessario raccogliere i dati audio più vicino alla fonte. Errori di inserimento significano che l'audio è stato registrato in un ambiente rumoroso e crosstalk potrebbe essere presente, causando problemi di riconoscimento. Gli errori di sostituzione si verificano spesso quando è stato fornito un campione insufficiente di termini specifici del dominio come trascrizioni con etichetta umana o testo correlato.

Analizzando i singoli file, è possibile determinare il tipo di errori esistenti e gli errori univoci per un file specifico. Comprendere i problemi a livello di file ti aiuterà a scegliere come target i miglioramenti.

## <a name="create-a-test"></a>Creare un testCreate a test

Se si desidera testare la qualità del modello di linea di base di sintesi vocale di Microsoft o di un modello personalizzato di cui è stato eseguito il training, è possibile confrontare due modelli affiancati per valutare l'accuratezza. Il confronto include il WER e i risultati del riconoscimento. In genere, un modello personalizzato viene confrontato con il modello di base di Microsoft.Typically, a custom model is compared with Microsoft's baseline model.

Per valutare i modelli affiancati:

1. Accedere al [portale di riconoscimento vocale personalizzato](https://speech.microsoft.com/customspeech).
2. Passare a **Sintesi vocale > sintesi vocale personalizzato > Testing**.
3. Fare clic su **Aggiungi test**.
4. Selezionare **Valuta precisione**. Assegnare al test un nome, una descrizione e selezionare il set di dati di trascrizione audio e con etichetta umana.
5. Seleziona fino a due modelli che desideri testare.
6. Fare clic su **Crea**.

Dopo aver creato correttamente il test, è possibile confrontare i risultati affiancati.

## <a name="side-by-side-comparison"></a>Confronto affiancato

Una volta completato il test, indicato dalla modifica dello stato *in Riuscito*, troverai un numero WER per entrambi i modelli inclusi nel test. Fare clic sul nome del test per visualizzare la pagina dei dettagli del test. In questa pagina dettaglio sono elencate tutte le espressioni nel set di dati, indicando i risultati del riconoscimento dei due modelli insieme alla trascrizione del set di dati inviato. Per controllare il confronto affiancato, è possibile attivare o disattivare vari tipi di errore, tra cui inserimento, eliminazione e sostituzione. Ascoltando l'audio e confrontando i risultati del riconoscimento in ogni colonna, che mostra la trascrizione con etichetta umana e i risultati per due modelli di sintesi vocale, è possibile decidere quale modello soddisfa le proprie esigenze e dove sono disponibili ulteriori corsi di formazione e miglioramenti Obbligatorio.

## <a name="next-steps"></a>Passaggi successivi

* [Eseguire il training del modello](how-to-custom-speech-train-model.md)
* [Distribuire il modello](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>Risorse aggiuntive

* [Preparare e testare i dati](how-to-custom-speech-test-data.md)
* [Ispezionare i dati](how-to-custom-speech-inspect-data.md)
