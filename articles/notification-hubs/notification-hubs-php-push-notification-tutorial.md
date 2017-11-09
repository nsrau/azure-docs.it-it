---
title: Come usare Hub di notifica con PHP
description: Informazioni su come usare Hub di notifica di Azure da un back-end PHP.
services: notification-hubs
documentationcenter: 
author: ysxu
manager: erikre
editor: 
ms.assetid: 0156f994-96d0-4878-b07b-49b7be4fd856
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: php
ms.devlang: php
ms.topic: article
ms.date: 06/07/2016
ms.author: yuaxu
ms.openlocfilehash: c27b6308ff528224a0398e0ff40537db05417bb0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-notification-hubs-from-php"></a>Come usare Hub di notifica da PHP
[!INCLUDE [notification-hubs-backend-how-to-selector](../../includes/notification-hubs-backend-how-to-selector.md)]

Per accedere a tutte le funzionalità di Hub di notifica da un back-end Java/PHP/Ruby, è possibile usare l'interfaccia REST di Hub di notifica come descritto nell'argomento [API REST degli hub di notifica](http://msdn.microsoft.com/library/dn223264.aspx)di MSDN.

In questo argomento viene illustrato come:

* Compilare un client REST per le funzionalità di Hub di notifica in PHP.
* Completare l' [esercitazione introduttiva](notification-hubs-ios-apple-push-notification-apns-get-started.md) per la piattaforma mobile preferita, implementando la parte del backend in PHP.

## <a name="client-interface"></a>Interfaccia del client
L'interfaccia del client principale può fornire gli stessi metodi disponibili nell'[SDK di Hub di notifica per .NET](http://msdn.microsoft.com/library/jj933431.aspx). In questo modo sarà possibile tradurre direttamente tutte le esercitazioni e gli esempi disponibili in questo sito oppure messi a disposizione dalla community in Internet.

Tutto il codice disponibile è incluso nell' [esempio di wrapper REST PHP].

Ad esempio, per creare un client:

    $hub = new NotificationHub("connection string", "hubname");    

Per inviare una notifica nativa iOS:

    $notification = new Notification("apple", '{"aps":{"alert": "Hello!"}}');
    $hub->sendNotification($notification, null);

## <a name="implementation"></a>Implementazione
Se non è già stato fatto, seguire l' [esercitazione Introduzione ad Hub di notifica] fino all'ultima sezione in cui è necessario implementare il back-end.
Si può anche usare il codice dell'[esempio di wrapper REST PHP] e passare direttamente alla sezione [Completare l'esercitazione](#complete-tutorial).

Tutti i dettagli per implementare un wrapper REST completo sono disponibili in [MSDN](http://msdn.microsoft.com/library/dn530746.aspx). In questa sezione viene illustrata l'implementazione PHP dei passaggi principali necessari per accedere agli endpoint REST di Hub di notifica:

1. Analizzare la stringa di connessione
2. Generare il token di autorizzazione
3. Eseguire la chiamata HTTP

### <a name="parse-the-connection-string"></a>Analizzare la stringa di connessione
Questa è la classe principale che implementa il client, il cui costruttore analizza la stringa di connessione:

    class NotificationHub {
        const API_VERSION = "?api-version=2013-10";

        private $endpoint;
        private $hubPath;
        private $sasKeyName;
        private $sasKeyValue;

        function __construct($connectionString, $hubPath) {
            $this->hubPath = $hubPath;

            $this->parseConnectionString($connectionString);
        }

        private function parseConnectionString($connectionString) {
            $parts = explode(";", $connectionString);
            if (sizeof($parts) != 3) {
                throw new Exception("Error parsing connection string: " . $connectionString);
            }

            foreach ($parts as $part) {
                if (strpos($part, "Endpoint") === 0) {
                    $this->endpoint = "https" . substr($part, 11);
                } else if (strpos($part, "SharedAccessKeyName") === 0) {
                    $this->sasKeyName = substr($part, 20);
                } else if (strpos($part, "SharedAccessKey") === 0) {
                    $this->sasKeyValue = substr($part, 16);
                }
            }
        }
    }


### <a name="create-security-token"></a>Creare il token di sicurezza
I dettagli della creazione del token di sicurezza sono disponibili [qui](http://msdn.microsoft.com/library/dn495627.aspx).
È necessario aggiungere il metodo seguente alla classe **NotificationHub** per creare il token in base dell'URI della richiesta corrente e delle credenziali estratte dalla stringa di connessione.

    private function generateSasToken($uri) {
        $targetUri = strtolower(rawurlencode(strtolower($uri)));

        $expires = time();
        $expiresInMins = 60;
        $expires = $expires + $expiresInMins * 60;
        $toSign = $targetUri . "\n" . $expires;

        $signature = rawurlencode(base64_encode(hash_hmac('sha256', $toSign, $this->sasKeyValue, TRUE)));

        $token = "SharedAccessSignature sr=" . $targetUri . "&sig="
                    . $signature . "&se=" . $expires . "&skn=" . $this->sasKeyName;

        return $token;
    }

### <a name="send-a-notification"></a>Inviare una notifica
Definire innanzitutto una classe che rappresenta una notifica.

    class Notification {
        public $format;
        public $payload;

        # array with keynames for headers
        # Note: Some headers are mandatory: Windows: X-WNS-Type, WindowsPhone: X-NotificationType
        # Note: For Apple you can set Expiry with header: ServiceBusNotification-ApnsExpiry in W3C DTF, YYYY-MM-DDThh:mmTZD (for example, 1997-07-16T19:20+01:00).
        public $headers;

        function __construct($format, $payload) {
            if (!in_array($format, ["template", "apple", "windows", "gcm", "windowsphone"])) {
                throw new Exception('Invalid format: ' . $format);
            }

            $this->format = $format;
            $this->payload = $payload;
        }
    }

Questa classe è un contenitore per un corpo di notifica nativo oppure un insieme di proprietà nel caso di una notifica modello e un insieme di intestazioni che contengono il formato (modello o piattaforma nativa) e proprietà specifiche della piattaforma (come la proprietà di scadenza e le intestazioni WNS di Apple).

Per tutte le opzioni disponibili fare riferimento alla [documentazione delle API REST di Hub di notifica](http://msdn.microsoft.com/library/dn495827.aspx) e ai formati delle piattaforme di notifica specifiche.

Una volta definita questa classe, è possibile scrivere i metodi di notifica all'interno della classe **NotificationHub** .

    public function sendNotification($notification, $tagsOrTagExpression="") {
        if (is_array($tagsOrTagExpression)) {
            $tagExpression = implode(" || ", $tagsOrTagExpression);
        } else {
            $tagExpression = $tagsOrTagExpression;
        }

        # build uri
        $uri = $this->endpoint . $this->hubPath . "/messages" . NotificationHub::API_VERSION;
        $ch = curl_init($uri);

        if (in_array($notification->format, ["template", "apple", "gcm"])) {
            $contentType = "application/json";
        } else {
            $contentType = "application/xml";
        }

        $token = $this->generateSasToken($uri);

        $headers = [
            'Authorization: '.$token,
            'Content-Type: '.$contentType,
            'ServiceBusNotification-Format: '.$notification->format
        ];

        if ("" !== $tagExpression) {
            $headers[] = 'ServiceBusNotification-Tags: '.$tagExpression;
        }

        # add headers for other platforms
        if (is_array($notification->headers)) {
            $headers = array_merge($headers, $notification->headers);
        }

        curl_setopt_array($ch, array(
            CURLOPT_POST => TRUE,
            CURLOPT_RETURNTRANSFER => TRUE,
            CURLOPT_SSL_VERIFYPEER => FALSE,
            CURLOPT_HTTPHEADER => $headers,
            CURLOPT_POSTFIELDS => $notification->payload
        ));

        // Send the request
        $response = curl_exec($ch);

        // Check for errors
        if($response === FALSE){
            throw new Exception(curl_error($ch));
        }

        $info = curl_getinfo($ch);

        if ($info['http_code'] <> 201) {
            throw new Exception('Error sending notificaiton: '. $info['http_code'] . ' msg: ' . $response);
        }
    } 

I metodi sopra indicati inviano una richiesta POST HTTP all'endpoint /messages dell'hub di notifica, contenenti il corpo e le intestazioni corrette per l'invio della notifica.

## <a name="complete-tutorial"></a>Completare l'esercitazione
È ora possibile completare l'esercitazione introduttiva inviando la notifica da un back-end PHP.

Inizializzare il client di Hub di notifica, sostituendo la stringa di connessione e il nome hub come indicato nell'esercitazione [esercitazione Introduzione ad Hub di notifica]:

    $hub = new NotificationHub("connection string", "hubname");    

Aggiungere quindi il codice di invio a seconda della piattaforma mobile di destinazione.

### <a name="windows-store-and-windows-phone-81-non-silverlight"></a>Windows Store e Windows Phone 8.1 (non Silverlight)
    $toast = '<toast><visual><binding template="ToastText01"><text id="1">Hello from PHP!</text></binding></visual></toast>';
    $notification = new Notification("windows", $toast);
    $notification->headers[] = 'X-WNS-Type: wns/toast';
    $hub->sendNotification($notification, null);

### <a name="ios"></a>iOS
    $alert = '{"aps":{"alert":"Hello from PHP!"}}';
    $notification = new Notification("apple", $alert);
    $hub->sendNotification($notification, null);

### <a name="android"></a>Android
    $message = '{"data":{"msg":"Hello from PHP!"}}';
    $notification = new Notification("gcm", $message);
    $hub->sendNotification($notification, null);

### <a name="windows-phone-80-and-81-silverlight"></a>Windows Phone 8.0 e 8.1 Silverlight
    $toast = '<?xml version="1.0" encoding="utf-8"?>' .
                '<wp:Notification xmlns:wp="WPNotification">' .
                   '<wp:Toast>' .
                        '<wp:Text1>Hello from PHP!</wp:Text1>' .
                   '</wp:Toast> ' .
                '</wp:Notification>';
    $notification = new Notification("windowsphone", $toast);
    $notification->headers[] = 'X-WindowsPhone-Target : toast';
    $notification->headers[] = 'X-NotificationClass : 2';
    $hub->sendNotification($notification, null);


### <a name="kindle-fire"></a>Kindle Fire
    $message = '{"data":{"msg":"Hello from PHP!"}}';
    $notification = new Notification("adm", $message);
    $hub->sendNotification($notification, null);

Eseguendo il codice PHP dovrebbe essere visualizzata una notifica sul dispositivo di destinazione.

## <a name="next-steps"></a>Passaggi successivi
In questo argomento è stato illustrato come creare un semplice client REST Java per Hub di notifica. A questo punto è possibile:

* Scaricare l'intero [esempio di wrapper REST PHP], che contiene tutto il codice sopra indicato.
* Visualizzare altre informazioni sulla funzionalità di aggiunta tag di Hub di notifica nell'[esercitazione per l'invio di notizie]
* Visualizzare altre informazioni sull'invio di notifiche push a singoli utenti nell'[esercitazione sull'invio di notifiche agli utenti]

Per ulteriori informazioni, vedere anche il [Centro per sviluppatori di PHP](/develop/php/).

[esempio di wrapper REST PHP]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/notificationhubs-rest-php
[esercitazione Introduzione ad Hub di notifica]: http://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/

