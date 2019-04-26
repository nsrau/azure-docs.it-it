---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 2/20/2019
ms.author: erhopf
ms.openlocfilehash: b5a24d83faef5a895317f162178f8bd588a1466d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60620071"
---
1. Avviare Visual Studio 2017.

1. Assicurarsi che il carico di lavoro di **sviluppo di applicazioni desktop con C++** sia disponibile. Scegliere **Strumenti** > **Ottieni strumenti e funzionalità** dalla barra dei menu di Visual Studio per aprire il programma di installazione di Visual Studio. Se questo carico di lavoro è già attivato, andare al passaggio successivo.

    ![Screenshot della scheda del carico di lavoro di Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-cpp-workload.png)

    In caso contrario, selezionare la casella accanto a **Sviluppo di applicazioni Desktop con C++**.

1. Assicurarsi che il componente **Gestione pacchetti NuGet** sia disponibile. Passare alla scheda **Singoli componenti** della finestra di dialogo del programma di installazione di Visual Studio e selezionare **Gestione pacchetti NuGet** se non è già abilitato.

      ![Screenshot della scheda Singoli componenti di Visual Studio](../articles/cognitive-services/Speech-Service/media/sdk/vs-enable-nuget-package-manager.png)

1. Se è necessario abilitare il carico di lavoro di C++ o NuGet, selezionare **Modifica** (nell'angolo inferiore destro della finestra di dialogo). L'installazione delle nuove funzionalità richiede qualche secondo. Se entrambe le funzionalità sono state già abilitate, chiudere la finestra di dialogo.

1. Creare un'applicazione console Windows in Visual C++ per Windows Desktop. In primo luogo, scegliere **File** > **Nuovo** > **Progetto** dal menu. Nella finestra di dialogo **Nuovo progetto**, espandere **Installato** > **Visual C++** > **Desktop di Windows** nel riquadro a sinistra. Selezionare quindi **Applicazione console di Windows**. Per il nome del progetto, immettere *helloworld*.

    ![Screenshot della finestra di dialogo Nuovo progetto](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-01-new-console-app.png)

1. Se si esegue Windows a 64 bit, è possibile passare la piattaforma della build di `x64` utilizzando il menu a discesa sulla barra degli strumenti di Visual Studio. (le versioni a 64 bit di Windows possono eseguire applicazioni a 32 bit, perciò questo non è un requisito.)

    ![Screenshot della barra degli strumenti di Visual Studio, con l'opzione x64 evidenziata](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-02-switch-to-x64.png)

1. In Esplora soluzioni, fare clic con il pulsante destro del mouse sulla soluzione e quindi scegliere **Gestire pacchetti NuGet per la soluzione**.

    ![Screenshot di Esplora soluzioni, con l'opzione Gestisci pacchetti NuGet per la soluzione selezionata](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-03-manage-nuget-packages.png)

1. Nell'angolo superiore a destra, nel campo **Origine pacchetto**, selezionare **nuget.org**. Cercare il pacchetto `Microsoft.CognitiveServices.Speech` e installarlo nel progetto **helloworld**.

    ![Screenshot della finestra di dialogo Gestisci pacchetti per la soluzione](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-04-nuget-install-1.0.0.png)

    > [!NOTE]
    > La versione corrente di Speech SDK di Servizi cognitivi è `1.4.0`.

1. Accettare la licenza visualizzata per iniziare l'installazione del pacchetto NuGet.

    ![Screenshot della finestra di dialogo dell'accettazione della licenza](../articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-05-nuget-license.png)

Dopo aver installato il pacchetto, viene visualizzato un messaggio di conferma nella console di Gestione pacchetti.
