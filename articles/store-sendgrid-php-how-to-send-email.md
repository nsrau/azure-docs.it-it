<properties title="How to use the SendGrid email service (PHP) - Azure" pageTitle="How to use the SendGrid email service (PHP) - Azure" metaKeywords="Azure SendGrid, Azure email service, Azure SendGrid PHP, Azure email PHP" description="Learn how send email with the SendGrid email service on Azure. Code samples written in PHP." documentationCenter="PHP" services="" manager="wpickett" editor="mollybos" authors="robmcm" scriptId="" videoId="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="robmcm"></tags>

# Come usare il servizio di posta elettronica SendGrid da PHP

In questa guida viene illustrato come eseguire attività di programmazione comuni con il servizio di posta elettronica SendGrid in Azure. Gli esempi sono scritti in PHP.
Gli scenari presentati includono la **creazione dei messaggi di posta elettronica**,l'**invio di messaggi di posta elettronica** e l'**aggiunta di allegati**. Per altre informazioni su SendGrid e sull'invio di messaggi di posta elettronica, vedere la sezione [Passaggi successivi][].

## Sommario

-   [Informazioni sul servizio di posta elettronica SendGrid][]
-   [Creare un account SendGrid][]
-   [Utilizzo di SendGrid dall'applicazione PHP][]
-   [Procedura: Inviare un messaggio di posta elettronica][]
-   [Procedura: Aggiungere un allegato][]
-   [Procedura: Utilizzare filtri per abilitare piè di pagina, monitoraggio e analisi][]
-   [Passaggi successivi][]

## <a name="bkmk_WhatIsSendGrid"> </a>Informazioni sul servizio di posta elettronica SendGrid

SendGrid è un [servizio di posta elettronica basato sul cloud][] che offre
[recapito affidabile di messaggi di posta elettronica transazionali][], scalabilità e analisi in tempo reale, oltre ad API
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

## <a name="bkmk_CreateSendGrid"> </a>Creare un account SendGrid

[WACOM.INCLUDE [sendgrid-sign-up][]]

## <a name="bkmk_UsingSendGridfromPHP"> </a>Utilizzo di SendGrid dall'applicazione PHP

L'utilizzo di SendGrid in un'applicazione PHP di Azure non richiede speciali
attività di configurazione o di scrittura del codice. Poiché SendGrid è un servizio, è possibile
accedervi da un'applicazione cloud esattamente come da un'applicazione
locale.

## <a name="bkmk_HowToSendEmail"> </a>Procedura: Inviare un messaggio di posta elettronica

È possibile inviare un messaggio di posta elettronica tramite SMTP o con l'API Web di
SendGrid.

### API SMTP

Per inviare un messaggio di posta elettronica tramite l'API SMTP di SendGrid, usare *Swift Mailer*, una
libreria basata su componenti per l'invio di messaggi di posta elettronica da applicazioni PHP. Per
scaricare la libreria *Swift Mailer*, accedere alla pagina
[][1]<http://swiftmailer.org/download></a>. L'invio di messaggi di posta elettronica tramite la libreria
prevede la creazione di istanze delle classi
<span class="auto-style2">Swift\_SmtpTransport</span>,
<span class="auto-style2">Swift\_Mailer</span> e
<span class="auto-style2">Swift\_Message</span>, l'impostazione delle proprietà
appropriate e la chiamata del metodo
<span class="auto-style2">Swift\_Mailer::send</span>.

    <?php
     include_once "lib/swift_required.php";
     /*
      * Create the body of the message (a plain-text and an HTML version).
      * $text is your plain-text email
      * $html is your html version of the email
      * If the reciever is able to view html emails then only the html
      * email will be displayed
      */ 
     $text = "Hi!\nHow are you?\n";
     $html = <<<EOM
           <html>
           <head></head>
           <body>
               <p>Hi!<br>
                   How are you?<br>
               </p>
           </body>
           </html>
           EOM;
     // This is your From email address
     $from = array('someone@example.com' => 'Name To Appear');
     // Email recipients
     $to = array(
           'john@contoso.com'=>'Destination 1 Name',
           'anna@contoso.com'=>'Destination 2 Name'
     );
     // Email subject
     $subject = 'Example PHP Email';

     // Login credentials
     $username = 'yoursendgridusername';
     $password = 'yourpassword';
     
     // Setup Swift mailer parameters
     $transport = Swift_SmtpTransport::newInstance('smtp.sendgrid.net', 587);
     $transport->setUsername($username);
     $transport->setPassword($password);
     $swift = Swift_Mailer::newInstance($transport);

     // Create a message (subject)
     $message = new Swift_Message($subject);

     // attach the body of the email
     $message->setFrom($from);
     $message->setBody($html, 'text/html');
     $message->setTo($to);
     $message->addPart($text, 'text/plain');
     
     // send message 
     if ($recipients = $swift->send($message, $failures))
     {
         // This will let us know how many users received this message
         echo 'Message sent out to '.$recipients.' users';
     }
     // something went wrong =(
     else
     {
         echo "Something went wrong - ";
         print_r($failures);
     }

### API Web

Usare la [funzione curl][] di PHP per inviare messaggi di posta elettronica tramite l'API Web di SendGrid.

    <?php

     $url = 'http://sendgrid.com/';
     $user = 'USERNAME';
     $pass = 'PASSWORD'; 

     $params = array(
          'api_user' => $user,
          'api_key' => $pass,
          'to' => 'john@contoso.com',
          'subject' => 'testing from curl',
          'html' => 'testing body',
          'text' => 'testing body',
          'from' => 'anna@sendgrid.com',
       );
       
     $request = $url.'api/mail.send.json';
     
     // Generate curl request
     $session = curl_init($request);
     
     // Tell curl to use HTTP POST
     curl_setopt ($session, CURLOPT_POST, true);
     
     // Tell curl that this is the body of the POST
     curl_setopt ($session, CURLOPT_POSTFIELDS, $params);
     
     // Tell curl not to return headers, but do return the response
     curl_setopt($session, CURLOPT_HEADER, false);
     curl_setopt($session, CURLOPT_RETURNTRANSFER, true);
     
     // obtain response
     $response = curl_exec($session);
     curl_close($session);
     
     // print everything out
     print_r($response);

L'API Web di SendGrid è molto simile a un'API REST, sebbene
non sia una vera API RESTful poiché nella maggior parte delle chiamate è possibile usare entrambi i verbi GET e POST
in modo intercambiabile.

## <a name="bkmk_HowToAddAttachment"> </a>Procedura: Aggiungere un allegato

### API SMTP

L'invio di un allegato tramite l'API SMTP prevede una riga di
codice aggiuntiva per lo script di esempio per l'invio di un messaggio di posta elettronica con Swift Mailer.

    <?php
     include_once "lib/swift_required.php";
     /*
      * Create the body of the message (a plain-text and an HTML version).
      * $text is your plain-text email
      * $html is your html version of the email
      * If the reciever is able to view html emails then only the html
      * email will be displayed
      */
     $text = "Hi!\nHow are you?\n";
      $html = <<<EOM
          <html>
          <head></head>
          <body>
             <p>Hi!<br>
                How are you?<br>
             </p>
          </body>
          </html>
     EOM;

     // This is your From email address
     $from = array('someone@example.com' => 'Name To Appear');
     
     // Email recipients
     $to = array(
          'john@contoso.com'=>'Destination 1 Name',
          'anna@contoso.com'=>'Destination 2 Name'
     );
     // Email subject
     $subject = 'Example PHP Email';
     
     // Login credentials
     $username = 'yoursendgridusername';
     $password = 'yourpassword';
     
     // Setup Swift mailer parameters
     $transport = Swift_SmtpTransport::newInstance('smtp.sendgrid.net', 587);
     $transport->setUsername($username);
     $transport->setPassword($password);
     $swift = Swift_Mailer::newInstance($transport);
     
     // Create a message (subject)
     $message = new Swift_Message($subject);
     
     // attach the body of the email
     $message->setFrom($from);
     $message->setBody($html, 'text/html');
     $message->setTo($to);
     $message->addPart($text, 'text/plain');
     $message->attach(Swift_Attachment::fromPath("path\to\file")->setFileName("file_name"));
     
     // send message 
     if ($recipients = $swift->send($message, $failures))
     {
          // This will let us know how many users received this message
          echo 'Message sent out to '.$recipients.' users';
     }
     // something went wrong =(
     else
     {
          echo "Something went wrong - "
          print_r($failures);
     }

Di seguito è riportata la riga di codice aggiuntiva:

     $message->attach(Swift_Attachment::fromPath("path\to\file")->setFileName('file_name'));

Questa riga di codice chiama il metodo attach sull'oggetto
<span class="auto-style2">Swift\_Message</span> e usa il metodo
statico <span class="auto-style2">fromPath</span> sulla classe
<span class="auto-style2">Swift\_Attachment</span> per recuperare e
allegare un file a un messaggio.

### API Web

L'invio di un allegato tramite l'API Web è molto simile all'invio di un
messaggio di posta elettronica con l'API Web. Si noti tuttavia che nell'esempio riportato di seguito,
il parametro array deve contenere questo elemento:

     'files['.$fileName.']' => '@'.$filePath.'/'.$fileName

    <?php

     $url = 'http://sendgrid.com/';
     $user = 'USERNAME';
     $pass = 'PASSWORD';
     
     $fileName = 'myfile';
     $filePath = dirname(__FILE__);

     $params = array(
         'api_user' => $user,
         'api_key' => $pass,
         'to' =>'john@contoso.com',
         'subject' => 'test of file sends',
         'html' => '<p> the HTML </p>',
         'text' => 'the plain text',
         'from' => 'anna@sendgrid.com',
         'files['.$fileName.']' => '@'.$filePath.'/'.$fileName
     );
     
     print_r($params);
     
     $request = $url.'api/mail.send.json';
     
     // Generate curl request
     $session = curl_init($request);
     
     // Tell curl to use HTTP POST
     curl_setopt ($session, CURLOPT_POST, true);
     
     // Tell curl that this is the body of the POST
     curl_setopt ($session, CURLOPT_POSTFIELDS, $params);
     
     // Tell curl not to return headers, but do return the response
     curl_setopt($session, CURLOPT_HEADER, false);
     curl_setopt($session, CURLOPT_RETURNTRANSFER, true);
     
     // obtain response
     $response = curl_exec($session);
     curl_close($session);
     
     // print everything out
     print_r($response);

## <a name="bkmk_HowToUseFilters"> </a>Procedura: Utilizzare filtri per abilitare piè di pagina, monitoraggio e analisi

SendGrid fornisce funzionalità di posta elettronica aggiuntive attraverso l'uso di
filtri. Si tratta di impostazioni che si possono aggiungere a un messaggio di posta elettronica per
abilitare funzionalità specifiche, ad esempio il monitoraggio dei clic, Google
Analytics, il monitoraggio delle sottoscrizioni e così via.

È possibile applicare filtri a un messaggio usando la proprietà filters. Ogni
filtro è specificato da un hash che contiene impostazioni specifiche del filtro. Nell'esempio
seguente viene abilitato il filtro piè di pagina e viene specificato un messaggio di testo
che verrà aggiunto nella parte inferiore del messaggio di posta elettronica:

    <?php
     /*
      * This example is used for Swift Mailer V4
      */
     include "./lib/swift_required.php";
     include 'SmtpApiHeader.php';
     
     $hdr = new SmtpApiHeader();
     // The list of addresses this message will be sent to
     // [This list is used for sending multiple emails using just ONE request to 
     SendGrid]
     $toList = array('john@contoso.com', 'anna@contoso.com');
     
     // Specify the names of the recipients
     $nameList = array('Name 1', 'Name 2');
     
     // Used as an example of variable substitution
     $timeList = array('4 PM', '5 PM');
     
     // Set all of the above variables
     $hdr->addTo($toList);
     $hdr->addSubVal('-name-', $nameList);
     $hdr->addSubVal('-time-', $timeList);
     
     // Specify that this is an initial contact message
     $hdr->setCategory("initial");
     
     // You can optionally setup individual filters here, in this example, we have 
     enabled the footer filter
     $hdr->addFilterSetting('footer', 'enable', 1);
     $hdr->addFilterSetting('footer', "text/plain", "Thank you for your business");
     
     // The subject of your email
     $subject = 'Example SendGrid Email';
     
     // Where is this message coming from. For example, this message can be from support@yourcompany.com, info@yourcompany.com
     $from = array('someone@example.com' =&gt; 'Name Of Your Company');
     
     // If you do not specify a sender list above, you can specifiy the user here. If 
     // a sender list IS specified above, this email address becomes irrelevant.
     $to = array('john@contoso.com'=&gt;'Personal Name Of Recipient');
     
     # Create the body of the message (a plain-text and an HTML version). 
     # text is your plain-text email 
     # html is your html version of the email
     # if the receiver is able to view html emails then only the html
     # email will be displayed
     
     /*
     * Note the variable substitution here =)
     */
     $text = <<<EOM 
     Hello -name-,
     Thank you for your interest in our products. We have set up an appointment to call you at -time- EST to discuss your needs in more detail.
     Regards,
     Fred
     EOM;
     
     $html = <<<EOM
     < html> 
     <head></head>
     <body>
     <p>Hello -name-,<br>
     Thank you for your interest in our products. We have set up an appointment
     to call you at -time- EST to discuss your needs in more detail.
     
     Regards,
     
     Fred, How are you?<br>
     </p>
     </body>
     < /html>
     EOM;
     
     // Your SendGrid account credentials
     $username = 'sendgridusername@yourdomain.com';
     $password = 'example';
     
     // Create new swift connection and authenticate
     $transport = Swift_SmtpTransport::newInstance('smtp.sendgrid.net', 25);
     $transport ->setUsername($username);
     $transport ->setPassword($password);
     $swift = Swift_Mailer::newInstance($transport);
     
     // Create a message (subject)
     $message = new Swift_Message($subject);
     
     // add SMTPAPI header to the message
     // *****IMPORTANT NOTE*****
     // SendGrid's asJSON function escapes characters. If you are using Swift 
     Mailer's
     // PHP Mailer functions, the getTextHeader function will also escape characters.
     // This can cause the filter to be dropped.
     $headers = $message->getHeaders();
     $headers->addTextHeader('X-SMTPAPI', $hdr->asJSON());
     
     // attach the body of the email
     $message->setFrom($from);
     $message->setBody($html, 'text/html');
     $message->setTo($to);
     $message->addPart($text, 'text/plain');
     
     // send message
     if ($recipients = $swift->send($message, $failures))
     {
     // This will let us know how many users received this message
     // If we specify the names in the X-SMTPAPI header, then this will always be 1.
     echo 'Message sent out to '.$recipients.' users';
     }

     // something went wrong =(
     else
     {
     echo "Something went wrong - ";
     print_r($failures);
     }

