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
ms.openlocfilehash: bead348e64fcee4cc5b790f975c9da5200ee796b
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422400"
---
# <a name="learn-the-basics-of-the-speech-cli"></a>Nozioni di base sull'interfaccia della riga di comando per Voce

Questo articolo illustra i modelli d'uso di base dell'interfaccia della riga di comando per Voce, uno strumento da riga di comando per usare il servizio Voce senza scrivere codice. È possibile testare rapidamente le funzionalità principali del servizio Voce, senza creare ambienti di sviluppo né scrivere codice, per verificare se i casi d'uso possono essere soddisfatti adeguatamente. L'interfaccia della riga di comando per Voce, inoltre, è pronta per la produzione e può essere usata per automatizzare flussi di lavoro semplici nel servizio Voce, usando `.bat` o script della shell.

[!INCLUDE [](includes/spx-setup.md)]

## <a name="basic-usage"></a>Uso di base

Questa sezione illustra alcuni comandi SPX di base spesso utili per eseguire i primi test e la prima sperimentazione. Per iniziare, visualizzare la Guida incorporata nello strumento eseguendo il comando seguente.

```shell
spx
```

Si notino gli argomenti della Guida **vedere** riportati a destra dei parametri del comando. È possibile immettere questi comandi per ottenere informazioni dettagliate della Guida sui sottocomandi.

È possibile cercare gli argomenti della Guida per parola chiave. Ad esempio, immettere il comando seguente per visualizzare un elenco di esempi di utilizzo dell'interfaccia della riga di comando di Voce:

```shell
spx help find --topics "examples"
```

Immettere il comando seguente per visualizzare le opzioni del comando recognize:

```shell
spx help recognize
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

### <a name="configuration-files-in-the-datastore"></a>File di configurazione nell'archivio dati

Il comportamento dell'interfaccia della riga di comando di Voce può essere basato sulle impostazioni dei file di configurazione, a cui è possibile fare riferimento all'interno delle chiamate all'interfaccia della riga di comando di Voce tramite un simbolo @.
L'interfaccia della riga di comando di Voce salva una nuova impostazione in una nuova sottodirectory `./spx/data` creata nella directory di lavoro corrente.
Per la ricerca di un valore di configurazione, l'interfaccia della riga di comando di Voce cerca nella directory di lavoro corrente, quindi nell'archivio dati nella sottodirectory `./spx/data` e infine in altri archivi dati, tra cui un archivio dati finale di sola lettura disponibile nel binario `spx`.
In precedenza, si usava l'archivio dati per salvare i valori `@key` e `@region`, quindi non era necessario specificarli con ogni chiamata della riga di comando.
È anche possibile usare i file di configurazione per archiviare le proprie impostazioni di configurazione o usarli per passare URL o altri contenuti dinamici generati in fase di esecuzione.

Questa sezione mostra come usare un file di configurazione nell'archivio dati locale per archiviare e recuperare le impostazioni dei comandi usando `spx config` e archiviare l'output dell'interfaccia della riga di comando del servizio Voce usando l'opzione `--output`.

Nell'esempio seguente viene cancellato il file di configurazione `@my.defaults`, vengono aggiunte coppie chiave-valore per **chiave** e **area** nel file e viene usata la configurazione in una chiamata a `spx recognize`.

```shell
spx config @my.defaults --clear
spx config @my.defaults --add key 000072626F6E20697320636F6F6C0000
spx config @my.defaults --add region westus

spx config @my.defaults

spx recognize --nodefaults @my.defaults --file hello.wav
```

È anche possibile eseguire la scrittura di contenuto dinamico in un file di configurazione. Il comando seguente ad esempio crea un modello di conversione voce/testo personalizzato e archivia l'URL del nuovo modello in un file di configurazione. Il comando successivo attende fino a quando il modello in tale URL non è pronto per l'uso prima di restituire il risultato.

```shell
spx csr model create --name "Example 4" --datasets @my.datasets.txt --output url @my.model.txt
spx csr model status --model @my.model.txt --wait
```

L'esempio seguente esegue la scrittura di due URL nel file di configurazione `@my.datasets.txt`.
In questo scenario, `--output` può includere una parola chiave **add** facoltativa per creare un file di configurazione o accodarlo a quello esistente.


```shell
spx csr dataset create --name "LM" --kind Language --content https://crbn.us/data.txt --output url @my.datasets.txt
spx csr dataset create --name "AM" --kind Acoustic --content https://crbn.us/audio.zip --output add url @my.datasets.txt

spx config @my.datasets.txt
```

Per informazioni dettagliate sui file di archivio dati, incluso l'utilizzo dei file di configurazione predefiniti (`@spx.default`, `@default.config` e `@*.default.config` per le impostazioni predefinite specifiche dei comandi), immettere questo comando:

```shell
spx help advanced setup
```

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

## <a name="synthesize-speech-to-a-file"></a>Sintetizzare la voce in un file

Eseguire il comando seguente per sostituire l'output del relatore con un file `.wav`.

```bash
spx synthesize --text "The speech synthesizer greets you!" --audio output greetings.wav
```

L'interfaccia della riga di comando per Voce produrrà il linguaggio naturale in inglese nel file audio `greetings.wav`.
In Windows è possibile riprodurre il file audio immettendo `start greetings.wav`.


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

* Completare le guide di avvio rapido sul [riconoscimento vocale](get-started-speech-to-text.md?pivots=programmer-tool-spx) o sulla [sintesi vocale](get-started-text-to-speech.md?pivots=programmer-tool-spx) tramite l'interfaccia della riga di comando di Voce.
