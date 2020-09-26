---
author: georgewallace
ms.service: multiple
ms.topic: include
ms.date: 11/25/2018
ms.author: gwallace
ms.openlocfilehash: 4eb1a6f351cdf129611949049f762fe51cac4b16
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91377405"
---
I clienti di Azure possono sbloccare 25.000 messaggi di posta elettronica gratuiti ogni mese. Questi 25.000 di messaggi di posta elettronica mensili gratuiti ti consentono di accedere a report avanzati e analisi e a [tutte le API][all APIs] (Web, SMTP, Event, Parse e altro ancora). Per informazioni sui servizi aggiuntivi offerti da SendGrid, vedere la pagina delle [soluzioni di SendGrid][SendGrid Solutions].

### <a name="to-sign-up-for-a-sendgrid-account"></a>Per iscriversi a un account SendGrid
1. Accedere al [portale di Azure][Azure portal].
2. Nel menu portale di Azure o nella home page selezionare **Crea una risorsa**.

    ![Screenshot del menu portale di Azure con l'opzione Crea una risorsa selezionata.][command-bar-new]
3. Cercare e selezionare **SendGrid**.

    ![Screenshot della schermata portale di Azure Marketplace che mostra "SendGr" nella casella di ricerca e SendGrid selezionato nei risultati della ricerca.][sendgrid-store]
4. Completare il modulo di iscrizione e selezionare **Crea**.

    ![Screenshot della finestra di dialogo Crea un nuovo account SendGrid con i campi nome, password, sottoscrizione e gruppo di risorse compilati.][sendgrid-create]
5. Immettere un **nome** per identificare il proprio servizio SendGrid nelle impostazioni di Azure. I nomi devono essere composti da un numero di caratteri compreso tra 1 e 100 e possono includere solo caratteri alfanumerici, trattini, punti e caratteri di sottolineatura. Il nome deve essere univoco nell'elenco di elementi di Azure Store sottoscritti.
6. Immettere e confermare la **password**.
7. Scegliere la propria **sottoscrizione**.
8. Creare un nuovo **gruppo di risorse** o selezionarne uno esistente.
9. Nella sezione **Piano tariffario** selezionare il piano di SendGrid a cui si desidera iscriversi.

    ![Screenshot della finestra di dialogo Crea un nuovo account SendGrid con la sezione scegliere il piano tariffario aperto e il piano tariffario gratuito selezionato.][sendgrid-pricing]
10. Se disponibile, immettere un **codice di promozione**.
11. Immettere le **informazioni di contatto**.
12. Rivedere e accettare le **condizioni legali**.
13. Dopo aver confermato l'acquisto, viene visualizzata una finestra popup con **esito positivo** e l'account viene visualizzato nell'elenco.

    ![Screenshot della pagina degli account SendGrid che mostra il nuovo account ContosoSendGrid elencato.][all-resources]

    Dopo avere completato l'acquisto e avere fatto clic sul pulsante **Gestisci** per avviare il processo di verifica dei messaggi di posta elettronica, SendGrid invierà un'email in cui si chiede di verificare l'account. Se non si riceve questo messaggio di posta elettronica o si verificano problemi durante la verifica dell'account, vedere le domande frequenti.

    ![Screenshot della pagina dell'account ContosoSendGrid con il pulsante Gestisci evidenziato.][manage]

    **È possibile inviare fino a 100 email al giorno fino a quando non si è verificato l'account.**

    Per modificare il piano di sottoscrizione o visualizzare le impostazioni di contatto di SendGrid, fare clic sul nome del servizio SendGrid per aprire il dashboard del Marketplace di SendGrid.

    ![Screenshot che illustra che la pagina delle impostazioni per l'account ContosoSendGrid viene aperta selezionando tutte le impostazioni nella pagina dell'account ContosoSendGrid.][settings]

    Per inviare un'email tramite SendGrid, è necessario specificare la chiave API.

### <a name="to-find-your-sendgrid-api-key"></a>Per trovare la chiave API per SendGrid
1. Fare clic su **Manage**.

    ![Screenshot della pagina dell'account ContosoSendGrid con il pulsante Gestisci evidenziato.][manage]
2. Nel dashboard di SendGrid, selezionare **Impostazioni** e quindi **Chiavi API** nel menu a sinistra.

    ![Screenshot del dashboard SendGrid con l'elenco a discesa Impostazioni aperto e chiavi API selezionate.][api-keys]

3. Fare clic su **Crea chiave API**.

    ![Screenshot della schermata delle chiavi API con il pulsante Crea chiave API selezionato.][general-api-key]
4. Indicare almeno il **nome di questa chiave** e garantire l'accesso completo a **Mail Send** (Invio email) e selezionare **Salva**.

    ![Screenshot della schermata Aggiungi nuova chiave API generale con posta elettronica impostata su accesso completo, invio pianificato impostato su nessun accesso e pulsante Salva evidenziato.][access]
5. L'API verrà visualizzata una sola volta a questo punto. Assicurarsi di salvarla in modo sicuro.

### <a name="to-find-your-sendgrid-credentials"></a>Per individuare le credenziali di SendGrid
1. Fare clic sull'icona della chiave per trovare il **nome utente**.

    ! Screenshot della pagina dell'account ContosoSendGrid con l'icona della chiave evidenziata.] [chiave] di
2. La password è quella specificata al momento della configurazione. È possibile selezionare **Cambia password** o **Reimposta password** per apportare modifiche.

Per gestire le impostazioni di recapito della posta, fare clic sul **pulsante Gestisci**. Si verrà reindirizzati al dashboard di SendGrid.

![Screenshot della pagina dell'account ContosoSendGrid con il pulsante Gestisci evidenziato.][manage]

Per altre informazioni sull'invio di posta elettronica tramite SendGrid, vedere la [panoramica dell'API Email][Email API Overview].

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

<!--Links-->

[SendGrid Solutions]: https://sendgrid.com/solutions
[Azure portal]: https://portal.azure.com
[SendGrid Getting Started]: http://sendgrid.com/docs
[SendGrid Provisioning Process]: https://support.sendgrid.com/hc/articles/200181628-Why-is-my-account-being-provisioned-
[all APIs]: https://sendgrid.com/docs/API_Reference/index.html
[Email API Overview]: https://sendgrid.com/docs/API_Reference/Web_API_v3/Mail/index.html
