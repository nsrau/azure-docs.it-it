---
title: Diagnostica del proxy inverso di Azure Service Fabric | Microsoft Docs
description: Informazioni su come monitorare e diagnosticare l'elaborazione delle richieste nel proxy inverso.
services: service-fabric
documentationcenter: .net
author: kavyako
manager: vipulm
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 08/08/2017
ms.author: kavyako
ms.translationtype: HT
ms.sourcegitcommit: 0aae2acfbf30a77f57ddfbaabdb17f51b6938fd6
ms.openlocfilehash: 3bc631606afbc93d5bca94f4955fd2ef816fa9fd
ms.contentlocale: it-it
ms.lasthandoff: 08/09/2017

---
# <a name="monitor-and-diagnose-request-processing-at-the-reverse-proxy"></a>Monitorare e diagnosticare l'elaborazione della richiesta nel proxy inverso

A partire da Service Fabric versione 5.7 è possibile raccogliere gli eventi del proxy inverso. Gli eventi sono disponibili in due canali: un canale contenente solo gli eventi con esito negativo dell'elaborazione della richiesta nel proxy inverso e il secondo canale contenente gli eventi dettagliati con le voci relative alle richieste con esito positivo e negativo.

Leggere [Collect reverse proxy events](service-fabric-diagnostics-event-aggregation-wad.md#collect-reverse-proxy-events) (Raccogliere gli eventi del proxy inverso) per abilitare la raccolta di eventi da questi canali in locale e nei cluster di Azure Service Fabric.

## <a name="troubleshoot-using-diagnostics-logs"></a>Eseguire la risoluzione dei problemi usando i log di diagnostica
Di seguito sono riportati alcuni esempi su come interpretare i log di potenziali errori comuni:

1. Il proxy inverso restituisce il codice di stato della risposta 504 (timeout).

    Una possibile causa è la mancata risposta del servizio entro il periodo di timeout della richiesta.
Il primo evento di seguito registra i dettagli della richiesta ricevuti nel proxy inverso. Il secondo evento indica che la richiesta ha avuto esito negativo in fase di inoltro al servizio a causa dell'errore: "internal error = ERROR_WINHTTP_TIMEOUT" 

    Il payload include:

    *  **traceId**: questo GUID può essere usato per correlare tutti gli eventi corrispondenti a una singola richiesta. Nei due eventi di seguito, il traceId = **2f87b722-e254-4ac2-a802-fd315c1a0271** implica l'appartenenza alla stessa richiesta.
    *  **requestUrl**: l'URL (URL del proxy inverso) a cui è stata inviata la richiesta.
    *  **verb**: il verbo HTTP.
    *  **remoteAddress**: l'indirizzo del client che ha inviato la richiesta.
    *  **resolvedServiceUrl**: l'URL dell'endpoint di servizio con la risoluzione della richiesta in ingresso. 
    *  **errorDetails**: informazioni aggiuntive sull'errore.

    ```
    {
      "Timestamp": "2017-07-20T15:57:59.9871163-07:00",
      "ProviderName": "Microsoft-ServiceFabric",
      "Id": 51477,
      "Message": "2f87b722-e254-4ac2-a802-fd315c1a0271 Request url = https://localhost:19081/LocationApp/LocationFEService?zipcode=98052, verb = GET, remote (client) address = ::1, resolved service url = Https://localhost:8491/LocationApp/?zipcode=98052, request processing start time =     15:58:00.074114 (745,608.196 MSec) ",
      "ProcessId": 57696,
      "Level": "Informational",
      "Keywords": "0x1000000000000021",
      "EventName": "ReverseProxy",
      "ActivityID": null,
      "RelatedActivityID": null,
      "Payload": {
        "traceId": "2f87b722-e254-4ac2-a802-fd315c1a0271",
        "requestUrl": "https://localhost:19081/LocationApp/LocationFEService?zipcode=98052",
        "verb": "GET",
        "remoteAddress": "::1",
        "resolvedServiceUrl": "Https://localhost:8491/LocationApp/?zipcode=98052",
        "requestStartTime": "2017-07-20T15:58:00.0741142-07:00"
      }
    }

    {
      "Timestamp": "2017-07-20T16:00:01.3173605-07:00",
      ...
      "Message": "2f87b722-e254-4ac2-a802-fd315c1a0271 Error while forwarding request to service: response status code = 504, description = Reverse proxy Timeout, phase = FinishSendRequest, internal error = ERROR_WINHTTP_TIMEOUT ",
      ...
      "Payload": {
        "traceId": "2f87b722-e254-4ac2-a802-fd315c1a0271",
        "statusCode": 504,
        "description": "Reverse Proxy Timeout",
        "sendRequestPhase": "FinishSendRequest",
        "errorDetails": "internal error = ERROR_WINHTTP_TIMEOUT"
      }
    }
    ```

2. Il proxy inverso restituisce il codice di stato della risposta 404 - Non trovato. 
    
    Di seguito è riportato un evento di esempio in cui il proxy inverso restituisce il codice 404 poiché non è riuscito a trovare l'endpoint di servizio corrispondente.
    Le voci del payload di interesse sono:
    *  **processRequestPhase**: indica la fase di elaborazione della richiesta in cui si è verificato l'errore ***TryGetEndpoint***, ad esempio durante il tentativo di recupero dell'endpoint di servizio a cui eseguire l'inoltro. 
    *  **errorDetails**: elenca i criteri di ricerca dell'endpoint. Esaminare qui che il listenerName specificato = **FrontEndListener**, mentre l'elenco di endpoint di replica contiene solo un listener con il nome **OldListener**.
    
    ```
    {
      ...
      "Message": "c1cca3b7-f85d-4fef-a162-88af23604343 Error while processing request, cannot forward to service: request url = https://localhost:19081/LocationApp/LocationFEService?ListenerName=FrontEndListener&zipcode=98052, verb = GET, remote (client) address = ::1, request processing start time = 16:43:02.686271 (3,448,220.353 MSec), error = FABRIC_E_ENDPOINT_NOT_FOUND, message = , phase = TryGetEndoint, SecureOnlyMode = false, gateway protocol = https, listenerName = FrontEndListener, replica endpoint = {\"Endpoints\":{\"\":\"Https:\/\/localhost:8491\/LocationApp\/\"}} ",
      "ProcessId": 57696,
      "Level": "Warning",
      "EventName": "ReverseProxy",
      "Payload": {
        "traceId": "c1cca3b7-f85d-4fef-a162-88af23604343",
        "requestUrl": "https://localhost:19081/LocationApp/LocationFEService?ListenerName=NewListener&zipcode=98052",
        ...
        "processRequestPhase": "TryGetEndoint",
        "errorDetails": "SecureOnlyMode = false, gateway protocol = https, listenerName = FrontEndListener, replica endpoint = {\"Endpoints\":{\"OldListener\":\"Https:\/\/localhost:8491\/LocationApp\/\"}}"
      }
    }
    ```
    Un altro esempio in cui il proxy inverso può restituire l'errore 404 - Non trovato è quando il parametro di configurazione ApplicationGateway\Http **SecureOnlyMode** è impostato su true con il proxy inverso in ascolto su **HTTPS**, ma tutti gli endpoint di replica risultano non sicuri (in ascolto su HTTP).
    Il server proxy restituisce il codice di stato 404 poiché non è possibile trovare un endpoint in ascolto su HTTPS per inoltrare la richiesta. L'analisi dei parametri nel payload dell'evento consente di circoscrivere il problema:
    
    ```
        "errorDetails": "SecureOnlyMode = true, gateway protocol = https, listenerName = NewListener, replica endpoint = {\"Endpoints\":{\"OldListener\":\"Http:\/\/localhost:8491\/LocationApp\/\", \"NewListener\":\"Http:\/\/localhost:8492\/LocationApp\/\"}}"
    ```

3. Viene generato un errore di timeout della richiesta al proxy inverso. 
    I registri eventi contengono un evento con i dettagli della richiesta ricevuta (non indicati qui).
    L'evento successivo mostra che il servizio ha restituito un codice di stato 404 e il proxy inverso ha avviato nuovamente una risoluzione. 

    ```
    {
      ...
      "Message": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e Request to service returned: status code = 404, status description = , Reresolving ",
      "Payload": {
        "traceId": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e",
        "statusCode": 404,
        "statusDescription": ""
      }
    }
    {
      ...
      "Message": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e Re-resolved service url = Https://localhost:8491/LocationApp/?zipcode=98052 ",
      "Payload": {
        "traceId": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e",
        "requestUrl": "Https://localhost:8491/LocationApp/?zipcode=98052"
      }
    }
    ```
    Se si raccolgono tutti gli eventi, viene visualizzato un flusso di eventi che mostra ogni azione di risoluzione e tentativo di inoltro.
    L'ultimo evento della serie mostra l'esito negativo dell'elaborazione della richiesta con un timeout, oltre al numero di tentativi di risoluzione con esito positivo.
    
    > [!NOTE]
    > È consigliabile mantenere disabilitata la raccolta dettagliata di eventi del canale per impostazione predefinita e abilitarla per la risoluzione di problemi in base alle necessità.

    ```
    {
      ...
      "Message": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e Error while processing request: number of successful resolve attempts = 12, error = FABRIC_E_TIMEOUT, message = , phase = ResolveServicePartition,  ",
      "EventName": "ReverseProxy",
      ...
      "Payload": {
        "traceId": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e",
        "resolveCount": 12,
        "errorval": -2147017729,
        "errorMessage": "",
        "processRequestPhase": "ResolveServicePartition",
        "errorDetails": ""
      }
    }
    ```
    
    Se la raccolta è abilitata solo per gli eventi critici/di errore, viene visualizzato un evento con i dettagli del timeout e il numero di tentativi di risoluzione. 
    
    Se il servizio tenta di inviare un codice di stato 404 all'utente, deve essere aggiunta un'intestazione "X-ServiceFabric". Dopo la risoluzione di questo errore, il proxy inverso inoltra il codice di stato di nuovo al client.  

4. Casi in cui il client ha disconnesso la richiesta.

    Il seguente evento viene registrato quando il proxy inverso inoltra la risposta al client, ma il client esegue la disconnessione:

    ```
    {
      ...
      "Message": "6e2571a3-14a8-4fc7-93bb-c202c23b50b8 Unable to send response to client: phase = SendResponseHeaders, error = -805306367, internal error = ERROR_SUCCESS ",
      "ProcessId": 57696,
      "Level": "Warning",
      ...
      "EventName": "ReverseProxy",
      "Payload": {
        "traceId": "6e2571a3-14a8-4fc7-93bb-c202c23b50b8",
        "sendResponsePhase": "SendResponseHeaders",
        "errorval": -805306367,
        "winHttpError": "ERROR_SUCCESS"
      }
    }
    ```

> [!NOTE]
> Attualmente gli eventi correlati all'elaborazione della richiesta websocket non vengono registrati. Questa opzione verrà aggiunta nella versione successiva.

## <a name="next-steps"></a>Passaggi successivi
* [Aggregazione e raccolta di eventi usando Diagnostica di Microsoft Azure](service-fabric-diagnostics-event-aggregation-wad.md) per abilitare la raccolta di log nei cluster di Azure.
* Per visualizzare gli eventi di Service Fabric in Visual Studio, vedere [Monitorare e diagnosticare in locale](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md).
* Fare riferimento a [Configure reverse proxy to connect to secure services](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/ReverseProxySecureSample#configure-reverse-proxy-to-connect-to-secure-services) (Configurare il proxy inverso per la connessione ai servizi protetti) per il modello di Azure Resource Manager per configurare il proxy inverso protetto con le diverse opzioni di convalida del certificato del servizio .
* Per altre informazioni, leggere [Proxy inverso di Service Fabric](service-fabric-reverseproxy.md).

