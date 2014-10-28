<properties title="How to make a phone call from Twilio (PHP) - Azure" pageTitle="How to make a phone call from Twilio (PHP) - Azure" metaKeywords="Azure PHP Twilio, Azure Twilio, Azure phone calls, Azure twilio, Azure SMS, Azure SMS, Azure voice calls, azure voice calls, Azure text messages, Azure text messages, PHP twilio Azure" description="Learn how to make a phone call and send a SMS message with the Twilio API service on Azure. Samples are for PHP application." documentationCenter="PHP" services="" solutions="" videoId="" scriptId="" authors="MicrosoftHelp@twilio.com; robmcm" manager="wpickett" editor="mollybos" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="MicrosoftHelp@twilio.com; robmcm"></tags>

# Come effettuare una chiamata tramite Twilio in un'applicazione PHP in Azure

Nell'esempio seguente viene illustrato come è possibile usare Twilio per effettuare una chiamata da una pagina Web PHP ospitata in Azure. L'applicazione risultante chiederà all'utente di inserire i valori relativi alla chiamata telefonica, come illustrato nella schermata seguente.

![Modulo di chiamata di Azure con Twilio e PHP][Modulo di chiamata di Azure con Twilio e PHP]

Per usare il codice in questo argomento è necessario eseguire le operazioni seguenti:

1.  Ottenere un account e un token di autenticazione Twilio. Per informazioni sui prezzi di Twilio, visitare la pagina [][]<http://www.twilio.com/pricing></a>. Per effettuare l'iscrizione e ottenere un account di valutazione gratuito, visitare la pagina [][1]<https://www.twilio.com/try-twilio></a>. Per informazioni sull'API fornita da Twilio, vedere [][2]<http://www.twilio.com/api></a>.
2.  Verificare il proprio numero di telefono come ID chiamante in uscita presso Twilio. Per informazioni su come verificare il numero di telefono, vedere [][3]<https://www.twilio.com/user/account/phone-numbers/verified#></a>. In alternativa, anziché usare un numero esistente, è possibile acquistare un numero di telefono Twilio.
    Ai fini di questo esempio, usare il numero di telefono verificato come valore **From** di **callform.php** illustrato più avanti.
