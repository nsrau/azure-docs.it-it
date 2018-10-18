---
title: 'Guida introduttiva: Riconoscimento vocale in C# per .NET Core in Windows con Speech SDK di Servizi cognitivi'
titleSuffix: Microsoft Cognitive Services
description: Informazioni sul riconoscimento vocale in C# per .NET Core su Windows con Speech SDK di Servizi cognitivi
services: cognitive-services
author: wolfma61
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 10/12/2018
ms.author: wolfma
ms.openlocfilehash: 04cdfd487036e79eccbb4fb879902a12e077d104
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2018
ms.locfileid: "49341651"
---
# <a name="quickstart-recognize-speech-in-c-under-net-core-on-windows-by-using-the-speech-sdk"></a>Avvio rapido: Riconoscimento vocale in C# per .NET Core su Windows con Speech SDK

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

In questo articolo, si crea un'applicazione console C# per .NET Core su Windows usando [Speech SDK](speech-sdk.md) di Servizi cognitivi. Trascrizione riconoscimento vocale in tempo reale dal microfono del PC. L'applicazione si basa sul [pacchetto NuGet Speech SDK](https://aka.ms/csspeech/nuget) e su Microsoft Visual Studio 2017 (qualsiasi edizione).

> [!NOTE]
> .NET core è una multipiattaforma open source, multipiattaforma di .NET che implementa la specifica [.NET Standard](https://docs.microsoft.com/dotnet/standard/net-standard).

È necessaria una chiave di sottoscrizione del Servizio di riconoscimento vocale per completare la guida introduttiva. È possibile ottenerne una gratuitamente. Consultare [Provare gratuitamente il Servizio di riconoscimento vocale](get-started.md) per informazioni dettagliate.


## <a name="create-a-visual-studio-project"></a>Creare un progetto di Visual Studio

1. Avviare Visual Studio 2017.

1. Assicurarsi che il carico di lavoro dello **sviluppo multipiattaforma .NET** sia disponibile. Scegliere **Strumenti** > **Ottieni strumenti e funzionalità** dalla barra dei menu di Visual Studio per aprire il programma di installazione di Visual Studio. Se questo carico di lavoro è già attivato, chiudere la finestra di dialogo.

    ![Screenshot dell'eseguibile di Visual Studio, con evidenziata la scheda dei carichi di lavoro](media/sdk/vs-enable-net-core-workload.png)

    In caso contrario, selezionare la casella accanto a **sviluppo multipiattaforma con .NET** e selezionare **Modifica** nell'angolo inferiore destro della finestra di dialogo. L'installazione delle nuove funzionalità richiede qualche secondo.

1. Creare una nuova applicazione console Visual C# .NET Core. Nella finestra di dialogo **Nuovo progetto**, dal riquadro sinistro espandere **Installato** > **Visual C#** > **.NET Core**. Selezionare **App console (.NET Core)**. Per il nome del progetto, immettere *helloworld*.

    ![Screenshot della finestra di dialogo del nuovo progetto](media/sdk/qs-csharp-dotnetcore-windows-01-new-console-app.png "Creare console dell’app Visual C# (.NET Core)")

1. Installare e fare riferimento al [pacchetto Speech SDK NuGet](https://aka.ms/csspeech/nuget). In Esplora soluzioni fare clic con il pulsante destro del mouse sulla soluzione e scegliere **Gestisci pacchetti NuGet per la soluzione**.

    ![Screenshot di Esplora soluzioni, con l'opzione Gestisci pacchetti NuGet per la soluzione selezionata](media/sdk/qs-csharp-dotnetcore-windows-02-manage-nuget-packages.png "Gestisci pacchetti NuGet per la soluzione")

1. Nell'angolo superiore a destra, nel campo **Origine pacchetto**, selezionare **nuget.org**. Cercare il pacchetto `Microsoft.CognitiveServices.Speech` e installarlo nel progetto **helloworld**.

    ![Screenshot della finestra di dialogo Gestisci pacchetti per la soluzione](media/sdk/qs-csharp-dotnetcore-windows-03-nuget-install-1.0.0.png "Installare pacchetti NuGet")

1. Accettare la licenza visualizzata per iniziare l'installazione del pacchetto NuGet.

    ![Screenshot della finestra di dialogo dell'accettazione della licenza](media/sdk/qs-csharp-dotnetcore-windows-04-nuget-license.png "Accettare la licenza")

Dopo aver installato il pacchetto, viene visualizzato un messaggio di conferma nella console di Gestione pacchetti.


## <a name="add-sample-code"></a>Aggiungere codice di esempio

1. Aprire `Program.cs` e sostituire tutto il codice in questo file con quanto segue.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/csharp-dotnetcore/helloworld/Program.cs#code)]

1. Nello stesso file, sostituire la stringa `YourSubscriptionKey` con la chiave di sottoscrizione.

1. Inoltre, sostituire la stringa `YourServiceRegion` con l'[area](regions.md) associata alla sottoscrizione (ad esempio, `westus` per la sottoscrizione di valutazione gratuita).

1. Salvare le modifiche apportate al progetto.

## <a name="build-and-run-the-app"></a>Compilare ed eseguire l'app

1. Compilare l'applicazione. Nella barra dei menu scegliere **Compila** > **Compila soluzione**. Il codice dovrebbe risultare compilato senza errori.

    ![Screenshot dell'applicazione Visual Studio, con l'opzione Compila soluzione evidenziata](media/sdk/qs-csharp-dotnetcore-windows-05-build.png "Compilazione completata")

1. Avviare l’applicazione. Nella barra dei menu scegliere **Debug** > **Avvia debug** o premere **F5**.

    ![Screenshot dell'applicazione Visual Studio, con l'opzione Avvio del debug evidenziata](media/sdk/qs-csharp-dotnetcore-windows-06-start-debugging.png "Avviare l’applicazione per eseguire il debug")

1. Si apre una finestra della console che chiede di dire qualcosa. Pronunciare una frase o un'espressione in inglese. Il riconoscimento vocale viene trasmesso al Servizio di riconoscimento vocale e trascritto in formato testo, che appare nella stessa finestra.

    ![Risultato sulla console dopo un riconoscimento riuscito](media/sdk/qs-csharp-dotnetcore-windows-07-console-output.png "Risultato sulla console dopo un riconoscimento riuscito")

[!INCLUDE [Download this sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Ricercare questo esempio nella cartella `quickstart/csharp-dotnetcore-windows`.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Riconoscere le finalità dai contenuti vocali con Speech SDK per C#](how-to-recognize-intents-from-speech-csharp.md)

## <a name="see-also"></a>Vedere anche 

- [Traduzione vocale](how-to-translate-speech-csharp.md)
- [Personalizzare modelli acustici](how-to-customize-acoustic-models.md)
- [Personalizzare modelli linguistici](how-to-customize-language-model.md)
