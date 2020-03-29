---
title: Come preparare i dati per Custom Voice - Servizio di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Crea una voce personalizzata per il tuo brand con il servizio di riconoscimento vocale. Per fornire le registrazioni in studio e gli script associati, il servizio genera un modello vocale univoco sintonizzato sulla voce registrata. Usa questa voce per sintetizzare il parlato nei tuoi prodotti, strumenti e applicazioni.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: erhopf
ms.openlocfilehash: 5427e9f996fb77d455aa8064fc7cb1c65e1fcf7e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "74805978"
---
# <a name="prepare-data-to-create-a-custom-voice"></a>Preparare i dati per creare una voce personalizzataPrepare data to create a custom voice

Quando si è pronti a creare una voce di sintesi vocale personalizzata per l'applicazione, il primo passaggio consiste nel raccogliere registrazioni audio e script associati per iniziare a eseguire il training del modello vocale. Il servizio di riconoscimento vocale utilizza questi dati per creare una voce univoca sintonizzata per abbinare la voce nelle registrazioni. Dopo aver eseguito il training della voce, è possibile iniziare a sintetizzare il riconoscimento vocale nelle applicazioni.

È possibile iniziare con una piccola quantità di dati per creare una prova di concetto. Tuttavia, più dati fornisci, più naturale sarà la tua voce personalizzata. Prima di poter eseguire il training del proprio modello vocale di sintesi vocale, sono necessarie registrazioni audio e le trascrizioni di testo associate. In questa pagina esamineremo i tipi di dati, il modo in cui vengono usati e come gestirlo.

## <a name="data-types"></a>Tipi di dati

Un set di dati di training vocale include registrazioni audio e un file di testo con le trascrizioni associate. Ogni file audio deve contenere una singola espressione (una singola frase o un singolo giro per un sistema di finestre di dialogo) ed essere lungo meno di 15 secondi.

In alcuni casi, è possibile che il set di dati sia pronto e che si desideri testare il training vocale personalizzato con i file audio disponibili, brevi o lunghi, con o senza trascrizioni. Forniamo strumenti (beta) che consentono di segmentare l'audio in espressioni e preparare le trascrizioni utilizzando [l'API trascrizione batch](batch-transcription.md).

In questa tabella sono elencati i tipi di dati e il modo in cui ognuno viene utilizzato per creare un modello vocale di sintesi vocale personalizzato.

