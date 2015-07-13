
Il servizio Apple Push Notification Service (APNS) usa i certificati per autenticare le notifiche push. Seguire queste istruzioni per creare il certificato push necessario per inviare e ricevere notifiche. Per la documentazione ufficiale del servizio APNS, vedere [Apple Push Notification Service](http://go.microsoft.com/fwlink/p/?LinkId=272584).

##Generare il file di richiesta di firma del certificato

È necessario innanzitutto generare il file della richiesta di firma del certificato usato da Apple per la generazione di un certificato push firmato.

1. Dalla cartella **Utility** o **Altro** eseguire lo strumento Accesso Portachiavi.

2. Fare clic su **Accesso Portachiavi**, espandere **Assistente Certificato**, quindi fare clic su **Richiedi un certificato da una Autorità di Certificazione**.

  	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)

3. Fare clic su **Indirizzo e-mail utente**, selezionare un valore per **Nome comune**, assicurarsi che l'opzione **Salvata su disco** sia selezionata, quindi fare clic su **Continua**. Lasciare vuoto il campo **Indirizzo e-mail CA**, in quanto non è obbligatorio.

  	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-csr-info.png)

4. Digitare un nome per il file della richiesta di firma del certificato (CSR) in **Salva col nome**, selezionare il percorso in **Percorso**, quindi fare clic su **Salva**.

  	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-save-csr.png)

  	Il file CSR viene salvato nel percorso selezionato. Il percorso predefinito è Scrivania. Tenere a mente il percorso scelto per il file.

A questo punto registrare l'app con Apple, abilitare le notifiche push e caricare il file CSR esportato per creare un certificato push.

##Registrare l'app per le notifiche push

Per poter inviare notifiche push a un'app per iOS, è necessario registrare l'applicazione con Apple ed eseguire un'altra registrazione per abilitare le notifiche push.

1. Se l'app non è ancora stata registrata, accedere al <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">portale di provisioning iOS</a> su Apple Developer Center, eseguire l'accesso con il proprio ID Apple, fare clic su **Identifiers**, quindi su **App IDs** e infine fare clic sul segno **+** per registrare una nuova app.

   	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids.png)


2. Digitare un nome descrittivo per l'app in **App ID Description**.

	In **Explicit App ID**, immettere un valore in **Bundle Identifier** nel formato`<Organization Identifier>.<Product Name>`, come indicato nella [App Distribution Guide](http://go.microsoft.com/fwlink/?LinkId=613485). *Organization Identifier* e *Product Name* devono corrispondere all'identificatore di organizzazione e al nome del prodotto che verranno usati quando si crea il progetto XCode. Nella schermata seguente *NotificationHubs* viene usato come identificatore di organizzazione e *GetStarted* viene usato come nome del prodotto. Verificare che corrispondano ai valori che si useranno nel progetto XCode, in modo da usare il profilo di pubblicazione corretto con XCode.
	
	Selezionare l'opzione **Push Notifications** nella sezione App Services e quindi fare clic su **Continue**.

	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-info.png)

   	Verrà generato l'ID app e all'utente verrà chiesto di confermare le informazioni. Fare clic su **Invia**


    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-confirm-new-appid.png)


   	Dopo aver fatto clic su **Submit** verrà visualizzata la schermata **Registration complete**, come illustrato di seguito. Fare clic su **Done**.


    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-registration-complete.png)


3. Individuare l'ID dell'app appena creato e fare clic sulla relativa riga.

   	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids2.png)

   	Facendo clic sull'ID app verranno visualizzati i dettagli relativi all'app. Fare clic su **Edit**.

   	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-edit-appid.png)

4. Scorrere fino alla fine della schermata e fare clic su **Create Certificate...** nella sezione **Development Push SSL Certificate**.

   	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

   	Verrà visualizzato l'assistente "Add iOS Certificate".

    > [AZURE.NOTE]In questa esercitazione viene usato un certificato di sviluppo. La stessa procedura viene usata per registrare un certificato di produzione. Quando si inviano le notifiche, accertarsi semplicemente di usare lo stesso tipo di certificato.

5. Fare clic su **Choose File**, passare al percorso in cui è stato salvato il file CSR creato durante la prima attività, quindi fare clic su **Generate**.

  	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-cert-choose-csr.png)

6. Al termine della creazione del certificato nel portale, fare clic su **Download** e quindi su **Done**.

  	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

   	Il certificato di firma verrà scaricato e salvato nel computer nella cartella Download.

  	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

    > [AZURE.NOTE]Per impostazione predefinita, il file scaricato di un certificato di sviluppo è denominato **aps_development.cer**.

7. Fare doppio clic sul certificato push scaricato **aps_development.cer**.

   	Il nuovo certificato verrà installato nel Portachiavi, come mostrato di seguito:

   	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

    > [AZURE.NOTE]Il nome del certificato potrebbe essere diverso, ma verrà preceduto da **Apple Development iOS Push Services:**.

Questo certificato verrà usato in un secondo momento per generare un file con estensione p12 per abilitare l'autenticazione con il servizio APN.

##Creare un profilo di provisioning per l'app

1. Nel <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">portale di provisioning iOS</a> selezionare **Provisioning Profiles**, quindi **All** e infine fare clic sul pulsante **+** per creare un nuovo profilo. Verrà avviata la procedura guidata **Add iOS Provisiong Profile**

   	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

2. Selezionare **iOS App Development** in **Development** come tipo di profilo di provisioning e fare clic su **Continue**


3. Selezionare quindi l'ID app appena creato nell'elenco a discesa **App ID** e fare clic su **Continue**.

   	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)


4. Nella schermata **Select certificates** selezionare il solito certificato di sviluppo usato per la firma del codice e fare clic su **Continue**. Questo non è il certificato push appena creato.

   	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-select-cert.png)


5. In **Devices** selezionare i dispositivi da usare per il test e fare clic su **Continue**

   	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-select-devices.png)


6. Infine, scegliere un nome per il profilo in **Profile Name**, fare clic su **Generate** e quindi su **Done**

   	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)


  	Verrà creato un nuovo profilo di provisioning.

   	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-profile-ready.png)

<!---HONumber=62-->