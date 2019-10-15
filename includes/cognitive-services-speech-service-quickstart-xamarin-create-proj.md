---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 09/13/2019
ms.author: erhopf
ms.openlocfilehash: 0e4e67710c98b80dce2b0d55a86869625f3942d2
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/03/2019
ms.locfileid: "71837345"
---
Per creare un progetto di Visual Studio per lo sviluppo .NET di applicazioni per dispositivi mobili multipiattaforma con Xamarin, è necessario configurare le opzioni di sviluppo di Visual Studio, creare il progetto, selezionare l'architettura di destinazione e installare Speech SDK.

### <a name="set-up-visual-studio-development-options"></a>Configurare le opzioni di sviluppo di Visual Studio

Per iniziare, verificare di essere configurati correttamente in Visual Studio per lo sviluppo di applicazioni per dispositivi mobili multipiattaforma con .NET:

1. Aprire Visual Studio 2019.

1. Dalla barra dei menu di Visual Studio scegliere **Strumenti** > **Ottieni strumenti e funzionalità** per aprire il Programma di installazione di Visual Studio e visualizzare la finestra di dialogo **Modifica**.

   ![Scheda Carichi di lavoro, finestra di dialogo Modifica, Programma di installazione di Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-workload.png)

1. Nella scheda **Carichi di lavoro**, in **Windows**, trovare il carico di lavoro **Sviluppo di applicazioni per dispositivi mobili con .NET**. Se la casella di controllo accanto a tale carico di lavoro è già selezionata, chiudere la finestra di dialogo **Modifica** e andare al passaggio 5.

1. Selezionare la casella di controllo **Sviluppo di applicazioni per dispositivi mobili con .NET**, fare clic su **Modifica** e quindi nella finestra di dialogo **Prima di iniziare** fare clic su **Continua** per installare il carico di lavoro per lo sviluppo di applicazioni per dispositivi mobili con .NET. L'installazione della nuova funzionalità può richiedere qualche minuto.

1. Chiudere il Programma di installazione di Visual Studio.

### <a name="create-the-project"></a>Creare il progetto

1. Dalla barra dei menu di Visual Studio scegliere **File** > **Nuovo** > **Progetto** per visualizzare la finestra **Crea un nuovo progetto**.

   ![Crea un nuovo progetto - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-create-new-project.png)

1. Trovare e selezionare **App per dispositivi mobili (Xamarin Forms)** .

1. Fare clic su **Avanti** per visualizzare la schermata **Configura il nuovo progetto**. 

   ![Configura il nuovo progetto - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-configure-your-new-project.png)

1. In **Nome del progetto** immettere `helloworld`.

1. In **Percorso** individuare e selezionare oppure creare la cartella in cui salvare il progetto.

1. Fare clic su **Crea** per passare alla finestra **New Mobile App Xamarin Forms Project** (Nuovo progetto Xamarin Forms per app per dispositivi mobili).

   ![Finestra di dialogo Nuovo progetto della piattaforma UWP (Universal Windows Platform) - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-xamarin-new-xamarin-project.png)

1. Selezionare il modello **vuoto**

1. In **Piattaforma** selezionare le caselle per **Android**, **iOS** e **Windows (UWP)** .

1. Selezionare **OK**. Si tornerà all'IDE di Visual Studio e il nuovo progetto risulterà creato e visibile nel riquadro **Esplora soluzioni**.

   ![Progetto helloworld - Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-xamarin-helloworld.png)

A questo punto selezionare l'architettura della piattaforma di destinazione e il progetto di avvio. Nella barra degli strumenti di Visual Studio individuare la casella di riepilogo a discesa **Piattaforme soluzione**. Se non è visibile, scegliere **Visualizza** > **Barre degli strumenti** > **Standard** per visualizzare la barra degli strumenti contenente **Piattaforme soluzione**. Se è in esecuzione Windows a 64 bit, selezionare **x64** dalla casella di riepilogo a discesa. Windows a 64 bit può eseguire anche applicazioni a 32 bit, pertanto è possibile selezionare **x86** se si preferisce. Nell'elenco a discesa **Start-up Projects** (Progetti di avvio) impostare helloworld.UWP (Universal Windows).

### <a name="install-the-speech-sdk"></a>Installare Speech SDK

Installare il [pacchetto NuGet Speech SDK](https://aka.ms/csspeech/nuget) e fare riferimento a tale SDK nel progetto:

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla soluzione e scegliere **Gestire pacchetti NuGet per la soluzione** per passare alla finestra **NuGet - Soluzione**.

1. Selezionare **Esplora**.

   ![Screenshot della finestra di dialogo Gestisci pacchetti per la soluzione](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-nuget-solution-browse.png)

1. In **Origine dei pacchetti** selezionare **nuget.org**.

1. Nella casella **Cerca** immettere `Microsoft.CognitiveServices.Speech` e quindi fare clic su tale pacchetto dopo che viene visualizzato nei risultati della ricerca.

   ![Screenshot della finestra di dialogo Gestisci pacchetti per la soluzione](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-xamarin-nuget-install.png)
   > Nota: per la libreria iOS all'interno del NuGet Microsoft.CognitiveServices.Speech non è abilitato Bitcode. Se è necessaria la libreria abilitata per Bitcode per l'applicazione, usare in modo specifico il NuGet Microsoft.CognitiveServices.Speech.Xamarin.iOS per il progetto iOS.

1. Nel riquadro relativo allo stato del pacchetto, accanto ai risultati della ricerca, selezionare il progetto tutti i progetti. **helloworld**, **helloworld.Android**, **helloworld.iOS** e **helloworld.UWP**.

1. Selezionare **Installa**.

1. Nella finestra di dialogo **Anteprima modifiche** fare clic su **OK**.

1. Nella finestra di dialogo **Accettazione della licenza** visualizzare la licenza e quindi fare clic su **Accetto**, infine installare il riferimento al pacchetto Speech SDK a tutti i progetti. Al termine dell'installazione, è possibile che venga visualizzato il seguente avviso per helloworld.iOS. Si tratta di un problema noto che non dovrebbe influire sulle funzionalità dell'app.

> Non è stato possibile risolvere il riferimento "C:\Users\Default\.nuget\packages\microsoft.cognitiveservices.speech\1.7.0\build\Xamarin.iOS\libMicrosoft.CognitiveServices.Speech.core.a". Se questo riferimento è richiesto dal codice, è possibile che vengano generati errori di compilazione.
