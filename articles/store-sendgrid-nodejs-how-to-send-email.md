<properties urlDisplayName="SendGrid Email Service" pageTitle="Come usare il servizio di posta elettronica SendGrid (Node. js) - Azure"metaKeywords="Azure SendGrid, Azure email service, Azure SendGrid Node.js, Azure email Node.js" description="Informazioni su come inviare messaggi di posta elettronica con il servizio di posta elettronica SendGrid disponibile in Azure. Gli esempi di codice sono scritti mediante l'API Node.js." metaCanonical="" services="" documentationCenter="nodejs" title="How to Send Email Using SendGrid from Node.js" authors="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork; matt.bernier@sendgrid.com" solutions="" manager="wpickett" editor="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="10/30/2014" ms.author="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork; matt.bernier@sendgrid.com" />





# Come inviare messaggi di posta elettronica usando SendGrid Node.js

Questa guida illustra come eseguire attività di programmazione comuni con il
Servizio di posta elettronica SendGrid in Azure. Gli esempi sono scritti usando l'API Node.js. Gli scenari presentati includono **creazione di messaggi di posta elettronica**, **invio di messaggi di posta elettronica**, **aggiunta di allegati**, **uso di filtri** e **aggiornamento delle proprietà**. Per altre informazioni su SendGrid e sull'invio di messaggi di posta elettronica, vedere la sezione [Passaggi successivi][].

## Sommario

* [Informazioni sul servizio di posta elettronica SendGrid][]   
* [Creare un account SendGrid][]   
* [Riferimento al modulo SendGrid per Node.js][]   
* [Procedura: Creare un messaggio di posta elettronica][]   
* [Procedura: Inviare un messaggio di posta elettronica][]   
* [Procedura: Aggiungere un allegato][]   
* [Procedura: Usare filtri per abilitare piè di pagina, monitoraggio e analisi][]   
* [Procedura: Aggiornare le proprietà dei messaggi di posta elettronica][]   
* [Procedura: Usare servizi aggiuntivi forniti da SendGrid][]   
* [Passaggi successivi][1]

## <a name="whatis"> </a>Informazioni sul servizio di posta elettronica SendGrid

SendGrid è un [servizio di posta elettronica basato sul cloud] che offre [recapito affidabile di messaggi di posta elettronica transazionali], scalabilità e analisi in tempo reale, oltre ad API flessibili che agevolano l'integrazione personalizzata. Gli scenari di utilizzo comuni di SendGrid includono:

-   Invio automatico di ricevute ai clienti
-   Amministrazione di liste di distribuzione per invio mensile ai clienti
    di volantini elettronici e offerte speciali
-   Raccolta di metriche in tempo reale, ad esempio per messaggi di posta elettronica bloccati e
    tempi di risposta dei clienti
-   Generazione di report per agevolare l'identificazione delle tendenze
-   Inoltro di richieste dei clienti
-   Notifiche di posta elettronica dall'applicazione

