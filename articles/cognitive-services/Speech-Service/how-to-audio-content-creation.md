---
title: Creazione di contenuto audio-servizio riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: La creazione di contenuti audio è uno strumento online che consente di personalizzare e ottimizzare l'output di sintesi vocale di Microsoft per le app e i prodotti.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: trbye
ms.openlocfilehash: 72fecbdc173a6174e54a28e48f983965f397ba6a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86224588"
---
# <a name="improve-synthesis-with-the-audio-content-creation-tool"></a>Migliorare la sintesi con lo strumento di creazione del contenuto audio

La [creazione di contenuti audio](https://aka.ms/audiocontentcreation) è uno strumento online che consente di personalizzare e ottimizzare l'output di sintesi vocale di Microsoft per le app e i prodotti. È possibile usare questo strumento per ottimizzare le voci pubbliche e personalizzate per espressioni naturali più accurate e per gestire l'output nel cloud.

Lo strumento di creazione di contenuti audio è basato sul [linguaggio di markup sintesi vocale (SSML)](speech-synthesis-markup.md). Per semplificare la personalizzazione e l'ottimizzazione, la creazione di contenuti audio consente di esaminare visivamente gli output di sintesi vocale in tempo reale.

Vedere l' [esercitazione video](https://www.youtube.com/watch?v=O1wIJ7mts_w) per la creazione di contenuto audio.

## <a name="how-does-it-work"></a>Come funziona?

Questo diagramma illustra i passaggi necessari per ottimizzare gli output di sintesi vocale. Usare i collegamenti seguenti per ulteriori informazioni su ogni passaggio.

![Diagramma dei passaggi necessari per ottimizzare gli output di sintesi vocale.](media/audio-content-creation/audio-content-creation-diagram.jpg)

1. [Configurare l'account Azure e la risorsa vocale](#set-up-your-azure-account-and-speech-resource) per iniziare.
2. [Creare un file di ottimizzazione audio](#create-an-audio-tuning-file) usando gli script SSML o testo normale.
3. Scegliere la voce e la lingua per il contenuto dello script. La creazione di contenuto audio include tutte le voci di sintesi [vocale di Microsoft](language-support.md#text-to-speech). Puoi usare la tua voce standard, neurale o personalizzata.
   >[!NOTE]
   > L'accesso controllato è disponibile per le voci neurali personalizzate, che consentono di creare voci ad alta definizione simili alla voce naturale. Per ulteriori informazioni, vedere [processo](https://aka.ms/ignite2019/speech/ethics)di controllo.

4. Esaminare l'output di sintesi predefinito. Migliorare quindi l'output regolando la pronuncia, le interruzioni, il pitch, la velocità, l'intonazione, lo stile vocale e altro ancora. Per un elenco completo delle opzioni, vedere [linguaggio di markup sintesi vocale](speech-synthesis-markup.md). Ecco un [video](https://youtu.be/mUvf2NbfuYU) che illustra come ottimizzare l'output vocale con la creazione di contenuto audio. 
5. Salvare ed [esportare l'audio ottimizzato](#export-tuned-audio). Quando si salva la traccia di ottimizzazione nel sistema, è possibile continuare a lavorare ed eseguire l'iterazione dell'output. Quando si è soddisfatti dell'output, è possibile creare un'attività di creazione audio con la funzionalità Esporta. È possibile osservare lo stato dell'attività di esportazione e scaricare l'output da usare con le app e i prodotti.

## <a name="set-up-your-azure-account-and-speech-resource"></a>Configurare l'account di Azure e la risorsa vocale

1. Per usare la creazione di contenuto audio, è necessario disporre di un account Azure. È possibile creare un account Azure usando il proprio account Microsoft. Seguire queste istruzioni per [configurare un account Azure](get-started.md#new-resource). 
2. [Creare una risorsa vocale](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#create-the-resource) per l'account Azure. Verificare che il piano tariffario sia impostato su **S0**. Se si usa una delle voci neurali, assicurarsi di creare la risorsa in un' [area supportata](regions.md#standard-and-neural-voices).
2. Una volta ottenuto l'account Azure e la risorsa vocale, è possibile usare i servizi di riconoscimento vocale e accedere alla [creazione di contenuto audio](https://aka.ms/audiocontentcreation).
3. Selezionare la risorsa vocale che si vuole usare. È anche possibile creare una nuova risorsa vocale qui. 
4. È possibile modificare la risorsa di riconoscimento vocale in qualsiasi momento con l'opzione **Impostazioni** , situata nella parte superiore del NAV.

## <a name="create-an-audio-tuning-file"></a>Creare un file di ottimizzazione audio

Esistono due modi per ottenere il contenuto nello strumento di creazione del contenuto audio.

**Opzione 1:**

1. Fare clic su **nuovo file** per creare un nuovo file di ottimizzazione audio.
2. Digitare o incollare il contenuto nella finestra di modifica. I caratteri per ogni file sono fino a 20.000. Se lo script è più lungo di 20.000 caratteri, è possibile usare l'opzione 2 per suddividere automaticamente il contenuto in più file. 
3. Non dimenticare di salvarlo.

**Opzione 2:**

1. Fare clic su **carica** per importare uno o più file di testo. Sono supportati sia testo normale che SSML.
2. Se il file di script è più di 20.000 caratteri, suddividere il file in base ai paragrafi, al carattere o alle espressioni regolari. 
3. Quando si caricano i file di testo, verificare che il file soddisfi questi requisiti.

   | Proprietà | Valore/note |
   |----------|---------------|
   | Formato file | Testo normale (con estensione txt)<br/> Testo SSML (. txt)<br/> I file zip non sono supportati |
   | Formato di codifica. | UTF-8 |
   | Nome file | Ogni file deve avere un nome univoco. I duplicati non sono supportati. |
   | Lunghezza del testo | I file di testo non devono superare i 20.000 caratteri. |
   | Restrizioni SSML | Ogni file SSML può contenere solo un singolo elemento di SSML. |

### <a name="plain-text-example"></a>Esempio di testo normale

```txt
Welcome to use Audio Content Creation to customize audio output for your products.
```

### <a name="ssml-text-example"></a>Esempio di testo SSML

```xml
<speak xmlns="http://www.w3.org/2001/10/synthesis" xmlns:mstts="http://www.w3.org/2001/mstts" version="1.0" xml:lang="en-US">
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, AriaNeural)">
    Welcome to use Audio Content Creation <break time="10ms" />to customize audio output for your products.
    </voice>
</speak>
```

## <a name="export-tuned-audio"></a>Esporta audio ottimizzato

Dopo aver esaminato l'output audio e aver soddisfatto l'ottimizzazione e la regolazione, è possibile esportare l'audio.

1. Fare clic su **Esporta** per creare un'attività di creazione audio. L' **esportazione nella libreria audio** è consigliata perché supporta l'output audio lungo e l'intera esperienza di output audio. È anche possibile scaricare direttamente il file audio nel disco locale, ma sono disponibili solo i primi 10 minuti. 
2. Scegliere il formato di output per l'audio ottimizzato. Di seguito è riportato un elenco dei formati supportati e delle frequenze di campionamento.
3. È possibile visualizzare lo stato dell'attività nella scheda **Esporta attività** . Se l'attività non riesce, vedere la pagina informazioni dettagliate per un report completo.
4. Al termine dell'attività, l'audio sarà disponibile per il download nella scheda **libreria audio** .
5. Fare clic su **Download**. A questo punto si è pronti per usare l'audio ottimizzato personalizzato nelle app o nei prodotti.

### <a name="supported-audio-formats"></a>Formati audio supportati

| Formato | frequenza di campionamento di 16 kHz | frequenza di campionamento di 24 kHz |
|--------|--------------------|--------------------|
| wav | riff-16kHz-16 bit-mono-PCM | riff-24kHz-16 bit-mono-PCM |
| mp3 | audio-16kHz-128kbitrate-mono-MP3 | audio-24kHz-160kbitrate-mono-MP3 |

## <a name="see-also"></a>Vedere anche

* [API Long audio](https://aka.ms/long-audio-api)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Speech Studio](https://speech.microsoft.com)
