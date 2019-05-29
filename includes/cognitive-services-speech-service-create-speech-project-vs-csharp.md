---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 09/13/2018
ms.author: wolfma
ms.openlocfilehash: afe6f1493c7fa8272c67f23d6708ad6e4eea9381
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66145494"
---
1. Avviare Visual Studio 2017.

1. Nella barra dei menu di Visual Studio selezionare **Strumenti > Scarica gli strumenti** e assicurarsi che il carico di lavoro **Sviluppo per desktop .NET** sia disponibile. Se il carico di lavoro non è stato installato, contrassegnare la casella di controllo e quindi fare clic su **Modifica** per avviare l'installazione. Il download e l'installazione può richiedere alcuni minuti.

   Se la casella di controllo accanto a **Sviluppo per desktop .NET** è selezionata, è ora possibile chiudere la finestra di dialogo.

   ![Abilitare lo sviluppo per desktop .NET](~/articles/cognitive-services/speech-service/media/sdk/vs-enable-net-desktop-workload.png)

1. A questo punto, creiamo un progetto. Nella barra dei menu selezionare **File > Nuovo > Progetto**. Quando viene visualizzata la finestra di dialogo, nel pannello sinistro espandere queste sezioni **Installati > Visual C# > Windows Desktop** e selezionare **App console (.NET Framework)** . Assegnare a questo progetto il nome *helloworld*.

    ![Creare App console Visual C# (.NET Framework)](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-01-new-console-app.png "Creare App console Visual C# (.NET Framework)")

1. Ora che il progetto è configurato, è necessario installare il [pacchetto NuGet di SDK Speech](https://aka.ms/csspeech/nuget) e farvi riferimento nel codice. Individuare Esplora soluzioni e fare clic con il pulsante destro del mouse su helloworld. Nel menu selezionare **Gestisci pacchetti NuGet**.

   ![Fare clic con il pulsante destro del mouse su Gestisci pacchetti NuGet per la soluzione](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-02-manage-nuget-packages.png "Gestisci pacchetti NuGet per la soluzione")

1. Nell'angolo in alto a destra di Gestione pacchetti NuGet individuare l'elenco a discesa **Origine dei pacchetti** e assicurarsi che **nuget.org** sia selezionato. Selezionare quindi **Sfoglia**, cercare il pacchetto `Microsoft.CognitiveServices.Speech` e installare la versione stabile più recente.

   ![Installare il pacchetto Microsoft.CognitiveServices.Speech NuGet](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-03-nuget-install-1.0.0.png "Installare pacchetto NuGet")

1. Accettare tutti i contratti e le licenze per avviare l'installazione.

   ![Accettare le condizioni di licenza](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-04-nuget-license.png "Accettare le condizioni di licenza")

    Dopo aver installato il pacchetto, viene visualizzato un messaggio di conferma nella console di Gestione pacchetti.

1. Il passaggio successivo consiste nel creare una configurazione della piattaforma che corrisponde all'architettura del computer in uso per compilare ed eseguire l'applicazione console. Nella barra dei menu selezionare **Compila** > **Gestione configurazione**.

    ![Avviare Gestione configurazione](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-05-cfg-manager-click.png "Avviare Gestione configurazione")

1. Nella finestra di dialogo **Gestione configurazione** individuare l'elenco a discesa **Piattaforma soluzione attiva** e selezionare **Nuova**.

    ![Aggiungere una nuova piattaforma nella finestra di gestione configurazione](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-06-cfg-manager-new.png "Aggiungere una nuova piattaforma nella finestra di gestione configurazione")

1. Se si esegue Windows a 64 bit, quando viene richiesto di specificare un valore in **Digitare o selezionare la nuova piattaforma**, selezionare `x64`. Se si esegue Windows a 32 bit, selezionare `x86`. Al termine, fare clic su **OK**.

    ![In Windows a 64-bit, aggiungere una nuova piattaforma denominata "x64"](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-07-cfg-manager-add-x64.png "Aggiungere piattaforma x64")
