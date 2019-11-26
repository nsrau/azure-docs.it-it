---
title: File di inclusione
description: File di inclusione
services: notification-hubs
author: sethmanheim
ms.service: notification-hubs
ms.topic: include
ms.date: 11/21/2019
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: ef2b98821b28d8a49e5f16bf1c6ac176eb8b5793
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74407186"
---
## <a name="generate-the-certificate-signing-request-file"></a>Generare il file della richiesta di firma del certificato

Apple Push Notification Service (servizio APN) usa i certificati per autenticare le notifiche push. Seguire queste istruzioni per creare il certificato push necessario per inviare e ricevere notifiche. Per altre informazioni su questi concetti, vedere la documentazione ufficiale [Apple Push Notification Service](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html).

Generare il file della richiesta di firma del certificato usato da Apple per la generazione di un certificato push firmato.

1. Sul Mac eseguire lo strumento Accesso Portachiavi. Può essere aperto dalla cartella **Utilities** (Utility) o **Other** (Altro) nella finestra di avvio.

1. Selezionare **Accesso Portachiavi**, espandere **Certificate Assistant** (Assistente certificati) e quindi selezionare **Request a Certificate from a Certificate Authority** (Richiedi certificato da Autorità di certificazione).

    ![Usare lo strumento Accesso Portachiavi per richiedere un nuovo certificato](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)

1. Selezionare **User Email Address** (Indirizzo di posta elettronica utente), immettere il valore **Common Name** (Nome comune), assicurarsi di specificare **Saved to disk** (Salvata su disco) e quindi selezionare **Continue** (Continua). Lasciare vuoto il campo **CA Email Address** (Indirizzo di posta elettronica CA), in quanto non è obbligatorio.

    ![Informazioni sul certificato necessarie](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-csr-info.png)

1. Immettere un nome per il file CSR in **Save As** (Salva con nome), selezionare il percorso in **Where** (Percorso) e quindi selezionare **Save** (Salva).

    ![Scegliere un nome file del certificato](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-save-csr.png)

    Questa azione consente di salvare il file CSR nel percorso selezionato. Il percorso predefinito è **Desktop**. Tenere a mente il percorso scelto per il file.

A questo punto registrare l'app con Apple, abilitare le notifiche push e caricare il file CSR esportato per creare un certificato push.

## <a name="register-your-app-for-push-notifications"></a>Registrare l'app per le notifiche push

Per inviare notifiche push a un'app per iOS, registrare l'applicazione con Apple ed eseguire un'altra registrazione per abilitare le notifiche push.  

1. Se l'app non è stata ancora registrata, passare al [portale di provisioning iOS](https://go.microsoft.com/fwlink/p/?LinkId=272456) in Apple Developer Center. Accedere al portale con l'ID Apple e selezionare **Identifiers** (Identificatori). Selezionare quindi **+** per registrare una nuova app.

    ![Pagina ID app del portale di provisioning di iOS](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids.png)

2. Nella schermata **Register a New Identifier** (Registra un nuovo identificatore) selezionare il pulsante di opzione **App IDs** (ID app). Selezionare quindi **Continua**.

    ![Pagina di registrazione di un nuovo ID nel portale di provisioning di iOS](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids-new.png)

