<properties linkid="dev-nodejs-how-to-sendgrid-email-service" urlDisplayName="SendGrid Email Service" pageTitle="How to use the SendGrid email service (Node.js) - Azure" metaKeywords="Azure SendGrid, Azure email service, Azure SendGrid Node.js, Azure email Node.js" description="Learn how send email with the SendGrid email service on Azure. Code samples written using the Node.js API." metaCanonical="" services="" documentationCenter="nodejs" title="How to Send Email Using SendGrid from Node.js" authors="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork" solutions="" manager="wpickett" editor="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="01/01/1900" ms.author="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork" />

# Come inviare messaggi di posta elettronica utilizzando SendGrid Node.js

Questa guida illustra come eseguire attività di programmazione comuni con il
servizio di posta elettronica SendGrid in Azure. Gli esempi sono scritti usando
l'API Node.js. Gli scenari presentati includono **creazione di messaggi di posta elettronica**,
**invio di messaggi di posta elettronica**,
**aggiunta di allegati**,
**utilizzo di filtri** e
**aggiornamento delle proprietà**. Per altre informazioni su SendGrid e sull'invio
di messaggi di posta elettronica, vedere la sezione [Passaggi successivi][Passaggi successivi].

## Sommario

-   [Informazioni sul servizio di posta elettronica SendGrid][Informazioni sul servizio di posta elettronica SendGrid]
-   [Creare un account SendGrid][Creare un account SendGrid]
-   [Riferimento al modulo SendGrid per Node.js][Riferimento al modulo SendGrid per Node.js]
-   [Procedura: Creare un messaggio di posta elettronica][Procedura: Creare un messaggio di posta elettronica]
-   [Procedura: Inviare un messaggio di posta elettronica][Procedura: Inviare un messaggio di posta elettronica]
-   [Procedura: Aggiungere un allegato][Procedura: Aggiungere un allegato]
-   [Procedura: Utilizzare filtri per abilitare piè di pagina, monitoraggio e analisi][Procedura: Utilizzare filtri per abilitare piè di pagina, monitoraggio e analisi]
-   [Procedura: Aggiornare le proprietà dei messaggi di posta elettronica][Procedura: Aggiornare le proprietà dei messaggi di posta elettronica]
-   [Procedura: Utilizzare servizi aggiuntivi forniti da SendGrid][Procedura: Utilizzare servizi aggiuntivi forniti da SendGrid]
-   [Passaggi successivi][1]

## <a name="whatis"> </a>Informazioni sul servizio di posta elettronica SendGrid

SendGrid è un [servizio di posta elettronica basato sul cloud][servizio di posta elettronica basato sul cloud] che offre
[recapito affidabile di messaggi di posta elettronica transazionali][recapito affidabile di messaggi di posta elettronica transazionali], scalabilità e analisi in tempo reale, oltre ad API
 flessibili che agevolano l'integrazione personalizzata. Gli scenari di utilizzo comuni di SendGrid
includono:

-   Invio automatico di ricevute ai clienti
-   Amministrazione di liste di distribuzione per l'invio mensile
    ai clienti di volantini elettronici e offerte speciali
-   Raccolta di metriche in tempo reale per elementi quali indirizzi di posta elettronica bloccati e
    velocità di risposta al cliente
-   Generazione di report per agevolare l'identificazione delle tendenze
-   Inoltro di richieste dei clienti
-   Notifiche di posta elettronica dall'applicazione

Per altre informazioni, visitare il sito [][]<http://sendgrid.com></a>.

## <a name="createaccount"> </a>Creare un account SendGrid

