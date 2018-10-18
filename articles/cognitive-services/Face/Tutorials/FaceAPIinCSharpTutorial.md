---
title: "Esercitazione: rilevare e incorniciare il viso in un'immagine - API Viso, C#"
titleSuffix: Azure Cognitive Services
description: In questa esercitazione viene creata un'app Windows che usa la funzione API Viso per rilevare e incorniciare il volto in un'immagine.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: face-api
ms.topic: tutorial
ms.date: 09/24/2018
ms.author: pafarley
ms.openlocfilehash: ba850c1d4f9d8018f333662eead56f8e91b3fbd7
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2018
ms.locfileid: "49340954"
---
# <a name="tutorial-create-a-wpf-app-to-detect-and-frame-faces-in-an-image"></a>Esercitazione: Creare un'app WPF per rilevare e incorniciare i visi in un'immagine

In questa esercitazione viene creata un'applicazione Windows Presentation Framework (WPF) che usa il servizio Viso tramite la rispettiva libreria client .NET. L'app rileva i visi in un'immagine, traccia una cornice intorno a ogni viso e visualizza una descrizione del viso sulla barra di stato. Il codice di esempio completo è disponibile su GitHub in [Detect and frame faces in an image on Windows](https://github.com/Azure-Samples/Cognitive-Face-CSharp-sample) (Rilevare e incorniciare i visi in un'immagine in Windows).

![Screenshot che mostra i visi rilevati racchiusi in rettangoli](../Images/getting-started-cs-detected.png)

Questa esercitazione illustra come:

> [!div class="checklist"]
> - Creare un'applicazione WPF
> - Installare la libreria client del servizio Viso
> - Usare la libreria client per rilevare i visi in un'immagine
> - Tracciare una cornice intorno a ogni viso rilevato
> - Visualizzare una descrizione del viso sulla barra di stato

## <a name="prerequisites"></a>Prerequisiti

