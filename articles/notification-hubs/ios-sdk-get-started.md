---
title: Inviare notifiche push a iOS con Hub di notifica di Azure e iOS SDK
description: In questa esercitazione si apprenderà come usare Hub di notifica di Azure e Apple Push Notification Service per inviare notifiche push a dispositivi iOS.
author: sethmanheim
ms.author: sethm
ms.date: 10/30/2020
ms.topic: tutorial
ms.service: notification-hubs
ms.reviewer: thsomasu
ms.lastreviewed: 06/01/2020
ms.openlocfilehash: c920c9b3b28df7f5bf3bf169ef88ab967f23649e
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93085378"
---
# <a name="tutorial-send-push-notifications-to-ios-apps-using-azure-notification-hubs"></a>Esercitazione: Inviare notifiche push alle app iOS con Hub di notifica di Azure

Questa esercitazione illustra come configurare Hub di notifica di Azure e impostare le credenziali per inviare notifiche push a un dispositivo iOS tramite [Apple Push Notification Service (APNS)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1). 

L'esecuzione di questa esercitazione è un prerequisito per le esercitazioni successive su iOS con Objective C e Swift ed è costituita dai passaggi seguenti:

- Generare il file della richiesta di firma del certificato.
- Richiedere le notifiche push all'app.
- Creare un profilo di provisioning per l'app.
- Creare un hub di notifica.
- Configurare l'hub di notifica con le informazioni del servizio APNS.

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione, è necessario disporre di un account Azure attivo. Se non si dispone di un account Azure, è possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/free/).

Sono necessari anche:

