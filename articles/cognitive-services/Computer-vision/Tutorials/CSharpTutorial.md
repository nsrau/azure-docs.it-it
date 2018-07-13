---
title: Esercitazione in C# dell'API Visione artificiale | Microsoft Docs
description: In questa esercitazione si esplora un'applicazione di base per Windows che usa l'API Visione artificiale in Servizi cognitivi Microsoft. Si effettua il riconoscimento ottico dei caratteri (OCR), si creano anteprime e si usano le funzionalità visive in un'immagine.
services: cognitive-services
author: KellyDF
manager: corncar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: article
ms.date: 05/22/2017
ms.author: kefre
ms.openlocfilehash: f2aeb1734f8858ed8b80e5cdf48ef7e558703919
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35374753"
---
# <a name="computer-vision-api-c35-tutorial"></a>Esercitazione dell'API Visione artificiale C&#35;

Si esplora un'applicazione di base per Windows che usa l'API Visione artificiale per effettuare il riconoscimento ottico dei caratteri (OCR), creare immagini di anteprima perfettamente ritagliate, nonché per rilevare, suddividere in categorie, aggiungere tag e descrivere le caratteristiche visive, inclusi i visi, in un'immagine. L'esempio seguente consente di inviare un URL di immagine o un file memorizzato localmente. È possibile usare questo esempio open source come modello per creare una propria applicazione per Windows che usa l'API Visione e WPF (Windows Presentation Foundation), nell'ambito di .NET Framework.

### <a name="prerequisites"></a>prerequisiti

#### <a name="platform-requirements"></a>Requisiti di piattaforma

L'esempio seguente è stato sviluppato per .NET Framework usando [Visual Studio 2015, Community Edition](https://www.visualstudio.com/downloads/).

#### <a name="subscribe-to-computer-vision-api-and-get-a-subscription-key"></a>Sottoscrivere l'API Visione artificiale e ottenere una chiave di sottoscrizione 

