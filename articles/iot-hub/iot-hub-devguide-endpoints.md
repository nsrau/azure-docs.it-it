---
title: Conoscere gli endpoint di Azure IoT Hub | Documentazione Microsoft
description: "Guida per gli sviluppatori: informazioni di riferimento sugli endpoint dell'hub IoT per dispositivi e per servizi."
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/18/2018
ms.openlocfilehash: 5015068f9b165190bef3b0cb97ddb194e173303e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61322758"
---
# <a name="reference---iot-hub-endpoints"></a>Informazioni di riferimento - Endpoint dell'hub IoT

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

## <a name="iot-hub-names"></a>Nomi dell'hub IoT

È possibile trovare il nome host dell'hub IoT che ospita gli endpoint nel portale nella pagina **Panoramica** dell'hub. Per impostazione predefinita, il nome DNS di un hub IoT è simile al seguente: `{your iot hub name}.azure-devices.net`.

## <a name="list-of-built-in-iot-hub-endpoints"></a>Elenco di endpoint dell'hub IoT incorporati

L'hub IoT di Azure è un servizio multi-tenant che espone le proprie funzionalità a diversi attori. Il diagramma seguente mostra i vari endpoint esposti dall'hub IoT.

![Endpoint hub IoT](./media/iot-hub-devguide-endpoints/endpoints.png)

L'elenco seguente offre una descrizione degli endpoint:

