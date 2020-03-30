---
title: Informazioni sull'indirizzo IP dell'istanza del servizio di provisioning dei dispositivi IoT (DPS) Documenti Microsoft
description: Comprendere come eseguire una query sull'indirizzo DPS (Device Provisioning Service) IoT e sulle relative proprietà. L'indirizzo IP dell'istanza DPS può cambiare durante determinati scenari, ad esempio il ripristino di emergenza o il failover regionale.
author: wesmc7777
ms.author: wesmc
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: f6afd5c4cc5aa0215f943979ae91389b39d449f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284928"
---
# <a name="iot-hub-dps-ip-addresses"></a>Indirizzi IP DPS hub IoT

I prefissi degli indirizzi IP per gli endpoint pubblici di un servizio DPS (Hub Device Provisioning) IoT vengono pubblicati periodicamente nel [tag del servizio](../virtual-network/service-tags-overview.md) _AzureIoTHub._ È possibile utilizzare questi prefissi di indirizzi IP per controllare la connettività tra un'istanza DPS IoT e i dispositivi o le risorse di rete per implementare una varietà di obiettivi di isolamento rete:You may use these IP address prefixes to control connectivity between an IoT DPS instance and devices or network assets in order to implement a variety of network isolation goals:

| Obiettivo | Approccio |
|------|----------|
| Assicurati che i dispositivi e i servizi comunichino solo con gli endpoint DPS dell'hub IoT | Usare il tag del servizio AzureIoTHub per individuare le istanze DPS dell'hub IoT.Use the _AzureIoTHub_ service tag to discover IoT Hub DPS instances. Configurare di conseguenza le regole ALLOW nell'impostazione del firewall dei dispositivi e dei servizi per tali prefissi di indirizzi IP. Configurare le regole per eliminare il traffico verso altri indirizzi IP di destinazione con cui non si desidera che i dispositivi o i servizi comunichino. |
| Assicurati che l'endpoint IoT Hub DPS riceva le connessioni solo dai tuoi dispositivi e dalle tue risorse di rete | Utilizzare la funzionalità di [filtro IP](iot-dps-ip-filtering.md) IoT DPS per creare regole di filtro per il dispositivo e le API del servizio DPS. Queste regole di filtro possono essere utilizzate per consentire le connessioni solo dai dispositivi e dagli indirizzi IP delle risorse di rete (vedere la sezione [limitazioni).](#limitations-and-workarounds) | 




## <a name="best-practices"></a>Procedure consigliate

* Quando si aggiungono regole ALLOW nella configurazione del firewall dei dispositivi, è consigliabile fornire porte specifiche [utilizzate dai protocolli applicabili.](../iot-hub/iot-hub-devguide-protocols.md#port-numbers)

* I prefissi degli indirizzi IP delle istanze DPS IoT sono soggetti a modifiche. Queste modifiche vengono pubblicate periodicamente tramite tag di servizio prima di rendere effettive. È quindi importante sviluppare processi per recuperare e utilizzare regolarmente i tag di servizio più recenti. Questo processo può essere automatizzato tramite l'API di individuazione dei tag del [servizio.](../virtual-network/service-tags-overview.md#service-tags-on-premises) L'API di individuazione dei tag del servizio è ancora in anteprima e in alcuni casi potrebbe non produrre l'elenco completo di tag e indirizzi IP. Finché l'API di individuazione non è generalmente disponibile, è consigliabile usare i tag del [servizio in formato JSON scaricabile.](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) 

* Usare *AzureIoTHub.[ nome regione]* per identificare i prefissi IP utilizzati dagli endpoint DPS in un'area specifica. Per tenere conto del ripristino di emergenza del data center o del [failover regionale,](../iot-hub/iot-hub-ha-dr.md)verificare che sia abilitata anche la connettività ai prefissi IP dell'area di coppia geografica dell'istanza DPS.

* L'impostazione delle regole del firewall per un'istanza DPS può bloccare la connettività necessaria per eseguire i comandi dell'interfaccia della riga di comando e di PowerShell di Azure.Setting up firewall rules for a DPS instance may block off connectivity needed to run Azure CLI and PowerShell commands on it. Per evitare questi problemi di connettività, è possibile aggiungere regole ALLOW per i prefissi degli indirizzi IP dei client per riattivare l'interfaccia della riga di comando o i client PowerShell per comunicare con l'istanza DPS.  


## <a name="limitations-and-workarounds"></a>Limitazioni e soluzioni alternative

* La funzionalità di filtro IP DPS ha un limite di 100 regole. Questo limite e può essere generato tramite richieste tramite il supporto clienti di Azure.This limit and can be raised via requests through Azure Customer Support. 

* Le [regole](iot-dps-ip-filtering.md) di filtro IP configurate vengono applicate solo agli endpoint DPS e non agli endpoint dell'hub IoT collegato. Il filtro IP per gli hub IoT collegati deve essere configurato separatamente. Per ulteriori informazioni, vedere Regole di [filtro IP dell'hub IoT](../iot-hub/iot-hub-ip-filtering.md).

## <a name="support-for-ipv6"></a>Supporto per IPv6 

IPv6 non è attualmente supportato nell'hub IoT o DPS.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulle configurazioni degli indirizzi IP con DPS, vedere:

* [Configurare il filtro IP](iot-dps-ip-filtering.md)
