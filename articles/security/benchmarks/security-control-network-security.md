---
title: Controllo di sicurezza di Azure-sicurezza della rete
description: Sicurezza di rete del controllo di sicurezza di Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 3a232f8e8c35e265a8243ac79e465c03f6b9650e
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96487865"
---
# <a name="security-control-network-security"></a>Controllo di sicurezza: sicurezza di rete

I consigli sulla sicurezza di rete sono incentrati sulla specifica di quali protocolli di rete, porte TCP/UDP e servizi connessi alla rete sono autorizzati o negati l'accesso ai servizi di Azure.

## <a name="11-protect-azure-resources-within-virtual-networks"></a>1,1: proteggere le risorse di Azure nelle reti virtuali

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 1.1 | 9,2, 9,4, 14,1, 14,2, 14,3 | Customer |

Verificare che tutte le distribuzioni di subnet della rete virtuale dispongano di un gruppo di sicurezza di rete applicato con controlli di accesso alla rete specifici delle porte e delle origini attendibili dell'applicazione. Se disponibile, usare endpoint privati con collegamento privato per proteggere le risorse dei servizi di Azure nella rete virtuale estendendo l'identità VNet al servizio. Quando gli endpoint privati e il collegamento privato non sono disponibili, usare gli endpoint del servizio. Per i requisiti specifici del servizio, fare riferimento alle raccomandazioni sulla sicurezza per il servizio specifico. 

In alternativa, se si ha un caso d'uso specifico, il requisito può essere soddisfatto implementando il firewall di Azure.

- [Informazioni sugli endpoint del servizio rete virtuale](../../virtual-network/virtual-network-service-endpoints-overview.md)

- [Informazioni sul collegamento privato di Azure](../../private-link/private-link-overview.md)

- [Come creare una rete virtuale](../../virtual-network/quick-create-portal.md)

- [Come creare una NSG con una configurazione di sicurezza](../../virtual-network/tutorial-filter-network-traffic.md)

- [Come distribuire e configurare il firewall di Azure](../../firewall/tutorial-firewall-deploy-portal.md)

## <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1,2: monitorare e registrare la configurazione e il traffico di reti virtuali, subnet e NIC

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 1.2 | 9,3, 12,2, 12,8 | Customer |

Usare il Centro sicurezza di Azure e seguire le raccomandazioni per la protezione della rete per proteggere le risorse di rete in Azure. Abilitare i log dei flussi NSG e inviare i log a un account di archiviazione per il controllo del traffico. È anche possibile inviare i log dei flussi di NSG a un'area di lavoro Log Analytics e usare Analisi del traffico per fornire informazioni dettagliate sul flusso del traffico nel cloud di Azure. L'uso di Analisi del traffico offre diversi vantaggi, tra cui la possibilità di visualizzare l'attività di rete e identificare le aree sensibili, individuare le minacce per la sicurezza, comprendere i modelli di flusso del traffico e individuare le configurazioni di rete errate.

