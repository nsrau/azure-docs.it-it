---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 2/20/2019
ms.author: erhopf
ms.openlocfilehash: b5c61ecb74c0fcdacc6024a74565f05b036e2ac6
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65081250"
---
1. Avviare Visual Studio 2017.

1. Assicurarsi che il carico di lavoro dello **sviluppo di Universal Windows Platform** sia disponibile. Scegliere **Strumenti** > **Ottieni strumenti e funzionalità** dalla barra dei menu di Visual Studio per aprire il programma di installazione di Visual Studio. Se questo carico di lavoro è già attivato, chiudere la finestra di dialogo.

    ![Screenshot di Visual Studio installer, con evidenziata la scheda dei carichi di lavoro](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-uwp-workload.png)

    In caso contrario, selezionare la casella accanto a **sviluppo multipiattaforma con .NET** e selezionare **Modifica** nell'angolo inferiore destro della finestra di dialogo. L'installazione delle nuove funzionalità richiede qualche secondo.

1. Creare un'app di Windows universale di Visual C# vuota. In primo luogo, scegliere **File** > **Nuovo** > **Progetto** dal menu. Nella finestra di dialogo **Nuovo progetto**, espandere **Installato** > **Visual C#** > **Windows universale** nel riquadro a sinistra. Selezionare quindi **App vuota (Windows universale)**. Per il nome del progetto, immettere *helloworld*.

    ![Screenshot della finestra di dialogo Nuovo progetto](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-01-new-blank-app.png)

1. Speed SDK richiede che l'applicazione debba essere compilata per Windows 10 Fall Creators Update o versione successiva. Nella finestra **Nuovo progetto della piattaforma UWP (Universal Windows Platform)** visualizzata scegliere **Windows 10 Fall Creators Update (10.0; Build 16299)** come **Versione minima**. Nella casella **Versione di destinazione**, selezionare questa o qualsiasi versione successiva e quindi fare clic su **OK**.

    ![Screenshot della finestra Nuovo progetto della piattaforma UWP (Universal Windows Platform)](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-02-new-uwp-project.png)

1. Se si esegue Windows a 64 bit, è possibile passare la piattaforma della build di `x64` utilizzando il menu a discesa sulla barra degli strumenti di Visual Studio. (Windows a 64 bit può eseguire applicazioni a 32 bit, pertanto è possibile lasciarlo impostato su `x86` se si preferisce.)

   ![Screenshot della barra degli strumenti di Visual Studio, con l'opzione x64 evidenziata](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-03-switch-to-x64.png)

   > [!NOTE]
   > Al momento, Speech SDK supporta solo i processori compatibili Intel. ARM non è attualmente supportato.

1. Installare e fare riferimento al [pacchetto Speech SDK NuGet](https://aka.ms/csspeech/nuget). In Esplora soluzioni fare clic con il pulsante destro del mouse sulla soluzione e scegliere **Gestisci pacchetti NuGet per la soluzione**.

    ![Screenshot di Esplora soluzioni, con l'opzione Gestisci pacchetti NuGet per la soluzione selezionata](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-04-manage-nuget-packages.png)

1. Nell'angolo superiore a destra, nel campo **Origine pacchetto**, selezionare **nuget.org**. Cercare il pacchetto `Microsoft.CognitiveServices.Speech` e installarlo nel progetto **helloworld**.

    ![Screenshot della finestra di dialogo Gestisci pacchetti per la soluzione](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-05-nuget-install-1.0.0.png "Installare pacchetti NuGet")

1. Accettare la licenza visualizzata per iniziare l'installazione del pacchetto NuGet.

    ![Screenshot della finestra di dialogo dell'accettazione della licenza](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-06-nuget-license.png "Accettare la licenza")

1. Nella console di Gestione pacchetti viene visualizzata la riga di output seguente.

   ```text
   Successfully installed 'Microsoft.CognitiveServices.Speech 1.5.0' to helloworld
   ```

1. Poiché l'applicazione usa il microfono per l'input vocale, aggiungere la funzionalità **Microfono** al progetto. In Esplora soluzioni, fare doppio clic su **Package.appxmanifest** per modificare il manifesto dell'applicazione. Passare quindi alla scheda **Funzionalità**, selezionare la casella di controllo per la funzionalità **Microfono** e salvare le modifiche.

   ![Screenshot del manifesto dell'applicazione di Visual Studio, con Funzionalità e Microfono evidenziati](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-uwp-07-capabilities.png)
