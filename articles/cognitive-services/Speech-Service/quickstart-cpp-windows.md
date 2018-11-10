---
title: "Guida introduttiva: Riconoscimento vocale in C++ su Windows con l'SDK del servizio Voce"
titleSuffix: Azure Cognitive Services
description: Informazioni sul riconoscimento vocale in C++ su Windows Desktop con l'SDK del servizio Voce
services: cognitive-services
author: wolfma61
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 11/06/2018
ms.author: wolfma
ms.openlocfilehash: 8947ba3f39cebf51e956db0d841e393963832bc4
ms.sourcegitcommit: 1b186301dacfe6ad4aa028cfcd2975f35566d756
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2018
ms.locfileid: "51218737"
---
# <a name="quickstart-recognize-speech-in-c-on-windows-by-using-the-speech-sdk"></a>Avvio rapido: Riconoscimento vocale in C++ su Windows Desktop con Speech SDK

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

In questo articolo, viene creata un'applicazione console C++ per Windows. Utilizzare i Servizi cognitivi [Speech SDK](speech-sdk.md) per trascrivere il riconoscimento vocale in tempo reale dal microfono del PC. L'applicazione si basa sul [pacchetto NuGet Speech SDK](https://aka.ms/csspeech/nuget) e su Microsoft Visual Studio 2017 (qualsiasi edizione).

## <a name="prerequisites"></a>Prerequisiti

È necessaria una chiave di sottoscrizione del Servizio di riconoscimento vocale per completare la guida introduttiva. È possibile ottenerne una gratuitamente. Consultare [Provare gratuitamente il Servizio di riconoscimento vocale](get-started.md) per informazioni dettagliate.

## <a name="create-a-visual-studio-project"></a>Creare un progetto di Visual Studio

1. Avviare Visual Studio 2017.

1. Assicurarsi che il carico di lavoro di **sviluppo di applicazioni desktop con C++** sia disponibile. Scegliere **Strumenti** > **Ottieni strumenti e funzionalità** dalla barra dei menu di Visual Studio per aprire il programma di installazione di Visual Studio. Se questo carico di lavoro è già attivato, andare al passaggio successivo. 

    ![Screenshot della scheda del carico di lavoro di Visual Studio](media/sdk/vs-enable-cpp-workload.png)

    In caso contrario, selezionare la casella accanto a **Sviluppo di applicazioni Desktop con C++**. 

1. Assicurarsi che il componente **Gestione pacchetti NuGet** sia disponibile. Passare alla scheda **Singoli componenti** della finestra di dialogo del programma di installazione di Visual Studio e selezionare **Gestione pacchetti NuGet** se non è già abilitato.

      ![Screenshot della scheda Singoli componenti di Visual Studio](media/sdk/vs-enable-nuget-package-manager.png)

1. Se è necessario abilitare il carico di lavoro di C++ o NuGet, selezionare **Modifica** (nell'angolo inferiore destro della finestra di dialogo). L'installazione delle nuove funzionalità richiede qualche secondo. Se entrambe le funzionalità sono state già abilitate, chiudere la finestra di dialogo.

1. Creare un'applicazione console Windows in Visual C++ per Windows Desktop. In primo luogo, scegliere **File** > **Nuovo** > **Progetto** dal menu. Nella finestra di dialogo **Nuovo progetto**, espandere **Installato** > **Visual C++** > **Desktop di Windows** nel riquadro a sinistra. Selezionare quindi **Applicazione console di Windows**. Per il nome del progetto, immettere *helloworld*.

    ![Screenshot della finestra di dialogo Nuovo progetto](media/sdk/qs-cpp-windows-01-new-console-app.png)

1. Se si esegue Windows a 64 bit, è possibile passare la piattaforma della build di `x64` utilizzando il menu a discesa sulla barra degli strumenti di Visual Studio. (le versioni a 64 bit di Windows possono eseguire applicazioni a 32 bit, perciò questo non è un requisito.)

    ![Screenshot della barra degli strumenti di Visual Studio, con l'opzione x64 evidenziata](media/sdk/qs-cpp-windows-02-switch-to-x64.png)

1. In Esplora soluzioni, fare clic con il pulsante destro del mouse sulla soluzione e quindi scegliere **Gestire pacchetti NuGet per la soluzione**.

    ![Screenshot di Esplora soluzioni, con l'opzione Gestisci pacchetti NuGet per la soluzione selezionata](media/sdk/qs-cpp-windows-03-manage-nuget-packages.png)

1. Nell'angolo superiore a destra, nel campo **Origine pacchetto**, selezionare **nuget.org**. Cercare il pacchetto `Microsoft.CognitiveServices.Speech` e installarlo nel progetto **helloworld**.

    ![Screenshot della finestra di dialogo Gestisci pacchetti per la soluzione](media/sdk/qs-cpp-windows-04-nuget-install-1.0.0.png)

    > [!NOTE]
    > La versione corrente di Speech SDK di Servizi cognitivi è `1.1.0`.

1. Accettare la licenza visualizzata per iniziare l'installazione del pacchetto NuGet.

    ![Screenshot della finestra di dialogo dell'accettazione della licenza](media/sdk/qs-cpp-windows-05-nuget-license.png)

Dopo aver installato il pacchetto, un messaggio di conferma viene visualizzato nella console di Gestione pacchetti.

## <a name="add-sample-code"></a>Aggiungere codice di esempio

1. Aprire il file di origine *helloworld.cpp*. Sostituire tutto il codice seguente sotto l'istruzione iniziale di inclusione (`#include "stdafx.h"` o `#include "pch.h"`) con il codice seguente:

   [!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp-windows/helloworld/helloworld.cpp#code)]

1. Nello stesso file, sostituire la stringa `YourSubscriptionKey` con la chiave di sottoscrizione.

1. Sostituire la stringa `YourServiceRegion` con la [regione](regions.md) associata alla sottoscrizione (ad esempio, `westus` per la sottoscrizione di valutazione gratuita).

1. Salvare le modifiche apportate al progetto.

## <a name="build-and-run-the-app"></a>Compilare ed eseguire l'app

1. Compilare l'applicazione. Nella barra dei menu scegliere **Compila** > **Compila soluzione**. Il codice dovrebbe risultare compilato senza errori.

   ![Screenshot dell'applicazione Visual Studio, con l'opzione Compila soluzione selezionata](media/sdk/qs-cpp-windows-06-build.png)

1. Avviare l’applicazione. Nella barra dei menu scegliere **Debug** > **Avvia debug** o premere **F5**.

   ![Screenshot dell'applicazione Visual Studio, con l'opzione Avvio del debug selezionata](media/sdk/qs-cpp-windows-07-start-debugging.png)

1. Si apre una finestra della console che chiede di dire qualcosa. Pronunciare una frase o un'espressione in inglese. I contenuti vocali vengono trasmessi al Servizio di riconoscimento vocale e trascritti in un testo che viene visualizzato nella stessa finestra.

   ![Screenshot della console dopo il riconoscimento corretto](media/sdk/qs-cpp-windows-08-console-output-release.png)

[!INCLUDE [Download this sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Ricercare questo esempio nella cartella `quickstart/cpp-windows`.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Riconoscere le finalità dai contenuti vocali con Speech SDK per C++](how-to-recognize-intents-from-speech-cpp.md)

## <a name="see-also"></a>Vedere anche 

- [Traduzione vocale](how-to-translate-speech-csharp.md)
- [Personalizzare modelli acustici](how-to-customize-acoustic-models.md)
- [Personalizzare modelli linguistici](how-to-customize-language-model.md)
