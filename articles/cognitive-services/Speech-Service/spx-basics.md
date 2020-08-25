---
title: Nozioni di base sull'interfaccia della riga di comando per Voce
titleSuffix: Azure Cognitive Services
description: Informazioni su come usare lo strumento di comando dell'interfaccia della riga di comando per Voce per usare il servizio Voce senza codice e con configurazione minima.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 04/04/2020
ms.author: trbye
ms.openlocfilehash: 2f5a1d190c6e63056c2377641446f617edaa1bd3
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/19/2020
ms.locfileid: "88590218"
---
# <a name="learn-the-basics-of-the-speech-cli"></a>Nozioni di base sull'interfaccia della riga di comando per Voce

Questo articolo illustra i modelli d'uso di base dell'interfaccia della riga di comando per Voce, uno strumento da riga di comando per usare il servizio Voce senza scrivere codice. È possibile testare rapidamente le funzionalità principali del servizio Voce, senza creare ambienti di sviluppo né scrivere codice, per verificare se i casi d'uso possono essere soddisfatti adeguatamente. L'interfaccia della riga di comando per Voce, inoltre, è pronta per la produzione e può essere usata per automatizzare flussi di lavoro semplici nel servizio Voce, usando `.bat` o script della shell.

[!INCLUDE [](includes/spx-setup.md)]

## <a name="basic-usage"></a>Uso di base

Questa sezione illustra alcuni comandi SPX di base spesso utili per eseguire i primi test e la prima sperimentazione. Per iniziare, visualizzare la Guida incorporata nello strumento eseguendo il comando seguente.

```shell
spx
```

Si notino gli argomenti della Guida **vedere anche** riportati a destra dei parametri del comando. È inoltre possibile cercare gli argomenti in base a parola chiave. Ad esempio, immettere il comando seguente per visualizzare un elenco degli argomenti della Guida sugli esempi di interfaccia della riga di comando di Voce:

```shell
spx help find --topics "examples"
```

Ora usare il servizio Voce per eseguire alcune operazioni di riconoscimento vocale con il microfono predefinito eseguendo il comando seguente.

```shell
spx recognize --microphone
```

Dopo l'immissione del comando, SPX inizierà ad ascoltare l'audio sul dispositivo di input attivo corrente e si arresterà dopo che l'utente preme `ENTER`. Le parole registrate vengono quindi riconosciute e convertite in testo nell'output della console. L'interfaccia della riga di comando per Voce consente di eseguire il modo semplice anche la sintesi vocale. 

Il comando seguente accetta il testo immesso come input e restituisce la voce sintetizzata al dispositivo di output attivo corrente.

```shell
spx synthesize --text "Testing synthesis using the Speech CLI" --speakers
```

Oltre al riconoscimento e alla sintesi vocali, l'interfaccia della riga di comando per Voce consente anche di eseguire la traduzione vocale. Analogamente al comando di riconoscimento vocale precedente, eseguire il comando seguente per acquisire audio dal microfono predefinito ed effettuare la traduzione in testo nella lingua di destinazione.

```shell
spx translate --microphone --source en-US --target ru-RU --output file C:\some\file\path\russian_translation.txt
```

In questo comando è possibile specificare sia la lingua di origine (lingua **da** cui tradurre) che quella di destinazione (lingua **in** cui tradurre). L'uso dell'argomento `--microphone` consente di ascoltare l'audio sul dispositivo di input attivo corrente e di interrompere l'ascolto se si preme `ENTER`. L'output è una traduzione nella lingua di destinazione, scritta in un file di testo.

> [!NOTE]
> Per un elenco di tutte le lingue supportate con i rispettivi codici delle impostazioni locali, vedere l'[articolo relativo alla lingua e alle impostazioni locali](language-support.md).

## <a name="batch-operations"></a>Operazioni batch

I comandi della sezione precedente sono molto interessanti per visualizzare rapidamente il funzionamento del servizio Voce. Quando si valuta se i casi d'uso possono essere soddisfatti, tuttavia, è probabile che sia necessario eseguire operazioni batch su un intervallo di input già presente, per capire il modo in cui il servizio gestisce un'ampia gamma di scenari. Questa sezione illustra come effettuare le operazioni seguenti:

* Eseguire il riconoscimento vocale in batch in una directory di file audio
* Scorrere un file `.tsv` ed eseguire la sintesi vocale in batch

