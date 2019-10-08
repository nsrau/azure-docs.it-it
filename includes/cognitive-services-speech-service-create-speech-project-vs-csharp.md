---
author: wolfma61
ms.service: cognitive-services
ms.topic: include
ms.date: 08/30/2019
ms.author: wolfma
ms.openlocfilehash: c7332177795a45331749225e0f3dc3856e28a6a2
ms.sourcegitcommit: e9936171586b8d04b67457789ae7d530ec8deebe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71327035"
---
Per creare un progetto di Visual Studio per lo sviluppo per Windows, è necessario creare il progetto, configurare l'ambiente di sviluppo per desktop .NET in Visual Studio, installare Speech SDK e selezionare l'architettura di destinazione.

### <a name="create-the-project-and-add-the-workload"></a>Creare il progetto e aggiungere il carico di lavoro

Per iniziare, creare il progetto in Visual Studio e verificare che Visual Studio sia configurato per lo sviluppo per desktop .NET:

1. Aprire Visual Studio 2019.

1. Nella finestra di avvio selezionare **Crea un nuovo progetto**. 

1. Nella finestra **Crea un nuovo progetto** scegliere **App console (.NET Framework)** e quindi fare clic su **Avanti**.

1. Nella finestra **Configura il nuovo progetto** immettere *helloworld* in **Nome progetto** scegliere o creare il percorso della directory in **Percorso** e quindi selezionare **Crea**.

1. Dalla barra dei menu di Visual Studio scegliere **Strumenti** > **Ottieni strumenti e funzionalità** per aprire il Programma di installazione di Visual Studio e visualizzare la finestra di dialogo **Modifica**.

1. Assicurarsi che il carico di lavoro di **Sviluppo per desktop .NET** sia disponibile. Se il carico di lavoro non è stato installato, selezionare la casella di controllo accanto ad esso e quindi fare clic su **Modifica** per avviare l'installazione. Il download e l'installazione può richiedere alcuni minuti.

   Se la casella di controllo accanto a **Sviluppo per desktop .NET** è già selezionata, fare clic su **Chiudi** per chiudere la finestra di dialogo.

   ![Abilitare lo sviluppo per desktop .NET](../articles/cognitive-services/speech-service/media/sdk/vs-enable-net-desktop-workload.png)

1. Chiudere il Programma di installazione di Visual Studio.

### <a name="install-the-speech-sdk"></a>Installare Speech SDK

Il passaggio successivo prevede l'installazione del [pacchetto NuGet Speech SDK](https://aka.ms/csspeech/nuget) per potervi fare riferimento nel codice.

1. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **helloworld** e quindi scegliere **Gestisci pacchetti NuGet** per visualizzare Gestione pacchetti NuGet.

   ![Gestione pacchetti NuGet](../articles/cognitive-services/speech-service/media/sdk/vs-nuget-package-manager.png)

1. Nell'angolo in alto a destra individuare la casella a discesa **Origine dei pacchetti** e assicurarsi che sia selezionata l'opzione **nuget.org**.

1. Nell'angolo in alto a sinistra fare clic su **Sfoglia**.

1. Nella casella di ricerca digitare *Microsoft.CognitiveServices.Speech* e premere **INVIO**.

1. Nei risultati della ricerca selezionare il pacchetto **Microsoft.CognitiveServices.Speech** e quindi selezionare **Installa** per installare la versione stabile più recente.

   ![Installare il pacchetto NuGet Microsoft.CognitiveServices.Speech](../articles/cognitive-services/speech-service/media/sdk/qs-csharp-dotnet-windows-03-nuget-install-1.0.0.png)

1. Accettare tutti i contratti e le licenze per avviare l'installazione.

   Dopo aver installato il pacchetto, viene visualizzato un messaggio di conferma nella finestra della **Console di Gestione pacchetti**.

### <a name="choose-the-target-architecture"></a>Scegliere l'architettura di destinazione

A questo punto, per compilare ed eseguire l'applicazione console, creare una configurazione della piattaforma corrispondente all'architettura del computer.

1. Nella barra dei menu selezionare **Compila** > **Gestione configurazione**. Verrà visualizzata finestra di dialogo **Gestione configurazione**.

   ![Finestra di dialogo Gestione configurazione](../articles/cognitive-services/speech-service/media/sdk/vs-configuration-manager-dialog-box.png)

1. Nella casella a discesa **Piattaforma soluzione attiva** selezionare **Nuova**. Verrà visualizzata la finestra di dialogo **Nuova piattaforma soluzione**.

1. Nella casella a discesa **Digitare o selezionare la nuova piattaforma**:
   - Se è in esecuzione Windows a 64 bit, selezionare **x64**.
   - Se è in esecuzione Windows a 32 bit, selezionare **x86**.

1. Fare clic su **OK** e quindi su **Chiudi**.
