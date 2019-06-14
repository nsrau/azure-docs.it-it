---
title: Che cos'è l'indirizzo IP 168.63.129.16? | Microsoft Docs
description: Informazioni sull'indirizzo IP 168.63.129.16 e su come interagisce con le risorse.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: v-jesits
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/15/2019
ms.author: genli
ms.openlocfilehash: acfd5230d1bd572ea5179651558e3f736a8570af
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65833131"
---
# <a name="what-is-ip-address-1686312916"></a>Che cos'è l'indirizzo IP 168.63.129.16?

L'indirizzo IP 168.63.129.16 è un indirizzo IP pubblico virtuale usato per facilitare un canale di comunicazione con le risorse della piattaforma Azure. I clienti possono definire qualsiasi spazio di indirizzi per la propria rete virtuale privata in Azure. Le risorse della piattaforma Azure, quindi, devono essere presentate come un indirizzo IP pubblico univoco. Questo indirizzo IP pubblico virtuale offre i vantaggi seguenti:

- Consente all'agente di macchine virtuali di comunicare con la piattaforma Azure per segnalare che si trova in uno stato "Pronto".
- Consente la comunicazione con il server virtuale DNS per fornire la risoluzione dei nomi filtrati alle risorse (ad esempio, alla macchina virtuale) che non hanno un server DNS personalizzato. Questo filtro garantisce che i clienti possano risolvere solo i nomi host delle proprie risorse.
- Abilita [probe di integrità dal servizio Azure load balancer](../load-balancer/load-balancer-custom-probe-overview.md) per determinare lo stato di integrità delle macchine virtuali.
- Consente alla macchina virtuale di ottenere un indirizzo IP dinamico dal servizio DHCP in Azure.
- Consente messaggi heartbeat dell'agente guest per il ruolo PaaS.

## <a name="scope-of-ip-address-1686312916"></a>Ambito dell'indirizzo IP 168.63.129.16

L'indirizzo IP pubblico 168.63.129.16 viene usato in tutte le aree e tutti i cloud nazionali. Questo particolare indirizzo IP pubblico è di proprietà di Microsoft e non cambia. È consentito dalla regola del gruppo di sicurezza di rete predefinito. È consigliabile consentire questo indirizzo IP in tutti i criteri firewall locali. La comunicazione tra questo particolare indirizzo IP e le risorse è sicura poiché solo la piattaforma Azure interna può generare un messaggio da questo indirizzo IP. Se questo indirizzo viene bloccato, in alcuni scenari è possibile che si verifichi un comportamento imprevisto.

[I probe di integrità di bilanciamento del carico Azure](../load-balancer/load-balancer-custom-probe-overview.md) proveniente da questo indirizzo IP. Se si blocca questo indirizzo IP, i probe avrà esito negativo.

In uno scenario di rete non virtuale (versione classica), il probe di integrità è originato da un indirizzo IP privato e non viene usato 168.63.129.16.

## <a name="next-steps"></a>Passaggi successivi

- [Gruppi di sicurezza](security-overview.md)
- [Creare, modificare o eliminare un gruppo di sicurezza di rete](manage-network-security-group.md)