- [Come abilitare i log dei flussi NSG](../../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [Come abilitare e usare Analisi del traffico](../../network-watcher/traffic-analytics.md)

- [Informazioni sulla sicurezza di rete fornita dal centro sicurezza di Azure](../../security-center/security-center-network-recommendations.md)

## <a name="13-protect-critical-web-applications"></a>1.3: proteggere le applicazioni Web critiche

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 1.3 | 9,5 | Customer |

Distribuire il Web Application Firewall (WAF) di Azure davanti alle applicazioni Web critiche per un ulteriore controllo del traffico in ingresso. Abilitare l'impostazione di diagnostica per WAF e inserire i log in un account di archiviazione, un hub eventi o un'area di lavoro Log Analytics.

- [Come distribuire Azure WAF](../../web-application-firewall/ag/create-waf-policy-ag.md)

## <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: negare le comunicazioni con indirizzi IP dannosi noti

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 1.4 | 12.3 | Customer |

Abilitare la protezione standard DDoS nelle reti virtuali di Azure per proteggersi da attacchi DDoS. Usare il Centro sicurezza di Azure Integrated Threat Intelligence per negare le comunicazioni con indirizzi IP dannosi noti.

Distribuire il firewall di Azure in ogni limite di rete dell'organizzazione con l'Intelligence per le minacce abilitata e configurata per "avviso e negazione" per il traffico di rete dannoso.

Usare il Centro sicurezza di Azure accesso alla rete just-in-time per configurare gruppi per limitare l'esposizione degli endpoint agli indirizzi IP approvati per un periodo di tempo limitato.

Usare il Centro sicurezza di Azure Adaptive Network hardening per consigliare configurazioni NSG che limitano le porte e gli indirizzi IP di origine in base al traffico effettivo e all'Intelligence per le minacce.

- [Come configurare la protezione DDoS](../../ddos-protection/manage-ddos-protection.md)

- [Come distribuire il firewall di Azure](../../firewall/tutorial-firewall-deploy-portal.md)

- [Informazioni sull'intelligence sulle minacce integrata nel Centro sicurezza di Azure](../../security-center/azure-defender.md)

- [Informazioni sul centro sicurezza di Azure Adaptive Network hardening](../../security-center/security-center-adaptive-network-hardening.md)

- [Informazioni sul controllo di accesso di rete just-in-Time del Centro sicurezza di Azure](../../security-center/security-center-just-in-time.md)

## <a name="15-record-network-packets"></a>1,5: registrare i pacchetti di rete

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 1.5 | 12.5 | Customer |

Abilitare Network Watcher acquisizione pacchetti per analizzare le attività anomale.

- [Come abilitare Network Watcher](../../network-watcher/network-watcher-create.md)

## <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: distribuire sistemi di rilevamento intrusioni/prevenzione intrusioni (IDS/IPS) basati sulla rete

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 1.6 | 12,6, 12,7 | Customer |

Selezionare un'offerta da Azure Marketplace che supporta la funzionalità di ID/IP con le funzionalità di ispezione del payload.  Se il rilevamento e/o la prevenzione di intrusioni in base all'ispezione del payload non costituisce un requisito, è possibile usare Firewall di Azure con intelligence sulle minacce. I filtri basati sull'intelligence sulle minacce del Firewall di Azure possono creare avvisi e rifiutare il traffico da o verso indirizzi IP e domini dannosi noti. Gli indirizzi IP e i domini sono originati dal feed Intelligence sulle minacce Microsoft.

Distribuire la soluzione firewall scelta a ogni limite di rete dell'organizzazione per rilevare e/o negare il traffico dannoso.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Come distribuire il firewall di Azure](../../firewall/tutorial-firewall-deploy-portal.md)

- [Come configurare gli avvisi con il firewall di Azure](../../firewall/threat-intel.md)

## <a name="17-manage-traffic-to-web-applications"></a>1.7: gestire il traffico verso le applicazioni Web

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 1.7 | 12,9, 12,10 | Customer |

Distribuire applicazione Azure gateway per le applicazioni Web con HTTPS/TLS abilitato per i certificati attendibili.

- [Come distribuire il gateway applicazione](../../application-gateway/quick-create-portal.md)

- [Come configurare il gateway applicazione per l'uso di HTTPS](../../application-gateway/create-ssl-portal.md)

- [Informazioni sul bilanciamento del carico di livello 7 con i gateway applicazione Web di Azure](../../application-gateway/overview.md)

## <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: ridurre al minimo la complessità e il sovraccarico amministrativo delle regole di sicurezza di rete

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 1.8 | 1.5 | Customer |

Usare i tag del servizio rete virtuale per definire i controlli di accesso alla rete nei gruppi di sicurezza di rete o nel firewall di Azure. È possibile usare tag di servizio invece di indirizzi IP specifici nella creazione di regole di sicurezza. Se si specifica il nome del tag di servizio (ad esempio ApiManagement) nel campo di origine o di destinazione appropriato di una regola, è possibile consentire o negare il traffico per il servizio corrispondente. I prefissi di indirizzo inclusi nel tag di servizio sono gestiti da Microsoft, che lo aggiorna automaticamente in caso di modifica degli indirizzi.

È anche possibile usare i gruppi di sicurezza delle applicazioni per semplificare la configurazione della sicurezza complessa. I gruppi di sicurezza delle applicazioni consentono di configurare la sicurezza di rete come un'estensione naturale della struttura di un'applicazione, raggruppando le macchine virtuali e definendo i criteri di sicurezza di rete in base a tali gruppi.

- [Comprendere e usare i tag di servizio](../../virtual-network/service-tags-overview.md)

- [Comprendere e usare i gruppi di sicurezza delle applicazioni](../../virtual-network/network-security-groups-overview.md#application-security-groups)

## <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: gestire le configurazioni di sicurezza standard per i dispositivi di rete

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 1,9 | 11,1 | Customer |

Definire e implementare configurazioni di sicurezza standard per le risorse di rete con criteri di Azure.

È anche possibile usare i progetti di Azure per semplificare le distribuzioni di Azure su larga scala mediante la creazione di pacchetti di elementi chiave dell'ambiente, ad esempio modelli di Azure Resource Manager, controlli RBAC di Azure e criteri, in una singola definizione di progetto. È possibile applicare il progetto a nuove sottoscrizioni e ottimizzare il controllo e la gestione tramite il controllo delle versioni.

- [Come configurare e gestire Criteri di Azure](../../governance/policy/tutorials/create-and-manage.md)

- [Esempi di criteri di Azure per la rete](../../governance/policy/samples/built-in-policies.md#network)

- [Come creare un progetto di Azure](../../governance/blueprints/create-blueprint-portal.md)

## <a name="110-document-traffic-configuration-rules"></a>1.10: documentare le regole di configurazione del traffico

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 1,10 | 11.2 | Customer |

Usare i tag per gruppi e altre risorse correlate alla sicurezza di rete e al flusso del traffico. Per le regole dei singoli gruppi di sicurezza di rete, usare il campo "Descrizione" per specificare le esigenze aziendali e/o la durata (e così via) per le regole che consentono il traffico da e verso una rete.

Usare una delle definizioni di criteri di Azure predefinite correlate all'assegnazione di tag, ad esempio "Richiedi tag e il relativo valore" per garantire che tutte le risorse vengano create con tag e per notificare le risorse esistenti senza tag.

È possibile usare Azure PowerShell o l'interfaccia della riga di comando di Azure per cercare o eseguire azioni sulle risorse in base ai tag.

- [Come creare e usare i tag](../../azure-resource-manager/management/tag-resources.md)

- [Come creare una rete virtuale](../../virtual-network/quick-create-portal.md)

- [Come creare un gruppo di sicurezza di rete con una configurazione di sicurezza](../../virtual-network/tutorial-filter-network-traffic.md)

## <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: usare strumenti automatizzati per monitorare le configurazioni delle risorse di rete e rilevare le modifiche

| ID Azure | ID CIS | Responsabilità |
|--|--|--|
| 1.11 | 11.3 | Customer |

Usare log attività di Azure per monitorare le configurazioni delle risorse e rilevare le modifiche apportate alle risorse di Azure. Creare avvisi in monitoraggio di Azure che verranno attivati quando vengono apportate modifiche alle risorse critiche.

- [Come visualizzare e recuperare gli eventi del log attività di Azure](../../azure-monitor/platform/activity-log.md#view-the-activity-log)

- [Come creare avvisi in Monitoraggio di Azure](../../azure-monitor/platform/alerts-activity-log.md)

## <a name="next-steps"></a>Passaggi successivi

- Vedere il controllo di sicurezza successivo: [registrazione e monitoraggio](security-control-logging-monitoring.md)