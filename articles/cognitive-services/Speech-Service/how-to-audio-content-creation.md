---
title: Creazione di contenuti audio - Servizio di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: La creazione di contenuti audio è uno strumento online che consente di personalizzare e ottimizzare l'output di sintesi vocale di Microsoft per le app e i prodotti.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: dapine
ms.openlocfilehash: ab0d2b8d95b4cb5996dd93fa0bb24085c9de26d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78331537"
---
# <a name="improve-synthesis-with-audio-content-creation"></a>Migliorare la sintesi con la creazione di contenuti audio

[La creazione di contenuti audio](https://aka.ms/audiocontentcreation) è uno strumento online che consente di personalizzare e ottimizzare l'output di sintesi vocale di Microsoft per le app e i prodotti. È possibile utilizzare questo strumento per ottimizzare le voci pubbliche e personalizzate per le espressioni naturali più accurate e gestire l'output nel cloud.

Lo strumento di creazione di contenuti audio è basato su [Speech Synthesis Markup Language (SSML)](speech-synthesis-markup.md). Per semplificare la personalizzazione e l'ottimizzazione, Audio Content Creation consente di controllare visivamente gli output di sintesi vocale in tempo reale.

## <a name="how-does-it-work"></a>Come funziona?

Questo diagramma mostra i passaggi necessari per ottimizzare ed esportare output di sintesi vocale personalizzati. Utilizzare i collegamenti seguenti per ulteriori informazioni su ogni passaggio.

![](media/audio-content-creation/audio-content-creation-diagram.jpg)

1. Il primo passaggio consiste nel [creare un account Azure, registrare una risorsa di riconoscimento vocale e ottenere una chiave](#create-a-speech-resource)di sottoscrizione. Dopo aver creato una chiave di sottoscrizione, è possibile utilizzarla per chiamare il servizio di riconoscimento vocale e per accedere a [Creazione contenuto audio](https://aka.ms/audiocontentcreation).
2. [Creare un file di ottimizzazione audio](#create-an-audio-tuning-file) utilizzando testo normale o SSML.
3. Scegli la voce e la lingua da sintonizzare. La creazione di contenuto audio include tutte le voci di [sintesi vocale Microsoft.](language-support.md#text-to-speech) È possibile usare la voce standard, neurale o personalizzata.
   >[!NOTE]
   > L'accesso gated è disponibile per Custom Neural Voices, che consente di creare voci ad alta definizione simili al parlato dal suono naturale. Per ulteriori dettagli, vedere [Gating process](https://aka.ms/ignite2019/speech/ethics).

4. Esaminare il risultato predefinito. Quindi utilizzare lo strumento di sintonizzazione per regolare la pronuncia, passo, velocità, intonazione, stile vocale, e altro ancora. Per un elenco completo delle opzioni, vedere [Lingua markup di sintesi vocale](speech-synthesis-markup.md).
5. Salvare ed [esportare l'audio sintonizzato](#export-tuned-audio). Quando si salva la traccia di ottimizzazione nel sistema, è possibile continuare a lavorare e iterare sull'output. Quando si è soddisfatti dell'output, è possibile creare un'attività di creazione audio con la funzione di esportazione. È possibile osservare lo stato dell'attività di esportazione e scaricare l'output da usare con le app e i prodotti.
6. L'ultimo passaggio consiste nell'usare la voce sintonizzata personalizzata nelle app e nei prodotti.

## <a name="create-a-speech-resource"></a>Creare una risorsa di riconoscimento vocaleCreate a Speech resource

Seguire questi passaggi per creare una risorsa di riconoscimento vocale e connetterla con Speech Studio.Follow these steps to create a Speech resource and connect it with Speech Studio.

1. Seguire queste istruzioni per iscriversi a un account Azure e creare una risorsa di [riconoscimento](get-started.md#new-resource) [vocale.](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#create-the-resource) Assicurarsi che il piano tariffario sia impostato su **S0**. Se si utilizza una delle voci neurali, assicurarsi di creare la risorsa in [un'area supportata.](regions.md#standard-and-neural-voices)
2. Accedere a [Creazione contenuto audio](https://aka.ms/audiocontentcreation).
3. Selezionare un progetto esistente o fare clic su **Crea nuovo**.
4. È possibile modificare l'abbonamento in qualsiasi momento con l'opzione **Impostazioni,** che si trova nel riquadro di spostamento superiore.

## <a name="create-an-audio-tuning-file"></a>Creare un file di ottimizzazione audio

Esistono due modi per ottenere i contenuti nello strumento di creazione di contenuti audio.

**Opzione 1:**

1. Dopo aver effettuato l'accesso a [Creazione contenuto audio](https://aka.ms/audiocontentcreation), fare clic su **Ottimizzazione audio** per creare un nuovo file di ottimizzazione audio.
2. Quando viene visualizzata la finestra di modifica, è possibile immettere fino a 10.000 caratteri.
3. Non dimenticare di salvare.

**Opzione 2:**

1. Dopo aver effettuato l'accesso a [Creazione contenuto audio](https://aka.ms/audiocontentcreation), fare clic su **Carica** per importare uno o più file di testo. Sono supportati sia testo normale che SSML.
2. Quando carichi i file di testo, assicurati che il contenuto soddisfi questi requisiti.

   | Proprietà | Valore / Note |
   |----------|---------------|
   | Formato file | Testo normale (con estensione txt)<br/> Testo SSML (.txt)<br/> I file zip non sono supportati |
   | Formato di codifica. | UTF-8 |
   | Nome file | Ogni file deve avere un nome univoco. I duplicati non sono supportati. |
   | Lunghezza del testo | I file di testo non devono superare i 10.000 caratteri. |
   | Restrizioni SSML | Ogni file SSML può contenere solo una singola parte di SSML. |

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

## <a name="export-tuned-audio"></a>Esportare l'audio sintonizzato

Dopo aver esaminato l'output audio e aver verificato la messa a punto e la regolazione, è possibile esportare l'audio.

1. Dallo strumento [Creazione contenuto audio,](https://aka.ms/audiocontentcreation) fare clic su **Esporta** per creare un'attività di creazione audio.
2. Scegliere il formato di output per l'audio sintonizzato. Di seguito è riportato un elenco dei formati e delle frequenze di campionamento supportati.
3. È possibile visualizzare lo stato dell'attività nella scheda Attività di **esportazione.** Se l'attività non riesce, vedere la pagina delle informazioni dettagliate per un report completo.
4. Al termine dell'operazione, l'audio sarà disponibile per il download nella scheda **Libreria audio.**
5. Fare clic su **Download** Ora sei pronto per usare l'audio sintonizzato personalizzato nelle tue app o nei tuoi prodotti.

### <a name="supported-audio-formats"></a>Formati audio supportati

| Format | 16 kHz di campionamento | 24 kHz di campionamento |
|--------|--------------------|--------------------|
| wav | Riff-16khz-16bit-mono-pcm | Riff-24khz-16bit-mono-pcm |
| mp3 | audio-16khz-128kbitrate-mono-mp3 | audio-24khz-160kbitrate-mono-mp3 |

## <a name="see-also"></a>Vedere anche

* [API audio lunga](https://aka.ms/long-audio-api)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Speech Studio](https://speech.microsoft.com)