- Per eseguire l'esempio è necessaria una sottoscrizione. È possibile ottenere le chiavi di sottoscrizione della versione di valutazione gratuita da [Prova Servizi cognitivi](https://azure.microsoft.com/try/cognitive-services/?api=face-api).
- Qualsiasi edizione di [Visual Studio 2015 o 2017](https://www.visualstudio.com/downloads/). Per Visual Studio 2017 è necessario il carico di lavoro di sviluppo di applicazioni .NET Desktop. Questa esercitazione usa Visual Studio 2017 Community Edition.
- Pacchetto NuGet della libreria client [Microsoft.Azure.CognitiveServices.Vision.Face 2.2.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview). Non è necessario scaricare il pacchetto. Le istruzioni di installazione sono disponibili più avanti.

## <a name="create-the-visual-studio-solution"></a>Creare la soluzione in Visual Studio

Seguire questa procedura per creare un progetto di applicazione Windows o WPF.

1. Aprire Visual Studio, scegliere **Nuovo** dal menu **File** e infine **Progetto**.
   - In Visual Studio 2017 espandere **Installato**, quindi **Altri linguaggi**. Selezionare **Visual C#** e quindi **App WPF (.NET Framework)**.
   - In Visual Studio 2015 espandere **Installato** e quindi **Modelli**. Selezionare **Visual C#** e quindi **Applicazione WPF**.
1. Assegnare all'applicazione il nome **FaceTutorial** e fare clic su **OK**.

## <a name="install-the-face-service-client-library"></a>Installare la libreria client del servizio Viso

Seguire queste istruzioni per installare la libreria client.

1. Scegliere **Gestione pacchetti NuGet** dal menu **Strumenti** e quindi fare clic su **Console di Gestione pacchetti**.
1. In **Console di Gestione pacchetti** incollare il codice seguente e quindi premere **INVIO**.

    `Install-Package Microsoft.Azure.CognitiveServices.Vision.Face -Version 2.2.0-preview`

## <a name="add-the-initial-code"></a>Aggiungere il codice iniziale

### <a name="mainwindowxaml"></a>MainWindow.xaml

Aprire *MainWindow.xaml* (suggerimento: spostarsi tra i riquadri usando l'**icona a forma di freccia verso il basso/alto**) e sostituire i contenuti con il codice seguente. Questo codice XAML viene usato per creare la finestra dell'interfaccia utente. Notare i gestori dell'evento `FacePhoto_MouseMove` e `BrowseButton_Click`.

```xml
<Window x:Class="FaceTutorial.MainWindow"
        xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
        xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
        Title="MainWindow" Height="700" Width="960">
    <Grid x:Name="BackPanel">
        <Image x:Name="FacePhoto" Stretch="Uniform" Margin="0,0,0,50" MouseMove="FacePhoto_MouseMove" />
        <DockPanel DockPanel.Dock="Bottom">
            <Button x:Name="BrowseButton" Width="72" Height="20" VerticalAlignment="Bottom" HorizontalAlignment="Left"
                    Content="Browse..."
                    Click="BrowseButton_Click" />
            <StatusBar VerticalAlignment="Bottom">
                <StatusBarItem>
                    <TextBlock Name="faceDescriptionStatusBar" />
                </StatusBarItem>
            </StatusBar>
        </DockPanel>
    </Grid>
</Window>
```

### <a name="mainwindowxamlcs"></a>MainWindow.xaml.cs

Espandere *MainWindow.xaml*, quindi aprire *MainWindow.xaml.cs* e sostituire i contenuti con il codice seguente. Ignorare le sottolineature rosse ondulate. Scompariranno dopo la prima compilazione.

Le prime due righe consentono di importare gli spazi dei nomi della libreria client. Viene quindi creato l'elemento `FaceClient` tramite il passaggio della chiave di sottoscrizione, mentre l'area di Azure viene impostata nel costruttore `MainWindow`. I due metodi, `BrowseButton_Click` e `FacePhoto_MouseMove`, corrispondono ai gestori dell'evento dichiarati in *MainWindow.xaml*.

`BrowseButton_Click` crea un elemento `OpenFileDialog`, che consente all'utente di selezionare un'immagine JPG. L'immagine viene letta e visualizzata nella finestra principale. Il codice rimanente per `BrowseButton_Click` e il codice per `FacePhoto_MouseMove` vengono inseriti in passaggi successivi.

```csharp
using Microsoft.Azure.CognitiveServices.Vision.Face;
using Microsoft.Azure.CognitiveServices.Vision.Face.Models;

using System;
using System.Collections.Generic;
using System.IO;
using System.Text;
using System.Threading.Tasks;
using System.Windows;
using System.Windows.Input;
using System.Windows.Media;
using System.Windows.Media.Imaging;

namespace FaceTutorial
{
    public partial class MainWindow : Window
    {
        // Replace <SubscriptionKey> with your valid subscription key.
        // For example, subscriptionKey = "0123456789abcdef0123456789ABCDEF"
        private const string subscriptionKey = "<SubscriptionKey>";

        // Replace or verify the region.
        //
        // You must use the same region as you used to obtain your subscription
        // keys. For example, if you obtained your subscription keys from the
        // westus region, replace "westcentralus" with "westus".
        //
        // NOTE: Free trial subscription keys are generated in the westcentralus
        // region, so if you are using a free trial subscription key, you should
        // not need to change this region.
        private const string faceEndpoint =
            "https://westcentralus.api.cognitive.microsoft.com";

        private readonly IFaceClient faceClient = new FaceClient(
            new ApiKeyServiceClientCredentials(subscriptionKey),
            new System.Net.Http.DelegatingHandler[] { });

        IList<DetectedFace> faceList;   // The list of detected faces.
        String[] faceDescriptions;      // The list of descriptions for the detected faces.
        double resizeFactor;            // The resize factor for the displayed image.

        public MainWindow()
        {
            InitializeComponent();

            if (Uri.IsWellFormedUriString(faceEndpoint, UriKind.Absolute))
            {
                faceClient.Endpoint = faceEndpoint;
            }
            else
            {
                MessageBox.Show(faceEndpoint,
                    "Invalid URI", MessageBoxButton.OK, MessageBoxImage.Error);
                Environment.Exit(0);
            }
        }

        // Displays the image and calls UploadAndDetectFaces.
        private async void BrowseButton_Click(object sender, RoutedEventArgs e)
        {
            // Get the image file to scan from the user.
            var openDlg = new Microsoft.Win32.OpenFileDialog();

            openDlg.Filter = "JPEG Image(*.jpg)|*.jpg";
            bool? result = openDlg.ShowDialog(this);

            // Return if canceled.
            if (!(bool)result)
            {
                return;
            }

            // Display the image file.
            string filePath = openDlg.FileName;

            Uri fileUri = new Uri(filePath);
            BitmapImage bitmapSource = new BitmapImage();

            bitmapSource.BeginInit();
            bitmapSource.CacheOption = BitmapCacheOption.None;
            bitmapSource.UriSource = fileUri;
            bitmapSource.EndInit();

            FacePhoto.Source = bitmapSource;
        }

        // Displays the face description when the mouse is over a face rectangle.
        private void FacePhoto_MouseMove(object sender, MouseEventArgs e)
        {
        }
    }
}
```

### <a name="insert-your-subscription-key-and-verify-or-change-the-region"></a>Inserire la chiave di sottoscrizione e verificare o modificare l'area

- Individuare la riga seguente in *MainWindow.xaml.cs* e sostituire `<Subscription Key>` con la chiave di sottoscrizione dell'API Viso:

    ```csharp
    private const string subscriptionKey = "<SubscriptionKey>";
    ```

- Individuare la riga seguente in *MainWindow.xaml.cs* e sostituire o verificare l'area di Azure associata alla Individuare la riga seguente in:

    ```csharp
    private const string Endpoint =
        "https://westcentralus.api.cognitive.microsoft.com";
    ```

    Assicurarsi che la posizione sia uguale a quella in cui sono state ottenute le chiavi di sottoscrizione. Se le chiavi di sottoscrizione sono state ottenute ad esempio dall'area **westus**, sostituire `Westcentralus` con `Westus`.

    Se le chiavi di sottoscrizione sono state ottenute usando la versione di valutazione gratuita, l'area delle chiavi è **westcentralus**, pertanto non è richiesta alcuna modifica.

### <a name="test-the-app"></a>Testare l'app

Scegliere **Avvia** dal menu per testare l'app. All'apertura della finestra fare clic su **Sfoglia** nell'angolo inferiore sinistro. Verrà visualizzata una finestra di dialogo di tipo **Apertura file** in cui è possibile esplorare e selezionare una foto, che viene quindi visualizzata nella finestra.

![Screenshot che mostra un'immagine non modificata dei visi](../Images/getting-started-cs-ui.png)

## <a name="upload-an-image-to-detect-faces"></a>Caricare un'immagine per rilevare i visi

Il modo più diretto di rilevare i visi consiste nel chiamare il metodo `FaceClient.Face.DetectWithStreamAsync`, che esegue il wrapping del metodo dell'API [Rileva](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) per il caricamento dell'immagine locale.

Inserire il metodo seguente nella classe `MainWindow` sotto il metodo `FacePhoto_MouseMove`.

Viene creato un elenco di attributi dei visi da analizzare e il file di immagine inviato viene letto in `Stream`. Entrambi vengono passati alla chiamata `DetectWithStreamAsync`.

```csharp
// Uploads the image file and calls DetectWithStreamAsync.
private async Task<IList<DetectedFace>> UploadAndDetectFaces(string imageFilePath)
{
    // The list of Face attributes to return.
    IList<FaceAttributeType> faceAttributes =
        new FaceAttributeType[]
        {
            FaceAttributeType.Gender, FaceAttributeType.Age,
            FaceAttributeType.Smile, FaceAttributeType.Emotion,
            FaceAttributeType.Glasses, FaceAttributeType.Hair
        };

    // Call the Face API.
    try
    {
        using (Stream imageFileStream = File.OpenRead(imageFilePath))
        {
            // The second argument specifies to return the faceId, while
            // the third argument specifies not to return face landmarks.
            IList<DetectedFace> faceList =
                await faceClient.Face.DetectWithStreamAsync(
                    imageFileStream, true, false, faceAttributes);
            return faceList;
        }
    }
    // Catch and display Face API errors.
    catch (APIErrorException f)
    {
        MessageBox.Show(f.Message);
        return new List<DetectedFace>();
    }
    // Catch and display all other errors.
    catch (Exception e)
    {
        MessageBox.Show(e.Message, "Error");
        return new List<DetectedFace>();
    }
}
```

## <a name="draw-rectangles-around-each-face"></a>Tracciare rettangoli intorno a ogni viso

Aggiungere il codice per tracciare un rettangolo intorno a ogni viso rilevato nell'immagine.

In *MainWindow.xaml.cs*, aggiungere il modificatore `async` al metodo `BrowseButton_Click`.

```csharp
private async void BrowseButton_Click(object sender, RoutedEventArgs e)
```

Inserire il codice seguente alla fine del metodo `BrowseButton_Click`, dopo la riga `FacePhoto.Source = bitmapSource`.

L'elenco di visi rilevati viene popolato tramite la chiamata a `UploadAndDetectFaces`. Viene quindi tracciato un rettangolo intorno a ogni viso e l'immagine modificata viene visualizzata nella finestra principale.

```csharp
// Detect any faces in the image.
Title = "Detecting...";
faceList = await UploadAndDetectFaces(filePath);
Title = String.Format(
    "Detection Finished. {0} face(s) detected", faceList.Count);

if (faceList.Count > 0)
{
    // Prepare to draw rectangles around the faces.
    DrawingVisual visual = new DrawingVisual();
    DrawingContext drawingContext = visual.RenderOpen();
    drawingContext.DrawImage(bitmapSource,
        new Rect(0, 0, bitmapSource.Width, bitmapSource.Height));
    double dpi = bitmapSource.DpiX;
    resizeFactor = (dpi > 0) ? 96 / dpi : 1;
    faceDescriptions = new String[faceList.Count];

    for (int i = 0; i < faceList.Count; ++i)
    {
        DetectedFace face = faceList[i];

        // Draw a rectangle on the face.
        drawingContext.DrawRectangle(
            Brushes.Transparent,
            new Pen(Brushes.Red, 2),
            new Rect(
                face.FaceRectangle.Left * resizeFactor,
                face.FaceRectangle.Top * resizeFactor,
                face.FaceRectangle.Width * resizeFactor,
                face.FaceRectangle.Height * resizeFactor
                )
        );

        // Store the face description.
        faceDescriptions[i] = FaceDescription(face);
    }

    drawingContext.Close();

    // Display the image with the rectangle around the face.
    RenderTargetBitmap faceWithRectBitmap = new RenderTargetBitmap(
        (int)(bitmapSource.PixelWidth * resizeFactor),
        (int)(bitmapSource.PixelHeight * resizeFactor),
        96,
        96,
        PixelFormats.Pbgra32);

    faceWithRectBitmap.Render(visual);
    FacePhoto.Source = faceWithRectBitmap;

    // Set the status bar text.
    faceDescriptionStatusBar.Text =
        "Place the mouse pointer over a face to see the face description.";
}
```

## <a name="describe-the-faces-in-the-image"></a>Descrivere i visi nell'immagine

Aggiungere il metodo seguente alla fine della classe `MainWindow`, sotto il metodo `UploadAndDetectFaces`.

Il metodo converte gli attributi del viso in una stringa che descrive il viso. La stringa viene visualizzata quando si sofferma il puntatore del mouse sul rettangolo del viso.

```csharp
// Creates a string out of the attributes describing the face.
private string FaceDescription(DetectedFace face)
{
    StringBuilder sb = new StringBuilder();

    sb.Append("Face: ");

    // Add the gender, age, and smile.
    sb.Append(face.FaceAttributes.Gender);
    sb.Append(", ");
    sb.Append(face.FaceAttributes.Age);
    sb.Append(", ");
    sb.Append(String.Format("smile {0:F1}%, ", face.FaceAttributes.Smile * 100));

    // Add the emotions. Display all emotions over 10%.
    sb.Append("Emotion: ");
    Emotion emotionScores = face.FaceAttributes.Emotion;
    if (emotionScores.Anger >= 0.1f)
        sb.Append(String.Format("anger {0:F1}%, ", emotionScores.Anger * 100));
    if (emotionScores.Contempt >= 0.1f)
        sb.Append(String.Format("contempt {0:F1}%, ", emotionScores.Contempt * 100));
    if (emotionScores.Disgust >= 0.1f)
        sb.Append(String.Format("disgust {0:F1}%, ", emotionScores.Disgust * 100));
    if (emotionScores.Fear >= 0.1f)
        sb.Append(String.Format("fear {0:F1}%, ", emotionScores.Fear * 100));
    if (emotionScores.Happiness >= 0.1f)
        sb.Append(String.Format("happiness {0:F1}%, ", emotionScores.Happiness * 100));
    if (emotionScores.Neutral >= 0.1f)
        sb.Append(String.Format("neutral {0:F1}%, ", emotionScores.Neutral * 100));
    if (emotionScores.Sadness >= 0.1f)
        sb.Append(String.Format("sadness {0:F1}%, ", emotionScores.Sadness * 100));
    if (emotionScores.Surprise >= 0.1f)
        sb.Append(String.Format("surprise {0:F1}%, ", emotionScores.Surprise * 100));

    // Add glasses.
    sb.Append(face.FaceAttributes.Glasses);
    sb.Append(", ");

    // Add hair.
    sb.Append("Hair: ");

    // Display baldness confidence if over 1%.
    if (face.FaceAttributes.Hair.Bald >= 0.01f)
        sb.Append(String.Format("bald {0:F1}% ", face.FaceAttributes.Hair.Bald * 100));

    // Display all hair color attributes over 10%.
    IList<HairColor> hairColors = face.FaceAttributes.Hair.HairColor;
    foreach (HairColor hairColor in hairColors)
    {
        if (hairColor.Confidence >= 0.1f)
        {
            sb.Append(hairColor.Color.ToString());
            sb.Append(String.Format(" {0:F1}% ", hairColor.Confidence * 100));
        }
    }

    // Return the built string.
    return sb.ToString();
}
```

## <a name="display-the-face-description"></a>Visualizzare la descrizione del viso

Sostituire il metodo `FacePhoto_MouseMove` con il codice seguente.

Questo gestore dell'evento mostra la stringa di descrizione del viso quando il puntatore del mouse si sofferma sopra il rettangolo del viso.

```csharp
private void FacePhoto_MouseMove(object sender, MouseEventArgs e)
{
    // If the REST call has not completed, return.
    if (faceList == null)
        return;

    // Find the mouse position relative to the image.
    Point mouseXY = e.GetPosition(FacePhoto);

    ImageSource imageSource = FacePhoto.Source;
    BitmapSource bitmapSource = (BitmapSource)imageSource;

    // Scale adjustment between the actual size and displayed size.
    var scale = FacePhoto.ActualWidth / (bitmapSource.PixelWidth / resizeFactor);

    // Check if this mouse position is over a face rectangle.
    bool mouseOverFace = false;

    for (int i = 0; i < faceList.Count; ++i)
    {
        FaceRectangle fr = faceList[i].FaceRectangle;
        double left = fr.Left * scale;
        double top = fr.Top * scale;
        double width = fr.Width * scale;
        double height = fr.Height * scale;

        // Display the face description if the mouse is over this face rectangle.
        if (mouseXY.X >= left && mouseXY.X <= left + width &&
            mouseXY.Y >= top  && mouseXY.Y <= top + height)
        {
            faceDescriptionStatusBar.Text = faceDescriptions[i];
            mouseOverFace = true;
            break;
        }
    }

    // String to display when the mouse is not over a face rectangle.
    if (!mouseOverFace)
        faceDescriptionStatusBar.Text =
            "Place the mouse pointer over a face to see the face description.";
}
```

## <a name="run-the-app"></a>Esecuzione dell'app

Eseguire l'applicazione e quindi cercare un'immagine che contenga un viso. Attendere qualche secondo per consentire al servizio Viso di rispondere. Verrà quindi visualizzato un rettangolo rosso intorno ai visi nell'immagine. Se si sposta il mouse sul rettangolo del viso, viene visualizzata la descrizione del viso sulla barra di stato.

![Screenshot che mostra i visi rilevati racchiusi in rettangoli](../Images/getting-started-cs-detected.png)

## <a name="summary"></a>Summary

In questa esercitazione si è appreso il processo di base per l'uso della libreria client del servizio Viso ed è stata creata un'applicazione per visualizzare e incorniciare i visi in un'immagine.

## <a name="next-steps"></a>Passaggi successivi

Informazioni sul rilevamento e sull'uso dei punti di riferimento del viso.

> [!div class="nextstepaction"]
> [Come rilevare visi nelle immagini](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)
