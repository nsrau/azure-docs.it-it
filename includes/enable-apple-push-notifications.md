
Il servizio APNS usa i certificati per autenticare il servizio mobile. Seguire le istruzioni fornite per creare i certificati necessari e caricarli nel servizio mobile. Per la documentazione ufficiale del servizio APNS, vedere [Apple Push Notification Service](http://go.microsoft.com/fwlink/p/?LinkId=272584).

## <a id="certificates"></a>Generare il file di richiesta di firma del certificato

È necessario innanzitutto generare il file della richiesta di firma del certificato (CSR, Certificate Signing Request) che viene usato da Apple per la generazione di un certificato firmato.

1. Dalla cartella Utility eseguire lo strumento Accesso Portachiavi.

2. Fare clic su **Accesso Portachiavi**, espandere **Assistente Certificato**, quindi fare clic su **Richiedi un certificato da una Autorità di Certificazione**.

 	![](./media/enable-apple-push-notifications/mobile-services-ios-push-step5.png)

3. Fare clic su **Indirizzo e-mail utente**, selezionare un valore per **Nome comune**, assicurarsi che l'opzione **Salvata su disco** sia selezionata, quindi fare clic su **Continua**. Lasciare vuoto il campo **Indirizzo e-mail CA**, in quanto non è obbligatorio.

 	![](./media/enable-apple-push-notifications/mobile-services-ios-push-step6.png)

4. Digitare un nome per il file della richiesta di firma del certificato (CSR) in **Salva col nome**, selezionare il percorso in **Percorso**, quindi fare clic su **Salva**.

 	![](./media/enable-apple-push-notifications/mobile-services-ios-push-step7.png)

 	Il file CSR viene salvato nel percorso selezionato. Il percorso predefinito è Scrivania. Tenere a mente il percorso scelto per il file.

A questo punto registrare l'app con Apple, abilitare le notifiche push e caricare il file CSR esportato per creare un certificato push.

## <a id="register"></a>Registrare l'app per le notifiche push

Per poter inviare notifiche push a un'app per iOS da Servizi mobili, è necessario registrare l'applicazione con Apple ed eseguire un'altra registrazione per abilitare le notifiche push. 

1. Se l'app non è ancora stata registrata, accedere al <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">portale di provisioning iOS</a> in Apple Developer Center, eseguire l'accesso con il proprio ID Apple, fare clic su **Identifiers**, quindi su **App IDs** e infine fare clic sul segno **+** per registrare una nuova app.

 	![](./media/enable-apple-push-notifications/mobile-services-ios-push-02.png)



> [AZURE.NOTE] Se si sceglie di specificare un valore per <strong>Bundle Identifier</strong> diverso da <i>MobileServices.Quickstart</i>, è necessario aggiornare il valore dell'identificatore bundle anche nel progetto Xcode. Si consiglia di usare l'identificatore del bundle esatto già usato nel progetto di guida introduttiva.

2. Digitare un nome per l'app in **Description**, immettere il valore _MobileServices.Quickstart_ in **Bundle Identifier**, selezionare l'opzione "Push Notifications" nella sezione "App Services", quindi fare clic su **Continue**. In questo esempio viene usato l'ID **MobileServices.Quickstart**, tuttavia non è possibile riutilizzare lo stesso ID, in quanto gli ID app devono essere univoci per tutti gli utenti. È pertanto consigliabile aggiungere il proprio nome completo o le proprie iniziali dopo il nome dell'app.


 ![](./media/enable-apple-push-notifications/mobile-services-ios-push-03.png)

 	Verrà generato l'ID dell'app e all'utente verrà chiesto di **inviare** le informazioni. Fare clic su **Submit**.


 ![](./media/enable-apple-push-notifications/mobile-services-ios-push-04.png)


 	Dopo aver fatto clic su **Submit**, verrà visualizzata la schermata **Registration complete**, come illustrato di seguito. Fare clic su **Done**.


 ![](./media/enable-apple-push-notifications/mobile-services-ios-push-05.png)


3. Individuare l'ID dell'app appena creato e fare clic sulla relativa riga.

 	![](./media/enable-apple-push-notifications/mobile-services-ios-push-06.png)

 	Facendo clic sull'ID dell'app verranno visualizzati i dettagli relativi all'app e all'ID dell'app. Fare clic sul pulsante **Impostazioni**.

 	![](./media/enable-apple-push-notifications/mobile-services-ios-push-07.png)

4. Scorrere fino alla fine della schermata e fare clic su **Create Certificate** nella sezione **Development Push SSL Certificate**.

 	![](./media/enable-apple-push-notifications/mobile-services-ios-push-08.png)

 	Verrà visualizzato l'assistente "Add iOS Certificate".

 > [AZURE.NOTE] In questa esercitazione viene usato un certificato di sviluppo. La stessa procedura viene usata per registrare un certificato di produzione. Verificare di impostare lo stesso tipo di certificato quando si carica il certificato in Servizi mobili.

5. Fare clic su **Choose File**, passare al percorso in cui è stato salvato il file CSR creato durante la prima attività, quindi fare clic su **Generate**.

 	![](./media/enable-apple-push-notifications/mobile-services-ios-push-10.png)

6. Al termine della creazione del certificato nel portale, fare clic sul pulsante **Download** e quindi su **Done**.

 	![](./media/enable-apple-push-notifications/mobile-services-ios-push-11.png)

 	Il certificato di firma verrà scaricato e salvato nel computer nella cartella Download.

 	![](./media/enable-apple-push-notifications/mobile-services-ios-push-step9.png)

 > [AZURE.NOTE] Per impostazione predefinita, il nome del file scaricato di un certificato di sviluppo è **aps_development.cer**.

7. Fare doppio clic sul certificato push scaricato **aps_development.cer**.

 	Il nuovo certificato verrà installato nel Portachiavi, come mostrato di seguito:

 	![](./media/enable-apple-push-notifications/mobile-services-ios-push-step10.png)

 > [AZURE.NOTE] Il nome del certificato potrebbe essere diverso, ma verrà preceduto da **Apple Development iOS Push Services:**.

Questo certificato verrà usato in un secondo momento per generare un file con estensione p12 e verrà caricato in Servizi mobili per abilitare l'autenticazione con APNS.

## <a id="profile"></a>Creare un profilo di provisioning per l'app

1. Nel <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">portale di provisioning iOS</a>, selezionare **Provisioning Profiles**, quindi **All** e infine fare clic sul pulsante **+** per creare un nuovo profilo. Verrà avviata la procedura guidata **Add iOS Provisiong Profile**.

 	![](./media/enable-apple-push-notifications/mobile-services-ios-push-12.png)

2. Selezionare **iOS App Development** in **Development** come tipo di profilo di provisioning e fare clic su **Continue**.

 	![](./media/enable-apple-push-notifications/mobile-services-ios-push-13.png)

3. Selezionare quindi l'ID dell'app di guida introduttiva per Servizi mobili nell'elenco a discesa **App ID** e fare clic su **Continue**.

 	![](./media/enable-apple-push-notifications/mobile-services-ios-push-14.png)

4. Nella schermata **Select certificates** selezionare il certificato creato in precedenza e fare clic su **Continue**.

 	![](./media/enable-apple-push-notifications/mobile-services-ios-push-15.png)

5. In **Devices** selezionare i dispositivi da usare per il test e fare clic su **Continue**.

 	![](./media/enable-apple-push-notifications/mobile-services-ios-push-16.png)

6. Infine, scegliere un nome per il profilo in **Profile Name**, fare clic su **Generate** e quindi su **Done**.

 	![](./media/enable-apple-push-notifications/mobile-services-ios-push-17.png)

 	Verrà creato un nuovo profilo di provisioning.

7. In Xcode aprire Organizer e selezionare la visualizzazione Devices, selezionare **Provisioning Profiles** nella sezione **Library** del riquadro sinistro, quindi fare clic sul pulsante **Refresh** nella parte inferiore del riquadro centrale.

8. In alternativa, nel menu Xcode selezionare **Preferences** e quindi **Accounts**. Nel riquadro sinistro selezionare il proprio ID Apple Developer. Fare clic sul pulsante **View Details** sulla destra. Nella finestra a comparsa fare clic sul pulsante arrotondato **Refresh** per aggiornare l'elenco dei profili di provisioning. L'operazione potrebbe richiedere alcuni minuti. Si consiglia di fare clic su **Refresh** 2-3 volte finché non viene visualizzato il nuovo profilo di provisioning. Verificare inoltre che l'identificatore del bundle di questo progetto Xcode sia identico a quello associato all'ID dell'app e al profilo di provisioning creati finora.

 ![](./media/enable-apple-push-notifications/mobile-services-ios-push-01.png)

9. In **Targets** fare clic su **Quickstart**, espandere **Code Signing Identity**, quindi in **Debug** selezionare il nuovo profilo. Per effetto di questa operazione, il progetto Xcode userà il nuovo profilo per la firma del codice. A questo punto, è necessario caricare il certificato in Azure.

 	![](./media/enable-apple-push-notifications/mobile-services-ios-push-step17.png)

<!--HONumber=47-->
