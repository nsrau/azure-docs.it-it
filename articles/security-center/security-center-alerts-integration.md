---
title: Integrazione dei prodotti per la sicurezza di Azure con il Centro sicurezza di Azure
description: Questo argomento presenta i prodotti di sicurezza di Azure che sono stati integrati con il Centro sicurezza di Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ad4b0373-08ee-46ca-a573-638ed93a647c
ms.service: security-center
ms.topic: conceptual
ms.date: 07/02/2019
ms.author: memildin
ms.openlocfilehash: dc1cb5cde06314e52ac886c1be1bb46b5f4bbd73
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73686474"
---
# <a name="integration-of-azure-security-products-in-azure-security-center"></a>Integrazione dei prodotti per la sicurezza di Azure nel centro sicurezza di Azure

Il Centro sicurezza di Azure offre altre licenze Microsoft per lavorare con i prodotti di sicurezza seguenti:

* [Azure WAF](#azure-waf)
* [Protezione DDoS di Azure](#azure-ddos)

## Azure WAF<a name="azure-waf"></a>

Il gateway applicazione di Azure offre un web application firewall (WAF) che garantisce alle applicazioni Web una protezione centralizzata da exploit e vulnerabilità comuni.

Le applicazioni Web sono sempre più destinate ad attacchi dannosi che sfruttano vulnerabilità comunemente note. Il WAF del gateway applicazione è basato sul set di regole di base 3,0 o 2.2.9 dal progetto di sicurezza dell'applicazione Web aperta. Il WAF viene aggiornato automaticamente per proteggersi da nuove vulnerabilità, senza che sia necessaria alcuna configurazione aggiuntiva. Gli avvisi WAF vengono trasmessi al centro sicurezza. Per ulteriori informazioni sugli avvisi generati da WAF, vedere [regole e gruppi di regole CRS del Web Application Firewall](https://docs.microsoft.com/azure/application-gateway/application-gateway-crs-rulegroups-rules?tabs=owasp3#crs911).

## Protezione DDoS di Azure<a name="azure-ddos"></a>

Gli attacchi Distributed Denial of Service (DDoS) sono noti per essere facili da eseguire. Si tratta di un problema di sicurezza eccezionale, in particolare se si trasferiscono le applicazioni nel cloud. 

Un attacco DDoS tenta di esaurire le risorse di un'applicazione, che quindi non risulta più disponibile per gli utenti legittimi. Gli attacchi DDoS possono avere come destinazione qualsiasi endpoint che può essere raggiunto tramite Internet.

Protezione DDoS di Azure, in combinazione con le procedure consigliate per la progettazione di applicazioni, offre una difesa contro gli attacchi DDoS. La protezione DDoS offre diversi livelli di servizio. Per altre informazioni, vedere [Panoramica di protezione DDoS di Azure](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview).

Protezione DDoS Standard consente di mitigare i seguenti tipi di attacchi:

> [!div class="mx-tableFixed"]

|Avviso|Descrizione|
|---|---|
|**Attacco volumetrico rilevato**|L'obiettivo di questo attacco è quello di inondare il livello di rete con una notevole quantità di traffico apparentemente legittimo. Sono inclusi attacchi flood UDP, attacchi flood con amplificazione e attacchi flood con pacchetti falsificati. Protezione DDoS standard attenua questi potenziali attacchi a più gigabyte mediante l'assorbimento e lo scrubbing, con scalabilità di rete globale, automaticamente.|
|**Attacco del protocollo rilevato**|Questi attacchi rendono inaccessibile una destinazione, sfruttando un punto debole negli stack di protocolli di livello 3 e 4. Include gli attacchi SYN flood, gli attacchi di reflection e altri attacchi del protocollo. Protezione DDoS Standard mitiga questi attacchi distinguendo tra traffico dannoso e legittimo tramite l'interazione con il client e bloccando il traffico dannoso.|
|**Rilevato attacco a livello di risorsa (applicazione)**|Questi attacchi hanno come destinazione pacchetti di applicazioni Web, per compromettere la trasmissione dei dati tra host. Gli attacchi includono violazioni del protocollo HTTP, attacchi SQL injection, script intersito e altri attacchi di livello 7. Usare il gateway applicazione Azure WAF, con protezione DDoS standard, per difendersi da questi attacchi. In Azure Marketplace sono disponibili anche offerte WAF di terze parti.|