## <a name="batch-speech-recognition"></a>Riconoscimento vocale in batch

Se si dispone di una directory di file audio, l'interfaccia della riga di comando per Voce consente di eseguire rapidamente il riconoscimento vocale in batch. È sufficiente eseguire questo comando, puntando alla directory con il comando `--files`. In questo esempio si aggiunge `\*.wav` alla directory per riconoscere tutti i file `.wav` presenti nella directory. Specificare inoltre l'argomento `--threads` per eseguire il riconoscimento su 10 thread paralleli.

> [!NOTE]
> L'argomento `--threads` può essere usato anche nella sezione successiva per i comandi `spx synthesize` e i thread disponibili dipendono dalla CPU e dalla percentuale di carico corrente.

```shell
spx recognize --files C:\your_wav_file_dir\*.wav --output file C:\output_dir\speech_output.tsv --threads 10
```

L'output vocale riconosciuto viene scritto in `speech_output.tsv` tramite l'argomento `--output file`. Di seguito è riportato un esempio della struttura dei file di output.

```output
audio.input.id    recognizer.session.started.sessionid    recognizer.recognized.result.text
sample_1    07baa2f8d9fd4fbcb9faea451ce05475    A sample wave file.
sample_2    8f9b378f6d0b42f99522f1173492f013    Sample text synthesized.
```

## <a name="batch-text-to-speech-synthesis"></a>Sintesi vocale in batch

Il modo più semplice per eseguire la sintesi vocale in batch consiste nel creare un nuovo file `.tsv` (con valori delimitati da tabulazioni) e nell'usare il comando `--foreach` nell'interfaccia della riga di comando per Voce. Considerare il file `text_synthesis.tsv` seguente:

```output
audio.output    text
C:\batch_wav_output\wav_1.wav    Sample text to synthesize.
C:\batch_wav_output\wav_2.wav    Using the Speech CLI to run batch-synthesis.
C:\batch_wav_output\wav_3.wav    Some more text to test capabilities.
```

 Eseguire quindi un comando per puntare a `text_synthesis.tsv`, eseguire la sintesi su ogni campo `text` e scrivere il risultato nel percorso `audio.output` corrispondente come file `.wav`. 

```shell
spx synthesize --foreach in @C:\your\path\to\text_synthesis.tsv
```

Questo comando equivale a eseguire `spx synthesize --text Sample text to synthesize --audio output C:\batch_wav_output\wav_1.wav` **per ogni** record nel file `.tsv`. Un paio di aspetti da considerare:

* Le intestazioni di colonna, `audio.output` e `text`, corrispondono rispettivamente agli argomenti della riga di comando `--audio output` e `--text`. Gli argomenti della riga di comando multiparte come `--audio output` devono essere formattati nel file senza alcuno spazio, alcun trattino iniziale e alcun punto di separazione delle stringhe, ad esempio `audio.output`. Qualsiasi altro argomento della riga di comando esistente può essere aggiunto al file come colonna aggiuntiva tramite questo modello.
* Quando il file viene formattato in questo modo, non è necessario passare argomenti aggiuntivi a `--foreach`.
* Verificare di separare ogni valore nel file `.tsv` con una **tabulazione**.

Può essere presente tuttavia un file `.tsv` come nell'esempio seguente, con intestazioni di colonna che **non corrispondono** agli argomenti della riga di comando:

```output
wav_path    str_text
C:\batch_wav_output\wav_1.wav    Sample text to synthesize.
C:\batch_wav_output\wav_2.wav    Using the Speech CLI to run batch-synthesis.
C:\batch_wav_output\wav_3.wav    Some more text to test capabilities.
```

In tal caso è possibile eseguire l'override di questi nomi di campo negli argomenti corretti usando la sintassi seguente nella chiamata a `--foreach`. Si tratta della stessa chiamata descritta in precedenza.

```shell
spx synthesize --foreach audio.output;text in @C:\your\path\to\text_synthesis.tsv
```

## <a name="next-steps"></a>Passaggi successivi

* Completare le guide introduttive al [riconoscimento vocale](./quickstarts/speech-to-text-from-microphone.md) o alla [sintesi vocale](./quickstarts/text-to-speech.md) tramite il Software Development Kit (SDK).
