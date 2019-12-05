---
title: Come preparare i dati per il servizio di riconoscimento vocale personalizzato
titleSuffix: Azure Cognitive Services
description: Creare una voce personalizzata per il marchio con il servizio di riconoscimento vocale. Si forniscono le registrazioni di studio e gli script associati, il servizio genera un modello Voice univoco ottimizzato per la voce registrata. Usa questa voce per sintetizzare la sintesi vocale nei tuoi prodotti, strumenti e applicazioni.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: erhopf
ms.openlocfilehash: 5427e9f996fb77d455aa8064fc7cb1c65e1fcf7e
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74805978"
---
# <a name="prepare-data-to-create-a-custom-voice"></a>Preparare i dati per creare una voce personalizzata

Quando si è pronti per creare una voce di sintesi vocale personalizzata per l'applicazione, il primo passaggio consiste nel raccogliere le registrazioni audio e gli script associati per avviare il training del modello vocale. Il servizio di riconoscimento vocale usa questi dati per creare una voce univoca ottimizzata in modo da corrispondere alla voce nelle registrazioni. Dopo aver eseguito il training della voce, è possibile iniziare a sintetizzare la sintesi vocale nelle applicazioni.

È possibile iniziare con una piccola quantità di dati per creare un modello di prova. Tuttavia, maggiore è il numero di dati forniti, più naturale sarà il suono della voce personalizzata. Prima di poter eseguire il training di un modello vocale di sintesi vocale, sono necessarie registrazioni audio e le trascrizioni del testo associate. In questa pagina, verranno esaminati i tipi di dati, il modo in cui vengono usati e come gestirli.

## <a name="data-types"></a>Tipi di dati

Un set di dati di training vocale include registrazioni audio e un file di testo con le trascrizioni associate. Ogni file audio deve contenere un singolo enunciato (una singola frase o un singolo turno per un sistema di dialogo) e essere di lunghezza inferiore a 15 secondi.

In alcuni casi, è possibile che non si disponga del set di dati appropriato e che si voglia testare il training vocale personalizzato con i file audio disponibili, short o Long, con o senza trascrizioni. Sono disponibili strumenti (beta) che consentono di segmentare l'audio in espressioni e di preparare le trascrizioni usando l' [API di trascrizione batch](batch-transcription.md).

Questa tabella elenca i tipi di dati e il modo in cui ognuno viene usato per creare un modello vocale personalizzato per la sintesi vocale.

