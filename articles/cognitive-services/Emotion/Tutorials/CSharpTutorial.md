---
title: Esercitazione in C# sull'API Emozioni | Microsoft Docs
description: In questa esercitazione si esplora un'applicazione di base per Windows che usa l'API Emozioni di Servizi cognitivi per riconoscere le emozioni espresse dai visi in un'immagine.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 01/23/2017
ms.author: anroth
ms.openlocfilehash: f015e5720f65d0951a77de76ce8882a6dcdc1c3b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35374177"
---
# <a name="emotion-api-in-c35-tutorial"></a>Esercitazione in C&#35 sull'API Emozioni

> [!IMPORTANT]
> L'anteprima dell'API Video terminerà il 30 ottobre 2017. La nuova [anteprima API Video Indexer](https://azure.microsoft.com/services/cognitive-services/video-indexer/) consente di estrarre informazioni dettagliate dai video e ottimizzare le esperienze di individuazione del contenuto, ad esempio risultati della ricerca, tramite l'individuazione di parole, visi, caratteri ed emozioni. [Altre informazioni](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview).

In questa esercitazione si esplora un'applicazione di base per Windows che usa l'API Emozioni per riconoscere le emozioni espresse dai visi in un'immagine. L'esempio seguente consente di inviare un URL di immagine o un file archiviato localmente. È possibile usare questo esempio open source come modello per creare una propria applicazione per Windows che usa l'API Emozioni e WPF (Windows Presentation Foundation), nell'ambito di .NET Framework.

## <a name="Prerequisites">Prerequisiti</a>
#### <a name="platform-requirements"></a>Requisiti di piattaforma  
L'esempio seguente è stato sviluppato per .NET Framework usando [Visual Studio 2015, Community Edition](https://www.visualstudio.com/products/visual-studio-community-vs).  

#### <a name="subscribe-to-emotion-api-and-get-a-subscription-key"></a>Sottoscrivere l'API Emozioni e ottenere una chiave di sottoscrizione  
Prima di creare l'esempio è necessario sottoscrivere l'API Emozioni, che fa parte di Servizi cognitivi Microsoft. Vedere [Sottoscrizioni](https://azure.microsoft.com/try/cognitive-services/). In questa esercitazione è possibile usare sia la chiave primaria sia quella secondaria. Seguire le procedura consigliata per garantire la segretezza e la sicurezza della chiave API.  

#### <a name="get-the-client-library-and-example"></a>Scaricare la libreria client e l'esempio  
È possibile scaricare la libreria client per l'API Emozioni tramite [SDK](https://www.github.com/microsoft/cognitive-emotion-windows). Il file ZIP scaricato deve essere estratto in una cartella a propria scelta. Molti utenti scelgono la cartella di Visual Studio 2015.
## <a name="Step1">Passaggio 1: Aprire l'esempio</a>
1.  Avviare Microsoft Visual Studio 2015, fare clic su **File**, **Apri**, quindi scegliere **Progetto/Soluzione**.
2.  Passare alla cartella in cui sono stati salvati i file dell'API Emozioni scaricati. Fare clic su **Emozioni**, su **Windows**, quindi sulla cartella **Sample-WPF**.
3.  Fare doppio clic per aprire il file di soluzione di Visual Studio 2015 (con estensione SLN) denominato **EmotionAPI-WPF-Samples.sln**. La soluzione verrà aperta in Visual Studio.

## <a name="Step2">Passaggio 2: Compilare l'esempio</a>
1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **Riferimenti** e scegliere **Gestisci pacchetti NuGet**.

  ![Aprire Gestione pacchetti NuGet](../Images/EmotionNuget.png)

2.  Si apre la finestra **Gestione pacchetti NuGet**. Selezionare **Sfoglia** nell'angolo superiore sinistro, quindi nella casella di ricerca digitare "Newtonsoft. JSON", selezionare il pacchetto **Newtonsoft.Json** e fare clic su **Installa**.  

  ![Passare al pacchetto NuGet](../Images/EmotionNugetBrowse.png)  

3.  Premere CTRL+MAIUSC+B oppure fare clic su **Compila** nel menu della barra multifunzione e quindi selezionare **Compila soluzione**.

## <a name="Step3">Passaggio 3: Eseguire l'esempio</a>
1.  Al termine della compilazione, premere **F5** o fare clic su **Avvia** nel menu della barra multifunzione per eseguire l'esempio.
2.  Individuare la finestra dell'API Emozioni con la **casella di testo** "**Paste your subscription key here to start**" (Incollare qui la chiave di sottoscrizione per iniziare). Incollare la chiave di sottoscrizione nella casella di testo, come mostrato nello screenshot seguente. È possibile scegliere di conservare la chiave di sottoscrizione nel PC o laptop facendo clic sul pulsante "Salva chiave". Quando si desidera eliminare la chiave di sottoscrizione dal sistema, fare clic su "Elimina chiave" per rimuoverla dal PC o laptop.
  
  ![Interfaccia della funzionalità Emozioni](../Images/EmotionKey.png)

3.  In "**Selezionare uno scenario**" fare clic per usare uno dei due scenari disponibili, "**Detect emotion using a stream**" (Rileva emozione usando un flusso) o "**Detect emotion using a URL**" (Rileva emozione usando un URL) e quindi seguire le istruzioni visualizzate. Microsoft riceve le immagini che vengono caricate e può usarle per migliorare l'API Emozioni e i servizi correlati. Quando si invia un'immagine, si conferma di avere rispettato il [Codice di comportamento per gli sviluppatori](https://azure.microsoft.com/support/legal/developer-code-of-conduct/).
4.  Vi sono immagini di esempio da usare con questa applicazione di esempio. Queste immagini sono disponibili nel [repository GitHub dell'API Viso](https://github.com/Microsoft/Cognitive-Face-Windows/tree/master/Data), nella cartella **Data**. Si noti che l'uso di queste immagini è concesso in licenza in base ai principi del Fair Use, vale a dire che è possibile usarle per testare questo esempio, ma non per la ripubblicazione.

## <a name="Review">Esame e apprendimento</a>
Con l'applicazione in esecuzione, è ora possibile esaminare in che modo l'app di esempio si integra con Servizi cognitivi Microsoft. In questo modo diventerà più facile continuare a sviluppare questa applicazione o sviluppare un'app propria usando l'API Emozioni di Microsoft. 

Questa app di esempio usa la libreria client dell'API Emozioni, un wrapper thin client C# per l'API Emozioni di Microsoft. Quando è stata creata l'app di esempio nella procedura precedente, la libreria client è stata ottenuta da un pacchetto NuGet. È possibile esaminare il codice sorgente della libreria client nella cartella denominata "[Client Library](https://github.com/Microsoft/Cognitive-Emotion-Windows/tree/master/ClientLibrary)" in **Emozioni**, **Windows**, **Client Library**, che fa parte del repository di file scaricati indicato nei [Prerequisiti](#Prerequisites).
 
Si può anche scoprire come usare il codice della libreria client in **Esplora soluzioni**: in **EmotionAPI-WPF_Samples** espandere **DetectEmotionUsingStreamPage.xaml** per individuare **DetectEmotionUsingStreamPage.xaml.cs**, che viene usato per passare a un file archiviato in locale, oppure espandere **DetectEmotionUsingURLPage.xaml** per trovare **DetectEmotionUsingURLPage.xaml.cs**, che viene usato quando si carica l'URL di un'immagine. Fare doppio clic sui file con estensione xaml.cs per aprirli in nuove finestre di Visual Studio. 

Per vedere in che modo la libreria client di Emozioni viene usata nell'app di esempio, verranno ora esaminati due frammenti di codice da **DetectEmotionUsingStreamPage.xaml.cs** e **DetectEmotionUsingURLPage.xaml.cs**. Ogni file contiene commenti di codice che indicano "KEY SAMPLE CODE STARTS HERE" e "KEY SAMPLE CODE ENDS HERE", che aiutano a individuare i frammenti di codice riportati di seguito.

L'API Emozioni è in grado di usare l'URL di un'immagine o dati immagine binari (in forma di flusso di ottetti) come input. Le due opzioni vengono esaminate di seguito. In entrambi i casi si trova prima di tutto una direttiva using, che consente di usare la libreria client di Emozioni. 
```csharp

            // ----------------------------------------------------------------------- 
            // KEY SAMPLE CODE STARTS HERE 
            // Use the following namespace for EmotionServiceClient 
            // ----------------------------------------------------------------------- 
            using Microsoft.ProjectOxford.Emotion; 
            using Microsoft.ProjectOxford.Emotion.Contract; 
            // ----------------------------------------------------------------------- 
            // KEY SAMPLE CODE ENDS HERE 
            // ----------------------------------------------------------------------- 
```
#### <a name="detectemotionusingurlpagexamlcs"></a>DetectEmotionUsingURLPage.xaml.cs 

Questo frammento di codice mostra come usare la libreria client per inviare la chiave di sottoscrizione e un URL di fotografia al servizio API Emozioni. 

```csharp

            // -----------------------------------------------------------------------
            // KEY SAMPLE CODE STARTS HERE
            // -----------------------------------------------------------------------

            window.Log("EmotionServiceClient is created");

            //
            // Create Project Oxford Emotion API Service client
            //
            EmotionServiceClient emotionServiceClient = new EmotionServiceClient(subscriptionKey);

            window.Log("Calling EmotionServiceClient.RecognizeAsync()...");
            try
            {
                //
                // Detect the emotions in the URL
                //
                Emotion[] emotionResult = await emotionServiceClient.RecognizeAsync(url);
                return emotionResult;
            }
            catch (Exception exception)
            {
                window.Log("Detection failed. Please make sure that you have the right subscription key and proper URL to detect.");
                window.Log(exception.ToString());
                return null;
            }
            // -----------------------------------------------------------------------
            // KEY SAMPLE CODE ENDS HERE
            // -----------------------------------------------------------------------
```
#### <a name="detectemotionusingstreampagexamlcs"></a>DetectEmotionUsingStreamPage.xaml.cs 

Il frammento di codice seguente mostra come inviare la chiave di sottoscrizione e un'immagine archiviata in locale all'API Emozioni. 


```csharp


            // -----------------------------------------------------------------------
            // KEY SAMPLE CODE STARTS HERE
            // -----------------------------------------------------------------------

            //
            // Create Project Oxford Emotion API Service client
            //
            EmotionServiceClient emotionServiceClient = new EmotionServiceClient(subscriptionKey);

            window.Log("Calling EmotionServiceClient.RecognizeAsync()...");
            try
            {
                Emotion[] emotionResult;
                using (Stream imageFileStream = File.OpenRead(imageFilePath))
                {
                    //
                    // Detect the emotions in the URL
                    //
                    emotionResult = await emotionServiceClient.RecognizeAsync(imageFileStream);
                    return emotionResult;
                }
            }
            catch (Exception exception)
            {
                window.Log(exception.ToString());
                return null;
            }
            // -----------------------------------------------------------------------
            // KEY SAMPLE CODE ENDS HERE
            // -----------------------------------------------------------------------
```
<!--
## <a name="Related">Related Topics</a>
[Emotion API Overview](.)
-->