| Tipo di dati | Descrizione | Utilizzo | Servizio aggiuntivo richiesto | Quantità per il training di un modello | Impostazioni locali |
| --------- | ----------- | ----------- | --------------------------- | ----------------------------- | --------- |
| **Singole espressioni: trascrizione corrispondente** | Raccolta (.zip) di file audio (wav) come singole espressioni. Ogni file audio deve avere una lunghezza massima di 15 secondi, associata a una trascrizione formattata (.txt). | Registrazioni professionali con trascrizioni corrispondenti | Pronti per l'allenamento. | Nessun requisito rigido per en-US e zh-CN. Più di 2.000 espressioni distinte per altre impostazioni locali. | [Tutte le impostazioni locali di Custom Voice](language-support.md#customization) |
| **Audio lungo - trascrizione (beta)** | Raccolta (.zip) di file audio lunghi e non segmentati (più lunghi di 20 secondi), associata a una trascrizione (.txt) che contiene tutte le parole pronunciate. | Si dispone di file audio e trascrizioni corrispondenti, ma non sono segmentati in espressioni. | Segmentazione (utilizzando la trascrizione batch).<br>Trasformazione del formato audio dove richiesto. | Nessun requisito rigido  | [Tutte le impostazioni locali di Custom Voice](language-support.md#customization) |
| **Solo audio (beta)** | Raccolta (.zip) di file audio senza trascrizione. | Hai solo file audio disponibili, senza trascrizioni. | Segmentazione: generazione di trascrizioni (utilizzando la trascrizione batch).<br>Trasformazione del formato audio dove richiesto.| Nessun requisito rigido | [Tutte le impostazioni locali di Custom Voice](language-support.md#customization) |

I file devono essere raggruppati per tipo in un set di dati e caricati come file zip. Ogni set di dati può contenere un solo tipo di dati.

> [!NOTE]
> Il numero massimo di set di dati che possono essere importati per ogni sottoscrizione è 10 file .zip per gli utenti di sottoscrizione gratuita (F0) e 500 per gli utenti di sottoscrizione standard (S0).

## <a name="individual-utterances--matching-transcript"></a>Singole espressioni: trascrizione corrispondente

È possibile preparare le registrazioni di singole espressioni e la trascrizione corrispondente in due modi. Scrivere un copione e farlo leggere da un talento vocale o utilizzare l'audio disponibile al pubblico e trascriverlo al testo. Nel secondo caso, modificare le disfluenze dai file audio, ad esempio "um" e altri suoni riempitivi, balbettii, parole mormorate o errori di pronuncia.

Per produrre un buon font vocale, creare le registrazioni in una stanza tranquilla con un microfono di alta qualità. Volume costante, velocità di parola, tono di parola e manierismi espressivi del discorso sono essenziali.

> [!TIP]
> Per creare una voce per l'uso in produzione, è consigliabile avvalersi di uno studio di registrazione e uno speaker professionali. Per altre informazioni, vedere [Come registrare esempi vocali per una voce personalizzata](record-custom-voice-samples.md).

### <a name="audio-files"></a>File audio

Ogni file audio deve contenere una singola espressione (una singola frase o un singolo giro di un sistema di finestre di dialogo), con una durata inferiore a 15 secondi. Tutti i file devono essere nella stessa lingua parlata. Le voci di sintesi vocale personalizzate in più lingue non sono supportate, ad eccezione del multilingue cinese-inglese. Ogni file audio deve avere un nome di file numerico univoco con estensione wav.

Seguire queste linee guida durante la preparazione dell'audio.

| Proprietà | valore |
| -------- | ----- |
| Formato file | RIFF (.wav), raggruppati in un file .zip |
| Frequenza di campionamento | Almeno 16.000 Hz |
| Formato del campione | PCM, 16 bit |
| Nome file | Numerico, con estensione .wav. Non sono consentiti nomi di file duplicati. |
| Lunghezza audio | Inferiore a 15 secondi |
| Formato di archiviazione | zip |
| Dimensione massima dell'archivio | 2048 MB |

> [!NOTE]
> I file .wav con una frequenza di campionamento inferiore a 16.000 Hz verranno rifiutati. Se un file .zip contiene file .wav con frequenze di campionamento diverse, verranno importati solo quelli uguali o superiori a 16.000 Hz. Il portale attualmente importa archivi ZIP fino a 200 MB. È tuttavia possibile caricare più archivi.

### <a name="transcripts"></a>Trascrizioni

Il file di trascrizione è un file di testo normale. Usa queste linee guida per preparare le trascrizioni.

| Proprietà | valore |
| -------- | ----- |
| Formato file | Testo normale (con estensione txt) |
| Formato di codifica. | ANSI/ASCII, UTF-8, UTF-8-BOM, UTF-16-LE o UTF-16-BE. Per le codifiche zh-CN, le codifiche ANSI/ASCII e UTF-8 non sono supportate. |
| N. di espressioni per riga | **Uno** - Ogni riga del file di trascrizione deve contenere il nome di uno dei file audio, seguito dalla trascrizione corrispondente. Il nome del file deve essere separato dalla trascrizione mediante un carattere di tabulazione (\t). |
| Dimensione massima dei file | 2048 MB |

Di seguito è riportato un esempio di come le trascrizioni sono organizzate espressioni per espressione in un file con estensione txt:

```
0000000001[tab] This is the waistline, and it's falling.
0000000002[tab] We have trouble scoring.
0000000003[tab] It was Janet Maslin.
```
È importante che le trascrizioni siano trascrizioni accurate al 100% dell'audio corrispondente. Gli errori nelle trascrizioni introdurranno una perdita di qualità durante la formazione.

> [!TIP]
> Quando si creano voci di sintesi vocale di produzione, selezionare espressioni (o scrivere script) che tengano conto sia della copertura fonetica che dell'efficienza. Problemi nell'ottenere i risultati desiderati? [Contatta il](mailto:speechsupport@microsoft.com) team Di Custom Voice per saperne di più su come farci consultare.

## <a name="long-audio--transcript-beta"></a>Audio lungo - trascrizione (beta)

In alcuni casi, è possibile che l'audio segmentato non sia disponibile. Forniamo un servizio (beta) attraverso il portale vocale personalizzato per aiutarti a segmentare i file audio lunghi e creare trascrizioni. Tieni presente che questo servizio verrà addebitato per l'utilizzo dell'abbonamento da sintesi vocale.

> [!NOTE]
> Il servizio di segmentazione audio lungo sfrutterà la funzionalità di trascrizione batch di sintesi vocale, che supporta solo gli utenti di sottoscrizione standard (S0). Durante l'elaborazione della segmentazione, i file audio e le trascrizioni verranno inviati anche al servizio di riconoscimento vocale personalizzato per perfezionare il modello di riconoscimento in modo da migliorare l'accuratezza dei dati. Durante questo processo non verranno conservati dati. Al termine della segmentazione, verranno archiviate solo le espressioni segmentate e le relative trascrizioni di mapping per il download e la formazione.

### <a name="audio-files"></a>File audio

Seguire queste linee guida durante la preparazione dell'audio per la segmentazione.

| Proprietà | valore |
| -------- | ----- |
| Formato file | RIFF (.wav) con una frequenza di campionamento di almeno 16 khz-16 bit in PCM o .mp3 con una velocità in bit di almeno 256 KBps, raggruppati in un file .zip |
| Nome file | Caratteri ASCII e Unicode supportati. Non sono consentiti nomi duplicati. |
| Lunghezza audio | Più di 20 secondi |
| Formato di archiviazione | zip |
| Dimensione massima dell'archivio | 2048 MB |

Tutti i file audio devono essere raggruppati in un file zip. E 'OK per mettere i file .wav e .mp3 in un file audio. Ad esempio, è possibile caricare un file zip contenente un file audio denominato 'kingstory.wav', 45 secondi-lungo, e un altro audio denominato 'queenstory.mp3', 200 secondi-lungo. Tutti i file .mp3 verranno trasformati nel formato .wav dopo l'elaborazione.

### <a name="transcripts"></a>Trascrizioni

Le trascrizioni devono essere preparate in base alle specifiche elencate in questa tabella. Ogni file audio deve essere abbinato a una trascrizione.

| Proprietà | valore |
| -------- | ----- |
| Formato file | Testo normale (.txt), raggruppato in un file .zip |
| Nome file | Utilizzare lo stesso nome del file audio corrispondente |
| Formato di codifica. | Solo UTF-8-BOM |
| N. di espressioni per riga | Nessun limite |
| Dimensione massima dei file | 2048 MB |

Tutti i file di trascrizione in questo tipo di dati devono essere raggruppati in un file zip. Ad esempio, è stato caricato un file zip contenente un file audio denominato 'kingstory.wav', lungo 45 secondi e un altro denominato 'queenstory.mp3', lungo 200 secondi. Sarà necessario caricare un altro file zip contenente due trascrizioni, una denominata 'kingstory.txt', l'altra 'queenstory.txt'. All'interno di ogni file di testo normale, si fornirà la trascrizione corretta completa per l'audio corrispondente.

Dopo aver caricato correttamente il set di dati, sarà possibile segmentare il file audio in espressioni in base alla trascrizione fornita. È possibile controllare le espressioni segmentate e le trascrizioni corrispondenti scaricando il set di dati. Gli ID univoci verranno assegnati automaticamente alle espressioni segmentate. È importante assicurarsi che le trascrizioni fornite siano accurate al 100%. Gli errori nelle trascrizioni possono ridurre la precisione durante la segmentazione audio e introdurre ulteriormente la perdita di qualità nella fase di allenamento successiva.

## <a name="audio-only-beta"></a>Solo audio (beta)

Se non si dispone di trascrizioni per le registrazioni audio, utilizzare l'opzione **Solo audio** per caricare i dati. Il nostro sistema può aiutarti a segmentare e trascrivere i tuoi file audio. Tieni presente che questo servizio conterà per l'utilizzo della sottoscrizione da sintesi vocale.

Seguire queste linee guida durante la preparazione dell'audio.

> [!NOTE]
> Il servizio di segmentazione audio lungo sfrutterà la funzionalità di trascrizione batch di sintesi vocale, che supporta solo gli utenti di sottoscrizione standard (S0).

| Proprietà | valore |
| -------- | ----- |
| Formato file | RIFF (.wav) con una frequenza di campionamento di almeno 16 khz-16 bit in PCM o .mp3 con una velocità in bit di almeno 256 KBps, raggruppati in un file .zip |
| Nome file | Caratteri ASCII e Unicode supportati. Nessun nome duplicato consentito. |
| Lunghezza audio | Più di 20 secondi |
| Formato di archiviazione | zip |
| Dimensione massima dell'archivio | 2048 MB |

Tutti i file audio devono essere raggruppati in un file zip. Una volta caricato correttamente il set di dati, sarà possibile segmentare il file audio in espressioni in base al servizio di trascrizione batch vocale. Gli ID univoci verranno assegnati automaticamente alle espressioni segmentate. Le trascrizioni corrispondenti verranno generate tramite il riconoscimento vocale. Tutti i file .mp3 verranno trasformati nel formato .wav dopo l'elaborazione. È possibile controllare le espressioni segmentate e le trascrizioni corrispondenti scaricando il set di dati.

## <a name="next-steps"></a>Passaggi successivi

- [Creare una voce personalizzata](how-to-custom-voice-create-voice.md)
- [Guida: Registrare i campioni vocali](record-custom-voice-samples.md)