| Tipo di dati | Description | Quando usare questa opzione | Richiesto servizio aggiuntivo | Quantità per il training di un modello | Impostazioni locali |
| --------- | ----------- | ----------- | --------------------------- | ----------------------------- | --------- |
| **Singole espressioni + trascrizione corrispondente** | Raccolta (zip) di file audio (WAV) come singoli enunciati. Ogni file audio deve avere una lunghezza di 15 secondi o inferiore, associato a una trascrizione formattata (con estensione txt). | Registrazioni professionali con trascrizioni corrispondenti | Pronto per il training. | Nessun requisito rigido per en-US e zh-CN. Oltre 2000 espressioni distinte per altre impostazioni locali. | [Tutte le impostazioni locali vocali personalizzate](language-support.md#customization) |
| **Audio lungo + trascrizione (beta)** | Raccolta (con estensione zip) di file audio lunghi e non segmentati (più di 20 secondi), abbinati a una trascrizione (con estensione txt) che contiene tutte le parole vocali. | Sono presenti file audio e trascrizioni corrispondenti, ma non sono segmentati in espressioni. | Segmentazione (usando la trascrizione batch).<br>Trasformazione del formato audio, ove necessario. | Nessun requisito rigido  | [Tutte le impostazioni locali vocali personalizzate](language-support.md#customization) |
| **Solo audio (beta)** | Raccolta (zip) di file audio senza una trascrizione. | Sono disponibili solo file audio, senza trascrizioni. | Segmentazione + generazione della trascrizione (usando la trascrizione batch).<br>Trasformazione del formato audio, ove necessario.| Nessun requisito rigido | [Tutte le impostazioni locali vocali personalizzate](language-support.md#customization) |

I file devono essere raggruppati per tipo in un set di dati e caricati come file zip. Ogni set di dati può contenere solo un singolo tipo di dati.

> [!NOTE]
> Il numero massimo di set di dati che possono essere importati per ogni sottoscrizione è 10 file zip per gli utenti della sottoscrizione gratuita (F0) e 500 per gli utenti con sottoscrizione standard (S0).

## <a name="individual-utterances--matching-transcript"></a>Singole espressioni + trascrizione corrispondente

È possibile preparare le registrazioni di singole espressioni e la trascrizione corrispondente in due modi. Scrivere uno script e leggerlo da un talento vocale o usare l'audio disponibile pubblicamente e trasmetterlo in testo. Nel secondo caso, modificare le disfluenze dai file audio, ad esempio "um" e altri suoni riempitivi, balbettii, parole mormorate o errori di pronuncia.

Per produrre un tipo di carattere vocale appropriato, creare le registrazioni in una stanza silenziosa con un microfono di alta qualità. Il volume coerente, la velocità di pronuncia, il pitch di pronuncia e i comportamenti espressivi di sintesi vocale sono essenziali.

> [!TIP]
> Per creare una voce per l'uso in produzione, è consigliabile avvalersi di uno studio di registrazione e uno speaker professionali. Per altre informazioni, vedere [Come registrare esempi vocali per una voce personalizzata](record-custom-voice-samples.md).

### <a name="audio-files"></a>File audio

Ogni file audio deve contenere un singolo enunciato, ovvero una singola frase o un solo turno di un sistema di dialogo, con una lunghezza inferiore a 15 secondi. Tutti i file devono trovarsi nella stessa lingua parlata. Le voci di sintesi vocale personalizzate multi-linguaggio non sono supportate, ad eccezione della lingua inglese cinese (BI). Ogni file audio deve avere un nome di file numerico univoco con il nome file Extension. wav.

Seguire queste linee guida durante la preparazione dell'audio.

| Proprietà | Value |
| -------- | ----- |
| Formato file | RIFF (WAV), raggruppati in un file con estensione zip |
| Frequenza di campionamento | Almeno 16.000 Hz |
| Formato del campione | PCM, 16 bit |
| Nome file | Numeric, con estensione wav. Non sono consentiti nomi file duplicati. |
| Lunghezza audio | Più breve di 15 secondi |
| Formato di archiviazione | zip |
| Dimensione massima dell'archivio | 2048 MB |

> [!NOTE]
> i file WAV con frequenza di campionamento inferiore a 16.000 Hz verranno rifiutati. Se un file con estensione zip contiene file con estensione wav con frequenze di campionamento diverse, verranno importati solo i valori uguali o superiori a 16.000 Hz. Il portale attualmente importa archivi ZIP fino a 200 MB. È tuttavia possibile caricare più archivi.

### <a name="transcripts"></a>Trascrizioni

Il file di trascrizione è un file di testo normale. Usare queste linee guida per preparare le trascrizioni.

| Proprietà | Value |
| -------- | ----- |
| Formato file | Testo normale (con estensione txt) |
| Formato di codifica. | ANSI/ASCII, UTF-8, UTF-8-BOM, UTF-16-LE o UTF-16-BE. Per le codifiche zh-CN, ANSI/ASCII e UTF-8 non sono supportate. |
| N. di espressioni per riga | **Uno** -ogni riga del file di trascrizione deve contenere il nome di uno dei file audio, seguito dalla trascrizione corrispondente. Il nome del file deve essere separato dalla trascrizione mediante un carattere di tabulazione (\t). |
| Dimensione massima dei file | 2048 MB |

Di seguito è riportato un esempio di come le trascrizioni siano organizzate in base all'espressione in un file con estensione txt:

```
0000000001[tab] This is the waistline, and it's falling.
0000000002[tab] We have trouble scoring.
0000000003[tab] It was Janet Maslin.
```
È importante che le trascrizioni siano trascrizioni accurate del 100% dell'audio corrispondente. Gli errori nelle trascrizioni introdurranno una perdita di qualità durante il training.

> [!TIP]
> Quando si compilano voci di sintesi vocale di produzione, selezionare espressioni (o scrivere script) che tengano conto sia della copertura fonetica che dell'efficienza. Problemi nell'ottenere i risultati desiderati? [Contattare il team del Voice personalizzato](mailto:speechsupport@microsoft.com) per scoprire di più su come consultarlo.

## <a name="long-audio--transcript-beta"></a>Audio lungo + trascrizione (beta)

In alcuni casi, l'audio segmentato potrebbe non essere disponibile. Viene fornito un servizio (beta) tramite il portale vocale personalizzato che consente di segmentare i file audio lunghi e creare trascrizioni. Tenere presente che questo servizio verrà addebitato all'utilizzo della sottoscrizione di riconoscimento vocale.

> [!NOTE]
> Il servizio di segmentazione dell'audio lungo utilizzerà la funzionalità di trascrizione batch di riconoscimento vocale, che supporta solo gli utenti di sottoscrizione standard (S0). Durante l'elaborazione della segmentazione, i file audio e le trascrizioni verranno inviati anche al servizio Riconoscimento vocale personalizzato per perfezionare il modello di riconoscimento, in modo che l'accuratezza possa essere migliorata per i dati. Durante questo processo non verranno conservati dati. Al termine della segmentazione, verranno archiviati solo gli enunciati segmentati e le relative trascrizioni di mapping per il download e il training.

### <a name="audio-files"></a>File audio

Seguire queste linee guida durante la preparazione dell'audio per la segmentazione.

| Proprietà | Value |
| -------- | ----- |
| Formato file | RIFF (WAV) con frequenza di campionamento di almeno 16 kHz-16 bit in PCM o. mp3 con una velocità in bit di almeno 256 KBps, raggruppati in un file con estensione zip |
| Nome file | Sono supportati i caratteri ASCII e Unicode. Non sono consentiti nomi duplicati. |
| Lunghezza audio | Più di 20 secondi |
| Formato di archiviazione | zip |
| Dimensione massima dell'archivio | 2048 MB |

Tutti i file audio devono essere raggruppati in un file zip. È possibile inserire i file con estensione wav e MP3 in un unico file ZIP audio. Ad esempio, è possibile caricare un file zip contenente un file audio denominato ' kingstory. wav ', 45-second-Long e un altro audio denominato ' queenstory. mp3', 200-Second-Long. Tutti i file con estensione mp3 verranno trasformati nel formato WAV dopo l'elaborazione.

### <a name="transcripts"></a>Trascrizioni

Le trascrizioni devono essere preparate alle specifiche elencate in questa tabella. Ogni file audio deve corrispondere a una trascrizione.

| Proprietà | Value |
| -------- | ----- |
| Formato file | Testo normale (con estensione txt), raggruppati in un file con estensione zip |
| Nome file | USA lo stesso nome del file audio corrispondente |
| Formato di codifica. | Solo UTF-8-BOM |
| N. di espressioni per riga | Senza limiti |
| Dimensione massima dei file | 2048 MB |

Tutti i file delle trascrizioni in questo tipo di dati devono essere raggruppati in un file zip. Ad esempio, è stato caricato un file zip contenente un file audio denominato ' kingstory. wav ', 45 secondi e un altro denominato ' queenstory. mp3', 200 secondi di lunghezza. Sarà necessario caricare un altro file zip contenente due trascrizioni, una con il nome "kingstory. txt", l'altra "queenstory. txt". All'interno di ogni file di testo normale, viene fornita la trascrizione completa corretta per l'audio corrispondente.

Dopo che il set di dati è stato caricato correttamente, sarà possibile segmentare il file audio in espressioni basate sulla trascrizione fornita. È possibile controllare le espressioni segmentate e le trascrizioni corrispondenti scaricando il set di dati. Gli ID univoci verranno assegnati automaticamente agli enunciati segmentati. È importante assicurarsi che le trascrizioni fornite siano accurate al 100%. Gli errori nelle trascrizioni possono ridurre la precisione durante la segmentazione audio e introdurre ulteriore perdita di qualità nella fase di training che verrà rilevata in un secondo momento.

## <a name="audio-only-beta"></a>Solo audio (beta)

Se non si hanno trascrizioni per le registrazioni audio, usare l'opzione **solo audio** per caricare i dati. Il sistema può essere utile per segmentare e trascrivere i file audio. Tenere presente che questo servizio verrà conteggiato per l'utilizzo della sottoscrizione di riconoscimento vocale.

Seguire queste linee guida durante la preparazione dell'audio.

> [!NOTE]
> Il servizio di segmentazione dell'audio lungo utilizzerà la funzionalità di trascrizione batch di riconoscimento vocale, che supporta solo gli utenti di sottoscrizione standard (S0).

| Proprietà | Value |
| -------- | ----- |
| Formato file | RIFF (WAV) con frequenza di campionamento di almeno 16 kHz-16 bit in PCM o. mp3 con una velocità in bit di almeno 256 KBps, raggruppati in un file con estensione zip |
| Nome file | Sono supportati i caratteri ASCII e Unicode. Nessun nome duplicato consentito. |
| Lunghezza audio | Più di 20 secondi |
| Formato di archiviazione | zip |
| Dimensione massima dell'archivio | 2048 MB |

Tutti i file audio devono essere raggruppati in un file zip. Una volta che il set di dati è stato caricato correttamente, sarà possibile segmentare il file audio in espressioni basate sul servizio di trascrizione di batch vocale. Gli ID univoci verranno assegnati automaticamente agli enunciati segmentati. Le trascrizioni corrispondenti verranno generate tramite il riconoscimento vocale. Tutti i file con estensione mp3 verranno trasformati nel formato WAV dopo l'elaborazione. È possibile controllare le espressioni segmentate e le trascrizioni corrispondenti scaricando il set di dati.

## <a name="next-steps"></a>Passaggi successivi

- [Creare una voce personalizzata](how-to-custom-voice-create-voice.md)
- [Guida: registrare gli esempi di voce](record-custom-voice-samples.md)
