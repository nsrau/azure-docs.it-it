---
title: Valutazione dell'accuratezza per il servizio di Riconoscimento vocale personalizzato vocale
titleSuffix: Azure Cognitive Services
description: In questo documento si apprenderà come misurare quantitativamente la qualità del modello di riconoscimento vocale o del modello personalizzato. Per il test dell'accuratezza sono necessari dati di trascrizione audio e con etichetta umana. è necessario fornire da 30 minuti a 5 ore di audio rappresentativo.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: f710b8bfdd4dcfd3b7a63aa0b457036ab7037016
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806097"
---
# <a name="evaluate-custom-speech-accuracy"></a>Valuta Riconoscimento vocale personalizzato accuratezza

In questo documento si apprenderà come misurare quantitativamente la qualità del modello di sintesi vocale di Microsoft o del modello personalizzato. Per il test dell'accuratezza sono necessari dati di trascrizione audio e con etichetta umana. è necessario fornire da 30 minuti a 5 ore di audio rappresentativo.

## <a name="what-is-word-error-rate-wer"></a>Che cos'è la frequenza degli errori di parola?

Lo standard di settore per misurare l'accuratezza del modello è la *frequenza degli errori di Word* (WER). WER conta il numero di parole non corrette identificate durante il riconoscimento, quindi divide in base al numero totale di parole fornite nella trascrizione con etichetta umana. Infine, tale numero viene moltiplicato per il 100% per calcolare il WER.

![Formula WER](./media/custom-speech/custom-speech-wer-formula.png)

Le parole identificate in modo errato rientrano in tre categorie:

* Inserimento (I): parole erroneamente aggiunte nella trascrizione di ipotesi
* Eliminazione (D): parole che non sono state rilevate nella trascrizione dell'ipotesi
* Sostituzioni: parole sostituite tra i riferimenti e le ipotesi

Ecco un esempio:

![Esempio di parole erroneamente identificate](./media/custom-speech/custom-speech-dis-words.png)

## <a name="resolve-errors-and-improve-wer"></a>Risolvere gli errori e migliorare WER

È possibile usare il WER dai risultati del riconoscimento del computer per valutare la qualità del modello usato con l'app, lo strumento o il prodotto. Un WER del 5%-10% è considerato una qualità ottima ed è pronto per l'uso. Un WER del 20% è accettabile, tuttavia è opportuno prendere in considerazione una formazione aggiuntiva. Un numero di messaggi pari al 30% indica una scarsa qualità e richiede la personalizzazione e la formazione.

Il modo in cui vengono distribuiti gli errori è importante. Quando vengono rilevati molti errori di eliminazione, è in genere dovuto a un livello di attendibilità del segnale audio debole. Per risolvere questo problema, è necessario raccogliere i dati audio più vicino all'origine. Gli errori di inserimento indicano che l'audio è stato registrato in un ambiente rumoroso ed è possibile che sia presente l'area interattiva, causando problemi di riconoscimento. Gli errori di sostituzione vengono spesso rilevati quando un campione insufficiente di termini specifici del dominio è stato fornito come trascrizioni con etichetta umana o testo correlato.

Analizzando i singoli file è possibile determinare il tipo di errore e gli errori che sono univoci per un file specifico. La comprensione dei problemi a livello di file consentirà di individuare i miglioramenti.

## <a name="create-a-test"></a>Creare un test

Se si vuole testare la qualità del modello di base di sintesi vocale di Microsoft o di un modello personalizzato che è stato sottoposto a training, è possibile confrontare due modelli affiancati per valutare l'accuratezza. Il confronto include i risultati di WER e il riconoscimento. In genere, un modello personalizzato viene confrontato con il modello di base di Microsoft.

Per valutare i modelli side-by-Side:

1. Accedere al portale di [riconoscimento vocale personalizzato](https://speech.microsoft.com/customspeech).
2. Passare a **riconoscimento vocale > Riconoscimento vocale personalizzato > test**.
3. Fare clic su **Aggiungi test**.
4. Selezionare **valuta accuratezza**. Assegnare al test un nome, una descrizione e selezionare il set di dati di trascrizione audio + con etichetta umana.
5. Selezionare fino a due modelli che si desidera testare.
6. Fare clic su **Create**(Crea).

Una volta creato correttamente il test, è possibile confrontare i risultati affiancati.

## <a name="side-by-side-comparison"></a>Confronto affiancato

Una volta completato il test, indicato dalla modifica dello stato a *succeeded*, si troverà un numero wer per entrambi i modelli inclusi nel test. Fare clic sul nome del test per visualizzare la pagina dei dettagli del test. Questa pagina di dettaglio elenca tutte le espressioni del set di dati, indicando i risultati del riconoscimento dei due modelli insieme alla trascrizione dal set di dati inviato. Per esaminare il confronto affiancato, è possibile abilitare o disabilitare vari tipi di errore, tra cui inserimento, eliminazione e sostituzione. Ascoltando l'audio e confrontando i risultati del riconoscimento in ogni colonna, che mostra la trascrizione con etichetta umana e i risultati per due modelli di riconoscimento vocale, è possibile decidere quale modello soddisfi le proprie esigenze e dove siano disponibili ulteriori attività di formazione e miglioramento Obbligatorio.

## <a name="next-steps"></a>Passaggi successivi

* [Eseguire il training del modello](how-to-custom-speech-train-model.md)
* [Distribuire il modello](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>Risorse aggiuntive

* [Preparare e testare i dati](how-to-custom-speech-test-data.md)
* [Esaminare i dati](how-to-custom-speech-inspect-data.md)
