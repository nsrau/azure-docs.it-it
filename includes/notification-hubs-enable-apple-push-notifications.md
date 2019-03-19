---
title: File di inclusione
description: File di inclusione
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 08/28/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 2fc4f26f187301ea7a7a1e3051038f75da728547
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58125292"
---
## <a name="generate-the-certificate-signing-request-file"></a>Generare il file della richiesta di firma del certificato

Il servizio Apple Push Notification Service (APNS) usa i certificati per autenticare le notifiche push. Seguire queste istruzioni per creare il certificato push necessario per inviare e ricevere notifiche. Per altre informazioni su questi concetti, vedere la documentazione ufficiale [Apple Push Notification Service](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html).

Generare il file della richiesta di firma del certificato usato da Apple per la generazione di un certificato push firmato.

1. Sul Mac eseguire lo strumento **Accesso Portachiavi**. Può essere aperto dalla cartella **Utilities** (Utility) o **Other** (Altro) nella finestra di avvio.
2. Fare clic su **Keychain Access** (Accesso Portachiavi), espandere **Certificate Assistant** (Assistente Certificato), quindi fare clic su **Request a Certificate from a Certificate Authority...** (Richiedi un certificato da una Autorità di Certificazione...).

    ![Usare lo strumento Accesso Portachiavi per richiedere un nuovo certificato](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)
3. Selezionare **User Email Address** (Indirizzo e-mail utente) e **Common Name** (Nome comune), assicurarsi che l'opzione **Saved to disk** (Salvata sul disco) sia selezionata, quindi fare clic su **Continue** (Continua). Lasciare vuoto il campo **Indirizzo e-mail CA** , in quanto non è obbligatorio.

    ![Informazioni sul certificato necessarie](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-csr-info.png)

4. Digitare un nome per il file della richiesta di firma del certificato (CSR) in **Save As**, (Salva col nome), selezionare il percorso in **Where** (Posizione), quindi fare clic su **Save** (Salva).

    ![Scegliere un nome file del certificato](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-save-csr.png)

    Questa operazione consente di salvare il file CSR nel percorso selezionato. Il percorso predefinito è il Desktop. Tenere a mente il percorso scelto per il file.

A questo punto registrare l'app con Apple, abilitare le notifiche push e caricare il file CSR esportato per creare un certificato push.

## <a name="register-your-app-for-push-notifications"></a>Registrare l'app per le notifiche push

Per poter inviare notifiche push a un'app per iOS, è necessario registrare l'applicazione con Apple ed eseguire un'altra registrazione per abilitare le notifiche push.  

1. Se l'app non è ancora stata registrata, accedere al [portale di provisioning iOS](https://go.microsoft.com/fwlink/p/?LinkId=272456) su Apple Developer Center, eseguire l'accesso con il proprio ID Apple, fare clic su **Identifiers**, quindi su **App IDs** e infine fare sul segno **+** per registrare una nuova app.

    ![Pagina ID app del portale di provisioning di iOS](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids.png)