- Un account [Apple Developer](https://developer.apple.com/) attivo.
- Un Mac che esegue [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532), insieme a un certificato dello sviluppatore valido installato nel portachiavi.
- Un iPhone o iPad con iOS versione 10 o successiva.
- Il dispositivo fisico registrato nel [portale Apple](https://developer.apple.com/) e associato al certificato.

Leggere la [panoramica di Hub di notifica di Azure](notification-hubs-push-notification-overview.md) se non si ha familiarità con il servizio.

> [!NOTE]
> L'hub di notifica verrà configurato solo per l'uso della modalità di autenticazione Sandbox. Non usare questa modalità di autenticazione per i carichi di lavoro di produzione.

## <a name="generate-the-certificate-signing-request-file"></a>Generare il file della richiesta di firma del certificato

Il servizio Apple Push Notification Service (APNS) usa i certificati per autenticare le notifiche push. Seguire queste istruzioni per creare il certificato push necessario per inviare e ricevere notifiche. Per altre informazioni su questi concetti, vedere la documentazione ufficiale [Apple Push Notification Service](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html).

Generare il file della richiesta di firma del certificato usato da Apple per la generazione di un certificato push firmato:

1. Sul Mac eseguire lo strumento Accesso Portachiavi. Può essere aperto dalla cartella **Utilities** (Utility) o **Other** (Altro) nella finestra di avvio.

2. Selezionare **Accesso Portachiavi** , espandere **Certificate Assistant** (Assistente certificati) e quindi selezionare **Request a Certificate from a Certificate Authority** (Richiedi certificato da Autorità di certificazione).

   :::image type="content" source="media/ios-sdk-get-started/image1.png" alt-text="Screenshot in cui è evidenziata l'opzione di menu Request a Certificate from a Certificate Authority.":::

   > [!NOTE]
   > Per impostazione predefinita, lo strumento seleziona la prima voce dell'elenco. Ciò potrebbe causare problemi se la prima voce dell'elenco nella sezione **Certificates** (Certificati) non è **Apple Worldwide Developer Relations Certification Authority** (Autorità di certificazione relazioni sviluppatori Apple a livello mondiale). Prima di generare la richiesta di firma del certificato, verificare che sia presente una voce non di chiave oppure che sia selezionata la chiave **Apple Worldwide Developer Relations Certification Authority**.

3. Selezionare **User Email Address** (Indirizzo di posta elettronica utente), immettere il valore **Common Name** (Nome comune), assicurarsi di specificare **Saved to disk** (Salvata su disco) e quindi selezionare **Continue** (Continua). Lasciare vuoto il campo **CA Email Address** (Indirizzo di posta elettronica CA), in quanto non è obbligatorio.

   :::image type="content" source="media/ios-sdk-get-started/image2.png" alt-text="Informazioni sul certificato necessarie":::

4. Immettere un nome per il file CSR in **Save As** (Salva con nome), selezionare il percorso in **Where** (Percorso) e quindi selezionare **Save** (Salva).

   :::image type="content" source="media/ios-sdk-get-started/image3.png" alt-text="Scegliere il nome di file":::

   Questa azione consente di salvare il file CSR nel percorso selezionato. Il percorso predefinito è **Desktop**. Tenere a mente il percorso scelto per il file.

A questo punto registrare l'app con Apple, abilitare le notifiche push e caricare il file CSR esportato per creare un certificato push.

## <a name="register-your-app-for-push-notifications"></a>Registrare l'app per le notifiche push

Per inviare notifiche push a un'app per iOS, registrare l'applicazione con Apple ed eseguire un'altra registrazione per abilitare le notifiche push.

1. Se l'app non è stata ancora registrata, passare al [portale di provisioning iOS](https://go.microsoft.com/fwlink/p/?LinkId=272456) in Apple Developer Center. Accedere al portale con l'ID Apple e selezionare **Identifiers** (Identificatori). Selezionare quindi **+** per registrare una nuova app.

   :::image type="content" source="media/ios-sdk-get-started/image4.png" alt-text="Pagina ID app":::

2. Nella schermata **Register a New Identifier** (Registra un nuovo identificatore) selezionare il pulsante di opzione **App IDs** (ID app). Selezionare quindi **Continua**.

   :::image type="content" source="media/ios-sdk-get-started/image5.png" alt-text="Pagina di registrazione di un nuovo ID":::

3. Aggiornare i tre valori seguenti per la nuova app e quindi selezionare **Continue** (Continua).

   - **Descrizione** : digitare un nome descrittivo per l'app.
   - **Bundle ID** : immettere un valore di ID bundle nel formato **Organization Identifier.Product Name** come indicato nella [guida alla distribuzione di app](https://help.apple.com/xcode/mac/current/#/dev91fe7130a). I valori **Organization Identifier** (Identificatore organizzazione) e **Product Name** (Nome prodotto) devono corrispondere all'identificatore dell'organizzazione e al nome del prodotto usati nella creazione del progetto Xcode. Nello screenshot seguente il valore **NotificationHubs** viene usato come identificatore dell'organizzazione e il valore **GetStarted** viene usato come nome del prodotto. Assicurarsi che il valore **Bundle Identifier** (Identificatore bundle) corrisponda al valore nel progetto Xcode, affinché Xcode usi il profilo di pubblicazione corretto.

      :::image type="content" source="media/ios-sdk-get-started/image6.png" alt-text="Registrare un ID app":::

   - **Push Notifications** (Notifiche Push): selezionare l'opzione **Push Notifications** (Notifiche push) nella sezione **Capabilities** (Funzionalità).

      :::image type="content" source="media/ios-sdk-get-started/image7.png" alt-text="Registrazione di un nuovo ID app":::

      Questa azione consente di generare l'ID app e richiede all'utente di confermare le informazioni. Fare clic su **Continue** (Continua) e quindi selezionare **Register** per confermare il nuovo ID app.

      :::image type="content" source="media/ios-sdk-get-started/image8.png" alt-text="Conferma del nuovo ID app":::

      Dopo aver selezionato **Register** (Registra), il nuovo ID app viene visualizzato come voce nella pagina **Certificates, Identifiers & Profiles** (Certificati, identificatori e profili).

4. Nella pagina **Certificates, Identifiers & Profiles** (Certificati, identificatori e profili), in **Identifiers** (Identificatori) individuare la voce relativa all'ID app appena creata e selezionare la riga corrispondente per visualizzare la schermata **Edit your App ID Configuration** (Modificare la configurazione dell'ID app).

## <a name="create-a-certificate-for-notification-hubs"></a>Creare un certificato per Hub di notifica

> [!NOTE]
> Con la versione di iOS 13, è possibile ricevere notifiche automatiche solo usando l'autenticazione basata su token. Se si usa l'autenticazione basata su certificati per le credenziali del servizio APN, è necessario passare all'autenticazione basata su token.

Per consentire all'hub di notifica di usare **APNS** , è necessario un certificato. Questa operazione può essere eseguita in uno dei due modi seguenti:

- Creare un file con estensione **p12** che può essere caricato direttamente in Hub di notifica.

- Creare un file con estensione **p8** che può essere usato per l' [autenticazione basata su token](notification-hubs-push-notification-http2-token-authentication.md) (approccio più recente).

La seconda opzione presenta diversi vantaggi rispetto all'uso dei certificati, come documentato in [Autenticazione basata su token (HTTP/2) per APNS](notification-hubs-push-notification-http2-token-authentication.md). Sono comunque fornite le procedure per entrambi gli approcci.

### <a name="option-1-create-a-p12-push-certificate-that-can-be-uploaded-directly-to-notification-hubs"></a>Opzione 1: creare un certificato push con estensione p12 che può essere caricato direttamente in Hub di notifica

1. Scorrere verso il basso fino all'opzione **Push Notifications** (Notifiche push) selezionata, quindi selezionare **Configure** (Configura) per creare il certificato.

   :::image type="content" source="media/ios-sdk-get-started/image9.png" alt-text="ID app":::

2. Verrà visualizzata la finestra **Apple Push Notification service SSL Certificates** (Certificati SSL di Apple Push Notification Service). Selezionare il pulsante **Create Certificate** (Crea certificato) nella sezione **Development SSL Certificate** (Certificato SSL per lo sviluppo).

   :::image type="content" source="media/ios-sdk-get-started/image10.png" alt-text="Creazione del certificato":::

   Verrà visualizzata la schermata **Create a new Certificate** (Crea un nuovo certificato).

   > [!NOTE]
   > Questa esercitazione usa un certificato di sviluppo. La stessa procedura viene usata per registrare un certificato di produzione. Assicurarsi di usare lo stesso tipo di certificato quando si inviano notifiche.

3. Selezionare **Choose File** (Scegli file), passare al percorso in cui è stato salvato il file CSR creato durante la prima attività e quindi fare doppio clic sul nome del certificato per caricarlo. Selezionare quindi **Continua**.

4. Dopo che il portale avrà creato il certificato, fare clic sul pulsante **Download** (Scarica). Salvare il certificato e prendere nota del percorso di salvataggio.

   :::image type="content" source="media/ios-sdk-get-started/image11.png" alt-text="Download del certificato":::

   Il certificato viene scaricato e salvato nella cartella **Download**.

   :::image type="content" source="media/ios-sdk-get-started/image12.png" alt-text="Individuazione del file di certificato":::

   Per impostazione predefinita, il certificato di sviluppo scaricato viene denominato **aps_development.cer**.

5. Fare doppio clic sul certificato push scaricato **aps\_development.cer**. Questa azione consente di installare il nuovo certificato in Keychain, come illustrato nell'immagine seguente:

   :::image type="content" source="media/ios-sdk-get-started/image13.png" alt-text="Accesso Portachiavi":::

   Anche se il nome del certificato potrebbe essere diverso, verrà preceduto da **Apple Development iOS Push Services** (Servizi push iOS per lo sviluppo Apple).

6. In Accesso portachiavi fare clic con il pulsante destro del mouse sul nuovo certificato push creato nella categoria **Certificati** . Selezionare **Export** (Esporta), assegnare un nome al file, selezionare il formato **.p12** e quindi selezionare **Save** (Salva).

   :::image type="content" source="media/ios-sdk-get-started/image14.png" alt-text="Esportare il certificato":::

   È facoltativamente possibile scegliere di proteggere il certificato tramite una password. Fare clic su **OK** se si desidera ignorare la creazione della password. Prendere nota del nome del file e del percorso del certificato con estensione p12 esportato. Verranno usati per abilitare l'autenticazione con il servizio APNS.

   > [!NOTE]
   > Il nome e il percorso del file. P12 potrebbero essere diversi rispetto a quelli illustrati in questa esercitazione.

### <a name="option-2-create-a-p8-certificate-that-can-be-used-for-token-based-authentication"></a>Opzione 2: creare un certificato con estensione p8 che può essere usato per l'autenticazione basata su token

1. Prendere nota dei dettagli seguenti:

   - **App ID Prefix** (Prefisso ID app); si tratta di un **ID team**
   - **Bundle ID** (ID bundle)

2. In **Certificates, Identifiers & Profiles** (Certificati, identificatori e profili) fare clic su **Keys** (Chiavi). Se è già stata configurata una chiave per **APNS** , è possibile riutilizzare il certificato con estensione .p8 scaricato subito dopo la creazione. In tal caso, è possibile ignorare i passaggi da 3 a 5.

3. Per creare una nuova chiave, fare clic sul pulsante **+** o sul pulsante **Create a key** (Crea una chiave).

4. Specificare un valore appropriato in **Key Name** (Nome chiave), selezionare l'opzione **Apple Push Notifications service (APNS)** e quindi fare clic su **Continue** (Continua), seguito da **Register** (Registra) nella schermata successiva.

5. Fare clic su **Download** (Scarica) e quindi spostare il file con estensione **.p8** (e prefisso `AuthKey_` ) in una directory locale sicura, infine fare clic su **Done** (Fine).

   > [!IMPORTANT]
   > Assicurarsi di conservare il file con estensione .p8 in un luogo sicuro e salvare un backup. Dopo aver scaricato la chiave, non è possibile scaricarla di nuovo perché la copia del server viene rimossa.

6. In **Keys** (Chiavi) fare clic sulla chiave appena creata (oppure su una chiave esistente se si è scelto di usare tale chiave).

7. Prendere nota del valore di **Key ID** (ID chiave).

8. Aprire il certificato con estensione p8 in un'applicazione appropriata a scelta, ad esempio [Visual Studio Code](https://code.visualstudio.com/), e quindi prendere nota del valore della chiave, ovvero del valore compreso tra **-----BEGIN PRIVATE KEY-----** e **-----END PRIVATE KEY-----** .

   ```p8
   -----BEGIN PRIVATE KEY-----
   <key_value>
   -----END PRIVATE KEY-----
   ```

   Si tratta del valore del token che verrà usato in seguito per configurare Hub di notifica.

Al termine di questa procedura si dovrebbe disporre delle informazioni seguenti, che verranno usate successivamente in [Configurare l'hub di notifica con le informazioni del servizio APNS](#configure-the-notification-hub-with-apns-information):

- **Team ID** (ID team) (vedere il passaggio 1)
- **Bundle ID** (ID bundle) (vedere il passaggio 1)
- **Key ID** (ID chiave) (vedere il passaggio 7)
- **Token value** (Valore del token) (valore della chiave con estensione p8, vedere il passaggio 8)

## <a name="create-a-provisioning-profile"></a>Creare un profilo di provisioning

1. Tornare a [iOS Provisioning Portal](https://go.microsoft.com/fwlink/p/?LinkId=272456) (Portale di provisioning di iOS), selezionare **Certificates, Identifiers & Profiles** (Certificati, identificatori e profili), quindi **Profiles** (Profili) dal menu a sinistra e infine selezionare **+** per creare un nuovo profilo. Verrà visualizzata la schermata **Register a New Provisioning Profile** (Registra un nuovo profilo di provisioning).

2. In **Development** (Sviluppo) selezionare **iOS App Development** (Sviluppo app iOS) come tipo di profilo di provisioning e quindi **Continue** (Continua).

   :::image type="content" source="media/ios-sdk-get-started/image15.png" alt-text="Elenco profili di provisioning":::

3. Selezionare quindi l'ID dell'app creata nell'elenco a discesa **App ID** (ID app) e selezionare **Continue** (Continua).

   :::image type="content" source="media/ios-sdk-get-started/image16.png" alt-text="Selezionare l'ID app":::

4. Nella finestra **Select certificates** (Seleziona certificati) selezionare il certificato di sviluppo usato per la firma del codice e quindi fare clic su **Continue** (Continua). Questo non è il certificato push creato. Se non ne esiste uno, è necessario crearlo. Se esiste un certificato, andare al passaggio successivo. Per creare un certificato di sviluppo, se non ne esiste uno:

   1. Se viene visualizzato il messaggio **No Certificates are available** (Nessun certificato disponibile), selezionare **Create Certificate** (Crea certificato).
   2. Nella sezione **Software** selezionare **Apple Development** (Sviluppo Apple). Selezionare quindi **Continua**.
   3. Nella schermata **Create a New Certificate** (Crea un nuovo certificato) selezionare **Choose File** (Scegli file).
   4. Passare al certificato **Certificate Signing Request** (Richiesta di firma del certificato) creato in precedenza, selezionarlo e quindi scegliere **Open** (Apri).
   5. Selezionare **Continua**.
   6. Scaricare il certificato di sviluppo e prendere nota del percorso in cui è salvato.

5. Tornare alla pagina **Certificates, Identifiers & Profiles** (Certificati, identificatori e profili), selezionare **Profiles** (Profili) dal menu a sinistra e infine selezionare **+** per creare un nuovo profilo. Verrà visualizzata la schermata **Register a New Provisioning Profile** (Registra un nuovo profilo di provisioning).

6. Nella finestra **Select certificates** (Seleziona certificati) selezionare il certificato di sviluppo appena creato. Selezionare quindi **Continua**.

7. Selezionare quindi i dispositivi da usare per il test e selezionare **Continue** (Continua).

8. Scegliere infine un nome per il profilo in **Provisioning Profile Name** (Nome profilo di provisioning) e quindi selezionare **Generate** (Genera).

   :::image type="content" source="media/ios-sdk-get-started/image17.png" alt-text="Scegliere un nome per il profilo di provisioning":::

9. Quando viene creato il nuovo profilo di provisioning, selezionare **Download**. Prendere nota del percorso in cui viene salvato.

10. Passare al percorso del profilo di provisioning e quindi fare doppio clic su di esso per installarlo nel computer di sviluppo Xcode.

## <a name="create-a-notification-hub"></a>Creare un hub di notifica

In questa sezione viene creato un hub di notifica e viene configurata l'autenticazione con il servizio APNS usando il certificato push con estensione p12 o l'autenticazione basata su token. Se si vuole usare un hub di notifica che è già stato creato, è possibile ignorare il passaggio 5.

1. Accedere al [portale di Azure](https://portal.azure.com/).

2. Scegliere **Tutti i servizi** dal menu a sinistra e quindi selezionare **Hub di notifica** nella sezione **Dispositivi mobili**. Selezionare l'icona a forma di stella accanto al nome del servizio per aggiungere il servizio alla sezione **PREFERITI** nel menu a sinistra. Dopo aver aggiunto **Hub di notifica** a **PREFERITI** , selezionarlo.

   :::image type="content" source="media/ios-sdk-get-started/image18.png" alt-text="Azure portal":::

3. Nella pagina **Hub di notifica** selezionare **Aggiungi** sulla barra degli strumenti.

   :::image type="content" source="media/ios-sdk-get-started/image19.png" alt-text="Pulsante Aggiungi sulla barra degli strumenti":::

4. Nella pagina **Hub di notifica** eseguire questa procedura:

   1. Immettere un nome in **Hub di notifica**.
   2. Immettere un nome in **Crea un nuovo spazio dei nomi**. Uno spazio dei nomi contiene uno o più hub di notifica.
   3. Selezionare un valore nell'elenco a discesa **Località**. Questo valore specifica la posizione in cui creare l'hub di notifica.
   4. Selezionare un gruppo di risorse esistente in **Gruppo di risorse** oppure crearne uno nuovo.
   5. Selezionare **Crea**.

   :::image type="content" source="media/ios-sdk-get-started/image20.png" alt-text="Impostare le proprietà":::

5. Selezionare **Notifiche** (icona a forma di campanello) e quindi selezionare **Vai alla risorsa**. È anche possibile aggiornare l'elenco nella pagina **Hub di notifica** e selezionare l'hub.

   :::image type="content" source="media/ios-sdk-get-started/image21.png" alt-text="Notifiche del portale":::

6. Selezionare **Criteri di accesso** dall'elenco. Prendere nota delle due stringhe di connessione disponibili. Sono necessarie in un secondo momento per gestire le notifiche push.

   > [!IMPORTANT]
   > Non usare il criterio **DefaultFullSharedAccessSignature** nell'applicazione. Deve essere usato solo nel back-end.

   :::image type="content" source="media/ios-sdk-get-started/image22.png" alt-text="Stringhe di connessione":::

## <a name="configure-the-notification-hub-with-apns-information"></a>Configurare l'hub di notifica con le informazioni del servizio APNS

In **Notification Services** (Servizi di notifica) selezionare **Apple (APNS)** , quindi seguire la procedura appropriata in base all'approccio scelto in precedenza nella sezione [Creazione di un certificato per Hub di notifica](#create-a-certificate-for-notification-hubs).

> [!NOTE]
> Usare **Production** (Produzione) per **Application Mode** (Modalità applicazione) solo se si vogliono inviare notifiche push agli utenti che hanno acquistato l'app dallo Store.

### <a name="option-1-use-a-p12-push-certificate"></a>Opzione 1: usare un certificato push con estensione p12

1. Selezionare **Certificate**.

2. Selezionare l'icona del file.

3. Selezionare il file con estensione p12 esportato in precedenza e quindi selezionare **Open** (Apri).

4. Se necessario, specificare la password corretta.

5. Selezionare la modalità **Sandbox**.

   :::image type="content" source="media/ios-sdk-get-started/image23.png" alt-text="Configurare":::

6. Selezionare **Salva**.

### <a name="option-2-use-token-based-authentication"></a>Opzione 2: usare l'autenticazione basata su token

1. Selezionare **Token**.

2. Immettere i valori seguenti acquisiti in precedenza:

   - **Key ID** (ID chiave)
   - **Bundle ID** (ID bundle)
   - **Team ID** (ID team)
   - **Token**

3. Scegliere **Sandbox**

4. Selezionare **Salva**.

L'hub di notifica è ora configurato con le informazioni del servizio APNS. Sono presenti anche le stringhe di connessione necessarie per registrare l'app e inviare notifiche push.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato creato e configurato un hub di notifica in Azure, quindi l'hub è stato configurato per consentire l'invio delle notifiche all'applicazione tramite Apple Push Notification Service (APNS). Nella prossima esercitazione si creerà un'applicazione iOS di esempio e vi si integrerà l'SDK di Hub di notifica di Azure in modo che possa ricevere le notifiche push inviate tramite il portale di Azure. Passare all'esercitazione successiva in base al linguaggio scelto:

- [Esercitazione: Inviare notifiche push alle app iOS con Hub di notifica di Azure](ios-sdk-300.md)