3. Aggiornare i tre valori seguenti per la nuova app e quindi selezionare **Continue** (Continua).

   * **Descrizione**: digitare un nome descrittivo per l'app.

   * **Bundle ID**: immettere un valore di ID bundle nel formato **Organization Identifier.Product Name** come indicato nella [guida alla distribuzione di app](https://help.apple.com/xcode/mac/current/#/dev91fe7130a). I valori *Organization Identifier* (Identificatore organizzazione) e *Product Name* (Nome prodotto) devono corrispondere all'identificatore dell'organizzazione e al nome del prodotto usati nella creazione del progetto Xcode. Nello screenshot seguente il valore **NotificationHubs** viene usato come identificatore dell'organizzazione e il valore **GetStarted** viene usato come nome del prodotto. Assicurarsi che il valore **Bundle Identifier** (Identificatore bundle) corrisponda al valore nel progetto Xcode, affinché Xcode usi il profilo di pubblicazione corretto.

      ![Pagina di registrazione dell'ID app nel portale di provisioning di iOS](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-bundle.png)

   * **Push Notifications** (Notifiche Push): selezionare l'opzione **Push Notifications** (Notifiche push) nella sezione **Capabilities** (Funzionalità).

      ![Modulo per registrare un nuovo ID app](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-push.png)

      Questa azione consente di generare l'ID app e richiede all'utente di confermare le informazioni. Fare clic su **Continue** (Continua) e quindi selezionare **Register** per confermare il nuovo ID app.

      ![Conferma nuovo ID app](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-register.png)

      Dopo aver selezionato **Register** (Registra), il nuovo ID app viene visualizzato come una voce nella pagina **Certificates, Identifiers & Profiles** (Certificati, identificatori e profili).

4. Nella pagina **Certificates, Identifiers & Profiles** (Certificati, identificatori e profili), in **Identifiers** (Identificatori) individuare la voce relativa all'ID app appena creata e selezionare la riga corrispondente per visualizzare la schermata **Edit your App ID Configuration** (Modificare la configurazione dell'ID app).

5. Scorrere verso il basso fino all'opzione **Push Notifications** (Notifiche push) selezionata, quindi selezionare **Configure** (Configura) per creare il certificato.

    ![Pagina Modifica ID app](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-edit-appid.png)

6. Verrà visualizzata la finestra **Apple Push Notification service SSL Certificates** (Certificati SSL di Apple Push Notification Service). Fare clic sul pulsante **Create Certificate** (Crea certificato) nella sezione **Development SSL Certificate** (Certificato SSL per lo sviluppo).

    ![Pulsante Crea certificato per l'ID app](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

    Verrà visualizzata la schermata **Create a new Certificate** (Crea un nuovo certificato).

    > [!NOTE]
    > Questa esercitazione usa un certificato di sviluppo. La stessa procedura viene usata per registrare un certificato di produzione. Per l'invio delle notifiche, assicurarsi di usare lo stesso tipo di certificato.

1. Selezionare **Choose File** (Scegli file), passare al percorso in cui è stato salvato il file CSR creato durante la prima attività e quindi fare doppio clic sul nome del certificato per caricarlo. Selezionare quindi **Continua**.

1. Dopo che il portale avrà creato il certificato, fare clic sul pulsante **Download** (Scarica). Salvare il certificato e prendere nota del percorso di salvataggio.

    ![Pagina di download per il certificato generato](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

    Il certificato viene scaricato e salvato nel computer nella cartella **Download**.

    ![Trovare il file del certificato nella cartella Download](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

    > [!NOTE]
    > Per impostazione predefinita, il certificato di sviluppo scaricato viene denominato **aps_development.cer**.

1. Fare doppio clic sul certificato push scaricato **aps_development.cer**. Questa azione consente di installare il nuovo certificato in Keychain, come illustrato nell'immagine seguente:

    ![Elenco di certificati di Accesso Portachiavi con il nuovo certificato](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

    > [!NOTE]
    > Anche se il nome del certificato potrebbe essere diverso, verrà preceduto da **Apple Development iOS Push Services** (Servizi push iOS per lo sviluppo Apple).

1. In Accesso portachiavi fare clic con il pulsante destro del mouse sul nuovo certificato push creato nella categoria **Certificati** . Selezionare **Export** (Esporta), assegnare un nome al file, selezionare il formato **.p12** e quindi selezionare **Save** (Salva).

    ![Esportare il certificato come formato p12](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-export-cert-p12.png)

    È facoltativamente possibile scegliere di proteggere il certificato tramite una password. Fare clic su **OK** se si desidera ignorare la creazione della password. Prendere nota del nome del file e del percorso del certificato con estensione p12 esportato. Verranno usati per abilitare l'autenticazione con il servizio APN.

    > [!NOTE]
    > Il nome e il percorso del file. P12 potrebbero essere diversi rispetto a quelli illustrati in questa esercitazione.

## <a name="create-a-provisioning-profile-for-the-app"></a>Creare un profilo di provisioning per l'app

1. Tornare a [iOS Provisioning Portal](https://go.microsoft.com/fwlink/p/?LinkId=272456) (Portale di provisioning di iOS), selezionare **Certificates, Identifiers & Profiles** (Certificati, identificatori e profili), quindi **Profiles** (Profili) dal menu a sinistra e infine selezionare **+** per creare un nuovo profilo. Verrà visualizzata la schermata **Register a New Provisioning Profile** (Registra un nuovo profilo di provisioning).

1. In **Development** (Sviluppo) selezionare **iOS App Development** (Sviluppo app iOS) come tipo di profilo di provisioning e quindi **Continue** (Continua).

    ![Elenco profili di provisioning](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

1. Selezionare quindi l'ID app creata nell'elenco a discesa **App ID** (ID app) e selezionare **Continue** (Continua).

    ![Selezionare l'ID app](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)

1. Nella finestra **Select certificates** (Seleziona certificati) selezionare il certificato di sviluppo usato per la firma del codice e quindi fare clic su **Continue** (Continua). Questo non è il certificato push creato. Se non ne esiste uno, è necessario crearlo. Se esiste un certificato, andare al passaggio successivo. Per creare un certificato di sviluppo, se non ne esiste uno:

    1. Se viene visualizzato il messaggio **No Certificates are available** (Nessun certificato disponibile), selezionare **Create Certificate** (Crea certificato).
    2. Nella sezione **Software** selezionare **Apple Development** (Sviluppo Apple). Selezionare quindi **Continua**.
    3. Nella schermata **Create a New Certificate** (Crea un nuovo certificato) selezionare **Choose File** (Scegli file).
    4. Passare al certificato **Certificate Signing Request** (Richiesta di firma del certificato) creato in precedenza, selezionarlo e quindi scegliere **Open** (Apri).
    5. Selezionare **Continua**.
    6. Scaricare il certificato di sviluppo e prendere nota del percorso di salvataggio.

1. Tornare alla pagina **Certificates, Identifiers & Profiles** (Certificati, identificatori e profili), selezionare **Profiles** (Profili) dal menu a sinistra e infine selezionare **+** per creare un nuovo profilo. Verrà visualizzata la schermata **Register a New Provisioning Profile** (Registra un nuovo profilo di provisioning).

1. Nella finestra **Select certificates** (Seleziona certificati) selezionare il certificato di sviluppo appena creato. Selezionare quindi **Continua**.

1. Selezionare quindi i dispositivi da usare per il test e selezionare **Continue** (Continua).

1. Scegliere infine un nome per il profilo in **Provisioning Profile Name** (Nome profilo di provisioning) e selezionare **Generate** (Genera).

    ![Scegliere un nome del profilo di provisioning](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)

1. Quando viene creato il nuovo profilo di provisioning, selezionare **Download**. Prendere nota del percorso di salvataggio.

1. Passare al percorso del profilo di provisioning e quindi fare doppio clic su di esso per installarlo nel computer di sviluppo Xcode.

## <a name="create-a-notification-hub"></a>Creare un hub di notifica

In questa sezione viene creato un hub di notifica e viene configurata l'autenticazione con il servizio APN usando il certificato push con estensione p12 creato in precedenza. Se si vuole usare un hub di notifica che è già stato creato, è possibile ignorare il passaggio 5.

[!INCLUDE [notification-hubs-portal-create-new-hub](notification-hubs-portal-create-new-hub.md)]

## <a name="configure-your-notification-hub-with-apns-information"></a>Configurare l'hub di notifica con le informazioni del servizio APN

1. In **Servizi di notifica** selezionare **Apple (APNS)** .

1. Selezionare **Certificate**.

1. Selezionare l'icona del file.

1. Selezionare il file con estensione p12 esportato in precedenza e quindi selezionare **Open** (Apri).

1. Se necessario, specificare la password corretta.

1. Selezionare la modalità **Sandbox**. Usare la modalità **Production** (Produzione) solo se si vuole inviare notifiche push agli utenti che hanno acquistato l'app dallo Store.

    ![Configurare il certificato APN nel portale di Azure](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-apple-config-cert.png)

1. Selezionare **Salva**.

L'hub di notifica è ora configurato con le informazioni del servizio APN. Sono presenti anche le stringhe di connessione per registrare l'app e inviare notifiche push.
