---
title: 'Avvio rapido: Riconoscere i contenuti vocali, C# (UWP) - Servizi di riconoscimento vocale'
titleSuffix: Azure Cognitive Services
description: In questo articolo viene creata un'applicazione della piattaforma UWP (Universal Windows Platform) C# usando Speech SDK di Servizi cognitivi. Trascrizione riconoscimento vocale in tempo reale dal microfono del dispositivo. L'applicazione si basa sul pacchetto NuGet Speech SDK e su Microsoft Visual Studio 2017.
services: cognitive-services
author: wolfma61
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 12/06/2018
ms.author: wolfma
ms.custom: seodec18
ms.openlocfilehash: 55988ef65e223c76a485c3cbec13626abf68d3b9
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2018
ms.locfileid: "53104630"
---
# <a name="quickstart-recognize-speech-in-a-uwp-app-by-using-the-speech-sdk"></a>Avvio rapido: Riconoscere i contenuti vocali in un'app UWP con Speech SDK

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

In questo articolo verrà creata un'applicazione piattaforma UWP (Universal Windows Platform) C# usando [Speech SDK](speech-sdk.md) di Servizi cognitivi. Trascrizione riconoscimento vocale in tempo reale dal microfono del dispositivo. L'applicazione si basa sul [pacchetto NuGet Speech SDK](https://aka.ms/csspeech/nuget) e su Microsoft Visual Studio 2017 (qualsiasi edizione).

> [!NOTE]
> La piattaforma UWP (Universal Windows Platform) consente di sviluppare applicazioni eseguibili su qualsiasi dispositivo che supporta Windows 10, inclusi PC, Xbox, Surface Hub e altri dispositivi.

## <a name="prerequisites"></a>Prerequisiti

È necessaria una chiave di sottoscrizione del Servizio di riconoscimento vocale per completare la guida introduttiva. È possibile ottenerne una gratuitamente. Consultare [Provare gratuitamente il Servizio di riconoscimento vocale](get-started.md) per informazioni dettagliate.

## <a name="create-a-visual-studio-project"></a>Creare un progetto di Visual Studio

1. Avviare Visual Studio 2017.

1. Assicurarsi che il carico di lavoro dello **sviluppo di Universal Windows Platform** sia disponibile. Scegliere **Strumenti** > **Ottieni strumenti e funzionalità** dalla barra dei menu di Visual Studio per aprire il programma di installazione di Visual Studio. Se questo carico di lavoro è già attivato, chiudere la finestra di dialogo.

    ![Screenshot di Visual Studio installer, con evidenziata la scheda dei carichi di lavoro](media/sdk/vs-enable-uwp-workload.png)

    In caso contrario, selezionare la casella accanto a **sviluppo multipiattaforma con .NET** e selezionare **Modifica** nell'angolo inferiore destro della finestra di dialogo. L'installazione delle nuove funzionalità richiede qualche secondo.

1. Creare un'app di Windows universale di Visual C# vuota. In primo luogo, scegliere **File** > **Nuovo** > **Progetto** dal menu. Nella finestra di dialogo **Nuovo progetto**, espandere **Installato** > **Visual C#** > **Windows universale** nel riquadro a sinistra. Selezionare quindi **App vuota (Windows universale)**. Per il nome del progetto, immettere *helloworld*.

    ![Screenshot della finestra di dialogo Nuovo progetto](media/sdk/qs-csharp-uwp-01-new-blank-app.png)

1. Speed SDK richiede che l'applicazione debba essere compilata per Windows 10 Fall Creators Update o versione successiva. Nella finestra **Nuovo progetto della piattaforma UWP (Universal Windows Platform)** visualizzata scegliere **Windows 10 Fall Creators Update (10.0; Build 16299)** come **Versione minima**. Nella casella **Versione di destinazione**, selezionare questa o qualsiasi versione successiva e quindi fare clic su **OK**.

    ![Screenshot della finestra Nuovo progetto della piattaforma UWP (Universal Windows Platform)](media/sdk/qs-csharp-uwp-02-new-uwp-project.png)

1. Se si esegue Windows a 64 bit, è possibile passare la piattaforma della build di `x64` utilizzando il menu a discesa sulla barra degli strumenti di Visual Studio. (Windows a 64 bit può eseguire applicazioni a 32 bit, pertanto è possibile lasciarlo impostato su `x86` se si preferisce.)

   ![Screenshot della barra degli strumenti di Visual Studio, con l'opzione x64 evidenziata](media/sdk/qs-csharp-uwp-03-switch-to-x64.png)

   > [!NOTE]
   > Al momento, Speech SDK supporta solo i processori compatibili Intel. ARM non è attualmente supportato.

1. Installare e fare riferimento al [pacchetto Speech SDK NuGet](https://aka.ms/csspeech/nuget). In Esplora soluzioni fare clic con il pulsante destro del mouse sulla soluzione e scegliere **Gestisci pacchetti NuGet per la soluzione**.

    ![Screenshot di Esplora soluzioni, con l'opzione Gestisci pacchetti NuGet per la soluzione selezionata](media/sdk/qs-csharp-uwp-04-manage-nuget-packages.png)

1. Nell'angolo superiore a destra, nel campo **Origine pacchetto**, selezionare **nuget.org**. Cercare il pacchetto `Microsoft.CognitiveServices.Speech` e installarlo nel progetto **helloworld**.

    ![Screenshot della finestra di dialogo Gestisci pacchetti per la soluzione](media/sdk/qs-csharp-uwp-05-nuget-install-1.0.0.png "Installare pacchetti NuGet")

1. Accettare la licenza visualizzata per iniziare l'installazione del pacchetto NuGet.

    ![Screenshot della finestra di dialogo dell'accettazione della licenza](media/sdk/qs-csharp-uwp-06-nuget-license.png "Accettare la licenza")

1. Nella console di Gestione pacchetti viene visualizzata la riga di output seguente.

   ```text
   Successfully installed 'Microsoft.CognitiveServices.Speech 1.1.0' to helloworld
   ```

1. Poiché l'applicazione usa il microfono per l'input vocale, aggiungere la funzionalità **Microfono** al progetto. In Esplora soluzioni, fare doppio clic su **Package.appxmanifest** per modificare il manifesto dell'applicazione. Passare quindi alla scheda **Funzionalità**, selezionare la casella di controllo per la funzionalità **Microfono** e salvare le modifiche.

   ![Screenshot del manifesto dell'applicazione di Visual Studio, con Funzionalità e Microfono evidenziati](media/sdk/qs-csharp-uwp-07-capabilities.png)


## <a name="add-sample-code"></a>Aggiungere codice di esempio

1. L'interfaccia utente dell'applicazione viene definita tramite XAML. Aprire `MainPage.xaml` in Esplora soluzioni. Nella visualizzazione della finestra di progettazione XAML inserire il frammento di codice XAML seguente nel tag Grid (tra `<Grid>` e `</Grid>`).

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp-uwp/helloworld/MainPage.xaml#StackPanel)]

1. Aprire il file di origine code-behind `MainPage.xaml.cs` (lo si trova raggruppato sotto `MainPage.xaml`). Sostituire tutto il codice in questo file con quanto segue.

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-uwp/helloworld/MainPage.xaml.cs#code)]

1. Nel gestore `SpeechRecognitionFromMicrophone_ButtonClicked` di questo file, sostituire la stringa `YourSubscriptionKey` con la chiave di sottoscrizione.

1. Nel gestore `SpeechRecognitionFromMicrophone_ButtonClicked` sostituire la stringa `YourServiceRegion`con la [regione](regions.md) associata alla sottoscrizione (ad esempio, `westus` per la sottoscrizione di valutazione gratuita).

1. Salvare tutte le modifiche apportate al progetto.

## <a name="build-and-run-the-app"></a>Compilare ed eseguire l'app

1. Compilare l'applicazione. Nella barra dei menu selezionare **Compila** > **Compila soluzione**. Il codice dovrebbe ora risultare compilato senza errori.

    ![Screenshot dell'applicazione Visual Studio, con l'opzione Compila soluzione evidenziata](media/sdk/qs-csharp-uwp-08-build.png "Compilazione completata")

1. Avviare l’applicazione. Nella barra dei menu, selezionare **Debug** > **Avvia debug** o premere **F5**.

    ![Screenshot dell'applicazione Visual Studio, con l'opzione Avvio del debug evidenziata](media/sdk/qs-csharp-uwp-09-start-debugging.png "Avviare l’applicazione per eseguire il debug")

1. Viene visualizzata una finestra. Selezionare **Attiva microfono** e confermare la richiesta di autorizzazione che viene visualizzata.

    ![Screenshot della richiesta di autorizzazione](media/sdk/qs-csharp-uwp-10-access-prompt.png "Avviare l'applicazione per eseguire il debug")

1. Selezionare **Speech recognition with microphone input** (Riconoscimento vocale con l'input del microfono) e pronunciare una frase o espressione nel microfono del dispositivo. Il riconoscimento vocale viene trasmesso al Servizio di riconoscimento vocale e trascritto in formato testo, che appare nella finestra.

    ![Screenshot dell'interfaccia utente di riconoscimento vocale](media/sdk/qs-csharp-uwp-11-ui-result.png)

[!INCLUDE [Download this sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Ricercare questo esempio nella cartella `quickstart/csharp-uwp`.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Riconoscere le finalità dai contenuti vocali con Speech SDK per C#](how-to-recognize-intents-from-speech-csharp.md)

## <a name="see-also"></a>Vedere anche 

- [Traduzione vocale](how-to-translate-speech-csharp.md)
- [Personalizzare modelli acustici](how-to-customize-acoustic-models.md)
- [Personalizzare modelli linguistici](how-to-customize-language-model.md)
