---
title: Come effettuare una chiamata telefonica da Twilio (PHP) | Microsoft Docs
description: Informazioni su come effettuare una chiamata telefonica e inviare un SMS con il servizio API Twilio API in Azure. Esempi per un'applicazione PHP.
documentationcenter: php
services: 
author: devinrader
manager: twilio
editor: mollybos
ms.assetid: 44e35adc-be06-4700-beee-8c9e2c20c540
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 11/25/2014
ms.author: microsofthelp@twilio.com
ms.openlocfilehash: 9866a196b3be10548d7a431430e570b41c190fc0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-make-a-phone-call-using-twilio-in-a-php-application-on-azure"></a>Come effettuare una chiamata tramite Twilio in un'applicazione PHP in Azure
Nell'esempio seguente viene illustrato come è possibile utilizzare Twilio per effettuare una chiamata da una pagina Web PHP ospitata in Azure. L'applicazione risultante chiederà all'utente di inserire i valori relativi alla chiamata telefonica, come illustrato nella schermata seguente.

![Modulo di chiamata di Azure con Twilio e PHP][twilio_php]

Per usare il codice in questo argomento è necessario eseguire le operazioni seguenti:

1. Ottenere un account Twilio e un token di autenticazione dalla [console di Twilio][twilio_console]. Per informazioni sui prezzi di Twilio, vedere la pagina [http://www.twilio.com/pricing][twilio_pricing]. Per effettuare l'iscrizione e ottenere un account di valutazione gratuito, vedere la pagina [https://www.twilio.com/try-twilio][try_twilio].
2. Ottenere la [libreria Twilio per PHP](https://github.com/twilio/twilio-php) o installarla come pacchetto PEAR. Per altre informazioni, vedere il [file leggimi](https://github.com/twilio/twilio-php/blob/master/README.md).
3. Installare Azure SDK per PHP. 
<!-- For an overview of the SDK and instructions on installing it, see [Set up the Azure SDK for PHP](app-service-web/web-sites-php-mysql-deploy-use-git.md) -->

## <a name="create-a-web-form-for-making-a-call"></a>Creare un modulo Web per effettuare una chiamata
Il codice HTML seguente mostra come creare una pagina Web (**callform.html**) che consente di recuperare i dati utente per l'effettuazione di una chiamata:

```html
<!DOCTYPE html>
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
        <td><input name="callTo" size="50" type="text" value=""></td>
      </tr>
      <tr>
        <td>From:</td>
        <td><input name="callFrom" size="50" type="text" value=""></td>
      </tr>
      <tr>
        <td>Call message:</td>
        <td><input name="callText" size="100" type="text" value="Hello. This is the call text. Good bye."></td>
      </tr>
      <tr>
        <td colspan="2"><input type="submit" value="Make this call"></td>
      </tr>
    </table>
  </form><br>
</body>
</html>
```

## <a name="create-the-code-to-make-the-call"></a>Creare il codice per l'esecuzione della chiamata
Il codice seguente illustra come compilare **makecall.php**, una pagina Web chiamata quando l'utente invia il modulo visualizzato da **callform.html**. Il codice seguente crea il messaggio di chiamata e genera la chiamata. Accertarsi inoltre di usare l'account e il token di autenticazione Twilio ottenuti dalla [console di Twilio][twilio_console] anziché i valori segnaposto assegnati a **$sid** e **$token** nel codice seguente.

```html
<html>
<head><title>Making call...</title></head>
<body>
<p>Your call is being made.</p>

<?php
require_once 'path/to/vendor/autoload.php';

$sid   = "your_account_sid";
$token = "your_authentication_token";

$from_number = $_POST['callFrom']; // Calls must be made from a registered Twilio number.
$to_number   = $_POST['callTo'];
$message     = $_POST['callText'];

$client = new Twilio\Rest\Client($sid, $token);

$call = $client->calls->create(
            $to_number,
            $from_number,
            array('url' => http://twimlets.com/message?Message=' . urlencode($message))
        );

echo "Call status: " . $call->status . "<br />";
echo "URI resource: " . $call->uri . "<br />";
?>
</body>
</html>
```

Oltre a eseguire la chiamata, **makecall.php** visualizza alcuni metadati della chiamata, come mostrato nell'immagine seguente. Per altre informazioni sui metadati della chiamata, vedere [https://www.twilio.com/docs/api/rest/call#instance-properties][twilio_call_properties].

![Risposta a chiamata di Azure tramite Twilio e PHP][twilio_php_response]

## <a name="run-the-application"></a>Eseguire l'applicazione
Il passaggio successivo consiste nel [distribuire l'applicazione in App Web di Azure con Git](app-service/app-service-web-get-started-php.md), ma non tutte le informazioni contenute in questa pagina sono rilevanti. 

## <a name="next-steps"></a>Passaggi successivi
Questo codice ha lo scopo di illustrare le funzionalità di base dell'utilizzo di Twilio con PHP in Azure. Prima di eseguire la distribuzione in Azure in produzione, può essere necessario aggiungere ulteriori funzionalità per la gestione degli errori o per altri scopi. Ad esempio:

* Anziché utilizzare un modulo Web, è possibile utilizzare l'archiviazione BLOB o un database SQL di Azure per l'archiviazione di numeri di telefono e testo delle chiamate. Per informazioni sull'uso dei BLOB di archiviazione di Azure in PHP, vedere [Come usare l'archiviazione BLOB da PHP][howto_blob_storage_php]. Per informazioni sull'uso di database SQL in PHP, vedere [Raccolte di connessioni per database SQL e Server SQL][howto_sql_azure_php].
* Il codice **makecall.php** usa l'URL fornito da Twilio ([http://twimlets.com/message][twimlet_message_url]) per fornire una risposta TwiML (Twilio Markup Language) che indichi a Twilio come procedere con la chiamata. Ad esempio, la risposta TwiML restituita può contenere un verbo `<Say>`, che offre una versione parlata del testo al destinatario della chiamata. Anziché usare l'URL fornito da Twilio, è possibile creare un servizio personalizzato per rispondere alla richiesta di Twilio. Per altre informazioni, vedere [Come usare Twilio per le funzionalità voce ed SMS in PHP][howto_twilio_voice_sms_php]. Per altre informazioni su TwiML, vedere la pagina [http://www.twilio.com/docs/api/twiml][twiml] e per altre informazioni su `<Say>` e altri verbi Twilio, vedere la pagina [http://www.twilio.com/docs/api/twiml/say][twilio_say].
* Leggere le linee guida sulla sicurezza di Twilio all'indirizzo [https://www.twilio.com/docs/security][twilio_docs_security].

Per altre informazioni su Twilio, vedere [https://www.twilio.com/docs][twilio_docs].

## <a name="see-also"></a>Vedere anche
* [Come usare Twilio per le funzionalità voce ed SMS in PHP](partner-twilio-php-how-to-use-voice-sms.md)

[twilio_console]: https://www.twilio.com/console
[twilio_pricing]: http://www.twilio.com/pricing
[try_twilio]: http://www.twilio.com/try-twilio
[twilio_api]: http://www.twilio.com/docs/api
[verify_phone]: https://www.twilio.com/console/phone-numbers/verified
[twimlet_message_url]: http://twimlets.com/message
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api_service]: http://api.twilio.com
[build_php_azure_app]: http://azurephp.interoperabilitybridges.com/articles/build-and-deploy-a-windows-azure-php-application
[howto_twilio_voice_sms_php]: partner-twilio-php-how-to-use-voice-sms.md
[howto_blob_storage_php]: http://azure.microsoft.com/documentation/articles/storage-php-how-to-use-blobs/
[howto_sql_azure_php]: http://azure.microsoft.com/documentation/articles/sql-database-php-how-to-use/
[twilio_call_properties]: https://www.twilio.com/docs/api/rest/call#instance-properties
[twilio_docs_security]: http://www.twilio.com/docs/security
[twilio_docs]: http://www.twilio.com/docs
[twilio_say]: http://www.twilio.com/docs/api/twiml/say
[ssl_validation]: http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html
[twilio_php]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPCallForm.jpg
[twilio_php_response]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPMakeCall.jpg
[twilio_php_github]: https://github.com/twilio/twilio-php
