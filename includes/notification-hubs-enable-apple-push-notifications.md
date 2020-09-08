---
title: File di inclusione
description: File di inclusione
services: notification-hubs
author: sethmanheim
ms.service: notification-hubs
ms.topic: include
ms.date: 02/10/2020
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: 1cb7753f54e9c1334e35635c227f776041631f1d
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/25/2020
ms.locfileid: "88864850"
---
## <a name="generate-the-certificate-signing-request-file"></a>Generare il file della richiesta di firma del certificato

Apple Push Notification Service (servizio APN) usa i certificati per autenticare le notifiche push. Seguire queste istruzioni per creare il certificato push necessario per inviare e ricevere notifiche. Per altre informazioni su questi concetti, vedere la documentazione ufficiale [Apple Push Notification Service](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html).

Generare il file della richiesta di firma del certificato usato da Apple per la generazione di un certificato push firmato.

1. Sul Mac eseguire lo strumento Accesso Portachiavi. Può essere aperto dalla cartella **Utilities** (Utility) o **Other** (Altro) nella finestra di avvio.

1. Selezionare **Accesso Portachiavi**, espandere **Certificate Assistant** (Assistente certificati) e quindi selezionare **Request a Certificate from a Certificate Authority** (Richiedi certificato da Autorità di certificazione).

    ![Usare lo strumento Accesso Portachiavi per richiedere un nuovo certificato](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)

   > [!NOTE]
   > Per impostazione predefinita, lo strumento seleziona la prima voce dell'elenco. Ciò potrebbe causare problemi se la prima voce dell'elenco nella sezione **Certificates** (Certificati) non è **Apple Worldwide Developer Relations Certification Authority** (Autorità di certificazione relazioni sviluppatori Apple a livello mondiale). Prima di generare la richiesta di firma del certificato, verificare che sia presente una voce non di chiave oppure che sia selezionata la chiave **Apple Worldwide Developer Relations Certification Authority**.

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

## <a name="creating-a-certificate-for-notification-hubs"></a>Creazione di un certificato per Hub di notifica
Per consentire all'hub di notifica di usare **APNS**, è necessario un certificato. Questa operazione può essere eseguita in uno dei due modi seguenti:

1. Creare un file con estensione **.p12** che può essere caricato direttamente in Hub di notifica.  
2. Creare un file con estensione **.p8** che può essere usato per l'[autenticazione basata su token](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-http2-token-authentification) (*approccio più recente*).

L'approccio più recente presenta diversi vantaggi rispetto all'uso dei certificati, come documentato in [autenticazione basata su token (HTTP/2) per APNS](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-http2-token-authentification). Sono state però fornite le procedure relative a entrambi gli approcci. 

### <a name="option-1-creating-a-p12-push-certificate-that-can-be-uploaded-directly-to-notification-hub"></a>OPZIONE 1: Creazione di un certificato push con estensione .p12 che può essere caricato direttamente in Hub di notifica

1. Scorrere verso il basso fino all'opzione **Push Notifications** (Notifiche push) selezionata, quindi selezionare **Configure** (Configura) per creare il certificato.

    ![Pagina Modifica ID app](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-edit-appid.png)

