---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: erhopf
ms.openlocfilehash: 6d20df031633df4642ce9fb5cbbc469fd7f0a5da
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96188329"
---
Per creare un progetto di Visual Studio per lo sviluppo di app per dispositivi mobili multipiattaforma con .NET usando Xamarin, è necessario:
- Configurare le opzioni di sviluppo di Visual Studio.
- Creare il progetto e selezionare l'architettura di destinazione. 
- Installare Speech SDK.

### <a name="set-up-visual-studio-development-options"></a>Configurare le opzioni di sviluppo di Visual Studio

Per iniziare, verificare che Visual Studio sia stato configurato correttamente per lo sviluppo di applicazioni per dispositivi mobili multipiattaforma con .NET:

1. Aprire Visual Studio 2019.

1. Dalla barra dei menu di Visual Studio scegliere **Strumenti** > **Ottieni strumenti e funzionalità** per aprire il Programma di installazione di Visual Studio e visualizzare la finestra di dialogo **Modifica**.

   ![Screenshot che mostra la scheda Carichi di lavoro nella finestra di dialogo Modifica del programma di installazione di Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-workload.png)

1. Nella scheda **Carichi di lavoro** in **Windows** trovare il carico di lavoro **Sviluppo di applicazioni per dispositivi mobili con .NET**. Se la casella di controllo accanto a tale carico di lavoro è già selezionata, chiudere la finestra di dialogo **Modifica** e andare al passaggio 5.

1. Selezionare la casella di controllo **Sviluppo di applicazioni per dispositivi mobili con .NET** e selezionare **Modifica**. Nella finestra di dialogo **Prima di iniziare** selezionare **Continua** per installare il carico di lavoro Sviluppo di applicazioni per dispositivi mobili con .NET. L'installazione della nuova funzionalità potrebbe richiedere qualche minuto.

1. Chiudere il Programma di installazione di Visual Studio.

### <a name="create-the-project"></a>Creare il progetto

1. Dalla barra dei menu di Visual Studio selezionare **File** > **Nuovo** > **Progetto** per visualizzare la finestra **Crea un nuovo progetto**.

   ![Screenshot che mostra come creare un nuovo progetto in Visual Studio.](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-create-new-project.png)

1. Trovare e selezionare **App per dispositivi mobili (Xamarin.Forms)** .

1. Fare clic su **Avanti** per visualizzare la schermata **Configura il nuovo progetto**.

   ![Screenshot che mostra come configurare il nuovo progetto in Visual Studio.](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-configure-your-new-project.png)

1. In **Nome progetto** immettere *helloworld*.

1. In **Percorso** individuare e selezionare oppure creare la cartella in cui salvare il progetto.

1. Fare clic su **Crea** per passare alla finestra **New Mobile App Xamarin Forms Project** (Nuovo progetto Xamarin Forms per app per dispositivi mobili).

   ![Screenshot che mostra la finestra di dialogo New Mobile App Xamarin Forms Project in Visual Studio.](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-xamarin-new-xamarin-project.png)

1. Selezionare il modello **Vuoto**.

1. In **Piattaforma** selezionare le caselle per **Android**, **iOS** e **Windows (UWP)** .

1. Selezionare **OK**. Si tornerà all'IDE di Visual Studio e il nuovo progetto risulterà creato e visibile nel riquadro **Esplora soluzioni**.

   ![Progetto helloworld - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-helloworld.png)

A questo punto selezionare l'architettura della piattaforma di destinazione e il progetto di avvio. Nella barra degli strumenti di Visual Studio individuare la casella di riepilogo a discesa **Piattaforme soluzione**. Se non è visibile, scegliere **Visualizza** > **Barre degli strumenti** > **Standard** per visualizzare la barra degli strumenti contenente **Piattaforme soluzione**. Se è in esecuzione Windows a 64 bit, selezionare **x64** dalla casella di riepilogo a discesa. Se si vuole, è possibile selezionare **x86** perché Windows a 64 bit può anche eseguire applicazioni a 32 bit. Nella casella di riepilogo a discesa **Progetti di avvio** impostare **helloworld.UWP (Windows universale)** .

### <a name="install-the-speech-sdk"></a>Installare Speech SDK

Installare il [pacchetto NuGet Speech SDK](https://aka.ms/csspeech/nuget) e fare riferimento a tale SDK nel progetto.

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul file della soluzione. Scegliere **Gestisci pacchetti NuGet per la soluzione** per passare alla finestra **NuGet - Soluzione**.

1. Selezionare **Sfoglia**.

   ![Screenshot della finestra di dialogo Gestisci i pacchetti per la soluzione durante l'installazione di Speech SDK.](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-nuget-solution-browse.png)

1. In **Origine dei pacchetti** selezionare nuget.org.

1. Nella **casella di ricerca** digitare *Microsoft.CognitiveServices.Speech*. Selezionare quindi il pacchetto dopo che viene visualizzato nei risultati della ricerca.

   ![Screenshot del pacchetto Microsoft.CognitiveServices.Speech in evidenza.](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-xamarin-nuget-install.png)

   > [!NOTE] 
   > Per la libreria iOS all'interno del pacchetto NuGet `Microsoft.CognitiveServices.Speech` il Bitcode non è abilitato. Se è necessario che la libreria Bitcode sia abilitata per l'applicazione, usare il pacchetto NuGet `Microsoft.CognitiveServices.Speech.Xamarin.iOS` specifico per il progetto iOS.

1. Nel riquadro Stato pacchetto accanto ai risultati della ricerca selezionare tutti i progetti: **helloworld**, **helloworld.Android**, **helloworld.iOS** e **helloworld.UWP**.

1. Selezionare **Installa**.

1. Nella finestra di dialogo **Anteprima modifiche** fare clic su **OK**.

1. Nella finestra di dialogo **Accettazione della licenza** visualizzare la licenza e quindi fare clic su **Accetto**. Installare il riferimento al pacchetto Speech SDK in tutti i progetti. Al termine dell'installazione, è possibile che venga visualizzato l'avviso per helloworld.iOS seguente. Si tratta di un problema noto che non dovrebbe influire sulle funzionalità dell'app.

   > Non è stato possibile risolvere il riferimento "C:\Users\Default\.nuget\packages\microsoft.cognitiveservices.speech\1.7.0\build\Xamarin.iOS\libMicrosoft.CognitiveServices.Speech.core.a". Se questo riferimento è richiesto dal codice, è possibile che vengano generati errori di compilazione.