Per altre informazioni, vedere [https://sendgrid.com](https://sendgrid.com).

## <a name="createaccount"> </a>Creare un account SendGrid

[WACOM.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="reference"> </a>Riferimento al modulo SendGrid per Node.js

Il modulo SendGrid per Node.js può essere installato con il programma di gestione dei pacchetti per Node.js (npm) usando il comando seguente:

    npm install sendgrid

Dopo l'installazione, è possibile includere il modulo nell'applicazione usando il comando seguente:

    var SendGrid = require('sendgrid')

Il modulo SendGrid esporta le funzioni **SendGrid** ed **Email**. **SendGrid** è responsabile dell'invio di messaggi di posta elettronica tramite SMTP o API Web, mentre **Email** incapsula un messaggio di posta elettronica.

## <a name="createemail"> </a>Procedura: Creare un messaggio di posta elettronica

La creazione di un messaggio di posta elettronica usando il modulo SendGrid implica prima
la creazione di un messaggio di posta elettronica usando la funzione Email e quindi il suo invio
usando la funzione SendGrid. Di seguito è riportato un esempio di creazione di un
nuovo messaggio usando la funzione Email:

    var mail = new SendGrid.Email({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.'
    });

È inoltre possibile specificare un messaggio HTML per i client che li supportano, configurando la proprietà html. Ad esempio:

    html: This is a sample <b>HTML<b> email message.

Impostando sia la proprietà text che la proprietà html è possibile implementare il fallback graduale a contenuto testuale per i client che non supportano i messaggi HTML.

Per altre informazioni su tutte le proprietà supportate dalla funzione Email, vedere [sendgrid-nodejs][].

## <a name="sendemail"> </a>Procedura: Inviare un messaggio di posta elettronica

Dopo aver creato un messaggio di posta elettronica usando la funzione Email, è possibile inviarlo tramite SMTP o con l'API Web di SendGrid. Per informazioni dettagliate sui vantaggi e sulle differenze di ogni API, vedere il [confronto tra SMTP e l'API Web][] nella documentazione di SendGrid.

Per usare l'API SMTP o l'API Web API è innanzitutto necessario inizializzare la funzione SendGrid con l'utente e la chiave dell'account SendGrid, come illustrato di seguito:

    var sender = new SendGrid('user','key');

A questo punto è possibile inviare il messaggio tramite SMTP o con l'API Web. Le chiamate sono virtualmente identiche e passano il messaggio di posta elettronica e una funzione di richiamata opzionale. La funzione di richiamata viene usata per determinare l'esito positivo o negativo dell'operazione. Negli esempi seguenti viene illustrato come inviare un messaggio sia tramite SMTP che con l'API Web.

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
<p>Anche se gli esempi precedenti illustrano il passaggio di un oggetto di posta elettronica e una funzione di richiamata, è inoltre possibile richiamare direttamente le funzioni send e smtp specificando direttamente le proprietà dei messaggi di posta elettronica. Ad esempio:</p>
<pre class="prettyprint">sender.send({
    to: 'john@contoso.com',
    from: 'anna@contoso.com',
    subject: 'test mail',
    text: 'This is a sample email message.'
});
</pre>
</div>

## <a name="addattachment"> </a>Procedura: Aggiungere un allegato

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
<p>Quando si usa la proprietà <strong>files</strong> il file deve essere accessibile
tramite <a href="http://nodejs.org/docs/v0.6.7/api/fs.html#fs.readFile">fs.readFile</a>. Se il file da allegare è ospitato in un'archiviazione di Azure, ad esempio in un contenitore BLOB, è necessario copiarlo nell'archiviazione locale o in un'unità Azure prima di poterlo inviare come allegato usando la proprietà <strong>files</strong> .</p>
</div>

## <a name="usefilters"> </a>Procedura: Usare filtri per abilitare piè di pagina, monitoraggio e Twitter

SendGrid fornisce funzionalità di posta elettronica aggiuntive attraverso l'utilizzo di filtri. Si tratta di impostazioni che è possibile aggiungere a un messaggio di posta elettronica per abilitare funzionalità specifiche, ad esempio il monitoraggio del clic, Google Analytics, il monitoraggio delle sottoscrizioni e così via. Per un elenco completo dei filtri, vedere [Impostazioni dei filtri][].

È possibile applicare filtri a un messaggio usando la proprietà **filters**. Ogni filtro è specificato da un hash che contiene impostazioni specifiche del filtro. Negli esempi seguenti vengono illustrati i filtri per abilitare il piè di pagina, il monitoraggio dei clic e Twitter:

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

## <a name="updateproperties"> </a>Procedura: Aggiornare le proprietà dei messaggi di posta elettronica

Alcune proprietà relative alla posta elettronica possono essere sovrascritte usando **set*Property*** oppure è possibile aggiungerne usando **add*Property***. Ad esempio, è possibile aggiungere altri destinatari usando

    email.addTo('jeff@contoso.com');

oppure impostare un filtro usando

    email.setFilterSetting({
      'footer': {
        'setting': {
          'enable': 1,
          'text/plain': 'This is a footer.'
        }
      }
    });

Per altre informazioni, vedere [sendgrid-nodejs][].

## <a name="useservices"> </a>Procedura: Usare servizi aggiuntivi forniti da SendGrid

SendGrid offre API basate sul Web che è possibile usare per sfruttare altre funzionalità di SendGrid dall'applicazione Azure. Per informazioni dettagliate, vedere la [documentazione dell'API SendGrid][].

## <a name="nextsteps"> </a>Passaggi successivi

A questo punto, dopo aver appreso le nozioni di base del servizio di posta elettronica SendGrid, usare i collegamenti seguenti per ulteriori informazioni.

-   Archivio del modulo SendGrid per Node.js: [sendgrid-nodejs][]
-   Documentazione sull'API SendGrid:
    <https://sendgrid.com/docs>
-   Offerta speciale SendGrid per i clienti di Azure:
    [http://sendgrid.com/azure.html](https://sendgrid.com/windowsazure.html)

  [Passaggi successivi]: http://www.windowsazure.com/it-it/develop/nodejs/how-to-guides/blob-storage/#next-steps
  [Informazioni sul servizio di posta elettronica SendGrid]: #whatis
  [Creare un account SendGrid]: #createaccount
  [Riferimento al modulo SendGrid per Node.js]: #reference
  [Procedura: Creare un messaggio di posta elettronica]: #createemail
  [Procedura: Inviare un messaggio di posta elettronica]: #sendemail
  [Procedura: Aggiungere un allegato]: #addattachment
  [Procedura: Usare filtri per abilitare piè di pagina, monitoraggio e analisi]: #usefilters
  [Procedura: Aggiornare le proprietà dei messaggi di posta elettronica]: #updateproperties
  [Procedura: Usare servizi aggiuntivi forniti da SendGrid]: #useservices
  [1]: #nextsteps

  
  
  [offerta speciale]: https://sendgrid.com/windowsazure.html
  
  
  [sendgrid-nodejs]: https://github.com/sendgrid/sendgrid-nodejs
  [confronto tra SMTP e l'API Web]: https://sendgrid.com/docs/Integrate/index.html
  
  [Impostazioni dei filtri]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
  [documentazione dell'API SendGrid]: https://sendgrid.com/docs
  [servizio di posta elettronica basato sul cloud]: https://sendgrid.com/email-solutions
  [recapito affidabile di messaggi di posta elettronica transazionali]: https://sendgrid.com/transactional-email
