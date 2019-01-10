---
title: "Esercitazione: Creare un'app di elaborazione delle immagini - C#"
titleSuffix: Computer Vision - Cognitive Services - Azure
description: In questa esercitazione si esplora un'applicazione di base per Windows che usa l'API Visione artificiale in Servizi cognitivi Microsoft. Si effettua il riconoscimento ottico dei caratteri (OCR), si creano anteprime e si usano le caratteristiche visive in un'immagine.
services: cognitive-services
author: PatrickFarley
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: article
ms.date: 08/28/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 8640d89c5fb9e38fc6c982e21147361ad690964a
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/18/2018
ms.locfileid: "53584446"
---
# <a name="tutorial-build-an-image-processing-app---c35"></a>Esercitazione: Creare un'app di elaborazione delle immagini - C&#35;

Esaminare un'applicazione Windows di base che usa Visione artificiale per eseguire il riconoscimento ottico dei caratteri (OCR), creare anteprime ritagliate in modo intelligente, nonché rilevare, classificare, contrassegnare con tag e descrivere le caratteristiche visive, inclusi i visi, di un'immagine. L'esempio seguente consente di inviare un URL di immagine o un file archiviato localmente. È possibile usare questo esempio open source come modello per creare un'app per Windows che usa l'API Visione artificiale e WPF (Windows Presentation Foundation), nell'ambito di .NET Framework.

> [!div class="checklist"]
> * Ottenere l'app di esempio da GitHub
> * Aprire e compilare l'app di esempio in Visual Studio
> * Eseguire l'app di esempio e interagire con essa per vari scenari
> * Esplorare i diversi scenari inclusi nell'app di esempio

## <a name="prerequisites"></a>Prerequisiti

Prima di esplorare l'app di esempio, verificare che i prerequisiti seguenti siano soddisfatti:

