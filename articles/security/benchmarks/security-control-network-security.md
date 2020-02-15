---
title: Controllo di sicurezza di Azure-sicurezza della rete
description: Sicurezza di rete del controllo di sicurezza
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 7916bbb28602d64e0916fce7badf16a65c242227
ms.sourcegitcommit: 79cbd20a86cd6f516acc3912d973aef7bf8c66e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2020
ms.locfileid: "77251873"
---
# <a name="security-control-network-security"></a>Controllo di sicurezza: sicurezza di rete

I consigli sulla sicurezza di rete sono incentrati sulla specifica di quali protocolli di rete, porte TCP/UDP e servizi connessi alla rete sono autorizzati o negati l'accesso ai servizi di Azure.

## <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1,1: proteggere le risorse usando i gruppi di sicurezza di rete o il firewall di Azure nella rete virtuale

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 1.1 | 9,2, 9,4, 14,1-14,3 | Cliente |

Verificare che tutte le distribuzioni di subnet della rete virtuale dispongano di un gruppo di sicurezza di rete applicato con controlli di accesso alla rete specifici delle porte e delle origini attendibili dell'applicazione. Usare i servizi di Azure con collegamento privato abilitato, distribuire il servizio all'interno della VNET o connettersi privatamente usando endpoint privati. Per i requisiti specifici del servizio, fare riferimento alle raccomandazioni sulla sicurezza per il servizio specifico.

In alternativa, se si ha un caso d'uso specifico, è possibile soddisfare i requisiti implementando il firewall di Azure.

Informazioni generali sul collegamento privato:

https://docs.microsoft.com/azure/private-link/private-link-overview

Come creare una rete virtuale:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Come creare una NSG con una configurazione di sicurezza:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

Come distribuire e configurare il firewall di Azure:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

## <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1,2: monitorare e registrare la configurazione e il traffico di reti virtuali, subnet e NIC

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 1.2 | 9,3, 12,2 | Cliente |

Usare il Centro sicurezza di Azure e seguire le raccomandazioni per la protezione della rete per proteggere le risorse di rete in Azure. Abilitare i log di flusso NSG e inviare i log a un account di archiviazione per il controllo del traffico.

Come abilitare i log dei flussi di NSG:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Informazioni sulla sicurezza di rete fornita dal centro sicurezza di Azure:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

## <a name="13-protect-critical-web-applications"></a>1,3: proteggere le applicazioni Web critiche

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 1.3 | 9,5 | Cliente |

Distribuire il Web Application Firewall (WAF) di Azure davanti alle applicazioni Web critiche per un ulteriore controllo del traffico in ingresso. Abilitare l'impostazione di diagnostica per WAF e inserire i log in un account di archiviazione, un hub eventi o un'area di lavoro Log Analytics.

Come distribuire Azure WAF:

https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag

## <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: negare le comunicazioni con indirizzi IP dannosi noti

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 1.4 | 12.3 | Cliente |

Abilitare la protezione standard DDoS nelle reti virtuali di Azure per proteggersi da attacchi DDoS. Usare il Centro sicurezza di Azure Integrated Threat Intelligence per negare le comunicazioni con indirizzi IP dannosi noti.

Distribuire il firewall di Azure in ogni limite di rete dell'organizzazione con l'Intelligence per le minacce abilitata e configurata per &quot;avviso e negare&quot; per il traffico di rete dannoso.

Usare il Centro sicurezza di Azure accesso alla rete just-in-time per configurare gruppi per limitare l'esposizione degli endpoint agli indirizzi IP approvati per un periodo di tempo limitato.

Usare il Centro sicurezza di Azure Adaptive Network hardening per consigliare configurazioni NSG che limitano le porte e gli indirizzi IP di origine in base al traffico effettivo e all'Intelligence per le minacce.

Come configurare la protezione DDoS:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Come distribuire il firewall di Azure:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Informazioni sull'Intelligence per le minacce integrata nel centro sicurezza di Azure:

https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

Informazioni sulla protezione avanzata della rete adattiva del Centro sicurezza di Azure:

https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening

Informazioni sul controllo di accesso di rete just-in-Time del Centro sicurezza di Azure:

https://docs.microsoft.com/azure/security-center/security-center-just-in-time

