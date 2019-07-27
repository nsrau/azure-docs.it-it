---
title: Preparare i dati di test per il servizio di Riconoscimento vocale personalizzato vocale
titleSuffix: Azure Cognitive Services
description: Che tu stia testando per verificare l'accuratezza del riconoscimento vocale Microsoft o il training dei tuoi modelli, ti serviranno dati (sotto forma di audio e/o testo). In questa pagina vengono descritti i tipi di dati, il modo in cui vengono usati e come gestirli.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 8821ce46c65ac8bca36f006ef77bcaf475b0573d
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559611"
---
# <a name="prepare-data-for-custom-speech"></a>Preparare i dati per Riconoscimento vocale personalizzato

Che tu stia testando per verificare l'accuratezza del riconoscimento vocale Microsoft o il training dei tuoi modelli, ti serviranno dati sotto forma di audio e testo. In questa pagina vengono descritti i tipi di dati, il modo in cui vengono usati e come gestirli.

## <a name="data-types"></a>Tipi di dati

In questa tabella sono elencati i tipi di dati accettati, quando è necessario utilizzare ogni tipo di dati e la quantità consigliata. Non tutti i tipi di dati sono necessari per creare un modello. I requisiti dei dati variano a seconda che si stia creando un test o un training di un modello.

| Tipo di dati | Usato per il test | Quantità | Usato per il training | Quantità |
|-----------|-----------------|----------|-------------------|----------|
| [Audio](#audio-data-for-testing) | Yes<br>Usato per l'ispezione visiva | 5 + file audio | No | N/a |
| [Trascrizioni audio + con etichetta umana](#audio--human-labeled-transcript-data-for-testingtraining) | Sì<br>Utilizzato per valutare l'accuratezza | 0,5-5 ore di audio | Sì | da 1 a 1.000 ore di audio |
| [Testo correlato](##related-text-data-for-training) | No | N/a | Sì | 1-200 MB di testo correlato |

I file devono essere raggruppati per tipo in un set di dati e caricati come file zip. Ogni set di dati può contenere solo un singolo tipo di dati.

## <a name="upload-data"></a>Caricare dati

Quando si è pronti per caricare i dati, fare clic su **Carica dati** per avviare la procedura guidata e creare il primo set di dati. Prima di poter caricare i dati, verrà richiesto di selezionare un tipo di dati vocale per il set di dati.

![Selezionare audio dal portale vocale](./media/custom-speech/custom-speech-select-audio.png)

Ogni set di dati caricato deve soddisfare i requisiti per il tipo di dati scelto. È importante formattare correttamente i dati prima che vengano caricati. Ciò garantisce che i dati vengano elaborati accuratamente dal servizio Riconoscimento vocale personalizzato. I requisiti sono elencati nelle sezioni seguenti.

Dopo aver caricato il set di dati, sono disponibili alcune opzioni:

* È possibile passare alla scheda **test** e controllare visivamente solo l'audio o i dati di trascrizione con etichetta umana.
* Per eseguire il training di un modello personalizzato, è possibile passare alla scheda **Training** e usare dati di trascrizione audio e di traduzione umana o dati di testo correlati.

## <a name="audio-data-for-testing"></a>Dati audio per il test

I dati audio sono ottimali per il test dell'accuratezza del modello di sintesi vocale o di un modello personalizzato di Microsoft. Tenere presente che i dati audio vengono usati per controllare l'accuratezza del riconoscimento vocale per quanto riguarda le prestazioni di un modello specifico. Se si sta cercando di quantificare l'accuratezza di un modello, usare [i dati di trascrizione audio e con etichetta umana](#audio--human-labeled-transcript-data-for-testingtraining).

Usare questa tabella per assicurarsi che i file audio siano formattati correttamente per l'uso con Riconoscimento vocale personalizzato:

| Proprietà | Value |
|----------|-------|
| Formato file | RIFF (WAV) |
| Frequenza di campionamento | 8\.000 Hz o 16.000 Hz |
| Canali | 1 (mono) |
| Lunghezza massima per audio | 2 ore |
| Formato del campione | PCM, 16 bit |
| Formato di archiviazione | zip |
| Dimensione massima dell'archivio | 2 GB |

Se l'audio non soddisfa queste proprietà o se si vuole verificarlo, è consigliabile scaricare [Sox](http://sox.sourceforge.net) per controllare o convertire l'audio. Di seguito sono riportati alcuni esempi di come è possibile eseguire ognuna di queste attività tramite la riga di comando:

| Attività | DESCRIZIONE | Comando Sox |
|----------|-------------|-------------|
| Controlla formato audio | Usare questo comando per controllare il formato del file audio. | `sox --i <filename>` |
| Converti formato audio | Usare questo comando per convertire il file audio in un canale singolo, a 16 bit, a 16 KHz. | `sox <input> -b 16 -e signed-integer -c 1 -r 16k -t wav <output>.wav` |

## <a name="audio--human-labeled-transcript-data-for-testingtraining"></a>Dati della trascrizione audio + con etichetta umana per test/formazione

Per misurare l'accuratezza dell'accuratezza del riconoscimento vocale di Microsoft durante l'elaborazione dei file audio, è necessario fornire le trascrizioni con etichetta umana (Word-by-Word) per il confronto. Sebbene la trascrizione con etichetta umana sia spesso dispendiosa in termini di tempo, è necessario valutare l'accuratezza e per eseguire il training del modello per i casi d'uso. Tenere presente che i miglioramenti apportati al riconoscimento saranno validi solo per i dati forniti. Per questo motivo, è importante che vengano caricate solo le trascrizioni di alta qualità.  

| Proprietà | Value |
|----------|-------|
| Formato file | RIFF (WAV) |
| Frequenza di campionamento | 8\.000 Hz o 16.000 Hz |
| Canali | 1 (mono) |
| Lunghezza massima per audio | 60 s |
| Formato del campione | PCM, 16 bit |
| Formato di archiviazione | zip |
| Dimensioni massime zip | 2 GB |

Per risolvere problemi come l'eliminazione o la sostituzione di parole, per migliorare il riconoscimento è necessaria una quantità significativa di dati. In genere, è consigliabile fornire trascrizioni Word per parola per circa 10 o 1.000 ore di audio. Le trascrizioni di tutti i file WAV devono essere contenute in un unico file di testo normale. Ogni riga del file delle trascrizioni deve contenere il nome di uno dei file audio ed essere seguita dalla trascrizione corrispondente. Il nome del file deve essere separato dalla trascrizione mediante un carattere di tabulazione (\t).

  Ad esempio:
```
  speech01.wav  speech recognition is awesome
  speech02.wav  the quick brown fox jumped all over the place
  speech03.wav  the lazy dog was not amused
```
> [!NOTE]
> La trascrizione deve presentare la codifica UTF-8 BOM (byte order mark).

Alle trascrizioni viene applicata la normalizzazione del testo in modo che possano essere elaborate dal sistema. Tuttavia, esistono alcune importanti normalizzazioni da effettuare _prima_ di caricare i dati nel servizio di riconoscimento vocale personalizzato. Per il linguaggio appropriato da usare quando si preparano le trascrizioni, vedere [come creare una trascrizione con etichetta umana](how-to-custom-speech-human-labeled-transcriptions.md)

Dopo aver raccolto i file audio e le trascrizioni corrispondenti, è necessario imballarli come un unico file zip prima di caricarli nel portale di Riconoscimento vocale personalizzato. Si tratta di un set di dati di esempio con tre file audio e un file di trascrizione con etichetta umana:

![Selezionare audio dal portale vocale](./media/custom-speech/custom-speech-audio-transcript-pairs.png)

## <a name="related-text-data-for-training"></a>Dati di testo correlati per il training

Se sono presenti nomi di prodotto o funzionalità univoche e si desidera assicurarsi che siano riconosciute correttamente, è importante includere dati di testo correlati per il training. Per migliorare il riconoscimento è possibile fornire due tipi di dati di testo correlati:

| Tipo di dati | Miglioramento del riconoscimento dei dati |
|-----------|------------------------------------|
| Espressioni e/o frasi | Questi possono migliorare l'accuratezza quando riconoscono i nomi dei prodotti o il vocabolario specifico del settore all'interno del contesto di una frase. |
| Pronunce | Questi possono migliorare la pronuncia di termini non comuni, acronimi o altre parole con pronunce non definite. |

Le espressioni possono essere fornite come uno o più file di testo. Più si avvicinano i dati di testo a quello che verrà pronunciato, maggiore è la probabilità che la precisione venga migliorata. Le pronunce devono essere fornite come un singolo file di testo. Tutto può essere incluso in un unico file zip e caricato nel portale di Riconoscimento vocale personalizzato.

### <a name="guidelines-to-create-an-utterances-file"></a>Linee guida per la creazione di un file enunciato

Per creare un modello personalizzato usando il testo correlato, è necessario fornire un elenco di espressioni di esempio. Queste espressioni non devono necessariamente essere frasi complete o corrette grammaticali, ma devono riflettere accuratamente l'input parlato previsto nell'ambiente di produzione. Se si desidera che determinati termini abbiano un peso maggiore, è possibile aggiungere diverse frasi al file di dati correlato che includano questi termini specifici.

Usare questa tabella per assicurarsi che il file di dati correlato per le espressioni sia formattato correttamente:

| Proprietà | Value |
|----------|-------|
| Codifica testo | UTF-8 BOM |
| N. di espressioni per riga | 1 |
| Dimensione massima dei file | 200 MB |

Inoltre, è opportuno tenere conto delle restrizioni seguenti:

* Evitare di ripetere i caratteri più di quattro volte. Ad esempio: "aaaa" o "uuuu".
* Non usare i caratteri speciali o i caratteri UTF-8 sopra U + 00A1.
* Gli URI verranno rifiutati.

### <a name="guidelines-to-create-a-pronunciation-file"></a>Linee guida per la creazione di un file di pronuncia

Se sono presenti termini non comuni senza pronunce standard che verranno rilevati o utilizzati dagli utenti, è possibile fornire un file di pronuncia personalizzato per migliorare il riconoscimento.

> [!IMPORTANT]
> Non è consigliabile usare questa funzionalità per modificare la pronuncia di parole comuni.

Sono inclusi esempi di espressione pronunciata e una pronuncia personalizzata per ogni:

| Form riconosciuto/visualizzato | Forma parlata |
|--------------|--------------------------|
| 3CPO | tre c p o |  
| CNTK | c n t k |
| IEEE | i tripla e |

Il form vocale è la sequenza fonetica digitata. Può essere costituito da lettere, parole, sillabe o da una combinazione di tutti e tre.

La pronuncia personalizzata è disponibile in inglese (en-US) e tedesco (de-DE). Questa tabella mostra i caratteri supportati per lingua:

| Linguaggio | Impostazioni locali | Caratteri |
|----------|--------|------------|
| Inglese | en-US | a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z |
| Tedesco | de-DE | ä, ö, ü, a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z |

Usare questa tabella per assicurarsi che il file di dati correlato per le pronunce sia formattato correttamente. I file di pronuncia sono di piccole dimensioni e non devono superare alcune KB.

| Proprietà | Value |
|----------|-------|
| Codifica testo | BOM UTF-8 (ANSI è supportato anche per l'inglese) |
| numero di pronunce per riga | 1 |
| Dimensione massima dei file | 1 MB (1 KB per il livello gratuito) |

## <a name="next-steps"></a>Passaggi successivi

* [Esaminare i dati](how-to-custom-speech-inspect-data.md)
* [Valutazione dei dati](how-to-custom-speech-evaluate-data.md)
* [Eseguire il training del modello](how-to-custom-speech-train-model.md)
* [Distribuire il modello](how-to-custom-speech-deploy-model.md)