3.  Ottenere la libreria Twilio per PHP. È possibile scaricarla da Github ([][4]<https://github.com/twilio/twilio-php></a>) o installarla come pacchetto PEAR. Per altre informazioni, vedere [][5]<https://github.com/twilio/twilio-php/blob/master/README.md></a>.
4.  Installare Azure SDK per PHP: Per informazioni generali sull'SDK e istruzioni per installarlo, vedere la pagina relativa alla [configurazione di Azure SDK per PHP][configurazione di Azure SDK per PHP].

## Creare un modulo Web per l'esecuzione di una chiamata

Il codice HTML seguente mostra come creare una pagina Web (**callform.html**) che consente di recuperare i dati utente per l'effettuazione di una chiamata:

    <html>
    <head>
        <title>Automated call form</title>
    </head>
    <body>
    <h1>Automated Call Form</h1>
    <p>Fill in all fields and click <b>Make this call</b>.</p>
    <form action="makecall.php" method="post">
    <table>
        <tr>
            <td>To:</td>
            <td><input type="text" size=50 name="callTo" value=""></td>
        </tr>
        <tr>
            <td>From:</td>
            <td><input type="text" size=50 name="callFrom" value=""></td>
        </tr>
        <tr>
            <td>Call message:</td>
            <td><input type="text" size=100 name="callText" value="Hello. This is the call text. Good bye." /></td>
        </tr>
        <tr>
            <td colspan=2><input type="submit" value="Make this call"></td>
        </tr>
    </table>
    </form>
    <br/>
    </body>
    </html>

## Creare il codice per l'esecuzione della chiamata

Nel codice seguente viene illustrato come creare una pagina Web (**makecall.php**), che viene chiamata quando l'utente invia il form visualizzato da **callform.html**. Il codice seguente crea il messaggio di chiamata e genera la chiamata. Nel codice seguente sostituire i valori segnaposto assegnati a **$sid** e **$token** con il proprio account e il token di autenticazione Twilio.

    <html>
    <head><title>Making call...</title></head>
    <body>
    <p>Your call is being made.</p>

    <?php
    require_once 'Services/Twilio.php';

    $sid = "your_account_sid";
    $token = "your_authentication_token";

    $from_number = $_POST['callFrom']; // Calls must be made from a registered Twilio number.
    $to_number = $_POST['callTo'];
    $message = $_POST['callText'];

    $client = new Services_Twilio($sid, $token, "2010-04-01");

    $call = $client->account->calls->create(
        $from_number, 
        $to_number,
        'http://twimlets.com/message?Message='.urlencode($message)
    );

    echo "Call status: ".$call->status."<br />";
    echo "URI resource: ".$call->uri."<br />";
    ?>
    </body>
    </html>

Oltre a effettuare la chiamata, **makecall.php** visualizza alcuni metadati della chiamata, come mostrato nell'esempio nella schermata seguente. Per altre informazioni sui metadati della chiamata, vedere [][6]<https://www.twilio.com/docs/api/rest/call#instance-properties></a>.

![Risposta a chiamata di Azure tramite Twilio e PHP][Risposta a chiamata di Azure tramite Twilio e PHP]

## Esecuzione dell'applicazione

Il passaggio successivo consiste nel distribuire l'applicazione in Siti Web di Azure. Negli articoli seguenti sono disponibili informazioni sulla creazione di un sito Web e sulla distribuzione del codice con Git, FTP o WebMatrix, benché non tutte le informazioni incluse in ogni articolo siano rilevanti:

-   [Creazione di un sito Web di Azure PHP-MySQL e distribuzione tramite Git][Creazione di un sito Web di Azure PHP-MySQL e distribuzione tramite Git]
-   [Creazione di un sito Web di Azure PHP-MySQL e distribuzione tramite FTP][Creazione di un sito Web di Azure PHP-MySQL e distribuzione tramite FTP]
-   [Creazione e distribuzione di un sito Web di Azure PHP-MySQL tramite WebMatrix][Creazione e distribuzione di un sito Web di Azure PHP-MySQL tramite WebMatrix]

## Passaggi successivi

Questo codice ha lo scopo di illustrare le funzionalità di base dell'utilizzo di Twilio con PHP in Azure. Prima di eseguire la distribuzione in Azure in produzione, può essere necessario aggiungere ulteriori funzionalità per la gestione degli errori o per altri scopi. Ad esempio:

-   Anziché usare un modulo Web, è possibile usare l'archiviazione BLOB o un database SQL di Azure per l'archiviazione di numeri di telefono e testo delle chiamate. Per informazioni sull'utilizzo dei BLOB di archiviazione di Azure in PHP, vedere [Utilizzo dell'archiviazione di Azure con applicazioni PHP][Utilizzo dell'archiviazione di Azure con applicazioni PHP]. Per informazioni sull'utilizzo di database SQL in PHP, vedere [Utilizzo del database SQL di Azure con applicazioni PHP][Utilizzo del database SQL di Azure con applicazioni PHP].
-   Il codice **makecall.php** utilizza l'URL ([][7]<http://twimlets.com/message></a>) fornito da Twilio per fornire una risposta TwiML (Twilio Markup Language) che indichi a Twilio come procedere con la chiamata. Ad esempio, la risposta TwiML restituita può contenere un verbo `<Say>`, che offre una versione parlata del testo al destinatario della chiamata. Anziché usare l'URL fornito da Twilio, è possibile creare un servizio personalizzato per rispondere alla richiesta di Twilio. Per altre informazioni, vedere [Come usare Twilio per le funzionalità voce ed SMS in PHP][Come usare Twilio per le funzionalità voce ed SMS in PHP]. Per altre informazioni su TwiML, visitare la pagina [][8]<http://www.twilio.com/docs/api/twiml></a> e per altre informazioni su `<Say>` e altri verbi Twilio, visitare la pagina [][9]<http://www.twilio.com/docs/api/twiml/say></a>.
-   Leggere le linee guida sulla sicurezza di Twilio all'indirizzo [][10]<https://www.twilio.com/docs/security></a>.

Per altre informazioni su Twilio, vedere [][11]<https://www.twilio.com/docs></a>.

## Vedere anche

-   [Come usare Twilio per le funzionalità voce ed SMS in PHP][Come usare Twilio per le funzionalità voce ed SMS in PHP]

  [Modulo di chiamata di Azure con Twilio e PHP]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPCallForm.jpg
  []: http://www.twilio.com/pricing
  [1]: http://www.twilio.com/try-twilio
  [2]: http://www.twilio.com/api
  [3]: https://www.twilio.com/user/account/phone-numbers/verified#
  [4]: https://github.com/twilio/twilio-php
  [5]: https://github.com/twilio/twilio-php/blob/master/README.md
  [configurazione di Azure SDK per PHP]: http://azurephp.interoperabilitybridges.com/articles/setup-the-windows-azure-sdk-for-php
  [6]: https://www.twilio.com/docs/api/rest/call#instance-properties
  [Risposta a chiamata di Azure tramite Twilio e PHP]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPMakeCall.jpg
  [Creazione di un sito Web di Azure PHP-MySQL e distribuzione tramite Git]: https://www.windowsazure.com/it-it/develop/php/tutorials/website-w-mysql-and-git/
  [Creazione di un sito Web di Azure PHP-MySQL e distribuzione tramite FTP]: https://www.windowsazure.com/it-it/develop/php/tutorials/website-w-mysql-and-ftp/
  [Creazione e distribuzione di un sito Web di Azure PHP-MySQL tramite WebMatrix]: https://www.windowsazure.com/it-it/develop/php/tutorials/website-w-mysql-and-webmatrix/
  [Utilizzo dell'archiviazione di Azure con applicazioni PHP]: http://msdn.microsoft.com/it-it/library/windowsazure/hh674502(v=vs.103).aspx
  [Utilizzo del database SQL di Azure con applicazioni PHP]: http://msdn.microsoft.com/it-it/library/windowsazure/hh674500(v=vs.103).aspx
  [7]: http://twimlets.com/message
  [Come usare Twilio per le funzionalità voce ed SMS in PHP]: ../partner-twilio-php-how-to-use-voice-sms
  [8]: http://www.twilio.com/docs/api/twiml
  [9]: http://www.twilio.com/docs/api/twiml/say
  [10]: http://www.twilio.com/docs/security
  [11]: http://www.twilio.com/docs
