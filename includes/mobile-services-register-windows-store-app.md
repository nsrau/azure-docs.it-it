
1. Se l'app non è ancora stata registrata, passare alla pagina [Invia un'app] nel Dev Center delle app di Windows Store, accedere con l'account Microsoft e quindi fare clic su **Nome app**.

   	![](./media/mobile-services-register-windows-store-app/mobile-services-submit-win8-app.png)

2. Immettere un nome per l'app in **Nome app**, fare clic su **Riserva nome dell'app** e quindi su **Salva**.

   	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-app-name.png)

   Verrà creata una nuova registrazione a Windows Store per l'app.

3. In Visual Studio aprire il progetto creato dopo aver completato l'esercitazione [Introduzione a Servizi mobili].

4. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto, scegliere **Store** e quindi fare clic su **Associa applicazione a Store**. 

  	![](./media/mobile-services-register-windows-store-app/mobile-services-store-association.png)

   Verrà visualizzata la procedura guidata **Associa l'applicazione con Windows Store**.

5. Nella procedura guidata fare clic su **Accedi**, quindi accedere con il proprio account Microsoft.

6. Selezionare l'app registrata nel passaggio 2, fare clic su **Avanti**, quindi su **Associa**.

   	![](./media/mobile-services-register-windows-store-app/mobile-services-select-app-name.png)

   Le necessarie informazioni di registrazione a Windows Store verranno aggiunte al manifesto dell'applicazione.    

7. Nella pagina di Windows Dev Center per la nuova app fare clic su **Servizi**. 

   	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-edit-app.png) 

8. Nella pagina Servizi fare clic su **Sito di servizi Live** in **Servizi mobili di Azure**.

	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-edit2-app.png) 

9. In **Impostazioni app** prendere nota dei valori di **ID client**, **Chiave privata client** e **Identificatore di sicurezza del pacchetto (SID)**. 

   	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-app-push-auth.png)

    >[AZURE.NOTE]La chiave privata client e il SID di pacchetto sono importanti credenziali di sicurezza. Non condividere questi valori con altri né distribuirli con l'app.

10. (Facoltativo) Fare clic su **API Settings**, abilitare **Enhanced redirection security**, specificare un valore di `https://<mobile_service>.azure-mobile.net/login/microsoftaccount` in **Redirect URL**, quindi fare clic su **Save**.

	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-app-push-auth-2.png)

	Ciò consente di abilitare l'autenticazione dell'account Microsoft per l'app.

11. Accedere al [portale di gestione di Azure], fare clic su **Servizi mobili** e quindi sull'app.

   	![](./media/mobile-services-register-windows-store-app/mobile-services-selection.png)

12. Fare clic sulla scheda **Push**, immettere i valori di **chiave privata client** e **pacchetto SID** ottenuti da WNS nel passaggio 4, quindi fare clic su **Salva**.

   	![](./media/mobile-services-register-windows-store-app/mobile-push-tab.png)

13. Fare clic sulla scheda **Identità**. Si noti che i valori di **chiave privata client** e **pacchetto SID** risultano già impostati dal passaggio precedente. Immettere il valore di **ID client** di cui si è precedentemente preso nota, quindi fare clic su **Salva**.

   	![](./media/mobile-services-register-windows-store-app/mobile-services-identity-tab.png)
 
È ora possibile usare un account Microsoft per l'autenticazione nell'app.  

<!-- Anchors. -->

<!-- Images. -->
 

<!-- URLs. -->
[Introduzione a Servizi mobili]: /it-it/develop/mobile/tutorials/get-started/#create-new-service
[Pagina Invia un'app]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Portale di gestione di Azure]: https://manage.windowsazure.com/

<!--HONumber=42-->
