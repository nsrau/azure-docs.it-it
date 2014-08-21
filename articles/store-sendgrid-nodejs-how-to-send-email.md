<properties linkid="dev-nodejs-how-to-sendgrid-email-service" urlDisplayName="SendGrid Email Service" pageTitle="How to use the SendGrid email service (Node.js) - Azure" metaKeywords="Azure SendGrid, Azure email service, Azure SendGrid Node.js, Azure email Node.js" description="Learn how send email with the SendGrid email service on Azure. Code samples written using the Node.js API." metaCanonical="" services="" documentationCenter="Node.js" title="How to Send Email Using SendGrid from Node.js" authors="" solutions="" manager="" editor="" />

Come inviare messaggi di posta elettronica utilizzando SendGrid Node.js
=======================================================================

In questa guida viene illustrato come eseguire attività di programmazione comuni con il servizio di posta elettronica SendGrid in Azure. Gli esempi sono scritti utilizzando l'API Node.js. Gli scenari presentati includono **creazione di messaggi di posta elettronica**, **invio di messaggi di posta elettronica**, **aggiunta di allegati**, **utilizzo di filtri** e **aggiornamento delle proprietà**. Per ulteriori informazioni su SendGrid e sull'invio di messaggi di posta elettronica, vedere la sezione [Passaggi successivi](http://www.windowsazure.com/it-it/develop/nodejs/how-to-guides/blob-storage/#next-steps).

Sommario
--------

-   [Informazioni sul servizio di posta elettronica SendGrid](#whatis)
-   [Creazione di un account SendGrid](#createaccount)
-   [Riferimento al modulo SendGrid per Node.js](#reference)
-   [Procedura: Creare un messaggio di posta elettronica](#createemail)
-   [Procedura: Inviare un messaggio di posta elettronica](#sendemail)
-   [Procedura: Aggiungere un allegato](#addattachment)
-   [Procedura: Utilizzare filtri per abilitare piè di pagina, monitoraggio e analisi](#usefilters)
-   [Procedura: Aggiornare le proprietà dei messaggi di posta elettronica](#updateproperties)
-   [Procedura: Utilizzare servizi aggiuntivi forniti da SendGrid](#useservices)
-   [Passaggi successivi](#nextsteps)

Informazioni sul servizio di posta elettronica SendGrid
-------------------------------------------------------

SendGrid è un [servizio di posta elettronica basato sul cloud](http://sendgrid.com/solutions) che offre [recapito affidabile di messaggi di posta elettronica transazionali](http://sendgrid.com/transactional-email), scalabilità e analisi in tempo reale, oltre ad API flessibili che agevolano l'integrazione personalizzata. Gli scenari di utilizzo comuni di SendGrid includono:

-   Invio automatico di ricevute ai clienti
-   Amministrazione di liste di distribuzione per l'invio mensile ai clienti di volantini elettronici e offerte speciali
-   Raccolta di metriche in tempo reale per elementi quali indirizzi di posta elettronica bloccati e velocità di risposta al cliente
-   Generazione di report per agevolare l'identificazione delle tendenze
-   Inoltro di richieste dei clienti
-   Notifiche di posta elettronica dall'applicazione

Per ulteriori informazioni, visitare il sito <http://sendgrid.com>.

Creazione di un account SendGrid
--------------------------------

[WACOM.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

Riferimento al modulo SendGrid per Node.js
------------------------------------------

Il modulo SendGrid per Node.js può essere installato con il programma di gestione dei pacchetti per Node.js (npm) utilizzando il comando seguente:

    npm install sendgrid

Dopo l'installazione, è possibile includere il modulo nell'applicazione utilizzando il comando seguente:

    var SendGrid = require('sendgrid')

Il modulo SendGrid esporta le funzioni **SendGrid** ed **Email**. **SendGrid** è responsabile dell'invio di messaggi di posta elettronica tramite SMTP o API Web, mentre **Email** incapsula un messaggio di posta elettronica.

Procedura: Creare un messaggio di posta elettronica
---------------------------------------------------

Creare un messaggio di posta elettronica utilizzando il modulo SendGrid è una procedura in due fasi. La prima consiste nel creare il messaggio utilizzando la funzione Email, la seconda nell'inviarlo utilizzando la funzione SendGrid. Di seguito è riportato un esempio di codice per la creazione di un nuovo messaggio mediante la funzione Email:

    var mail = new SendGrid.Email({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.'
        });

È inoltre possibile specificare un messaggio HTML per i client che li supportano, configurando la proprietà html. Ad esempio:

    html: This is a sample <b>HTML<b> email message.

Impostando sia la proprietà text che la proprietà html è possibile implementare il fallback graduale a contenuto testuale per i client che non supportano i messaggi HTML.

Per ulteriori informazioni su tutte le proprietà supportate dalla funzione Email, vedere [sendgrid-nodejs](https://github.com/sendgrid/sendgrid-nodejs).

Procedura: Inviare un messaggio di posta elettronica
----------------------------------------------------

Dopo aver creato un messaggio di posta elettronica utilizzando la funzione Email, è possibile inviarlo tramite SMTP o con l'API Web di SendGrid. Per informazioni dettagliate sui vantaggi e gli svantaggi di ogni API, vedere[il confronto tra SMTP e l'API Web](http://docs.sendgrid.com/documentation/get-started/integrate/examples/smtp-vs-rest/) nella documentazione di SendGrid.

Per utilizzare l'API SMTP o l'API Web API è innanzitutto necessario inizializzare la funzione SendGrid con l'utente e la chiave dell'account SendGrid, come illustrato di seguito:

    var sender = new SendGrid.SendGrid('user','key');

A questo punto è possibile inviare il messaggio tramite SMTP o con l'API Web. Le chiamate sono virtualmente identiche e passano il messaggio di posta elettronica e una funzione di richiamata opzionale. La funzione di richiamata viene utilizzata per determinare l'esito positivo o negativo dell'operazione. Negli esempi seguenti viene illustrato come inviare un messaggio sia tramite SMTP che con l'API Web.

### SMTP

    sender.smtp(mail, function(success, err){
        if(success) console.log('Email sent');
        else console.log(err);
        });

### API Web

    sender.send(mail, function(success, err){
        if(success) console.log('Email sent');
        else console.log(err);
        });


<div class="dev-callout">
<strong>Nota</strong>
<p>Anche se gli esempi precedenti illustrano il passaggio di un oggetto di posta elettronica e una funzione di richiamata, è inoltre possibile richiamare direttamente le funzioni send e smtp specificando direttamente le proprietà dei messaggi di posta elettronica. Ad esempio: </p>
    to: 'john@contoso.com',
    from: 'anna@contoso.com',
    subject: 'test mail',
    text: 'This is a sample email message.'
});
</pre>
</div>

Procedura: Aggiungere un allegato
---------------------------------

Per aggiungere allegati a un messaggio, specificare i nomi e i percorsi dei file nella proprietà **files**. Nell'esempio seguente viene illustrato l'invio di un allegato:

    sender.send({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.',
        files: {
            'file1.txt': __dirname + '/file1.txt',
            'image.jpg': __dirname + '/image.jpg'
        }
    });

<div class="dev-callout">
<strong>Nota</strong>
<p>Quando si utilizza la proprietà **files**, il file deve essere accessibile tramite [fs.readFile](http://nodejs.org/docs/v0.6.7/api/fs.html#fs.readFile). Se il file da allegare è ospitato in un'archiviazione di Azure, ad esempio in un contenitore BLOB, prima di poterlo inviare come allegato utilizzando la proprietà **files** è necessario copiarlo nell'archiviazione locale o in un'unità Azure.</p>
</div>

Procedura: Utilizzare filtri per abilitare piè di pagina, monitoraggio e Twitter
--------------------------------------------------------------------------------

SendGrid fornisce funzionalità di posta elettronica aggiuntive attraverso l'utilizzo di filtri. Si tratta di impostazioni che è possibile aggiungere a un messaggio di posta elettronica per abilitare funzionalità specifiche, ad esempio il monitoraggio del clic, Google Analytics, il monitoraggio delle sottoscrizioni e così via. Per un elenco completo dei filtri, vedere [Impostazioni dei filtri](http://docs.sendgrid.com/documentation/api/smtp-api/filter-settings/).

È possibile applicare filtri a un messaggio utilizzando la proprietà **filters**. Ogni filtro è specificato da un hash che contiene impostazioni specifiche del filtro. Negli esempi seguenti vengono illustrati i filtri per abilitare il piè di pagina, il monitoraggio dei clic e Twitter:

### Piè di pagina

    sender.send({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.',
        filters: {
            'footer': {
                'settings': {
                    'enable': 1,
                    'text/plain': 'This is a text footer.'
                }
            }
        }
    });

### Monitoraggio dei clic

    sender.send({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.',
        filters: {
            'clicktrack': {
                'settings': {
                    'enable': 1
                }
            }
        }
    });

### Twitter

    sender.send({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.',
        filters: {
            'twitter': {
                'settings': {
                    'enable': 1,
                    'username': 'twitter_username',
                    'password': 'twitter_password'
                }
            }
        }
    });

Procedura: Aggiornare le proprietà dei messaggi di posta elettronica
--------------------------------------------------------------------

È possibile sovrascrivere alcune proprietà dei messaggi di posta elettronica utilizzando **set*Property*** oppure aggiungerle utilizzando **add*Property***. Ad esempio, è possibile aggiungere altri destinatari utilizzando

    email.addTo('jeff@contoso.com');

oppure impostare un filtro utilizzando

    email.setFilterSetting({
      'footer': {
        'setting': {
          'enable': 1,
          'text/plain': 'This is a footer.'
        }
      }
    });

Per ulteriori informazioni, vedere [sendgrid-nodejs](https://github.com/sendgrid/sendgrid-nodejs).

Procedura: Utilizzare servizi aggiuntivi forniti da SendGrid
------------------------------------------------------------

SendGrid offre API basate sul Web che è possibile utilizzare per sfruttare altre funzionalità di SendGrid dall'applicazione Azure. Per informazioni dettagliate, vedere la [documentazione sull'API SendGrid](http://docs.sendgrid.com/documentation/api/).

Passaggi successivi
-------------------

A questo punto, dopo aver appreso le nozioni di base del servizio di posta elettronica SendGrid, utilizzare i collegamenti seguenti per ulteriori informazioni.

-   Archivio del modulo SendGrid per Node.js: [sendgrid-nodejs](https://github.com/sendgrid/sendgrid-nodejs)
-   Documentazione sull'API SendGrid: &lt;http://docs.sendgrid.com/documentation/api/&gt;
-   Offerta speciale SendGrid per i clienti di Azure: &lt;http://sendgrid.com/azure.html&gt;

