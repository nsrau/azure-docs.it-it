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
ms.openlocfilehash: c664e73b39ad48a860661cfd9141ee74df203f3e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67116671"
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

1. Se l'app non è stata ancora registrata, passare al [portale di provisioning iOS](https://go.microsoft.com/fwlink/p/?LinkId=272456) in Apple Developer Center. Successivamente, eseguire l'accesso con il proprio ID Apple, selezionare **Identificatori**, selezionare **ID app** e infine selezionare **+** per registrare una nuova app.

    ![Pagina ID app del portale di provisioning di iOS](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids.png)

1. Aggiornare i tre valori seguenti per la nuova app e quindi selezionare **Continue** (Continua).

   * **Nome**: digitare un nome descrittivo per l'app nella casella **Nome** della sezione **App ID Description** (Descrizione ID app).

   * **Bundle Identifier** (Identificatore bundle): nella sezione **Explicit App ID** (ID app esplicito) immettere un valore in **Bundle Identifier** (Identificatore bundle) nel formato `<Organization Identifier>.<Product Name>`, come indicato nella [guida alla distribuzione di app](https://help.apple.com/xcode/mac/current/#/dev91fe7130a). I valori *Organization Identifier* (Identificatore organizzazione) e *Product Name* (Nome prodotto) devono corrispondere all'identificatore dell'organizzazione e al nome del prodotto usati nella creazione del progetto Xcode. Nello screenshot seguente il valore *NotificationHubs* viene usato come identificatore dell'organizzazione e il valore *GetStarted* viene usato come nome del prodotto. Assicurarsi che il valore **Bundle Identifier** (Identificatore bundle) corrisponda al valore nel progetto Xcode, in modo che Xcode userà il profilo di pubblicazione corretto.

   * **Push Notifications** (Notifiche Push): selezionare l'opzione **Push Notifications** (Notifiche Push) nella sezione **App Services** (Servizi app).

     ![Modulo per registrare un nuovo ID app](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-info.png)

     Questa azione consente di generare l'ID app e richiede all'utente di confermare le informazioni. Selezionare **Register** (Registrazione) per confermare il nuovo ID app.

     Dopo aver selezionato **Register** (Registrazione) verrà visualizzata la schermata **Registration complete** (Registrazione completata), come mostrato nell'immagine seguente. Selezionare **Operazione completata**.

     ![Registrazione dell'ID app completa con i diritti](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-registration-complete.png)

1. In Developer Center individuare l'ID app creato in **App IDs** (ID app) e selezionare la relativa riga.

    ![Elenco ID app](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids2.png)

    Selezionare l'ID app per visualizzare i dettagli dell'app e quindi selezionare il pulsante **Edit** (Modifica) in basso.

    ![Pagina Modifica ID app](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-edit-appid.png)

1. Scorrere fino alla fine della schermata e selezionare il pulsante **Create Certificate** (Crea certificato) nella sezione **Development Push SSL Certificate** (Certificato SSL push di sviluppo).

    ![Pulsante Crea certificato per l'ID app](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

    Verrà visualizzato ora l'assistente **Add iOS Certificate** (Aggiungi il certificato iOS).

    > [!NOTE]
    > Questa esercitazione usa un certificato di sviluppo. La stessa procedura viene usata per registrare un certificato di produzione. Per l'invio delle notifiche, assicurarsi di usare lo stesso tipo di certificato.

1. Selezionare **Choose File** (Scegli file), passare al percorso in cui è stato salvato il file CSR creato durante la prima attività e quindi selezionare **Generate** (Genera).

    ![Pagina di caricamento del file CSR per il certificato generato](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-cert-choose-csr.png)

1. Dopo che il portale avrà creato il certificato, selezionare il pulsante **Download** (Scarica) e quindi selezionare **Done** (Fine).

    ![Pagina di download per il certificato generato](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

    Il certificato viene scaricato e salvato nel computer nella cartella **Download**.

    ![Trovare il file del certificato nella cartella Download](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

    > [!NOTE]
    > Per impostazione predefinita, il certificato di sviluppo scaricato viene denominato **aps_development.cer**.

1. Selezionare il certificato push scaricato **aps_development.cer**.

    Questa azione consente di installare il nuovo certificato in Keychain, come illustrato nell'immagine seguente:

    ![Elenco di certificati di Accesso Portachiavi con il nuovo certificato](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

    > [!NOTE]
    > Anche se il nome del certificato potrebbe essere diverso, verrà preceduto da **Apple Development iOS Push Services** (Servizi push iOS per lo sviluppo Apple).

1. In Accesso portachiavi fare clic con il pulsante destro del mouse sul nuovo certificato push creato nella categoria **Certificati** . Selezionare **Export** (Esporta), assegnare un nome al file, selezionare il formato **.p12** e quindi selezionare **Save** (Salva).

    ![Esportare il certificato come formato p12](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-export-cert-p12.png)

    Prendere nota del nome del file e del percorso del certificato con estensione p12 esportato. Verranno usati per abilitare l'autenticazione con il servizio APN.

    > [!NOTE]
    > In questa esercitazione viene creato un file denominato **QuickStart.p12**. Il nome e il percorso del file potrebbero essere diversi.

## <a name="create-a-provisioning-profile-for-the-app"></a>Creare un profilo di provisioning per l'app

1. Nel [portale di provisioning iOS](https://go.microsoft.com/fwlink/p/?LinkId=272456) selezionare **Provisioning Profiles** (Profili di provisioning), selezionare **All** (Tutti) e quindi selezionare **+** per creare un nuovo profilo. Viene visualizzata la procedura guidata **Add iOS Provisioning Profile** (Aggiungi profilo di provisioning iOS).

    ![Elenco profili di provisioning](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

1. Selezionare **iOS App Development** (Sviluppo app iOS) in **Development** (Sviluppo) come tipo di profilo di provisioning e selezionare **Continue** (Continua).

1. Selezionare quindi l'ID app creata nell'elenco a discesa **App ID** (ID app) e selezionare **Continue** (Continua).

    ![Selezionare l'ID app](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)

1. Nella finestra **Select certificates** (Seleziona certificati) selezionare il certificato di sviluppo usato per la firma del codice e selezionare **Continue** (Continua). Questo non è il certificato push creato.

    ![Selezionare il certificato](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-select-cert.png)

1. Selezionare quindi i dispositivi da usare per il test e selezionare **Continue** (Continua).

    ![Selezionare i dispositivi](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-select-devices.png)

1. Scegliere infine un nome per il profilo in **Profile Name** (Nome profilo) e selezionare **Generate** (Genera).

    ![Scegliere un nome del profilo di provisioning](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)

1. Una volta creato il nuovo profilo di provisioning, scegliere di scaricarlo e installarlo nel computer di sviluppo Xcode. Al termine selezionare **Done** (Fine).

    ![Scaricare profilo di provisioning](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-profile-ready.png)

## <a name="create-a-notification-hub"></a>Creare un hub di notifica

In questa sezione viene creato un hub di notifica e viene configurata l'autenticazione con il servizio APN usando il certificato push con estensione p12 creato in precedenza. Se si vuole usare un hub di notifica che è già stato creato, è possibile ignorare il passaggio 5.

[!INCLUDE [notification-hubs-portal-create-new-hub](notification-hubs-portal-create-new-hub.md)]

## <a name="configure-your-notification-hub-with-apns-information"></a>Configurare l'hub di notifica con le informazioni del servizio APN

1. In **Servizi di notifica** selezionare **Apple (APNS)** .

1. Selezionare **Certificate**.

1. Selezionare l'icona del file.

1. Selezionare il file con estensione p12 esportato in precedenza.

1. Specificare la password corretta.

1. Selezionare la modalità **Sandbox**. Usare la modalità **Production** (Produzione) solo se si vuole inviare notifiche push agli utenti che hanno acquistato l'app dallo Store.

    ![Configurare il certificato APN nel portale di Azure](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-apple-config-cert.png)

L'hub di notifica è ora configurato con le informazioni del servizio APN. Sono presenti anche le stringhe di connessione per registrare l'app e inviare notifiche push.
