---
title: Conoscere gli endpoint di Azure IoT Hub | Documentazione Microsoft
description: 'Guida per gli sviluppatori: informazioni di riferimento sugli endpoint dell''hub IoT per dispositivi e per servizi.'
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 57ba52ae-19c6-43e4-bc6c-d8a5c2476e95
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/16/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: 7948c99b7b60d77a927743c7869d74147634ddbf
ms.openlocfilehash: 2b1e8d35a71609092875f0b20b49876bd43d95aa
ms.contentlocale: it-it
ms.lasthandoff: 06/20/2017


---
# <a name="reference---iot-hub-endpoints"></a>Informazioni di riferimento - Endpoint dell'hub IoT
## <a name="list-of-built-in-iot-hub-endpoints"></a>Elenco di endpoint dell'hub IoT incorporati
L'hub IoT di Azure è un servizio multi-tenant che espone le proprie funzionalità a diversi attori. Il diagramma seguente mostra i vari endpoint esposti dall'hub IoT.

![Endpoint hub IoT][img-endpoints]

L'elenco seguente offre una descrizione degli endpoint:

* **Provider di risorse**. Il provider di risorse dell'hub IoT espone un'interfaccia di [Azure Resource Manager][lnk-arm] che consente ai proprietari di sottoscrizioni di Azure di creare ed eliminare hub IoT e di aggiornarne le proprietà. Le proprietà dell'hub IoT disciplinano i [criteri di sicurezza a livello di hub][lnk-accesscontrol], in contrasto con il controllo di accesso a livello di dispositivo, e le opzioni funzionali per la messaggistica da cloud a dispositivo e da dispositivo a cloud. Il provider di risorse dell'hub IoT consente anche di [esportare le identità dei dispositivi][lnk-importexport].
* **Gestione delle identità dei dispositivi**. Ogni hub IoT espone un set di endpoint REST HTTP per gestire le identità dei dispositivi (per operazioni di creazione, recupero, aggiornamento ed eliminazione). Le [identità dei dispositivi][lnk-device-identities] vengono usate per l'autenticazione dei dispositivi e il controllo di accesso.
* **Gestione dei dispositivi gemelli**. Ogni hub IoT espone un set di endpoint HTTP REST orientati ai servizi per eseguire query e aggiornare [dispositivi gemelli][lnk-twins] (aggiornare tag e proprietà).
* **Gestione dei processi**. Ogni hub IoT espone un set di endpoint HTTP REST orientati ai servizi per eseguire query e gestire i [processi][lnk-jobs].
* **Endpoint del dispositivo**. Per ogni dispositivo di cui è stato effettuato il provisioning nel registro delle identità, l'hub IoT espone un set di endpoint che il dispositivo può usare per inviare e ricevere messaggi:
  
  * *Invio di messaggi da dispositivo a cloud*. Usare questo endpoint per [inviare messaggi da dispositivo a cloud][lnk-d2c].
  * *Ricezione di messaggi da cloud a dispositivo*. Il dispositivo usa questo endpoint per ricevere [messaggi da cloud a dispositivo][lnk-c2d] specifici.
  * *Avvio di caricamenti di file*. Il dispositivo usa questo endpoint per ricevere un URI di firma di accesso condiviso di Archiviazione di Azure dall'hub IoT per il [caricamento di un file][lnk-upload].
  * *Recuperare e aggiornare le proprietà dei dispositivi gemelli*. Un dispositivo usa questo endpoint per accedere alle relative proprietà del [dispositivo gemello][lnk-twins].
  * *Ricezione di richieste di metodi diretti*. Un dispositivo usa questo endpoint per ascoltare le richieste di [metodi diretti][lnk-methods].
    
    Questi endpoint vengono esposti con i protocolli [MQTT v3.1.1][lnk-mqtt], HTTP 1.1 e [AMQP 1.0][lnk-amqp]. AMQP è disponibile anche su [WebSocket][lnk-websockets] sulla porta 443.
    
    Gli endpoint di dispositivi gemelli e di metodi sono disponibili solo usando [MQTT v3.1.1][lnk-mqtt].
* **Endpoint di servizio**. Ogni hub IoT espone un set di endpoint che il back-end della soluzione può usare per comunicare con i dispositivi. Questi endpoint sono attualmente esposti solo tramite il protocollo [AMQP][lnk-amqp], tranne l'endpoint di chiamata al metodo che viene esposto tramite HTTP 1.1.
  
  * *Ricezione di messaggi da dispositivo a cloud*. Questo endpoint è compatibile con [Hub eventi di Azure][lnk-event-hubs] e può essere usato da un servizio back-end per leggere i [messaggi da dispositivo a cloud][lnk-d2c] inviati dai dispositivi. Oltre a questo endpoint predefinito, è possibile creare endpoint personalizzati sull'hub IoT.
  * *Invio di messaggi da cloud a dispositivo e ricezione di acknowledgement di recapito*. Questi endpoint consentono al back-end della soluzione di inviare [messaggi da cloud a dispositivo][lnk-c2d] affidabili e di ricevere gli acknowledgment di recapito o di scadenza corrispondenti.
  * *Ricezione di notifiche relative ai file*. Questo endpoint di messaggistica consente di ricevere notifiche quando i dispositivi completano il caricamento di un file. 
  * *Chiamata diretta al metodo*. Questo endpoint consente a un servizio back-end di richiamare un [metodo diretto][lnk-methods] in un dispositivo.
  * *Ricezione di eventi di monitoraggio delle operazioni*. Questo endpoint consente di ricevere gli eventi di monitoraggio delle operazioni, se l'hub IoT è stato configurato per generarli. Per altre informazioni, vedere [Monitoraggio delle operazioni dell'hub IoT][lnk-operations-mon].

