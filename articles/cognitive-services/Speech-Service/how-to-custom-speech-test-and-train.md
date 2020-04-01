---
title: Preparare i dati per il riconoscimento vocale personalizzato - Servizio di riconoscimento vocalePrepare data for Custom Speech - Speech service
titleSuffix: Azure Cognitive Services
description: Quando si verifica l'accuratezza del riconoscimento vocale Microsoft o della formazione dei modelli personalizzati, sono necessari dati audio e di testo. In questa pagina vengono illustrati i tipi di dati, come utilizzarli e gestirli.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/27/2020
ms.author: dapine
ms.openlocfilehash: d6e377ca4f33c0160267b23daaaaef5e07c271bb
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398689"
---
# <a name="prepare-data-for-custom-speech"></a>Preparare i dati per il riconoscimento vocale personalizzatoPrepare data for Custom Speech

Quando si verifica l'accuratezza del riconoscimento vocale Microsoft o della formazione dei modelli personalizzati, sono necessari dati audio e di testo. In questa pagina vengono illustrati i tipi di dati, come utilizzarli e gestirli.

## <a name="data-types"></a>Tipi di dati

In questa tabella sono elencati i tipi di dati accettati, quando deve essere utilizzato ogni tipo di dati e la quantità consigliata. Non tutti i tipi di dati sono necessari per creare un modello. I requisiti dei dati variano a seconda che si stia creando un test o si esema un modello.

