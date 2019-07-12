---
title: Integrazione del Centro sicurezza con i prodotti di sicurezza di Azure | Microsoft Docs
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
ms.author: monhaber
ms.openlocfilehash: 805c770f1a7e9bb4e0619b27ac937a2451421dc6
ms.sourcegitcommit: 1e347ed89854dca2a6180106228bfafadc07c6e5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67571738"
---
# <a name="security-center-integration-with-azure-security-products-in-asc"></a>Integrazione del Centro sicurezza con i prodotti di sicurezza di Azure nel Centro sicurezza di AZURE

Il Centro sicurezza offre ai clienti con licenze Microsoft aggiuntive per l'onboarding i loro risultati al Centro sicurezza e visualizzarli in un modo consolidato.

* [Azure Web Application firewall](#azure-waf)
* [Azure DDoS](#azure-ddos)

## Azure WAF <a name="azure-waf"></a>

Il gateway applicazione di Azure offre un web application firewall (WAF) che garantisce alle applicazioni Web una protezione centralizzata da exploit e vulnerabilità comuni.

Le applicazioni Web sono sempre più destinate da attacchi che sfruttano vulnerabilità comunemente note. Il WAF del Gateway applicazione è basato su Core regola impostato (CRS) 3.0 o 2.2.9 dal Open Web Application Security Project (OWASP). Il WAF viene aggiornato automaticamente per proteggersi da nuove vulnerabilità, senza alcuna configurazione aggiuntiva. Gli avvisi generati dal WAF sono state trasmesse al Centro sicurezza. Per altre informazioni sugli avvisi generati dal WAF, vedere questo [articolo](https://docs.microsoft.com/azure/application-gateway/application-gateway-crs-rulegroups-rules?tabs=owasp3#crs911).

## Azure DDoS <a name="azure-ddos"></a>

Distribuita attacchi denial of dervice (DDoS) sono note facili da eseguire. Pertanto, hanno diventano un problema di sicurezza per i clienti che spostano le applicazioni nel cloud. 

Un attacco DDoS tenta di esaurire le risorse di un'applicazione, che quindi non risulta più disponibile per gli utenti legittimi. Gli attacchi DDoS possono far riferimento a qualsiasi endpoint che può essere raggiunto tramite Internet.

Protezione DDoS di Azure, combinata con progettazione procedure consigliate per applicazioni, offre una difesa contro gli attacchi DDoS. Protezione DDoS di Azure offre diversi livelli di servizio. Per altre informazioni, vedere [Panoramica di protezione DDoS di Azure](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview).

Protezione DDoS Standard consente di mitigare i seguenti tipi di attacchi:

> [!div class="mx-tableFixed"]

|Avviso|DESCRIZIONE|
|---|---|
|**Rilevato attacco volumetrico**|Obiettivo di questo tipo di attacco è quello di intasare il livello di rete con una notevole quantità di traffico in apparenza legittimo. Sono inclusi attacchi flood UDP, attacchi flood con amplificazione e attacchi flood con pacchetti falsificati. Protezione DDoS Standard mitiga questi attacchi con dimensioni potenziali di molti gigabyte assorbendoli ed eseguendone lo scrubbing, sfruttando automaticamente la scalabilità di rete globale di Azure.|
|**Rilevato attacco protocollo**|Questi attacchi rendono inaccessibile una destinazione, sfruttando un punto debole di livello 3 e stack di protocolli 4 di livello. Sono inclusi attacchi di tipo SYN flood, attacchi di tipo reflection e altri attacchi contro i protocolli. Protezione DDoS Standard mitiga questi attacchi distinguendo tra traffico dannoso e legittimo tramite l'interazione con il client e bloccando il traffico dannoso.|
|**Rilevato attacco di livello di risorsa (applicazione)**|Questi attacchi colpiscono i pacchetti di applicazione web, per interrompere la trasmissione dei dati tra host. Gli attacchi includono violazioni del protocollo HTTP, attacchi SQL injection, script intersito e altri attacchi di livello 7. Usare il web application firewall di Gateway applicazione di Azure, con protezione DDoS Standard per difendersi da questi attacchi. Sono disponibili anche offerte di terze parti web application firewall in Azure Marketplace.|
