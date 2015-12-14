

Per registrare l'app per le notifiche push tramite Apple Push Notification Service (servizio APN), è necessario creare un certificato push, un ID App e un profilo di provisioning nuovi per il progetto nel portale per sviluppatori di Apple. L'ID conterrà le impostazioni di configurazione che consentono all'app di inviare e ricevere le notifiche push. Queste impostazioni includeranno il certificato di notifica push necessario per l'autenticazione con Apple Push Notification Service (servizio APN) durante l'invio e la ricezione di notifiche push. Per altre informazioni su questi concetti, vedere la documentazione ufficiale relativa al servizio [Apple Push Notification Service](http://go.microsoft.com/fwlink/p/?LinkId=272584).


####Generare il file di richiesta di firma del certificato per il certificato push

Questa procedura illustra la creazione della richiesta di firma del certificato, che verrà usata per generare un certificato push da usare con il servizio APN.

1. Sul Mac eseguire lo strumento Accesso Portachiavi. Può essere aperto dalla cartella **Utility** o **Altro** nella finestra di avvio.

2. Fare clic su **Accesso Portachiavi**, espandere **Assistente Certificato**, quindi fare clic su **Richiedi un certificato da una Autorità di Certificazione**.

  	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)

3. Fare clic su **Indirizzo e-mail utente**, selezionare un valore per **Nome comune**, assicurarsi che l'opzione **Salvata su disco** sia selezionata, quindi fare clic su **Continua**. Lasciare vuoto il campo **Indirizzo e-mail CA**, in quanto non è obbligatorio.

  	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-csr-info.png)

4. Digitare un nome per il file della richiesta di firma del certificato (CSR) in **Salva col nome**, selezionare il percorso in **Percorso**, quindi fare clic su **Salva**.

  	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-save-csr.png)

  	Il file CSR viene salvato nel percorso selezionato. Il percorso predefinito è Scrivania. Tenere a mente il percorso scelto per il file.


####Registrare l'app per le notifiche push

Creare un nuovo ID App esplicito per l'applicazione in Apple e configurarlo anche per le notifiche push.

1. Passare al [portale di provisioning per iOS](http://go.microsoft.com/fwlink/p/?LinkId=272456) in Apple Developer Center, eseguire l'accesso con il proprio ID Apple, fare clic su**Identifiers**, quindi su **App IDs**v e infine fare clic sul segno **+** per registrare una nuova app.

   	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-ios-appids.png)

2. Aggiornare i tre campi seguenti per la nuova app e quindi fare clic su **Continue**:

	* **Name**: digitare un nome descrittivo per l'app nel campo **Name** della sezione **App ID Description**.
	
	* **Bundle Identifier**: nella sezione **Explicit App ID** immettere un valore in **Bundle Identifier** nel formato `<Organization Identifier>.<Product Name>`, come indicato nel documento [App Distribution Guide](https://developer.apple.com/library/mac/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringYourApp/ConfiguringYourApp.html#//apple_ref/doc/uid/TP40012582-CH28-SW8). Questo valore deve corrispondere al valore usato nel progetto XCode o Xamarin per l'applicazione.
	 
	* **Push Notifications**: selezionare l'opzione **Push Notifications** nella sezione **App Services**.

	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-new-appid-info.png)

3.	Nella schermata Confirm your App ID esaminare le impostazioni e dopo averne verificato la correttezza fare clic su **Submit**

4. 	Dopo aver inviato il nuovo ID app, verrà visualizzata la schermata **Registration complete**. Fare clic su **Done**.

5. In Developer Center, sotto App IDs trovare l'ID app appena creato e fare clic sulla relativa riga. Se si fa clic sulla riga dell'ID app, verranno visualizzati i dettagli relativi all'app. Fare clic sul pulsante **Edit** nella parte inferiore della schermata.

6. Scorrere fino alla fine della schermata e fare clic su **Create Certificate...** nella sezione **Development Push SSL Certificate**.

   	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

   	Verrà visualizzato l'assistente "Add iOS Certificate".

    > [AZURE.NOTE]Questa esercitazione usa un certificato di sviluppo. La stessa procedura viene usata per registrare un certificato di produzione. Per l'invio delle notifiche, assicurarsi di usare lo stesso tipo di certificato.

7. Fare clic su **Choose File**, passare al percorso in cui è stato salvato il file CSR e quindi fare clic su **Generate**.

  	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-appid-cert-choose-csr.png)

8. Al termine della creazione del certificato nel portale, fare clic sul pulsante **Download**.

  	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

   	Il certificato di firma verrà scaricato e salvato nel computer nella cartella Download.

  	![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

    > [AZURE.NOTE]Per impostazione predefinita, il file scaricato di un certificato di sviluppo è denominato **aps\_development.cer**.

9. Fare doppio clic sul certificato push scaricato **aps\_development.cer**. Il nuovo certificato verrà installato nel Portachiavi, come mostrato di seguito:

   	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

    > [AZURE.NOTE]Il nome del certificato potrebbe essere diverso, ma verrà preceduto da **Apple Development iOS Push Services:**.

10. In Accesso portachiavi fare clic con il pulsante destro del mouse sul nuovo certificato push appena creato nella categoria **Certificati**. Fare clic su **Esporta**, assegnare un nome al file, selezionare il formato **.p12** e quindi fare clic su **Salva**.

	Prendere nota del nome e del percorso del file di certificato con estensione p12 esportato. Questo certificato verrà usato per abilitare l'autenticazione con il servizio APN, una volta caricato nel portale di Azure.



####Creare un profilo di provisioning per l'app

1. Nel <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">portale di provisioning iOS</a> selezionare **Provisioning Profiles**, quindi **All** e infine fare clic sul pulsante **+** per creare un nuovo profilo. Verrà avviata la procedura guidata **Add iOS Provisiong Profile**

   	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

2. Selezionare **iOS App Development** in **Development** come tipo di profilo di provisioning e fare clic su **Continue**


3. Selezionare quindi l'ID app appena creato nell'elenco a discesa **App ID** e fare clic su **Continue**.

   	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)


4. Nella schermata **Select certificates** selezionare il certificato di sviluppo usato abitualmente per la firma del codice e fare clic su **Continue**. Questo è un certificato di firma e non il certificato push appena creato.

   	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-provisioning-select-cert.png)


5. In **Devices** selezionare i dispositivi da usare per il test e fare clic su **Continue**

   	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-provisioning-select-devices.png)


6. Scegliere infine un nome per il profilo in **Profile Name** e fare clic su **Generate**.

   	![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)

<!---HONumber=AcomDC_1203_2015-->