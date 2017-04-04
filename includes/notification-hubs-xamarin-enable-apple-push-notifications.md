
Per registrare l'app per le notifiche push tramite Apple Push Notification Service (servizio APN), è necessario creare un certificato push, un ID app e un profilo di provisioning per il progetto nel portale per sviluppatori di Apple.

L'ID app contiene le impostazioni di configurazione che consentono all'app di inviare e ricevere le notifiche push. Queste impostazioni includono il certificato di notifica push necessario per l'autenticazione con il servizio APN quando l'app invia e riceve notifiche push.

Per altre informazioni su questi concetti, vedere la documentazione ufficiale relativa a [Apple Push Notification Service](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1).

## <a name="generate-the-certificate-signing-request-file-for-the-push-certificate"></a>Generare il file della richiesta di firma del certificato per il certificato push
Questa procedura illustra il processo di creazione della richiesta di firma del certificato, che genera un certificato push usato con il servizio APN.

1. Sul Mac eseguire lo strumento Accesso Portachiavi. Può essere aperto dalla cartella **Utilities** (Utility) o **Other** (Altro) nella finestra di avvio.

2. Selezionare **Accesso Portachiavi**, espandere **Certificate Assistant** (Assistente certificati) e quindi selezionare **Request a Certificate from a Certificate Authority** (Richiedi certificato da Autorità di certificazione).

      ![Richiedere un certificato](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)

3. Selezionare **User Email Address** (Indirizzo di posta elettronica utente) e **Common Name** (Nome comune).

4. Verificare che l'opzione **Saved to disk** (Salvata su disco) sia selezionata e quindi selezionare **Continue** (Continua). Lasciare vuoto il campo **CA Email Address** (Indirizzo di posta elettronica CA), perché non è obbligatorio.

      ![Informazioni relative al certificato](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-csr-info.png)

4. Digitare un nome per il file della richiesta di firma del certificato (CSR) in **Save As** (Salva con nome).
5. Selezionare il percorso in **Where** (Percorso) e quindi **Save** (Salva).

      ![Salvare la richiesta di firma del certificato](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-save-csr.png)

      Questo consente di salvare il file CSR nel percorso selezionato. Il percorso predefinito è il desktop. Ricordare il percorso scelto per il file.

## <a name="register-your-app-for-push-notifications"></a>Registrare l'app per le notifiche push
Creare un nuovo ID app esplicito per l'applicazione in Apple e quindi configurarlo per le notifiche push.  