* **Provider di risorse**. Il provider di risorse dell'hub IoT espone un'interfaccia [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). Questa interfaccia consente ai proprietari della sottoscrizione di Azure di creare ed eliminare gli hub IoT, nonché di aggiornare le proprietà degli hub IoT. Le proprietà dell'hub IoT disciplinano i [criteri di sicurezza a livello di hub](iot-hub-devguide-security.md#access-control-and-permissions), in contrasto con il controllo di accesso a livello di dispositivo, e le opzioni funzionali per la messaggistica da cloud a dispositivo e da dispositivo a cloud. Il provider di risorse dell'hub IoT consente anche di [esportare le identità dei dispositivi](iot-hub-devguide-identity-registry.md#import-and-export-device-identities).

* **Gestione delle identità dei dispositivi**. Ogni hub IoT espone un set di endpoint REST HTTPS per gestire le identità dei dispositivi (per operazioni di creazione, recupero, aggiornamento ed eliminazione). Le [identità dei dispositivi](iot-hub-devguide-identity-registry.md) vengono usate per l'autenticazione dei dispositivi e il controllo di accesso.

* **Gestione dei dispositivi gemelli**. Ogni hub IoT espone un set di endpoint REST HTTPS orientati ai servizi per eseguire query e aggiornare [dispositivi gemelli](iot-hub-devguide-device-twins.md) (aggiornare tag e proprietà).

* **Gestione dei processi**. Ogni hub IoT espone un set di endpoint REST HTTPS orientati ai servizi per eseguire query e gestire i [processi](iot-hub-devguide-jobs.md).

* **Endpoint del dispositivo**. Per ogni dispositivo nel registro delle identità, l'hub IoT espone un set di endpoint:

  * *Invio di messaggi da dispositivo a cloud*. Un dispositivo usa questo endpoint per [inviare messaggi da dispositivo a cloud](iot-hub-devguide-messages-d2c.md).

  * *Ricezione di messaggi da cloud a dispositivo*. Il dispositivo usa questo endpoint per ricevere [messaggi da cloud a dispositivo](iot-hub-devguide-messages-c2d.md) specifici.

  * *Avvio di caricamenti di file*. Un dispositivo usa questo endpoint per ricevere un URI di firma di accesso condiviso di Archiviazione di Azure dall'hub IoT per il [caricamento di un file](iot-hub-devguide-file-upload.md).

  * *Recuperare e aggiornare le proprietà dei dispositivi gemelli*. Un dispositivo usa questo endpoint per accedere alle relative proprietà del [dispositivo gemello](iot-hub-devguide-device-twins.md).

  * *Ricezione di richieste di metodi diretti*. Un dispositivo usa questo endpoint per ascoltare le richieste di [metodi diretti](iot-hub-devguide-direct-methods.md).

    Questi endpoint vengono esposti con i protocolli [MQTT v3.1.1](https://mqtt.org/), HTTPS 1.1 e [AMQP 1.0](https://www.amqp.org/). AMQP è disponibile anche su [WebSocket](https://tools.ietf.org/html/rfc6455) sulla porta 443.

* **Endpoint di servizio**. Ogni hub IoT espone un set di endpoint per il back-end della soluzione per comunicare con i dispositivi. Con una eccezione, questi endpoint sono esposti solo tramite il protocollo [AMQP](https://www.amqp.org/). Tramite il protocollo HTTPS viene esposto l'endpoint di chiamata del metodo.
  
  * *Ricezione di messaggi da dispositivo a cloud*. Questo endpoint è compatibile con [Hub eventi di Azure](https://azure.microsoft.com/documentation/services/event-hubs/) e può essere usato da un servizio back-end per leggere i [messaggi da dispositivo a cloud](iot-hub-devguide-messages-d2c.md) inviati dai dispositivi. Oltre a questo endpoint predefinito, è possibile creare endpoint personalizzati sull'hub IoT.
  
  * *Invio di messaggi da cloud a dispositivo e ricezione di acknowledgement di recapito*. Questi endpoint consentono al back-end della soluzione di inviare [messaggi da cloud a dispositivo](iot-hub-devguide-messages-c2d.md) affidabili e di ricevere gli acknowledgment di recapito o di scadenza corrispondenti.
  
  * *Ricezione di notifiche relative ai file*. Questo endpoint di messaggistica consente di ricevere notifiche quando i dispositivi completano il caricamento di un file. 
  
  * *Chiamata diretta al metodo*. Questo endpoint consente a un servizio back-end di richiamare un [metodo diretto](iot-hub-devguide-direct-methods.md) in un dispositivo.
  
  * *Ricezione di eventi di monitoraggio delle operazioni*. Questo endpoint consente di ricevere gli eventi di monitoraggio delle operazioni, se l'hub IoT è stato configurato per generarli. Per altre informazioni, vedere [Monitoraggio delle operazioni dell'hub IoT](iot-hub-operations-monitoring.md).

L'articolo [Azure IoT SDKs](iot-hub-devguide-sdks.md) (SDK di IoT di Azure) descrive le varie modalità di accesso a questi endpoint.

Tutti gli endpoint dell'hub IoT usano il protocollo [TLS](https://tools.ietf.org/html/rfc5246) e non vengono mai esposti su canali non crittografati o non protetti.

## <a name="custom-endpoints"></a>Endpoint personalizzati

È possibile collegare i servizi di Azure esistenti nella sottoscrizione all'hub IoT che agiranno come endpoint per il routing dei messaggi. Questi endpoint agiscono come endpoint di servizio e vengono usati come sink per il ruoting dei messaggi. I dispositivi non possono scrivere direttamente sugli endpoint aggiuntivi. Informazioni su [routing dei messaggi](../iot-hub/iot-hub-devguide-messages-d2c.md).

Hub IoT supporta attualmente i servizi di Azure seguenti come endpoint aggiuntivi:

* Contenitori di Archiviazione di Azure
* Hub eventi
* Code del bus di servizio
* Argomenti del bus di servizio

Per i limiti sul numero di endpoint che è possibile aggiungere, vedere [Quotas and throttling](iot-hub-devguide-quotas-throttling.md) (Quote e limitazioni).

È possibile usare l'API REST [integrità degli Endpoint ottenere](https://docs.microsoft.com/de-de/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) per ottenere lo stato di integrità degli endpoint. È consigliabile usare la [le metriche dell'IoT Hub](iot-hub-metrics.md) relativi alla latenza di routing di messaggi per identificare e il debug degli errori quando l'integrità dell'endpoint è inattivo o non integro.

|Stato integrità|DESCRIZIONE|
|---|---|
|healthy|L'endpoint accetta i messaggi nel modo previsto.|
|Tipo non integro|L'endpoint non accetta i messaggi come previsto e nuovo tentativo dell'IoT Hub per inviare dati a questo endpoint. Lo stato di un endpoint integro verrà aggiornato in integro quando l'IoT Hub ha stabilito uno stato di integrità con coerenza finale.|
|unknown|L'IoT Hub non ha stabilito una connessione con l'endpoint. Nessun messaggio è stato recapitato o rifiutato da questo endpoint.|
|Dead|L'endpoint non accetta i messaggi, dopo che l'IoT Hub ritentare l'invio di messaggi per il periodo retrial.|

## <a name="field-gateways"></a>Gateway sul campo

In una soluzione IoT un *gateway sul campo* è posizionato tra i dispositivi e l'hub IoT e in genere si trova vicino ai dispositivi. I dispositivi comunicano direttamente con il gateway sul campo tramite un protocollo supportato dai dispositivi. Il gateway sul campo si connette a un endpoint dell'hub IoT usando un protocollo supportato dall'hub IoT. Un gateway sul campo potrebbe essere un dispositivo hardware dedicato o un computer a bassa potenza che esegue il software del gateway personalizzato.

È possibile usare [Azure IoT Edge](/azure/iot-edge/) per implementare un gateway sul campo. IoT Edge offre funzionalità come il multiplex delle comunicazioni da più dispositivi sulla stessa connessione dell'hub IoT.

## <a name="next-steps"></a>Passaggi successivi

Di seguito sono indicati altri argomenti di riferimento reperibili nella Guida per gli sviluppatori dell'hub IoT:

* [Linguaggio di query dell'hub IoT per dispositivi gemelli, processi e routing di messaggi](iot-hub-devguide-query-language.md)
* [Quote e limitazione](iot-hub-devguide-quotas-throttling.md)
* [Supporto di MQTT nell'hub IoT](iot-hub-mqtt-support.md)
