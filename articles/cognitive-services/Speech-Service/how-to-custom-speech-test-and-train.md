---
title: Preparare i dati per il servizio di Riconoscimento vocale personalizzato vocale
titleSuffix: Azure Cognitive Services
description: Quando si esegue il test dell'accuratezza del riconoscimento vocale Microsoft o del training dei modelli personalizzati, saranno necessari dati audio e di testo. In questa pagina vengono illustrati i tipi di dati, come usarli e gestirli.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/27/2020
ms.author: trbye
ms.openlocfilehash: f43f7894c46a75894eb648f02ec378f3a8b2633d
ms.sourcegitcommit: d7fba095266e2fb5ad8776bffe97921a57832e23
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84628060"
---
# <a name="prepare-data-for-custom-speech"></a>Preparare i dati per Riconoscimento vocale personalizzato

Quando si esegue il test dell'accuratezza del riconoscimento vocale Microsoft o del training dei modelli personalizzati, saranno necessari dati audio e di testo. In questa pagina vengono descritti i tipi di dati necessari per un modello di riconoscimento vocale personalizzato.

## <a name="data-diversity"></a>Diversità dei dati

Testo e audio usati per testare ed eseguire il training di un modello personalizzato devono includere esempi da un set diversificato di altoparlanti e scenari per cui è necessario che il modello sia in grado di riconoscere.
Considerare questi fattori quando si raccolgono i dati per il training e il testing del modello personalizzato:

* I dati audio di testo e sintesi vocale devono comprendere i tipi di istruzioni verbali che gli utenti faranno quando interagiscono con il modello. Ad esempio, un modello che aumenta e riduce la temperatura necessita di training sulle istruzioni che possono essere eseguite da persone per richiedere tali modifiche.
* È necessario che i dati includano tutte le varianze vocali che dovranno essere riconosciute dal modello. Molti fattori possono variare la voce, inclusi accenti, dialetti, combinazione di lingue, età, sesso, tono vocale, livello di stress e ora del giorno.
* È necessario includere esempi da ambienti diversi (interni, esterni, rumori stradali) in cui verrà usato il modello.
* L'audio deve essere raccolto usando i dispositivi hardware che verranno usati dal sistema di produzione. Se il modello deve identificare il riconoscimento vocale registrato sui dispositivi di registrazione di qualità variabile, i dati audio forniti per il training del modello devono rappresentare anche questi diversi scenari.
* È possibile aggiungere più dati al modello in un secondo momento, ma è necessario tenere in considerazione il set di dati in modo diverso e rappresentativo delle esigenze del progetto.
* L'inclusione di dati che *non* rientrano nelle esigenze di riconoscimento del modello personalizzato può danneggiare la qualità complessiva, quindi non includere i dati che non devono essere trascritti dal modello.

Un modello sottoposto a training su un subset di scenari può essere eseguito correttamente in questi scenari. Scegliere con attenzione i dati che rappresentano l'ambito completo degli scenari necessari per riconoscere il modello personalizzato.

> [!TIP]
> Inizia con piccoli set di dati di esempio che corrispondono alla lingua e all'acustica che il tuo modello incontrerà.
> Registrare, ad esempio, un campione piccolo ma rappresentativo di audio sullo stesso hardware e nello stesso ambiente acustico che il modello troverà negli scenari di produzione.
> I set di dati di piccole dimensioni dei dati rappresentativi possono esporre problemi prima di investire nella raccolta di set di dati di dimensioni molto più grandi per il training.

## <a name="data-types"></a>Tipi di dati

In questa tabella sono elencati i tipi di dati accettati, quando è necessario utilizzare ogni tipo di dati e la quantità consigliata. Non tutti i tipi di dati sono necessari per creare un modello. I requisiti dei dati variano a seconda che si stia creando un test o un training di un modello.

