<properties 
	pageTitle="Inviare posta elettronica usando SendGrid - Servizi mobili di Azure" 
	description="Informazioni su come usare il servizio SendGrid per inviare messaggi di posta elettronica dall'app Servizi mobili di Azure." 
	services="mobile-services" 
	documentationCenter="" 
	authors="Erikre" 
	manager="sendgrid" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="04/24/2015" 
	ms.author="Erikre"/>


# Invio di posta elettronica da Servizi mobili con SendGrid

In questo argomento viene illustrato come aggiungere funzionalità di posta elettronica a un servizio mobile. In questa esercitazione si apprenderà come aggiungere script sul lato server per inviare messaggi di posta elettronica tramite SendGrid. Al termine dell'esercitazione, il servizio mobile invierà un messaggio di posta elettronica ogni volta che viene inserito un record.

SendGrid è un [servizio di posta elettronica basato sul cloud] che offre [recapito affidabile di messaggi di posta elettronica transazionali], scalabilità e analisi in tempo reale, oltre ad API flessibili che agevolano l'integrazione personalizzata. Per altre informazioni, vedere <http://sendgrid.com>.

In questa esercitazione vengono descritte le operazioni di base per abilitare le funzionalità di posta elettronica:

1. [Creare un account SendGrid]
2. [Aggiungere uno script per l'invio di posta elettronica]
3. [Inserire dati per la ricezione di posta elettronica]

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. Prima di iniziare questa esercitazione, è necessario completare le procedure illustrate in [Introduzione a Servizi mobili].

## <a name="sign-up"></a>Creare un nuovo account SendGrid

[AZURE.INCLUDE [sendgrid-sign-up](../../includes/sendgrid-sign-up.md)]

## <a name="add-script"></a>Registrare un nuovo script per l'invio di posta elettronica

1. Accedere al [portale di gestione di Azure], fare clic su **Servizi mobili** e quindi sul servizio mobile.

2. Nel portale di gestione fare clic sulla scheda **Dati** e quindi sulla tabella **TodoItem**.

	![][1]

3. In **todoitem** fare clic sulla scheda **Script**, quindi selezionare **Insert**.
   
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

	- **_email-address_**: l'indirizzo a cui inviare il messaggio di posta elettronica. In un'app effettiva è possibile utilizzare tabelle per archiviare e recuperare indirizzi di posta elettronica. Durante il test dell'app, utilizzare il proprio indirizzo di posta elettronica.

	- **_from-address_**: l'indirizzo di origine del messaggio di posta elettronica. È consigliabile utilizzare l'indirizzo di un dominio registrato appartenente all'organizzazione dell'utente.

     >[AZURE.NOTE]Se non si dispone di un dominio registrato, è possibile usare il dominio del servizio mobile in uso nel formato *notifications@_your-mobile-service_.azure-mobile.net*. I messaggi inviati al dominio del servizio mobile, tuttavia, vengono ignorati.

6. Fare clic sul pulsante **Salva**. È stato configurato uno script per l'invio di un messaggio di posta elettronica ogni volta che viene inserito un record nella tabella **TodoItem**.

## <a name="insert-data"></a>Inserire dati di prova per la ricezione di posta elettronica

1. Nel progetto di app client eseguire l'app di guida introduttiva. 

	In questo argomento viene utilizzata la versione dell'app di guida introduttiva disponibile in Windows Store.

2. Nell'app digitare un testo in **Insert a TodoItem**, quindi fare clic su **Save**.

	![][3]

3. Si riceverà un messaggio di posta elettronica simile a quello mostrato nella notifica di seguito.

	![][4]

	In questa esercitazione si è appreso come configurare un servizio mobile per l'invio di posta elettronica tramite SendGrid.

## <a name="nextsteps"> </a>Passaggi successivi

Dopo avere sperimentato quanto sia semplice utilizzare il servizio di posta elettronica SendGrid con Servizi mobili, utilizzare i collegamenti seguenti per ulteriori informazioni su SendGrid.

-   Documentazione sull'API SendGrid: <https://sendgrid.com/docs>
-   Offerta speciale SendGrid per i clienti di Azure: <https://sendgrid.com/windowsazure.html>

<!-- Anchors. -->
[Creare un account SendGrid]: #sign-up
[Aggiungere uno script per l'invio di posta elettronica]: #add-script
[Inserire dati per la ricezione di posta elettronica]: #insert-data

<!-- Images. -->
[1]: ./media/store-sendgrid-mobile-services-send-email-scripts/mobile-portal-data-tables.png
[2]: ./media/store-sendgrid-mobile-services-send-email-scripts/mobile-insert-script-push2.png
[3]: ./media/store-sendgrid-mobile-services-send-email-scripts/mobile-quickstart-push1.png
[4]: ./media/store-sendgrid-mobile-services-send-email-scripts/mobile-receive-email.png

<!-- URLs. -->
[Introduzione a Servizi mobili]: /develop/mobile/tutorials/get-started
[sign up page]: https://sendgrid.com/windowsazure.html
[Multiple User Credentials page]: https://sendgrid.com/credentials
[portale di gestione di Azure]: https://manage.windowsazure.com/
[servizio di posta elettronica basato sul cloud]: https://sendgrid.com/email-solutions
[recapito affidabile di messaggi di posta elettronica transazionali]: https://sendgrid.com/transactional-email

 

<!---HONumber=July15_HO4-->