| Tipo di dati | Utilizzato per il test | Quantità consigliata | Utilizzato per la formazione | Quantità consigliata |
|-----------|-----------------|----------|-------------------|----------|
| [Audio](#audio-data-for-testing) | Sì<br>Utilizzato per l'ispezione visiva | Più di 5 file audio | No | N/a |
| [Audio - Trascrizioni con etichetta umana](#audio--human-labeled-transcript-data-for-testingtraining) | Sì<br>Utilizzato per valutare la precisione | 0,5-5 ore di audio | Sì | 1-1.000 ore di audio |
| [Testo correlato](#related-text-data-for-training) | No | N/a | Sì | 1-200 MB di testo correlato |

I file devono essere raggruppati per tipo in un set di dati e caricati come file con estensione zip. Ogni set di dati può contenere un solo tipo di dati.

> [!TIP]
> Per iniziare rapidamente, prendere in considerazione l'uso di dati di esempio. Vedere questo repository GitHub per <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/sampledata/customspeech" target="_target">i dati vocali personalizzati di esempioSee <span class="docon docon-navigate-external x-hidden-focus"></span> </a> this GitHub repository for sample Custom Speech data

## <a name="upload-data"></a>Caricare dati

Per caricare i dati, passare al <a href="https://speech.microsoft.com/customspeech" target="_blank">portale <span class="docon docon-navigate-external x-hidden-focus"> </span>di riconoscimento vocale personalizzato </a>. Dal portale fare clic su **Carica dati** per avviare la procedura guidata e creare il primo set di dati. Ti verrà chiesto di selezionare un tipo di dati vocali per il tuo set di dati, prima di consentire di caricare i dati.

![Selezionare l'audio dal portale di riconoscimento vocale](./media/custom-speech/custom-speech-select-audio.png)

Ogni set di dati caricato deve soddisfare i requisiti per il tipo di dati scelto. I dati devono essere formattati correttamente prima di essere caricati. I dati formattati correttamente assicurano che vengano elaborati con precisione dal servizio di riconoscimento vocale personalizzato. I requisiti sono elencati nelle sezioni seguenti.

Dopo il caricamento del set di dati, sono disponibili alcune opzioni:After your dataset is uploaded, you have a few options:

* È possibile passare alla scheda **Test** e controllare visivamente solo l'audio o i dati di trascrizione con etichetta umana, con etichetta umana.
* È possibile passare alla scheda **Training** e usare i dati di trascrizione audio, umani o i dati di testo correlati per eseguire il training di un modello personalizzato.

## <a name="audio-data-for-testing"></a>Dati audio per il test

I dati audio sono ottimali per testare l'accuratezza del modello di sintesi vocale di Microsoft o di un modello personalizzato. Tenere presente che i dati audio vengono utilizzati per controllare l'accuratezza del parlato per quanto riguarda le prestazioni di un modello specifico. Se si sta cercando di quantificare l'accuratezza di un modello, utilizzare i dati di [trascrizione audio e con etichetta umana.](#audio--human-labeled-transcript-data-for-testingtraining)

Utilizzate questa tabella per assicurarvi che i file audio siano formattati correttamente per l'utilizzo con il riconoscimento vocale personalizzato:

| Proprietà                 | valore                 |
|--------------------------|-----------------------|
| Formato file              | RIFF (WAV)            |
| Frequenza di campionamento              | 8.000 Hz o 16.000 Hz |
| Canali                 | 1 (mono)              |
| Lunghezza massima per audio | 2 ore               |
| Formato del campione            | PCM, 16 bit           |
| Formato di archiviazione           | zip                  |
| Dimensione massima dell'archivio     | 2 GB                  |

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

> [!TIP]
> Quando si caricano dati di training e test, la dimensione del file .zip non può superare i 2 GB. Se hai bisogno di più dati per il training, dividili in diversi file .zip e caricali separatamente. Successivamente, è possibile scegliere di eseguire il training da *più* set di dati. Tuttavia, è possibile eseguire il test solo da un *singolo* set di dati.

Utilizzate <a href="http://sox.sourceforge.net" target="_blank" rel="noopener">SoX <span class="docon docon-navigate-external x-hidden-focus"></span> </a> per verificare le proprietà audio o convertire l'audio esistente nei formati appropriati. Di seguito sono riportati alcuni esempi di come ognuna di queste attività può essere eseguita tramite la riga di comando SoX:

| Attività | Descrizione | Comando SoX |
|----------|-------------|-------------|
| Controllare il formato audio | Utilizzare questo comando per<br>il formato del file audio. | `sox --i <filename>` |
| Convertire il formato audio | Utilizzare questo comando per convertire<br>il file audio su un singolo canale, 16 bit, 16 KHz. | `sox <input> -b 16 -e signed-integer -c 1 -r 16k -t wav <output>.wav` |

## <a name="audio--human-labeled-transcript-data-for-testingtraining"></a>Audio: dati di trascrizione con etichetta umana per il test/formazione

Per misurare l'accuratezza dell'accuratezza del riconoscimento vocale di Microsoft durante l'elaborazione dei file audio, è necessario fornire trascrizioni con etichetta umana (parola per parola) per il confronto. Mentre la trascrizione con etichettatura umana richiede spesso molto tempo, è necessario valutare l'accuratezza e eseguire il training del modello per i casi d'uso. Tenete a mente, i miglioramenti nel riconoscimento sarà solo buono come i dati forniti. Per questo motivo, è importante caricare solo le trascrizioni di alta qualità.

| Proprietà                 | valore                               |
|--------------------------|-------------------------------------|
| Formato file              | RIFF (WAV)                          |
| Frequenza di campionamento              | 8.000 Hz o 16.000 Hz               |
| Canali                 | 1 (mono)                            |
| Lunghezza massima per audio | 2 ore (test) / 60 s (allenamento) |
| Formato del campione            | PCM, 16 bit                         |
| Formato di archiviazione           | zip                                |
| Dimensione massima zip         | 2 GB                                |

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

> [!NOTE]
> Quando si caricano dati di training e test, la dimensione del file .zip non può superare i 2 GB. È possibile eseguire il test solo da un *singolo* set di dati, assicurarsi di mantenerlo entro le dimensioni del file appropriate. Inoltre, ogni file di training non può superare i 60 secondi altrimenti si eserciterà un errore.

Per risolvere problemi come l'eliminazione o la sostituzione di parole, è necessaria una quantità significativa di dati per migliorare il riconoscimento. In genere, si consiglia di fornire trascrizioni parola per parola per circa 10-1.000 ore di audio. Le trascrizioni di tutti i file WAV devono essere contenute in un unico file di testo normale. Ogni riga del file delle trascrizioni deve contenere il nome di uno dei file audio ed essere seguita dalla trascrizione corrispondente. Il nome del file deve essere separato dalla trascrizione mediante un carattere di tabulazione (\t).

  Ad esempio:
```
  speech01.wav  speech recognition is awesome
  speech02.wav  the quick brown fox jumped all over the place
  speech03.wav  the lazy dog was not amused
```

> [!IMPORTANT]
> La trascrizione deve presentare la codifica UTF-8 BOM (byte order mark).

Alle trascrizioni viene applicata la normalizzazione del testo in modo che possano essere elaborate dal sistema. Tuttavia, esistono alcune importanti normalizzazioni che devono essere eseguite prima di caricare i dati in Speech Studio.However, there are some important normalizations that must be done before uploading the data to the Speech Studio. Per il linguaggio appropriato da utilizzare quando si preparano le trascrizioni, vedere [Come creare una trascrizione con etichetta umana](how-to-custom-speech-human-labeled-transcriptions.md)

Dopo aver raccolto i file audio e le trascrizioni corrispondenti, creare un pacchetto come un singolo file .zip prima di caricarli nel <a href="https://speech.microsoft.com/customspeech" target="_blank">portale <span class="docon docon-navigate-external x-hidden-focus"> </span>di riconoscimento vocale personalizzato. </a> Di seguito è riportato un set di dati di esempio con tre file audio e un file di trascrizione con etichetta umana:

> [!div class="mx-imgBorder"]
> ![Selezionare l'audio dal portale di riconoscimento vocale](./media/custom-speech/custom-speech-audio-transcript-pairs.png)

## <a name="related-text-data-for-training"></a>Dati di testo correlati per il training

I nomi o le funzionalità dei prodotti univoche devono includere dati di testo correlati per il training. Il testo correlato contribuisce a garantire il corretto riconoscimento. Per migliorare il riconoscimento, è possibile fornire due tipi di dati di testo correlati:

| Tipo di dati | Come questi dati migliorano il riconoscimento |
|-----------|------------------------------------|
| Frasi (espressioni) | Migliora la precisione quando rilevi i nomi dei prodotti o il vocabolario specifico del settore nel contesto di una frase. |
| Pronunce | Migliorare la pronuncia di termini non comuni, acronimi o altre parole con pronunce indefinite. |

Le frasi possono essere fornite come un singolo file di testo o più file di testo. Per migliorare l'accuratezza, usare dati di testo più vicini alle espressioni vocali previste. Le pronunce devono essere fornite come un singolo file di testo. Tutto può essere incluso nel pacchetto come un singolo file zip e caricato nel <a href="https://speech.microsoft.com/customspeech" target="_blank">portale <span class="docon docon-navigate-external x-hidden-focus"> </span>di riconoscimento vocale personalizzato </a>.

### <a name="guidelines-to-create-a-sentences-file"></a>Linee guida per creare un file di frasi

Per creare un modello personalizzato usando frasi, è necessario fornire un elenco di espressioni di esempio. Le utterancenon _non_ devono essere complete o grammaticalmente corrette, ma devono riflettere accuratamente l'input parlato che ci si aspetta nella produzione. Se vuoi che determinati termini abbiano un aumento di peso, aggiungi diverse frasi che includono questi termini specifici.

Come guida generale, l'adattamento del modello è più efficace quando il testo di training è il più vicino possibile al testo reale previsto nella produzione. Il gergo specifico del dominio e le frasi che hai come targeting per migliorare devono essere inclusi nel testo di training. Quando possibile, prova a controllare una frase o una parola chiave su una riga separata. Per le parole chiave e le frasi che sono importanti per te (ad esempio, i nomi dei prodotti), puoi copiarli alcune volte. Ma tenere a mente, non copiare troppo - potrebbe influenzare il tasso di riconoscimento complessivo.

Utilizzare questa tabella per assicurarsi che il file di dati correlato per le espressioni sia formattato correttamente:Use this table to ensure that your related data file for utterances is formatted correctly:

| Proprietà | valore |
|----------|-------|
| Codifica testo | UTF-8 BOM |
| N. di espressioni per riga | 1 |
| Dimensione massima dei file | 200 MB |

Inoltre, ti consigliamo di tenere conto delle seguenti restrizioni:

* Evitare di ripetere i caratteri più di quattro volte. Ad esempio: "aaaa" o "uuuu".
* Non utilizzare caratteri speciali o caratteri `U+00A1`UTF-8 sopra.
* Gli URI verranno rifiutati.

### <a name="guidelines-to-create-a-pronunciation-file"></a>Linee guida per creare un file di pronuncia

Se esistono termini non comuni senza pronuncia standard che gli utenti incontreranno o utilizzeranno, è possibile fornire un file di pronuncia personalizzato per migliorare il riconoscimento.

> [!IMPORTANT]
> Non è consigliabile utilizzare file di pronuncia personalizzati per modificare la pronuncia delle parole comuni.

Sono inclusi esempi di un'espressione parlata e una pronuncia personalizzata per ognuna di esse:

| Modulo riconosciuto/visualizzato | Forma parlata |
|--------------|--------------------------|
| 3CPO | tre c p o |
| CNTK | c n t k k |
| Ieee | i triplo e |

La forma parlata è la sequenza fonetica scritta. Può essere composto da lettere, parole, sillabe o una combinazione di tutti e tre.

La pronuncia personalizzata è disponibile`en-US`in inglese`de-DE`( ) e tedesco ( ). Questa tabella mostra i caratteri supportati per lingua:

| Linguaggio | Impostazioni locali | Caratteri |
|----------|--------|------------|
| Inglese | `en-US` | `a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z` |
| Tedesco | `de-DE` | `ä, ö, ü, a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z` |

Utilizzare la tabella seguente per assicurarsi che il file di dati correlato per le pronunce sia formattato correttamente. I file di pronuncia sono di piccole dimensioni e devono essere di pochi kilobyte.

| Proprietà | valore |
|----------|-------|
| Codifica testo | UTF-8 BOM (ANSI è supportato anche per l'inglese) |
| Numero di pronunce per riga | 1 |
| Dimensione massima dei file | 1 MB (1 KB per livello gratuito) |

## <a name="next-steps"></a>Passaggi successivi

* [Ispezionare i dati](how-to-custom-speech-inspect-data.md)
* [Valutare i dati](how-to-custom-speech-evaluate-data.md)
* [Eseguire il training del modello](how-to-custom-speech-train-model.md)
* [Distribuire il modello](how-to-custom-speech-deploy-model.md)
