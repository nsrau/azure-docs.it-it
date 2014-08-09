

1.  Se l'app non è ancora stata registrata, passare alla pagina relativa all'[invio di app](http://go.microsoft.com/fwlink/p/?LinkID=266582) nel Centro per sviluppatori di app di Windows Store, accedere con il proprio account Microsoft e quindi fare clic su **App name**.

    ![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-submit-win8-app.png)

2.  Immettere un nome per l'app in **Nome app**, fare clic su **Riserva nome applicazione** e quindi su **Salva**.

    ![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-win8-app-name.png)

    Verrà creata una nuova registrazione a Windows Store per l'app.

3.  In Visual Studio 2012 Express per Windows 8 aprire il progetto creato dopo aver completato l'esercitazione [Introduzione a Servizi mobili](/it-it/documentation/articles/mobile-services-windows-store-get-started/).

4.  In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto, scegliere **Store** e quindi fare clic su **Associa applicazione a Store**.

	![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-store-association.png) 

	Verrà visualizzata la procedura guidata **Associa l'applicazione con Windows Store**.

1.  Nella procedura guidata fare clic su **Accedi** e quindi accedere con il proprio account Microsoft.

2.  Selezionare l'app registrata nel passaggio 2, fare clic su **Avanti** e quindi su **Associa**.

    ![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-select-app-name.png)

    Le necessarie informazioni di registrazione a Windows Store verranno aggiunte al manifesto dell'applicazione.    

3.  Nella pagina di Windows Dev Center per la nuova app fare clic su **Servizi**.

    ![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-win8-edit-app.png) 

4.  Nella pagina Servizi fare clic su **Sito di servizi Live** in **Servizi mobili di Azure**.

    ![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-win8-edit2-app.png)

5.  Fare clic su **Autenticazione del servizio** e prendere nota dei valori di **Segreto client** e **Identificatore di sicurezza del pacchetto (SID)**.

    ![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-win8-app-push-auth.png)

    **Nota sulla sicurezza**

    Il segreto client e il SID di pacchetto sono importanti credenziali di sicurezza. Non condividere questi valori con altri né distribuirli con l'app.

6.  Accedere al [portale di gestione di Azure](https://manage.windowsazure.com/), fare clic su **Mobile Services** e quindi sull'app.

	![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-selection.png)

1.  Fare clic sulla scheda **Push**, quindi su **Enable enhanced push** e su **Yes** per accettare la modifica della configurazione.

    ![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-enable-enhanced-push.png)

    Verrà aggiornata la configurazione del servizio mobile in modo da utilizzare la funzionalità di notifica push avanzata fornita da Hub di notifica. L'utilizzo di Hub di notifica è in parte gratuito con il servizio mobile a pagamento. Per ulteriori informazioni, vedere la pagina [Dettagli prezzi: servizi mobili](http://go.microsoft.com/fwlink/p/?LinkID=311786).

    **Importante**

    Questa operazione reimposta le credenziali push e modifica il comportamento dei metodi push negli script. Tali modifiche non possono essere annullate. Non utilizzare questo metodo per aggiungere un hub di notifica a un servizio mobile di produzione. Per indicazioni su come abilitare le notifiche push avanzate in un servizio mobile di produzione, vedere [queste linee guida](http://go.microsoft.com/fwlink/p/?LinkId=391951).

2.  Immettere i valori di **Segreto client** e **Package SID** ottenuti da WNS nel passaggio 4, quindi fare clic su **Save**.

	![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-push-tab.png)

    >[WACOM.NOTE]Le credenziali WNS per le notifiche push avanzate configurate nella scheda **Push** del portale vengono condivise con Hub di notifica per configurare l'hub di notifica con l'app.
