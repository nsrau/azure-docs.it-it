---
title: Creare flussi di lavoro con il connettore IoT Central di Azure in Microsoft Flow | Microsoft Docs
description: Usare il connettore IoT Central in Microsoft Flow per attivare flussi di lavoro e creare, aggiornare ed eliminare dispositivi nei flussi di lavoro.
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 02/05/2019
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: 76b1940ea1724c7eee8d2784c0e2c2328dd4c8bf
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/06/2019
ms.locfileid: "55772563"
---
# <a name="build-workflows-with-the-iot-central-connector-in-microsoft-flow"></a>Creare flussi di lavoro con il connettore IoT Central in Microsoft Flow

*Questo argomento è rivolto ai responsabili della compilazione e agli amministratori.*

Usare Microsoft Flow per automatizzare i flussi di lavoro nelle molte applicazioni e i vari servizi di cui si servono gli utenti aziendali. Grazie al connettore IoT Central in Microsoft Flow è possibile attivare flussi di lavoro quando una regola viene attivata in IoT Central. In un flusso di lavoro attivato da IoT Central o da qualsiasi altra applicazione è possibile usare le azioni del connettore IoT Central per creare un dispositivo, aggiornare le proprietà e le impostazioni di un dispositivo o eliminare un dispositivo. Consultare [questi modelli di Microsoft Flow](https://aka.ms/iotcentralflowtemplates) che connettono IoT Central ad altri servizi, quali le notifiche per dispositivi mobili e Microsoft Teams.

## <a name="prerequisites"></a>Prerequisiti

- Un'applicazione con pagamento in base al consumo
- Un account Microsoft personale, aziendale o dell'istituto di istruzione per eseguire l'accesso a Flow ([altre informazioni sui piani di Microsoft Flow](https://aka.ms/microsoftflowplans))

## <a name="trigger-a-workflow-when-a-rule-is-triggered"></a>Attivare un flusso di lavoro quando viene eseguita una regola

Questa sezione illustra come attivare una notifica per dispositivi mobili nell'app Microsoft Flow per dispositivi mobili quando viene attivata una regola in IoT Central.

1. Iniziare con la [creazione di una regola in IoT Central](howto-create-telemetry-rules-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json). Dopo aver salvato le condizioni della regola, fare clic su **Microsoft Flow action** (Azione di Microsoft Flow) come nuova azione. Nel browser si aprirà una nuova scheda o finestra che indirizzerà a Microsoft Flow.

    ![Creare una nuova azione di Microsoft Flow](media/howto-add-microsoft-flow-experimental/createflowaction.png)

1. Eseguire l'accesso a Microsoft Flow. Questo non deve necessariamente essere lo stesso account che si utilizza in IoT Central. Si aprirà una pagina di panoramica con un connettore IoT Central connesso a un'azione personalizzata.

1. Accedere al connettore IoT Central e fare clic su **Continua**. Viene visualizzata la finestra di progettazione di Microsoft Flow in cui creare il flusso di lavoro. Il flusso di lavoro include un trigger di IoT Central in cui l'applicazione e la regola sono già inserite.

1. Scegliere **+ Nuovo passaggio** e **Aggiungi un'azione**. A questo punto è possibile aggiungere qualsiasi azione che si desidera al flusso di lavoro. Ad esempio, è possibile inviare una notifica per dispositivo mobile. Cercare **notification** (notifica), quindi scegliere **Notifications - Send me a mobile notification** (Notifiche - Inviami una notifica sul dispositivo mobile).

1. Compilare il campo Text (Testo) dell'azione con ciò che la notifica deve comunicare. È possibile includere *contenuto dinamico* proveniente dalla regola di IoT Central per trasmettere alla notifica informazioni importanti, quali il nome del dispositivo e il timestamp.

    > [!NOTE]
    > Fare clic sul testo "Altre informazioni" nella finestra del contenuto dinamico per ottenere i valori delle misurazioni e delle proprietà che hanno attivato la regola.

    ![Azione di modifica del flusso con il pannello dinamico aperto](./media/howto-add-microsoft-flow-experimental/flowdynamicpane.png)

1. Al termine dell'azione di modifica, fare clic su **Salva**. Si verrà riportati alla pagina di panoramica del flusso di lavoro. Qui è possibile visualizzare la cronologia delle esecuzioni e condividerla con i colleghi.

    > [!NOTE]
    > Se si desidera che altri utenti dell'app IoT Central siano autorizzati a modificare la regola, è necessario condividerla con loro in Microsoft Flow. Aggiungere i loro account di Microsoft Flow nel flusso di lavoro configurandoli come proprietari.

1. Se si torna all'app IoT Central, si noterà che per questa regola è presente un'azione di Microsoft Flow nell'area Azioni.

È comunque sempre possibile iniziare a creare un flusso di lavoro usando il connettore IoT Central in Microsoft Flow. In seguito si sceglierà l'app IoT Central e la regola alle quali connettersi.

## <a name="create-a-device-in-a-workflow"></a>Creare un dispositivo in un flusso di lavoro

Questa sezione illustra come creare un nuovo dispositivo in IoT Central quando si preme un pulsante di un dispositivo mobile usando l'app per dispositivi mobili Microsoft Flow. È possibile usare questa azione in Microsoft Flow per integrare i sistemi ERP, come Dynamic,s con IoT Central creando un nuovo dispositivo quando un dispositivo viene aggiunto altrove.

1. Iniziare creando un flusso di lavoro vuoto in Microsoft Flow.

1. Cercare il trigger **Pulsante Flusso per dispositivi mobili**.

1. In questo trigger aggiungere un input di testo denominato **Nome dispositivo**. Modificare il testo della descrizione in **Immettere il nome del nuovo dispositivo**.

1. Aggiungere una nuova azione. Cercare l'azione **Azure IoT Central - Create a device** (Azure IoT Central - Crea un dispositivo).

1. Selezionare l'applicazione e scegliere un modello di dispositivo in base al quale creare un dispositivo negli elenchi a discesa. L'azione si espande per mostrare tutte le proprietà e le impostazioni del dispositivo.

1. Selezionare il campo Nome dispositivo. Dal panello del contenuto dinamico scegliere **Nome dispositivo**. Questo valore verrà passato dall'input che l'utente immette nell'app per dispositivi mobili e diventerà il nome del nuovo dispositivo in IoT Central. In questo esempio, l'unico campo obbligatorio è il nome del dispositivo, indicato dall'asterisco rosso. Un modello di dispositivo diverso potrebbe avere più campi obbligatori da compilare per la creazione di un nuovo dispositivo.

    ![Pannello dinamico dell'azione di creazione dispositivo in Microsoft Flow](./media/howto-add-microsoft-flow-experimental/flowcreatedevice.png)
1. (Facoltativo) Compilare gli altri campi come si è soliti fare per creare nuovi dispositivi.

1. Per finire, salvare il flusso di lavoro.

1. Provare il flusso di lavoro nell'app per dispositivi mobili Microsoft Flow. Andare alla scheda **Buttons** (Pulsanti) nell'app. Verrà visualizzato il flusso di lavoro Button -> Create a new device (Pulsante > Crea un nuovo dispositivo). Immettere il nome del nuovo dispositivo e osservare come viene visualizzato in IoT Central.

    ![Screenshot di creazione dispositivo mobile in Microsoft Flow](./media/howto-add-microsoft-flow-experimental/flowmobilescreenshot.png)

## <a name="update-a-device-in-a-workflow"></a>Aggiornare un dispositivo in un flusso di lavoro

Questa sezione illustra come aggiornare le impostazioni e proprietà di un dispositivo in IoT Central quando si preme un pulsante di un dispositivo mobile usando l'app per dispositivi mobili Microsoft Flow.

1. Iniziare creando un flusso di lavoro vuoto in Microsoft Flow.

1. Cercare il trigger **Pulsante Flusso per dispositivi mobili**.

1. In questo trigger aggiungere un input, ad esempio un input di testo **Location** (Posizione), che corrisponda a un'impostazione o a una proprietà da modificare. Cambiare il testo descrittivo.

1. Aggiungere una nuova azione. Cercare l'azione **Azure IoT Central - Update a device** (Azure IoT Central - Aggiorna un dispositivo).

1. Scegliere l'applicazione dall'elenco a discesa. A questo punto è necessario procurarsi un ID dispositivo del dispositivo che si desidera aggiornare. È possibile ottenere l'ID del dispositivo IoT Central da **Device Explorer**.

    ![ID dispositivo in Device Explorer di IoT Central](./media/howto-add-microsoft-flow-experimental/iotcdeviceid.png)

1. È possibile aggiornare il nome del dispositivo. Per aggiornare le proprietà e le impostazioni del dispositivo, è necessario selezionare il modello del dispositivo da aggiornare nell'elenco a discesa **Device Template** (Modello di dispositivo). Il riquadro Azioni si espande per visualizzare tutte le proprietà e le impostazioni che è possibile aggiornare.

    ![Flusso di lavoro di aggiornamento dispositivo di Microsoft Flow](./media/howto-add-microsoft-flow-experimental/flowupdatedevice.png)

1. Selezionare tutte le proprietà e le impostazioni che si desidera aggiornare. Dal riquadro del contenuto dinamico, scegliere l'input corrispondente dal trigger. In questo esempio, il valore Location (Posizione) viene propagato verso il basso per aggiornare la proprietà Location del dispositivo.

1. Per finire, salvare il flusso di lavoro.

1. Provare il flusso di lavoro nell'app per dispositivi mobili Microsoft Flow. Andare alla scheda **Buttons** (Pulsanti) nell'app. Verrà visualizzato il flusso di lavoro Button -> Update a device (Pulsante > Aggiorna un dispositivo). Immettere gli input e visualizzare il dispositivo aggiornato in IoT Central.

## <a name="delete-a-device-in-a-workflow"></a>Eliminare un dispositivo in un flusso di lavoro

È possibile eliminare un dispositivo usando l'ID dispositivo tramite l'azione **Azure IoT Central - Delete a device** (Azure IoT Central - Elimina un dispositivo). Segue un flusso di lavoro di esempio che consente di eliminare un dispositivo quando si preme un pulsante nell'app per dispositivi mobili Microsoft Flow.

   ![Flusso di lavoro di eliminazione dispositivo di Microsoft Flow](./media/howto-add-microsoft-flow-experimental/flowdeletedevice.png)
    
## <a name="troubleshooting"></a>risoluzione dei problemi

Se si incontrano difficoltà a creare una connessione al connettore di Azure IoT Central, leggere questi suggerimenti.

1. Gli account personali Microsoft (ad esempio i domini @hotmail.com, @live.com, @outlook.com) al momento non sono supportati. È obbligatorio usare un account AAD aziendale o dell'istituto di istruzione.

2. Per usare il connettore IoT Central in Microsoft Flow, è necessario avere effettuato l'accesso all'applicazione IoT Central almeno una volta. In caso contrario, l'applicazione non sarà più visualizzata nel menu a discesa Applicazione.

3. Se si riceve un errore quando si utilizza un account AAD, provare ad aprire Windows PowerShell e ad eseguire i seguenti cmdlet come amministratore.
    ``` PowerShell
    Install-Module AzureAD
    Connect-AzureAD
    New-AzureADServicePrincipal -AppId 9edfcdd9-0bc5-4bd4-b287-c3afc716aac7 -DisplayName "Azure IoT Central"
    ```
    
## <a name="next-steps"></a>Passaggi successivi
Ora che si è appreso come usare Microsoft Flow per creare flussi di lavoro, il passaggio successivo è [Gestire i dispositivi](howto-manage-devices-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).