L'articolo [Azure IoT SDKs][lnk-sdks] (SDK di IoT di Azure) descrive le varie modalità di accesso a questi endpoint.

È infine importante notare che tutti gli endpoint dell'hub IoT usano il protocollo [TLS][lnk-tls] e non vengono mai esposti su canali non crittografati o non protetti.

## <a name="custom-endpoints"></a>Endpoint personalizzati
È possibile collegare i servizi di Azure esistenti nella sottoscrizione all'hub IoT che agiranno come endpoint per il routing dei messaggi. Questi endpoint agiscono come endpoint di servizio e vengono usati come sink per il ruoting dei messaggi. I dispositivi non possono scrivere direttamente sugli endpoint aggiuntivi. Per ulteriori informazioni sul routing dei messaggi, vedere la voce della guida per gli sviluppatori relativa a [invio e ricezione di messaggi con hub IoT][lnk-devguide-messaging].

Hub IoT supporta attualmente i servizi di Azure seguenti come endpoint aggiuntivi:

* Hub eventi
* Code del bus di servizio
* Argomenti del bus di servizio

Hub IoT richiede l'accesso in scrittura a questi endpoint di servizio affinché il routing dei messaggi funzioni correttamente. Se si configurano gli endpoint tramite il Portale di Azure, verranno aggiunte le autorizzazioni necessarie. Accertarsi di configurare i servizi per supportare la velocità effettiva prevista. Potrebbe essere necessario monitorare gli endpoint aggiuntivi durante la prima configurazione della soluzione IoT e quindi apportare le modifiche necessarie per il carico effettivo.

Se un messaggio corrisponde a più percorsi che puntano allo stesso endpoint, hub IoT invia il messaggio a questo endpoint una sola volta. Pertanto, non è necessario configurare la deduplicazione nella coda o nell'argomento del bus di servizio. Nelle code partizionate, l'affinità della partizione garantisce l'ordinamento dei messaggi.

> [!NOTE]
> Nelle code e negli argomenti del bus di servizio usati come endpoint dell'hub IoT non devono essere abilitati le **sessioni** e il **rilevamento duplicati**. Se una di queste opzioni è abilitata, l'endpoint risulta **non raggiungibile** nel portale di Azure.

Per i limiti sul numero di endpoint che è possibile aggiungere, vedere [Quotas and throttling][lnk-devguide-quotas] (Quote e limitazioni).

## <a name="field-gateways"></a>Gateway sul campo
In una soluzione IoT un *gateway sul campo* è posizionato tra i dispositivi e l'hub IoT e in genere si trova vicino ai dispositivi. I dispositivi comunicano direttamente con il gateway sul campo tramite un protocollo supportato dai dispositivi. Il gateway sul campo si connette a un endpoint dell'hub IoT usando un protocollo supportato dall'hub IoT. Un gateway sul campo può essere un dispositivo hardware altamente specializzato o un computer a basso consumo in cui viene eseguito il software che esegue lo scenario end-to-end a cui è destinato il gateway.

È possibile usare [Azure IoT Edge][lnk-iot-edge] per implementare un gateway sul campo. IoT Edge offre funzionalità specifiche come la possibilità di eseguire il multiplex delle comunicazioni da più dispositivi sulla stessa connessione all'hub IoT.

## <a name="next-steps"></a>Passaggi successivi
Di seguito sono indicati altri argomenti di riferimento reperibili nella Guida per gli sviluppatori dell'hub IoT:

* [Linguaggio di query dell'hub IoT per dispositivi gemelli, processi e routing di messaggi][lnk-devguide-query]
* [Quote e limitazioni][lnk-devguide-quotas]
* [Supporto di MQTT nell'hub IoT][lnk-devguide-mqtt]

[lnk-iot-edge]: https://github.com/Azure/iot-edge

[img-endpoints]: ./media/iot-hub-devguide-endpoints/endpoints.png
[lnk-amqp]: https://www.amqp.org/
[lnk-mqtt]: http://mqtt.org/
[lnk-websockets]: https://tools.ietf.org/html/rfc6455
[lnk-arm]: ../azure-resource-manager/resource-group-overview.md
[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/

[lnk-tls]: https://tools.ietf.org/html/rfc5246


[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-accesscontrol]: iot-hub-devguide-security.md#access-control-and-permissions
[lnk-importexport]: iot-hub-devguide-identity-registry.md#import-and-export-device-identities
[lnk-d2c]: iot-hub-devguide-messages-d2c.md
[lnk-device-identities]: iot-hub-devguide-identity-registry.md
[lnk-upload]: iot-hub-devguide-file-upload.md
[lnk-c2d]: iot-hub-devguide-messages-c2d.md
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-jobs]: iot-hub-devguide-jobs.md

[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-devguide-messaging]: iot-hub-devguide-messaging.md
[lnk-operations-mon]: iot-hub-operations-monitoring.md

