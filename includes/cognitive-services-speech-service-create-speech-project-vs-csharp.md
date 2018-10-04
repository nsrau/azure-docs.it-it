---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 09/13/2018
ms.author: wolfma
ms.openlocfilehash: 8ff8e8341b6f39f66c2cc8014d41d3d3a2918d2b
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/28/2018
ms.locfileid: "47454473"
---
1. Avviare Visual Studio 2017.
 
1. Assicurarsi che il carico di lavoro **ambiente Desktop .NET** sia disponibile. Scegliere **Strumenti** \> **Ottieni strumenti e funzionalità** dalla barra dei menu di Visual Studio per aprire il programma di installazione di Visual Studio. Se questo carico di lavoro è già attivato, chiudere la finestra di dialogo. 

    In caso contrario, selezionare la casella di controllo accanto a **Sviluppo per desktop .NET**, quindi fare clic sul pulsante **Modifica** nell'angolo inferiore destro della finestra di dialogo. L'installazione delle nuove funzionalità richiede qualche secondo.

    ![Abilitare lo sviluppo per desktop .NET](~/articles/cognitive-services/speech-service/media/sdk/vs-enable-net-desktop-workload.png)

1. Creare una nuova applicazione console in Visual C#. Nella finestra di dialogo **Nuovo progetto**, nel riquadro a sinistra, espandere **Installato** \> **Visual C#** \> **Windows Desktop** e quindi scegliere **App Console (.NET Framework)**. Per il nome del progetto, immettere *helloworld*.

    ![Creare App console Visual C# (.NET Framework)](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-01-new-console-app.png "Creare App console Visual C# (.NET Framework)")

1. Installare e fare riferimento al [pacchetto Speech SDK NuGet](https://aka.ms/csspeech/nuget). In Esplora soluzioni fare clic con il pulsante destro del mouse sulla soluzione e scegliere **Gestisci pacchetti NuGet per la soluzione**.

    ![Fare clic con il pulsante destro del mouse su Gestisci pacchetti NuGet per la soluzione](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-02-manage-nuget-packages.png "Gestisci pacchetti NuGet per la soluzione")

1. Nell'angolo superiore a destra, nel campo **Origine pacchetto**, selezionare **nuget.org**. Cercare il pacchetto `Microsoft.CognitiveServices.Speech` e installarlo nel progetto **helloworld**.

    ![Installare il pacchetto Microsoft.CognitiveServices.Speech NuGet](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-03-nuget-install-1.0.0.png "Installare pacchetto NuGet")

1. Accettare la licenza visualizzata per iniziare l'installazione del pacchetto NuGet.

    ![Accettare le condizioni di licenza](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-04-nuget-license.png "Accettare le condizioni di licenza")

    Dopo aver installato il pacchetto, un messaggio di conferma viene visualizzato nella console di Gestione pacchetti.

1. Creare una configurazione di piattaforma corrispondente all'architettura del PC utilizzando Gestione configurazione. Selezionare **Genera** > **Gestione configurazione**.

    ![Avviare Gestione configurazione](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-05-cfg-manager-click.png "Avviare Gestione configurazione")

1. Nella finestra di dialogo **Gestione configurazione**, aggiungere una nuova piattaforma. Dall'elenco a discesa **Piattaforma soluzione attiva**, selezionare **Nuova**.

    ![Aggiungere una nuova piattaforma nella finestra di gestione configurazione](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-06-cfg-manager-new.png "Aggiungere una nuova piattaforma nella finestra di gestione configurazione")

1. Se si esegue Windows a 64 bit, creare una nuova configurazione della piattaforma denominata `x64`. Se si esegue Windows a 32 bit, creare una nuova configurazione della piattaforma denominata `x86`.

    ![In Windows a 64-bit, aggiungere una nuova piattaforma denominata "x64"](~/articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-07-cfg-manager-add-x64.png "Aggiungere piattaforma x64")


