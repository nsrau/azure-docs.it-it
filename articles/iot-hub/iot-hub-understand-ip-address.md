---
title: Informazioni sull'indirizzo IP dell'hub Internet delle cose | Microsoft Docs
description: Informazioni su come eseguire una query sull'indirizzo IP dell'hub Internet e sulle relative proprietà. È possibile modificare l'indirizzo IP dell'hub Internet in determinati scenari, ad esempio il ripristino di emergenza o il failover a livello di area.
author: philmea
ms.author: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: 5c77fb30ef60c1ad82d0a87442bc8af186c54321
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383905"
---
# <a name="iot-hub-ip-addresses"></a>Indirizzi IP dell'hub Internet

I prefissi degli indirizzi IP dell'hub Internet vengono pubblicati periodicamente nel [tag del servizio](../virtual-network/service-tags-overview.md)AzureIoTHub. Per assicurare il corretto funzionamento, i dispositivi Internet devono avere connettività in uscita per i prefissi degli indirizzi elencati sotto Tag del servizio *AzureIoTHub* . È necessario che i servizi dell'applicazione per le applicazioni abbiano anche una connettività in uscita per gli indirizzi prefissi elencati nel tag del servizio *EventHub* .


## <a name="best-practices"></a>Procedure consigliate

* I prefissi degli indirizzi IP dell'hub Internet è soggetto a modifiche. Queste modifiche vengono pubblicate periodicamente tramite i tag di servizio prima di avere effetto. È pertanto importante sviluppare processi per recuperare e usare regolarmente i tag del servizio più recenti. Questo processo può essere automatizzato tramite l' [API di individuazione dei tag di servizio](../virtual-network/service-tags-overview.md#service-tags-in-on-premises).
* Usare *AzureIoTHub. [ nome dell'area]* tag per identificare i prefissi IP usati dagli endpoint dell'hub Internet in un'area specifica. Per tenere conto del ripristino di emergenza del Data Center o del [failover a livello](iot-hub-ha-dr.md) di area, è abilitata anche la connettività ai prefissi IP dell'area geografica dell'hub Internet.


## <a name="support-for-ipv6"></a>Supporto per IPv6 

IPv6 non è attualmente supportato nell'hub Internet delle cose.