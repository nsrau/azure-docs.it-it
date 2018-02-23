---
title: Come usare il servizio di e-mail SendGrid (Node.js) | Microsoft Docs
description: Informazioni su come inviare messaggi di posta elettronica con il servizio di posta elettronica SendGrid disponibile in Azure. Gli esempi di codice sono scritti mediante l'API Node.js.
services: 
documentationcenter: nodejs
author: erikre
manager: wpickett
editor: 
ms.assetid: cac444b4-26b0-45ea-9c3d-eca28d57dacb
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 01/05/2016
ms.author: erikre
ms.openlocfilehash: 327cea3a24cc47a9cc463b37cc2346ebc475ef7f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-send-email-using-sendgrid-from-nodejs"></a>Come inviare messaggi di posta elettronica usando SendGrid da Node.js
Questa guida illustra come eseguire attività di programmazione comuni con il servizio di posta elettronica SendGrid in Azure. Gli esempi sono scritti usando l'API Node.js. Gli scenari presentati includono **creazione di messaggi di posta elettronica**, **invio di messaggi di posta elettronica**, **aggiunta di allegati**, **uso di filtri** e **aggiornamento delle proprietà**. Per altre informazioni su SendGrid e sull'invio della posta elettronica, vedere la sezione [Passaggi successivi](#next-steps) .

## <a name="what-is-the-sendgrid-email-service"></a>Informazioni sul servizio di posta elettronica SendGrid
SendGrid è un [servizio di posta elettronica basato sul cloud] che offre [recapito affidabile di messaggi di posta elettronica transazionali], scalabilità e analisi in tempo reale, oltre ad API flessibili che agevolano l'integrazione personalizzata. Gli scenari di utilizzo comuni di SendGrid includono:

* Invio automatico di ricevute ai clienti
* Amministrazione di liste di distribuzione per l'invio mensile ai clienti di volantini elettronici e offerte speciali
* Raccolta di metriche in tempo reale per elementi quali indirizzi di posta elettronica bloccati e velocità di risposta al cliente
* Generazione di report per agevolare l'identificazione delle tendenze
* Inoltro di richieste dei clienti
* Notifiche di posta elettronica dall'applicazione

Per altre informazioni, vedere [https://sendgrid.com](https://sendgrid.com).

## <a name="create-a-sendgrid-account"></a>Creazione di un account SendGrid
[!INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="reference-the-sendgrid-nodejs-module"></a>Riferimento al modulo SendGrid per Node.js
Il modulo SendGrid per Node.js può essere installato con il programma di gestione dei pacchetti per Node.js (npm) usando il comando seguente:

    npm install sendgrid

Dopo l'installazione, è possibile includere il modulo nell'applicazione usando il comando seguente:

    var sendgrid = require('sendgrid')(sendgrid_username, sendgrid_password);

Il modulo SendGrid esporta le funzioni **SendGrid** ed **Email**.
**SendGrid** è responsabile dell'invio di e-mail tramite API Web, mentre **Email** incapsula un'e-mail.

## <a name="how-to-create-an-email"></a>Procedura: Creare un messaggio di posta elettronica
Creare un messaggio di posta elettronica usando il modulo SendGrid è una procedura in due fasi. La prima consiste nel creare il messaggio usando la funzione Email, la seconda nell'inviarlo usando la funzione SendGrid. Di seguito è riportato un esempio di codice per la creazione di un nuovo messaggio mediante la funzione Email:

    var email = new sendgrid.Email({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.'
    });

È inoltre possibile specificare un messaggio HTML per i client che li supportano, configurando la proprietà html. Ad esempio: 

    html: This is a sample <b>HTML<b> email message.

Impostando sia la proprietà text che la proprietà html è possibile implementare il fallback graduale a contenuto testuale per i client che non supportano i messaggi HTML.

Per altre informazioni su tutte le proprietà supportate dalla funzione Email, vedere [sendgrid-nodejs][sendgrid-nodejs].

## <a name="how-to-send-an-email"></a>Procedura: Inviare un messaggio di posta elettronica
Dopo aver creato un messaggio di posta elettronica usando la funzione Email, è possibile inviarlo tramite SMTP o con l'API Web fornita da SendGrid. 

### <a name="web-api"></a>API Web
    sendgrid.send(email, function(err, json){
        if(err) { return console.error(err); }
        console.log(json);
    });

> [!NOTE]
> Sebbene gli esempi precedenti illustrino il passaggio di un oggetto di posta elettronica e una funzione di richiamata, è inoltre possibile richiamare direttamente la funzione send specificando direttamente le proprietà dei messaggi di posta elettronica. Ad esempio:   
> 
> `````
> sendgrid.send({
> to: 'john@contoso.com',
> from: 'anna@contoso.com',
> subject: 'test mail',
> text: 'This is a sample email message.'
> });
> `````
> 
> 

## <a name="how-to-add-an-attachment"></a>Procedura: Aggiungere un allegato
Per aggiungere allegati a un messaggio, specificare i nomi e i percorsi dei file nella proprietà **files**. Nell'esempio seguente viene illustrato l'invio di un allegato:

    sendgrid.send({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.',
        files: [
            {
                filename:     '',           // required only if file.content is used.
                contentType:  '',           // optional
                cid:          '',           // optional, used to specify cid for inline content
                path:         '',           //
                url:          '',           // == One of these three options is required
                content:      ('' | Buffer) //
            }
        ],
    });

> [!NOTE]
> Quando si usa la proprietà **files**, il file deve essere accessibile tramite [fs.readFile](http://nodejs.org/docs/v0.6.7/api/fs.html#fs.readFile). Se il file da allegare è ospitato in Archiviazione di Azure, ad esempio in un contenitore BLOB, prima di poterlo inviare come allegato usando la proprietà **files** è necessario copiarlo nell'archiviazione locale o in un'unità Azure.
> 
> 

## <a name="how-to-use-filters-to-enable-footers-and-tracking"></a>Procedura: Usare filtri per abilitare piè di pagina e monitoraggio
SendGrid fornisce funzionalità di posta elettronica aggiuntive attraverso l'uso di filtri. Si tratta di impostazioni che è possibile aggiungere a un messaggio di posta elettronica per abilitare funzionalità specifiche, ad esempio il monitoraggio del clic, Google Analytics, il monitoraggio delle sottoscrizioni e così via. Per un elenco completo dei filtri, vedere le [impostazioni dei filtri][Filter Settings].

È possibile applicare filtri a un messaggio usando la proprietà **filters**.
Ogni filtro è specificato da un hash che contiene impostazioni specifiche del filtro.
Negli esempi seguenti vengono illustrati i filtri per abilitare il piè di pagina e per il monitoraggio dei clic:

### <a name="footer"></a>Piè di pagina
    var email = new sendgrid.Email({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.'
    });

    email.setFilters({
        'footer': {
            'settings': {
                'enable': 1,
                'text/plain': 'This is a text footer.'
            }
        }
    });

    sendgrid.send(email);

### <a name="click-tracking"></a>Monitoraggio dei clic
    var email = new sendgrid.Email({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.'
    });

    email.setFilters({
        'clicktrack': {
            'settings': {
                'enable': 1
            }
        }
    });

    sendgrid.send(email);

## <a name="how-to-update-email-properties"></a>Procedura: Aggiornare le proprietà dei messaggi di posta elettronica
È possibile sovrascrivere alcune proprietà delle e-mail usando **set*Property*** oppure aggiungerle usando  **add*Property***. Ad esempio, è possibile aggiungere altri destinatari usando

    email.addTo('jeff@contoso.com');

oppure impostare un filtro utilizzando

    email.addFilter('footer', 'enable', 1);
    email.addFilter('footer', 'text/html', '<strong>boo</strong>');

Per altre informazioni, vedere [sendgrid-nodejs][sendgrid-nodejs].

## <a name="how-to-use-additional-sendgrid-services"></a>Procedura: Usare servizi aggiuntivi forniti da SendGrid
SendGrid offre API basate sul Web che è possibile usare per sfruttare altre funzionalità di SendGrid dall'applicazione Azure. Per informazioni dettagliate, vedere la [documentazione sull'API SendGrid][SendGrid API documentation].

## <a name="next-steps"></a>Passaggi successivi
A questo punto, dopo aver appreso le nozioni di base del servizio di posta elettronica SendGrid, usare i collegamenti seguenti per altre informazioni.

* Repository del modulo SendGrid per Node.js: [sendgrid-nodejs][sendgrid-nodejs]
* Documentazione relativa all'API SendGrid: <https://sendgrid.com/docs>
* Offerta speciale SendGrid per i clienti di Azure: [http://sendgrid.com/azure.html](https://sendgrid.com/windowsazure.html)

[special offer]: https://sendgrid.com/windowsazure.html
[sendgrid-nodejs]: https://github.com/sendgrid/sendgrid-nodejs
[Filter Settings]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
[SendGrid API documentation]: https://sendgrid.com/docs
[servizio di posta elettronica basato sul cloud]: https://sendgrid.com/email-solutions
[recapito affidabile di messaggi di posta elettronica transazionali]: https://sendgrid.com/transactional-email
