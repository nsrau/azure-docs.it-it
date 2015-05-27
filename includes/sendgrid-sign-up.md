I clienti di Azure possono sbloccare 25.000 messaggi di posta elettronica gratuiti ogni mese. Questi 25.000 messaggi gratuiti mensili offrono l'accesso a funzionalità avanzate di creazione di report e analisi e a [tutte le API][] (Web, SMTP, Event, Parse e altre). Per informazioni sui servizi aggiuntivi forniti da SendGrid, vedere la pagina di [funzionalità di SendGrid][].

### Per iscriversi a un account SendGrid

1. Accedere al [portale di gestione di Azure][].

2. Nel riquadro inferiore del portale di gestione fare clic su **New**.

	![command-bar-new][command-bar-new]

3. Fare clic su **Marketplace**.

	![sendgrid-store][sendgrid-store]

4. Nella finestra di dialogo **Scegliere un'applicazione e servizio** selezionare **SendGrid** e fare clic sulla freccia destra.

5. Nella finestra di dialogo **Personalizza applicazione e servizio** selezionare il piano di **SendGrid** a cui iscriversi.

6. Immettere un nome per identificare il servizio **SendGrid** nelle impostazioni di Azure o usare il valore predefinito **SendGridEmailDelivery.Simplified.SMTPWebAPI**. I nomi devono essere composti da un numero di caratteri compreso tra 1 e 100 e possono includere solo caratteri alfanumerici, trattini, punti e caratteri di sottolineatura. Il nome deve essere univoco nell'elenco di elementi di Azure Store sottoscritti.

	![store-screen-2][store-screen-2]

7. Scegliere un valore per l'area, ad esempio Stati Uniti occidentali.

8. Fare clic sulla freccia destra.

9. Nella scheda **Review Purchase** rivedere le informazioni sul piano e sui prezzi e consultare le note legali. Per accettare le condizioni, fare clic sul segno di spunta. Dopo aver fatto clic sul segno di spunta, verrà avviato il [processo di provisioning dell'account SendGrid].

	![store-screen-3][store-screen-3]

10. Dopo aver confermato l'acquisto, si verrà reindirizzati nel dashboard di componenti aggiuntivi e verrà visualizzato il messaggio **Purchasing SendGrid**.

	![sendgrid-purchasing-message][sendgrid-purchasing-message]

	Il provisioning dell'account SendGrid verrà eseguito immediatamente e verrà visualizzato il messaggio **Successfully purchased Add-On SendGrid**. A questo punto la creazione di account e credenziali è stata completata ed è possibile inviare posta elettronica.

	Per modificare il piano di sottoscrizione o visualizzare le impostazioni di contatto di SendGrid, fare clic sul nome del servizio SendGrid per aprire il dashboard del Marketplace di SendGrid.

	![sendgrid-add-on-dashboard][sendgrid-add-on-dashboard]

	Per inviare posta elettronica tramite SendGrid, è necessario specificare le credenziali dell'account, ovvero nome utente e password.

### Per individuare le credenziali di SendGrid ###

1. Fare clic su **Connection Info**.

	![sendgrid-connection-info-button][sendgrid-connection-info-button]

2. Nella finestra di dialogo *Connection info* copiare i valori di **Password** e nome utente da utilizzare più avanti nell'esercitazione.

	![sendgrid-connection-info][sendgrid-connection-info]

	Per impostare le impostazioni di recapito della posta, fare clic sul pulsante **Manage**. Verrà visualizzata l'interfaccia Web Sendgrid.com, in cui è possibile effettuare l'accesso e aprire il pannello di controllo SendGrid.

	![sendgrid-control-panel][sendgrid-control-panel]

	Per altre informazioni introduttive su SendGrid, vedere la [guida introduttiva a SendGrid][].

<!--images-->

[command-bar-new]: ./media/sendgrid-sign-up/sendgrid_BAR_NEW.PNG
[sendgrid-store]: ./media/sendgrid-sign-up/sendgrid_offerings_store.png
[store-screen-2]: ./media/sendgrid-sign-up/sendgrid_store_scrn2.png
[store-screen-3]: ./media/sendgrid-sign-up/sendgrid_store_scrn3.png
[sendgrid-purchasing-message]: ./media/sendgrid-sign-up/sendgrid_purchasing_message.png
[sendgrid-add-on-dashboard]: ./media/sendgrid-sign-up/sendgrid_add-on_dashboard.png
[sendgrid-connection-info]: ./media/sendgrid-sign-up/sendgrid_connection_info.png
[sendgrid-connection-info-button]: ./media/sendgrid-sign-up/sendgrid_connection_info_button.png
[sendgrid-control-panel]: ./media/sendgrid-sign-up/sendgrid_control_panel.png

<!--Links-->

[funzionalità di SendGrid]: http://sendgrid.com/features
[portale di gestione di Azure]: https://manage.windowsazure.com
[guida introduttiva a SendGrid]: http://sendgrid.com/docs
[processo di provisioning dell'account SendGrid]: https://support.sendgrid.com/hc/articles/200181628-Why-is-my-account-being-provisioned-
[tutte le API]: https://sendgrid.com/docs/API_Reference/index.html


<!--HONumber=54-->