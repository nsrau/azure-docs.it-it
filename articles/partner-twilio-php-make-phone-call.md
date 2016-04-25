<properties
	pageTitle="Come effettuare una chiamata telefonica da Twilio (PHP) | Microsoft Azure"
	description="Informazioni su come effettuare una chiamata telefonica e inviare un SMS con il servizio API Twilio API in Azure. Esempi per un'applicazione PHP."
	documentationCenter="php"
	services=""
	authors="devinrader"
	manager="twilio"
	editor="mollybos"/>

<tags
	ms.service="multiple"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="PHP"
	ms.topic="article"
	ms.date="11/25/2014"
	ms.author="microsofthelp@twilio.com"/>

# Come effettuare una chiamata tramite Twilio in un'applicazione PHP in Azure

Nell'esempio seguente viene illustrato come è possibile utilizzare Twilio per effettuare una chiamata da una pagina Web PHP ospitata in Azure. L'applicazione risultante chiederà all'utente di inserire i valori relativi alla chiamata telefonica, come illustrato nella schermata seguente.

![Modulo di chiamata di Azure con Twilio e PHP][twilio_php]

Per usare il codice in questo argomento è necessario eseguire le operazioni seguenti:

1. Ottenere un account e un token di autenticazione Twilio. Per informazioni sui prezzi di Twilio, visitare la pagina [http://www.twilio.com/pricing][twilio_pricing]. Per effettuare l'iscrizione e ottenere un account di valutazione gratuito, visitare la pagina [https://www.twilio.com/try-twilio][try_twilio]. Per informazioni sull'API fornita da Twilio, vedere [http://www.twilio.com/api][twilio_api].
2. Ottenere la [libreria Twilio per PHP](https://github.com/twilio/twilio-php) o installarla come pacchetto PEAR. Per altre informazioni, vedere il [file leggimi](https://github.com/twilio/twilio-php/blob/master/README.md).
3. Installare Azure SDK per PHP. Per informazioni generali sull'SDK e istruzioni per installarlo, vedere la pagina relativa alla [configurazione di Azure SDK per PHP][setup_php_sdk].

## Creare un modulo Web per effettuare una chiamata

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

Oltre a effettuare la chiamata, **makecall.php** visualizza alcuni metadati della chiamata, come mostrato nell'esempio nella schermata seguente. Per altre informazioni sui metadati della chiamata, vedere [https://www.twilio.com/docs/api/rest/call#instance-properties][twilio_call_properties].

![Risposta a chiamata di Azure tramite Twilio e PHP][twilio_php_response]

## Eseguire l'applicazione
Il passaggio successivo consiste nel distribuire l'applicazione in Siti Web di Azure. Gli articoli seguenti contengono informazioni sulla creazione di un sito Web e sulla distribuzione del codice con Git, FTP o WebMatrix, sebbene non tutte le informazioni incluse in ogni articolo siano rilevanti:

* [Creare un sito Web di Azure PHP-MySQL ed effettuarne la distribuzione tramite Git][website-git]
* [Creare un sito Web di Azure PHP-MySQL e distribuirlo tramite FTP][website-ftp]

## Passaggi successivi
Questo codice ha lo scopo di illustrare le funzionalità di base dell'utilizzo di Twilio con PHP in Azure. Prima di eseguire la distribuzione in Azure in produzione, può essere necessario aggiungere ulteriori funzionalità per la gestione degli errori o per altri scopi. Ad esempio:

* Anziché utilizzare un modulo Web, è possibile utilizzare l'archiviazione BLOB o un database SQL di Azure per l'archiviazione di numeri di telefono e testo delle chiamate. Per informazioni sull'utilizzo dei BLOB di archiviazione di Azure in PHP, vedere [Utilizzo dell'archiviazione di Azure con applicazioni PHP][howto_blob_storage_php]. Per informazioni sull'utilizzo di database SQL in PHP, vedere [Utilizzo del database SQL di Azure con applicazioni PHP][howto_sql_azure_php].
* Il codice **makecall.php** utilizza l'URL ([http://twimlets.com/message][twimlet_message_url]) fornito da Twilio per fornire una risposta TwiML (Twilio Markup Language) che indichi a Twilio come procedere con la chiamata. Ad esempio, la risposta TwiML restituita può contenere un verbo `<Say>`, che offre una versione parlata del testo al destinatario della chiamata. Anziché utilizzare l'URL fornito da Twilio, è possibile creare un servizio personalizzato per rispondere alla richiesta di Twilio. Per altre informazioni, vedere [Come utilizzare Twilio per le funzionalità voce ed SMS in PHP][howto_twilio_voice_sms_php]. Per altre informazioni su TwiML, visitare la pagina [http://www.twilio.com/docs/api/twiml][twiml] e per altre informazioni su `<Say>` e altri verbi Twilio, visitare la pagina [http://www.twilio.com/docs/api/twiml/say][twilio_say].
* Leggere le linee guida sulla sicurezza di Twilio all'indirizzo [https://www.twilio.com/docs/security][twilio_docs_security].

Per ulteriori informazioni su Twilio, vedere [https://www.twilio.com/docs][twilio_docs].

## Vedere anche
* [Come usare Twilio per le funzionalità voce ed SMS in PHP](partner-twilio-php-how-to-use-voice-sms.md)

[twilio_pricing]: http://www.twilio.com/pricing
[try_twilio]: http://www.twilio.com/try-twilio
[twilio_api]: http://www.twilio.com/api
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[setup_php_sdk]: http://azurephp.interoperabilitybridges.com/articles/setup-the-windows-azure-sdk-for-php
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
[website-git]: ./web-sites/web-sites-php-mysql-deploy-use-git.md
[website-ftp]: ./web-sites/web-sites-php-mysql-deploy-use-ftp.md
[twilio_php_github]: https://github.com/twilio/twilio-php

<!---HONumber=AcomDC_0413_2016-->