1.  Se l'app non è ancora stata registrata, passare alla pagina di
    [invio di app][1] nel Centro per sviluppatori di app di Windows
    Store, accedere con il proprio account Microsoft e quindi fare clic
    su **Nome app**.
    
       ![][0]

2.  Immettere un nome per l'app in **App name**, fare clic su **Reserve
    app name** e quindi su **Save**.
    
	![][1]
 
	Verrà creata una nuova registrazione a Windows Store per l'app.

3.  In Visual Studio 2012 Express per Windows 8 aprire il progetto
    creato dopo aver completato l'esercitazione [Introduzione a Servizi mobili](/en-us/develop/mobile/tutorials/get-started/#create-new-service).

4.  In Esplora soluzioni fare clic con il pulsante destro del mouse sul
    progetto, scegliere **Store** e quindi fare clic su **Associa
    applicazione a Store...**.

	![](./media/mobile-services-register-windows-store-app/mobile-services-store-association.png) 

	Verrà visualizzata la procedura guidata **Associa l'applicazione con Windows Store**.
1.  Nella procedura guidata fare clic su **Accedi** e quindi accedere
    con il proprio account Microsoft.

2.  Selezionare l'app registrata nel passaggio 2, fare clic su
    **Avanti** e quindi su **Associa**.
    
	![][3]

	Le necessarie informazioni di registrazione a Windows Store verranno aggiunte al manifesto dell'applicazione.    

3.  Nella pagina di Windows Dev Center per la nuova app fare clic su
    **Servizi**.
    
	![][4]

4.  Nella pagina Servizi fare clic su **Sito di servizi Live** in
    **Servizi mobili di Azure**.
    
	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-edit2-app.png)

5.  Fare clic su **Autenticazione del servizio** e prendere nota dei
    valori di **Segreto client** e **Identificatore di sicurezza del
    pacchetto (SID)**.
    
	![][6]
    
    > [WACOM.NOTE]Il segreto client e il SID di pacchetto sono
    > importanti credenziali di sicurezza. Non condividere questi valori
    > con altri né distribuirli con l'app.

6.  Accedere al [portale di gestione di Azure][2], fare clic su **Mobile
    Services** e quindi sull'app.

	![](./media/mobile-services-register-windows-store-app/mobile-services-selection.png)
	
1.  Fare clic sulla scheda **Push**, immettere i valori di **Segreto
    client** e **Package SID** ottenuti da WNS nel passaggio 4, quindi
    fare clic su **Save**.

 	![](./media/mobile-services-register-windows-store-app/mobile-push-tab.png) <!-- Anchors. -->

<!-- Images. -->
[0]: ./media/mobile-services-register-windows-store-app/mobile-services-submit-win8-app.png
[1]: ./media/mobile-services-register-windows-store-app/mobile-services-win8-app-name.png
[2]: ./media/mobile-services-register-windows-store-app/mobile-services-store-association.png
[3]: ./media/mobile-services-register-windows-store-app/mobile-services-select-app-name.png
[4]: ./media/mobile-services-register-windows-store-app/mobile-services-win8-edit-app.png
[5]: ./media/mobile-services-register-windows-store-app/mobile-services-win8-edit2-app.png
[6]: ./media/mobile-services-register-windows-store-app/mobile-services-win8-app-push-auth.png
[7]: ./media/mobile-services-register-windows-store-app/mobile-services-selection.png
[8]: ./media/mobile-services-register-windows-store-app/mobile-push-tab.png
<!-- URLs. -->



[1]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[2]: https://manage.windowsazure.com/
