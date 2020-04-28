---
title: Controllo di sicurezza di Azure-sicurezza della rete
description: Sicurezza di rete del controllo di sicurezza di Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: dad01212be3589af7167082ff22c624fa776772a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82193123"
---
# <a name="security-control-network-security"></a>Controllo di sicurezza: sicurezza di rete

I consigli sulla sicurezza di rete sono incentrati sulla specifica di quali protocolli di rete, porte TCP/UDP e servizi connessi alla rete sono autorizzati o negati l'accesso ai servizi di Azure.

## <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: proteggere le risorse di Azure nelle reti virtuali

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 1.1 | 9,2, 9,4, 14,1, 14,2, 14,3 | Customer |

Verificare che tutte le distribuzioni di subnet della rete virtuale dispongano di un gruppo di sicurezza di rete applicato con controlli di accesso alla rete specifici delle porte e delle origini attendibili dell'applicazione. Se disponibile, usare endpoint privati con collegamento privato per proteggere le risorse dei servizi di Azure nella rete virtuale estendendo l'identità VNet al servizio. Quando gli endpoint privati e il collegamento privato non sono disponibili, usare gli endpoint del servizio. Per i requisiti specifici del servizio, fare riferimento alle raccomandazioni sulla sicurezza per il servizio specifico. 

In alternativa, se si ha un caso d'uso specifico, il requisito può essere soddisfatto implementando il firewall di Azure.

- [Informazioni sugli endpoint del servizio rete virtuale](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)

