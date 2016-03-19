
1. Se l'app non è ancora stata registrata, passare alla pagina [Invia un'app] in Windows Store Dev Center, accedere con il proprio account Microsoft e quindi fare clic su **Nome app**.

   	![](./media/mobile-services-register-windows-store-app/mobile-services-submit-win8-app.png)

2. Selezionare l'opzione che consente di **creare una nuova app riservando un nome univoco** e fare clic su **Continua**, quindi digitare il nome dell'app in **Nome app**, fare clic su **Riserva nome dell'app** e quindi su **Salva**.

   	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-app-name.png)

   	Verrà creata una nuova registrazione a Windows Store per l'app.

3. In Visual Studio aprire il progetto creato dopo aver completato l'esercitazione [Introduzione a Servizi mobili].

4. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto di app di Windows Store, scegliere **Store** e quindi fare clic su **Associa applicazione a Store**.

  	![](./media/mobile-services-register-windows-store-app/mobile-services-store-association.png)

   	Verrà visualizzata la procedura guidata **Associa l'applicazione con Windows Store**.

5. Nella procedura guidata fare clic su **Accedi** e quindi accedere con il proprio account Microsoft, selezionare l'app registrata al passaggio 2, fare clic su **Avanti** e quindi su **Associa**.

   	Le informazioni di registrazione a Windows Store necessarie verranno aggiunte al manifesto dell'applicazione.

6. (Facoltativo) Per un'app di Windows universale, ripetere i passaggi da 4 a 5 per il progetto Windows Phone Store.

6. Nella pagina di Windows Dev Center per la nuova app fare clic su **Servizi**.

   	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-edit-app.png)

7. Nella pagina Servizi fare clic su **Sito di servizi Live** in **Servizi mobili di Azure**.

	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-edit2-app.png)

8. Fare clic su **Impostazioni API**, selezionare **App client desktop o per dispositivi mobili**, specificare l'URL del servizio mobile come **Dominio di destinazione**, specificare un valore di `https://<mobile_service>.azure-mobile.net/login/microsoftaccount/` in **URL di reindirizzamento** e quindi fare clic su **Salva**.

	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-app-push-auth-2.png)

9. In **Impostazioni app** prendere nota dei valori specificati nei campi **ID client**, **Segreto client** e **Identificatore di sicurezza del pacchetto (SID)**.

   	![](./media/mobile-services-register-windows-store-app/mobile-services-win8-app-push-auth.png)

    >[AZURE.NOTE]Il segreto client e il SID di pacchetto sono importanti credenziali di sicurezza. Non condividere questi valori con altri né distribuirli con l'app.

10. Accedere al [portale di Azure classico](https://manage.windowsazure.com/), fare clic su **Servizi mobili** e quindi sull'app.

11. Fare clic sulla scheda **Identità**, immettere i valori di **Segreto client** e **Identificatore di sicurezza del pacchetto (SID)** ottenuti da WNS al passaggio 4, quindi fare clic su **Salva**.

   	![](./media/mobile-services-register-windows-store-app/mobile-push-tab.png)

13. Fare clic sulla scheda **Identità**. Si noti che i valori di **Segreto client** e **Identificatore di sicurezza del pacchetto (SID)** risultano già impostati dal passaggio precedente. Immettere il valore di **ID client** di cui si è preso nota in precedenza e quindi fare clic su **Salva**.

   	![](./media/mobile-services-register-windows-store-app/mobile-services-identity-tab.png)
 
È ora possibile usare un account Microsoft per l'autenticazione nell'app.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Introduzione a Servizi mobili]: /develop/mobile/tutorials/get-started/#create-new-service
[Invia un'app]: http://go.microsoft.com/fwlink/p/?LinkID=266582

<!---HONumber=AcomDC_1203_2015-->