---
title: Informazioni sull'indirizzo IP dell'istanza del servizio Device provisioning (DPS) Microsoft Docs
description: Informazioni su come eseguire una query sull'indirizzo del servizio Device provisioning (DPS) e sulle relative proprietà. L'indirizzo IP dell'istanza di DPS può variare in determinati scenari, ad esempio il ripristino di emergenza o il failover a livello di area.
author: wesmc7777
ms.author: wesmc
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: f6afd5c4cc5aa0215f943979ae91389b39d449f6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "79284928"
---
# <a name="iot-hub-dps-ip-addresses"></a>Indirizzi IP DPS dell'hub Internet

I prefissi degli indirizzi IP per gli endpoint pubblici di un servizio Device provisioning (DPS) dell'hub Internet vengono pubblicati periodicamente nel _AzureIoTHub_ [tag del servizio](../virtual-network/service-tags-overview.md)AzureIoTHub. È possibile usare questi prefissi degli indirizzi IP per controllare la connettività tra un'istanza di Internet delle cose e dispositivi o risorse di rete per implementare una serie di obiettivi di isolamento rete:

| Obiettivo | Approccio |
|------|----------|
| Assicurarsi che i dispositivi e i servizi comunicano con gli endpoint DPS dell'hub | Usare il tag del servizio _AzureIoTHub_ per individuare le istanze di DPS dell'hub. Configurare di conseguenza le regole Consenti nell'impostazione del firewall per i dispositivi e i servizi per tali prefissi di indirizzi IP. Configurare le regole per eliminare il traffico verso altri indirizzi IP di destinazione per i quali non si vuole che i dispositivi o i servizi comunicano. |
| Assicurarsi che l'endpoint DPS dell'hub Internet riceva le connessioni solo dai dispositivi e dalle risorse di rete | Usare la [funzionalità filtro IP](iot-dps-ip-filtering.md) di Internet delle cose per creare regole di filtro per le API del dispositivo e del servizio DPS. Queste regole di filtro possono essere usate per consentire le connessioni solo dai dispositivi e dagli indirizzi IP degli asset di rete. vedere la sezione [limitazioni](#limitations-and-workarounds) . | 




## <a name="best-practices"></a>Procedure consigliate

* Quando si aggiungono le regole ALLOW nella configurazione del firewall dei dispositivi, è preferibile impostare [porte specifiche usate dai protocolli applicabili](../iot-hub/iot-hub-devguide-protocols.md#port-numbers).

* I prefissi degli indirizzi IP delle istanze di DPS sono soggetti a modifiche. Queste modifiche vengono pubblicate periodicamente tramite i tag di servizio prima di diventare effettive. Per questa ragione è importante sviluppare processi per recuperare e usare regolarmente i tag di servizio più recenti. Questo processo può essere automatizzato tramite l'[API di individuazione dei tag di servizio](../virtual-network/service-tags-overview.md#service-tags-on-premises). L'API di individuazione dei tag del servizio è ancora in anteprima e in alcuni casi potrebbe non produrre l'elenco completo dei tag e degli indirizzi IP. Fino a quando l'API di individuazione non è disponibile a livello generale, è consigliabile usare i [tag del servizio in formato JSON scaricabile](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files). 

* Usare *AzureIoTHub. [ nome area]* tag per identificare i prefissi IP usati dagli endpoint DPS in un'area specifica. Per tenere conto del ripristino di emergenza del Data Center o del [failover a livello](../iot-hub/iot-hub-ha-dr.md)di area, assicurarsi che sia abilitata anche la connettività ai prefissi IP dell'area della coppia geografica dell'istanza di DPS.

* La configurazione delle regole del firewall per un'istanza DPS può bloccare la connettività necessaria per eseguire l'interfaccia della riga di comando di Azure e i comandi di PowerShell. Per evitare questi problemi di connettività, è possibile aggiungere le regole Consenti per i prefissi degli indirizzi IP dei client per riabilitare l'interfaccia della riga di comando o i client PowerShell per comunicare con l'istanza di DPS.  


## <a name="limitations-and-workarounds"></a>Limitazioni e soluzioni alternative

* La funzionalità filtro IP DPS ha un limite di 100 regole. Questo limite può essere aumentato con richieste tramite l'assistenza clienti di Azure. 

* Le [regole di filtro IP](iot-dps-ip-filtering.md) configurate si applicano solo agli endpoint DPS e non agli endpoint dell'hub degli indirizzi IP collegati. Il filtro IP per gli hub di Internet delle cose collegato deve essere configurato separatamente. Per altre informazioni, vedere [regole di filtro IP dell'hub](../iot-hub/iot-hub-ip-filtering.md)Internet.

## <a name="support-for-ipv6"></a>Supporto per IPv6 

IPv6 non è al momento supportato nell'hub o nel DPS.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulle configurazioni degli indirizzi IP con DPS, vedere:

* [Configurare il filtro IP](iot-dps-ip-filtering.md)
