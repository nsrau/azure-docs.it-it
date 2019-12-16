---
title: "Esercitazione: Rilevare e mostrare i dati sui visi in un'immagine usando .NET SDK"
titleSuffix: Azure Cognitive Services
description: In questa esercitazione verrà creata un'app Windows che usa la funzione API Viso per rilevare e incorniciare il volto in un'immagine.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: tutorial
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: a444294497b82f316e7407999f5203cd13878928
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74977965"
---
# <a name="tutorial-create-a-windows-presentation-framework-wpf-app-to-display-face-data-in-an-image"></a>Esercitazione: Creare un'app Windows Presentation Framework (WPF) per visualizzare i dati relativi ai visi in un'immagine

In questa esercitazione si imparerà a usare l'API Viso di Azure tramite il client .NET SDK per rilevare i visi in un'immagine e quindi presentare i dati nell'interfaccia utente. Verrà creata un'applicazione Windows Presentation Foundation (WPF) che rileva i volti, disegna una cornice intorno a ogni viso e visualizza una descrizione del viso nella barra di stato. 

Questa esercitazione illustra come:

> [!div class="checklist"]
> - Creare un'applicazione WPF
> - Installare la libreria client dell'API Viso
> - Usare la libreria client per rilevare i visi in un'immagine
> - Tracciare una cornice intorno a ogni viso rilevato
> - Visualizzare una descrizione del viso evidenziato sulla barra di stato

![Screenshot che mostra i visi rilevati racchiusi in rettangoli](../Images/getting-started-cs-detected.png)

Il codice di esempio completo è disponibile nell'archivio di [esempio Cognitive Face CSharp](https://github.com/Azure-Samples/Cognitive-Face-CSharp-sample) su GitHub.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare. 


## <a name="prerequisites"></a>Prerequisiti

- Una chiave di sottoscrizione API Viso. È possibile ottenere una chiave di sottoscrizione della versione di valutazione gratuita da [Prova Servizi cognitivi](https://azure.microsoft.com/try/cognitive-services/?api=face-api). In alternativa, seguire le istruzioni in [Creare un account Servizi cognitivi](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) per effettuare la sottoscrizione al servizio API Viso e ottenere la chiave. Quindi, [creare le variabili di ambiente](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) per la chiave e per la stringa dell'endpoint di servizio, denominate rispettivamente `FACE_SUBSCRIPTION_KEY` e `FACE_ENDPOINT`.
- Qualsiasi edizione di [Visual Studio 2015 o 2017](https://www.visualstudio.com/downloads/).

## <a name="create-the-visual-studio-project"></a>Creare il progetto di Visual Studio

Seguire questa procedura per creare un nuovo progetto di applicazione WPF.

1. In Visual Studio, aprire la finestra di dialogo Nuovo progetto. Espandere **Installato**, quindi **Visual C#** , quindi selezionare **App WPF (.NET Framework)** .
1. Assegnare all'applicazione il nome **FaceTutorial** e fare clic su **OK**.
1. Ottenere i pacchetti NuGet necessari. Fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni e scegliere **Gestisci pacchetti NuGet**, quindi trovare e installare il pacchetto seguente:
    - [Microsoft.Azure.CognitiveServices.Vision.Face 2.2.0-preview](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.2.0-preview)

## <a name="add-the-initial-code"></a>Aggiungere il codice iniziale

In questa sezione si aggiungerà il framework di base dell'app senza le funzionalità specifiche del viso.

### <a name="create-the-ui"></a>Creare l'interfaccia utente

Aprire *MainWindow.xaml* e sostituire i contenuti con il codice seguente, che crea la finestra dell'interfaccia utente. I metodi `FacePhoto_MouseMove` e `BrowseButton_Click` sono i gestori dell'evento che verranno definiti successivamente.

[!code-xaml[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml?name=snippet_xaml)]

### <a name="create-the-main-class"></a>Creare la classe principale

Aprire *MainWindow.xaml.cs* e aggiungere spazi dei nomi della libreria client, assieme agli altri spazi dei nomi necessari. 

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_using)]

Quindi, inserire il codice seguente nella classe **MainWindow**. Questo codice consente di creare un istanza di **FaceClient** usando l'endpoint e la chiave di sottoscrizione.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mainwindow_fields)]

