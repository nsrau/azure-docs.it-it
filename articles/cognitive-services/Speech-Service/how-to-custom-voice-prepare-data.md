---
title: Come preparare i dati per le funzionalità vocali personalizzati - servizi di riconoscimento vocale
titlesuffix: Azure Cognitive Services
description: Creare una voce personalizzata per il tuo marchio con servizi di riconoscimento vocale di Azure. Si forniscono le registrazioni di studio e gli script associati, il servizio genera un modello vocale univoca ottimizzato per la voce registrata. Utilizzare questa voce per sintesi vocale per i prodotti, strumenti e applicazioni.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: erhopf
ms.openlocfilehash: 23ba45804632163cc5de41ac2353091b7cf850c1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67063186"
---
# <a name="prepare-data-to-create-a-custom-voice"></a>Preparare i dati per creare una voce personalizzata

Quando si è pronti per creare una voce di sintesi vocale personalizzata per l'applicazione, il primo passaggio è raccogliere registrazioni audio e gli script associati per avviare training del modello vocali. Il servizio Usa questi dati per creare una voce esclusiva ottimizzata in modo che corrisponda la voce nelle registrazioni. Dopo aver eseguito il training della voce, è possibile avviare la sintesi vocale nelle tue applicazioni.

È possibile iniziare con una piccola quantità di dati per creare un modello di verifica. Tuttavia, i dati più specificata dall'utente, il più naturale verranno suoni vocali personalizzati. Prima è possibile eseguire il training modello di sintesi vocale personalizzato, è necessario registrazioni audio e trascrizioni il testo associato. In questa pagina, verranno esaminati i tipi di dati, come vengono usati e come gestire ognuna.

## <a name="data-types"></a>Tipi di dati

Un set di dati di training vocali include registrazioni audio e file di testo con le trascrizioni associati. Ciascun file audio dovrà contenere un singolo utterance (una singola frase o un singolo attiva per un sistema di finestra di dialogo) ed essere meno di 15 secondi.

In alcuni casi, non si abbia il giusto set di dati pronti e sarà necessario testare il training vocali personalizzati con file audio disponibili, short o long, con o senza le trascrizioni. Offriamo gli strumenti (beta) che consentono di segmentare l'audio in espressioni e preparare le trascrizioni usando il [API Batch trascrizione](batch-transcription.md).

Questa tabella elenca i tipi di dati e come ognuno viene utilizzato per creare un modello personalizzato di sintesi vocale.

| Tipo di dati | Descrizione | Quando usare le autorizzazioni | Servizio aggiuntiva necessaria | Quantità per il training di un modello | Locale(s) |
| --------- | ----------- | ----------- | --------------------------- | ----------------------------- | --------- |
| **Singole espressioni + trascrizioni corrispondenti** | Una raccolta (con estensione zip) di file audio (wav) come singole espressioni. Ciascun file audio deve essere 15 secondi o una lunghezza, associato a una trascrizione formattata (con estensione txt). | Registrazioni professionale con i corrispondenti trascrizioni | Pronto per il training. | Nessun requisito disco rigido per en-US e zh-CN. Più di 2.000 + utterances distinti per le altre impostazioni locali. | Tutte le impostazioni locali vocale personalizzato |
| **Long audio + trascrizioni (beta)** | Una raccolta (con estensione zip) di lunghe, non segmentati file audio (più di 20 secondi), associata a una trascrizione (con estensione txt) contenente tutti i dialoghi. | Si dispone di file audio e trascrizioni corrispondente, ma non sono segmentati in espressioni. | Segmentazione (usando la trascrizione di batch).<br>Trasformazione di formato audio richiesto. | Nessun requisito disco rigido per en-US e zh-CN. | `en-US` e `zh-CN` |
| **Solo l'audio (beta)** | Raccolta (con estensione zip) di file audio senza una trascrizione. | È necessario solo i file disponibili, senza le trascrizioni audio. | Segmentazione + generazione di trascrizione (mediante la trascrizione di batch).<br>Trasformazione di formato audio richiesto.| Nessun requisito per `en-US` e `zh-CN`. | `en-US` e `zh-CN` |

I file devono essere raggruppati per tipo in un set di dati e caricati come file zip. Ogni set di dati può contenere solo un singolo tipo di dati.

> [!NOTE]
> Il numero massimo di set di dati possono essere importati per ogni sottoscrizione è 10 ZIP file gratuitamente utenti della sottoscrizione (F0) e 500 per gli utenti sottoscrizione standard (S0).

## <a name="individual-utterances--matching-transcript"></a>Singole espressioni + trascrizioni corrispondenti

È possibile preparare le registrazioni singole espressioni e la trascrizione corrispondente in due modi. Uno scrivere uno script che verrà letto da un talent vocali o usare audio disponibile pubblicamente e trascrivere al testo. Nel secondo caso, modificare le disfluenze dai file audio, ad esempio "um" e altri suoni riempitivi, balbettii, parole mormorate o errori di pronuncia.

