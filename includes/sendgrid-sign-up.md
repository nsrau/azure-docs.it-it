I clienti di Azure possono sbloccare 25.000 messaggi di posta elettronica gratuiti ogni mese. Questi 25.000 messaggi gratuiti mensili offrono l'accesso a funzionalità avanzate di creazione di report e analisi e a [tutte le API][all APIs] (Web, SMTP, Event, Parse e altre). Per informazioni sui servizi aggiuntivi offerti da SendGrid, vedere la pagina delle [soluzioni di SendGrid][SendGrid Solutions].

### <a name="to-sign-up-for-a-sendgrid-account"></a>Per iscriversi a un account SendGrid
1. Accedere al [portale di gestione di Azure][Azure Management Portal].
2. Fare clic su **Nuovo** dal menu a sinistra.

    ![command-bar-new][command-bar-new]
3. Fare clic su **Componenti aggiuntivi** e quindi **SendGrid Email Delivery** (Recapito email di SendGrid).

    ![sendgrid-store][sendgrid-store]
4. Completare il modulo di iscrizione e selezionare **Crea**.

    ![creazione di SendGrid][sendgrid-create]
5. Immettere un **nome** per identificare il proprio servizio SendGrid nelle impostazioni di Azure. I nomi devono essere composti da un numero di caratteri compreso tra 1 e 100 e possono includere solo caratteri alfanumerici, trattini, punti e caratteri di sottolineatura. Il nome deve essere univoco nell'elenco di elementi di Azure Store sottoscritti.
6. Immettere e confermare la **password**.
7. Scegliere la propria **sottoscrizione**.
8. Creare un nuovo **gruppo di risorse** o selezionarne uno esistente.
9. Nella sezione **Piano tariffario** selezionare il piano di SendGrid a cui si desidera iscriversi.

    ![prezzi di SendGrid][sendgrid-pricing]
10. Se disponibile, immettere un **codice di promozione**.
11. Inserire le **informazioni di contatto**.
12. Riesaminare e accettare le **Note legali**.
13. Dopo aver confermato l'acquisto verrà visualizzato un popup che indica che la **distribuzione è riuscita** e il proprio account verrà elencato nella sezione **Tutte le risorse**.

    ![all-resources][all-resources]

    Dopo avere completato l'acquisto e avere fatto clic sul pulsante **Gestisci** per avviare il processo di verifica dei messaggi di posta elettronica, SendGrid invierà un'email in cui si chiede di verificare l'account. Se non si riceve questa email o se si hanno problemi nella verifica dell'account, consultare le domande frequenti.

    ![manage][manage]

    **È possibile inviare fino a 100 email al giorno fino a quando non si è verificato l'account.**

    Per modificare il piano di sottoscrizione o visualizzare le impostazioni di contatto di SendGrid, fare clic sul nome del servizio SendGrid per aprire il dashboard del Marketplace di SendGrid.

    ![Impostazioni][settings]

    Per inviare un'email tramite SendGrid, è necessario specificare la chiave API.

### <a name="to-find-your-sendgrid-api-key"></a>Per trovare la chiave API per SendGrid
1. Fare clic su **Manage**.

    ![manage][manage]
2. Nel dashboard di SendGrid, selezionare **Impostazioni** e quindi **Chiavi API** nel menu a sinistra.

    ![chiavi API][api-keys]

3. Fare clic sul menu a discesa **Crea chiave API** e selezionare **General API Key** (Chiave API generale).

    ![chiave API generale][general-api-key]
4. Indicare almeno il **nome di questa chiave** e garantire l'accesso completo a **Mail Send** (Invio email) e selezionare **Salva**.

    ![access][access]
5. L'API verrà visualizzata una sola volta a questo punto. Assicurarsi di salvarla in modo sicuro.

### <a name="to-find-your-sendgrid-credentials"></a>Per individuare le credenziali di SendGrid
1. Fare clic sull'icona della chiave per trovare il **nome utente**.

    ![key][key]
2. La password è quella specificata al momento della configurazione. È possibile selezionare **Cambia password** o **Reimposta password** per apportare modifiche.

Per gestire le impostazioni di recapito della posta, fare clic sul **pulsante Gestisci**. Si verrà reindirizzati al [dashboard di SendGrid][SendGrid dashboard]

    ![manage][manage]

    For more information on sending email through SendGrid, visit the [Email API Overview][Email API Overview].

<!--images-->

[command-bar-new]: ./media/sendgrid-sign-up/new-addon.png
[sendgrid-store]: ./media/sendgrid-sign-up/sendgrid-store.png
[sendgrid-create]: ./media/sendgrid-sign-up/sendgrid-create.png
[sendgrid-pricing]: ./media/sendgrid-sign-up/sendgrid-pricing.png
[all-resources]: ./media/sendgrid-sign-up/all-resources.png
[manage]: ./media/sendgrid-sign-up/manage.png
[settings]: ./media/sendgrid-sign-up/settings.png
[api-keys]: ./media/sendgrid-sign-up/api-keys.png
[general-api-key]: ./media/sendgrid-sign-up/general-api-key.png
[access]: ./media/sendgrid-sign-up/access.png
[key]: ./media/sendgrid-sign-up/key.png
[SendGrid dashboard]: ./media/sendgrid-sign-up/dashboard.png

<!--Links-->

[SendGrid Solutions]: https://sendgrid.com/solutions
[Azure Management Portal]: https://manage.windowsazure.com
[SendGrid Getting Started]: http://sendgrid.com/docs
[SendGrid Provisioning Process]: https://support.sendgrid.com/hc/articles/200181628-Why-is-my-account-being-provisioned-
[all APIs]: https://sendgrid.com/docs/API_Reference/index.html
[Email API Overview]: https://sendgrid.com/docs/API_Reference/Web_API_v3/Mail/index.html