[WACOM.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="reference"> </a>Riferimento al modulo SendGrid per Node.js

Il modulo SendGrid per Node.js può essere installato con il programma
di gestione dei pacchetti per Node.js (npm) usando il comando seguente:

    npm install sendgrid

Dopo l'installazione, è possibile includere il modulo nell'applicazione
usando il comando seguente:

    var SendGrid = require('sendgrid')

Il modulo SendGrid esporta le funzioni **SendGrid** ed **Email**.
**SendGrid** è responsabile dell'invio di messaggi di posta elettronica tramite SMTP o API Web
, mentre **Email** incapsula un messaggio di posta elettronica.

## <a name="createemail"> </a>Procedura: Creare un messaggio di posta elettronica

La procedura di creazione di un messaggio di posta elettronica usando il modulo SendGrid si divide in due fasi.
La prima consiste nel creare il messaggio usando la funzione Email,
la seconda nell'inviarlo usando la funzione SendGrid. Di seguito è riportato un esempio di codice
per la creazione di un nuovo messaggio mediante la funzione Email:

    var mail = new SendGrid.Email({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.'
    });

È inoltre possibile specificare un messaggio HTML per i client che supportano questi messaggi,
configurando la proprietà html. Ad esempio:

    html: This is a sample <b>HTML<b> email message.

Impostando sia la proprietà text che la proprietà html è possibile implementare
il fallback graduale a contenuto testuale per i client che non supportano i messaggi HTML.

Per altre informazioni su tutte le proprietà supportate dalla funzione Email,
vedere [sendgrid-nodejs][sendgrid-nodejs].

## <a name="sendemail"> </a>Procedura: Inviare un messaggio di posta elettronica

Dopo aver creato un messaggio di posta elettronica usando la funzione Email,
è possibile inviarlo tramite SMTP o con l'API Web di SendGrid. Per informazioni dettagliate
sui vantaggi e sugli svantaggi di ogni API, vedere il [confronto tra SMTP e l'API Web][confronto tra SMTP e l'API Web]
nella documentazione di SendGrid.

Per usare l'API SMTP o l'API Web è innanzitutto necessario inizializzare
la funzione SendGrid con l'utente e la chiave dell'account SendGrid,
come illustrato di seguito:

    var sender = new SendGrid('user','key');

A questo punto è possibile inviare il messaggio tramite SMTP o con l'API Web. Le chiamate
sono virtualmente identiche e passano il messaggio di posta elettronica
e una funzione di richiamata opzionale. La funzione di richiamata viene usata per determinare
l'esito positivo o negativo dell'operazione. Gli esempi seguenti illustrano come inviare
un messaggio sia tramite SMTP sia con l'API Web.

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
<p>Anche se gli esempi precedenti illustrano il passaggio di un oggetto di posta elettronica e
una funzione di richiamata, &egrave; inoltre possibile richiamare direttamente le funzioni send e smtp
specificando direttamente le propriet&agrave; dei messaggi di posta elettronica. Ad esempio:</p>
<pre class="prettyprint">sender.send({
    to: 'john@contoso.com',
    from: 'anna@contoso.com',
    subject: 'test mail',
    text: 'This is a sample email message.'
});
</pre>
</div>

## <a name="addattachment"> </a>Procedura: Aggiungere un allegato

Per aggiungere allegati a un messaggio, specificare i nomi e i percorsi dei file
nella proprietà **files**. L'esempio seguente illustra
l'invio di un allegato:

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
<p>Quando si usa la propriet&agrave; <strong>files</strong>, il file deve essere accessibile
tramite <a href="http://nodejs.org/docs/v0.6.7/api/fs.html#fs.readFile">fs.readFile</a>. Se il file da allegare &egrave; ospitato in un'archiviazione di Azure, ad esempio in un contenitore BLOB, prima di poterlo inviare come allegato utilizzando la propriet&agrave; <strong>files</strong> &egrave; necessario copiarlo nell'archiviazione locale o in un'unit&agrave; Azure.</p>
</div>

## <a name="usefilters"> </a>Procedura: Utilizzare filtri per abilitare piè di pagina, monitoraggio e Twitter

SendGrid fornisce funzionalità di posta elettronica aggiuntive mediante
l'uso di filtri. Si tratta di impostazioni che si possono aggiungere a un messaggio di posta elettronica per
abilitare funzionalità specifiche, ad esempio il monitoraggio dei clic, Google
Analytics, il monitoraggio delle sottoscrizioni e così via. Per un elenco completo dei filtri,
vedere [Impostazioni dei filtri][Impostazioni dei filtri].

È possibile applicare filtri a un messaggio usando la proprietà **filters**.
Ogni filtro è specificato da un hash contenente impostazioni specifiche del filtro.
Gli esempi seguenti illustrano i filtri per abilitare il piè di pagina, il monitoraggio dei clic e
Twitter.

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

È possibile sovrascrivere alcune proprietà dei messaggi di posta elettronica mediante **set*Property***
oppure aggiungerle usando **add*Property***. Ad esempio, è possibile aggiungere
altri destinatari usando

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

Per ulteriori informazioni, vedere [sendgrid-nodejs][sendgrid-nodejs].

## <a name="useservices"> </a>Procedura: Utilizzare servizi aggiuntivi forniti da SendGrid

SendGrid offre API basate sul Web che è possibile usare per sfruttare
altre funzionalità di SendGrid dall'applicazione Azure. Per
informazioni dettagliate, vedere la [documentazione dell'API SendGrid][documentazione dell'API SendGrid].

## <a name="nextsteps"> </a>Passaggi successivi

A questo punto, dopo aver appreso le nozioni di base del servizio di posta elettronica SendGrid, usare i
collegamenti seguenti per ulteriori informazioni.

-   Archivio del modulo SendGrid per Node.js: [sendgrid-nodejs][sendgrid-nodejs]
-   Documentazione dell'API SendGrid:
    <http://docs.sendgrid.com/documentation/api/>
-   Offerta speciale SendGrid per i clienti di Azure:
    [][2]<http://sendgrid.com/azure.html></a>

  [Passaggi successivi]: http://www.windowsazure.com/it-it/develop/nodejs/how-to-guides/blob-storage/#next-steps
  [Informazioni sul servizio di posta elettronica SendGrid]: #whatis
  [Creare un account SendGrid]: #createaccount
  [Riferimento al modulo SendGrid per Node.js]: #reference
  [Procedura: Creare un messaggio di posta elettronica]: #createemail
  [Procedura: Inviare un messaggio di posta elettronica]: #sendemail
  [Procedura: Aggiungere un allegato]: #addattachment
  [Procedura: Utilizzare filtri per abilitare piè di pagina, monitoraggio e analisi]: #usefilters
  [Procedura: Aggiornare le proprietà dei messaggi di posta elettronica]: #updateproperties
  [Procedura: Utilizzare servizi aggiuntivi forniti da SendGrid]: #useservices
  [1]: #nextsteps
  [servizio di posta elettronica basato sul cloud]: http://sendgrid.com/solutions
  [recapito affidabile di messaggi di posta elettronica transazionali]: http://sendgrid.com/transactional-email
  []: http://sendgrid.com
  [sendgrid-nodejs]: https://github.com/sendgrid/sendgrid-nodejs
  [confronto tra SMTP e l'API Web]: http://docs.sendgrid.com/documentation/get-started/integrate/examples/smtp-vs-rest/
  [Impostazioni dei filtri]: http://docs.sendgrid.com/documentation/api/smtp-api/filter-settings/
  [documentazione dell'API SendGrid]: http://docs.sendgrid.com/documentation/api/
  [2]: http://sendgrid.com/azure.html
