---
title: Informazioni sull'indirizzo IP dell'hub Internet delle cose | Microsoft Docs
description: Informazioni su come eseguire una query sull'indirizzo IP dell'hub Internet e sulle relative proprietà. È possibile modificare l'indirizzo IP dell'hub Internet in determinati scenari, ad esempio il ripristino di emergenza o il failover a livello di area.
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
# <a name="iot-hub-ip-addresses"></a>Indirizzi IP dell'hub Internet

I prefissi degli indirizzi IP degli endpoint pubblici dell'hub Internet vengono pubblicati periodicamente sotto il [tag del servizio](../virtual-network/service-tags-overview.md) _AzureIoTHub_ .

> [!NOTE]
> Per i dispositivi distribuiti all'interno di reti locali, l'hub Azure per la connettività VNET supporta l'integrazione con gli endpoint privati. Per altre informazioni, vedere [supporto dell'hub Internet per VNET](./virtual-network-support.md#ingress-connectivity-to-iot-hub-using-private-endpoints) .


È possibile usare questi prefissi degli indirizzi IP per controllare la connettività tra l'hub Internet e i dispositivi o gli asset di rete per implementare un'ampia gamma di obiettivi di isolamento rete:

| Obiettivo | Scenari possibili | Approccio |
|------|-----------|----------|
| Assicurarsi che i dispositivi e i servizi comunicano con gli endpoint dell'hub Internet. | Messaggistica [da dispositivo a cloud](./iot-hub-devguide-messaging.md)e [da cloud a dispositivo](./iot-hub-devguide-messages-c2d.md) , [metodi diretti](./iot-hub-devguide-direct-methods.md), [dispositivi e moduli gemelli](./iot-hub-devguide-device-twins.md) e flussi di [dispositivi](./iot-hub-device-streams-overview.md) | Usare i tag di servizio _AzureIoTHub_ e _EventHub_ per individuare l'hub e i prefissi degli indirizzi IP dell'hub eventi e configurare le regole Consenti nell'impostazione del firewall per i dispositivi e i servizi per i prefissi degli indirizzi IP di conseguenza; eliminare il traffico verso altri indirizzi IP di destinazione per i quali non si vuole che i dispositivi o i servizi comunicano. |
| Assicurarsi che l'endpoint del dispositivo dell'hub Internet riceva le connessioni solo dai dispositivi e dalle risorse di rete | Messaggistica [da dispositivo a cloud](./iot-hub-devguide-messaging.md)e [da cloud a dispositivo](./iot-hub-devguide-messages-c2d.md) , [metodi diretti](./iot-hub-devguide-direct-methods.md), [dispositivi e moduli gemelli](./iot-hub-devguide-device-twins.md) e flussi di [dispositivi](./iot-hub-device-streams-overview.md) | Usare la [funzionalità filtro IP](iot-hub-ip-filtering.md) dell'hub Internet per consentire le connessioni dai dispositivi e dagli indirizzi IP degli asset di rete. vedere la sezione [limitazioni](#limitations-and-workarounds) . | 
| Assicurarsi che le risorse dell'endpoint personalizzato delle route (account di archiviazione, bus di servizio e hub eventi) siano raggiungibili solo dalle risorse di rete | [Routing dei messaggi](./iot-hub-devguide-messages-d2c.md) | Seguire le indicazioni della risorsa sulla limitazione della connettività, ad esempio tramite [regole del firewall](../storage/common/storage-network-security.md), [collegamenti privati](../private-link/private-endpoint-overview.md)o [endpoint di servizio](../virtual-network/virtual-network-service-endpoints-overview.md). usare i tag del servizio _AzureIoTHub_ per individuare i prefissi degli indirizzi IP dell'hub Internet e aggiungere le regole di autorizzazione per tali prefissi IP nella configurazione del firewall della risorsa. vedere la sezione [limitazioni](#limitations-and-workarounds) . |



## <a name="best-practices"></a>Procedure consigliate

* Quando si aggiungono le regole Consenti nella configurazione del firewall dei dispositivi, è preferibile fornire [porte specifiche utilizzate dai protocolli applicabili](./iot-hub-devguide-protocols.md#port-numbers).

* I prefissi degli indirizzi IP dell'hub Internet è soggetto a modifiche. Queste modifiche vengono pubblicate periodicamente tramite i tag di servizio prima di avere effetto. È pertanto importante sviluppare processi per recuperare e usare regolarmente i tag del servizio più recenti. Questo processo può essere automatizzato tramite l' [API di individuazione dei tag di servizio](../virtual-network/service-tags-overview.md#service-tags-on-premises). Si noti che l'API di individuazione dei tag di servizio è ancora in anteprima e in alcuni casi potrebbe non produrre l'elenco completo di tag e indirizzi IP. Fino a quando l'API di individuazione non è disponibile a livello generale, è consigliabile usare i [tag del servizio in formato JSON scaricabile](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files). 

* Usare *AzureIoTHub. [ nome dell'area]* tag per identificare i prefissi IP usati dagli endpoint dell'hub Internet in un'area specifica. Per tenere conto del ripristino di emergenza del Data Center o del [failover a livello](iot-hub-ha-dr.md) di area, è abilitata anche la connettività ai prefissi IP dell'area geografica dell'hub Internet.

* La configurazione delle regole del firewall nell'hub di Internet delle cose può impedire la connettività necessaria per eseguire l'interfaccia della riga di comando di Azure e i comandi PowerShell nell'hub Internet. Per evitare questo problema, è possibile aggiungere le regole Consenti per i prefissi degli indirizzi IP dei client per riabilitare l'interfaccia della riga di comando o i client PowerShell per comunicare con l'hub Internet.  


## <a name="limitations-and-workarounds"></a>Limitazioni e soluzioni alternative

* La funzionalità filtro IP dell'hub Internet ha un limite di 10 regole. Questo limite e può essere generato tramite richieste tramite il supporto tecnico di Azure. 

* Le [regole di filtro IP](iot-hub-ip-filtering.md) configurate si applicano solo agli endpoint IP dell'hub Internet e non all'endpoint predefinito dell'hub eventi dell'hub. Se è necessario applicare anche il filtro IP nell'hub eventi in cui sono archiviati i messaggi, è possibile usare la risorsa dell'hub eventi in cui è possibile configurare direttamente le regole di filtro IP desiderate. A tale scopo, è necessario effettuare il provisioning della risorsa dell'hub eventi e configurare il [routing](./iot-hub-devguide-messages-d2c.md) dei messaggi per inviare i messaggi a tale risorsa anziché all'hub eventi predefinito dell'hub. Infine, come illustrato nella tabella precedente, per abilitare la funzionalità di routing dei messaggi, è necessario anche consentire la connettività dai prefissi degli indirizzi IP dell'hub Internet alla risorsa dell'hub eventi sottoposta a provisioning.

* Quando si esegue il routing a un account di archiviazione, consentire il traffico dai prefissi degli indirizzi IP dell'hub Internet è possibile solo quando l'account di archiviazione si trova in un'area diversa come hub Internet.

## <a name="support-for-ipv6"></a>Supporto per IPv6 

IPv6 non è attualmente supportato nell'hub Internet delle cose.
