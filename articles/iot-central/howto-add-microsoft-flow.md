---
title: Creare flussi di lavoro con il connettore IoT Central di Azure in Microsoft Flow | Microsoft Docs
description: Usare il connettore IoT Central in Microsoft Flow ai flussi di lavoro di trigger e creare, ottenere, aggiornare, eliminare i dispositivi ed eseguire i comandi nei flussi di lavoro.
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 04/25/2019
ms.topic: conceptual
ms.service: iot-central
manager: hegate
ms.openlocfilehash: c0a03b70c6e5e4742e03d4892b2b5f97c908ab9c
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2019
ms.locfileid: "65467926"
---
# <a name="build-workflows-with-the-iot-central-connector-in-microsoft-flow"></a>Creare flussi di lavoro con il connettore IoT Central in Microsoft Flow

*Questo argomento è rivolto ai responsabili della compilazione e agli amministratori.*

Usare Microsoft Flow per automatizzare i flussi di lavoro nelle molte applicazioni e i vari servizi di cui si servono gli utenti aziendali. Grazie al connettore IoT Central in Microsoft Flow è possibile attivare flussi di lavoro quando una regola viene attivata in IoT Central. In un flusso di lavoro attivato da IoT Central o qualsiasi altra applicazione, è possibile usare le azioni nel connettore IoT Central per:
- Crea un dispositivo
- Ottenere le informazioni sul dispositivo
- Aggiornare le proprietà e le impostazioni di un dispositivo
- Eseguire un comando in un dispositivo
- Eliminare un dispositivo

