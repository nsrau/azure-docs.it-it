---
title: Controllare la qualità dei dati per il servizio di Riconoscimento vocale personalizzato vocale
titleSuffix: Azure Cognitive Services
description: Riconoscimento vocale personalizzato offre strumenti che consentono di esaminare visivamente la qualità del riconoscimento di un modello confrontando i dati audio con il risultato del riconoscimento corrispondente. È possibile riprodurre l'audio caricato e determinare se il risultato del riconoscimento specificato è corretto.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: erhopf
ms.openlocfilehash: d4da9a819d7aa96992259112c75154b1651341ac
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/16/2020
ms.locfileid: "90604758"
---
# <a name="inspect-custom-speech-data"></a>Esaminare i dati di Riconoscimento vocale personalizzato

> [!NOTE]
> Questa pagina presuppone che sia stata effettuata la lettura [dei dati di test per riconoscimento vocale personalizzato](how-to-custom-speech-test-data.md) e che sia stato caricato un set di dati per l'ispezione.

Riconoscimento vocale personalizzato offre strumenti che consentono di esaminare visivamente la qualità del riconoscimento di un modello confrontando i dati audio con il risultato del riconoscimento corrispondente. Dal [portale di riconoscimento vocale personalizzato](https://speech.microsoft.com/customspeech)è possibile riprodurre l'audio caricato e determinare se il risultato del riconoscimento fornito è corretto. Questo strumento consente di controllare la qualità del modello di sintesi vocale di Microsoft, controllare un modello personalizzato sottoposto a training o confrontare la trascrizione con due modelli.

In questo documento si apprenderà come esaminare visivamente la qualità del modello di sintesi vocale di base di Microsoft e/o i modelli personalizzati di cui è stato eseguito il training. Si apprenderà anche come usare l'editor trascrizioni online per creare e perfezionare i set di impostazioni audio con etichetta.

## <a name="create-a-test"></a>Creare un test

Per creare un test, seguire queste istruzioni:

1. Accedere al portale di [riconoscimento vocale personalizzato](https://speech.microsoft.com/customspeech).
2. Passare a **riconoscimento vocale > Riconoscimento vocale personalizzato > [nome del progetto] > test**.
3. Fare clic su **Aggiungi test**.
4. Selezionare **Controlla qualità (dati solo audio)**. Assegnare al test un nome, una descrizione e selezionare il set di dati audio.
5. Selezionare fino a due modelli che si desidera testare.
6. Fare clic su **Crea**.

Al termine della creazione di un test, è possibile visualizzare il modo in cui un modello consente di trascrivere il set di dati audio specificato oppure confrontare i risultati di due modelli side-by-side.

[!INCLUDE [service-pricing-advisory](includes/service-pricing-advisory.md)]

## <a name="side-by-side-model-comparisons"></a>Confronto tra modelli affiancati

Quando lo stato del test ha _esito positivo_, fare clic sul nome dell'elemento del test per visualizzare i dettagli del test. Questa pagina di dettaglio elenca tutte le espressioni del set di dati e Mostra i risultati del riconoscimento dei due modelli da confrontare.

Per esaminare il confronto affiancato, è possibile abilitare o disabilitare vari tipi di errore, tra cui inserimento, eliminazione e sostituzione. Ascoltando l'audio e confrontando i risultati del riconoscimento in ogni colonna (mostrando la trascrizione con etichetta umana e i risultati di due modelli di riconoscimento vocale), è possibile decidere quale modello soddisfi le proprie esigenze e dove siano necessari miglioramenti.

Il testing del modello affiancato è utile per convalidare il modello di riconoscimento vocale migliore per un'applicazione. Per una misura oggettiva di accuratezza, che richiede l'audio trascritto, seguire le istruzioni disponibili in [valutare l'accuratezza](how-to-custom-speech-evaluate-data.md).

## <a name="online-transcription-editor"></a>Editor trascrizioni online

L'editor trascrizioni online consente di usare facilmente le trascrizioni audio in Riconoscimento vocale personalizzato. I casi d'uso principali dell'editor sono i seguenti: 

* Sono disponibili solo dati audio, ma si vuole creare set di dati audio e con etichetta umana accurate da zero da usare per il training del modello.
* Sono già presenti set di impostazioni audio e con etichetta umana, ma si verificano errori o difetti nella trascrizione. L'editor consente di modificare rapidamente le trascrizioni per ottenere una migliore precisione di training.

L'unico requisito per utilizzare l'editor di trascrizione consiste nel caricare dati audio (solo audio o audio + trascrizione).

### <a name="import-datasets-to-editor"></a>Importa set di DataSet nell'editor

Per importare dati nell'editor, passare prima di tutto a **Riconoscimento vocale personalizzato > [progetto] > editor**.

![Scheda dell'editor](media/custom-speech/custom-speech-editor-detail.png)

Usare quindi la procedura seguente per importare i dati.

1. Fare clic su **Importa dati**
1. Creare un nuovo set di dati e assegnargli una descrizione
1. Selezionare set di impostazioni. Sono supportate più selezioni ed è possibile selezionare solo i dati audio, oltre ai dati audio e con etichetta umana.
1. Per i dati solo audio, è possibile usare facoltativamente i modelli predefiniti per generare automaticamente la trascrizione del computer dopo l'importazione nell'editor
1. Fare clic su **Importa**

Una volta importati correttamente i dati, è possibile fare clic nei set di dati e iniziare la modifica.

> [!TIP]
> È anche possibile importare i set di impostazioni direttamente nell'editor selezionando set di impostazioni e facendo clic su **Esporta nell'editor**

### <a name="edit-transcription-by-listening-to-audio"></a>Modificare la trascrizione ascoltando l'audio

Una volta completato il caricamento dei dati, fare clic su ogni nome di elemento per visualizzare i dettagli dei dati. La pagina dei dettagli elenca tutti i file del set di dati ed è possibile fare clic sull'espressione desiderata. Per ogni espressione, è possibile riprodurre l'audio ed esaminare le trascrizioni e modificare le trascrizioni se si riscontrano errori di inserimento, eliminazione o sostituzione. Per ulteriori informazioni sui tipi di errore, vedere la pagina relativa alla [valutazione dei dati](how-to-custom-speech-evaluate-data.md) .

![Pagina dell'editor](media/custom-speech/custom-speech-editor.png)

Se il file audio è lungo, viene segmentato automaticamente in parti più piccole. È possibile modificarli uno alla volta usando **Previous** e **Next** per spostarsi tra le pagine. Dopo aver apportato le modifiche, fare clic sul pulsante **Salva** .

### <a name="export-datasets-from-the-editor"></a>Esportare i set di impostazioni dall'editor

Per esportare di nuovo i set di **dati** nella scheda dati, passare alla pagina dei dettagli dei dati e fare clic sul pulsante **Esporta** per esportare tutti i file come nuovo set di dati. È anche possibile filtrare i file in base all'ora dell'Ultima modifica, alle durate audio e così via per selezionare parzialmente i file desiderati. 

![Esportare i dati](media/custom-speech/custom-speech-editor-export.png)

I file esportati nei dati verranno usati come set di dati completamente nuovo e non influiscono sulle entità esistenti di dati, training e test.

## <a name="next-steps"></a>Passaggi successivi

- [Valutazione dei dati](how-to-custom-speech-evaluate-data.md)
- [Eseguire il training del modello](how-to-custom-speech-train-model.md)
- [Migliorare il modello](how-to-custom-speech-improve-accuracy.md)
- [Distribuire il modello](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>Risorse aggiuntive

- [Preparare i dati di test per Riconoscimento vocale personalizzato](how-to-custom-speech-test-data.md)
