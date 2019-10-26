---
title: Creare flussi di lavoro con il connettore IoT Central di Azure in Microsoft Flow | Microsoft Docs
description: Usare il connettore IoT Central in Microsoft Flow per attivare i flussi di lavoro e creare, ottenere, aggiornare ed eliminare i dispositivi ed eseguire comandi nei flussi di lavoro.
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 08/26/2019
ms.topic: conceptual
ms.service: iot-central
manager: hegate
ms.openlocfilehash: 258020c8ccd690c524460873387293e73fc89125
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72951731"
---
# <a name="build-workflows-with-the-iot-central-connector-in-microsoft-flow"></a>Creare flussi di lavoro con il connettore IoT Central in Microsoft Flow

*Questo argomento è rivolto ai responsabili della compilazione e agli amministratori.*

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Usare Microsoft Flow per automatizzare i flussi di lavoro nelle molte applicazioni e i vari servizi di cui si servono gli utenti aziendali. Grazie al connettore IoT Central in Microsoft Flow è possibile attivare flussi di lavoro quando una regola viene attivata in IoT Central. In un flusso di lavoro attivato da IoT Central o da qualsiasi altra applicazione, è possibile usare le azioni nel connettore IoT Central per:
- Creare un dispositivo
- Ottenere informazioni sul dispositivo
- Aggiornare le proprietà e le impostazioni di un dispositivo
- Eseguire un comando in un dispositivo
- Eliminare un dispositivo

