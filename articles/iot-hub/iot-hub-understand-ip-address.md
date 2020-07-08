---
title: Informazioni sull'indirizzo IP dell'hub IoT | Microsoft Docs
description: Informazioni su come eseguire una query sull'indirizzo IP dell'hub IoT e sulle relative proprietà. È possibile che l'indirizzo IP dell'hub IoT venga modificato in determinati scenari, ad esempio nel ripristino di emergenza o nel failover a livello di area.
author: philmea
ms.author: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: 1bfae8c7afbfdc6e73dd8bb17b94e6543361e9ce
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.contentlocale: it-IT
ms.lasthandoff: 05/26/2020
ms.locfileid: "83848229"
---
# <a name="iot-hub-ip-addresses"></a>Indirizzi IP dell'hub IoT

I prefissi degli indirizzi IP degli endpoint pubblici dell'hub IoT vengono pubblicati periodicamente nel [tag di servizio](../virtual-network/service-tags-overview.md) di _AzureIoTHub_.

> [!NOTE]
> Per i dispositivi distribuiti all'interno di reti locali, l'hub IoT di Azure supporta l'integrazione di connettività VNET con endpoint privati. Per altre informazioni, vedere [Supporto dell'hub IoT per VNet](./virtual-network-support.md).


È possibile usare i prefissi degli indirizzi IP per controllare la connettività tra l'hub IoT e i dispositivi o gli asset di rete per implementare una gamma di obiettivi di isolamento rete:

| Obiettivo | Scenari applicabili | Approccio |
|------|-----------|----------|
| Assicurarsi che i dispositivi e i servizi comunichino solo con gli endpoint dell'hub IoT | Messaggistica [da dispositivo a cloud](./iot-hub-devguide-messaging.md) e [da cloud a dispositivo](./iot-hub-devguide-messages-c2d.md), [metodi diretti](./iot-hub-devguide-direct-methods.md), [dispositivo e moduli gemelli](./iot-hub-devguide-device-twins.md) e [flussi di dispositivi](./iot-hub-device-streams-overview.md) | Usare i tag di servizio _AzureIoTHub_ e _EventHub_ per individuare l'hub IoT e i prefissi degli indirizzi IP dell'hub eventi e configurare le regole ALLOW nell'impostazione del firewall di dispositivi e servizi per i prefissi degli indirizzi IP. Eliminare il traffico verso altri indirizzi IP di destinazione con cui interrompere la comunicazione dei dispositivi o dei servizi. |
| Assicurarsi che l'endpoint del dispositivo dell'hub IoT riceva le connessioni solo dai dispositivi e dalle risorse di rete | Messaggistica [da dispositivo a cloud](./iot-hub-devguide-messaging.md) e [da cloud a dispositivo](./iot-hub-devguide-messages-c2d.md), [metodi diretti](./iot-hub-devguide-direct-methods.md), [dispositivo e moduli gemelli](./iot-hub-devguide-device-twins.md) e [flussi di dispositivi](./iot-hub-device-streams-overview.md) | Usare la [funzionalità di filtro IP](iot-hub-ip-filtering.md) dell'hub IoT per consentire le connessioni dai dispositivi e dagli indirizzi IP delle risorse di rete (vedere la sezione [Limitazioni](#limitations-and-workarounds)). | 
| Assicurarsi che le risorse dell'endpoint personalizzato delle route (account di archiviazione, bus di servizio e hub eventi) siano raggiungibili solo dalle risorse di rete | [Routing dei messaggi](./iot-hub-devguide-messages-d2c.md) | Seguire le linee guida della risorsa sulla limitazione della connettività (ad esempio tramite [regole del firewall](../storage/common/storage-network-security.md), [collegamenti privati](../private-link/private-endpoint-overview.md) o [endpoint di servizio](../virtual-network/virtual-network-service-endpoints-overview.md)); usare i tag di servizio _AzureIoTHub_ per individuare i prefissi degli indirizzi IP dell'hub IoT e aggiungere le regole ALLOW per i prefissi IP nella configurazione del firewall della risorsa (vedere la sezione [Limitazioni](#limitations-and-workarounds)). |



## <a name="best-practices"></a>Procedure consigliate

* Quando si aggiungono le regole ALLOW nella configurazione del firewall dei dispositivi, è preferibile impostare [porte specifiche usate dai protocolli applicabili](./iot-hub-devguide-protocols.md#port-numbers).

* I prefissi degli indirizzi IP dell'hub IoT sono soggetti a modifiche. Queste modifiche vengono pubblicate periodicamente tramite i tag di servizio prima di diventare effettive. Per questa ragione è importante sviluppare processi per recuperare e usare regolarmente i tag di servizio più recenti. Questo processo può essere automatizzato tramite l'[API di individuazione dei tag di servizio](../virtual-network/service-tags-overview.md#service-tags-on-premises). Si noti che l'API di individuazione dei tag di servizio è ancora in anteprima e in alcuni casi potrebbe non produrre l'elenco completo di tag e indirizzi IP. Fino a quando l'API di individuazione non è disponibile a livello generale, è consigliabile usare i [tag del servizio in formato JSON scaricabile](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files). 

* Usare il tag *AzureIoTHub.[nome area]* per identificare i prefissi IP usati dagli endpoint dell'hub IoT in un'area specifica. Per tenere conto dei ripristini di emergenza del data center o dei [failover a livello di area](iot-hub-ha-dr.md) assicurarsi che sia abilitata anche la connettività ai prefissi IP dell'area geografica dell'hub IoT.

* La configurazione delle regole del firewall nell'hub IoT può impedire la connettività necessaria per eseguire l'interfaccia della riga di comando di Azure e i comandi PowerShell nell'hub IoT. Per evitare questo problema, è possibile aggiungere le regole ALLOW per i prefissi degli indirizzi IP dei client per riabilitare l'interfaccia della riga di comando o i client PowerShell per la comunicazione con l'hub IoT.  


## <a name="limitations-and-workarounds"></a>Limitazioni e soluzioni alternative

* La funzionalità di filtro IP dell'hub IoT ha un limite di 10 regole. Questo limite può essere aumentato con richieste tramite l'assistenza clienti di Azure. 

* Le [regole di filtro IP](iot-hub-ip-filtering.md) configurate sono applicate solo agli endpoint IP dell'hub IoT e non all'endpoint predefinito dell'hub eventi dell'hub IoT. Se è necessario applicare anche il filtro IP nell'hub eventi in cui sono archiviati i messaggi, è possibile usare la risorsa dell'hub eventi in cui configurare direttamente le regole di filtro IP desiderate. A tale scopo, è necessario effettuare il provisioning della risorsa dell'hub eventi e configurare il [routing dei messaggi](./iot-hub-devguide-messages-d2c.md) per inviare i messaggi alla risorsa anziché all'hub eventi predefinito dell'hub IoT. Infine, come illustrato nella tabella precedente, per abilitare la funzionalità di routing dei messaggi, è necessario anche consentire la connettività dai prefissi degli indirizzi IP dell'hub IoT alla risorsa dell'hub eventi di cui è stato effettuato il provisioning.

* Quando si esegue il routing a un account di archiviazione, è possibile consentire il traffico dai prefissi degli indirizzi IP dell'hub IoT solo quando l'account di archiviazione si trova in un'area diversa come l'hub IoT.

## <a name="support-for-ipv6"></a>Supporto per IPv6 

IPv6 non è attualmente supportato nell'hub IoT.