## <a name="15-record-network-packets-and-flow-logs"></a>1,5: registrare i pacchetti di rete e i log di flusso

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 1.5 | 12,5, 15,8 | Cliente |

Registrare i log dei flussi di NSG in un account di archiviazione per generare record di flusso. Se necessario per l'analisi dell'attività anomala, abilitare Network Watcher acquisizione pacchetti.

Come abilitare i log dei flussi di NSG:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Come abilitare Network Watcher:

https://docs.microsoft.com/azure/network-watcher/network-watcher-create

## <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: distribuire sistemi di rilevamento intrusioni/intrusioni basati su rete (IDS/IP)

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 1.6 | 12,6, 12,7 | Cliente |

Distribuire il firewall di Azure in ogni limite di rete dell'organizzazione con l'Intelligence per le minacce abilitata e configurata per &quot;avviso e negare&quot; per il traffico di rete dannoso.

Come distribuire il firewall di Azure: https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Come configurare gli avvisi con il firewall di Azure: https://docs.microsoft.com/azure/firewall/threat-intel

## <a name="17-manage-traffic-to-web-applications"></a>1,7: gestire il traffico per le applicazioni Web

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 1.7 | 12,9, 12,10 | Cliente |

Distribuire applicazione Azure gateway per le applicazioni Web con HTTPS/SSL abilitato per i certificati attendibili.

Come distribuire il gateway applicazione:

https://docs.microsoft.com/azure/application-gateway/quick-create-portal

Come configurare il gateway applicazione per l'uso di HTTPS:

https://docs.microsoft.com/azure/application-gateway/create-ssl-portal

Informazioni sul bilanciamento del carico di livello 7 con i gateway applicazione Web di Azure:

https://docs.microsoft.com/azure/application-gateway/overview

## <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: ridurre la complessità e il sovraccarico amministrativo delle regole di sicurezza di rete

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 1.8 | 1.5 | Cliente |

Usare i tag del servizio rete virtuale per definire i controlli di accesso alla rete nei gruppi di sicurezza di rete o nel firewall di Azure. È possibile usare tag di servizio invece di indirizzi IP specifici nella creazione di regole di sicurezza. Specificando il nome del tag di servizio (ad esempio, ApiManagement) nel campo di origine o di destinazione appropriato di una regola, è possibile consentire o negare il traffico per il servizio corrispondente. Microsoft gestisce i prefissi di indirizzo inclusi nel tag del servizio e aggiorna automaticamente il tag di servizio in base alla modifica degli indirizzi.

Comprendere e usare i tag di servizio:

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

## <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: mantenere le configurazioni di sicurezza standard per i dispositivi di rete

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 1.9 | 11.1 | Cliente |

Definire e implementare configurazioni di sicurezza standard per le risorse di rete con criteri di Azure.

È anche possibile usare i progetti di Azure per semplificare le distribuzioni di Azure su larga scala mediante la creazione di pacchetti di elementi chiave dell'ambiente, ad esempio Azure Resource Manager modelli, controlli RBAC e criteri, in una singola definizione di progetto. È possibile applicare il progetto a nuove sottoscrizioni e ottimizzare il controllo e la gestione tramite il controllo delle versioni.

Come configurare e gestire i criteri di Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Esempi di criteri di Azure per la rete:

https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network

Come creare un Azure Blueprint:

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

## <a name="110-document-traffic-configuration-rules"></a>1,10: regole di configurazione del traffico documento

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 1.1 | 11,2 | Cliente |

Usare i tag per gruppi e altre risorse correlate alla sicurezza di rete e al flusso del traffico. Per le singole regole NSG, usare il campo &quot;Description&quot; per specificare le esigenze aziendali e/o la durata (e così via) per le regole che consentono il traffico da e verso una rete.

Come creare e usare i tag:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Come creare una rete virtuale:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Come creare una NSG con una configurazione di sicurezza:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

## <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: usare gli strumenti automatici per monitorare le configurazioni delle risorse di rete e rilevare le modifiche

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 1.11 | 11.3 | Cliente |

Usare i criteri di Azure per convalidare e/o correggere la configurazione per le risorse di rete.

Come configurare e gestire i criteri di Azure:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Esempi di criteri di Azure per la rete:

https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network

## <a name="next-steps"></a>Passaggi successivi

- Vedere il controllo di sicurezza successivo: [registrazione e monitoraggio](security-control-logging-monitoring.md)