Consultare [questi modelli di Microsoft Flow](https://aka.ms/iotcentralflowtemplates) che connettono IoT Central ad altri servizi, quali le notifiche per dispositivi mobili e Microsoft Teams.

## <a name="prerequisites"></a>Prerequisiti

- Un'applicazione con pagamento in base al consumo
- Un account Microsoft personale, aziendale o dell'Istituto di istruzione da usare Microsoft Flow ([altre informazioni sui piani Microsoft Flow](https://aka.ms/microsoftflowplans))
- Un account aziendale o dell'Istituto di istruzione per usare il connettore Azure IoT Central

## <a name="trigger-a-workflow"></a>Attivare un flusso di lavoro

Questa sezione illustra come attivare una notifica mobile nell'app Flow per dispositivi mobili quando una regola viene attivata in IoT Central. È possibile compilare l'intero flusso di lavoro all'interno dell'app IoT Central usando la finestra di progettazione Microsoft Flow incorporata.

1. Iniziare con la [creazione di una regola in IoT Central](howto-create-telemetry-rules.md). Dopo aver salvato le condizioni della regola, selezionare l' **azione Microsoft Flow** come nuova azione. Viene visualizzata una finestra di dialogo in cui è possibile configurare il flusso di lavoro. Il IoT Central account utente a cui si è connessi verrà usato per accedere Microsoft Flow.

    ![Creare una nuova azione di Microsoft Flow](media/howto-add-microsoft-flow/createflowaction.png)

1. Verrà visualizzato un elenco dei flussi di lavoro a cui si ha accesso e che sono collegati a questa regola di IoT Central. Fare clic su **Esplora modelli** o su **nuovo > Crea da modello** ed è possibile scegliere uno dei modelli disponibili. 

    ![Modelli di Microsoft Flow disponibili](media/howto-add-microsoft-flow/flowtemplates1.png)

1. Verrà richiesto di accedere ai connettori nel modello scelto. 

    > [!NOTE]
    > Per usare Azure IoT Central Connector, è necessario accedere con un account di Azure Active Directory (account aziendale o dell'Istituto di istruzione). Un account personale, ad esempio abc@outlook.com o abc@live.com, non è supportato dal connettore Azure IoT Central.

    Dopo aver eseguito l'accesso ai connettori, si accederà alla finestra di progettazione per compilare il flusso di lavoro. Il flusso di lavoro include un trigger di IoT Central in cui l'applicazione e la regola sono già inserite.

1. È possibile personalizzare il flusso di lavoro personalizzando le informazioni passate all'azione e aggiungendo nuove azioni. In questo esempio, l'azione è **notifiche-invia una notifica per dispositivi mobili**. È possibile includere *contenuto dinamico* proveniente dalla regola di IoT Central per trasmettere alla notifica informazioni importanti, quali il nome del dispositivo e il timestamp.

    > [!NOTE]
    > Per ottenere i valori di misurazione e proprietà che hanno attivato la regola, selezionare la casella di controllo **Visualizza più** testo nella finestra del contenuto dinamico.

    ![Azione di modifica del flusso con il pannello dinamico aperto](./media/howto-add-microsoft-flow/flowdynamicpane1.png)

1. Al termine della modifica dell'azione, selezionare **Salva**. Si verrà riportati alla pagina di panoramica del flusso di lavoro. Qui è possibile visualizzare la cronologia delle esecuzioni e condividerla con i colleghi.

    > [!NOTE]
    > Se si desidera che altri utenti dell'app IoT Central siano autorizzati a modificare la regola, è necessario condividerla con loro in Microsoft Flow. Aggiungere i loro account di Microsoft Flow nel flusso di lavoro configurandoli come proprietari.

1. Se si torna all'app IoT Central, si noterà che questa regola ha un'azione Microsoft Flow nell'area azioni.

È anche possibile creare flussi di lavoro usando il connettore IoT Central direttamente da Microsoft Flow. È quindi possibile scegliere a quale app IoT Central connettersi.

## <a name="create-a-device-in-a-workflow"></a>Creare un dispositivo in un flusso di lavoro

Questa sezione illustra come creare un nuovo dispositivo in IoT Central quando si preme un pulsante di un dispositivo mobile usando l'app per dispositivi mobili Microsoft Flow. È possibile usare questa azione in Microsoft Flow per integrare i sistemi ERP, come Dynamic,s con IoT Central creando un nuovo dispositivo quando un dispositivo viene aggiunto altrove.

1. Iniziare creando un flusso di lavoro vuoto in Microsoft Flow.

1. Cercare il trigger **Pulsante Flusso per dispositivi mobili**.

1. In questo trigger aggiungere un input di testo denominato **Nome dispositivo**. Modificare il testo della descrizione in **Immettere il nome del nuovo dispositivo**.

1. Aggiungere una nuova azione. Cercare l'azione **Azure IoT Central - Create a device** (Azure IoT Central - Crea un dispositivo).

1. Selezionare l'applicazione e scegliere un modello di dispositivo in base al quale creare un dispositivo negli elenchi a discesa. L'azione si espande per mostrare tutte le proprietà e le impostazioni del dispositivo.

1. Selezionare il campo Nome dispositivo. Dal panello del contenuto dinamico scegliere **Nome dispositivo**. Questo valore viene passato dall'input che l'utente immette nell'app per dispositivi mobili e è il nome del nuovo dispositivo in IoT Central. In questo esempio, l'unico campo obbligatorio è il nome del dispositivo, indicato dall'asterisco rosso. Un modello di dispositivo diverso potrebbe avere più campi obbligatori da compilare per la creazione di un nuovo dispositivo.

    ![Pannello dinamico dell'azione di creazione dispositivo in Microsoft Flow](./media/howto-add-microsoft-flow/flowcreatedevice1.png)

1. (Facoltativo) Compilare gli altri campi come si è soliti fare per creare nuovi dispositivi.

1. Per finire, salvare il flusso di lavoro.

1. Provare il flusso di lavoro nell'app per dispositivi mobili Microsoft Flow. Andare alla scheda **Buttons** (Pulsanti) nell'app. Verrà visualizzato il flusso di lavoro Button -> Create a new device (Pulsante > Crea un nuovo dispositivo). Immettere il nome del nuovo dispositivo e osservare come viene visualizzato in IoT Central.

    ![Screenshot di creazione dispositivo mobile in Microsoft Flow](./media/howto-add-microsoft-flow/flowmobilescreenshot.png)

## <a name="update-a-device-in-a-workflow"></a>Aggiornare un dispositivo in un flusso di lavoro

Questa sezione illustra come aggiornare le impostazioni e proprietà di un dispositivo in IoT Central quando si preme un pulsante di un dispositivo mobile usando l'app per dispositivi mobili Microsoft Flow.

1. Iniziare creando un flusso di lavoro vuoto in Microsoft Flow.

1. Cercare il trigger **Pulsante Flusso per dispositivi mobili**.

1. In questo trigger aggiungere un input, ad esempio un input di testo **Location** (Posizione), che corrisponda a un'impostazione o a una proprietà da modificare. Cambiare il testo descrittivo.

1. Aggiungere una nuova azione. Cercare l'azione **Azure IoT Central - Update a device** (Azure IoT Central - Aggiorna un dispositivo).

1. Scegliere l'applicazione dall'elenco a discesa. A questo punto è necessario procurarsi un ID dispositivo del dispositivo che si desidera aggiornare. 

    > [!NOTE] 
    > **È necessario usare l'ID trovato nell'URL** della pagina dei dettagli del dispositivo che si vuole aggiornare. L'ID dispositivo trovato nell'elenco di dispositivi di Esplora dispositivi non è quello corretto da usare in Microsoft Flow.

    ![ID IoT Central dall'URL](./media/howto-add-microsoft-flow/iotcdeviceidurl.png)

1. È possibile aggiornare il nome del dispositivo. Per aggiornare le proprietà e le impostazioni del dispositivo, è necessario selezionare il modello del dispositivo da aggiornare nell'elenco a discesa **Device Template** (Modello di dispositivo). Il riquadro Azioni si espande per visualizzare tutte le proprietà e le impostazioni che è possibile aggiornare.

    ![Flusso di lavoro di aggiornamento dispositivo di Microsoft Flow](./media/howto-add-microsoft-flow/flowupdatedevice1.png)

1. Selezionare tutte le proprietà e le impostazioni che si desidera aggiornare. Dal riquadro del contenuto dinamico, scegliere l'input corrispondente dal trigger. In questo esempio, il valore Location (Posizione) viene propagato verso il basso per aggiornare la proprietà Location del dispositivo.

1. Per finire, salvare il flusso di lavoro.

1. Provare il flusso di lavoro nell'app per dispositivi mobili Microsoft Flow. Andare alla scheda **Buttons** (Pulsanti) nell'app. Verrà visualizzato il flusso di lavoro Button -> Update a device (Pulsante > Aggiorna un dispositivo). Immettere gli input e visualizzare il dispositivo aggiornato in IoT Central.

## <a name="get-device-information-in-a-workflow"></a>Ottenere informazioni sul dispositivo in un flusso di lavoro

È possibile ottenere le informazioni sul dispositivo in base al relativo ID usando il **IOT Central di Azure-ottenere un'** azione del dispositivo. 
> [!NOTE] 
> **È necessario usare l'ID trovato nell'URL** della pagina dei dettagli del dispositivo che si vuole aggiornare. L'ID dispositivo trovato nell'elenco di dispositivi di Esplora dispositivi non è quello corretto da usare in Microsoft Flow.

È possibile ottenere informazioni come il nome del dispositivo, il nome del modello di dispositivo, i valori delle proprietà e i valori delle impostazioni da passare a azioni successive nel flusso di lavoro. Ecco un flusso di lavoro di esempio che passa il valore della proprietà Customer Name da un dispositivo a Microsoft teams.

   ![Flusso di lavoro per ottenere il dispositivo](./media/howto-add-microsoft-flow/flowgetdevice1.png)


## <a name="run-a-command-on-a-device-in-a-workflow"></a>Eseguire un comando in un dispositivo in un flusso di lavoro
È possibile eseguire un comando in un dispositivo specificato dal relativo ID usando il **IOT Central di Azure: eseguire un'** azione di comando. 

> [!NOTE] 
> **È necessario usare l'ID trovato nell'URL** della pagina dei dettagli del dispositivo che si vuole aggiornare. L'ID dispositivo trovato nell'elenco di dispositivi di Esplora dispositivi non è quello corretto da usare in Microsoft Flow.
    
È possibile scegliere di eseguire il comando e passare i parametri del comando tramite questa azione. Ecco un flusso di lavoro di esempio che esegue un comando di riavvio del dispositivo da un pulsante nell'app per dispositivi mobili Microsoft Flow.

   ![Flusso di lavoro per ottenere il dispositivo](./media/howto-add-microsoft-flow/flowrunacommand1.png)

## <a name="delete-a-device-in-a-workflow"></a>Eliminare un dispositivo in un flusso di lavoro

È possibile eliminare un dispositivo in base al relativo ID usando il **IOT Central di Azure-eliminare un'** azione del dispositivo. 
> [!NOTE] 
> **È necessario usare l'ID trovato nell'URL** della pagina dei dettagli del dispositivo che si vuole aggiornare. L'ID dispositivo trovato nell'elenco di dispositivi di Esplora dispositivi non è quello corretto da usare in Microsoft Flow.

Segue un flusso di lavoro di esempio che consente di eliminare un dispositivo quando si preme un pulsante nell'app per dispositivi mobili Microsoft Flow.

   ![Flusso di lavoro di eliminazione dispositivo di Microsoft Flow](./media/howto-add-microsoft-flow/flowdeletedevice.png)

## <a name="troubleshooting"></a>risoluzione dei problemi

Se si incontrano difficoltà a creare una connessione al connettore di Azure IoT Central, leggere questi suggerimenti.

1. Gli account personali Microsoft (ad esempio i domini @hotmail.com, @live.com, @outlook.com) al momento non sono supportati. È necessario usare un account aziendale o dell'Istituto di istruzione Azure Active Directory (AD).

2. Per usare il connettore IoT Central in Microsoft Flow, è necessario avere effettuato l'accesso all'applicazione IoT Central almeno una volta. In caso contrario, l'applicazione non sarà più visualizzata nel menu a discesa Applicazione.

3. Se si riceve un errore durante l'uso di un account Azure AD, provare ad aprire Windows PowerShell ed eseguire il seguente cmdlet come amministratore.

    ``` PowerShell
    Install-Module AzureAD
    Connect-AzureAD
    New-AzureADServicePrincipal -AppId 9edfcdd9-0bc5-4bd4-b287-c3afc716aac7 -DisplayName "Azure IoT Central"
    ```

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come usare Microsoft Flow per creare flussi di lavoro, il passaggio successivo suggerito consiste nel [gestire i dispositivi](howto-manage-devices.md).