| Tipo di dati | Usato per il test | Quantità consigliata | Usato per il training | Quantità consigliata |
|-----------|-----------------|----------|-------------------|----------|
| [Audio](#audio-data-for-testing) | Sì<br>Usato per l'ispezione visiva | 5 + file audio | No | N/D |
| [Trascrizioni audio + con etichetta umana](#audio--human-labeled-transcript-data-for-testingtraining) | Sì<br>Utilizzato per valutare l'accuratezza | 0,5-5 ore di audio | Sì | da 1 a 1000 ore di audio |
| [Testo correlato](#related-text-data-for-training) | No | N/a | Sì | 1-200 MB di testo correlato |

I file devono essere raggruppati per tipo in un set di dati e caricati come file con estensione zip. Ogni set di dati può contenere solo un singolo tipo di dati.

> [!TIP]
> Per iniziare rapidamente, provare a usare i dati di esempio. Vedere questo repository GitHub per <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/sampledata/customspeech" target="_target">i dati <span class="docon docon-navigate-external x-hidden-focus"></span> di esempio riconoscimento vocale personalizzato</a>

## <a name="upload-data"></a>Caricare dati

Per caricare i dati, passare al <a href="https://speech.microsoft.com/customspeech" target="_blank">portale <span class="docon docon-navigate-external x-hidden-focus"></span> di riconoscimento vocale personalizzato </a>. Dal portale fare clic su **Carica dati** per avviare la procedura guidata e creare il primo set di dati. Prima di poter caricare i dati, verrà richiesto di selezionare un tipo di dati vocale per il set di dati.

![Selezionare audio dal portale vocale](./media/custom-speech/custom-speech-select-audio.png)

Ogni set di dati caricato deve soddisfare i requisiti per il tipo di dati scelto. I dati devono essere formattati correttamente prima del caricamento. I dati formattati correttamente garantiscono che vengano elaborati accuratamente dal servizio Riconoscimento vocale personalizzato. I requisiti sono elencati nelle sezioni seguenti.

Dopo aver caricato il set di dati, sono disponibili alcune opzioni:

* È possibile passare alla scheda **test** e controllare visivamente solo l'audio o i dati di trascrizione con etichetta umana.
* Per eseguire il training di un modello personalizzato, è possibile passare alla scheda **Training** e usare dati di trascrizione audio e di traduzione umana o dati di testo correlati.

## <a name="audio-data-for-testing"></a>Dati audio per il test

I dati audio sono ottimali per il test dell'accuratezza del modello di sintesi vocale o di un modello personalizzato di Microsoft. Tenere presente che i dati audio vengono usati per controllare l'accuratezza del riconoscimento vocale per quanto riguarda le prestazioni di un modello specifico. Se si sta cercando di quantificare l'accuratezza di un modello, usare [i dati di trascrizione audio e con etichetta umana](#audio--human-labeled-transcript-data-for-testingtraining).

Usare questa tabella per assicurarsi che i file audio siano formattati correttamente per l'uso con Riconoscimento vocale personalizzato:

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
> Quando si caricano i dati di training e di test, le dimensioni del file zip non possono superare i 2 GB. Se sono necessari più dati per il training, suddividerli in più file con estensione zip e caricarli separatamente. Successivamente, è possibile scegliere di eseguire il training da *più* set di impostazioni. Tuttavia, è possibile eseguire il test solo da un *singolo* set di dati.

Usare <a href="http://sox.sourceforge.net" target="_blank" rel="noopener">Sox <span class="docon docon-navigate-external x-hidden-focus"></span> </a> per verificare le proprietà audio o convertire l'audio esistente nei formati appropriati. Di seguito sono riportati alcuni esempi di come è possibile eseguire ognuna di queste attività tramite la riga di comando di SoX:

| Attività | Descrizione | Comando SoX |
|----------|-------------|-------------|
| Controlla formato audio | Usare questo comando per controllare<br>formato del file audio. | `sox --i <filename>` |
| Converti formato audio | Usare questo comando per convertire<br>file audio a canale singolo, a 16 bit, 16 KHz. | `sox <input> -b 16 -e signed-integer -c 1 -r 16k -t wav <output>.wav` |

## <a name="audio--human-labeled-transcript-data-for-testingtraining"></a>Dati della trascrizione audio + con etichetta umana per test/formazione

Per misurare l'accuratezza dell'accuratezza del riconoscimento vocale di Microsoft durante l'elaborazione dei file audio, è necessario fornire le trascrizioni con etichetta umana (Word-by-Word) per il confronto. Sebbene la trascrizione con etichetta umana sia spesso dispendiosa in termini di tempo, è necessario valutare l'accuratezza e per eseguire il training del modello per i casi d'uso. Tenere presente che i miglioramenti apportati al riconoscimento saranno validi solo per i dati forniti. Per questo motivo, è importante che vengano caricate solo le trascrizioni di alta qualità.

I file audio possono avere un silenzio all'inizio e alla fine della registrazione. Se possibile, includere almeno un mezzo secondo di silenzio prima e dopo il riconoscimento vocale in ogni file di esempio. Anche se l'audio con un volume di registrazione basso o un rumore di fondo a disturbo non è utile, non dovrebbe danneggiare il modello personalizzato. Provare sempre a aggiornare i microfoni e l'hardware di elaborazione dei segnali prima di raccogliere esempi di audio.

| Proprietà                 | valore                               |
|--------------------------|-------------------------------------|
| Formato file              | RIFF (WAV)                          |
| Frequenza di campionamento              | 8.000 Hz o 16.000 Hz               |
| Canali                 | 1 (mono)                            |
| Lunghezza massima per audio | 2 ore (test)/60 s (Training) |
| Formato del campione            | PCM, 16 bit                         |
| Formato di archiviazione           | zip                                |
| Dimensioni massime zip         | 2 GB                                |

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

> [!NOTE]
> Quando si caricano i dati di training e di test, le dimensioni del file zip non possono superare i 2 GB. È possibile eseguire il test solo da un *singolo* set di dati, assicurarsi di mantenerlo all'interno delle dimensioni del file appropriate. Inoltre, ogni file di training non può superare 60 secondi in caso contrario, si otterrà un errore.

Per risolvere problemi come l'eliminazione o la sostituzione di parole, per migliorare il riconoscimento è necessaria una quantità significativa di dati. In genere, è consigliabile fornire trascrizioni Word per parola per circa 10 o 1.000 ore di audio. Le trascrizioni di tutti i file WAV devono essere contenute in un unico file di testo normale. Ogni riga del file delle trascrizioni deve contenere il nome di uno dei file audio ed essere seguita dalla trascrizione corrispondente. Il nome del file deve essere separato dalla trascrizione mediante un carattere di tabulazione (\t).

  Ad esempio:
```
  speech01.wav  speech recognition is awesome
  speech02.wav  the quick brown fox jumped all over the place
  speech03.wav  the lazy dog was not amused
```

> [!IMPORTANT]
> La trascrizione deve presentare la codifica UTF-8 BOM (byte order mark).

Alle trascrizioni viene applicata la normalizzazione del testo in modo che possano essere elaborate dal sistema. Tuttavia, esistono alcune normalizzazioni importanti che devono essere eseguite prima di caricare i dati in speech studio. Per il linguaggio appropriato da usare quando si preparano le trascrizioni, vedere [come creare una trascrizione con etichetta umana](how-to-custom-speech-human-labeled-transcriptions.md)

Dopo aver raccolto i file audio e le trascrizioni corrispondenti, crearne un pacchetto come file con estensione zip prima del caricamento nel <a href="https://speech.microsoft.com/customspeech" target="_blank">portale <span class="docon docon-navigate-external x-hidden-focus"></span> di riconoscimento vocale personalizzato </a>. Di seguito è riportato un esempio di set di dati con tre file audio e un file di trascrizione con etichetta umana:

> [!div class="mx-imgBorder"]
> ![Selezionare audio dal portale vocale](./media/custom-speech/custom-speech-audio-transcript-pairs.png)

## <a name="related-text-data-for-training"></a>Dati di testo correlati per il training

I nomi di prodotto o le funzionalità univoche devono includere dati di testo correlati per il training. Il testo correlato contribuisce a garantire il riconoscimento corretto. Per migliorare il riconoscimento è possibile fornire due tipi di dati di testo correlati:

| Tipo di dati | Miglioramento del riconoscimento dei dati |
|-----------|------------------------------------|
| Frasi (espressioni) | Migliorare la precisione quando si riconoscono i nomi dei prodotti o il vocabolario specifico del settore all'interno del contesto di una frase. |
| Pronunce | Migliorare la pronuncia di termini non comuni, acronimi o altre parole con pronunce non definite. |

Le frasi possono essere fornite come un singolo file di testo o più file di testo. Per migliorare l'accuratezza, usare i dati di testo più vicini alle espressioni vocali previste. Le pronunce devono essere fornite come un singolo file di testo. Tutto può essere incluso in un unico file zip e caricato nel <a href="https://speech.microsoft.com/customspeech" target="_blank">portale <span class="docon docon-navigate-external x-hidden-focus"></span> di riconoscimento vocale personalizzato </a>.

### <a name="guidelines-to-create-a-sentences-file"></a>Linee guida per la creazione di un file di frasi

Per creare un modello personalizzato usando le frasi, è necessario fornire un elenco di espressioni di esempio. _Non_ è necessario che le espressioni siano complete o grammaticalmente corrette, ma devono riflettere accuratamente l'input parlato previsto nell'ambiente di produzione. Se si desidera che determinati termini abbiano un peso maggiore, aggiungere diverse frasi che includono questi termini specifici.

Come materiale sussidiario generale, l'adattamento del modello è più efficace quando il testo di training è il più vicino possibile al testo reale previsto nell'ambiente di produzione. Il gergo e le frasi specifici del dominio destinati a migliorare devono essere inclusi nel testo di training. Quando possibile, provare ad avere una frase o una parola chiave controllata su una riga distinta. Per le parole chiave e le frasi importanti per l'utente (ad esempio, i nomi dei prodotti), è possibile copiarle alcune volte. Tuttavia, tenere presente che non è possibile copiarne troppe. questo potrebbe influire sulla frequenza di riconoscimento complessiva.

Usare questa tabella per assicurarsi che il file di dati correlato per le espressioni sia formattato correttamente:

| Proprietà | valore |
|----------|-------|
| Codifica testo | UTF-8 BOM |
| N. di espressioni per riga | 1 |
| Dimensione massima dei file | 200 MB |

Inoltre, è opportuno tenere conto delle restrizioni seguenti:

* Evitare di ripetere i caratteri più di quattro volte. Ad esempio: "aaaa" o "uuuu".
* Non usare i caratteri speciali o i caratteri UTF-8 precedenti `U+00A1` .
* Gli URI verranno rifiutati.

### <a name="guidelines-to-create-a-pronunciation-file"></a>Linee guida per la creazione di un file di pronuncia

Se sono presenti termini non comuni senza pronunce standard che verranno rilevati o utilizzati dagli utenti, è possibile fornire un file di pronuncia personalizzato per migliorare il riconoscimento.

> [!IMPORTANT]
> Non è consigliabile usare i file di pronuncia personalizzati per modificare la pronuncia di parole comuni.

Sono inclusi esempi di espressione pronunciata e una pronuncia personalizzata per ogni:

| Form riconosciuto/visualizzato | Forma parlata |
|--------------|--------------------------|
| 3CPO | tre c p o |
| CNTK | c n t k |
| IEEE | i tripla e |

Il form vocale è la sequenza fonetica digitata. Può essere costituito da lettere, parole, sillabe o da una combinazione di tutti e tre.

La pronuncia personalizzata è disponibile in inglese ( `en-US` ) e tedesco ( `de-DE` ). Questa tabella mostra i caratteri supportati per lingua:

| Linguaggio | Impostazioni locali | Caratteri |
|----------|--------|------------|
| Inglese | `en-US` | `a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z` |
| Tedesco | `de-DE` | `ä, ö, ü, a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z` |

Usare la tabella seguente per assicurarsi che il file di dati correlato per le pronunce sia formattato correttamente. I file di pronuncia sono di dimensioni ridotte e devono contenere solo pochi kilobyte.

| Proprietà | valore |
|----------|-------|
| Codifica testo | BOM UTF-8 (ANSI è supportato anche per l'inglese) |
| numero di pronunce per riga | 1 |
| Dimensione massima dei file | 1 MB (1 KB per il livello gratuito) |

## <a name="next-steps"></a>Passaggi successivi

* [Esaminare i dati](how-to-custom-speech-inspect-data.md)
* [Valutazione dei dati](how-to-custom-speech-evaluate-data.md)
* [Eseguire il training del modello](how-to-custom-speech-train-model.md)
* [Distribuire il modello](how-to-custom-speech-deploy-model.md)