Consultare [questi modelli di Microsoft Flow](https://aka.ms/iotcentralflowtemplates) che connettono IoT Central ad altri servizi, quali le notifiche per dispositivi mobili e Microsoft Teams.

## <a name="prerequisites"></a>Prerequisiti

- Un'applicazione con pagamento in base al consumo
- Un account Microsoft personale, aziendale o dell'istituto di istruzione per eseguire l'accesso a Flow ([altre informazioni sui piani di Microsoft Flow](https://aka.ms/microsoftflowplans))

## <a name="trigger-a-workflow"></a>Attivare un flusso di lavoro

Questa sezione illustra come attivare una notifica per dispositivi mobili nell'app per dispositivi mobili Microsoft Flow quando una regola viene attivata in IoT Central. È possibile compilare l'intero flusso di lavoro all'interno dell'app IoT Central usando la finestra di progettazione incorporata di Microsoft Flow.

1. Iniziare con la [creazione di una regola in IoT Central](howto-create-telemetry-rules.md). Dopo aver salvato le condizioni della regola, selezionare la **azione di Microsoft Flow** come una nuova azione. Verrà aperta una finestra di dialogo per configurare il flusso di lavoro. L'account utente IoT Central che è eseguito l'accesso verrà usato per accedere a Microsoft Flow.

    ![Creare una nuova azione di Microsoft Flow](media/howto-add-microsoft-flow/createflowaction.png)

1. Verrà visualizzato un elenco di flussi di lavoro che possono accedere a e sono associati a questa regola IoT Central. Fare clic su **Esplora modelli** oppure **nuovo > Crea da modello** ed è possibile scegliere da uno qualsiasi dei modelli disponibili. 

    ![Modelli disponibili di Microsoft Flow](media/howto-add-microsoft-flow/flowtemplates1.png)

1. Verrà richiesto di accedere ai connettori nel modello che scelto. Una volta che i connettori si sarà connesso, viene visualizzata nella finestra di progettazione per compilare il flusso di lavoro. Il flusso di lavoro include un trigger di IoT Central in cui l'applicazione e la regola sono già inserite.

1. È possibile personalizzare il flusso di lavoro personalizzando le informazioni passate per l'azione e l'aggiunta di nuove azioni. In questo esempio, l'azione è **notifiche - Inviami una notifica sul dispositivo mobile**. È possibile includere *contenuto dinamico* proveniente dalla regola di IoT Central per trasmettere alla notifica informazioni importanti, quali il nome del dispositivo e il timestamp.

    > [!NOTE]
    > Selezionare il **Vedi altro** testo nella finestra del contenuto dinamico per ottenere valori di misura e proprietà che ha attivato la regola.

    ![Azione di modifica del flusso con il pannello dinamico aperto](./media/howto-add-microsoft-flow/flowdynamicpane1.png)

1. Al termine dell'azione di modifica, selezionare **salvare**. Si verrà riportati alla pagina di panoramica del flusso di lavoro. Qui è possibile visualizzare la cronologia delle esecuzioni e condividerla con i colleghi.

    > [!NOTE]
    > Se si desidera che altri utenti dell'app IoT Central siano autorizzati a modificare la regola, è necessario condividerla con loro in Microsoft Flow. Aggiungere i loro account di Microsoft Flow nel flusso di lavoro configurandoli come proprietari.

1. Se si torna all'App IoT Central, si noterà che questa regola presenta un'azione di Microsoft Flow nell'area di azioni.

È anche possibile creare flussi di lavoro usando il connettore IoT Central direttamente da Microsoft Flow. È quindi possibile scegliere quale app IoT Central a cui connettersi.

## <a name="create-a-device-in-a-workflow"></a>Creare un dispositivo in un flusso di lavoro

Questa sezione illustra come creare un nuovo dispositivo in IoT Central quando si preme un pulsante di un dispositivo mobile usando l'app per dispositivi mobili Microsoft Flow. È possibile usare questa azione in Microsoft Flow per integrare i sistemi ERP, come Dynamic,s con IoT Central creando un nuovo dispositivo quando un dispositivo viene aggiunto altrove.

1. Iniziare creando un flusso di lavoro vuoto in Microsoft Flow.

1. Cercare il trigger **Pulsante Flusso per dispositivi mobili**.

1. In questo trigger aggiungere un input di testo denominato **Nome dispositivo**. Modificare il testo della descrizione in **Immettere il nome del nuovo dispositivo**.

1. Aggiungere una nuova azione. Cercare l'azione **Azure IoT Central - Create a device** (Azure IoT Central - Crea un dispositivo).

1. Selezionare l'applicazione e scegliere un modello di dispositivo in base al quale creare un dispositivo negli elenchi a discesa. L'azione si espande per mostrare tutte le proprietà e le impostazioni del dispositivo.

1. Selezionare il campo Nome dispositivo. Dal panello del contenuto dinamico scegliere **Nome dispositivo**. Questo valore viene passato dall'input l'utente immette tramite l'app per dispositivi mobili ed è il nome del nuovo dispositivo di IoT Central. In questo esempio, l'unico campo obbligatorio è il nome del dispositivo, indicato dall'asterisco rosso. Un modello di dispositivo diverso potrebbe avere più campi obbligatori da compilare per la creazione di un nuovo dispositivo.

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
    > **È necessario usare l'ID trovato nell'URL** nella pagina dei dettagli dispositivo del dispositivo da aggiornare. L'ID dispositivo rilevato nell'elenco del device explorer di dispositivi non è quello corretto da usare in Microsoft Flow.

    ![IoT Central ID dall'URL](./media/howto-add-microsoft-flow/iotcdeviceidurl.png)

1. È possibile aggiornare il nome del dispositivo. Per aggiornare le proprietà e le impostazioni del dispositivo, è necessario selezionare il modello del dispositivo da aggiornare nell'elenco a discesa **Device Template** (Modello di dispositivo). Il riquadro Azioni si espande per visualizzare tutte le proprietà e le impostazioni che è possibile aggiornare.

    ![Flusso di lavoro di aggiornamento dispositivo di Microsoft Flow](./media/howto-add-microsoft-flow/flowupdatedevice1.png)

1. Selezionare tutte le proprietà e le impostazioni che si desidera aggiornare. Dal riquadro del contenuto dinamico, scegliere l'input corrispondente dal trigger. In questo esempio, il valore Location (Posizione) viene propagato verso il basso per aggiornare la proprietà Location del dispositivo.

1. Per finire, salvare il flusso di lavoro.

1. Provare il flusso di lavoro nell'app per dispositivi mobili Microsoft Flow. Andare alla scheda **Buttons** (Pulsanti) nell'app. Verrà visualizzato il flusso di lavoro Button -> Update a device (Pulsante > Aggiorna un dispositivo). Immettere gli input e visualizzare il dispositivo aggiornato in IoT Central.

## <a name="get-device-information-in-a-workflow"></a>Ottenere le informazioni sul dispositivo in un flusso di lavoro

È possibile ottenere le informazioni sul dispositivo dal relativo ID usando il **Azure IoT Central - ottenere un dispositivo** azione. 
> [!NOTE] 
> **È necessario usare l'ID trovato nell'URL** nella pagina dei dettagli dispositivo del dispositivo da aggiornare. L'ID dispositivo rilevato nell'elenco del device explorer di dispositivi non è quello corretto da usare in Microsoft Flow.

È possibile ottenere informazioni quali nome del dispositivo, nome del modello di dispositivo, i valori delle proprietà e i valori delle impostazioni da passare all'azione successiva nel flusso di lavoro. Ecco un flusso di lavoro di esempio che passano lungo il valore della proprietà nome del cliente da un dispositivo per Microsoft Teams.

   ![Flusso di lavoro di flusso get dispositivi](./media/howto-add-microsoft-flow/flowgetdevice1.png)


## <a name="run-a-command-on-a-device-in-a-workflow"></a>Eseguire un comando in un dispositivo in un flusso di lavoro
È possibile eseguire un comando in un dispositivo specificato dal relativo ID usando il **Azure IoT Central - eseguire un comando** azione. 

> [!NOTE] 
> **È necessario usare l'ID trovato nell'URL** nella pagina dei dettagli dispositivo del dispositivo da aggiornare. L'ID dispositivo rilevato nell'elenco del device explorer di dispositivi non è quello corretto da usare in Microsoft Flow.
    
È possibile scegliere il comando per eseguire e passare i parametri del comando tramite questa azione. Ecco un flusso di lavoro di esempio che esegue un comando di riavvio del dispositivo da un pulsante nell'app per dispositivi mobili Microsoft Flow.

   ![Flusso di lavoro di flusso get dispositivi](./media/howto-add-microsoft-flow/flowrunacommand1.png)

## <a name="delete-a-device-in-a-workflow"></a>Eliminare un dispositivo in un flusso di lavoro

È possibile eliminare un dispositivo dal relativo ID usando il **Azure IoT Central - eliminare un dispositivo** azione. 
> [!NOTE] 
> **È necessario usare l'ID trovato nell'URL** nella pagina dei dettagli dispositivo del dispositivo da aggiornare. L'ID dispositivo rilevato nell'elenco del device explorer di dispositivi non è quello corretto da usare in Microsoft Flow.

Segue un flusso di lavoro di esempio che consente di eliminare un dispositivo quando si preme un pulsante nell'app per dispositivi mobili Microsoft Flow.

   ![Flusso di lavoro di eliminazione dispositivo di Microsoft Flow](./media/howto-add-microsoft-flow/flowdeletedevice.png)

## <a name="troubleshooting"></a>risoluzione dei problemi

Se si incontrano difficoltà a creare una connessione al connettore di Azure IoT Central, leggere questi suggerimenti.

1. Gli account personali Microsoft (ad esempio i domini @hotmail.com, @live.com, @outlook.com) al momento non sono supportati. È necessario usare un Azure Active Directory (AD) o dell'istituto di istruzione.

2. Per usare il connettore IoT Central in Microsoft Flow, è necessario avere effettuato l'accesso all'applicazione IoT Central almeno una volta. In caso contrario, l'applicazione non sarà più visualizzata nel menu a discesa Applicazione.

3. Se si riceve un errore durante l'utilizzo di un account Azure AD, provare ad aprire Windows PowerShell ed eseguire i seguenti cmdlet come amministratore.

    ``` PowerShell
    Install-Module AzureAD
    Connect-AzureAD
    New-AzureADServicePrincipal -AppId 9edfcdd9-0bc5-4bd4-b287-c3afc716aac7 -DisplayName "Azure IoT Central"
    ```

## <a name="next-steps"></a>Passaggi successivi

Dopo avere appreso come usare Microsoft Flow per creare flussi di lavoro, il passaggio successivo consigliato consiste [gestire i dispositivi](howto-manage-devices.md).

