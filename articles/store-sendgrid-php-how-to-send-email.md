<properties 
	pageTitle="Come usare il servizio di posta elettronica SendGrid (PHP) | Microsoft Azure" 
	description="Informazioni su come inviare messaggi di posta elettronica con il servizio di posta elettronica SendGrid disponibile in Azure. Gli esempi di codice sono scritti in PHP." 
	documentationCenter="php" 
	services="" 
	manager="sendgrid" 
	editor="mollybos" 
	authors="thinkingserious"/>

<tags 
	ms.service="multiple" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="PHP" 
	ms.topic="article" 
	ms.date="10/30/2014" 
	ms.author="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork; matt.bernier@sendgrid.com"/>

# Come usare il servizio di posta elettronica SendGrid da PHP

Questa guida illustra come eseguire attività di programmazione comuni con il servizio di posta elettronica SendGrid in Azure. Gli esempi sono scritti in PHP. Gli scenari presentati includono la **creazione dei messaggi di posta elettronica**, **l'invio di messaggi di posta elettronica** e ** l'aggiunta di allegati**. Per altre informazioni su SendGrid e sull'invio della posta elettronica, vedere la sezione [Passaggi successivi][].

## Sommario

-   [Informazioni sul servizio di posta elettronica SendGrid][]
-   [Creare un account SendGrid][]
-   [Usare SendGrid dall'applicazione PHP][]
-   [Procedura: Inviare un messaggio di posta elettronica][]
-   [Procedura: Aggiungere un allegato][]
-   [Procedura: Usare filtri per abilitare piè di pagina, monitoraggio e analisi][]
-   [Passaggi successivi][]

## <a name="bkmk_WhatIsSendGrid"> </a>Informazioni sul servizio di posta elettronica SendGrid

SendGrid è un [servizio di posta elettronica basato sul cloud] che offre [recapito affidabile di messaggi di posta elettronica transazionali], scalabilità e analisi in tempo reale, oltre ad API flessibili che agevolano l'integrazione personalizzata. Gli scenari di utilizzo comuni di SendGrid includono:

-   Invio automatico di ricevute ai clienti
-   Amministrazione di liste di distribuzione per l'invio mensile ai clienti di volantini elettronici e offerte speciali
-   Raccolta di metriche in tempo reale per elementi quali indirizzi di posta elettronica bloccati e velocità di risposta al cliente
-   Generazione di report per agevolare l'identificazione delle tendenze
-   Inoltro di richieste dei clienti
- Notifiche di posta elettronica dall'applicazione

Per altre informazioni, vedere [https://sendgrid.com][].

## <a name="bkmk_CreateSendGrid"> </a>Creare un account SendGrid

[AZURE.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="bkmk_UsingSendGridfromPHP"> </a>Usare SendGrid dall'applicazione PHP

L'uso di SendGrid in un'applicazione PHP di Azure non richiede speciali attività di configurazione o di scrittura del codice. Poiché SendGrid è un servizio, è possibile accedervi da un'applicazione cloud esattamente come da un'applicazione locale.

## <a name="bkmk_HowToSendEmail"> </a>Procedura: Inviare un messaggio di posta elettronica

È possibile inviare un messaggio di posta elettronica tramite SMTP o con l'API Web di SendGrid.

### API SMTP

Per inviare un messaggio di posta elettronica tramite l'API SMTP di SendGrid, usare *Swift Mailer*, una libreria basata su componenti per l'invio di messaggi di posta elettronica da applicazioni PHP. Per scaricare la libreria *Swift Mailer*, accedere alla pagina [http://swiftmailer.org/download][] v5.3.0 (usare [Composer] per installare Swift Mailer). L'invio di messaggi di posta elettronica tramite la libreria prevede la creazione di istanze delle classi <span class="auto-style2">Swift\_SmtpTransport</span>, <span class="auto-style2">Swift\_Mailer</span> e <span class="auto-style2">Swift\_Message</span> l'impostazione delle proprietà appropriate e la chiamata del metodo <span class="auto-style2">Swift\_Mailer::send</span>.

    <?php
     include_once "vendor/autoload.php";
     /*
      * Create the body of the message (a plain-text and an HTML version).
      * $text is your plain-text email
      * $html is your html version of the email
      * If the reciever is able to view html emails then only the html
      * email will be displayed
      */ 
     $text = "Hi!\nHow are you?\n";
     $html = "<html>
           <head></head>
           <body>
               <p>Hi!<br>
                   How are you?<br>
               </p>
           </body>
           </html>";
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

     $url = 'https://api.sendgrid.com/';
     $user = 'USERNAME';
     $pass = 'PASSWORD'; 

     $params = array(
          'api_user' => $user,
          'api_key' => $pass,
          'to' => 'john@contoso.com',
          'subject' => 'testing from curl',
          'html' => 'testing body',
          'text' => 'testing body',
          'from' => 'anna@contoso.com',
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

L'API Web di SendGrid è molto simile a un'API REST, sebbene non sia una vera API RESTful poiché nella maggior parte delle chiamate è possibile indifferentemente i verbi GET e POST.

## <a name="bkmk_HowToAddAttachment"> </a>Procedura: Aggiungere un allegato

### API SMTP

L'invio di un allegato tramite l'API SMTP prevede una riga di codice aggiuntiva nello script di esempio per l'invio di un messaggio di posta elettronica con Swift Mailer.

    <?php
     include_once "vendor/autoload.php";
     /*
      * Create the body of the message (a plain-text and an HTML version).
      * $text is your plain-text email
      * $html is your html version of the email
      * If the reciever is able to view html emails then only the html
      * email will be displayed
      */
     $text = "Hi!\nHow are you?\n";
      $html = "<html>
          <head></head>
          <body>
             <p>Hi!<br>
                How are you?<br>
             </p>
          </body>
          </html>";

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
          echo "Something went wrong - ";
          print_r($failures);
     }

Di seguito è riportata la riga di codice aggiuntiva:

     $message->attach(Swift_Attachment::fromPath("path\to\file")->setFileName('file_name'));

Questa riga di codice chiama il metodo attach sull'oggetto <span class="auto-style2">Swift\_Message</span> e usa il metodo statico <span class="auto-style2">fromPath</span> sulla classe <span class="auto-style2">Swift\_Attachment</span> per recuperare e allegare un file a un messaggio.

### API Web

L'invio di un allegato tramite l'API Web è molto simile all'invio di un messaggio di posta elettronica con l'API Web. Si noti tuttavia che nell'esempio riportato di seguito, il parametro array deve contenere questo elemento:

    'files['.$fileName.']' => '@'.$filePath.'/'.$fileName

Esempio:

    <?php

     $url = 'https://api.sendgrid.com/';
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
         'from' => 'anna@contoso.com',
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

## <a name="bkmk_HowToUseFilters"> </a>Procedura: Usare filtri per abilitare piè di pagina, monitoraggio e analisi

SendGrid fornisce funzionalità di posta elettronica aggiuntive attraverso l'uso di "filtri". Si tratta di impostazioni che è possibile aggiungere a un messaggio di posta elettronica per abilitare funzionalità specifiche, ad esempio il monitoraggio del clic, Google Analytics, il monitoraggio delle sottoscrizioni e così via.

È possibile applicare filtri a un messaggio usando la proprietà filters. Ogni filtro è specificato da un hash che contiene impostazioni specifiche del filtro. Nell'esempio seguente viene abilitato il filtro piè di pagina e viene specificato un messaggio di testo che verrà aggiunto nella parte inferiore del messaggio di posta elettronica: Per questo esempio si userà la[libreria sendgrid-php]. Usare [Composer] per installare la libreria:
    
    php composer.phar require sendgrid/sendgrid 2.1.1

Esempio:

    <?php
     /*
      * This example is used for sendgrid-php V2.1.1 (https://github.com/sendgrid/sendgrid-php/tree/v2.1.1)
      */
     include "vendor/autoload.php";

     $email = new SendGrid\Email();
     // The list of addresses this message will be sent to
     // [This list is used for sending multiple emails using just ONE request to SendGrid]
     $toList = array('john@contoso.com', 'anna@contoso.com');

     // Specify the names of the recipients
     $nameList = array('Name 1', 'Name 2');

     // Used as an example of variable substitution
     $timeList = array('4 PM', '5 PM');

     // Set all of the above variables
     $email->setTos($toList);
     $email->addSubstitution('-name-', $nameList);
     $email->addSubstitution('-time-', $timeList);

     // Specify that this is an initial contact message
     $email->addCategory("initial");

     // You can optionally setup individual filters here, in this example, we have 
     // enabled the footer filter
     $email->addFilter('footer', 'enable', 1);
     $email->addFilter('footer', "text/plain", "Thank you for your business");
     $email->addFilter('footer', "text/html", "Thank you for your business");

     // The subject of your email
     $subject = 'Example SendGrid Email';

     // Where is this message coming from. For example, this message can be from 
     // support@yourcompany.com, info@yourcompany.com
     $from = 'someone@example.com';

     // If you do not specify a sender list above, you can specifiy the user here. If 
     // a sender list IS specified above, this email address becomes irrelevant.
     $to = 'john@contoso.com';

     # Create the body of the message (a plain-text and an HTML version). 
     # text is your plain-text email 
     # html is your html version of the email
     # if the receiver is able to view html emails then only the html
     # email will be displayed

     /*
      * Note the variable substitution here =)
      */
     $text = "
     Hello -name-,
     Thank you for your interest in our products. We have set up an appointment to call you at -time- EST to discuss your needs in more detail.
     Regards,
     Fred";

     $html = "
     <html> 
     <head></head>
     <body>
     <p>Hello -name-,<br>
     Thank you for your interest in our products. We have set up an appointment
     to call you at -time- EST to discuss your needs in more detail.

     Regards,

     Fred<br>
     </p>
     </body>
     </html>";

     // set subject
     $email->setSubject($subject);

     // attach the body of the email
     $email->setFrom($from);
     $email->setHtml($html);
     $email->addTo($to);
     $email->setText($text);

     // Your SendGrid account credentials
     $username = 'sendgridusername@yourdomain.com';
     $password = 'example';

     // Create SendGrid object
     $sendgrid = new SendGrid($username, $password);

     // send message
     $response = $sendgrid->send($email);

     print_r($response);

## <a name="bkmk_NextSteps"> </a>Passaggi successivi

A questo punto, dopo aver appreso le nozioni di base del servizio di posta elettronica SendGrid, usare i collegamenti seguenti per altre informazioni.

-   Documentazione su SendGrid: <https://sendgrid.com/docs>
-   Libreria PHP di SendGrid: <https://github.com/sendgrid/sendgrid-php>
-   Offerta speciale SendGrid per i clienti di Azure: <https://sendgrid.com/windowsazure.html>

  [Passaggi successivi]: #bkmk_NextSteps
  [Informazioni sul servizio di posta elettronica SendGrid]: #bkmk_WhatIsSendGrid
  [Creare un account SendGrid]: #bkmk_CreateSendGrid
  [Usare SendGrid dall'applicazione PHP]: #bkmk_UsingSendGridfromPHP
  [Procedura: Inviare un messaggio di posta elettronica]: #bkmk_HowToSendEmail
  [Procedura: Aggiungere un allegato]: #bkmk_HowToAddAttachment
  [Procedura: Usare filtri per abilitare piè di pagina, monitoraggio e analisi]: #bkmk_HowToUseFilters
  [How to: Use Additional SendGrid Services]: #bkmk_HowToUseAdditionalSvcs
  [https://sendgrid.com]: https://sendgrid.com
  [https://sendgrid.com/transactional-email/pricing]: https://sendgrid.com/transactional-email/pricing
  [special offer]: https://www.sendgrid.com/windowsazure.html
  [Packaging and Deploying PHP Applications for Azure]: http://msdn.microsoft.com/library/windowsazure/hh674499(v=VS.103).aspx
  [http://swiftmailer.org/download]: http://swiftmailer.org/download
  [funzione curl]: http://php.net/curl
  [servizio di posta elettronica basato sul cloud]: https://sendgrid.com/email-solutions
  [recapito affidabile di messaggi di posta elettronica transazionali]: https://sendgrid.com/transactional-email
  [libreria sendgrid-php]: https://github.com/sendgrid/sendgrid-php/tree/v2.1.1
  [Composer]: https://getcomposer.org/download/

<!---HONumber=August15_HO7-->