Prima di creare l'esempio, è necessario sottoscrivere l'API Visione artificiale che fa parte di Servizi cognitivi Microsoft (precedentemente denominati Progetto Oxford). Per i dettagli sulla sottoscrizione e sulla chiave di sottoscrizione, vedere [Sottoscrizioni](https://azure.microsoft.com/try/cognitive-services/). In questa esercitazione è possibile usare entrambe le chiavi primaria e secondaria. 

> [!NOTE]
> L'esercitazione è progettata per l'uso delle chiavi di sottoscrizione nell'area **westcentralus**. Le chiavi di sottoscrizione nella versione di valutazione gratuita di Visione artificiale usano l'area **westcentralus**, pertanto funzionano correttamente. Se le chiavi di sottoscrizione sono state generate usando l'account di Azure tramite [https://azure.microsoft.com/](https://azure.microsoft.com/), è necessario specificare l'area **westcentralus**. Le chiavi generate all'esterno dell'area **westcentralus** non funzioneranno.

#### <a name="get-the-client-library-and-example"></a>Scaricare la libreria client e l'esempio

È possibile clonare la libreria client e l'applicazione di esempio API Visione artificiale nel computer tramite l'[SDK](https://www.github.com/microsoft/cognitive-vision-windows). Non scaricarle come file ZIP.

### <a name="Step1">Passaggio 1: Installare l'esempio</a>

Nel Desktop di GitHub, aprire Sample-WPF\VisionAPI-WPF-Samples.sln.

### <a name="Step2">Passaggio 2: Generare l'esempio</a>

* Premere CTRL+MAIUSC+B oppure fare clic su Genera nel menu della barra multifunzione, quindi selezionare Compila soluzione.

### <a name="Step3">Passaggio 3: Eseguire l'esempio</a>

1. Al termine della compilazione, premere **F5** o fare clic su **Avvia** nel menu della barra multifunzione per eseguire l'esempio.
2. Individuare la finestra dell'interfaccia utente dell'API Visione artificiale con la casella di modifica testo "Paste your subscription key here to start" (Incollare qui la chiave di sottoscrizione per iniziare).
È possibile scegliere di conservare la chiave di sottoscrizione nel PC o laptop facendo clic sul pulsante "Salva chiave". Quando si vuole eliminare la chiave di sottoscrizione dal sistema, fare clic su "Elimina chiave" per rimuoverla dal PC o laptop.

    ![Chiave di sottoscrizione di Visione](../Images/Vision_UI_Subscription.PNG)

3. In "Selezionare uno scenario" fare clic per usare uno dei sei scenari disponibili e quindi seguire le istruzioni visualizzate. Microsoft riceve le immagini che vengono caricate e può usarle per migliorare l'API Visione artificiale e i servizi correlati. Quando si invia un'immagine, si conferma di avere rispettato il [Codice di comportamento per gli sviluppatori](https://azure.microsoft.com/support/legal/developer-code-of-conduct/).

    ![Interfaccia per l'analisi delle immagini](../Images/Analyze_Image_Example.PNG)

4. Vi sono immagini di esempio da usare con questa applicazione di esempio. Queste immagini sono disponibili nel repository di GitHub di Windows dell'API Viso, nella cartella [Data](https://github.com/Microsoft/Cognitive-Face-Windows/tree/master/Data). L'utilizzo di queste immagini viene concesso in licenza in base al contratto [LICENSE-IMAGE](https://github.com/Microsoft/Cognitive-Face-Windows/blob/master/LICENSE-IMAGE.md).

### <a name="Review">Esame e apprendimento</a>

Con l'applicazione in esecuzione, è ora possibile esaminare in che modo l'applicazione di esempio si integra con la tecnologia di Servizi cognitivi. In questo modo diventerà più facile continuare a creare in base a questa applicazione o sviluppare un'applicazione propria usando l'API Visione artificiale di Microsoft.

Questa applicazione di esempio usa la libreria client dell'API Visione artificiale, un wrapper thin client C# per l'API Visione artificiale di Microsoft. Quando è stata creata l'applicazione di esempio nella procedura precedente, la libreria client è stata ottenuta da un pacchetto NuGet. È possibile esaminare il codice sorgente della libreria client nella cartella denominata "**Client Library**" in **Visione**, **Windows**, **Libreria client**, che fa parte del repository di file scaricati indicato nei Prerequisiti.

È inoltre possibile scoprire come usare il codice della libreria client in Esplora soluzioni: in **VisionAPI-WPF_Samples**, espandere **AnalyzePage.xaml** per individuare **AnalyzePage.xaml.cs**, che viene usato per inviare un'immagine all'endpoint di analisi dell'immagine. Fare doppio clic sui file con estensione xaml.cs per aprirli in nuove finestre di Visual Studio.

Per esaminare in che modo la libreria client di Visione viene usate nell'applicazione di esempio, si osservino i due frammenti di codice del file **AnalyzePage.xaml.cs**. Il file contiene commenti al codice che indicano "KEY SAMPLE CODE STARTS HERE" e "KEY SAMPLE CODE ENDS HERE" che aiutano a individuare i frammenti di codice riportati di seguito.

L'endpoint di analisi è in grado di usare l'URL di un'immagine o i dati immagine binari (in forma di flusso di ottetti) come input. Si trova prima di tutto una direttiva using, che consente di usare la libreria client di Visione.

```
                // ----------------------------------------------------------------------
                // KEY SAMPLE CODE STARTS HERE
                // Use the following namespace for VisionServiceClient 
                // ---------------------------------------------------------------------- 
                using Microsoft.ProjectOxford.Vision; 
                using Microsoft.ProjectOxford.Vision.Contract; 
                // ----------------------------------------------------------------------
                // KEY SAMPLE CODE ENDS HERE 
                // ----------------------------------------------------------------------

```
**UploadAndAnalyzeImage(…)** Questo frammento di codice mostra come usare la libreria client per inviare la chiave di sottoscrizione e un'immagine memorizzata in locale all'endpoint di analisi del servizio API Visione artificiale.

```
    private async Task<AnalysisResult> UploadAndAnalyzeImage(string imageFilePath)
    {
        // -----------------------------------------------------------------------
        // KEY SAMPLE CODE STARTS HERE
        // -----------------------------------------------------------------------  
        //
        // Create Project Oxford Computer Vision API Service client
        //
        VisionServiceClient VisionServiceClient = new VisionServiceClient(SubscriptionKey);
        Log("VisionServiceClient is created");
    
        using (Stream imageFileStream = File.OpenRead(imageFilePath))
        {
            //
            // Analyze the image for all visual features
            //
            Log("Calling VisionServiceClient.AnalyzeImageAsync()...");
         VisualFeature[] visualFeatures = new VisualFeature[] { VisualFeature.Adult, VisualFeature.Categories, VisualFeature.Color, VisualFeature.Description, VisualFeature.Faces, VisualFeature.ImageType, VisualFeature.Tags };
            AnalysisResult analysisResult = await VisionServiceClient.AnalyzeImageAsync(imageFileStream, visualFeatures);
            return analysisResult;
        }
    
        // -----------------------------------------------------------------------
        // KEY SAMPLE CODE ENDS HERE
        // -----------------------------------------------------------------------
        }
```
**AnalyzeUrl(…)** Questo frammento di codice mostra come usare la libreria client per inviare la chiave di sottoscrizione e un URL di fotografia all'endpoint di analisi del servizio API Visione artificiale.

```
    private async Task<AnalysisResult> AnalyzeUrl(string imageUrl)
    {
        // -----------------------------------------------------------------------
        // KEY SAMPLE CODE STARTS HERE
        // -----------------------------------------------------------------------
    
        //
        // Create Project Oxford Computer Vision API Service client
        //
     VisionServiceClient VisionServiceClient = new VisionServiceClient(SubscriptionKey);
        Log("VisionServiceClient is created");
    
        //
        // Analyze the url for all visual features
        //
        Log("Calling VisionServiceClient.AnalyzeImageAsync()...");
        VisualFeature[] visualFeatures = new VisualFeature[] { VisualFeature.Adult, VisualFeature.Categories, VisualFeature.Color, VisualFeature.Description, VisualFeature.Faces, VisualFeature.ImageType, VisualFeature.Tags };
        AnalysisResult analysisResult = await VisionServiceClient.AnalyzeImageAsync(imageUrl, visualFeatures);
     return analysisResult;
    }
        // -----------------------------------------------------------------------
        // KEY SAMPLE CODE ENDS HERE
        // -----------------------------------------------------------------------
```
**Altre pagine e altri endpoint** È possibile capire come interagire con gli altri endpoint esposti dal servizio API Visione artificiale osservando le altre pagine dell'esempio. L'endpoint OCR, ad esempio, viene visualizzato come parte del codice contenuto in OCRPage.xaml.cs 

### <a name="Related">Argomenti correlati</a>
 * [Introduzione all'API Viso](../../Face/Tutorials/FaceAPIinCSharpTutorial.md)
 
 


