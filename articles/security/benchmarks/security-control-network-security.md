---
title: Azure Security Control - Network Security
description: Sicurezza di sicurezza Sicurezza di Rete
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/30/2019
ms.author: mbaldwin
ms.custom: security-recommendations
ms.openlocfilehash: 7916bbb28602d64e0916fce7badf16a65c242227
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77251873"
---
# <a name="security-control-network-security"></a>Controllo di sicurezza: Sicurezza di rete

I consigli per la sicurezza di rete si concentrano sulla specifica dei protocolli di rete, delle porte TCP/UDP e dei servizi connessi alla rete per l'accesso ai servizi di Azure.Network security recommendations focus on specifying which network protocols, TCP/UDP ports, and network connected services are allowed or denied access to Azure services.

## <a name="11-protect-resources-using-network-security-groups-or-azure-firewall-on-your-virtual-network"></a>1.1: Proteggere le risorse usando i gruppi di sicurezza di rete o il firewall di Azure nella rete virtuale1.1: Protect resources using Network Security Groups or Azure Firewall on your Virtual Network

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 1.1 | 9.2, 9.4, 14.1-14.3 | Customer |

Assicurarsi che a tutte le distribuzioni di subnet di rete virtuale sia applicato un gruppo di sicurezza di rete con controlli di accesso di rete specifici per le porte e le origini attendibili dell'applicazione. Usare Servizi di Azure con Private Link abilitato, distribuire il servizio all'interno della rete virtuale o connettersi privatamente usando endpoint privati. Per i requisiti specifici del servizio, fare riferimento alla raccomandazione di sicurezza per tale servizio specifico.

In alternativa, se si dispone di un caso d'uso specifico, i requisiti possono essere soddisfatti implementando Firewall di Azure.Alternatively, if you have a specific use case, requirements can be met by implementing Azure Firewall.

Informazioni generali su Private Link:

https://docs.microsoft.com/azure/private-link/private-link-overview

Come creare una rete virtuale:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Come creare un gruppo di sicurezza di rete con una configurazione di sicurezza:How to create an NSG with a security configuration:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

Come distribuire e configurare Firewall di Azure:How to deploy and configure Azure Firewall:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

## <a name="12-monitor-and-log-the-configuration-and-traffic-of-vnets-subnets-and-nics"></a>1.2: Monitorare e registrare la configurazione e il traffico di reti virtuali, subnet e schede di interfaccia di rete

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 1.2 | 9.3, 12.2 | Customer |

Usare il Centro sicurezza di Azure e seguire i consigli di protezione della rete per proteggere le risorse di rete in Azure.Use Azure Security Center and follow network protection recommendations to help secure your network resources in Azure. Abilitare i log di flusso del gruppo di sicurezza di rete e inviare i log in un account di archiviazione per il controllo del traffico.

Abilitazione dei registri di flusso del gruppo di sicurezza di rete:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Informazioni sulla sicurezza di rete fornita dal Centro sicurezza di Azure:Understand Network Security provided by Azure Security Center:

https://docs.microsoft.com/azure/security-center/security-center-network-recommendations

## <a name="13-protect-critical-web-applications"></a>1.3: Proteggere le applicazioni Web critiche

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 1.3 | 9,5 | Customer |

Distribuire Firewall applicazione Web di Azure davanti alle applicazioni Web critiche per un'ulteriore ispezione del traffico in ingresso. Abilitare l'impostazione di diagnostica per WAF e inserire i log in un account di archiviazione, un hub eventi o un'area di lavoro di Log Analytics.Enable Diagnostic Setting for WAF and ingest logs into a Storage Account, Event Hub, or Log Analytics Workspace.

Come distribuire Azure WAF:

https://docs.microsoft.com/azure/web-application-firewall/ag/create-waf-policy-ag

## <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Negare le comunicazioni con indirizzi IP dannosi noti

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 1.4 | 12.3 | Customer |

Abilitare la protezione DDoS Standard nelle reti virtuali di Azure per proteggersi dagli attacchi DDoS.Enable DDoS Standard protection on your Azure Virtual Networks to protection against DDoS attacks. Usare Azure Security Center Integrated Threat Intelligence per negare le comunicazioni con indirizzi IP dannosi noti.

Distribuire Firewall di Azure a ciascuno dei limiti di rete &quot;dell'organizzazione&quot; con Threat Intelligence abilitato e configurato per avvisare e negare il traffico di rete dannoso.

Usare l'accesso Just-InTime del Centro sicurezza di Azure per configurare i gruppi di sicurezza di rete per limitare l'esposizione degli endpoint agli indirizzi IP approvati per un periodo limitato.

Usare la protezione avanzata della rete adattiva del Centro sicurezza di Azure per consigliare configurazioni del gruppo di sicurezza di rete che limitano le porte e gli indirizzi IP di origine in base al traffico effettivo e all'intelligence sulle minacce.

Come configurare la protezione DDoS:

https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection

Come distribuire Firewall di Azure:How to deploy Azure Firewall:

https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Informazioni su Azure Security Center Integrated Threat Intelligence:

https://docs.microsoft.com/azure/security-center/security-center-alerts-service-layer

Informazioni sulla protezione avanzata della rete adattiva del Centro sicurezza di Azure:Understand Azure Security Center Adaptive Network Hardening:

https://docs.microsoft.com/azure/security-center/security-center-adaptive-network-hardening

Comprendere il Centro sicurezza di Azure Just In Time Network Access Control:Understand Azure Security Center Just In Time Network Access Control:

https://docs.microsoft.com/azure/security-center/security-center-just-in-time

## <a name="15-record-network-packets-and-flow-logs"></a>1.5: Registrare i pacchetti di rete e i registri di flusso

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 1.5 | 12.5, 15.8 | Customer |

