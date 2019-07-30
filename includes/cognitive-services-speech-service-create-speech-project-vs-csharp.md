---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 07/05/2019
ms.author: wolfma
ms.openlocfilehash: 308ee2ef121648cb45152948926c5fd7fb934744
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68362538"
---
1. Aprire Visual Studio 2019.

1. Nella finestra di avvio selezionare **Crea un nuovo progetto**. 

1. Scegliere **App console (.NET Framework)** , quindi selezionare **Avanti**.

1. In **Nome del progetto** immettere `helloworld` e quindi selezionare **Crea**.

1. Nella barra dei menu di Visual Studio selezionare **Strumenti** > **Ottieni strumenti e funzionalità** e assicurarsi che il carico di lavoro **Sviluppo per desktop .NET** sia disponibile. Se il carico di lavoro non è stato installato, selezionare la casella di controllo e quindi fare clic su **Modifica** per avviare l'installazione. Il download e l'installazione può richiedere alcuni minuti.

   Se la casella di controllo accanto a **Sviluppo per desktop .NET** è selezionata, è ora possibile chiudere la finestra di dialogo.

   ![Abilitare lo sviluppo per desktop .NET](../articles/cognitive-services/speech-service/media/sdk/vs-enable-net-desktop-workload.png)

Il passaggio successivo prevede l'installazione del [pacchetto NuGet Speech SDK](https://aka.ms/csspeech/nuget) per potervi fare riferimento nel codice.

1. In Esplora soluzioni fare clic con il pulsante destro del mouse su `helloworld` e quindi scegliere **Gestisci pacchetti NuGet** per visualizzare Gestione pacchetti NuGet.

   ![Gestione pacchetti NuGet](../articles/cognitive-services/speech-service/media/sdk/vs-nuget-package-manager.png)

1. Nell'angolo in alto a destra individuare la casella a discesa **Origine dei pacchetti** e assicurarsi che sia selezionata l'opzione **nuget.org**.

1. Nell'angolo in alto a sinistra fare clic su **Sfoglia**.

1. Nella casella di ricerca specificare il pacchetto `Microsoft.CognitiveServices.Speech` e premere INVIO.

1. Selezionare `Microsoft.CognitiveServices.Speech` e quindi **Installa** per installare la versione stabile più recente.

   ![Installare il pacchetto NuGet Microsoft.CognitiveServices.Speech](../articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-03-nuget-install-1.0.0.png)

1. Accettare tutti i contratti e le licenze per avviare l'installazione.

   Dopo aver installato il pacchetto, viene visualizzato un messaggio di conferma nella finestra della **Console di Gestione pacchetti**.

A questo punto, per compilare ed eseguire l'applicazione console, creare una configurazione della piattaforma corrispondente all'architettura del computer.

1. Nella barra dei menu selezionare **Compila** > **Gestione configurazione**. Verrà visualizzata finestra di dialogo **Gestione configurazione**.

   ![Finestra di dialogo Gestione configurazione](../articles/cognitive-services/speech-service/media/sdk/vs-configuration-manager-dialog-box.png)

1. Nella casella a discesa **Piattaforma soluzione attiva** selezionare **Nuova**. Verrà visualizzata la finestra di dialogo **Nuova piattaforma soluzione**.

1. Nella casella a discesa **Digitare o selezionare la nuova piattaforma**:
   - Se è in esecuzione Windows a 64 bit, selezionare **x64**.
   - Se è in esecuzione Windows a 32 bit, selezionare **x86**.

1. Fare clic su **OK** e quindi su **Chiudi**.