2. Verrà visualizzata la finestra **Apple Push Notification service SSL Certificates** (Certificati SSL di Apple Push Notification Service). Fare clic sul pulsante **Create Certificate** (Crea certificato) nella sezione **Development SSL Certificate** (Certificato SSL per lo sviluppo).

    ![Pulsante Crea certificato per l'ID app](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

    Verrà visualizzata la schermata **Create a new Certificate** (Crea un nuovo certificato).

    > [!NOTE]
    > Questa esercitazione usa un certificato di sviluppo usato dall'app per generare un token univoco del dispositivo. La stessa procedura viene usata per registrare un certificato di produzione. Per l'invio delle notifiche, assicurarsi di usare lo stesso tipo di certificato.

3. Selezionare **Choose File** (Scegli file), passare al percorso in cui è stato salvato il file CSR creato durante la prima attività e quindi fare doppio clic sul nome del certificato per caricarlo. Selezionare quindi **Continua**.

4. Dopo che il portale avrà creato il certificato, fare clic sul pulsante **Download** (Scarica). Salvare il certificato e prendere nota del percorso di salvataggio.

    ![Pagina di download per il certificato generato](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

    Il certificato viene scaricato e salvato nel computer nella cartella **Download**.

    ![Trovare il file del certificato nella cartella Download](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

    > [!NOTE]
    > Per impostazione predefinita, il certificato di sviluppo scaricato viene denominato **aps_development.cer**.

5. Fare doppio clic sul certificato push scaricato **aps_development.cer**. Questa azione consente di installare il nuovo certificato in Keychain, come illustrato nell'immagine seguente:

    ![Elenco di certificati di Accesso Portachiavi con il nuovo certificato](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

    > [!NOTE]
    > Anche se il nome del certificato potrebbe essere diverso, verrà preceduto da **Apple Development iOS Push Services** (Servizi push iOS per lo sviluppo Apple).

6. In Accesso portachiavi fare clic con il pulsante destro del mouse sul nuovo certificato push creato nella categoria **Certificati** . Selezionare **Export** (Esporta), assegnare un nome al file, selezionare il formato **.p12** e quindi selezionare **Save** (Salva).

    ![Esportare il certificato come formato p12](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-export-cert-p12.png)

    È facoltativamente possibile scegliere di proteggere il certificato tramite una password. Fare clic su **OK** se si desidera ignorare la creazione della password. Prendere nota del nome del file e del percorso del certificato con estensione p12 esportato. Verranno usati per abilitare l'autenticazione con il servizio APN.

    > [!NOTE]
    > Il nome e il percorso del file. P12 potrebbero essere diversi rispetto a quelli illustrati in questa esercitazione.

### <a name="option-2-creating-a-p8-certificate-that-can-be-used-for-token-based-authentication"></a>OPZIONE 2: Creazione di un certificato con estensione .p8 che può essere usato per l'autenticazione basata su token

1. Prendere nota dei dettagli seguenti:

    - **App ID Prefix** (Prefisso ID app); si tratta di un **ID team**
    - **Bundle ID** (ID bundle)
    
2. In **Certificates, Identifiers & Profiles** (Certificati, identificatori e profili) fare clic su **Keys** (Chiavi).

   > [!NOTE]
   > Se è già stata configurata una chiave per **APNS**, è possibile riutilizzare il certificato con estensione .p8 scaricato subito dopo la creazione. In tal caso, è possibile ignorare i passaggi da **3** a **5**.

3. Per creare una nuova chiave, fare clic sul pulsante **+** o sul pulsante **Create a key** (Crea una chiave).
4. Specificare un valore appropriato in **Key Name** (Nome chiave) e quindi selezionare l'opzione **Apple Push Notifications service (APNs)** e quindi fare clic su **Continue** (Continua), seguito da **Register** (Registra) nella schermata successiva.
5. Fare clic su **Download** (Scarica) e quindi spostare il file con estensione **.p8** (e prefisso *AuthKey_* ) in una directory locale sicura, infine fare clic su **Done** (Fine).

   > [!NOTE] 
   > Assicurarsi di conservare il file con estensione .p8 in un luogo sicuro e salvare un backup. Dopo aver scaricato la chiave, non è possibile scaricarla di nuovo perché la copia del server è stata rimossa.
  
6. In **Keys** (Chiavi) fare clic sulla chiave appena creata (oppure su una chiave esistente se si è scelto di usare tale chiave).
7. Prendere nota del valore di **Key ID** (ID chiave).
8. Aprire il certificato con estensione .p8 in un'applicazione appropriata di propria scelta, ad esempio [**Visual Studio Code**](https://code.visualstudio.com) e quindi prendere nota del valore della chiave, ovvero del valore compreso tra **-----BEGIN PRIVATE KEY-----** e **-----END PRIVATE KEY-----** .

    ```
    -----BEGIN PRIVATE KEY-----
    <key_value>
    -----END PRIVATE KEY-----
    ```

    > [!NOTE]
    > Si tratta del **valore del token** che verrà usato in seguito per configurare **Hub di notifica**. 

Al termine di questa procedura, è necessario disporre delle informazioni seguenti per usarle in un secondo momento in [Configurare l'hub di notifica con le informazioni del servizio APN](#configure-your-notification-hub-with-apns-information):

- **Team ID** (ID team) (vedere il passaggio 1)
- **Bundle ID** (ID bundle) (vedere il passaggio 1)
- **Key ID** (ID chiave) (vedere il passaggio 7)
- **Token value** (Valore del token), ad esempio il valore della chiave con estensione .p8 (vedere il passaggio 8)

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

In questa sezione viene creato un hub di notifica e viene configurata l'autenticazione con il servizio APN usando il certificato push con estensione p12 o l'autenticazione basata su token. Se si vuole usare un hub di notifica che è già stato creato, è possibile ignorare il passaggio 5.

[!INCLUDE [notification-hubs-portal-create-new-hub](notification-hubs-portal-create-new-hub.md)]

## <a name="configure-your-notification-hub-with-apns-information"></a>Configurare l'hub di notifica con le informazioni del servizio APN

In **Notification Services** (Servizi di notifica) selezionare **Apple (APNS)** e quindi seguire i passaggi appropriati in base all'approccio scelto in precedenza nella sezione [Creazione di un certificato per Hub di notifica](#creating-a-certificate-for-notification-hubs).  

> [!NOTE]
> Se si compila l'app con un profilo di distribuzione ad hoc o App Store, usare **Produzione** per **Modalità applicazione**. Ciò consentirà al dispositivo di inviare notifiche push agli utenti che hanno acquistato l'app dallo Store.

### <a name="option-1-using-a-p12-push-certificate"></a>OPZIONE 1: Uso di un certificato push con estensione .p12

1. Selezionare **Certificate**.

1. Selezionare l'icona del file.

1. Selezionare il file con estensione p12 esportato in precedenza e quindi selezionare **Open** (Apri).

1. Se necessario, specificare la password corretta.

1. Selezionare la modalità **Sandbox**.

    ![Configurare il certificato APN nel portale di Azure](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-apple-config-cert.png)

1. Selezionare **Salva**.

### <a name="option-2-using-token-based-authentication"></a>OPZIONE 2: Uso dell'autenticazione basata su token

1. Selezionare **Token**.
1. Immettere i valori seguenti acquisiti in precedenza:

    - **Key ID** (ID chiave)
    - **Bundle ID** (ID bundle)
    - **Team ID** (ID team)
    - **Token** 

1. Scegliere **Sandbox**
1. Selezionare **Salva**. 

L'hub di notifica è ora configurato con le informazioni del servizio APN. Sono presenti anche le stringhe di connessione per registrare l'app e inviare notifiche push.
