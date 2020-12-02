---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 10/15/2020
ms.author: erhopf
ms.openlocfilehash: 3f1a24db459cabd65d9ce17b89105c3b9ab8abb7
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96188320"
---
1. Avviare Visual Studio 2019.

1. Assicurarsi che il carico di lavoro dello **sviluppo multipiattaforma .NET** sia disponibile. Scegliere **Strumenti** > **Ottieni strumenti e funzionalità** dalla barra dei menu di Visual Studio per aprire il programma di installazione di Visual Studio. Se questo carico di lavoro è già attivato, chiudere la finestra di dialogo.

   ![Screenshot dell'eseguibile di Visual Studio, con evidenziata la scheda dei carichi di lavoro](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-net-core-workload.png)

   In caso contrario, selezionare la casella accanto a **sviluppo multipiattaforma con .NET** e selezionare **Modifica** nell'angolo inferiore destro della finestra di dialogo. L'installazione delle nuove funzionalità richiede qualche secondo.

1. Creare una nuova applicazione console Visual C# .NET Core. Nella finestra di dialogo **Nuovo progetto**, dal riquadro sinistro espandere **Installato** > **Visual C#**  >  **.NET Core**. Selezionare **App console (.NET Core)** . Per il nome del progetto, immettere *helloworld*.

   ![Screenshot della finestra di dialogo Nuovo progetto](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-01-new-console-app.png "Creare un'app console in Visual C# (.NET Core)")

1. Installare e fare riferimento al [pacchetto Speech SDK NuGet](https://aka.ms/csspeech/nuget). In Esplora soluzioni fare clic con il pulsante destro del mouse sulla soluzione e scegliere **Gestisci pacchetti NuGet per la soluzione**.

   ![Screenshot di Esplora soluzioni, con l'opzione Gestisci pacchetti NuGet per la soluzione selezionata](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-02-manage-nuget-packages.png "Gestisci pacchetti NuGet per la soluzione")

1. Nell'angolo superiore a destra, nel campo **Origine pacchetto**, selezionare **nuget.org**. Cercare il pacchetto `Microsoft.CognitiveServices.Speech` e installarlo nel progetto **helloworld**.

   ![Screenshot della finestra di dialogo "Gestisci i pacchetti per la soluzione".](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-03-nuget-install-1.0.0.png "Installare il pacchetto NuGet")

1. Accettare la licenza visualizzata per iniziare l'installazione del pacchetto NuGet.

   ![Screenshot della finestra di dialogo dell'accettazione della licenza](../articles/cognitive-services/Speech-Service/media/sdk/qs-csharp-dotnetcore-windows-04-nuget-license.png "Accettare la licenza")

Dopo aver installato il pacchetto, viene visualizzato un messaggio di conferma nella console di Gestione pacchetti.