Per produrre un carattere voce valida, creare le registrazioni in una chat room non interattiva con un microfono di alta qualità. Volume coerente, velocità, parlando passo ed espressivi è utile di riconoscimento vocale di lettura sono essenziali.

> [!TIP]
> Per creare una voce per l'uso in produzione, è consigliabile avvalersi di uno studio di registrazione e uno speaker professionali. Per altre informazioni, vedere [Come registrare esempi vocali per una voce personalizzata](record-custom-voice-samples.md).

### <a name="audio-files"></a>File audio

Ogni file audio deve contenere un singolo utterance (una singola frase o una singola attivazione di un sistema di finestra di dialogo), meno di 15 secondi. Tutti i file devono essere nella stessa lingua parlata. Voci di sintesi vocale personalizzate multilingue non sono supportate, ad eccezione di bilingue in lingua inglese cinese. Ogni file audio deve avere un nome file numerico univoco con il file wav estensione nome file.

Seguire queste linee guida durante la preparazione di audio.

| Proprietà | Value |
| -------- | ----- |
| Formato file | RIFF (. wav), raggruppati in un file con estensione zip |
| Frequenza di campionamento | almeno 16.000 Hz |
| Formato del campione | PCM, 16 bit |
| Nome file | Valori numerici, con estensione wav. Non nomi di file duplicati consentiti. |
| Lunghezza audio | Inferiore a 15 secondi |
| Formato di archiviazione | zip |
| Dimensione massima dell'archivio | 200 MB |

> [!NOTE]
> i file con estensione wav con una frequenza di campionamento inferiore a 16.000 Hz verranno rifiutati. Se un file con estensione zip contiene i file con estensione wav con frequenze di campionamento differenti, verranno importati solo tali uguale o superiore a 16.000 Hz. Il portale attualmente importa archivi ZIP fino a 200 MB. È tuttavia possibile caricare più archivi.

### <a name="transcripts"></a>Trascrizioni

Il file di trascrizione è un file di testo normale. Usare queste linee guida per preparare le trascrizioni.

| Proprietà | Value |
| -------- | ----- |
| Formato file | Testo normale (con estensione txt) |
| Formato di codifica | ANSI/ASCII, UTF-8, UTF-8-BOM, UTF-16-LE o UTF-16-BE. Per zh-CN, codifiche ANSI/ASCII e UTF-8 non sono supportate. |
| N. di espressioni per riga | **Uno** -ogni riga del file di trascrizione deve contenere il nome di uno dei file audio, aggiungendo la trascrizione corrispondente. Il nome del file deve essere separato dalla trascrizione mediante un carattere di tabulazione (\t). |
| Dimensione massima dei file | 50 MB |

Di seguito è riportato un esempio di come le trascrizioni sono organizzate utterance da utterance in un unico file. txt:

```
0000000001[tab] This is the waistline, and it's falling.
0000000002[tab] We have trouble scoring.
0000000003[tab] It was Janet Maslin.
```
È importante che le trascrizioni sono trascrizioni accurata del 100% dell'audio corrispondente. Errori nella trascrizione renderà disponibile la perdita di qualità durante il training.

> [!TIP]
> Durante la generazione di sintesi vocale voices, utterances selezionare (o creazione di script) che prendono in produzione account sia fonetica copertura ed efficienza. Problemi nell'ottenere i risultati desiderati? [Contattare la vocale personalizzato](mailto:speechsupport@microsoft.com) team per trovare informazioni sulla presenza di Stati Uniti, consultare.

## <a name="long-audio--transcript-beta"></a>Long audio + trascrizioni (beta)

In alcuni casi, si potrebbe non avere segmentati audio disponibile. Offriamo un servizio (beta) tramite il portale vocali personalizzati che consentono di segmentare i file audio lungo e creare trascrizioni. Tenere presente, questo servizio verrà addebitato verso l'utilizzo della sottoscrizione per il riconoscimento vocale.

> [!NOTE]
> Il servizio di long-audio segmentazione sfrutterà la funzionalità di trascrizione di batch di riconoscimento vocale in testo, che supporta solo gli utenti sottoscrizione standard (S0). Durante l'elaborazione della segmentazione, i file audio e le trascrizioni verranno anche inviate al servizio riconoscimento vocale personalizzato per perfezionare il modello di riconoscimento in modo che è possibile migliorare l'accuratezza dei dati. Nessun dato verrà mantenuto durante questo processo. Al termine la segmentazione, solo le espressioni segmentate e le trascrizioni di mapping verranno archiviate per il download e il training.

### <a name="audio-files"></a>File audio

Seguire queste linee guida quando si prepara l'audio per la segmentazione.

