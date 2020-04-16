---
title: Azure Security Control - Network Security
description: Sicurezza di Azure Sicurezza di Rete
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: f508de35470043cc71bfc8607367f28c04440b57
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81408311"
---
# <a name="security-control-network-security"></a>Controllo di sicurezza: Sicurezza di rete

I consigli per la sicurezza di rete si concentrano sulla specifica dei protocolli di rete, delle porte TCP/UDP e dei servizi connessi alla rete per l'accesso ai servizi di Azure.Network security recommendations focus on specifying which network protocols, TCP/UDP ports, and network connected services are allowed or denied access to Azure services.

## <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Proteggere le risorse di Azure all'interno di reti virtuali

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 1.1 | 9.2, 9.4, 14.1, 14.2, 14.3 | Customer |

Assicurarsi che a tutte le distribuzioni di subnet di rete virtuale sia applicato un gruppo di sicurezza di rete con controlli di accesso di rete specifici per le porte e le origini attendibili dell'applicazione. Se disponibile, usare Endpoint privati con collegamento privato per proteggere le risorse del servizio Azure alla rete virtuale estendendo l'identità della rete virtuale al servizio. Quando gli endpoint privati e private link non sono disponibili, usare gli endpoint del servizio. Per i requisiti specifici del servizio, fare riferimento alla raccomandazione di sicurezza per tale servizio specifico. 

In alternativa, se si dispone di un caso d'uso specifico, il requisito può essere soddisfatto implementando Firewall di Azure.Alternatively, if you have a specific use case, requirement may be met by implementing Azure Firewall.

- [Comprendere gli endpoint del servizio di rete virtualeUnderstand Virtual Network Service Endpoints](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)