* È necessario disporre di [Visual Studio 2015](https://visualstudio.microsoft.com/downloads/) o versioni successive.
* È necessario avere una chiave di sottoscrizione per Visione artificiale. Per ottenere una chiave di sottoscrizione, vedere [Come ottenere chiavi di sottoscrizione](../Vision-API-How-to-Topics/HowToSubscribe.md).

## <a name="get-the-sample-app"></a>Ottenere l'app di esempio

L'app di esempio di Visione artificiale è disponibile nel repository GitHub `Microsoft/Cognitive-Vision-Windows`. Questo repository include anche il repository `Microsoft/Cognitive-Common-Windows` come modulo secondario di Git. È possibile clonare il repository in modo ricorsivo, incluso il modulo secondario, usando il comando `git clone --recurse-submodules` dalla riga di comando o GitHub Desktop.

Ad esempio, per clonare in modo ricorsivo il repository per l'app di esempio di Visione artificiale da un prompt dei comandi, eseguire questo comando:

```Console
git clone --recurse-submodules https://github.com/Microsoft/Cognitive-Vision-Windows.git
```

> [!IMPORTANT]
> Non scaricare il repository come file con estensione zip. Git non include i moduli secondari durante il download di un repository come file con estensione zip.

### <a name="get-optional-sample-images"></a>Ottenere immagini di esempio facoltative

È possibile usare le immagini di esempio incluse nell'app di esempio [Viso](../../Face/Overview.md), disponibile nel repository GitHub `Microsoft/Cognitive-Face-Windows`. Questa app di esempio include una cartella, `/Data`, che contiene varie immagini di persone. È possibile clonare in modo ricorsivo anche questo repository tramite i metodi descritti per l'app di esempio di Visione artificiale.

Ad esempio, per clonare in modo ricorsivo il repository per l'app di esempio Viso da un prompt dei comandi, eseguire questo comando:

```Console
git clone --recurse-submodules https://github.com/Microsoft/Cognitive-Face-Windows.git
```

## <a name="open-and-build-the-sample-app-in-visual-studio"></a>Aprire e compilare l'app di esempio in Visual Studio

Prima di poter eseguire o esplorare l'app di esempio, è necessario compilarla in modo che Visual Studio possa risolvere le dipendenze. Per aprire e compilare l'app di esempio, seguire questa procedura:

1. Aprire il file della soluzione di Visual Studio, `/Sample-WPF/VisionAPI-WPF-Samples.sln`, in Visual Studio.
1. Verificare che la soluzione di Visual Studio contenga due progetti:  

   * SampleUserControlLibrary
   * VisionAPI-WPF-Samples  

   Se il progetto SampleUserControlLibrary non è disponibile, verificare di aver clonato in modo ricorsivo il repository `Microsoft/Cognitive-Vision-Windows`.
1. In Visual Studio premere CTRL+MAIUSC+B o scegliere **Compila** dal menu della barra multifunzione e quindi scegliere **Compila soluzione**.

## <a name="run-and-interact-with-the-sample-app"></a>Eseguire l'app di esempio e interagire con essa

È possibile eseguire l'app di esempio per verificarne la modalità di interazione con l'utente e con la libreria client di Visione artificiale durante l'esecuzione di varie attività, ad esempio la generazione di anteprime o l'assegnazione di tag a immagini. Per eseguire l'app di esempio e interagire con essa, seguire questa procedura:

1. Al termine della compilazione, premere **F5** oppure scegliere **Debug** dal menu della barra multifunzione e quindi scegliere **Avvia debug** per eseguire l'app di esempio.
1. Quando viene visualizzata l'app di esempio, scegliere **Subscription Key Management** (Gestione chiavi di sottoscrizione) dal riquadro di spostamento per visualizzare la pagina di gestione delle chiavi di sottoscrizione.
   ![Pagina di gestione delle chiavi di sottoscrizione](../Images/Vision_UI_Subscription.PNG)  
1. Immettere la chiave di sottoscrizione in **Subscription Key** (Chiave di sottoscrizione).
1. Immettere l'URL dell'endpoint, omettendo `/vision/v1.0`, della risorsa Visione artificiale per la chiave di sottoscrizione in **Endpoint**.  
   Se ad esempio si usa la chiave di sottoscrizione della versione di valutazione gratuita di Visione artificiale, immettere l'URL dell'endpoint seguente per l'area di Azure Stati Uniti centro-occidentali: `https://westcentralus.api.cognitive.microsoft.com`
1. Per evitare di immettere nuovamente la chiave di sottoscrizione e l'URL dell'endpoint alla successiva esecuzione dell'app di esempio, scegliere **Save Setting** (Salva impostazione) per salvare la chiave di sottoscrizione e l'URL dell'endpoint nel computer. Se si vuole eliminare la chiave di sottoscrizione e l'URL dell'endpoint salvati in precedenza, scegliere **Delete Setting** (Elimina impostazione).

   > [!NOTE]
   > L'app di esempio usa lo spazio di memorizzazione isolato, e `System.IO.IsolatedStorage`, per archiviare la chiave di sottoscrizione e l'URL dell'endpoint.

1. In **Select a scenario** (Seleziona uno scenario) nel riquadro di spostamento selezionare uno degli scenari attualmente inclusi nell'app di esempio:  

   | Scenario | DESCRIZIONE |
   |----------|-------------|
   |Analyze Image (Analisi dell'immagine) | Usa l'operazione [Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) (Analizza immagine) per analizzare un'immagine locale o remota. È possibile scegliere le caratteristiche visive e la lingua per l'analisi e visualizzare sia l'immagine che i risultati.  |
   |Analyze Image with Domain Model (Analisi dell'immagine con modello di dominio) | Usa l'operazione [List Domain Specific Models](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fd) (Elenca modelli di dominio specifici) per elencare i modelli di dominio che è possibile selezionare e l'operazione [Recognize Domain Specific Content](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e200) (Riconosci contenuti specifici del dominio) per analizzare un'immagine locale o remota usando il modello di dominio selezionato. È anche possibile scegliere la lingua per l'analisi. |
   |Describe Image (Descrizione dell'immagine) | Usa l'operazione [Describe Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fe) (Descrivi immagine) per creare una descrizione leggibile dall'utente di un'immagine locale o remota. È anche possibile scegliere la lingua per la descrizione. |
   |Generate Tags (Generazione di tag) | Usa l'operazione [Tag Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1ff) (Assegna tag a un'immagine) per contrassegnare le caratteristiche visive di un'immagine locale o remota. È anche possibile scegliere la lingua usata per i tag. |
   |Recognize Text (OCR) (Riconoscimento del testo - OCR) | Usa l'operazione [OCR](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) per riconoscere ed estrarre testo stampato da un'immagine. È possibile scegliere la lingua da usare o far rilevare automaticamente la lingua a Visione artificiale. |
   |Recognize Text V2 (English) (Riconoscimento del testo V2 - inglese) | Usa le operazioni [Recognize Text](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) (Riconosci testo) e [Get Recognize Text Operation Result](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2cf1154055056008f201) (Ottieni risultato dell'operazione Riconosci testo) per riconoscere ed estrarre in modo asincrono il testo stampato o scritto a mano da un'immagine. |
   |Get Thumbnail (Generazione dell'anteprima) | Usa l'operazione [Get Thumbnail](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) (Ottieni anteprima) per generare un'anteprima per un'immagine locale o remota. |

   > [!IMPORTANT]
   > Microsoft riceve le immagini che vengono caricate e può usarle per migliorare l'API Visione artificiale e i servizi correlati. Quando si invia un'immagine, si conferma di avere rispettato il [Codice di comportamento per gli sviluppatori](https://azure.microsoft.com/support/legal/developer-code-of-conduct/).

   Lo screenshot seguente illustra la pagina visualizzata per lo scenario di analisi dell'immagine dopo l'analisi di un'immagine di esempio.
   ![Screenshot della pagina di analisi delle immagini](../Images/Analyze_Image_Example.PNG)

## <a name="explore-the-sample-app"></a>Esplorare l'app di esempio

La soluzione di Visual Studio per l'app di esempio di Visione artificiale contiene due progetti:

* SampleUserControlLibrary  
  Il progetto SampleUserControlLibrary fornisce funzionalità condivise da più esempi di Servizi cognitivi. Il progetto contiene gli elementi seguenti:
  * SampleScenarios  
    Un elemento UserControl che fornisce una presentazione standardizzata, ad esempio la barra del titolo, il riquadro di spostamento e il riquadro del contenuto, per gli esempi. L'app di esempio di Visione artificiale usa questo controllo nella finestra MainWindow.xaml per la visualizzazione delle pagine dello scenario e l'accesso alle informazioni condivise tra gli scenari, ad esempio la chiave di sottoscrizione e l'URL dell'endpoint.
  * SubscriptionKeyPage  
    Una pagina che fornisce un layout standardizzato per l'immissione di una chiave di sottoscrizione e di un URL dell'endpoint per l'app di esempio. L'app di esempio di Visione artificiale usa questa pagina per gestire la chiave di sottoscrizione e l'URL dell'endpoint usati dalle pagine dello scenario.
  * VideoResultControl  
    Un elemento UserControl che fornisce una presentazione standardizzata per le informazioni relative al video. L'app di esempio di Visione artificiale non usa questo controllo.
* VisionAPI-WPF-Samples  
  Il progetto principale per l'app di esempio di Visione artificiale, che contiene tutte le funzionalità di interesse per Visione artificiale. Il progetto contiene gli elementi seguenti:
  * AnalyzeInDomainPage.xaml  
    La pagina dello scenario di analisi dell'immagine con modello di dominio.
  * AnalyzeImage.xaml  
    La pagina dello scenario di analisi dell'immagine.
  * DescribePage.xaml  
    La pagina dello scenario di descrizione dell'immagine.
  * ImageScenarioPage.cs  
    La classe ImageScenarioPage da cui derivano tutte le pagine di scenario nell'app di esempio. Questa classe gestisce funzionalità, ad esempio l'impostazione delle credenziali e la formattazione dell'output, condivise da tutte le pagine di scenario.
  * MainWindow.xaml  
    La finestra principale per l'app di esempio, che usa il controllo SampleScenarios per presentare la pagina SubscriptionKeyPage e le pagine di scenario.
  * OCRPage.xaml  
    La pagina dello scenario di riconoscimento del testo (OCR).
  * RecognizeLanguage.cs  
    La classe RecognizeLanguage, che fornisce informazioni sulle lingue supportate dai vari metodi nell'app di esempio.
  * TagsPage.xaml  
    La pagina dello scenario di generazione di tag.
  * TextRecognitionPage.xaml  
    La pagina dello scenario di riconoscimento del testo V2 (inglese).
  * ThumbnailPage.xaml  
    La pagina dello scenario di generazione dell'anteprima.

### <a name="explore-the-sample-code"></a>Esplorare il codice di esempio

Le parti principali del codice di esempio sono racchiuse tra blocchi di commento che iniziano con `KEY SAMPLE CODE STARTS HERE` e terminano con `KEY SAMPLE CODE ENDS HERE`, per semplificare l'esplorazione dell'app di esempio. Queste parti principali del codice di esempio contengono il codice più rilevante per imparare a usare la libreria client dell'API Visione artificiale allo scopo di eseguire varie attività. È possibile cercare `KEY SAMPLE CODE STARTS HERE` in Visual Studio per spostarsi tra le sezioni più importanti del codice nell'app di esempio di Visione artificiale. 

Ad esempio, il metodo `UploadAndAnalyzeImageAsync`, illustrato di seguito e incluso in AnalyzePage.xaml, descrive come usare la libreria client per analizzare un'immagine locale richiamando il metodo `ComputerVisionClient.AnalyzeImageInStreamAsync`.

```csharp
private async Task<ImageAnalysis> UploadAndAnalyzeImageAsync(string imageFilePath)
{
    // -----------------------------------------------------------------------
    // KEY SAMPLE CODE STARTS HERE
    // -----------------------------------------------------------------------

    //
    // Create Cognitive Services Vision API Service client.
    //
    using (var client = new ComputerVisionClient(Credentials) { Endpoint = Endpoint })
    {
        Log("ComputerVisionClient is created");

        using (Stream imageFileStream = File.OpenRead(imageFilePath))
        {
            //
            // Analyze the image for all visual features.
            //
            Log("Calling ComputerVisionClient.AnalyzeImageInStreamAsync()...");
            VisualFeatureTypes[] visualFeatures = GetSelectedVisualFeatures();
            string language = (_language.SelectedItem as RecognizeLanguage).ShortCode;
            ImageAnalysis analysisResult = await client.AnalyzeImageInStreamAsync(imageFileStream, visualFeatures, null, language);
            return analysisResult;
        }
    }

    // -----------------------------------------------------------------------
    // KEY SAMPLE CODE ENDS HERE
    // -----------------------------------------------------------------------
}
```

### <a name="explore-the-client-library"></a>Esplorare la libreria client

Questa app di esempio usa la libreria client dell'API Visione artificiale, un wrapper thin client C# per l'API Visione artificiale in Servizi cognitivi di Azure. La libreria client è disponibile da NuGet nel pacchetto [Microsoft.Azure.CognitiveServices.Vision.ComputerVision](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/). Quando si è compilata l'applicazione in Visual Studio, si è recuperata la libreria client dal pacchetto NuGet corrispondente. È anche possibile visualizzare il codice sorgente per la libreria client nella cartella `/ClientLibrary` del repository `Microsoft/Cognitive-Vision-Windows`.

La funzionalità della libreria client si basa sulla classe `ComputerVisionClient`, nello spazio dei nomi `Microsoft.Azure.CognitiveServices.Vision.ComputerVision`, mentre i modelli usati dalla classe `ComputerVisionClient` durante l'interazione con Visione artificiale si trovano nello spazio dei nomi `Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models`. Nelle varie pagine di scenario XAML incluse nell'app di esempio sono presenti le direttive `using` seguenti per tali spazi dei nomi:

```csharp
// -----------------------------------------------------------------------
// KEY SAMPLE CODE STARTS HERE
// Use the following namespace for ComputerVisionClient.
// -----------------------------------------------------------------------
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;
// -----------------------------------------------------------------------
// KEY SAMPLE CODE ENDS HERE
// -----------------------------------------------------------------------
```

Esplorando gli scenari inclusi nell'app di esempio di Visione artificiale si otterranno altre informazioni sui vari metodi della classe `ComputerVisionClient`.

## <a name="explore-the-analyze-image-scenario"></a>Esplorare lo scenario di analisi dell'immagine

Questo scenario viene gestito dalla pagina AnalyzePage.xaml. È possibile scegliere le caratteristiche visive e la lingua per l'analisi e visualizzare sia l'immagine che i risultati. A tale scopo, la pagina dello scenario usa uno dei metodi seguenti, a seconda dell'origine dell'immagine:

* UploadAndAnalyzeImageAsync  
  Questo metodo viene usato per le immagini locali, in cui l'immagine deve essere codificata come `Stream` e inviata a Visione artificiale chiamando il metodo `ComputerVisionClient.AnalyzeImageInStreamAsync`.
* AnalyzeUrlAsync  
  Questo metodo viene usato per le immagini remote, in cui l'URL dell'immagine viene inviato a Visione artificiale chiamando il metodo `ComputerVisionClient.AnalyzeImageAsync`.

Il metodo `UploadAndAnalyzeImageAsync` crea una nuova istanza di `ComputerVisionClient`, usando la chiave di sottoscrizione e l'URL dell'endpoint specificati. Poiché l'app di esempio analizza un'immagine locale, deve inviare il contenuto di tale immagine a Visione artificiale. Apre il file locale specificato in `imageFilePath` per la lettura come `Stream` e quindi ottiene le caratteristiche visive e la lingua selezionate nella pagina dello scenario. Chiama il metodo `ComputerVisionClient.AnalyzeImageInStreamAsync`, passando il valore di `Stream` per il file, le caratteristiche visive e la lingua e quindi restituisce il risultato come istanza di `ImageAnalysis`. I metodi ereditati dalla classe `ImageScenarioPage` presentano i risultati restituiti nella pagina dello scenario.

Il metodo `AnalyzeUrlAsync` crea una nuova istanza di `ComputerVisionClient`, usando la chiave di sottoscrizione e l'URL dell'endpoint specificati. Ottiene le caratteristiche visive e la lingua selezionate nella pagina dello scenario. Chiama il metodo `ComputerVisionClient.AnalyzeImageInStreamAsync`, passando l'URL dell'immagine, le caratteristiche visive e la lingua e quindi restituisce il risultato come istanza di `ImageAnalysis`. I metodi ereditati dalla classe `ImageScenarioPage` presentano i risultati restituiti nella pagina dello scenario.

## <a name="explore-the-analyze-image-with-domain-model-scenario"></a>Esplorare lo scenario di analisi dell'immagine con modello di dominio

Questo scenario viene gestito dalla pagina AnalyzeInDomainPage.xaml. È possibile scegliere un modello di dominio, ad esempio `celebrities` o `landmarks`, e la lingua per eseguire un'analisi dell'immagine specifica del dominio e visualizzare sia l'immagine che i risultati. La pagina dello scenario usa i metodi seguenti, a seconda dell'origine dell'immagine:

* GetAvailableDomainModelsAsync  
  Questo metodo ottiene l'elenco di modelli di dominio disponibili da Visione artificiale e popola il controllo ComboBox `_domainModelComboBox` nella pagina, usando il metodo `ComputerVisionClient.ListModelsAsync`.
* UploadAndAnalyzeInDomainImageAsync  
  Questo metodo viene usato per le immagini locali, in cui l'immagine deve essere codificata come `Stream` e inviata a Visione artificiale chiamando il metodo `ComputerVisionClient.AnalyzeImageByDomainInStreamAsync`.
* AnalyzeInDomainUrlAsync  
  Questo metodo viene usato per le immagini remote, in cui l'URL dell'immagine viene inviato a Visione artificiale chiamando il metodo `ComputerVisionClient.AnalyzeImageByDomainAsync`.

Il metodo `UploadAndAnalyzeInDomainImageAsync` crea una nuova istanza di `ComputerVisionClient`, usando la chiave di sottoscrizione e l'URL dell'endpoint specificati. Poiché l'app di esempio analizza un'immagine locale, deve inviare il contenuto di tale immagine a Visione artificiale. L'app apre il file locale specificato in `imageFilePath` per la lettura come `Stream` e quindi ottiene la lingua selezionata nella pagina dello scenario. Chiama il metodo `ComputerVisionClient.AnalyzeImageByDomainInStreamAsync`, passando il valore di `Stream` per il file, il nome del modello di dominio e la lingua e quindi restituisce il risultato come istanza di `DomainModelResults`. I metodi ereditati dalla classe `ImageScenarioPage` presentano i risultati restituiti nella pagina dello scenario.

Il metodo `AnalyzeInDomainUrlAsync` crea una nuova istanza di `ComputerVisionClient`, usando la chiave di sottoscrizione e l'URL dell'endpoint specificati. Ottiene la lingua selezionata nella pagina dello scenario. Chiama il metodo `ComputerVisionClient.AnalyzeImageByDomainAsync`, passando l'URL dell'immagine, le caratteristiche visive e la lingua e quindi restituisce il risultato come istanza di `DomainModelResults`. I metodi ereditati dalla classe `ImageScenarioPage` presentano i risultati restituiti nella pagina dello scenario.

## <a name="explore-the-describe-image-scenario"></a>Esplorare lo scenario di descrizione dell'immagine

Questo scenario viene gestito dalla pagina DescribePage.xaml. È possibile scegliere una lingua per creare una descrizione dell'immagine leggibile dall'utente e visualizzare sia l'immagine che i risultati. La pagina dello scenario usa i metodi seguenti, a seconda dell'origine dell'immagine:

* UploadAndDescribeImageAsync  
  Questo metodo viene usato per le immagini locali, in cui l'immagine deve essere codificata come `Stream` e inviata a Visione artificiale chiamando il metodo `ComputerVisionClient.DescribeImageInStreamAsync`.
* DescribeUrlAsync  
  Questo metodo viene usato per le immagini remote, in cui l'URL dell'immagine viene inviato a Visione artificiale chiamando il metodo `ComputerVisionClient.DescribeImageAsync`.

Il metodo `UploadAndDescribeImageAsync` crea una nuova istanza di `ComputerVisionClient`, usando la chiave di sottoscrizione e l'URL dell'endpoint specificati. Poiché l'app di esempio analizza un'immagine locale, deve inviare il contenuto di tale immagine a Visione artificiale. L'app apre il file locale specificato in `imageFilePath` per la lettura come `Stream` e quindi ottiene la lingua selezionata nella pagina dello scenario. Chiama il metodo `ComputerVisionClient.DescribeImageInStreamAsync`, passando il valore di `Stream` per il file, il numero massimo di candidati (in questo caso, 3) e la lingua e quindi restituisce il risultato come istanza di `ImageDescription`. I metodi ereditati dalla classe `ImageScenarioPage` presentano i risultati restituiti nella pagina dello scenario.

Il metodo `DescribeUrlAsync` crea una nuova istanza di `ComputerVisionClient`, usando la chiave di sottoscrizione e l'URL dell'endpoint specificati. Ottiene la lingua selezionata nella pagina dello scenario. Chiama il metodo `ComputerVisionClient.DescribeImageAsync`, passando l'URL dell'immagine, il numero massimo di candidati (in questo caso, 3) e la lingua e quindi restituisce il risultato come istanza di `ImageDescription`. I metodi ereditati dalla classe `ImageScenarioPage` presentano i risultati restituiti nella pagina dello scenario.

## <a name="explore-the-generate-tags-scenario"></a>Esplorare lo scenario di generazione di tag

Questo scenario viene gestito dalla pagina TagsPage.xaml. È possibile scegliere una lingua per contrassegnare le caratteristiche visive di un'immagine e visualizzare sia l'immagine che i risultati. La pagina dello scenario usa i metodi seguenti, a seconda dell'origine dell'immagine:

* UploadAndGetTagsForImageAsync  
  Questo metodo viene usato per le immagini locali, in cui l'immagine deve essere codificata come `Stream` e inviata a Visione artificiale chiamando il metodo `ComputerVisionClient.TagImageInStreamAsync`.
* GenerateTagsForUrlAsync  
  Questo metodo viene usato per le immagini remote, in cui l'URL dell'immagine viene inviato a Visione artificiale chiamando il metodo `ComputerVisionClient.TagImageAsync`.

Il metodo `UploadAndGetTagsForImageAsync` crea una nuova istanza di `ComputerVisionClient`, usando la chiave di sottoscrizione e l'URL dell'endpoint specificati. Poiché l'app di esempio analizza un'immagine locale, deve inviare il contenuto di tale immagine a Visione artificiale. L'app apre il file locale specificato in `imageFilePath` per la lettura come `Stream` e quindi ottiene la lingua selezionata nella pagina dello scenario. Chiama il metodo `ComputerVisionClient.TagImageInStreamAsync`, passando il valore di `Stream` per il file e la lingua e quindi restituisce il risultato come istanza di `TagResult`. I metodi ereditati dalla classe `ImageScenarioPage` presentano i risultati restituiti nella pagina dello scenario.

Il metodo `GenerateTagsForUrlAsync` crea una nuova istanza di `ComputerVisionClient`, usando la chiave di sottoscrizione e l'URL dell'endpoint specificati. Ottiene la lingua selezionata nella pagina dello scenario. Chiama il metodo `ComputerVisionClient.TagImageAsync`, passando l'URL dell'immagine e la lingua e quindi restituisce il risultato come istanza di `TagResult`. I metodi ereditati dalla classe `ImageScenarioPage` presentano i risultati restituiti nella pagina dello scenario.

## <a name="explore-the-recognize-text-ocr-scenario"></a>Esplorare lo scenario di riconoscimento del testo (OCR)

Questo scenario viene gestito dalla pagina OCRPage.xaml. È possibile scegliere una lingua per riconoscere ed estrarre testo stampato da un'immagine e visualizzare sia l'immagine che i risultati. La pagina dello scenario usa i metodi seguenti, a seconda dell'origine dell'immagine:

* UploadAndRecognizeImageAsync  
  Questo metodo viene usato per le immagini locali, in cui l'immagine deve essere codificata come `Stream` e inviata a Visione artificiale chiamando il metodo `ComputerVisionClient.RecognizePrintedTextInStreamAsync`.
* RecognizeUrlAsync  
  Questo metodo viene usato per le immagini remote, in cui l'URL dell'immagine viene inviato a Visione artificiale chiamando il metodo `ComputerVisionClient.RecognizePrintedTextAsync`.

Il metodo `UploadAndRecognizeImageAsync` crea una nuova istanza di `ComputerVisionClient`, usando la chiave di sottoscrizione e l'URL dell'endpoint specificati. Poiché l'app di esempio analizza un'immagine locale, deve inviare il contenuto di tale immagine a Visione artificiale. L'app apre il file locale specificato in `imageFilePath` per la lettura come `Stream` e quindi ottiene la lingua selezionata nella pagina dello scenario. Chiama il metodo `ComputerVisionClient.RecognizePrintedTextInStreamAsync`, indicando che l'orientamento non viene rilevato e passando il valore di `Stream` per il file e la lingua e quindi restituisce il risultato come istanza di `OcrResult`. I metodi ereditati dalla classe `ImageScenarioPage` presentano i risultati restituiti nella pagina dello scenario.

Il metodo `RecognizeUrlAsync` crea una nuova istanza di `ComputerVisionClient`, usando la chiave di sottoscrizione e l'URL dell'endpoint specificati. Ottiene la lingua selezionata nella pagina dello scenario. Chiama il metodo `ComputerVisionClient.RecognizePrintedTextAsync`, indicando che l'orientamento non viene rilevato e passando l'URL dell'immagine e la lingua e quindi restituisce il risultato come istanza di `OcrResult`. I metodi ereditati dalla classe `ImageScenarioPage` presentano i risultati restituiti nella pagina dello scenario.

## <a name="explore-the-recognize-text-v2-english-scenario"></a>Esplorare lo scenario di riconoscimento del testo V2 (inglese)

Questo scenario viene gestito dalla pagina TextRecognitionPage.xaml. È possibile scegliere la modalità di riconoscimento e una lingua per riconoscere ed estrarre in modo asincrono il testo stampato o scritto a mano da un'immagine e visualizzare sia l'immagine che i risultati. La pagina dello scenario usa i metodi seguenti, a seconda dell'origine dell'immagine:

* UploadAndRecognizeImageAsync  
  Questo metodo viene usato per le immagini locali, in cui l'immagine deve essere codificata come `Stream` e inviata a Visione artificiale chiamando il metodo `RecognizeAsync` e passando un delegato con parametri per il metodo `ComputerVisionClient.RecognizeTextInStreamAsync`.
* RecognizeUrlAsync  
  Questo metodo viene usato per le immagini remote, in cui l'URL dell'immagine viene inviato a Visione artificiale chiamando il metodo `RecognizeAsync` e passando un delegato con parametri per il metodo `ComputerVisionClient.RecognizeTextAsync`.
* RecognizeAsync Questo metodo gestisce la chiamata asincrona per entrambi i metodi `UploadAndRecognizeImageAsync` e `RecognizeUrlAsync` e anche il polling dei risultati chiamando il metodo `ComputerVisionClient.GetTextOperationResultAsync`.

A differenza di altri scenari inclusi nell'app di esempio di Visione artificiale, questo scenario è asincrono. Vengono infatti chiamati un metodo per avviare il processo e un altro metodo per controllare lo stato e restituire i risultati del processo. Il flusso logico in questo scenario è diverso da quello degli altri scenari.

Il metodo `UploadAndRecognizeImageAsync` apre il file locale specificato in `imageFilePath` per la lettura come `Stream` e quindi chiama il metodo `RecognizeAsync`, passando quanto segue:

* Un'espressione lambda per un delegato asincrono con parametri del metodo `ComputerVisionClient.RecognizeTextInStreamAsync`, con il valore di `Stream` per il file e la modalità di riconoscimento come parametri, in `GetHeadersAsyncFunc`.
* Un'espressione lambda per un delegato per ottenere il valore di intestazione della risposta `Operation-Location`, in `GetOperationUrlFunc`.

Il metodo `RecognizeUrlAsync` chiama il metodo `RecognizeAsync`, passando quanto segue:

* Un'espressione lambda per un delegato asincrono con parametri del metodo `ComputerVisionClient.RecognizeTextAsync`, con l'URL dell'immagine remota e la modalità di riconoscimento come parametri, in `GetHeadersAsyncFunc`.
* Un'espressione lambda per un delegato per ottenere il valore di intestazione della risposta `Operation-Location`, in `GetOperationUrlFunc`.

Quando il metodo `RecognizeAsync` viene completato, entrambi i metodi `UploadAndRecognizeImageAsync` e `RecognizeUrlAsync` restituiscono il risultato come istanza di `TextOperationResult`. I metodi ereditati dalla classe `ImageScenarioPage` presentano i risultati restituiti nella pagina dello scenario.

Il metodo `RecognizeAsync` chiama il delegato con parametri per il metodo `ComputerVisionClient.RecognizeTextInStreamAsync` o `ComputerVisionClient.RecognizeTextAsync` passato in `GetHeadersAsyncFunc` e attende la risposta. Il metodo quindi chiama il delegato passato in `GetOperationUrlFunc` per ottenere il valore di intestazione `Operation-Location` dalla risposta. Questo valore è l'URL usato per recuperare i risultati del metodo passati in `GetHeadersAsyncFunc` da Visione artificiale.

Il metodo `RecognizeAsync` chiama quindi il metodo `ComputerVisionClient.GetTextOperationResultAsync`, passando l'URL recuperato dall'intestazione della risposta `Operation-Location`, per ottenere lo stato e il risultato del metodo passato in `GetHeadersAsyncFunc`. Se lo stato non indica che il metodo è stato completato, con esito positivo o negativo, il metodo `RecognizeAsync` chiama `ComputerVisionClient.GetTextOperationResultAsync` altre 3 volte, aspettando 3 secondi tra le chiamate. Il metodo `RecognizeAsync` restituisce i risultati al metodo che lo ha chiamato.

## <a name="explore-the-get-thumbnail-scenario"></a>Esplorare lo scenario di generazione dell'anteprima

Questo scenario viene gestito dalla pagina ThumbnailPage.xaml. È possibile indicare se usare il ritaglio intelligente e specificare l'altezza e la larghezza desiderate, per generare un'anteprima da un'immagine e visualizzare sia l'immagine che i risultati. La pagina dello scenario usa i metodi seguenti, a seconda dell'origine dell'immagine:

* UploadAndThumbnailImageAsync  
  Questo metodo viene usato per le immagini locali, in cui l'immagine deve essere codificata come `Stream` e inviata a Visione artificiale chiamando il metodo `ComputerVisionClient.GenerateThumbnailInStreamAsync`.
* ThumbnailUrlAsync  
  Questo metodo viene usato per le immagini remote, in cui l'URL dell'immagine viene inviato a Visione artificiale chiamando il metodo `ComputerVisionClient.GenerateThumbnailAsync`.

Il metodo `UploadAndThumbnailImageAsync` crea una nuova istanza di `ComputerVisionClient`, usando la chiave di sottoscrizione e l'URL dell'endpoint specificati. Poiché l'app di esempio analizza un'immagine locale, deve inviare il contenuto di tale immagine a Visione artificiale. Apre il file locale specificato in `imageFilePath` per la lettura come `Stream`. Chiama il metodo `ComputerVisionClient.GenerateThumbnailInStreamAsync`, passando la larghezza, l'altezza, il valore di `Stream` per il file e indicando se usare il ritaglio intelligente e quindi restituisce il risultato come `Stream`. I metodi ereditati dalla classe `ImageScenarioPage` presentano i risultati restituiti nella pagina dello scenario.

Il metodo `RecognizeUrlAsync` crea una nuova istanza di `ComputerVisionClient`, usando la chiave di sottoscrizione e l'URL dell'endpoint specificati. Chiama il metodo `ComputerVisionClient.GenerateThumbnailAsync`, passando la larghezza, l'altezza, l'URL dell'immagine e indicando se usare il ritaglio intelligente e quindi restituisce il risultato come `Stream`. I metodi ereditati dalla classe `ImageScenarioPage` presentano i risultati restituiti nella pagina dello scenario.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non è più necessario, eliminare la cartella in cui è stato clonato il repository `Microsoft/Cognitive-Vision-Windows`. Se si è scelto di usare le immagini di esempio, eliminare anche la cartella in cui è stato clonato il repository `Microsoft/Cognitive-Face-Windows`.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Introduzione all'API Viso](../../Face/Tutorials/FaceAPIinCSharpTutorial.md)