| Proprietà | Value |
| -------- | ----- |
| Formato file | RIFF (. wav) con una frequenza di campionamento di almeno 16 khz 16-bit nel PCM o MP3 con una velocità in bit di almeno 256 KBps, raggruppati in un file con estensione zip |
| Nome file | Solo caratteri ASCII. I caratteri Unicode nel nome avrà esito negativo (ad esempio, i caratteri cinesi o simboli, ad esempio ","). Non sono consentiti nomi duplicati. |
| Lunghezza audio | Più di 20 secondi |
| Formato di archiviazione | zip |
| Dimensione massima dell'archivio | 200 MB |

Tutti i file audio devono essere raggruppati in un file zip. È anche possibile inserire i file con estensione wav e MP3 in un file zip audio, ma non è consentita alcuna sottocartella nel file zip. Ad esempio, è possibile caricare un file con estensione zip che contiene un file audio denominato 'kingstory.wav' 45-secondo-prolungata e un altro uno denominato 'queenstory.mp3', 200-secondo: long, senza sottocartelle. Tutti i file MP3 verranno trasformati in formato WAV dopo l'elaborazione.

### <a name="transcripts"></a>Trascrizioni

Le trascrizioni devono essere preparate per le specifiche elencate in questa tabella. Ciascun file audio deve essere associata a una trascrizione.

| Proprietà | Value |
| -------- | ----- |
| Formato file | Testo normale (con estensione txt), raggruppato in un file ZIP |
| Nome file | Usare lo stesso nome del file audio corrispondente |
| Formato di codifica | UTF-8-BOM solo |
| N. di espressioni per riga | Nessun limite |
| Dimensione massima dei file | 50 MILIONI |

Tutti i file di trascrizioni di questo tipo di dati devono essere raggruppati in un file zip. Nel file zip non è consentita alcuna sottocartella. Ad esempio, è stato caricato un file con estensione zip che contiene un file audio denominato 'kingstory.wav', 45 secondi di durata e un altro uno denominato 'queenstory.mp3', 200 secondi lungo. È necessario caricare un altro file con estensione zip contenente le trascrizioni delle due, uno denominato 'kingstory.txt', l'altro una 'queenstory.txt'. All'interno di ogni file di testo normale, si specificherà la trascrizione completo corretta per l'audio corrisponda.

Dopo che è stato caricato il set di dati, ti aiuteremo a segmentare il file audio in espressioni basati sulla trascrizione fornita. È possibile controllare le trascrizioni corrispondente e le espressioni segmentate scaricando il set di dati. ID univoco viene assegnato automaticamente per le espressioni segmentate. È importante assicurarsi che le trascrizioni che è fornire sono accurati al 100%. Gli errori nella trascrizione possono ridurre la precisione durante la segmentazione audio e introdurre un'ulteriore perdita di qualità in fase di formazione che viene eseguita in seguito.

## <a name="audio-only-beta"></a>Solo l'audio (beta)

Se non si dispone di trascrizioni per le registrazioni audio, usare il **solo l'Audio** opzione per caricare i dati. Il nostro sistema consentono di segmentare e trascrivere i file audio. Tenere presente che questo servizio verrà considerato come l'utilizzo della sottoscrizione per il riconoscimento vocale.

Seguire queste linee guida durante la preparazione di audio.

> [!NOTE]
> Il servizio di long-audio segmentazione sfrutterà la funzionalità di trascrizione di batch di riconoscimento vocale in testo, che supporta solo gli utenti sottoscrizione standard (S0).

| Proprietà | Value |
| -------- | ----- |
| Formato file | RIFF (. wav) con una frequenza di campionamento di almeno 16 khz 16-bit nel PCM o MP3 con una velocità in bit di almeno 256 KBps, raggruppati in un file con estensione zip |
| Nome file | Solo caratteri ASCII. I caratteri Unicode nel nome avrà esito negativo (ad esempio, i caratteri cinesi o simboli, ad esempio ","). Non è consentito: nome duplicato. |
| Lunghezza audio | Più di 20 secondi |
| Formato di archiviazione | zip |
| Dimensione massima dell'archivio | 200 MB |

Tutti i file audio devono essere raggruppati in un file zip. Nel file zip non è consentita alcuna sottocartella. Dopo che è stato caricato il set di dati, ti aiuteremo a segmentare il file audio in espressioni basati sul nostro servizio di trascrizione vocale batch. ID univoco viene assegnato automaticamente per le espressioni segmentate. Corrispondenza trascrizioni verrà generato tramite il riconoscimento vocale. Tutti i file MP3 verranno trasformati in formato WAV dopo l'elaborazione. È possibile controllare le trascrizioni corrispondente e le espressioni segmentate scaricando il set di dati.

## <a name="next-steps"></a>Passaggi successivi

- [Creare una voce personalizzata](how-to-custom-voice-create-voice.md)
- [Guida: Esempi di voce di record](record-custom-voice-samples.md)