- [Informazioni sul collegamento privato di Azure](https://docs.microsoft.com/azure/private-link/private-link-overview)

- [Come creare una rete virtuale](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

- [Come creare una NSG con una configurazione di sicurezza](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

- [Come distribuire e configurare il firewall di Azure](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

## <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: monitorare e registrare la configurazione e il traffico di reti virtuali, subnet e NIC

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 1.2 | 9,3, 12,2, 12,8 | Customer |

Usare il Centro sicurezza di Azure e seguire le raccomandazioni per la protezione della rete per proteggere le risorse di rete in Azure. Abilitare i log di flusso NSG e inviare i log a un account di archiviazione per il controllo del traffico. È anche possibile inviare i log dei flussi di NSG a un'area di lavoro Log Analytics e usare Analisi del traffico per fornire informazioni dettagliate sul flusso del traffico nel cloud di Azure. Alcuni vantaggi di Analisi del traffico sono la possibilità di visualizzare l'attività di rete e identificare le aree sensibili, identificare le minacce per la sicurezza, comprendere i modelli di flusso del traffico e individuare le configurazioni di rete non configurate.

- [Come abilitare i log di flusso NSG](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

- [Come abilitare e usare Analisi del traffico](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

- [Informazioni sulla sicurezza di rete fornita dal centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

## <a name="13-protect-critical-web-applications"></a>1,3: proteggere le applicazioni Web critiche

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 1.3 | 9,5 | Customer |

Distribuire il Web Application Firewall (WAF) di Azure davanti alle applicazioni Web critiche per un ulteriore controllo del traffico in ingresso. Abilitare l'impostazione di diagnostica per WAF e inserire i log in un account di archiviazione, un hub eventi o un'area di lavoro Log Analytics.

- [Come distribuire Azure WAF](https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag)

## <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1,4: negare le comunicazioni con indirizzi IP dannosi noti

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 1.4 | 12.3 | Customer |

Abilitare la protezione standard DDoS nelle reti virtuali di Azure per proteggersi da attacchi DDoS. Usare il Centro sicurezza di Azure Integrated Threat Intelligence per negare le comunicazioni con indirizzi IP dannosi noti.

Distribuire il firewall di Azure in ogni limite di rete dell'organizzazione con l'Intelligence per le minacce abilitata e configurata per "avviso e negazione" per il traffico di rete dannoso.

Usare il Centro sicurezza di Azure accesso alla rete just-in-time per configurare gruppi per limitare l'esposizione degli endpoint agli indirizzi IP approvati per un periodo di tempo limitato.

Usare il Centro sicurezza di Azure Adaptive Network hardening per consigliare configurazioni NSG che limitano le porte e gli indirizzi IP di origine in base al traffico effettivo e all'Intelligence per le minacce.

- [Come configurare la protezione DDoS](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)

- [Come distribuire il firewall di Azure](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

- [Informazioni sull'Intelligence per le minacce integrata nel centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer)

- [Informazioni sul centro sicurezza di Azure Adaptive Network hardening](https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening)

- [Informazioni sul controllo di accesso di rete just-in-Time del Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)

## <a name="15-record-network-packets"></a>1,5: registrare i pacchetti di rete

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 1.5 | 12.5 | Customer |

Abilitare Network Watcher acquisizione pacchetti per analizzare le attività anomale.

- [Come abilitare Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

## <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1,6: distribuire sistemi di rilevamento intrusioni/intrusioni basati su rete (IDS/IP)

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 1.6 | 12,6, 12,7 | Customer |

Selezionare un'offerta da Azure Marketplace che supporta la funzionalità di ID/IP con le funzionalità di ispezione del payload.  Se il rilevamento delle intrusioni e/o la prevenzione basata sull'ispezione del payload non è un requisito, è possibile usare il firewall di Azure con Intelligence per le minacce. Il filtro basato su Intelligence per le minacce del firewall di Azure può segnalare e negare il traffico da e verso domini e indirizzi IP dannosi noti. Gli indirizzi IP e i domini sono originati dal feed Intelligence sulle minacce Microsoft.

Distribuire la soluzione firewall scelta a ogni limite di rete dell'organizzazione per rilevare e/o negare il traffico dannoso.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Come distribuire il firewall di Azure](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

- [Come configurare gli avvisi con il firewall di Azure](https://docs.microsoft.com/azure/firewall/threat-intel)

## <a name="17-manage-traffic-to-web-applications"></a>1,7: gestire il traffico per le applicazioni Web

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 1.7 | 12,9, 12,10 | Customer |

Distribuire applicazione Azure gateway per le applicazioni Web con HTTPS/TLS abilitato per i certificati attendibili.

- [Come distribuire il gateway applicazione](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)

- [Come configurare il gateway applicazione per l'uso di HTTPS](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal)

- [Informazioni sul bilanciamento del carico di livello 7 con i gateway applicazione Web di Azure](https://docs.microsoft.com/azure/application-gateway/overview)

## <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1,8: ridurre la complessità e il sovraccarico amministrativo delle regole di sicurezza di rete

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 1.8 | 1.5 | Customer |

Usare i tag del servizio rete virtuale per definire i controlli di accesso alla rete nei gruppi di sicurezza di rete o nel firewall di Azure. È possibile usare tag di servizio invece di indirizzi IP specifici nella creazione di regole di sicurezza. Specificando il nome del tag di servizio (ad esempio, ApiManagement) nel campo di origine o di destinazione appropriato di una regola, è possibile consentire o negare il traffico per il servizio corrispondente. Microsoft gestisce i prefissi di indirizzo inclusi nel tag del servizio e aggiorna automaticamente il tag di servizio in base alla modifica degli indirizzi.

È anche possibile usare i gruppi di sicurezza delle applicazioni per semplificare la configurazione della sicurezza complessa. I gruppi di sicurezza delle applicazioni consentono di configurare la sicurezza di rete come un'estensione naturale della struttura di un'applicazione, raggruppando le macchine virtuali e definendo i criteri di sicurezza di rete in base a tali gruppi.

- [Comprendere e usare i tag di servizio](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

- [Comprendere e usare i gruppi di sicurezza delle applicazioni](https://docs.microsoft.com/azure/virtual-network/security-overview#application-security-groups)

## <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1,9: mantenere le configurazioni di sicurezza standard per i dispositivi di rete

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 1.9 | 11,1 | Customer |

Definire e implementare configurazioni di sicurezza standard per le risorse di rete con criteri di Azure.

È anche possibile usare i progetti di Azure per semplificare le distribuzioni di Azure su larga scala mediante la creazione di pacchetti di elementi chiave dell'ambiente, ad esempio modelli di Azure Resource Manager, controlli RBAC e criteri, in una singola definizione di progetto. È possibile applicare il progetto a nuove sottoscrizioni e ottimizzare il controllo e la gestione tramite il controllo delle versioni.

- [Come configurare e gestire i criteri di Azure](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Esempi di criteri di Azure per la rete](https://docs.microsoft.com/azure/governance/policy/samples/#network)

- [Come creare un Azure Blueprint](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

## <a name="110-document-traffic-configuration-rules"></a>1,10: regole di configurazione del traffico documento

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 1,10 | 11.2 | Customer |

Usare i tag per gruppi e altre risorse correlate alla sicurezza di rete e al flusso del traffico. Per le singole regole NSG, usare il campo "Description" per specificare le esigenze aziendali e/o la durata (e così via) per le regole che consentono il traffico da e verso una rete.

Usare una delle definizioni di criteri di Azure predefinite correlate all'assegnazione di tag, ad esempio "Richiedi tag e il relativo valore" per garantire che tutte le risorse vengano create con tag e per notificare le risorse esistenti senza tag.

È possibile usare Azure PowerShell o l'interfaccia della riga di comando di Azure per cercare o eseguire azioni sulle risorse in base ai tag.

- [Come creare e usare i tag](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

- [Come creare una rete virtuale](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

- [Come creare una NSG con una configurazione di sicurezza](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

## <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1,11: usare gli strumenti automatici per monitorare le configurazioni delle risorse di rete e rilevare le modifiche

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 1.11 | 11.3 | Customer |

Usare log attività di Azure per monitorare le configurazioni delle risorse e rilevare le modifiche apportate alle risorse di Azure. Creare avvisi in monitoraggio di Azure che verranno attivati quando vengono apportate modifiche alle risorse critiche.

- [Come visualizzare e recuperare gli eventi del log attività di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

- [Come creare avvisi in monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

## <a name="next-steps"></a>Passaggi successivi

- Vedere il controllo di sicurezza successivo: [registrazione e monitoraggio](security-control-logging-monitoring.md)
