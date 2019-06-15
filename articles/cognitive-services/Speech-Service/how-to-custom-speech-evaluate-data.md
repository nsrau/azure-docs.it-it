---
title: Valutare l'accuratezza del riconoscimento vocale personalizzato - servizi di riconoscimento vocale
titlesuffix: Azure Cognitive Services
description: In questo documento si apprenderà come misurare indica la qualità del modello di riconoscimento vocale di Microsoft o il modello personalizzato. Trascrizione audio + con etichetta umane dato necessario per testare l'accuratezza e 30 minuti e 5 ore dell'audio rappresentativo deve essere forniti.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 7c1b3602b09c1a129923180c4b1d4a5f8293de2c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65025910"
---
# <a name="evaluate-custom-speech-accuracy"></a>Valutare l'accuratezza di riconoscimento vocale personalizzato

In questo documento verrà illustrato come misurare indica la qualità del modello di riconoscimento vocale di Microsoft o il modello personalizzato. Trascrizione audio + con etichetta umane dato necessario per testare l'accuratezza e 30 minuti e 5 ore dell'audio rappresentativo deve essere forniti.

## <a name="what-is-word-error-rate-wer"></a>Che cos'è tasso di errore di Word (WER)?

È lo standard per misurare l'accuratezza del modello del settore *tasso di errore Word* (WER). Segnalazione errori Windows conta il numero di parole non corrette identificati durante il riconoscimento, quindi viene diviso per il numero totale di parole nella trascrizione con etichetta umane fornite. Infine, tale numero viene moltiplicato per 100% per la quale calcolare la segnalazione errori Windows.

![Formula di segnalazione errori Windows](./media/custom-speech/custom-speech-wer-formula.png)

Identificata in modo errato le parole rientrano in tre categorie:

* Inserimento (I): Parole che vengono aggiunti in modo non corretto nella trascrizione ipotesi
* Eliminazione (D): Parole che non vengono rilevate nella trascrizione ipotesi
* Sostituzione (S): Parole che sono state sostituite tra riferimenti e ipotesi

Ad esempio:

![Esempio di parole identificate in modo non corretto](./media/custom-speech/custom-speech-dis-words.png)

## <a name="resolve-errors-and-improve-wer"></a>Risolvere gli errori e migliorare la segnalazione errori Windows

È possibile usare la segnalazione errori Windows nei risultati di riconoscimento della macchina per valutare la qualità del modello che si usa con l'app, strumento o prodotto. Una segnalazione errori Windows del 5% al 10% è considerata buona qualità ed è pronto per l'uso. Una segnalazione errori Windows pari al 20% è accettabile, ma è possibile prendere in considerazione altri corsi di formazione. Una segnalazione errori Windows del 30% o più segnali di scarsa qualità e richiede la personalizzazione e la formazione.

Come vengono distribuiti gli errori è importanti. Quando si verificano molti errori di eliminazione, è in genere a causa di potenza del segnale audio debole. Per risolvere questo problema, è necessario raccogliere i dati audio più vicini all'origine. Gli errori di inserimento significa che l'audio è stato registrato in un ambiente di disturbo e scambio di battute possono essere presente, che causano problemi di riconoscimento. Spesso si verificano errori di sostituzione quando un campione sufficiente di termini specifici di dominio è stato fornito come trascrizioni con etichetta umane o correlato testo.

Analizzando i singoli file, è possibile determinare il tipo di errori presenti e quali errori sono univoci a un file specifico. Problemi relativi a livello di file consentirà di miglioramenti di destinazione.

## <a name="create-a-test"></a>Creare un test

Se si desidera testare la qualità del modello di linea di base per il riconoscimento vocale di Microsoft o un modello personalizzato che è stato eseguito il training, è possibile confrontare due modelli affiancati per valutare l'accuratezza. Il confronto include i risultati di segnalazione errori Windows e il riconoscimento. In genere, un modello personalizzato viene confrontato con il modello di base di Microsoft.

Valutare i modelli di fianco a fianco:

1. Passare a **vocale-> riconoscimento vocale personalizzato > test**.
2. Fare clic su **aggiungere Test**.
3. Selezionare **valutare l'accuratezza**. Assegnare un nome, descrizione, il test e selezionare il set di dati di trascrizione audio + con etichetta umane.
4. Selezionare fino a due modelli che si desidera testare.
5. Fare clic su **Create**(Crea).

Dopo il test è stato creato correttamente, è possibile confrontare i risultati affiancati.

## <a name="side-by-side-comparison"></a>Confronto side-by-side

Una volta completato il test, indicato per la modifica di stato *Succeeded*, si sarà disponibile una serie di segnalazione errori Windows per entrambi i modelli inclusi nel test. Fare clic sul nome del test per visualizzare la pagina dei dettagli test. Questa pagina di dettaglio Elenca tutte le espressioni nel set di dati, che indica i risultati di riconoscimento dei due modelli insieme la trascrizione del set di dati inviati. Per consentire di controllare il confronto side-by-side, è possibile passare diversi tipi di errore, tra cui inserimento, eliminazione e sostituzione. Ascoltare l'audio e confrontando i risultati del riconoscimento in ogni colonna, che mostra la trascrizione con etichetta umani e dai risultati di due modelli di riconoscimento vocale, è possibile decidere quale modello soddisfa le proprie esigenze e in cui sono corsi di formazione aggiuntive e miglioramenti richiesto.

## <a name="next-steps"></a>Passaggi successivi

* [Il training del modello](how-to-custom-speech-train-model.md)
* [Distribuire il modello](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>Risorse aggiuntive

* [Preparare e testare i dati](how-to-custom-speech-test-data.md)
* [Esaminare i dati](how-to-custom-speech-inspect-data.md)
