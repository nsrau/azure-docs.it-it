

1. Se l'app non è ancora stata registrata, passare alla pagina [Invia un'app] in Windows Store Dev Center, accedere con il proprio account Microsoft e quindi fare clic su **Nome app**.

   	![](./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-submit-win8-app.png)

2. Immettere un nome per l'app in **Nome app**, fare clic su **Riserva nome applicazione** e quindi su **Salva**.

   	![](./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-win8-app-name.png)

   	Verrà creata una nuova registrazione a Windows Store per l'app.

3. In Visual Studio aprire il progetto creato dopo aver completato l'esercitazione **Introduzione a Servizi mobili**.

4. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto, scegliere **Store** e quindi fare clic su **Associa applicazione a Store**.

  	![](./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-store-association.png)

   	Verrà visualizzata la procedura guidata **Associa l'applicazione con Windows Store**.

5. Nella procedura guidata fare clic su **Accedi** e quindi accedere con il proprio account Microsoft.

6. Selezionare l'app registrata nel passaggio 2, fare clic su **Avanti** e quindi su **Associa**.

   	![](./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-select-app-name.png)

   	Le informazioni di registrazione a Windows Store necessarie verranno aggiunte al manifesto dell'applicazione.

7. (Facoltativo) Ripetere i passaggi da 4 a 6 per registrare anche il progetto Windows Phone Store di un'app di Windows universale.

8. Nella pagina di Windows Dev Center per la nuova app fare clic su **Servizi**.

   	![](./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-win8-edit-app.png)

9. Nella pagina Servizi fare clic su **Sito di servizi Live** in **Servizi mobili di Azure**.

	![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-win8-edit2-app.png)

10. Fare clic su **Autenticazione del servizio** e prendere nota dei valori di **Segreto client** e **Identificatore di sicurezza del pacchetto (SID)**.

   	![](./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-win8-app-push-auth.png)

    > [AZURE.NOTE]Il segreto client e il SID di pacchetto sono importanti credenziali di sicurezza. Non condividere questi valori con altri né distribuirli con l'app.

11. Accedere al [portale di gestione di Azure], fare clic su **Servizi mobili** e quindi sull'app.

   	![](./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-selection.png)

12. Fare clic sulla scheda **Push**, immettere i valori di **Segreto client** e **SID pacchetto** ottenuti da WNS, quindi fare clic su **Salva**.

	>[AZURE.NOTE]Se si completa questa esercitazione con un servizio mobile precedente, nella parte inferiore della scheda **Push** è possibile che venga visualizzato un collegamento con l'indicazione **Abilita push avanzato**. Fare clic sul collegamento per aggiornare il servizio mobile per l'integrazione con Hub di notifica. Questa modifica non può essere annullata. Per indicazioni su come abilitare le notifiche push avanzate in un servizio mobile di produzione, vedere <a href="http://go.microsoft.com/fwlink/p/?LinkId=391951">queste linee guida</a>.

   	![](./media/mobile-services-notification-hubs-register-windows-store-app/mobile-push-tab.png)

	>[AZURE.NOTE]Le credenziali WNS per le notifiche push avanzate configurate nella scheda **Push** del portale vengono condivise con Hub di notifica per configurare l'hub di notifica per l'app.

<!-- URLs. -->
[Get started with Mobile Services]: ../articles/mobile-services-windows-store-get-started.md
[Invia un'app]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[portale di gestione di Azure]: https://manage.windowsazure.com/

<!---HONumber=August15_HO6-->