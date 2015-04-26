<properties 
	pageTitle="Invio di messaggi e-mail mediante SendGrid - Servizi mobili di Azure" 
	description="Informazioni su come usare il servizio SendGrid per inviare messaggi e-mail dall'app Servizi mobili di Azure." 
	services="mobile-services" 
	documentationCenter="" 
	authors="thinkingserious" 
	manager="sendgrid" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-multiple" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="10/27/2014" 
	ms.author="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork"/>


# Invio di messaggi e-mail da Servizi mobili con SendGrid

In questo argomento viene illustrato come aggiungere funzionalità di posta elettronica a un servizio mobile. In questa esercitazione si apprenderà come aggiungere script sul lato server per inviare messaggi e-mail tramite SendGrid. Al termine dell'esercitazione, il servizio mobile invierà un messaggio e-mail ogni volta che viene inserito un record.

SendGrid è un [servizio di posta elettronica basato sul cloud] che offre [recapito affidabile di messaggi e-mail transazionali], scalabilità e analisi in tempo reale, oltre ad API flessibili che agevolano l'integrazione personalizzata. Per altre informazioni, vedere <http://sendgrid.com>.

Questa esercitazione descrive le operazioni di base per abilitare le funzionalità di posta elettronica:

1. [Creare un account SendGrid]
2. [Aggiungere uno script per l'invio di messaggi e-mail]
3. [Inserire dati per la ricezione di messaggi e-mail]

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. Prima di iniziare questa esercitazione, è necessario completare le procedure illustrate in [Introduzione a Servizi mobili]. 

<h2><a name="sign-up"></a>Creare un nuovo account SendGrid</h2>

[AZURE.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

<h2><a name="add-script"></a>Registrare un nuovo script per l'invio di messaggi e-mail</h2>

1. Accedere al [portale di gestione di Azure], fare clic su **Servizi mobili** e quindi sul servizio mobile.

2. Nel portale di gestione fare clic sulla scheda **Dati**, quindi sulla tabella **TodoItem**. 

	![][1]

3. In **todoitem** fare clic sulla scheda **Script** e selezionare **Inserisci**.
   
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

	- **_username_ e _password_**: le credenziali di SendGrid identificate in [Creare un account SendGrid].

	- **_email-address_**: l'indirizzo a cui inviare il messaggio e-mail. In un'app effettiva è possibile usare tabelle per archiviare e recuperare indirizzi e-mail. Durante il test dell'app, usare il proprio indirizzo e-mail.

	- **_from-address_**: l'indirizzo di origine del messaggio e-mail. È consigliabile usare l'indirizzo di un dominio registrato appartenente all'organizzazione dell'utente. 

     > [AZURE.NOTE] Se non si dispone di un dominio registrato, è possibile usare il dominio del servizio mobile in uso nel formato *notifications@_your-mobile-service_.azure-mobile.net*. I messaggi inviati al dominio del servizio mobile, tuttavia, vengono ignorati.

6. Fare clic su **Salva**. È stato configurato un script per l'invio di un messaggio e-mail ogni volta che viene inserito un record nella tabella **TodoItem**.

<h2><a name="insert-data"></a>Inserire dati di prova per la ricezione di messaggi e-mail</h2>

1. Nel progetto di app client eseguire l'app di guida introduttiva. 

	In questo argomento viene usata la versione dell'app di guida introduttiva disponibile in Windows Store.

2. Nell'app digitare un testo in **Insert a TodoItem**, quindi fare clic su **Save**.

	![][3]

3. Si riceverà un messaggio e-mail simile a quello mostrato nella seguente notifica. 

	![][4]

	In questa esercitazione si è appreso come configurare un servizio mobile per l'invio di messaggi e-mail tramite SendGrid.

## <a name="nextsteps"> </a>Passaggi successivi

Dopo avere sperimentato quanto sia semplice usare il servizio di posta elettronica SendGrid con Servizi mobili, usare
i collegamenti seguenti per altre informazioni su SendGrid.

-   Documentazione sull'API SendGrid:
    <https://sendgrid.com/docs>
-   Offerta speciale SendGrid per i clienti di Azure:
    <https://sendgrid.com/windowsazure.html>

<!-- Anchors. -->
[Creare un account SendGrid]: #sign-up
[Aggiungere uno script per l'invio di messaggi e-mail]: #add-script
[Inserire dati per la ricezione di messaggi e-mail]: #insert-data

<!-- Images. -->
[1]: ./media/store-sendgird-mobile-services-send-email-scripts/mobile-portal-data-tables.png
[2]: ./media/store-sendgird-mobile-services-send-email-scripts/mobile-insert-script-push2.png
[3]: ./media/store-sendgird-mobile-services-send-email-scripts/mobile-quickstart-push1.png
[4]: ./media/store-sendgird-mobile-services-send-email-scripts/mobile-receive-email.png

<!-- URLs. -->
[Introduzione a Servizi mobili]: /it-it/develop/mobile/tutorials/get-started
[pagina di iscrizione]: https://sendgrid.com/windowsazure.html
[Pagina delle credenziali per più utenti]: https://sendgrid.com/credentials
[Portale di gestione di Azure]: https://manage.windowsazure.com/
[servizio di posta elettronica basato sul cloud]: https://sendgrid.com/email-solutions
[recapito affidabile di messaggi e-mail transazionali]: https://sendgrid.com/transactional-email




<!--HONumber=42-->