- [Informazioni su Azure Private Link](https://docs.microsoft.com/azure/private-link/private-link-overview)

- [Come creare una rete virtuale](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

- [Come creare un gruppo di sicurezza di rete con una configurazione di sicurezzaHow to create an NSG with a security configuration](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

- [Come distribuire e configurare Firewall di AzureHow to deploy and configure Azure Firewall](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

## <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: Monitorare e registrare la configurazione e il traffico di reti virtuali, subnet e schede di interfaccia di rete

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 1.2 | 9.3, 12.2, 12.8 | Customer |

Usare il Centro sicurezza di Azure e seguire i consigli di protezione della rete per proteggere le risorse di rete in Azure.Use Azure Security Center and follow network protection recommendations to help secure your network resources in Azure. Abilitare i log di flusso del gruppo di sicurezza di rete e inviare i log in un account di archiviazione per il controllo del traffico. È anche possibile inviare log di flusso del gruppo di sicurezza di rete a un'area di lavoro di Log Analytics e usare Analisi del traffico per fornire informazioni dettagliate sul flusso di traffico nel cloud di Azure.You may also send NSG flow logs to a Log Analytics Workspace and use Traffic Analytics to provide insights into traffic flow in your Azure cloud. Alcuni vantaggi di Analisi del traffico sono la possibilità di visualizzare l'attività di rete e identificare hot spot, identificare le minacce alla sicurezza, comprendere i modelli di flusso del traffico e individuare le configurazioni errate della rete.

- [Come abilitare i registri di flusso del gruppo di sicurezza di reteHow to Enable NSG Flow Logs](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal)

- [Come abilitare e usare Analisi del traffico](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)

- [Informazioni sulla sicurezza di rete fornita dal Centro sicurezza di AzureUnderstand Network Security provided by Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-network-recommendations)

## <a name="13-protect-critical-web-applications"></a>1.3: Proteggere le applicazioni Web critiche

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 1.3 | 9,5 | Customer |

Distribuire Firewall applicazione Web di Azure davanti alle applicazioni Web critiche per un'ulteriore ispezione del traffico in ingresso. Abilitare l'impostazione di diagnostica per WAF e inserire i log in un account di archiviazione, un hub eventi o un'area di lavoro di Log Analytics.Enable Diagnostic Setting for WAF and ingest logs into a Storage Account, Event Hub, or Log Analytics Workspace.

- [Come distribuire WAF di AzureHow to deploy Azure WAF](https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag)

## <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negare le comunicazioni con indirizzi IP dannosi noti

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 1.4 | 12.3 | Customer |

Abilitare la protezione DDoS Standard nelle reti virtuali di Azure per proteggersi dagli attacchi DDoS.Enable DDoS Standard protection on your Azure Virtual Networks to protection against DDoS attacks. Usare Azure Security Center Integrated Threat Intelligence per negare le comunicazioni con indirizzi IP dannosi noti.

Distribuire Firewall di Azure a ciascuno dei limiti di rete dell'organizzazione con Threat Intelligence abilitato e configurato per "Avvisa e nega" per il traffico di rete dannoso.

Usare l'accesso Just-InTime del Centro sicurezza di Azure per configurare i gruppi di sicurezza di rete per limitare l'esposizione degli endpoint agli indirizzi IP approvati per un periodo limitato.

Usare la protezione avanzata della rete adattiva del Centro sicurezza di Azure per consigliare configurazioni del gruppo di sicurezza di rete che limitano le porte e gli indirizzi IP di origine in base al traffico effettivo e all'intelligence sulle minacce.

- [Come configurare la protezione DDoS](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)

- [Come distribuire Firewall di AzureHow to deploy Azure Firewall](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

- [Informazioni su Azure Security Center Integrated Threat Intelligence](https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer)

- [Informazioni sulla protezione avanzata della rete adattiva del Centro sicurezza di AzureUnderstand Azure Security Center Adaptive Network Hardening](https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening)

- [Comprendere il controllo di accesso alla rete del Centro sicurezza di Azure just-in-time](https://docs.microsoft.com/azure/security-center/security-center-just-in-time)

## <a name="15-record-network-packets"></a>1.5: Registrare i pacchetti di rete

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 1.5 | 12.5 | Customer |

Abilitare l'acquisizione di pacchetti di Network Watcher per analizzare le attività anomale.

- [Come abilitare Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-create)

## <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Distribuire sistemi di rilevamento/prevenzione delle intrusioni basati sulla rete (IDS/IPS)

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 1.6 | 12.6, 12.7 | Customer |

Selezionare un'offerta da Azure Marketplace che supporti la funzionalità IDS/IPS con funzionalità di ispezione del payload.  Se non è un requisito il rilevamento e/o la prevenzione delle intrusioni basati sull'ispezione del payload, è possibile usare Firewall di Azure con Threat Intelligence.If intrusion detection and/or prevention based on payload inspection is not a requirement, Azure Firewall with Threat Intelligence can be used. Il filtro basato su Azure Firewall Threat Intelligence può avvisare e negare il traffico da e verso indirizzi IP e domini dannosi noti. Gli indirizzi IP e i domini sono originati dal feed Intelligence sulle minacce Microsoft.

Distribuire la soluzione firewall scelta a ciascuno dei limiti di rete dell'organizzazione per rilevare e/o negare traffico dannoso.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [Come distribuire Firewall di AzureHow to deploy Azure Firewall](https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal)

- [Come configurare gli avvisi con Firewall di AzureHow to configure alerts with Azure Firewall](https://docs.microsoft.com/azure/firewall/threat-intel)

## <a name="17-manage-traffic-to-web-applications"></a>1.7: Gestire il traffico verso le applicazioni web

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 1.7 | 12.9, 12.10 | Customer |

Distribuire il gateway applicazione di Azure per le applicazioni Web con HTTPS/SSL abilitato per i certificati attendibili.

- [Come distribuire il gateway applicazioneHow to deploy Application Gateway](https://docs.microsoft.com/azure/application-gateway/quick-create-portal)

- [Come configurare il gateway applicazione per l'utilizzo di HTTPSHow to configure Application Gateway to use HTTPS](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal)

- [Informazioni sul bilanciamento del carico di livello 7 con i gateway applicazione Web di AzureUnderstand livello 7 load balancing with Azure web application gateways](https://docs.microsoft.com/azure/application-gateway/overview)

## <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: ridurre al minimo la complessità e l'overhead amministrativo delle regole di sicurezza di rete

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 1.8 | 1.5 | Customer |

Usare i tag del servizio di rete virtuale per definire i controlli di accesso alla rete nei gruppi di sicurezza di rete o nel firewall di Azure.Use Virtual Network Service Tags to define network access controls on Network Security Groups or Azure Firewall. È possibile usare tag di servizio invece di indirizzi IP specifici nella creazione di regole di sicurezza. Specificando il nome del tag del servizio (ad esempio, ApiManagement) nel campo di origine o di destinazione appropriato di una regola, è possibile consentire o negare il traffico per il servizio corrispondente. Microsoft gestisce i prefissi degli indirizzi inclusi nel tag del servizio e aggiorna automaticamente il tag del servizio quando gli indirizzi cambiano.

È inoltre possibile utilizzare i gruppi di sicurezza delle applicazioni per semplificare la configurazione di sicurezza complessa. I gruppi di sicurezza delle applicazioni consentono di configurare la sicurezza di rete come un'estensione naturale della struttura di un'applicazione, raggruppando le macchine virtuali e definendo i criteri di sicurezza di rete in base a tali gruppi.

- [Comprendere e utilizzare i tag di servizio](https://docs.microsoft.com/azure/virtual-network/service-tags-overview)

- [Comprendere e utilizzare i gruppi di sicurezza delle applicazioniUnderstand and use Application Security Groups](https://docs.microsoft.com/azure/virtual-network/security-overview#application-security-groups)

## <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Gestire le configurazioni di sicurezza standard per i dispositivi di rete

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 1.9 | 11.1 | Customer |

Definire e implementare configurazioni di sicurezza standard per le risorse di rete con Criteri di Azure.Define and implement standard security configurations for network resources with Azure Policy.

È anche possibile usare Blueprint di Azure per semplificare le distribuzioni di Azure su larga scala creando una creazione di pacchetti di elementi dell'ambiente chiave, ad esempio modelli di Azure Resources Manager, controlli RBAC e criteri, in una singola definizione del blueprint. È possibile applicare il blueprint alle nuove sottoscrizioni e ottimizzare il controllo e la gestione tramite il controllo delle versioni.

- [Come configurare e gestire Criteri di AzureHow to configure and manage Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Esempi di criteri di Azure per la reteAzure Policy samples for networking](https://docs.microsoft.com/azure/governance/policy/samples/#network)

- [Come creare un blueprint di AzureHow to create an Azure Blueprint](https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal)

## <a name="110-document-traffic-configuration-rules"></a>1.10: Regole di configurazione del traffico dei documenti

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 1,10 | 11.2 | Customer |

Usare i tag per i gruppi di sicurezza di rete e altre risorse correlate alla sicurezza di rete e al flusso di traffico. Per le singole regole del gruppo di sicurezza di rete, utilizzare il campo "Descrizione" per specificare le esigenze e/o la durata dell'azienda (ecc.) per tutte le regole che consentono il traffico da/verso una rete.

Usare una delle definizioni di criteri di Azure predefinite correlate all'assegnazione di tag, ad esempio "Richiedi tag e relativo valore" per assicurarsi che tutte le risorse vengano create con i tag e per notificare le risorse senza tag esistenti.

È possibile usare Azure PowerShell o l'interfaccia della riga di comando di Azure per cercare o eseguire azioni sulle risorse in base ai relativi tag.

- [Come creare e utilizzare i tag](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags)

- [Come creare una rete virtuale](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

- [Come creare un gruppo di sicurezza di base con una configurazione di sicurezzaHow to create an NSG with a Security Config](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic)

## <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Utilizzare strumenti automatizzati per monitorare le configurazioni delle risorse di rete e rilevare le modifiche

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 1.11 | 11.3 | Customer |

Usare il log attività di Azure per monitorare le configurazioni delle risorse e rilevare le modifiche alle risorse di Azure.Use Azure Activity Log to monitor resource configurations and detect changes to your Azure resources. Creare avvisi all'interno di Monitoraggio di Azure che verranno attivati quando vengono apportate modifiche alle risorse critiche.

- [Come visualizzare e recuperare gli eventi del log attività di AzureHow to view and retrieve Azure Activity Log events](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-view)

- [Come creare avvisi in Monitoraggio di AzureHow to create alerts in Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log)

## <a name="next-steps"></a>Passaggi successivi

- Vedere il prossimo controllo di sicurezza: [registrazione e monitoraggio](security-control-logging-monitoring.md)
