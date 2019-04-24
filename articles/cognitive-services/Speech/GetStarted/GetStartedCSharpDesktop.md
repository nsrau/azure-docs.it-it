---
title: Introduzione all'API Riconoscimento vocale Bing con la libreria desktop C# | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: Sviluppare applicazioni Windows di base che usano l'API Riconoscimento vocale Bing per convertire audio parlato in testo.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: zhouwang
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 5f3b70a2dd9816210ed61280be38504a3980d205
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60515379"
---
# <a name="quickstart-use-the-bing-speech-recognition-api-in-c35-for-net-on-windows"></a>Guida introduttiva: Usare l'API Riconoscimento vocale Bing in C&#35; per .NET per dispositivi Windows

[!INCLUDE [Deprecation note](../../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

Questo articolo illustra come sviluppare un'applicazione Windows di base che usa l'API Riconoscimento vocale per convertire audio parlato in testo. L'uso della libreria client consente lo streaming in tempo reale, ossia quando l'applicazione client invia audio al servizio, in modo asincrono e simultaneo riceve risultati di riconoscimento parziali.

Gli sviluppatori che vogliono usare il Servizio di riconoscimento vocale da app eseguite in qualsiasi dispositivo possono usare la libreria desktop C#. Per usare la libreria, installare il [pacchetto NuGet Microsoft.ProjectOxford.SpeechRecognition-x86](https://www.nuget.org/packages/Microsoft.ProjectOxford.SpeechRecognition-x86/) per una piattaforma a 32 bit e il [pacchetto NuGet Microsoft.ProjectOxford.SpeechRecognition-x64](https://www.nuget.org/packages/Microsoft.ProjectOxford.SpeechRecognition-x64/) per un piattaforma a 64 bit. Per informazioni di riferimento sull'API della libreria client, vedere la [libreria desktop C# del Servizio di riconoscimento vocale Microsoft](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-Windows/master/docs/SpeechSDK/index.html).

Le sezioni seguenti descrivono come installare, compilare ed eseguire l'applicazione di esempio in C# tramite la libreria desktop C#.

## <a name="prerequisites"></a>Prerequisiti

### <a name="platform-requirements"></a>Requisiti di piattaforma

L'esempio seguente è stato sviluppato per Windows 8 e versioni successive e .NET Framework 4.5 e versioni successive usando [Visual Studio 2015, Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs).

### <a name="get-the-sample-application"></a>Ottenere l'applicazione di esempio

Clonare l'esempio dal repository di esempio della [libreria desktop C# per il riconoscimento vocale](https://github.com/microsoft/cognitive-speech-stt-windows).

### <a name="subscribe-to-the-speech-recognition-api-and-get-a-free-trial-subscription-key"></a>Eseguire la sottoscrizione all'API Riconoscimento vocale e ottenere una chiave di sottoscrizione della versione di valutazione gratuita

Speech API fa parte di Servizi cognitivi (noto in precedenza come Project Oxford). È possibile ottenere le chiavi di sottoscrizione della versione di valutazione gratuita dalla pagina di [sottoscrizione di Servizi cognitivi](https://azure.microsoft.com/try/cognitive-services/). Dopo aver selezionato Speech API, fare clic su **Ottieni la chiave API** per ottenere la chiave. Vengono restituite una chiave primaria e una chiave secondaria. Entrambe le chiavi sono legate alla stessa quota ed è quindi possibile usare indifferentemente una delle due.

> [!IMPORTANT]
> * Ottenere una chiave di sottoscrizione. Prima di usare le librerie client per il riconoscimento vocale, è necessario avere una [chiave di sottoscrizione](https://azure.microsoft.com/try/cognitive-services/).
>
> * Usare la chiave di sottoscrizione. Con l'applicazione di esempio della libreria desktop C# fornita, è necessario incollare la chiave di sottoscrizione nella casella di testo quando si esegue l'esempio. Per altre informazioni, vedere [Eseguire l'applicazione di esempio](#step-3-run-the-sample-application).

## <a name="step-1-install-the-sample-application"></a>Passaggio 1: Installare l'applicazione di esempio

1. Avviare Visual Studio 2015 e selezionare **File** > **Apri** > **Progetto/Soluzione**.

2. Passare alla cartella in cui sono stati salvati i file dell'API Riconoscimento vocale scaricati. Selezionare **Riconoscimento vocale** > **Windows** e quindi la cartella Sample-WP.

3. Fare doppio clic per aprire il file di soluzione di Visual Studio 2015 (con estensione sln) denominato SpeechToText-WPF-Samples.sln. La soluzione viene visualizzata in Visual Studio.

## <a name="step-2-build-the-sample-application"></a>Passaggio 2: Compilare l'applicazione di esempio

1. Per usare il *riconoscimento con finalità*, è innanzitutto necessario eseguire l'iscrizione al servizio [LUIS (Language Understanding Intelligent Service)](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/). Usare quindi l'URL dell'endpoint dell'app LUIS per impostare il valore della chiave `LuisEndpointUrl` nel file app.config nella cartella samples/SpeechRecognitionServiceExample. Per altre informazioni sull'URL dell'endpoint dell'app LUIS, vedere [Publish your app](../../luis/luis-get-started-create-app.md#publish-your-app) (Pubblicare l'app).

   > [!TIP]
   > Sostituire il carattere `&` nell'URL dell'endpoint LUIS con `&amp;` per garantire che l'URL venga interpretato correttamente dal parser XML.

2. Premere CTRL+MAIUSC+B oppure fare clic su **Compila** nel menu della barra multifunzione. Selezionare quindi **Compila soluzione**.

## <a name="step-3-run-the-sample-application"></a>Passaggio 3: Eseguire l'applicazione di esempio

1. Al termine della compilazione, premere F5 o fare clic su **Avvia** nel menu della barra multifunzione per eseguire l'esempio.

2. Passare alla finestra **Project Oxford Speech to Text Sample** (Esempio di riconoscimento vocale di Project Oxford). Incollare la chiave di sottoscrizione nella casella di testo **Paste your subscription key here to start** (Incollare qui la chiave di sottoscrizione per iniziare), come illustrato. Per rendere persistente la chiave di sottoscrizione nel PC o nel portatile, selezionare **Save Key** (Salva chiave). Per eliminare la chiave di sottoscrizione dal sistema, fare clic su **Delete Key** (Elimina chiave) per rimuoverla dal PC o dal portatile.

   ![Chiave incollata del Servizio di riconoscimento vocale](../Images/SpeechRecog_paste_key.PNG)

3. In **Speech Recognition Source** (Origine riconoscimento vocale) scegliere una delle sei origini di riconoscimento vocale, appartenenti a due categorie di input principali:

   * Usare il microfono del computer o uno collegato per registrare l'input vocale.
   * Riprodurre un file audio.

   Ogni categoria ha tre modalità di riconoscimento:

    * **Modalità ShortPhrase**: un'espressione fino a 15 secondi di durata. Quando i dati vengono inviati al server, il client riceve più risultati parziali e un risultato finale con N scelte migliori.
    * **Modalità LongDictation**: un'espressione fino a 2 minuti di durata. Quando i dati vengono inviati al server, il client riceve più risultati parziali e più risultati finali, in base alle posizioni di pausa della frase indicate dal server.
    * **Rilevamento della finalità**: il server restituisce informazioni aggiuntive strutturate sull'input vocale. Per usare il rilevamento della finalità, è innanzitutto necessario istruire un modello tramite il servizio [LUIS](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/).

Usare file audio di esempio con questa applicazione di esempio. Trovare i file nel repository scaricato con questo esempio nella cartella samples/SpeechRecognitionServiceExample. Questi file audio di esempio vengono eseguiti automaticamente se non si scelgono altri file quando si seleziona **Use wav file for Shortphrase mode** (Usa file WAV per la modalità ShortPhrase) oppure **Use wav file for Longdictation mode** (Usa file WAV per la modalità LongDictation) come input vocale. Attualmente, è supportato solo il formato audio WAV.

![Interfaccia per il riconoscimento vocale](../Images/HelloJones.PNG)

## <a name="samples-explained"></a>Descrizione degli esempi

### <a name="recognition-events"></a>Eventi di riconoscimento

* **Eventi con risultati parziali**: Questi eventi vengono chiamati ogni volta che il Servizio di riconoscimento vocale formula un'ipotesi su ciò che l'utente potrebbe dire, ancora prima che finisca di parlare (se si usa `MicrophoneRecognitionClient`) o completi l'invio dei dati (se si usa `DataRecognitionClient`).
* **Eventi con errore**: chiamati quando il servizio rileva un errore.
* **Eventi con finalità**: chiamati nei client "WithIntent" (solo nella modalità ShortPhrase) dopo che il risultato finale di riconoscimento viene analizzato come finalità JSON strutturata.
* **Eventi con risultato**:
  * Nella modalità `ShortPhrase` questo evento viene chiamato e restituisce N risultati migliori quando l'utente finisce di parlare.
  * Nella modalità `LongDictation` il gestore di eventi viene chiamato più volte, in base alle posizioni di pausa della frase identificate dal servizio.
  * **Per ognuna delle N scelte migliori**, vengono restituiti un valore di attendibilità e diverse forme del testo riconosciuto. Per altre informazioni, vedere [Formato di output](../Concepts.md#output-format).

I gestori di eventi sono già evidenziati nel codice sotto forma di commenti al codice.

## <a name="related-topics"></a>Argomenti correlati

* [Microsoft Speech desktop library reference](https://cdn.rawgit.com/Microsoft/Cognitive-Speech-STT-Windows/master/docs/SpeechSDK/index.html) (Informazioni di riferimento sulla libreria desktop del Servizio di riconoscimento vocale Microsoft)
* [Introduzione all'API Riconoscimento vocale Microsoft in Java per dispositivi Android](GetStartedJavaAndroid.md)
* [Introduzione all'API Riconoscimento vocale Microsoft in Objective-C per dispositivi iOS](Get-Started-ObjectiveC-iOS.md)
* [Introduzione all'API Riconoscimento vocale Microsoft in JavaScript](GetStartedJSWebsockets.md)
* [Introduzione all'API Riconoscimento vocale Microsoft tramite REST](GetStartedREST.md)
