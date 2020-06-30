---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 72065ce602c6d29255a3500e26d8c6f36a19ebed
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/19/2020
ms.locfileid: "85095508"
---
### <a name="register-your-ios-app-for-push-notifications"></a>Registrare l'app iOS per le notifiche push

Per inviare notifiche push a un'app per iOS, registrare l'applicazione con Apple ed eseguire un'altra registrazione per abilitare le notifiche push.  

1. Se l'app non è stata ancora registrata, passare al [portale di provisioning iOS](https://go.microsoft.com/fwlink/p/?LinkId=272456) in Apple Developer Center. Accedere al portale con l'ID Apple, passare a **Certificates, Identifiers & Profiles** (Certificati, identificatori e profili) e quindi selezionare **Identifiers** (Identificatori). Fare clic su **+** per registrare una nuova app.

    ![Pagina ID app del portale di provisioning di iOS](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-ios-appids.png)

1. Nella schermata **Register a New Identifier** (Registra un nuovo identificatore) selezionare il pulsante di opzione **App IDs** (ID app). Selezionare quindi **Continua**.

    ![Pagina di registrazione di un nuovo ID nel portale di provisioning di iOS](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-ios-appids-new.png)

1. Aggiornare i tre valori seguenti per la nuova app e quindi selezionare **Continue** (Continua).

   * **Descrizione**: digitare un nome descrittivo per l'app.

   * **Bundle ID**: immettere un valore di ID bundle nel formato **com.<identificatore_organizzazione>.<nome_prodotto>** come indicato nella [guida alla distribuzione di app](https://help.apple.com/xcode/mac/current/#/dev91fe7130a). Nello screenshot seguente il valore `mobcat` viene usato come identificatore dell'organizzazione e il valore **PushDemo** viene usato come nome del prodotto.

      ![Pagina di registrazione dell'ID app nel portale di provisioning di iOS](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-new-appid-bundle.png)

   * **Push Notifications** (Notifiche Push): selezionare l'opzione **Push Notifications** (Notifiche push) nella sezione **Capabilities** (Funzionalità).

      ![Modulo per registrare un nuovo ID app](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-new-appid-push.png)

      Questa azione consente di generare l'ID app e richiede all'utente di confermare le informazioni. Fare clic su **Continue** (Continua) e quindi selezionare **Register** per confermare il nuovo ID app.

      ![Conferma nuovo ID app](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-new-appid-register.png)

      Dopo aver selezionato **Register** (Registra), il nuovo ID app viene visualizzato come una voce nella pagina **Certificates, Identifiers & Profiles** (Certificati, identificatori e profili).

1. Nella pagina **Certificates, Identifiers & Profiles** (Certificati, identificatori e profili) individuare la voce della riga dell'ID app creata in **Identifiers** (Identificatori). Selezionare quindi la relativa riga per visualizzare la schermata **Edit your App ID Configuration** (Modifica la configurazione dell'ID app).

#### <a name="creating-a-certificate-for-notification-hubs"></a>Creazione di un certificato per Hub di notifica

Per garantire il funzionamento dell'hub di notifica con **Apple Push Notification Services (APNS)** è necessario un certificato, che può essere fornito in uno dei due modi seguenti:

1. [Creazione di un certificato push p12 che può essere caricato direttamente in Hub di notifica](#option-1-creating-a-p12-push-certificate-that-can-be-uploaded-directly-to-notification-hub) (*approccio originale*)

1. [Creazione di un certificato p8 che può essere usato per l'autenticazione basata su token](#option-2-creating-a-p8-certificate-that-can-be-used-for-token-based-authentication) (*approccio consigliato più recente*)

L'approccio più recente presenta diversi vantaggi, come documentato in [Autenticazione basata su token (HTTP/2) per APNS](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-http2-token-authentification). Oltre a richiedere un minor numero di passaggi, è una scelta obbligata per scenari specifici. Sono però state fornite le procedure per entrambi gli approcci, perché sono ugualmente applicabili ai fini di questa esercitazione.

##### <a name="option-1-creating-a-p12-push-certificate-that-can-be-uploaded-directly-to-notification-hub"></a>OPZIONE 1: Creazione di un certificato push p12 che può essere caricato direttamente in Hub di notifica

1. Sul Mac eseguire lo strumento Accesso Portachiavi. Può essere aperto dalla cartella **Utilities** (Utility) o **Other** (Altro) nella finestra di avvio.

1. Selezionare **Accesso Portachiavi**, espandere **Certificate Assistant** (Assistente certificati) e quindi selezionare **Request a Certificate from a Certificate Authority** (Richiedi certificato da Autorità di certificazione).

    ![Usare lo strumento Accesso Portachiavi per richiedere un nuovo certificato](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)

   > [!NOTE]
   > Per impostazione predefinita, lo strumento seleziona la prima voce dell'elenco. Ciò potrebbe causare problemi se la prima voce dell'elenco nella sezione **Certificates** (Certificati) non è **Apple Worldwide Developer Relations Certification Authority** (Autorità di certificazione relazioni sviluppatori Apple a livello mondiale). Prima di generare la richiesta di firma del certificato, verificare che sia presente una voce non di chiave oppure che sia selezionata la chiave **Apple Worldwide Developer Relations Certification Authority**.

1. Selezionare **User Email Address** (Indirizzo di posta elettronica utente), immettere il valore **Common Name** (Nome comune), assicurarsi di specificare **Saved to disk** (Salvata su disco) e quindi selezionare **Continue** (Continua). Lasciare vuoto il campo **CA Email Address** (Indirizzo di posta elettronica CA), in quanto non è obbligatorio.

    ![Informazioni sul certificato previste](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-csr-info.png)

1. Immettere un nome per il **file di richiesta di firma del certificato (CSR)** in **Salva con nome**, selezionare il percorso in **Where** (Dove) e quindi selezionare **Save** (Salva).

    ![Scegliere un nome file del certificato](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-save-csr.png)

    Questa azione consente di salvare il **file CSR** nel percorso selezionato. Il percorso predefinito è **Desktop**. Tenere a mente il percorso scelto per il file.

1. Tornare alla pagina **Certificates, Identifiers & Profiles** (Certificati, identificatori e profili) nel [portale di provisioning iOS](https://go.microsoft.com/fwlink/p/?LinkId=272456), scorrere verso il basso fino all'opzione **Push Notifications** (Notifiche push) selezionata e quindi selezionare **Configure** (Configura) per creare il certificato.

    ![Pagina Edit App ID](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-edit-appid.png)

1. Verrà visualizzata la finestra **Apple Push Notification service TLS/SSL Certificates** (Certificati TLS/SSL di Apple Push Notification Service). Fare clic sul pulsante **Create Certificate** (Crea certificato) nella sezione **Development TLS/SSL Certificate** (Certificato TLS/SSL per lo sviluppo).

    ![Pulsante Crea certificato per l'ID app](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

    Verrà visualizzata la schermata **Create a new Certificate** (Crea un nuovo certificato).

    > [!NOTE]
    > Questa esercitazione usa un certificato di sviluppo. La stessa procedura viene usata per registrare un certificato di produzione. Per l'invio delle notifiche, assicurarsi di usare lo stesso tipo di certificato.

1. Selezionare **Choose File** (Scegli file), passare al percorso in cui è stato salvato il **file CSR** e quindi fare doppio clic sul nome del certificato per caricarlo. Selezionare quindi **Continua**.

1. Dopo che il portale avrà creato il certificato, fare clic sul pulsante **Download** (Scarica). Salvare il certificato e prendere nota del percorso di salvataggio.

    ![Pagina di download per il certificato generato](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

    Il certificato viene scaricato e salvato nel computer nella cartella **Download**.

    ![Trovare il file del certificato nella cartella Download](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

    > [!NOTE]
    > Per impostazione predefinita, il certificato di sviluppo scaricato viene denominato **aps_development.cer**.

1. Fare doppio clic sul certificato push scaricato **aps_development.cer**. Questa azione consente di installare il nuovo certificato in Keychain, come illustrato nell'immagine seguente:

    ![Elenco di certificati di Accesso Portachiavi con il nuovo certificato](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

    > [!NOTE]
    > Anche potrebbe essere diverso, se il nome del certificato verrà preceduto da **Apple Development iOS Push Services** (Servizi push iOS per lo sviluppo Apple) e sarà associato all'identificatore di bundle appropriato.

1. In Accesso Portachiavi premere **CTRL** + **clic** sul nuovo certificato push creato nella categoria **Certificates** (Certificati). Selezionare **Export** (Esporta), assegnare un nome al file, selezionare il formato **p12** e quindi selezionare **Save** (Salva).

    ![Esportare il certificato come formato p12](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-export-cert-p12.png)

    Facoltativamente è possibile scegliere di proteggere il certificato tramite una password. Fare clic su **OK** se si desidera ignorare la creazione della password. Prendere nota del nome del file e del percorso del certificato p12 esportato. Verranno usati per abilitare l'autenticazione con Apple Push Notification Service.

    > [!NOTE]
    > Il nome e il percorso del file p12 potrebbero essere diversi rispetto a quelli illustrati in questa esercitazione.

##### <a name="option-2-creating-a-p8-certificate-that-can-be-used-for-token-based-authentication"></a>OPZIONE 2: Creazione di un certificato p8 che può essere usato per l'autenticazione basata su token

1. Prendere nota dei dettagli seguenti:

    * **App ID Prefix** (Prefisso ID app) (**Team ID**) (ID team)
    * **Bundle ID** (ID bundle)

1. In **Certificates, Identifiers & Profiles** (Certificati, identificatori e profili) fare clic su **Keys** (Chiavi).

   > [!NOTE]
   > Se è già stata configurata una chiave per **APNS**, è possibile riutilizzare il certificato p8 scaricato subito dopo la creazione. In tal caso, è possibile ignorare i passaggi da **3** a **5**.

1. Per creare una nuova chiave, fare clic sul pulsante **+** o sul pulsante **Create a key** (Crea una chiave).
1. Specificare un valore appropriato in **Key Name** (Nome chiave) e quindi selezionare l'opzione **Apple Push Notifications service (APNS)** e quindi fare clic su **Continue** (Continua), seguito da **Register** (Registra) nella schermata successiva.
1. Fare clic su **Download** (Scarica) e quindi spostare il file **p8** (e prefisso *AuthKey_* ) in una directory locale sicura, infine fare clic su **Done** (Fine).

   > [!NOTE]
   > Assicurarsi di conservare il file p8 in un luogo sicuro e salvare un backup. Dopo aver scaricato la chiave, non è possibile scaricarla di nuovo perché la copia del server è stata rimossa.
  
1. In **Keys** (Chiavi) fare clic sulla chiave creata (oppure su una chiave esistente se si è scelto di usare tale chiave).
1. Prendere nota del valore di **Key ID** (ID chiave).
1. Aprire il certificato p8 in un'applicazione appropriata di propria scelta, ad esempio [**Visual Studio Code**](https://code.visualstudio.com). Prendere nota del valore della chiave, ovvero del valore compreso tra **-----BEGIN PRIVATE KEY-----** and **-----END PRIVATE KEY-----** .

    -----BEGIN PRIVATE KEY-----  
    <valore_chiave>  
    -----END PRIVATE KEY-----

    > [!NOTE]
    > Si tratta del **valore del token** che verrà usato in seguito per configurare **Hub di notifica**.

Al termine di questa procedura, è necessario disporre delle informazioni seguenti per usarle in un secondo momento in [Configurare l'hub di notifica con le informazioni del servizio APNS](#configure-your-notification-hub-with-apns-information):

* **Team ID** (ID team) (vedere il passaggio 1)
* **Bundle ID** (ID bundle) (vedere il passaggio 1)
* **Key ID** (ID chiave) (vedere il passaggio 7)
* **Valore token** (Valore token) (valore della chiave p8 ottenuto nel passaggio 8)

### <a name="create-a-provisioning-profile-for-the-app"></a>Creare un profilo di provisioning per l'app

1. Tornare a [iOS Provisioning Portal](https://go.microsoft.com/fwlink/p/?LinkId=272456) (Portale di provisioning di iOS), selezionare **Certificates, Identifiers & Profiles** (Certificati, identificatori e profili), quindi **Profiles** (Profili) dal menu a sinistra e infine selezionare **+** per creare un nuovo profilo. Verrà visualizzata la schermata **Register a New Provisioning Profile** (Registra un nuovo profilo di provisioning).

1. In **Development** (Sviluppo) selezionare **iOS App Development** (Sviluppo app iOS) come tipo di profilo di provisioning e quindi **Continue** (Continua).

    ![Elenco profili di provisioning](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

1. Selezionare quindi l'ID app creata nell'elenco a discesa **App ID** (ID app) e selezionare **Continue** (Continua).

    ![Selezionare l'ID app](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)

1. Nella finestra **Select certificates** (Seleziona certificati) selezionare il certificato di sviluppo usato per la firma del codice e quindi fare clic su **Continue** (Continua).

    > [!NOTE]
    > Questo non è il certificato push creato nel [passaggio precedente](#creating-a-certificate-for-notification-hubs). Si tratta invece del certificato di sviluppo. Se non ne esiste uno, è necessario crearlo perché è un [prerequisito](#prerequisites) di questa esercitazione. È possibile creare certificati di sviluppo nel [portale Apple Developer](https://developer.apple.com), tramite [Xcode](https://developer.apple.com/library/archive/documentation/ToolsLanguages/Conceptual/YourFirstAppStoreSubmission/ProvisionYourDevicesforDevelopment/ProvisionYourDevicesforDevelopment.html) o in [Visual Studio](https://docs.microsoft.com/xamarin/ios/get-started/installation/device-provisioning/).

1. Tornare alla pagina **Certificates, Identifiers & Profiles** (Certificati, identificatori e profili), selezionare **Profiles** (Profili) dal menu a sinistra e infine selezionare **+** per creare un nuovo profilo. Verrà visualizzata la schermata **Register a New Provisioning Profile** (Registra un nuovo profilo di provisioning).

1. Nella finestra **Select certificates** (Seleziona certificati) selezionare il certificato di sviluppo creato. Selezionare quindi **Continua**.

1. Selezionare quindi i dispositivi da usare per il test e selezionare **Continue** (Continua).

1. Scegliere infine un nome per il profilo in **Provisioning Profile Name** (Nome profilo di provisioning) e selezionare **Generate** (Genera).

    ![Scegliere un nome del profilo di provisioning](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)

1. Quando viene creato il nuovo profilo di provisioning, selezionare **Download**. Prendere nota del percorso di salvataggio.

1. Passare al percorso del profilo di provisioning e quindi fare doppio clic su di esso per installarlo nel computer di sviluppo.
