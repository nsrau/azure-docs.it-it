---
title: "Guida introduttiva: Riconoscimento vocale in C# in un'app UWP con Speech SDK di Servizi cognitivi"
titleSuffix: Microsoft Cognitive Services
description: Informazioni sul riconoscimento vocale in un'applicazione piattaforma UWP con Speech SDK di Servizi cognitivi
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: d1245b07ac0de680c13542b9af86b25bdf517c21
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2018
ms.locfileid: "39576135"
---
# <a name="quickstart-recognize-speech-in-a-uwp-app-using-the-speech-sdk"></a>Avvio rapido: Riconoscimento vocale in un'applicazione piattaforma UWP con Speech SDK

[!include[Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Questo articolo spiega come creare un'applicazione piattaforma UWP (Universal Windows Platform) usando Speech SDK di Servizi cognitivi per trascrivere il riconoscimento vocale.
L'applicazione si basa sul [pacchetto NuGet Speech SDK di Servizi cognitivi Microsoft](https://aka.ms/csspeech/nuget) e su Microsoft Visual Studio 2017.

> [!NOTE]
> La piattaforma UWP (Universal Windows Platform) consente di sviluppare applicazioni eseguibili su qualsiasi dispositivo che supporta Windows 10, inclusi PC, Xbox, Surface Hub e altri dispositivi. Le applicazioni che usano Speech SDK non superano ancora il Kit di certificazione app di Windows (WACK). È possibile effettuare il trasferimento locale delle applicazioni, ma attualmente potrebbero non venire inviate a Windows Store.

## <a name="prerequisites"></a>Prerequisiti

* Una chiave di sottoscrizione per il servizio di riconoscimento vocale. Vedere [Provare gratuitamente il Servizio di riconoscimento vocale](get-started.md).
* Un PC Windows (Windows 10 Fall Creators Update o versione successiva) con un microfono funzionante.
* [Microsoft Visual Studio 2017](https://www.visualstudio.com/), Community Edition o versione successiva.
* Il carico di lavoro dello **sviluppo di Universal Windows Platform** in Visual Studio. È possibile abilitarlo in **Tools** \> **Get Tools and Features** (Strumenti>Ottieni strumenti e funzionalità).

  ![Abilitare lo sviluppo di app per la piattaforma UWP (Universal Windows Platform)](media/sdk/vs-enable-uwp-workload.png)

## <a name="create-a-visual-studio-project"></a>Creare un progetto di Visual Studio

1. In Visual Studio 2017, creare una nuova applicazione Visual C# Windows Universal vuota. Nella finestra di dialogo **Nuovo progetto**, nel riquadro a sinistra, espandere **Installato** \> **Visual C#** \> **Windows Universal** e quindi selezionare **App vuota (Universal Windows)**. Per il nome del progetto, immettere *helloworld*.

    ![](media/sdk/qs-csharp-uwp-01-new-blank-app.png)

1. Nella finestra **Nuovo progetto della piattaforma UWP (Universal Windows Platform)** visualizzata scegliere **Windows 10 Fall Creators Update (10.0; Build 16299)** come **Versione minima** e questa o qualsiasi versione successiva come **Versione di destinazione**, quindi fare clic su **OK**.

    ![](media/sdk/qs-csharp-uwp-02-new-uwp-project.png)

1. Se si esegue un'installazione di Windows a 64 bit, è possibile passare dalla piattaforma build in uso a `x64`.

   ![Passare dalla piattaforma build a x64](media/sdk/qs-csharp-uwp-03-switch-to-x64.png)

   > [!NOTE]
   > Al momento, Speech SDK supporta-processori compatibili Intel ma non ARM.

1. Installare e fare riferimento al [pacchetto Speech SDK NuGet](https://aka.ms/csspeech/nuget). In Esplora soluzioni fare clic con il pulsante destro del mouse sulla soluzione e scegliere **Gestisci pacchetti NuGet per la soluzione**.

    ![Scegliere Gestisci pacchetti NuGet per la soluzione](media/sdk/qs-csharp-uwp-04-manage-nuget-packages.png)

1. Nell'angolo superiore a destra, nel campo **Origine pacchetto**, selezionare **Nuget.org**. Cercare e installare il pacchetto `Microsoft.CognitiveServices.Speech` e installarlo nel progetto **helloworld**.

    ![Installare il pacchetto Microsoft.CognitiveServices.Speech NuGet](media/sdk/qs-csharp-uwp-05-nuget-install-0.5.0.png "Installare pacchetto NuGet")

1. Accettare la licenza visualizzata.

    ![Accettare le condizioni di licenza](media/sdk/qs-csharp-uwp-06-nuget-license.png "Accettare le condizioni di licenza")

1. Nella console di Gestione pacchetti viene visualizzata la riga di output seguente.

   ```text
   Successfully installed 'Microsoft.CognitiveServices.Speech 0.5.0' to helloworld
   ```

## <a name="add-the-sample-code"></a>Aggiungere il codice di esempio

1. In Esplora soluzioni, fare doppio clic su **Package.appxmanifest** per modificare il manifesto dell'applicazione.
   Selezionare la scheda **Funzionalità**, selezionare la casella di controllo per la funzionalità **Microfono** e salvare le modifiche.

   ![](media/sdk/qs-csharp-uwp-07-capabilities.png)

1. Modificare l'interfaccia utente dell'applicazione facendo doppio clic su `MainPage.xaml` in Esplora soluzioni. 

    Nella visualizzazione della finestra di progettazione XAML inserire il frammento di codice XAML seguente nel tag Grid (tra `<Grid>` e `</Grid>`).

   [!code-xml[UI elements](~/samples-cognitive-services-speech-sdk/quickstart/csharp-uwp/helloworld/MainPage.xaml#StackPanel)]

1. Modificare il code-behind XAML facendo doppio clic su `MainPage.xaml.cs` in Esplora soluzioni (raggruppato nell'elemento `MainPage.xaml`).
   Sostituire tutto il codice in questo file con quanto segue.

   [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-uwp/helloworld/MainPage.xaml.cs#code)]

1. Nel gestore `SpeechRecognitionFromMicrophone_ButtonClicked` sostituire la stringa `YourSubscriptionKey` con la chiave di sottoscrizione.

1. Nel gestore `SpeechRecognitionFromMicrophone_ButtonClicked` sostituire la stringa `YourServiceRegion`con la [regione](regions.md) associata alla sottoscrizione (ad esempio, `westus` per la sottoscrizione di valutazione gratuita).

1. Salvare tutte le modifiche apportate al progetto.

## <a name="build-and-run-the-sample"></a>Compilare ed eseguire l'esempio

1. Compilare l'applicazione. Nella barra dei menu selezionare **Compila** > **Compila soluzione**. Il codice dovrebbe ora risultare compilato senza errori.

    ![Compilazione riuscita](media/sdk/qs-csharp-uwp-08-build.png "Compilazione riuscita")

1. Avviare l’applicazione. Nella barra dei menu, selezionare **Debug** > **Avvia debug** o premere **F5**.

    ![Avviare l'app durante il debug](media/sdk/qs-csharp-uwp-09-start-debugging.png "Avviare l'app durante il debug")

1. Viene visualizzata una finestra di interfaccia utente grafica. Prima di tutto fare clic sul pulsante **Attiva microfono** e confermare la richiesta di autorizzazione che viene visualizzata.

    ![Avviare l'app durante il debug](media/sdk/qs-csharp-uwp-10-access-prompt.png "Avviare l'app durante il debug")

1. Scegliere **Speech recognition with microphone input** (Riconoscimento vocale con l'input del microfono) e pronunciare una breve frase nel microfono del dispositivo. Il testo riconosciuto viene visualizzato nella finestra.

    ![](media/sdk/qs-csharp-uwp-11-ui-result.png)

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Ricercare questo esempio nella cartella `quickstart/csharp-uwp`.

## <a name="next-steps"></a>Passaggi successivi

- [Traduzione vocale](how-to-translate-speech-csharp.md)
- [Personalizzare modelli acustici](how-to-customize-acoustic-models.md)
- [Personalizzare modelli linguistici](how-to-customize-language-model.md)