2. Aggiornare i tre campi seguenti per la nuova app e quindi fare clic su **Continue**:

   * **Nome**: Digitare un nome descrittivo per l'app nel **Name** campo le **App ID Description** sezione.
   * **Identificatore del bundle**: Sotto il **ID App esplicito** sezione, immettere una **identificatore del Bundle** nel formato `<Organization Identifier>.<Product Name>` come indicato nella [App Distribution Guide](https://help.apple.com/xcode/mac/current/#/dev91fe7130a). L'*identificatore dell'organizzazione* e il *nome del prodotto* usati devono corrispondere all'identificatore dell'organizzazione e al nome del prodotto usati nella creazione del progetto XCode. Nello screenshot seguente *NotificationHubs* viene usato come identificatore dell'organizzazione e *GetStarted* viene usato come nome del prodotto. Verificare che questi valori corrispondano ai valori usati nel progetto XCode, in modo da usare il profilo di pubblicazione corretto con XCode.
   * **Le notifiche push**: Verificare i **notifiche Push** opzione il **servizi App** sezione.

     ![Modulo per registrare un nuovo ID app](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-info.png)

     Questa azione consente di generare l'ID dell'app e richiede all'utente di confermare le informazioni. Fare clic su **Registra** per confermare il nuovo ID app.

     Dopo aver fatto clic su **Register** (Registrazione) verrà visualizzata la schermata **Registration complete** (Registrazione completata), come mostrato nell'immagine seguente. Fare clic su **Done**.

     ![Registrazione dell'ID app completa con i diritti](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-registration-complete.png)

3. In Developer Center (Centro per sviluppatori), individuare l'ID dell'app appena creata in App IDs (ID app) e fare clic sulla relativa riga.

    ![Elenco ID app](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids2.png)

    Se si fa clic sull'ID dell'app, verranno visualizzati i dettagli relativi all'app. Fare clic sul pulsante **Edit** nella parte inferiore della schermata.

    ![Pagina Modifica ID app](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-edit-appid.png)

4. Scorrere fino alla fine della schermata e fare clic su **Crea Certificato...** nella sezione **Development Push SSL Certificate**.

    ![Pulsante Crea certificato per l'ID app](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

    Verrà visualizzato l'assistente **Add iOS Certificate** (Aggiungi il certificato iOS).

    > [!NOTE]
    > Questa esercitazione usa un certificato di sviluppo. La stessa procedura viene usata per registrare un certificato di produzione. Per l'invio delle notifiche, assicurarsi di usare lo stesso tipo di certificato.

5. Fare clic su **Choose File**, passare al percorso in cui è stato salvato il file CSR creato durante la prima attività, quindi fare clic su **Generate**.

    ![Pagina di caricamento del file CSR per il certificato generato](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-cert-choose-csr.png)

6. Al termine della creazione del certificato nel portale, fare clic su **Download** e quindi su **Done**.

    ![Pagina di download per il certificato generato](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

    Il certificato verrà scaricato e salvato nel computer nella cartella **Download**.

    ![Trovare il file del certificato nella cartella Download](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

    > [!NOTE]
    > Per impostazione predefinita, il file scaricato di un certificato di sviluppo è denominato **aps_development.cer**.

7. Fare doppio clic sul certificato push scaricato **aps_development.cer**.

    Questa azione consente di installare il nuovo certificato in Keychain, come illustrato nell'immagine seguente:

    ![Elenco di certificati di Accesso Portachiavi con il nuovo certificato](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

    > [!NOTE]
    > Il nome del certificato potrebbe essere diverso, ma verrà preceduto da **Apple Development iOS Push Services** (Servizi push iOS per lo sviluppo Apple).

8. In Accesso portachiavi fare clic con il pulsante destro del mouse sul nuovo certificato push creato nella categoria **Certificati** . Fare clic su **Esporta**, assegnare un nome al file, selezionare il formato **.p12** e quindi fare clic su **Salva**.

    ![Esportare il certificato come formato p12](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-export-cert-p12.png)

    Prendere nota del nome del file e del percorso del certificato con estensione p12 esportato. Viene usato per abilitare l'autenticazione con il servizio APN.

    > [!NOTE]
    > In questa esercitazione viene creato un file QuickStart.p12. Il nome e il percorso del file potrebbero essere diversi.

## <a name="create-a-provisioning-profile-for-the-app"></a>Creare un profilo di provisioning per l'app

1. Nel [portale di provisioning iOS](https://go.microsoft.com/fwlink/p/?LinkId=272456) selezionare **Provisioning Profiles**, quindi **All** e infine fare clic sul pulsante **+** (più) per creare un nuovo profilo. Viene visualizzata la procedura guidata **Add iOS Provisioning Profile** (Aggiungi profilo di provisioning iOS):

    ![Elenco profili di provisioning](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

2. **Selezionare iOS App Development** (Sviluppo app iOS) in **Development** (Sviluppo) come tipo di profilo di provisioning e fare clic su **Continue** (Continua).

3. Selezionare quindi l'ID dell'app creata nell'elenco a discesa **App ID** (ID app) e fare clic su **Continue** (Continua)

    ![Selezionare l'ID app](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)

4. Nella schermata **Select certificates** selezionare il solito certificato di sviluppo usato per la firma del codice e fare clic su **Continue**. Questo non è il certificato push creato.

    ![Selezionare il certificato](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-select-cert.png)

5. In **Devices** selezionare i dispositivi da usare per il test e fare clic su **Continue**.

    ![Selezionare i dispositivi](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-select-devices.png)

6. Scegliere infine un nome per il profilo in **Profile Name** e fare clic su **Generate**.

    ![Scegliere un nome del profilo di provisioning](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)

7. Una volta creato il nuovo profilo di provisioning, fare clic per scaricarlo e installarlo nel computer di sviluppo Xcode. Fare quindi clic su **Done**.

    ![Scaricare profilo di provisioning](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-profile-ready.png)
