---
title: Esercitazione in C# sull'API Viso | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Creare una semplice app per Windows che usa l'API Emozioni di Servizi cognitivi Microsoft per rilevare i visi in un'immagine tramite l'inquadratura di volti.
services: cognitive-services
author: SteveMSFT
manager: corncar
ms.service: cognitive-services
ms.component: face-api
ms.topic: article
ms.date: 03/01/2018
ms.author: sbowles
ms.openlocfilehash: ac4f247eb38cafc5582c1ffbb3752e049f0305ba
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35374236"
---
# <a name="getting-started-with-face-api-in-c35-tutorial"></a>Introduzione all'esercitazione in C&#35; sull'API Viso

In questa esercitazione si creerà un'applicazione WPF per Windows che usa l'API Viso. L'applicazione rileva i volti in un'immagine, disegna una cornice intorno a ogni viso e visualizza una descrizione del viso nella barra di stato.

![GettingStartCSharpScreenshot](../Images/getting-started-cs-detected.png)

## <a name="Preparation"></a>Operazioni preliminari

Per usare questa esercitazione è necessario soddisfare i prerequisiti seguenti:

- Verificare che sia installato Visual Studio 2015 o versione successiva.

## <a name="step1"></a>Passaggio 1: Eseguire la sottoscrizione all'API Viso e ottenere la chiave di sottoscrizione

