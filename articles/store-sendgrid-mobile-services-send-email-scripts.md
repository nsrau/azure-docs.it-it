<properties urlDisplayName="Send Email Using SendGrid" pageTitle="Inviare posta elettronica usando SendGrid - Servizi mobili di Azure"metaKeywords ="Azure SendGrid, SendGrid service, Azure emailing, mobile services email" description="Informazioni su come usare il servizio SendGrid per inviare messaggi di posta elettronica dall'app Servizi mobili di Azure." metaCanonical="" services="" documentationCenter="Mobile" title="Send email from Mobile Services with SendGrid" authors="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork" solutions="" manager="wpickett" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="10/27/2014" ms.author="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork" />


# Inviare posta elettronica da Servizi mobili con SendGrid

In questo argomento viene illustrato come aggiungere funzionalità di posta elettronica a un servizio mobile. In questa esercitazione si apprenderà come aggiungere script sul lato server per inviare messaggi di posta elettronica tramite SendGrid. Al termine dell'esercitazione, il servizio mobile invierà un messaggio di posta elettronica ogni volta che viene inserito un record.

SendGrid è un [servizio di posta elettronica basato sul cloud] che offre [recapito affidabile di messaggi di posta elettronica transazionali], scalabilità e analisi in tempo reale, oltre ad API flessibili che agevolano l'integrazione personalizzata. Per altre informazioni, vedere <http://sendgrid.com>.

In questa esercitazione vengono descritte le operazioni di base per abilitare le funzionalità di posta elettronica:

1. [Creare un account SendGrid]
2. [Aggiungere uno script per l'invio di posta elettronica]
3. [Inserire dati per la ricezione di posta elettronica]

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. Prima di iniziare questa esercitazione, è necessario completare le procedure illustrate in [Introduzione a Servizi mobili]. 

<h2><a name="sign-up"></a>Creare un nuovo account SendGrid</h2>

[WACOM.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

<h2><a name="add-script"></a>Registrare un nuovo script per l'invio di posta elettronica</h2>

1. Accedere al [portale di gestione di Azure], fare clic su **Servizi mobili**, quindi sul servizio mobile desiderato.

2. Nel portale di gestione fare clic sulla scheda **Dati**, quindi sulla tabella **TodoItem**. 

	![][1]

3. In **todoitem** fare clic sulla scheda **Script**, quindi selezionare **Inserisci**.
   
	![][2]

	Verrà visualizzata la funzione che viene richiamata quando si verifica un inserimento nella tabella **TodoItem**.

4. Sostituire la funzione insert con il codice seguente:

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

5. Sostituire i segnaposto nello script precedente con i valori corretti:

	- **_username_ and _password_**: le credenziali di SendGrid identificate in [Creare un account SendGrid].

	- **_email-address_**: l'indirizzo a cui inviare il messaggio di posta elettronica. In un'app effettiva è possibile usare tabelle per archiviare e recuperare indirizzi di posta elettronica. Durante il test dell'app, usare il proprio indirizzo di posta elettronica.

	- **_from-address_**: l'indirizzo di origine del messaggio di posta elettronica. È consigliabile usare l'indirizzo di un dominio registrato appartenente all'organizzazione dell'utente. 

     <div class="dev-callout"><b>Nota</b>
     <p>Se non si dispone di un dominio registrato, è possibile usare il dominio del servizio mobile in uso nel formato <strong>notifications@<i>servizio-mobile</i>.azure-mobile.net</strong>. I messaggi inviati al dominio del servizio mobile, tuttavia, vengono ignorati.</p>
    </div> 

6. Fare clic su **Salva**. È stato configurato uno script per l'invio di un messaggio di posta elettronica ogni volta che viene inserito un record nella tabella **TodoItem**.

<h2><a name="insert-data"></a>Inserire dati di prova per la ricezione di posta elettronica</h2>

1. Nel progetto di app client eseguire l'app di guida introduttiva. 

	In questo argomento viene usata la versione dell'app di guida introduttiva disponibile in Windows Store.

2. Nell'app digitare un testo in **Insert a TodoItem**, quindi fare clic su **Save**.

	![][3]

3. Si riceverà un messaggio di posta elettronica simile a quello mostrato nella notifica di seguito. 

	![][4]

	In questa esercitazione si è appreso come configurare un servizio mobile per l'invio di posta elettronica tramite SendGrid.

## <a name="nextsteps"> </a>Passaggi successivi

Dopo avere sperimentato quanto sia semplice usare il servizio di posta elettronica SendGrid con Servizi mobili, usare i collegamenti seguenti per ulteriori informazioni su SendGrid.

-   Documentazione sull'API SendGrid:
    <https://sendgrid.com/docs>
-   Offerta speciale SendGrid per i clienti di Azure:
    <https://sendgrid.com/windowsazure.html>

<!-- Anchors. -->
[Creare un account SendGrid]: #sign-up
[Aggiungere uno script per l'invio di posta elettronica]: #add-script
[Inserire dati per la ricezione di posta elettronica]: #insert-data

<!-- Images. -->
[1]: ./media/store-sendgird-mobile-services-send-email-scripts/mobile-portal-data-tables.png
[2]: ./media/store-sendgird-mobile-services-send-email-scripts/mobile-insert-script-push2.png
[3]: ./media/store-sendgird-mobile-services-send-email-scripts/mobile-quickstart-push1.png
[4]: ./media/store-sendgird-mobile-services-send-email-scripts/mobile-receive-email.png

<!-- URLs. -->
[Introduzione a Servizi mobili]: /it-it/develop/mobile/tutorials/get-started
[Pagina di iscrizione]: https://sendgrid.com/windowsazure.html
[Pagina delle credenziali di più utenti]: https://sendgrid.com/credentials
[Portale di gestione di Azure]: https://manage.windowsazure.com/
[servizio di posta elettronica basato sul cloud]: https://sendgrid.com/email-solutions
[recapito di posta elettronica transazionale]: https://sendgrid.com/transactional-email

