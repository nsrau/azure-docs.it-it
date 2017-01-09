
1. In Visual Studio Solution Explorer (Esplora soluzioni di Visual Studio), fare clic con il pulsante destro del mouse sul progetto app di Windows Store, quindi scegliere **Store** > **Associa applicazione a Store**.

    ![Associa l’app con Windows Store](./media/app-service-mobile-register-wns/notification-hub-associate-win8-app.png)
2. Nella procedura guidata fare clic su **Avanti** e accedere con il proprio account Microsoft. Immettere un nome per l'app in **Riserva un nuovo nome dell'app**, fare clic su **Riserva**.
3. Dopo la creazione della registrazione dell'app, selezionare il nuovo nome dell'app, fare clic su **Avanti** e quindi su **Associa**. Le informazioni di registrazione a Windows Store necessarie verranno aggiunte al manifesto dell'applicazione.
4. Ripetere i passaggi 1 e 3 per il progetto app di Windows Phone Store usando la stessa registrazione creata in precedenza per l'app di Windows Store.  
5. Aprire il [Windows Dev Center](https://dev.windows.com/en-us/overview) e accedervi con l'account Microsoft. Fare clic sulla nuova registrazione di app in **App personali**, quindi espandere **Servizi** > **Notifiche Push**.
6. Nella pagina **Notifiche Push** fare clic su **Live Services site** (Sito di servizi Live) in **Servizi notifica Push Windows (WNS) e App per dispositivi mobili di Microsoft Azure**. Prendere nota dei valori del **SID pacchetto** e del valore *attuale* in **Segreto dell'applicazione**. 

    ![Impostazione dell’app nel centro per sviluppatori](./media/app-service-mobile-register-wns/mobile-services-win8-app-push-auth.png)

   > [!IMPORTANT]
   > Il segreto applicazione e il SID di pacchetto sono importanti credenziali di sicurezza. Non condividere questi valori con altri utenti né distribuirli con l'app.
   >
   >


<!--HONumber=Dec16_HO2-->


