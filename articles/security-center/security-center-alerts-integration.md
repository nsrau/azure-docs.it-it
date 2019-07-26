---
title: Integrazione dei prodotti per la sicurezza di Azure nel centro sicurezza di Azure | Microsoft Docs
description: Questo argomento presenta i prodotti di sicurezza di Azure che sono stati integrati con il Centro sicurezza di Azure.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: ad4b0373-08ee-46ca-a573-638ed93a647c
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: v-mohabe
ms.openlocfilehash: 847748d1c56221119d8f74a2aee716ee08448e28
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335810"
---
# <a name="integration-of-azure-security-products-in-azure-security-center"></a>Integrazione dei prodotti per la sicurezza di Azure nel centro sicurezza di Azure

Il Centro sicurezza offre ai clienti licenze Microsoft aggiuntive per l'onboarding dei risultati nel centro sicurezza e per visualizzarli in modo consolidato.

* [Azure WAF](#azure-waf)
* [DDoS di Azure](#azure-ddos)

## Azure WAF<a name="azure-waf"></a>

Il gateway applicazione di Azure offre un web application firewall (WAF) che garantisce alle applicazioni Web una protezione centralizzata da exploit e vulnerabilità comuni.

Le applicazioni Web sono sempre più destinate ad attacchi dannosi che sfruttano vulnerabilità comunemente note. Il WAF del gateway applicazione è basato sul set di regole di base 3,0 o 2.2.9 dal progetto Open Web Application Security (OWASP). Il WAF viene aggiornato automaticamente per proteggersi da nuove vulnerabilità, senza che sia necessaria alcuna configurazione aggiuntiva. Gli avvisi generati da WAF vengono trasmessi al centro sicurezza. Per altre informazioni sugli avvisi generati da WAF, vedere questo [articolo](https://docs.microsoft.com/azure/application-gateway/application-gateway-crs-rulegroups-rules?tabs=owasp3#crs911).

## DDoS di Azure<a name="azure-ddos"></a>

Gli attacchi Distributed Denial of Service (DDoS) sono noti per essere facili da eseguire. Pertanto, sono diventati un ottimo problema di sicurezza per i clienti che stanno muovendo le proprie applicazioni nel cloud. 

Un attacco DDoS tenta di esaurire le risorse di un'applicazione, che quindi non risulta più disponibile per gli utenti legittimi. Gli attacchi DDoS possono avere come destinazione qualsiasi endpoint che può essere raggiunto tramite Internet.

Protezione DDoS di Azure, in combinazione con le procedure consigliate per la progettazione di applicazioni, offre una difesa contro gli attacchi DDoS. Protezione DDoS di Azure fornisce livelli di servizio diversi. Per altre informazioni, vedere [Panoramica di protezione DDoS di Azure](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview).

Protezione DDoS Standard consente di mitigare i seguenti tipi di attacchi:

> [!div class="mx-tableFixed"]

|Avviso|DESCRIZIONE|
|---|---|
|**Attacco volumetrico rilevato**|L'obiettivo di questo attacco è quello di inondare il livello di rete con una notevole quantità di traffico apparentemente legittimo. Sono inclusi attacchi flood UDP, attacchi flood con amplificazione e attacchi flood con pacchetti falsificati. Protezione DDoS Standard mitiga questi attacchi con dimensioni potenziali di molti gigabyte assorbendoli ed eseguendone lo scrubbing, sfruttando automaticamente la scalabilità di rete globale di Azure.|
|**Attacco del protocollo rilevato**|Questi attacchi rendono inaccessibile una destinazione, sfruttando un punto debole nello stack di protocolli di livello 3 e 4. Sono inclusi attacchi di tipo SYN flood, attacchi di tipo reflection e altri attacchi contro i protocolli. Protezione DDoS Standard mitiga questi attacchi distinguendo tra traffico dannoso e legittimo tramite l'interazione con il client e bloccando il traffico dannoso.|
|**Rilevato attacco a livello di risorsa (applicazione)**|Questi attacchi hanno come destinazione pacchetti di applicazioni Web, per compromettere la trasmissione dei dati tra host. Gli attacchi includono violazioni del protocollo HTTP, attacchi SQL injection, script intersito e altri attacchi di livello 7. Usare il gateway applicazione Azure web application firewall, con protezione DDoS standard, per difendersi da questi attacchi. In Azure Marketplace sono disponibili anche offerte di web application firewall di terze parti.|
