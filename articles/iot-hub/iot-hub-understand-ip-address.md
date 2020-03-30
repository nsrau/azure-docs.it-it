---
title: Comprendere l'indirizzo IP dell'hub IoT Documenti Microsoft
description: Comprendere come eseguire una query sull'indirizzo IP dell'hub IoT e sulle relative proprietà. L'indirizzo IP dell'hub IoT può cambiare durante determinati scenari, ad esempio il ripristino di emergenza o il failover regionale.
author: philmea
ms.author: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: c609f2a3843481442e97061739a806de60a680b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367568"
---
# <a name="iot-hub-ip-addresses"></a>Indirizzi IP dell'hub IoT

I prefissi degli indirizzi IP degli endpoint pubblici dell'hub IoT vengono pubblicati periodicamente nel tag del [servizio](../virtual-network/service-tags-overview.md) _AzureIoTHub._

> [!NOTE]
> Per i dispositivi distribuiti all'interno di reti locali, l'hub IoT di Azure supporta l'integrazione della connettività VNET con gli endpoint privati. Per altre informazioni, vedere [Supporto dell'hub IoT per VNET.See IoT Hub support for VNET's](./virtual-network-support.md#ingress-connectivity-to-iot-hub-using-private-endpoints) for more information.


Puoi usare questi prefissi di indirizzi IP per controllare la connettività tra l'hub IoT e i tuoi dispositivi o risorse di rete per implementare una varietà di obiettivi di isolamento della rete:

