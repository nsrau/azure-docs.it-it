<properties linkid="develop-mobile-tutorials-send-email-with-sendgrid" urlDisplayName="Send Email Using SendGrid" pageTitle="Send email using SendGrid - Azure Mobile Services" metaKeywords="Azure SendGrid, SendGrid service, Azure emailing, mobile services email" description="Learn how to use the SendGrid service to send email from your Azure Mobile Services app." metaCanonical="" services="" documentationCenter="Mobile" title="Send email from Mobile Services with SendGrid" authors="" solutions="" manager="" editor="" />

Invio di posta elettronica da Servizi mobili con SendGrid
=========================================================

In questo argomento viene illustrato come aggiungere funzionalità di posta elettronica a un servizio mobile. In questa esercitazione si apprenderà come aggiungere script sul lato server per inviare messaggi di posta elettronica tramite SendGrid. Al termine dell'esercitazione, il servizio mobile invierà un messaggio di posta elettronica ogni volta che viene inserito un record.

SendGrid è un [servizio di posta elettronica basato sul cloud](http://sendgrid.com/solutions) che offre [recapito affidabile di messaggi di posta elettronica transazionali](http://sendgrid.com/transactional-email), scalabilità e analisi in tempo reale, oltre ad API flessibili che agevolano l'integrazione personalizzata. Per ulteriori informazioni, visitare il sito &lt;http://sendgrid.com&gt;.

In questa esercitazione vengono descritte le operazioni di base per abilitare le funzionalità di posta elettronica:

1.  [Creazione di un account SendGrid](#sign-up)
2.  [Aggiunta di uno script per l'invio di posta elettronica](#add-script)
3.  [Inserimento di dati per la ricezione di posta elettronica](#insert-data)

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. Prima di iniziare questa esercitazione, è necessario completare le procedure illustrate in [Introduzione a Servizi mobili](/en-us/develop/mobile/tutorials/get-started).

Creazione di un nuovo accountCreazione di un nuovo account SendGrid
-------------------------------------------------------------------

[WACOM.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

Registrazione di uno scriptRegistrazione di un nuovo script per l'invio di posta elettronica
--------------------------------------------------------------------------------------------

1.  Accedere al [portale di gestione di Azure](https://manage.windowsazure.com/), fare clic su **Mobile Services** e quindi sul servizio mobile.

2.  Nel portale di gestione fare clic sulla scheda **Data** e quindi sulla tabella **TodoItem**.

    ![](./media/store-sendgird-mobile-services-send-email-scripts/mobile-portal-data-tables.png)

3.  In **TodoItem** fare clic sulla scheda **Script**, quindi selezionare **Insert**.

    ![](./media/store-sendgird-mobile-services-send-email-scripts/mobile-insert-script-push2.png)

    Verrà visualizzata la funzione che viene richiamata quando si verifica un inserimento nella tabella **TodoItem**.

4.  Sostituire la funzione insert con il codice seguente:

         var SendGrid = require('sendgrid').SendGrid;
            
         function insert(item, user, request) {    
             request.execute({
                 success: function() {
                     // After the record has been inserted, send the response immediately to the client
                     request.respond();
                     // Send the email in the background
                     sendEmail(item);
                 }
             });

             function sendEmail(item) {
                 var sendgrid = new SendGrid('**username**', '**password**');       
                    
                 sendgrid.send({
                     to: '**email-address**',
                     from: '**from-address**',
                     subject: 'New to-do item',
                     text: 'A new to-do was added: ' + item.text
                 }, function(success, message) {
                     // If the email failed to send, log it as an error so we can investigate
                     if (!success) {
                         console.error(message);
                     }
                 });
             }
         }

5.  Sostituire i segnaposto nello script precedente con i valori corretti:

    -   ***username* e *password***: le credenziali di SendGrid identificate in [Creazione di un account SendGrid](#sign-up).

    -   ***email-address***: l'indirizzo a cui inviare il messaggio di posta elettronica. In un'app effettiva è possibile utilizzare tabelle per archiviare e recuperare indirizzi di posta elettronica. Durante il test dell'app, utilizzare il proprio indirizzo di posta elettronica.

    -   ***from-address***: l'indirizzo di origine del messaggio di posta elettronica. È consigliabile utilizzare l'indirizzo di un dominio registrato appartenente all'organizzazione dell'utente.

     **Nota**

     Se non si dispone di un dominio registrato, è possibile utilizzare il dominio del servizio mobile in uso nel formato **notifications@*servizio-mobile*.azure-mobile.net**. I messaggi inviati al dominio del servizio mobile, tuttavia, vengono ignorati.

6.  Fare clic sul pulsante **Save**. È stato configurato un script per l'invio di un messaggio di posta elettronica ogni volta che viene inserito un record nella tabella **TodoItem**.

Inserimento di dati di testInserimento di dati di test per la ricezione di posta elettronica
--------------------------------------------------------------------------------------------

1.  Nel progetto di app client eseguire l'app di guida introduttiva.

    In questo argomento viene utilizzata la versione dell'app di guida introduttiva disponibile in Windows Store.

2.  Nell'app digitare un testo in **Insert a TodoItem**, quindi fare clic su **Save**.

    ![](./media/store-sendgird-mobile-services-send-email-scripts/mobile-quickstart-push1.png)

3.  Si riceverà un messaggio di posta elettronica simile a quello mostrato nella notifica di seguito.

    ![](./media/store-sendgird-mobile-services-send-email-scripts/mobile-receive-email.png)

    In questa esercitazione si è appreso come configurare un servizio mobile per l'invio di posta elettronica tramite SendGrid.

Passaggi successivi
-------------------

Dopo avere sperimentato quanto sia semplice utilizzare il servizio di posta elettronica SendGrid con Servizi mobili, utilizzare i collegamenti seguenti per ulteriori informazioni su SendGrid.

-   Documentazione sull'API SendGrid: &lt;http://docs.sendgrid.com/documentation/api/&gt;
-   Offerta speciale SendGrid per i clienti di Azure: &lt;http://sendgrid.com/azure.html&gt;

