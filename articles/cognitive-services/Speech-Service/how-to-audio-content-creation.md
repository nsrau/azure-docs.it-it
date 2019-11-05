---
title: Creazione di contenuto audio-servizio riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: La creazione di contenuti audio è uno strumento online che consente di personalizzare e ottimizzare l'output di sintesi vocale di Microsoft per le app e i prodotti.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/26/2019
ms.author: erhopf
ms.openlocfilehash: 8e3a76abe840a70ba7410855cde1d82ec96c28eb
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73506956"
---
# <a name="audio-content-creation"></a>Creazione di contenuto audio

La [creazione di contenuti audio](https://aka.ms/audiocontentcreation) è uno strumento online che consente di personalizzare e ottimizzare l'output di sintesi vocale di Microsoft per le app e i prodotti. È possibile usare questo strumento per ottimizzare le voci pubbliche e personalizzate per espressioni naturali più accurate e per gestire l'output nel cloud.

Lo strumento di creazione di contenuti audio è basato sul [linguaggio di markup sintesi vocale (SSML)](speech-synthesis-markup.md). Per semplificare la personalizzazione e l'ottimizzazione, la creazione di contenuti audio consente di esaminare visivamente gli output di sintesi vocale in tempo reale.

## <a name="how-does-it-work"></a>Come funziona?

Questo diagramma illustra i passaggi necessari per ottimizzare ed esportare output di sintesi vocale personalizzati. Usare i collegamenti seguenti per ulteriori informazioni su ogni passaggio.

![](media/audio-content-creation/audio-content-creation-diagram.jpg)

1. Il primo passaggio consiste nel [creare un account Azure, registrare una risorsa vocale e ottenere una chiave di sottoscrizione](#create-a-speech-resource). Quando si dispone di una chiave di sottoscrizione, è possibile utilizzarla per chiamare il servizio di riconoscimento vocale e per accedere alla [creazione del contenuto audio](https://aka.ms/audiocontentcreation).
2. [Creare un file di ottimizzazione audio](#create-an-audio-tuning-file) usando testo normale o SSML.
3. Scegliere la voce e la lingua che si desidera ottimizzare. La creazione di contenuto audio include tutte le voci di sintesi [vocale di Microsoft](language-support.md#text-to-speech). Puoi usare la tua voce standard, neurale o personalizzata.
   >[!NOTE]
   > L'accesso controllato è disponibile per le voci neurali personalizzate, che consentono di creare voci ad alta definizione simili alla voce naturale. Per ulteriori informazioni, vedere [processo](https://aka.ms/ignite2019/speech/ethics)di controllo.

4. Esaminare il risultato predefinito. Usare quindi lo strumento di ottimizzazione per modificare la pronuncia, il pitch, la velocità, l'intonazione, lo stile vocale e altro ancora. Per un elenco completo delle opzioni, vedere [linguaggio di markup sintesi vocale](speech-synthesis-markup.md).
5. Salvare ed [esportare l'audio ottimizzato](#export-tuned-audio). Quando si salva la traccia di ottimizzazione nel sistema, è possibile continuare a lavorare ed eseguire l'iterazione dell'output. Quando si è soddisfatti dell'output, è possibile creare un'attività di creazione audio con la funzionalità Esporta. È possibile osservare lo stato dell'attività di esportazione e scaricare l'output da usare con le app e i prodotti.
6. L'ultimo passaggio consiste nell'usare la voce ottimizzata personalizzata nelle app e nei prodotti.

## <a name="create-a-speech-resource"></a>Creare una risorsa di sintesi vocale

Attenersi alla procedura seguente per creare una risorsa vocale e connetterla con speech studio.

1. Seguire queste istruzioni per [iscriversi per ottenere un account Azure e creare una risorsa di riconoscimento vocale](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#create-a-speech-resource-in-azure). Verificare che il piano tariffario sia impostato su **S0**. Se si usa una delle voci neurali, assicurarsi di creare la risorsa in un' [area supportata](regions.md#standard-and-neural-voices).
2. Accedere alla [creazione del contenuto audio](https://aka.ms/audiocontentcreation).
3. Selezionare un progetto esistente oppure fare clic su **Crea nuovo**.
4. È possibile modificare la sottoscrizione in qualsiasi momento con l'opzione **Impostazioni** , situata nella parte superiore del NAV.

## <a name="create-an-audio-tuning-file"></a>Creare un file di ottimizzazione audio

Esistono due modi per ottenere il contenuto nello strumento di creazione del contenuto audio.

**Opzione 1:**

1. Dopo aver eseguito l'accesso alla [creazione di contenuti audio](https://aka.ms/audiocontentcreation), fare clic su **ottimizzazione audio** per creare un nuovo file di ottimizzazione audio.
2. Quando viene visualizzata la finestra di modifica, è possibile immettere fino a 10.000 caratteri.
3. Non dimenticare di salvarlo.

**Opzione 2:**

1. Dopo aver eseguito l'accesso alla [creazione di contenuti audio](https://aka.ms/audiocontentcreation), fare clic su **carica** per importare uno o più file di testo. Sono supportati sia testo normale che SSML.
2. Quando si caricano i file di testo, verificare che il contenuto soddisfi questi requisiti.

   | Proprietà | Valore/note |
   |----------|---------------|
   | Formato file | Testo normale (con estensione txt)<br/> Testo SSML (. txt)<br/> I file zip non sono supportati |
   | Formato di codifica | UTF-8 |
   | Nome file | Ogni file deve avere un nome univoco. I duplicati non sono supportati. |
   | Lunghezza del testo | I file di testo non devono superare i 10.000 caratteri. |
   | Restrizioni SSML | Ogni file SSML può contenere solo un singolo elemento di SSML. |

### <a name="plain-text-example"></a>Esempio di testo normale

```txt
Welcome to use Audio Content Creation to customize audio output for your products.
```

### <a name="ssml-text-example"></a>Esempio di testo SSML

```xml
<speak xmlns="http://www.w3.org/2001/10/synthesis" xmlns:mstts="http://www.w3.org/2001/mstts" version="1.0" xml:lang="en-US">
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, JessaNeural)">
    Welcome to use Audio Content Creation <break time="10ms" />to customize audio output for your products.
    </voice>
</speak>
```

## <a name="export-tuned-audio"></a>Esporta audio ottimizzato

Dopo aver esaminato l'output audio e aver soddisfatto l'ottimizzazione e la regolazione, è possibile esportare l'audio.

1. Dallo strumento di [creazione del contenuto audio](https://aka.ms/audiocontentcreation) fare clic su **Esporta** per creare un'attività di creazione audio.
2. Scegliere il formato di output per l'audio ottimizzato. Di seguito è riportato un elenco dei formati supportati e delle frequenze di campionamento.
3. È possibile visualizzare lo stato dell'attività nella scheda **Esporta attività** . Se l'attività non riesce, vedere la pagina informazioni dettagliate per un report completo.
4. Al termine dell'attività, l'audio sarà disponibile per il download nella scheda **libreria audio** .
5. Fare clic su **Download**. A questo punto si è pronti per usare l'audio ottimizzato personalizzato nelle app o nei prodotti.

### <a name="supported-audio-formats"></a>Formati audio supportati

| Format | frequenza di campionamento di 16 kHz | frequenza di campionamento di 24 kHz |
|--------|--------------------|--------------------|
| wav | riff-16kHz-16 bit-mono-PCM | riff-24kHz-16 bit-mono-PCM |
| mp3 | audio-16kHz-128kbitrate-mono-MP3 | audio-24kHz-160kbitrate-mono-MP3 |

## <a name="see-also"></a>Vedere anche

* [API Long audio](https://aka.ms/long-audio-api)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Speech studio](https://speech.microsoft.com)