Aggiungere quindi il costruttore **MainWindow**. Verifica la stringa dell'URL dell'endpoint e quindi la associa all'oggetto client.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mainwindow_constructor)]

Infine, aggiungere i metodi **BrowseButton_Click** e **FacePhoto_MouseMove** alla classe. Questi metodi corrispondono ai gestori dell'evento dichiarati in *MainWindow.xaml*. Il metodo **BrowseButton_Click** crea un metodo **OpenFileDialog**, che consente all'utente di selezionare un'immagine JPG. L'immagine viene quindi visualizzata nella finestra principale. Nei passaggi successivi verrà inserito il codice residuo per i metodi **BrowseButton_Click** e **FacePhoto_MouseMove**. Si noti anche il riferimento `faceList`, ossia un elenco di oggetti **DetectedFace**. È da questo riferimento che l'app archivia e chiama i dati effettivi del viso.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_browsebuttonclick_start)]

<!-- [!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_browsebuttonclick_end)] -->

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mousemove_start)]

<!-- [!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mousemove_end)] -->

### <a name="try-the-app"></a>Provare l'app

Scegliere **Avvia** dal menu per testare l'app. All'apertura della finestra dell'app fare clic su **Sfoglia** nell'angolo inferiore sinistro. Viene visualizzata una finestra di dialogo **Apri file**. Selezionare un'immagine dal file system e verificare che venga visualizzata nella finestra. Quindi, chiudere l'app e andare al passaggio successivo.

![Screenshot che mostra un'immagine non modificata dei visi](../Images/getting-started-cs-ui.png)

## <a name="upload-image-and-detect-faces"></a>Caricare l'immagine e rilevare i visi

L'app rileverà i visi chiamando il metodo **FaceClient.Face.DetectWithStreamAsync**, che esegue il wrapping dell'API REST [Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) e per caricare un'immagine locale.

Inserire il metodo seguente nella classe **MainWindow** sotto il metodo **FacePhoto_MouseMove**. Questo metodo consente di definire un elenco di attributi dei visi da recuperare e legge il file di immagine inviato in un elemento **Stream**. Quindi, passa entrambi gli oggetti alla chiamata al metodo **DetectWithStreamAsync**.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_uploaddetect)]

## <a name="draw-rectangles-around-faces"></a>Disegnare rettangoli attorno ai visi

Quindi, aggiungere il codice per tracciare un rettangolo intorno a ogni viso rilevato nell'immagine. Nella classe **MainWindow** inserire il codice seguente alla fine dell'evento **BrowseButton_Click**, dopo la riga `FacePhoto.Source = bitmapSource`. Questo codice consente di popolare un elenco di visi rilevati dalla chiamata a **UploadAndDetectFaces**. Viene quindi tracciato un rettangolo intorno a ogni viso e l'immagine modificata viene visualizzata nella finestra principale.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_browsebuttonclick_mid)]

## <a name="describe-the-faces"></a>Descrivere i visi

Aggiungere il metodo seguente alla classe **MainWindow** sotto il metodo **UploadAndDetectFaces**. Questo metodo consente di convertire gli attributi del viso recuperati in una stringa che descrive il viso.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_facedesc)]

## <a name="display-the-face-description"></a>Visualizzare la descrizione del viso

Aggiungere il codice seguente al metodo **FacePhoto_MouseMove**. Questo gestore dell'evento mostra la stringa di descrizione del viso in `faceDescriptionStatusBar` quando il cursore si sofferma sopra il rettangolo del viso rilevato.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mousemove_mid)]

## <a name="run-the-app"></a>Esecuzione dell'app

Eseguire l'applicazione e quindi cercare un'immagine che contenga un viso. Attendere qualche secondo per consentire al servizio Viso di rispondere. Verrà visualizzato un rettangolo rosso intorno a ciascun viso nell'immagine. Se si sposta il mouse sul rettangolo del viso, viene visualizzata la descrizione del viso nella barra di stato.

![Screenshot che mostra i visi rilevati racchiusi in rettangoli](../Images/getting-started-cs-detected.png)


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso il processo di base per l'uso dell'SDK .NET del servizio Viso ed è stata creata un'applicazione per rilevare e incorniciare i visi in un'immagine. In seguito, si apprenderanno altre informazioni sul rilevamento volto.

> [!div class="nextstepaction"]
> [Come rilevare visi nelle immagini](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)
