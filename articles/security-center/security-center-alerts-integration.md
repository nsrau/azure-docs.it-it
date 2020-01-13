---
title: Avvisi di rilevamento delle minacce da prodotti di sicurezza di Azure nel centro sicurezza di Azure
description: Questo argomento presenta i prodotti di sicurezza di Azure da cui il Centro sicurezza di Azure può visualizzare gli avvisi sulle minacce
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ad4b0373-08ee-46ca-a573-638ed93a647c
ms.service: security-center
ms.topic: conceptual
ms.date: 01/12/2020
ms.author: memildin
ms.openlocfilehash: 16cae819b1714c2b410cfa311a3602e0f4ed968a
ms.sourcegitcommit: e9776e6574c0819296f28b43c9647aa749d1f5a6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/13/2020
ms.locfileid: "75913029"
---
# <a name="threat-detection-alerts-from-azure-waf-and-azure-ddos-protection"></a>Avvisi di rilevamento delle minacce da Azure WAF e protezione DDoS di Azure

Il Centro sicurezza di Azure può visualizzare e raccogliere gli avvisi di rilevamento delle minacce generati dai prodotti di sicurezza di Azure seguenti, ovvero è necessaria una licenza separata per ogni prodotto:

* [Azure WAF](#azure-waf)
* [Protezione DDoS di Azure](#azure-ddos)

## Azure WAF<a name="azure-waf"></a>

Il gateway applicazione di Azure offre un web application firewall (WAF) che garantisce alle applicazioni Web una protezione centralizzata da exploit e vulnerabilità comuni.

Le applicazioni Web sono sempre più vittime di attacchi che sfruttano le più comuni e note vulnerabilità. Il WAF del gateway applicazione è basato sul set di regole di base 3,0 o 2.2.9 dal progetto di sicurezza dell'applicazione Web aperta. Il WAF viene aggiornato automaticamente per proteggersi da nuove vulnerabilità, senza che sia necessaria alcuna configurazione aggiuntiva. Gli avvisi WAF vengono trasmessi al centro sicurezza. Per ulteriori informazioni sugli avvisi generati da WAF, vedere [regole e gruppi di regole CRS del Web Application Firewall](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31#crs911-31).

## Protezione DDoS di Azure<a name="azure-ddos"></a>

Gli attacchi Distributed Denial of Service (DDoS) sono noti per essere facili da eseguire. Si tratta di un problema di sicurezza eccezionale, in particolare se si trasferiscono le applicazioni nel cloud. 

Un attacco DDoS tenta di esaurire le risorse di un'applicazione, che quindi non risulta più disponibile per gli utenti legittimi. Gli attacchi DDoS possono avere come destinazione qualsiasi endpoint che può essere raggiunto tramite Internet.

Protezione DDoS di Azure, in combinazione con le procedure consigliate per la progettazione di applicazioni, offre una difesa contro gli attacchi DDoS. La protezione DDoS offre diversi livelli di servizio. Per altre informazioni, vedere [Panoramica di protezione DDoS di Azure](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview).

Per un elenco degli avvisi di protezione DDoS di Azure, vedere la [tabella di riferimento degli avvisi](alerts-reference.md#alerts-azureddos).