1.  Se l'app non è ancora stata registrata, passare alla pagina relativa all'[invio di app][invio di app] nel Centro per sviluppatori di app di Windows Store, accedere con il proprio account Microsoft e quindi fare clic su **App name**.

    ![][0]

2.  Immettere un nome per l'app in **Nome app**, fare clic su **Riserva nome applicazione** e quindi su **Salva**.

    ![][1]

    Verrà creata una nuova registrazione a Windows Store per l'app.

3.  In Visual Studio aprire il progetto creato dopo aver completato l'esercitazione [Introduzione a Servizi mobili][Introduzione a Servizi mobili].

4.  In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto, scegliere **Store** e quindi fare clic su **Associa applicazione a Store**.

    ![][2]

    Verrà visualizzata la procedura guidata **Associa l'applicazione con Windows Store**.

5.  Nella procedura guidata fare clic su **Accedi** e quindi accedere con il proprio account Microsoft.

6.  Selezionare l'app registrata nel passaggio 2, fare clic su **Avanti** e quindi su **Associa**.

    ![][3]

    Le necessarie informazioni di registrazione a Windows Store verranno aggiunte al manifesto dell'applicazione.

7.  Nella pagina di Windows Dev Center per la nuova app fare clic su **Servizi**.

    ![][4]

8.  Nella pagina Servizi fare clic su **Sito di servizi Live** in **Servizi mobili di Azure**.

    ![][5]

9.  In **Impostazioni app** prendere nota dei valori specificati nei campi **ID client**, **Chiave privata client** e **Identificatore di sicurezza del pacchetto (SID)**.

    ![][6]

    > [WACOM.NOTE]Il segreto client e il SID di pacchetto sono importanti credenziali di sicurezza. Non condividere questi valori con altri né distribuirli con l'app.

10. (Facoltativo) Fare clic su **Impostazioni API**, abilitare **Enhanced redirection security**, specificare un valore di `https://<mobile_service>.azure-mobile.net/login/microsoftaccount` in **URL di reindirizzamento**, quindi fare clic su **Salva**.

    ![][7]

    Ciò consente di abilitare l'autenticazione dell'account Microsoft per l'app.

11. Accedere al [portale di gestione di Azure][portale di gestione di Azure], fare clic su **Mobile Services** e quindi sull'app.

    ![][8]

12. Fare clic sulla scheda **Push**, immettere i valori di **Chiave privata client** e **Identificatore di sicurezza del pacchetto (SID)** ottenuti da WNS al passaggio 4, quindi fare clic su **Salva**.

    ![][9]

13. Fare clic sulla scheda **Identità**. Si noti che i valori di **Chiave privata client** e **Identificatore di sicurezza del pacchetto (SID)** risultano già impostati dal passaggio precedente. Immettere il valore di **ID client** di cui si è preso nota in precedenza, quindi fare clic su **Salva**.

    ![][10]

È ora possibile usare un account Microsoft per l'autenticazione nell'app.

 

  [invio di app]: http://go.microsoft.com/fwlink/p/?LinkID=266582
  [0]: ./media/mobile-services-register-windows-store-app/mobile-services-submit-win8-app.png
  [1]: ./media/mobile-services-register-windows-store-app/mobile-services-win8-app-name.png
  [Introduzione a Servizi mobili]: /it-it/develop/mobile/tutorials/get-started/#create-new-service
  [2]: ./media/mobile-services-register-windows-store-app/mobile-services-store-association.png
  [3]: ./media/mobile-services-register-windows-store-app/mobile-services-select-app-name.png
  [4]: ./media/mobile-services-register-windows-store-app/mobile-services-win8-edit-app.png
  [5]: ./media/mobile-services-register-windows-store-app/mobile-services-win8-edit2-app.png
  [6]: ./media/mobile-services-register-windows-store-app/mobile-services-win8-app-push-auth.png
  [7]: ./media/mobile-services-register-windows-store-app/mobile-services-win8-app-push-auth-2.png
  [portale di gestione di Azure]: https://manage.windowsazure.com/
  [8]: ./media/mobile-services-register-windows-store-app/mobile-services-selection.png
  [9]: ./media/mobile-services-register-windows-store-app/mobile-push-tab.png
  [10]: ./media/mobile-services-register-windows-store-app/mobile-services-identity-tab.png
