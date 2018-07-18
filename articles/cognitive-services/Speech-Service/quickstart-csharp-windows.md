---
title: 'Avvio rapido: Riconoscimento vocale in Cognitive Services Speech C# SDK per Windows | Microsoft Docs'
description: Informazioni su come attivare il riconoscimento vocale tramite il servizio di riconoscimento vocale C# SDK.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: wolfma
ms.openlocfilehash: 64281215d139731b61365936bc1b837798ad8fbf
ms.sourcegitcommit: 0fa8b4622322b3d3003e760f364992f7f7e5d6a9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/27/2018
ms.locfileid: "37016835"
---
# <a name="quickstart-recognize-speech-using-the-cognitive-services-speech-c-sdk"></a>Avvio rapido: Riconoscimento vocale in Cognitive Services Speech C# SDK

Questo articolo spiega come creare un'applicazione console C# in Windows usando Cognitive Services Speech SDK per trascrivere il riconoscimento vocale.

## <a name="prerequisites"></a>prerequisiti

* Una chiave di sottoscrizione per il servizio di riconoscimento vocale. Vedere [Provare gratuitamente il Servizio di riconoscimento vocale](get-started.md).
* Visual Studio 2017, Community Edition o versione successiva.
* Carico di lavoro **Sviluppo per desktop .NET** in Visual Studio. È possibile abilitarlo in **Strumenti** \> **Scarica strumenti e funzionalità**. 

## <a name="create-a-visual-studio-project"></a>Creare un progetto di Visual Studio

1. Creare una nuova applicazione console C# in Visual Studio 2017. Nella finestra di dialogo **Nuovo progetto**, nel riquadro a sinistra, espandere **Installato** e selezionare **App console (.NET Framework)**. Per il nome del progetto, immettere *CsharpHelloSpeech*.

    ![Creare App console Visual C# (.NET Framework)](media/sdk/speechsdk-05-vs-cs-new-console-app.png "Creare App console Visual C#")

2. Installare e fare riferimento al [pacchetto Speech SDK NuGet](https://aka.ms/csspeech/nuget). In Esplora soluzioni fare clic con il pulsante destro del mouse sulla soluzione e scegliere **Gestisci pacchetti NuGet per la soluzione**.

    ![Fare clic con il pulsante destro del mouse su Gestisci pacchetti NuGet per la soluzione](media/sdk/speechsdk-06-vs-cs-manage-nuget-packages.png "Gestisci pacchetti NuGet per la soluzione")

3. Nell'angolo superiore a destra, nel campo **Origine pacchetto**, selezionare **Nuget.org**. Cercare e installare il pacchetto `Microsoft.CognitiveServices.Speech` e installarlo nel progetto **CsharpHelloSpeech**.

    ![Installare il pacchetto Microsoft.CognitiveServices.Speech NuGet](media/sdk/speechsdk-08-vs-cs-nuget-install.png "Installare pacchetto NuGet")

4. Nella schermata di licenza visualizzata, accettare la licenza:

    ![Accettare le condizioni di licenza](media/sdk/speechsdk-09-vs-cs-nuget-license.png "Accettare le condizioni di licenza")

## <a name="create-a-platform-configuration-matching-your-pc-architecture"></a>Creare una configurazione di piattaforma corrispondente all'architettura del PC

In questa sezione, si aggiunge una nuova piattaforma alla configurazione che corrisponde all'architettura del processore.

1. Avviare Gestione configurazione. Selezionare **Genera** > **Gestione configurazione**.

    ![Avviare Gestione configurazione](media/sdk/speechsdk-12-vs-cs-cfg-manager-click.png "Avviare Gestione configurazione")

2. Nella finestra di dialogo **Gestione configurazione**, aggiungere una nuova piattaforma. Dall'elenco a discesa **Piattaforma soluzione attiva**, selezionare **Nuova**.

    ![Aggiungere una nuova piattaforma nella finestra di gestione configurazione](media/sdk/speechsdk-14-vs-cs-cfg-manager-new.png "Aggiungere una nuova piattaforma nella finestra di gestione configurazione")

3. Se si esegue Windows a 64 bit, creare una nuova configurazione della piattaforma denominata `x64`. Se si esegue Windows a 32 bit, creare una nuova configurazione della piattaforma denominata `x86`. Nel presente articolo viene creata una configurazione della piattaforma `x64`. 

    ![In Windows a 64-bit, aggiungere una nuova piattaforma denominata "x64"](media/sdk/speechsdk-15-vs-cs-cfg-manager-add-x64.png "Aggiungere piattaforma x64")

## <a name="add-the-sample-code"></a>Aggiungere il codice di esempio

1. In `Program.cs` per il progetto Visual Studio, sostituire il corpo della classe `Program` con gli elementi riportati di seguito. Assicurarsi di sostituire la chiave di sottoscrizione e la regione con una ottenuta per il servizio.

    [!code-csharp[Quickstart Code](~/samples-cognitive-services-speech-sdk/Windows/quickstart-csharp/Program.cs#code)]

2. Dopo aver incollato il codice, il metodo `Main()` deve essere simile a quello mostrato nella seguente schermata:

    ![Metodo principale dopo aver incollato il codice](media/sdk/speechsdk-17-vs-cs-paste-code.png "Metodo principale finale")

3. IntelliSense di Visual Studio evidenzia i riferimenti alle classi di Speech SDK che non è stato possibile risolvere. Per correggere questo errore, aggiungere l'istruzione `using` seguente all'inizio del codice (manualmente o usando le [azioni rapide di Visual Studio](https://docs.microsoft.com/visualstudio/ide/quick-actions)).

    [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/Windows/quickstart-csharp/Program.cs#usingstatement)]

    ![Usare l'azione rapida per aggiungere l'istruzione using mancante](media/sdk/speechsdk-18-vs-cs-add-using.png "Risolvere i problemi di IntelliSense")

4. Assicurarsi che sia stata risolta l'evidenziazione di IntelliSense e salvare le modifiche al progetto.

## <a name="build-and-run-the-sample"></a>Compilare ed eseguire l'esempio

1. Compilare l'applicazione. Nella barra dei menu selezionare **Compila** > **Compila soluzione**. Il codice dovrebbe ora risultare compilato senza errori:

    ![Compilazione riuscita](media/sdk/speechsdk-20-vs-cs-build.png "Compilazione riuscita")

2. Avviare l’applicazione. Nella barra dei menu, selezionare **Debug** > **Avvia debug** o premere **F5**. 

    ![Avviare l'app durante il debug](media/sdk/speechsdk-21-vs-cs-f5.png "Avviare l'app durante il debug")

3. Si apre una finestra della console che chiede di dire qualcosa (in inglese).
Il risultato del riconoscimento viene visualizzato sullo schermo.

    ![Risultato sulla console dopo il riconoscimento efficace](media/sdk/speechsdk-22-cs-vs-console-output.png "Risultato sulla console dopo il riconoscimento efficace")

## <a name="download-code"></a>Scaricare il codice

Per l'insieme di esempi più recente, vedere il [repository GitHub degli esempi di Speech SDK di Servizi cognitivi](https://aka.ms/csspeech/samples).

## <a name="next-steps"></a>Passaggi successivi

- [Traduzione vocale](how-to-translate-speech.md)
- [Personalizzare i modelli di riconoscimento vocale](how-to-customize-speech-models.md)
