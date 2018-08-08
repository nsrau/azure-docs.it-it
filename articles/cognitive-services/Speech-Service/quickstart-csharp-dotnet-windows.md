---
title: 'Guida introduttiva: Riconoscimento vocale in C# per .NET Framework in Windows con Speech SDK di Servizi cognitivi'
titleSuffix: Microsoft Cognitive Services
description: Informazioni sul riconoscimento vocale in C# per .NET Framework su Windows con Speech SDK di Servizi cognitivi
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: 587ae9f6452f85dee867047e47ccc272ee508b81
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/27/2018
ms.locfileid: "39325191"
---
# <a name="quickstart-recognize-speech-in-c-under-net-framework-on-windows-using-the-speech-sdk"></a>Avvio rapido: Riconoscimento vocale in C# per .NET Framework su Windows con Speech SDK | Microsoft Docs

[!include[Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Questo articolo spiega come creare un'applicazione console C# per .NET Framework in Windows usando Speech SDK di Servizi cognitivi per trascrivere il riconoscimento vocale.
L'applicazione si basa sul [pacchetto NuGet Speech SDK di Servizi cognitivi Microsoft](https://aka.ms/csspeech/nuget) e su Microsoft Visual Studio 2017.

## <a name="prerequisites"></a>Prerequisiti

* Una chiave di sottoscrizione per il servizio di riconoscimento vocale. Vedere [Provare gratuitamente il Servizio di riconoscimento vocale](get-started.md).
* Un PC Windows con un microfono funzionante.
* Visual Studio 2017, Community Edition o versione successiva.
* Carico di lavoro **Sviluppo per desktop .NET** in Visual Studio. È possibile abilitarlo in **Strumenti** \> **Scarica strumenti e funzionalità**.

  ![Abilitare lo sviluppo per desktop .NET](media/sdk/vs-enable-net-desktop-workload.png)

  ![Abilitare lo sviluppo multipiattaforma .NET Core](media/sdk/vs-enable-net-desktop-workload.png)

## <a name="create-a-visual-studio-project"></a>Creare un progetto di Visual Studio

1. Creare una nuova applicazione console C# in Visual Studio 2017. Nella finestra di dialogo **Nuovo progetto**, nel riquadro a sinistra, espandere **Installato** \> **Visual C#** \> **Windows Desktop** e quindi selezionare **App Console (.NET Framework)**. Per il nome del progetto, immettere *helloworld*.

    ![Creare App console Visual C# (.NET Framework)](media/sdk/qs-csharp-dotnet-windows-01-new-console-app.png "Creare App console Visual C# (.NET Framework)")

1. Installare e fare riferimento al [pacchetto Speech SDK NuGet](https://aka.ms/csspeech/nuget). In Esplora soluzioni fare clic con il pulsante destro del mouse sulla soluzione e scegliere **Gestisci pacchetti NuGet per la soluzione**.

    ![Fare clic con il pulsante destro del mouse su Gestisci pacchetti NuGet per la soluzione](media/sdk/qs-csharp-dotnet-windows-02-manage-nuget-packages.png "Gestisci pacchetti NuGet per la soluzione")

1. Nell'angolo superiore a destra, nel campo **Origine pacchetto**, selezionare **Nuget.org**. Cercare il pacchetto `Microsoft.CognitiveServices.Speech` e installarlo nel progetto **helloworld**.

    ![Installare il pacchetto Microsoft.CognitiveServices.Speech NuGet](media/sdk/qs-csharp-dotnet-windows-03-nuget-install-0.5.0.png "Installare pacchetto NuGet")

1. Accettare la licenza nella finestra di dialogo visualizzata.

    ![Accettare le condizioni di licenza](media/sdk/qs-csharp-dotnet-windows-04-nuget-license.png "Accettare le condizioni di licenza")

1. Nella console di Gestione pacchetti viene visualizzata la riga di output seguente.

   ```text
   Successfully installed 'Microsoft.CognitiveServices.Speech 0.5.0' to helloworld
   ```

## <a name="create-a-platform-configuration-matching-your-pc-architecture"></a>Creare una configurazione di piattaforma corrispondente all'architettura del PC

In questa sezione, si aggiunge una nuova piattaforma alla configurazione che corrisponde all'architettura del processore.

1. Avviare Gestione configurazione. Selezionare **Genera** > **Gestione configurazione**.

    ![Avviare Gestione configurazione](media/sdk/qs-csharp-dotnet-windows-05-cfg-manager-click.png "Avviare Gestione configurazione")

1. Nella finestra di dialogo **Gestione configurazione**, aggiungere una nuova piattaforma. Dall'elenco a discesa **Piattaforma soluzione attiva**, selezionare **Nuova**.

    ![Aggiungere una nuova piattaforma nella finestra di gestione configurazione](media/sdk/qs-csharp-dotnet-windows-06-cfg-manager-new.png "Aggiungere una nuova piattaforma nella finestra di gestione configurazione")

1. Se si esegue Windows a 64 bit, creare una nuova configurazione della piattaforma denominata `x64`. Se si esegue Windows a 32 bit, creare una nuova configurazione della piattaforma denominata `x86`. Nel presente articolo viene creata una configurazione della piattaforma `x64`.

    ![In Windows a 64-bit, aggiungere una nuova piattaforma denominata "x64"](media/sdk/qs-csharp-dotnet-windows-07-cfg-manager-add-x64.png "Aggiungere piattaforma x64")

## <a name="add-the-sample-code"></a>Aggiungere il codice di esempio

1. Aprire `Program.cs` nel progetto Visual Studio e sostituire tutto il codice in questo file come indicato di seguito.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnet-windows/helloworld/Program.cs#code)]

1. Sostituire la stringa `YourSubscriptionKey` con la chiave di sottoscrizione.

1. Sostituire la stringa `YourServiceRegion` con la [regione](regions.md) associata alla sottoscrizione (ad esempio, `westus` per la sottoscrizione di valutazione gratuita).

1. Salvare le modifiche apportate al progetto.

## <a name="build-and-run-the-sample"></a>Compilare ed eseguire l'esempio

1. Compilare l'applicazione. Nella barra dei menu selezionare **Compila** > **Compila soluzione**. Il codice dovrebbe ora risultare compilato senza errori.

    ![Compilazione riuscita](media/sdk/qs-csharp-dotnet-windows-08-build.png "Compilazione riuscita")

1. Avviare l’applicazione. Nella barra dei menu, selezionare **Debug** > **Avvia debug** o premere **F5**.

    ![Avviare l'app durante il debug](media/sdk/qs-csharp-dotnet-windows-09-start-debugging.png "Avviare l'app durante il debug")

1. Si apre una finestra della console che chiede di dire qualcosa (in inglese). Il testo riconosciuto viene quindi visualizzato nella stessa finestra.

    ![Risultato sulla console dopo il riconoscimento efficace](media/sdk/qs-csharp-dotnet-windows-10-console-output.png "Risultato sulla console dopo il riconoscimento efficace")

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Ricercare questo esempio nella cartella `quickstart/csharp-dotnet-windows`.

## <a name="next-steps"></a>Passaggi successivi

- [Traduzione vocale](how-to-translate-speech-csharp.md)
- [Personalizzare modelli acustici](how-to-customize-acoustic-models.md)
- [Personalizzare modelli linguistici](how-to-customize-language-model.md)