| Obiettivo | Scenari possibili | Approccio |
|------|-----------|----------|
| Assicurati che i dispositivi e i servizi comunichino solo con gli endpoint dell'hub IoT | [Dispositivo-cloud](./iot-hub-devguide-messaging.md)e messaggistica [da cloud a dispositivo,](./iot-hub-devguide-messages-c2d.md) [metodi diretti, dispositivi](./iot-hub-devguide-direct-methods.md)e moduli [gemelli](./iot-hub-devguide-device-twins.md) e [flussi di dispositivi](./iot-hub-device-streams-overview.md) | Usare i tag del servizio _AzureIoTHub_ e _EventHub_ per individuare i prefissi degli indirizzi IP dell'hub IoT e dell'hub eventi e configurare di conseguenza le regole ALLOW per i prefissi degli indirizzi IP e dei dispositivi per tali prefissi di indirizzo IP. eliminare il traffico verso altri indirizzi IP di destinazione con cui non si desidera che i dispositivi o i servizi comunichino. |
| Assicurati che l'endpoint del dispositivo Dell'hub IoT riceva le connessioni solo dai tuoi dispositivi e dalle risorse di rete | [Dispositivo-cloud](./iot-hub-devguide-messaging.md)e messaggistica [da cloud a dispositivo,](./iot-hub-devguide-messages-c2d.md) [metodi diretti, dispositivi](./iot-hub-devguide-direct-methods.md)e moduli [gemelli](./iot-hub-devguide-device-twins.md) e [flussi di dispositivi](./iot-hub-device-streams-overview.md) | Utilizzare la funzionalità di [filtro IP dell'hub](iot-hub-ip-filtering.md) IoT per consentire le connessioni dai dispositivi e gli indirizzi IP degli asset di rete (vedere la sezione [limitazioni).](#limitations-and-workarounds) | 
| Verificare che le risorse endpoint personalizzate delle route (account di archiviazione, bus di servizio e hub eventi) siano raggiungibili solo dalle risorse di reteEnsure your routes' custom endpoint resources (storage accounts, service bus and event hubs) are reachable from your network assets only | [Routing dei messaggi](./iot-hub-devguide-messages-d2c.md) | Seguire le indicazioni fornite dalla risorsa per limitare la connettività (ad esempio tramite [regole del firewall,](../storage/common/storage-network-security.md) [collegamenti privati](../private-link/private-endpoint-overview.md)o endpoint del [servizio);](../virtual-network/virtual-network-service-endpoints-overview.md) Usare i tag del servizio _AzureIoTHub_ per individuare i prefissi degli indirizzi IP dell'hub IoT e aggiungere le regole ALLOW per tali prefissi IP nella configurazione del firewall della risorsa (vedere la sezione [limitazioni).](#limitations-and-workarounds) |



## <a name="best-practices"></a>Procedure consigliate

* Quando si aggiungono regole ALLOW nella configurazione del firewall dei dispositivi, è consigliabile fornire porte specifiche [utilizzate dai protocolli applicabili.](./iot-hub-devguide-protocols.md#port-numbers)

* I prefissi degli indirizzi IP dell'hub IoT sono soggetti a modifiche. Queste modifiche vengono pubblicate periodicamente tramite tag di servizio prima di rendere effettive. È quindi importante sviluppare processi per recuperare e utilizzare regolarmente i tag di servizio più recenti. Questo processo può essere automatizzato tramite l'API di individuazione dei tag del [servizio.](../virtual-network/service-tags-overview.md#service-tags-on-premises) Si noti che l'API di individuazione dei tag del servizio è ancora in anteprima e in alcuni casi potrebbe non produrre l'elenco completo di tag e indirizzi IP. Finché l'API di individuazione non è generalmente disponibile, è consigliabile usare i tag del [servizio in formato JSON scaricabile.](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) 

* Usare *AzureIoTHub.[ nome area]* per identificare i prefissi IP utilizzati dagli endpoint dell'hub IoT in un'area specifica. Per tenere conto del ripristino di emergenza del data center o del [failover regionale,](iot-hub-ha-dr.md) verificare che sia abilitata anche la connettività ai prefissi IP dell'area della coppia geografica dell'hub IoT.To account for datacenter disaster recovery, or regional failover ensure connectivity to IP prefixes of your IoT Hub's geo-pair region is also enabled.

* La configurazione delle regole del firewall nell'hub IoT può bloccare la connettività necessaria per eseguire i comandi dell'interfaccia della riga di comando e di PowerShell di Azure nell'hub IoT.Setting up firewall rules in IoT Hub may block off connectivity needed to run Azure CLI and PowerShell commands against your IoT Hub. To avoid this, you can add ALLOW rules for your clients' IP address prefixes to re-enable CLI or PowerShell clients to communicate with your IoT Hub.  


## <a name="limitations-and-workarounds"></a>Limitazioni e soluzioni alternative

* La funzionalità di filtro IP dell'hub IoT ha un limite di 10 regole. Questo limite e può essere generato tramite richieste tramite il supporto clienti di Azure.This limit and can be raised via requests through Azure Customer Support. 

* Le [regole](iot-hub-ip-filtering.md) di filtro IP configurate vengono applicate solo agli endpoint IP dell'hub IoT e non all'endpoint hub eventi incorporato dell'hub IoT.Your configured IP filtering rules are only applied on your IoT Hub ENDPOINTs and not on your IoT hub hub hub endpoint. Se è necessario applicare anche il filtro IP nell'Hub eventi in cui sono archiviati i messaggi, è possibile portare direttamente la propria risorsa Hub eventi in cui è possibile configurare direttamente le regole di filtro IP desiderate. A tale scopo, è necessario eseguire il provisioning della propria risorsa Hub eventi e configurare il routing dei [messaggi](./iot-hub-devguide-messages-d2c.md) per inviare i messaggi a tale risorsa anziché all'hub IoT incorporato. Infine, come illustrato nella tabella precedente, per abilitare la funzionalità di routing dei messaggi è inoltre necessario consentire la connettività dai prefissi degli indirizzi IP dell'hub IoT alla risorsa Hub eventi di cui è stato eseguito il provisioning.

* Quando si esegue il routing a un account di archiviazione, consentire il traffico dai prefissi degli indirizzi IP dell'hub IoT è possibile solo quando l'account di archiviazione si trova in un'area diversa come hub IoT.When routing to a storage account, allowing traffic from IoT Hub's IP addresses is only possible when the storage account is in a different region as your IoT Hub.

## <a name="support-for-ipv6"></a>Supporto per IPv6 

IPv6 non è attualmente supportato nell'hub IoT.