Registrare i log di flusso del gruppo di sicurezza di rete in un account di archiviazione per generare i record di flusso. Se necessario per analizzare l'attività anomala, abilitare l'acquisizione di pacchetti di Network Watcher.If required for investigating anomalous activity, enable Network Watcher packet capture.

Abilitazione dei registri di flusso del gruppo di sicurezza di rete:

https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-portal

Come abilitare Network Watcher:

https://docs.microsoft.com/azure/network-watcher/network-watcher-create

## <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Distribuire sistemi di rilevamento/prevenzione delle intrusioni basati sulla rete (IDS/IPS)

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 1.6 | 12.6, 12.7 | Customer |

Distribuire Firewall di Azure a ciascuno dei limiti di rete &quot;dell'organizzazione&quot; con Threat Intelligence abilitato e configurato per avvisare e negare il traffico di rete dannoso.

Come distribuire Firewall di Azure:How to deploy Azure Firewall:https://docs.microsoft.com/azure/firewall/tutorial-firewall-deploy-portal

Come configurare gli avvisi con Firewall di Azure:How to configure alerts with Azure Firewall:https://docs.microsoft.com/azure/firewall/threat-intel

## <a name="17-manage-traffic-to-web-applications"></a>1.7: Gestire il traffico verso le applicazioni web

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 1.7 | 12.9, 12.10 | Customer |

Distribuire il gateway applicazione di Azure per le applicazioni Web con HTTPS/SSL abilitato per i certificati attendibili.

Come distribuire il gateway applicazione:

https://docs.microsoft.com/azure/application-gateway/quick-create-portal

Come configurare il gateway applicazione per l'utilizzo di HTTPS:

https://docs.microsoft.com/azure/application-gateway/create-ssl-portal

Informazioni sul bilanciamento del carico di livello 7 con i gateway applicazione Web di Azure:Understand layer 7 load balancing with Azure web application gateways:

https://docs.microsoft.com/azure/application-gateway/overview

## <a name="18-minimize-complexity-and-administrative-overhead-of-network-security-rules"></a>1.8: ridurre al minimo la complessità e l'overhead amministrativo delle regole di sicurezza di rete

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 1.8 | 1.5 | Customer |

Usare i tag del servizio di rete virtuale per definire i controlli di accesso alla rete nei gruppi di sicurezza di rete o nel firewall di Azure.Use Virtual Network Service Tags to define network access controls on Network Security Groups or Azure Firewall. È possibile usare tag di servizio invece di indirizzi IP specifici nella creazione di regole di sicurezza. Specificando il nome del tag del servizio (ad esempio, ApiManagement) nel campo di origine o di destinazione appropriato di una regola, è possibile consentire o negare il traffico per il servizio corrispondente. Microsoft gestisce i prefissi degli indirizzi inclusi nel tag del servizio e aggiorna automaticamente il tag del servizio quando gli indirizzi cambiano.

Comprendere e utilizzare i tag di servizio:

https://docs.microsoft.com/azure/virtual-network/service-tags-overview

## <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Gestire le configurazioni di sicurezza standard per i dispositivi di rete

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 1.9 | 11.1 | Customer |

Definire e implementare configurazioni di sicurezza standard per le risorse di rete con Criteri di Azure.Define and implement standard security configurations for network resources with Azure Policy.

È anche possibile usare Blueprint di Azure per semplificare le distribuzioni di Azure su larga scala creando una creazione di pacchetti di elementi dell'ambiente chiave, ad esempio modelli di Azure Resource Manager, controlli RBAC e criteri, in una singola definizione del blueprint. È possibile applicare il blueprint alle nuove sottoscrizioni e ottimizzare il controllo e la gestione tramite il controllo delle versioni.

Come configurare e gestire Criteri di Azure:How to configure and manage Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Esempi di criteri di Azure per la rete:Azure Policy samples for networking:

https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network

Come creare un blueprint di Azure:How to create an Azure Blueprint:

https://docs.microsoft.com/azure/governance/blueprints/create-blueprint-portal

## <a name="110-document-traffic-configuration-rules"></a>1.10: Regole di configurazione del traffico dei documenti

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 1.1 | 11.2 | Customer |

Usare i tag per i gruppi di sicurezza di rete e altre risorse correlate alla sicurezza di rete e al flusso di traffico. Per le singole regole &quot;del&quot; gruppo di sicurezza di rete, utilizzare il campo Descrizione per specificare le esigenze e/o la durata dell'azienda (ecc.) per tutte le regole che consentono il traffico da/verso una rete.

Come creare e utilizzare i tag:

https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags

Come creare una rete virtuale:

https://docs.microsoft.com/azure/virtual-network/quick-create-portal

Come creare un gruppo di sicurezza di base con una configurazione di sicurezza:How to create an NSG with a Security Config:

https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic

## <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Utilizzare strumenti automatizzati per monitorare le configurazioni delle risorse di rete e rilevare le modifiche

| ID AzureAzure ID | STATI di lavoro CIS | Responsabilità |
|--|--|--|
| 1.11 | 11.3 | Customer |

Usare Criteri di Azure per convalidare (e/o correggere) la configurazione per le risorse di rete.

Come configurare e gestire Criteri di Azure:How to configure and manage Azure Policy:

https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage

Esempi di criteri di Azure per la rete:Azure Policy samples for networking:

https://docs.microsoft.com/azure/governance/policy/samples/built-in-policies#network

## <a name="next-steps"></a>Passaggi successivi

- Vedere il controllo di sicurezza successivo: [registrazione e monitoraggioSee](security-control-logging-monitoring.md) the next security control: Logging and Monitoring