Prima di usare l'API Viso, è necessario registrarsi per sottoscrivere l'API Viso nel portale di Servizi cognitivi Microsoft. Vedere [Sottoscrizioni](https://azure.microsoft.com/try/cognitive-services/). In questa esercitazione è possibile usare entrambi le chiavi primaria e secondaria.

## <a name="step2"></a>Passaggio 2: Creare la soluzione in Visual Studio

In questo passaggio si crea un progetto di applicazione WPF per Windows per creare un'applicazione di base per la selezione e la visualizzazione di un'immagine. Seguire queste istruzioni:

1. Aprire Visual Studio.
1. Scegliere **Nuovo** dal menu **File**, quindi **Progetto**.
1. Nella finestra di dialogo **Nuovo progetto** selezionare WPF per l'applicazione.

   In Visual Studio 2015, espandere **Modelli** &gt; **installati** &gt; **Visual c#** &gt; **Windows** &gt; **Desktop classico** &gt; e selezionare **Applicazione WPF**.

   In Visual Studio 2017, espandere **Modelli** &gt; **installati** &gt; **Visual C#** &gt; **Desktop classico di Windows** &gt; e selezionare **App WPF (.NET Framework)**.
1. Assegnare all'applicazione il nome **FaceTutorial** e fare clic su **OK**.

   ![La finestra di dialogo Nuovo progetto, con l'applicazione WPF selezionata](../Images/vs2017-new-project.png)

1. Individuare **Esplora soluzioni**, fare clic con il pulsante destro del mouse sul progetto (in questo caso **FaceTutorial**) e quindi fare clic su **Gestisci pacchetti NuGet**.
1. Nella finestra **Gestione pacchetti NuGet** selezionare **nuget.org** come origine del pacchetto.
1. Cercare **Newtonsoft.Json**, quindi fare clic su **Installa**. Se si usa Visual Studio 2017, fare prima clic sulla scheda **Sfoglia**, quindi su **Cerca**.

   ![GettingStartCSharpPackageManager](../Images/install-nsoft-json.png)

## <a name="step3"></a>Passaggio 3: Configurare la libreria client dell'API Viso

API Viso è un'API cloud che è possibile richiamare tramite richieste HTTPS REST. Per facilitarne l'uso nelle applicazioni .NET, una libreria client .NET incapsula le richieste REST dell'API Viso. In questo esempio si usa la libreria client per semplificare il lavoro.

Per configurare la libreria client, seguire questa procedura:

1. In **Esplora soluzioni**, fare clic con il pulsante destro del mouse sul progetto (in questo caso **FaceTutorial**) e quindi fare clic su **Gestisci pacchetti NuGet**.
1. Nella finestra **Gestione pacchetti NuGet** selezionare **nuget.org** come origine del pacchetto.
1. Cercare **Microsoft.ProjectOxford.Face**, quindi fare clic su **Installa**. Se si usa Visual Studio 2017, fare prima clic sulla scheda **Sfoglia**, quindi su **Cerca**.

   ![GettingStartCSharpPackageManagerSDK](../Images/install-project-oxford-face.png)

1. In **Esplora soluzioni** verificare i riferimenti del progetto. I riferimenti **Microsoft.ProjectOxford.Common**, **Microsoft.ProjectOxford.Face** e **Newtonsoft.Json** vengono aggiunti automaticamente quando l'installazione ha esito positivo.

   ![GetStartedCSharp-CheckInstallation.png](../Images/GetStartedCSharp-CheckInstallation.png)

## <a name="step3"></a>Passaggio 4: Copiare e incollare il codice iniziale

1. Aprire il file MainWindow.xaml e sostituire il codice esistente con il codice riportato di seguito per creare l'interfaccia utente della finestra:

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

1. Aprire il file Open MainWindow.xaml.cs e sostituire il codice esistente con quello riportato di seguito:

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Text;
    using System.Threading.Tasks;
    using System.Windows;
    using System.Windows.Input;
    using System.Windows.Media;
    using System.Windows.Media.Imaging;
    using Microsoft.ProjectOxford.Common.Contract;
    using Microsoft.ProjectOxford.Face;
    using Microsoft.ProjectOxford.Face.Contract;

    namespace FaceTutorial
    {
        public partial class MainWindow : Window
        {
            // Replace the first parameter with your valid subscription key.
            //
            // Replace or verify the region in the second parameter.
            //
            // You must use the same region in your REST API call as you used to obtain your subscription keys.
            // For example, if you obtained your subscription keys from the westus region, replace
            // "westcentralus" in the URI below with "westus".
            //
            // NOTE: Free trial subscription keys are generated in the westcentralus region, so if you are using
            // a free trial subscription key, you should not need to change this region.
            private readonly IFaceServiceClient faceServiceClient =
                new FaceServiceClient("<Subscription Key>", "https://westcentralus.api.cognitive.microsoft.com/face/v1.0");

            Face[] faces;                   // The list of detected faces.
            String[] faceDescriptions;      // The list of descriptions for the detected faces.
            double resizeFactor;            // The resize factor for the displayed image.
            
            public MainWindow()
            {
                InitializeComponent();
            }
            
            // Displays the image and calls Detect Faces.

            private void BrowseButton_Click(object sender, RoutedEventArgs e)
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

1. Inserire la chiave di sottoscrizione e verificare l'area.

    Trovare questa riga nel file MainWindow.xaml.cs (righe 29 e 28):

    ```csharp
    private readonly IFaceServiceClient faceServiceClient =
            new FaceServiceClient("<Subscription Key>", "https://westcentralus.api.cognitive.microsoft.com/face/v1.0");
    ```

    Sostituire `<Subscription Key>` nel primo parametro con la chiave di sottoscrizione dell'API Viso ottenuta nel passaggio 1.

    Controllare inoltre il secondo parametro per assicurarsi di usare la posizione da cui sono state ottenute le chiavi di sottoscrizione. Se le chiavi di sottoscrizione sono state ottenute ad esempio dall'area westus, sostituire "**westcentralus**" nell'URI con "**westus**".

    Se le chiavi di sottoscrizione sono state ottenute usando la versione di valutazione gratuita, l'area delle chiavi è **westcentralus**, pertanto non è richiesta alcuna modifica.

L'applicazione può a questo punto cercare una foto e visualizzarla nella finestra.

![GettingStartCSharpUI](../Images/getting-started-cs-ui.png)

## <a name="step4"></a>Passaggio 5: Caricare immagini per rilevare i visi

Il modo più diretto di rilevare i visi consiste nel richiamare l'API di [rilevamento viso](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) caricando direttamente il file immagine.
Quando si usa la libreria client, questa operazione può essere eseguita usando il metodo asincrono DetectAsync di FaceServiceClient.
Ogni viso restituito è racchiuso in un rettangolo che ne indica la posizione, combinata con una serie di attributi del viso opzionali.

Inserire il codice seguente nella classe **MainWindow**:

```csharp
// Uploads the image file and calls Detect Faces.

private async Task<Face[]> UploadAndDetectFaces(string imageFilePath)
{
    // The list of Face attributes to return.
    IEnumerable<FaceAttributeType> faceAttributes =
        new FaceAttributeType[] { FaceAttributeType.Gender, FaceAttributeType.Age, FaceAttributeType.Smile, FaceAttributeType.Emotion, FaceAttributeType.Glasses, FaceAttributeType.Hair };

    // Call the Face API.
    try
    {
        using (Stream imageFileStream = File.OpenRead(imageFilePath))
        {
            Face[] faces = await faceServiceClient.DetectAsync(imageFileStream, returnFaceId: true, returnFaceLandmarks:false, returnFaceAttributes: faceAttributes);
            return faces;
        }
    }
    // Catch and display Face API errors.
    catch (FaceAPIException f)
    {
        MessageBox.Show(f.ErrorMessage, f.ErrorCode);
        return new Face[0];
    }
    // Catch and display all other errors.
    catch (Exception e)
    {
        MessageBox.Show(e.Message, "Error");
        return new Face[0];
    }
}
```

## <a name="step5"></a>Passaggio 6: Contrassegnare i visi nell'immagine

In questo passaggio si combinano tutti i passaggi precedenti e si contrassegnano i visi rilevati nell'immagine.

Nel file **MainWindow.xaml.cs** aggiungere il modificatore 'async' al metodo **BrowseButton_Click**:

```csharp
private async void BrowseButton_Click(object sender, RoutedEventArgs e)
```

Inserire il codice seguente alla fine del gestore dell'evento **BrowseButton_Click**:

```csharp
// Detect any faces in the image.
Title = "Detecting...";
faces = await UploadAndDetectFaces(filePath);
Title = String.Format("Detection Finished. {0} face(s) detected", faces.Length);

if (faces.Length > 0)
{
    // Prepare to draw rectangles around the faces.
    DrawingVisual visual = new DrawingVisual();
    DrawingContext drawingContext = visual.RenderOpen();
    drawingContext.DrawImage(bitmapSource,
        new Rect(0, 0, bitmapSource.Width, bitmapSource.Height));
    double dpi = bitmapSource.DpiX;
    resizeFactor = 96 / dpi;
    faceDescriptions = new String[faces.Length];

    for (int i = 0; i < faces.Length; ++i)
    {
        Face face = faces[i];

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
    faceDescriptionStatusBar.Text = "Place the mouse pointer over a face to see the face description.";
}
```

## <a name="step6"></a>Passaggio 7: Descrivere i visi nell'immagine

In questo passaggio si esaminano le proprietà di Viso e si genera una stringa per descrivere il viso. Questa stringa viene visualizzata quando si sofferma il puntatore del mouse sul rettangolo del viso.

Aggiungere inoltre questo metodo alla classe **MainWindow** per convertire i dettagli del viso in una stringa:

```csharp
// Returns a string that describes the given face.

private string FaceDescription(Face face)
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
    EmotionScores emotionScores = face.FaceAttributes.Emotion;
    if (emotionScores.Anger     >= 0.1f) sb.Append(String.Format("anger {0:F1}%, ",     emotionScores.Anger * 100));
    if (emotionScores.Contempt  >= 0.1f) sb.Append(String.Format("contempt {0:F1}%, ",  emotionScores.Contempt * 100));
    if (emotionScores.Disgust   >= 0.1f) sb.Append(String.Format("disgust {0:F1}%, ",   emotionScores.Disgust * 100));
    if (emotionScores.Fear      >= 0.1f) sb.Append(String.Format("fear {0:F1}%, ",      emotionScores.Fear * 100));
    if (emotionScores.Happiness >= 0.1f) sb.Append(String.Format("happiness {0:F1}%, ", emotionScores.Happiness * 100));
    if (emotionScores.Neutral   >= 0.1f) sb.Append(String.Format("neutral {0:F1}%, ",   emotionScores.Neutral * 100));
    if (emotionScores.Sadness   >= 0.1f) sb.Append(String.Format("sadness {0:F1}%, ",   emotionScores.Sadness * 100));
    if (emotionScores.Surprise  >= 0.1f) sb.Append(String.Format("surprise {0:F1}%, ",  emotionScores.Surprise * 100));

    // Add glasses.
    sb.Append(face.FaceAttributes.Glasses);
    sb.Append(", ");

    // Add hair.
    sb.Append("Hair: ");

    // Display baldness confidence if over 1%.
    if (face.FaceAttributes.Hair.Bald >= 0.01f)
        sb.Append(String.Format("bald {0:F1}% ", face.FaceAttributes.Hair.Bald * 100));

    // Display all hair color attributes over 10%.
    HairColor[] hairColors = face.FaceAttributes.Hair.HairColor;
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

## <a name="step6"></a>Passaggio 8: Visualizzare la descrizione del viso

Sostituire il metodo **FacePhoto_MouseMove** con il codice seguente:

```csharp
private void FacePhoto_MouseMove(object sender, MouseEventArgs e)
{
    // If the REST call has not completed, return from this method.
    if (faces == null)
        return;

    // Find the mouse position relative to the image.
    Point mouseXY = e.GetPosition(FacePhoto);

    ImageSource imageSource = FacePhoto.Source;
    BitmapSource bitmapSource = (BitmapSource)imageSource;

    // Scale adjustment between the actual size and displayed size.
    var scale = FacePhoto.ActualWidth / (bitmapSource.PixelWidth / resizeFactor);

    // Check if this mouse position is over a face rectangle.
    bool mouseOverFace = false;

    for (int i = 0; i < faces.Length; ++i)
    {
        FaceRectangle fr = faces[i].FaceRectangle;
        double left = fr.Left * scale;
        double top = fr.Top * scale;
        double width = fr.Width * scale;
        double height = fr.Height * scale;

        // Display the face description for this face if the mouse is over this face rectangle.
        if (mouseXY.X >= left && mouseXY.X <= left + width && mouseXY.Y >= top && mouseXY.Y <= top + height)
        {
            faceDescriptionStatusBar.Text = faceDescriptions[i];
            mouseOverFace = true;
            break;
        }
    }

    // If the mouse is not over a face rectangle.
    if (!mouseOverFace)
        faceDescriptionStatusBar.Text = "Place the mouse pointer over a face to see the face description.";
}
```

Eseguire questa applicazione e quindi cercare un'immagine che contenga un viso. Attendere alcuni secondi per consentire all'API cloud di rispondere. Verrà quindi visualizzato un rettangolo rosso intorno ai visi nell'immagine. Se si sposta il mouse sul rettangolo del viso, viene visualizzata la descrizione del viso nella barra di stato:

![GettingStartCSharpScreenshot](../Images/getting-started-cs-detected.png)

## <a name="summary"></a> Riepilogo

In questa esercitazione si è appreso il processo di base per l'uso dell'API Viso ed è stata creata un'applicazione per visualizzare i contrassegni dei visi nelle immagini. Per altre informazioni sui dettagli dell'API Viso, vedere le guide pratiche e le [informazioni di riferimento sull'API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).

## <a name="fullsource"></a> Codice sorgente completo

Di seguito viene presentato il codice sorgente completo per l'applicazione WPF per Windows.

MainWindow.xaml:

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

MainWindow.xaml.cs:

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Text;
using System.Threading.Tasks;
using System.Windows;
using System.Windows.Input;
using System.Windows.Media;
using System.Windows.Media.Imaging;
using Microsoft.ProjectOxford.Common.Contract;
using Microsoft.ProjectOxford.Face;
using Microsoft.ProjectOxford.Face.Contract;

namespace FaceTutorial
{
    public partial class MainWindow : Window
    {
        // Replace the first parameter with your valid subscription key.
        //
        // Replace or verify the region in the second parameter.
        //
        // You must use the same region in your REST API call as you used to obtain your subscription keys.
        // For example, if you obtained your subscription keys from the westus region, replace
        // "westcentralus" in the URI below with "westus".
        //
        // NOTE: Free trial subscription keys are generated in the westcentralus region, so if you are using
        // a free trial subscription key, you should not need to change this region.
        private readonly IFaceServiceClient faceServiceClient =
            new FaceServiceClient("<Subscription Key>", "https://westcentralus.api.cognitive.microsoft.com/face/v1.0");

        Face[] faces;                   // The list of detected faces.
        String[] faceDescriptions;      // The list of descriptions for the detected faces.
        double resizeFactor;            // The resize factor for the displayed image.

        public MainWindow()
        {
            InitializeComponent();
        }

        // Displays the image and calls Detect Faces.

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

            // Detect any faces in the image.
            Title = "Detecting...";
            faces = await UploadAndDetectFaces(filePath);
            Title = String.Format("Detection Finished. {0} face(s) detected", faces.Length);

            if (faces.Length > 0)
            {
                // Prepare to draw rectangles around the faces.
                DrawingVisual visual = new DrawingVisual();
                DrawingContext drawingContext = visual.RenderOpen();
                drawingContext.DrawImage(bitmapSource,
                    new Rect(0, 0, bitmapSource.Width, bitmapSource.Height));
                double dpi = bitmapSource.DpiX;
                resizeFactor = 96 / dpi;
                faceDescriptions = new String[faces.Length];

                for (int i = 0; i < faces.Length; ++i)
                {
                    Face face = faces[i];

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
                faceDescriptionStatusBar.Text = "Place the mouse pointer over a face to see the face description.";
            }
        }

        // Displays the face description when the mouse is over a face rectangle.

        private void FacePhoto_MouseMove(object sender, MouseEventArgs e)
        {
            // If the REST call has not completed, return from this method.
            if (faces == null)
                return;

            // Find the mouse position relative to the image.
            Point mouseXY = e.GetPosition(FacePhoto);

            ImageSource imageSource = FacePhoto.Source;
            BitmapSource bitmapSource = (BitmapSource)imageSource;

            // Scale adjustment between the actual size and displayed size.
            var scale = FacePhoto.ActualWidth / (bitmapSource.PixelWidth / resizeFactor);

            // Check if this mouse position is over a face rectangle.
            bool mouseOverFace = false;

            for (int i = 0; i < faces.Length; ++i)
            {
                FaceRectangle fr = faces[i].FaceRectangle;
                double left = fr.Left * scale;
                double top = fr.Top * scale;
                double width = fr.Width * scale;
                double height = fr.Height * scale;

                // Display the face description for this face if the mouse is over this face rectangle.
                if (mouseXY.X >= left && mouseXY.X <= left + width && mouseXY.Y >= top && mouseXY.Y <= top + height)
                {
                    faceDescriptionStatusBar.Text = faceDescriptions[i];
                    mouseOverFace = true;
                    break;
                }
            }

            // If the mouse is not over a face rectangle.
            if (!mouseOverFace)
                faceDescriptionStatusBar.Text = "Place the mouse pointer over a face to see the face description.";
        }

        // Uploads the image file and calls Detect Faces.

        private async Task<Face[]> UploadAndDetectFaces(string imageFilePath)
        {
            // The list of Face attributes to return.
            IEnumerable<FaceAttributeType> faceAttributes =
                new FaceAttributeType[] { FaceAttributeType.Gender, FaceAttributeType.Age, FaceAttributeType.Smile, FaceAttributeType.Emotion, FaceAttributeType.Glasses, FaceAttributeType.Hair };

            // Call the Face API.
            try
            {
                using (Stream imageFileStream = File.OpenRead(imageFilePath))
                {
                    Face[] faces = await faceServiceClient.DetectAsync(imageFileStream, returnFaceId: true, returnFaceLandmarks: false, returnFaceAttributes: faceAttributes);
                    return faces;
                }
            }
            // Catch and display Face API errors.
            catch (FaceAPIException f)
            {
                MessageBox.Show(f.ErrorMessage, f.ErrorCode);
                return new Face[0];
            }
            // Catch and display all other errors.
            catch (Exception e)
            {
                MessageBox.Show(e.Message, "Error");
                return new Face[0];
            }
        }

        // Returns a string that describes the given face.

        private string FaceDescription(Face face)
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
            EmotionScores emotionScores = face.FaceAttributes.Emotion;
            if (emotionScores.Anger >= 0.1f) sb.Append(String.Format("anger {0:F1}%, ", emotionScores.Anger * 100));
            if (emotionScores.Contempt >= 0.1f) sb.Append(String.Format("contempt {0:F1}%, ", emotionScores.Contempt * 100));
            if (emotionScores.Disgust >= 0.1f) sb.Append(String.Format("disgust {0:F1}%, ", emotionScores.Disgust * 100));
            if (emotionScores.Fear >= 0.1f) sb.Append(String.Format("fear {0:F1}%, ", emotionScores.Fear * 100));
            if (emotionScores.Happiness >= 0.1f) sb.Append(String.Format("happiness {0:F1}%, ", emotionScores.Happiness * 100));
            if (emotionScores.Neutral >= 0.1f) sb.Append(String.Format("neutral {0:F1}%, ", emotionScores.Neutral * 100));
            if (emotionScores.Sadness >= 0.1f) sb.Append(String.Format("sadness {0:F1}%, ", emotionScores.Sadness * 100));
            if (emotionScores.Surprise >= 0.1f) sb.Append(String.Format("surprise {0:F1}%, ", emotionScores.Surprise * 100));

            // Add glasses.
            sb.Append(face.FaceAttributes.Glasses);
            sb.Append(", ");

            // Add hair.
            sb.Append("Hair: ");

            // Display baldness confidence if over 1%.
            if (face.FaceAttributes.Hair.Bald >= 0.01f)
                sb.Append(String.Format("bald {0:F1}% ", face.FaceAttributes.Hair.Bald * 100));

            // Display all hair color attributes over 10%.
            HairColor[] hairColors = face.FaceAttributes.Hair.HairColor;
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
    }
}
```

## <a name="related"></a> Passaggi successivi

- [Getting Started with Face API in Java for Android](FaceAPIinJavaForAndroidTutorial.md) (Introduzione all'API Viso in Java per Android)
- [Getting Started with Face API in Python](FaceAPIinPythonTutorial.md) (Introduzione all'API Viso in Python)
