---
title: Preparare i dati di test per riconoscimento vocale personalizzato - servizi di riconoscimento vocale
titlesuffix: Azure Cognitive Services
description: Se si esegue il test per verificare l'accuratezza del riconoscimento vocale Microsoft è o training di modelli personalizzati, sarà necessario dei dati (sotto forma di testo e/o audio). In questa pagina vengono illustrati i tipi di dati, come vengono usati e come gestirli.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 6e1ffa11456fc6a021e370d674624d297463ac73
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67603192"
---
# <a name="prepare-data-for-custom-speech"></a>Preparare i dati per il riconoscimento vocale personalizzato

Se si esegue il test per verificare l'accuratezza del riconoscimento vocale Microsoft è o training di modelli personalizzati, sarà necessario dati sotto forma di testo e audio. In questa pagina vengono illustrati i tipi di dati, come vengono usati e come gestirli.

## <a name="data-types"></a>Tipi di dati

Questa tabella elenca i tipi di dati accettati, quando usare ogni tipo di dati e la quantità consigliata. Non tutti i tipi dei dati sono necessario per creare un modello. Requisiti dei dati varia a seconda se si crea un test o training di un modello.

| Tipo di dati | Utilizzato di test | Quantità | Utilizzato per il training | Quantità |
|-----------|-----------------|----------|-------------------|----------|
| [Audio](#audio-data-for-testing) | Yes<br>Utilizzato per l'esame visivo | file audio 5 + | No | n/d |
| [Trascrizioni audio + con etichetta umane](#audio--human-labeled-transcript-data-for-testingtraining) | Yes<br>Utilizzato per valutare l'accuratezza | 0,5 - 5 ore dell'audio | Yes | 1 - 1.000 ore di audio |
| [Testo correlato](##related-text-data-for-training) | No | n/d | Yes | 1-200 MB di testo correlato |

I file devono essere raggruppati per tipo in un set di dati e caricati come file zip. Ogni set di dati può contenere solo un singolo tipo di dati.

## <a name="upload-data"></a>Caricare dati

Quando si è pronti per caricare i dati, fare clic su **caricare i dati** per avviare la procedura guidata e creare il primo set di dati. Verrà chiesto di selezionare un tipo di dati di riconoscimento vocale per il set di dati, prima di consentire il caricamento dei dati.

![Selezionare l'audio dal portale di riconoscimento vocale](./media/custom-speech/custom-speech-select-audio.png)

Ogni set di dati caricati deve soddisfare i requisiti per il tipo di dati scelto. È importante formattare correttamente i dati prima del caricamento. In questo modo che verranno elaborati in modo accurato i dati dal servizio di riconoscimento vocale personalizzato. Nelle sezioni seguenti vengono elencati i requisiti.

Dopo aver caricato il set di dati, sono disponibili alcune opzioni:

* È possibile passare al **Testing** scheda ed esaminare visivamente trascrizione audio + con etichetta umane dati o solo l'audio.
* È possibile passare per il **Training** scheda e usare dati di trascrizione audio + risorse umane o testo correlato per addestrare un modello personalizzato.

## <a name="audio-data-for-testing"></a>Dati audio per il test

Dati audio sono ottimali per test dell'accuratezza del modello di riconoscimento vocale della linea di base di Microsoft o un modello personalizzato. Tenere presente, i dati audio viene usati per esaminare l'accuratezza del riconoscimento vocale per quanto riguarda le prestazioni del modello specifico. Se si sta cercando di quantificare l'accuratezza di un modello, usare [dati trascrizione audio + con etichetta umane](#audio--human-labeled-transcript-data-for-testingtraining).

Usare questa tabella per assicurarsi che i file audio siano formattati correttamente per l'uso con il riconoscimento vocale personalizzato:

| Proprietà | Value |
|----------|-------|
| Formato file | RIFF (WAV) |
| Frequenza di campionamento | 8\.000 Hz o Hz 16.000 |
| Canali | 1 (mono) |
| Lunghezza massima per ogni audio | 2 ore |
| Formato del campione | PCM, 16 bit |
| Formato di archiviazione | zip |
| Dimensione massima dell'archivio | 2 GB |

Se l'audio non soddisfa queste proprietà o si desidera verificare se questa operazione, è consigliabile scaricare [sox](http://sox.sourceforge.net) per controllare o convertire l'audio. Di seguito sono riportati alcuni esempi del modo in cui ognuna di queste attività può essere eseguita tramite la riga di comando:

| Attività | Descrizione | Comando SOX |
|----------|-------------|-------------|
| Controllare il formato audio | Usare questo comando per controllare il formato di file audio. | `sox --i <filename>` |
| Convertire il formato audio | Usare questo comando per convertire il file audio a singolo canale, 16 bit, KHz 16. | `sox <input> -b 16 -e signed-integer -c 1 -r 16k -t wav <output>.wav` |

## <a name="audio--human-labeled-transcript-data-for-testingtraining"></a>Dati di trascrizione audio + human-etichetta per il testing/training

Per misurare l'accuratezza di accuratezza per il riconoscimento vocale di Microsoft durante l'elaborazione dei file audio, è necessario fornire un'etichetta umane trascrizioni (parola per parola) per il confronto. Mentre un'etichetta umane trascrizione è spesso molto tempo, è necessario per valutare l'accuratezza e per il training del modello per i casi d'uso. Tenere presente che i miglioramenti nel riconoscimento sarà solo tanto quanto i dati forniti. Per questo motivo, è importante che vengono caricate le trascrizioni solo alta qualità.  

| Proprietà | Value |
|----------|-------|
| Formato file | RIFF (WAV) |
| Frequenza di campionamento | 8\.000 Hz o Hz 16.000 |
| Canali | 1 (mono) |
| Lunghezza massima per ogni audio | 60 s |
| Formato del campione | PCM, 16 bit |
| Formato di archiviazione | zip |
| Dimensioni massime file zip | 2 GB |

Per risolvere i problemi, ad esempio l'eliminazione di word o la sostituzione, una quantità significativa di dati è necessarie per migliorare il riconoscimento. In generale, è consigliabile fornire parola per parola trascrizioni per circa 10 a 1.000 ore di audio. Le trascrizioni di tutti i file WAV devono essere contenute in un unico file di testo normale. Ogni riga del file delle trascrizioni deve contenere il nome di uno dei file audio ed essere seguita dalla trascrizione corrispondente. Il nome del file deve essere separato dalla trascrizione mediante un carattere di tabulazione (\t).

  Ad esempio:
```
  speech01.wav  speech recognition is awesome
  speech02.wav  the quick brown fox jumped all over the place
  speech03.wav  the lazy dog was not amused
```
> [!NOTE]
> La trascrizione deve presentare la codifica UTF-8 BOM (byte order mark).

Alle trascrizioni viene applicata la normalizzazione del testo in modo che possano essere elaborate dal sistema. Tuttavia, esistono alcune importanti normalizzazioni da effettuare _prima_ di caricare i dati nel servizio di riconoscimento vocale personalizzato. Per la lingua appropriata da utilizzare quando si preparano le trascrizioni, vedere [come creare una trascrizione con etichetta umane](how-to-custom-speech-human-labeled-transcriptions.md)

Dopo che è stato raccolto il file audio e trascrizioni corrispondenti, essi deve essere compresso come un unico file zip prima di caricare il portale di riconoscimento vocale personalizzato. Si tratta di un set di dati di esempio con tre file audio e un file di trascrizione con etichetta umane:

![Selezionare l'audio dal portale di riconoscimento vocale](./media/custom-speech/custom-speech-audio-transcript-pairs.png)

## <a name="related-text-data-for-training"></a>Dati di testo correlate per il training

Se si dispone di nomi di prodotto o funzionalità che sono univoche e si desidera assicurarsi che vengano riconosciuti correttamente, è importante includere dati di testo correlate per il training. È possibile specificare due tipi di dati di testo correlate per migliorare il riconoscimento:

| Tipo di dati | Modo in cui questi dati consente di migliorare il riconoscimento |
|-----------|------------------------------------|
| Espressioni e/o frasi | Questi possono migliorare l'accuratezza nel riconoscimento dei nomi di prodotto, o un vocabolario specifici del settore nel contesto di una frase. |
| Pronuncia | Tali può migliorare la pronuncia di termini non comuni, gli acronimi o altre parole con le pronunce non definite. |

Espressioni possono essere specificati come uno o più file di testo. Si avvicina il testo dei dati sono da che cosa verrà pronunciata, maggiore è la probabilità che la precisione è stata migliorata. Le pronunce devono essere specificate come un singolo file di testo. Tutto ciò che può essere compresso come un unico file zip e caricato nel portale di riconoscimento vocale personalizzato.

### <a name="guidelines-to-create-an-utterances-file"></a>Linee guida per creare un file di espressioni

Per creare un modello personalizzato usando testo correlato, è necessario fornire un elenco di espressioni di esempio. Queste espressioni non devono essere frasi complete o correggere grammaticalmente, ma è necessario riflettere accuratamente l'input vocale che previsti nell'ambiente di produzione. Se si desidera che alcuni termini di avere un maggior peso, è possibile aggiungere più frasi nel file di dati correlati che includono questi termini specifici.

Usare questa tabella per assicurarsi che il file di dati correlati per utterances sia formattato correttamente:

| Proprietà | Value |
|----------|-------|
| Codifica testo | UTF-8 BOM |
| N. di espressioni per riga | 1 |
| Dimensione massima dei file | 200 MB |

Inoltre, è opportuno tenere conto delle restrizioni seguenti:

* Evitare caratteri ripetuti più di quattro volte. Ad esempio: "aaaa" o "uuuu".
* Non usare caratteri speciali o caratteri UTF-8 di sopra di U + 00A1.
* Gli URI verranno rifiutate.

### <a name="guidelines-to-create-a-pronunciation-file"></a>Linee guida per creare un file di pronuncia

Se sono presenti condizioni non comuni senza le pronunce standard che gli utenti verificano o usare, è possibile fornire un file di pronuncia personalizzato per migliorare il riconoscimento.

> [!IMPORTANT]
> È consigliabile non usare questa funzionalità per modificare la pronuncia delle parole comuni.

Ciò include esempi di un utterance parlato e una pronuncia personalizzata per ogni:

| Formato riconosciuto/visualizzato | Forma parlata |
|--------------|--------------------------|
| 3CPO | tre c p o |  
| CNTK | k c n t |
| IEEE | i pari al triplo e |

La forma parlata è la sequenza fonetica venga specificata. Si può essere composto lettera, parole, sillabe o una combinazione di tutti e tre.

Pronuncia personalizzata è disponibile in lingua inglese (en-US) e tedesco (de-DE). Questa tabella Mostra caratteri supportati dalla lingua:

| Linguaggio | Impostazioni locali | Caratteri |
|----------|--------|------------|
| Inglese | en-US | a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z |
| Tedesco | de-DE | ä, ö, ü, a, b, c, d, e, f, g, h, i, j, k, l, m, n, o, p, q, r, s, t, u, v, w, x, y, z |

Usare questa tabella per garantire che il file di dati correlati per la pronuncia sia formattato correttamente. Pronuncia file sono di piccole dimensioni e non devono superare pochi Knowledge Base.

| Proprietà | Value |
|----------|-------|
| Codifica testo | Il BOM UTF-8 (ANSI è supportata anche per la lingua inglese) |
| n. di pronuncia per ogni riga | 1 |
| Dimensione massima dei file | 1 MB (1 KB per il livello gratuito) |

## <a name="next-steps"></a>Passaggi successivi

* [Esaminare i dati](how-to-custom-speech-inspect-data.md)
* [Valutare i dati](how-to-custom-speech-evaluate-data.md)
* [Il training del modello](how-to-custom-speech-train-model.md)
* [Distribuire il modello](how-to-custom-speech-deploy-model.md)