1. Passare al [portale di provisioning iOS](http://go.microsoft.com/fwlink/p/?LinkId=272456) in Apple Developer Center e quindi accedere con il proprio ID Apple.

2. Selezionare **Identifiers** (Identificatori) e quindi **App IDs** (ID app).

3. Selezionare il segno **+** per registrare una nuova app.

      ![Registrare una nuova app](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-ios-appids.png)

4. Aggiornare i tre campi seguenti per la nuova app e quindi selezionare **Continue** (Continua).

   * **Name** (Nome): digitare un nome descrittivo per l'app nella sezione **App ID Description** (Descrizione ID app).

   * **Bundle Identifier** (Identificatore bundle): nella sezione **Explicit App ID** (ID app esplicito) immettere un **identificatore del bundle** nel formato `<Organization Identifier>.<Product Name>`, come descritto nella [guida alla distribuzione di app](https://developer.apple.com/library/mac/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringYourApp/ConfiguringYourApp.html#//apple_ref/doc/uid/TP40012582-CH28-SW8). Questo identificatore deve corrispondere al valore usato nel progetto XCode, Xamarin o Cordova per l'app.

   * **Push Notifications** (Notifiche push): selezionare l'opzione **Push Notifications** (Notifiche push) nella sezione **App Services** (Servizi app).

     ![Registrare un ID app](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-new-appid-info.png)

5. Nella schermata **Confirm your App ID** (Conferma ID app) controllare le impostazioni. Dopo averle verificate, selezionare **Register** (Registra).

6. Dopo aver inviato il nuovo ID app, verrà visualizzata la schermata **Registration complete** (Registrazione completata). Selezionare **Operazione completata**.

7. In Developer Center individuare l'ID app creato in **App IDs** (ID app) e selezionare la relativa riga. Selezionando la riga dell'ID app verranno visualizzati i dettagli dell'app. Selezionare quindi il pulsante **Edit** (Modifica) nella parte inferiore.

8. Scorrere fino alla fine della schermata e quindi selezionare il pulsante **Create Certificate** (Crea certificato) nella sezione **Development Push SSL Certificate** (Certificato SSL push di sviluppo).

      ![Certificato SSL push di sviluppo](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

 Verrà visualizzato l'assistente "Add iOS Certificate".

   > [!NOTE]
   > Questa esercitazione usa un certificato di sviluppo. Lo stesso processo viene usato per registrare un certificato di produzione. Assicurarsi di usare lo stesso tipo di certificato quando si inviano notifiche.
   >

9. Selezionare **Choose File** (Scegli file) e quindi passare al percorso in cui è stato salvato il file CSR per il certificato push. Selezionare quindi **Generate** (Genera).

      ![Generare il certificato](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-appid-cert-choose-csr.png)

10. Al termine della creazione del certificato nel portale, selezionare il pulsante **Download** (Scarica).

      ![Certificato pronto](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

       Il certificato di firma verrà scaricato e salvato nel computer nella cartella Download.

      ![Cartella di download](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

   > [!NOTE]
   > Per impostazione predefinita, il file scaricato è un certificato di sviluppo denominato **aps_development.cer**.
   >
   >
11. Fare doppio clic sul certificato push scaricato **aps_development.cer**. Il nuovo certificato verrà installato nel portachiavi, come illustrato nello screenshot seguente:

       ![Accesso Portachiavi](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

   > [!NOTE]
   > Il nome del certificato potrebbe essere diverso, ma sarà preceduto da **Apple Development iOS Push Services** (Servizi push iOS di sviluppo Apple).
   >
   >
12. In **Accesso Portachiavi** selezionare il nuovo certificato push creato nella categoria **Certificates** (Certificati). Selezionare **Export** (Esporta), assegnare un nome al file, selezionare il formato **.p12** e quindi selezionare **Save** (Salva).

    Prendere nota del nome e del percorso del file di certificato con estensione p12 esportato. Questo certificato verrà caricato nel portale di Azure classico per abilitare l'autenticazione con il servizio APN. Se l'opzione di formato **.p12** non è disponibile, potrebbe essere necessario riavviare Accesso Portachiavi.

## <a name="create-a-provisioning-profile-for-the-app"></a>Creare un profilo di provisioning per l'app
1. Nel <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">portale di provisioning iOS</a> selezionare **Provisioning Profiles** (Profili di provisioning), quindi **All** (Tutti) e infine il pulsante **+** per creare un profilo. Verrà così avviato lo strumento per i profili **Add iOS Provisioning Profile** (Aggiungere profilo di provisioning iOS).

      ![Profili di provisioning](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

2. In **Development** (Sviluppo) selezionare **iOS App Development** (Sviluppo app iOS) come tipo di profilo di provisioning e quindi **Continue** (Continua).

3. Selezionare l'ID app creato nell'elenco a discesa **App ID** (ID app) e quindi **Continue** (Continua).

      ![Selezionare l'ID app](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)

4. Nella schermata **Select certificates** (Seleziona certificati) selezionare il certificato di sviluppo usato per la firma del codice e quindi **Continue** (Continua). Questo è un certificato di firma e non il certificato push creato.

       ![Signing certificate](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-provisioning-select-cert.png)

5. Selezionare i **dispositivi** da usare per il test e quindi **Continue** (Continua).

     ![Aggiungere un profilo di provisioning](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-provisioning-select-devices.png)

6. Scegliere infine un nome per il profilo in **Profile Name** (Nome profilo) e quindi selezionare **Generate** (Generate).

      ![Assegnare un nome al profilo](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)