## <a name="bkmk_NextSteps"> </a>Passaggi successivi

A questo punto, dopo aver appreso le nozioni di base del servizio di posta elettronica SendGrid, usare i
collegamenti seguenti per ulteriori informazioni.

-   Documentazione su SendGrid: <https://sendgrid.com/docs>
-   Offerta speciale SendGrid per i clienti di Azure: <http://sendgrid.com/azure.html>

  [Passaggi successivi]: #bkmk_NextSteps
  [Informazioni sul servizio di posta elettronica SendGrid]: #bkmk_WhatIsSendGrid
  [Creare un account SendGrid]: #bkmk_CreateSendGrid
  [Utilizzo di SendGrid dall'applicazione PHP]: #bkmk_UsingSendGridfromPHP
  [Procedura: Inviare un messaggio di posta elettronica]: #bkmk_HowToSendEmail
  [Procedura: Aggiungere un allegato]: #bkmk_HowToAddAttachment
  [Procedura: Utilizzare filtri per abilitare piè di pagina, monitoraggio e analisi]: #bkmk_HowToUseFilters
  [servizio di posta elettronica basato sul cloud]: http://sendgrid.com/solutions
  [recapito affidabile di messaggi di posta elettronica transazionali]: http://sendgrid.com/transactional-email
  []: http://sendgrid.com
  [sendgrid-sign-up]: ../includes/sendgrid-sign-up.md
  [1]: http://swiftmailer.org/download
  [funzione curl]: http://php.net/curl
