---
title: Protezione dalle minacce aggiuntiva nel centro sicurezza di Azure
description: Informazioni sulla protezione dalle minacce disponibile nel centro sicurezza di Azure oltre ad Azure Defender
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 33c45447-3181-4b75-aa8e-c517e76cd50d
ms.service: security-center
ms.topic: conceptual
ms.date: 09/15/2020
ms.author: memildin
ms.openlocfilehash: 0f4a849af2be9f02187dc3cda526c9c4727cab1b
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90938655"
---
# <a name="additional-threat-protections-in-azure-security-center"></a>Protezioni aggiuntive per le minacce nel centro sicurezza di Azure
Oltre alle protezioni predefinite di [Azure Defender](azure-defender.md), il Centro sicurezza di Azure offre anche le funzionalità di protezione dalle minacce seguenti.

> [!TIP]
> Per abilitare le funzionalità di protezione dalle minacce del Centro sicurezza, è necessario abilitare Azure Defender nella sottoscrizione che contiene i carichi di lavoro applicabili.
>
> È possibile abilitare la protezione dalle minacce per il **database di Azure per MariaDB/MySQL/PostgreSQL** solo a livello di risorsa.


## <a name="threat-protection-for-azure-network-layer"></a>Protezione dalle minacce per il livello di rete di Azure <a name="network-layer"></a>
L'analisi a livello di rete del Centro sicurezza si basa su [dati IPFIX](https://en.wikipedia.org/wiki/IP_Flow_Information_Export), di esempio, intestazioni di pacchetti raccolte dai router core di Azure. In base a questo feed di dati, il Centro sicurezza usa modelli di Machine Learning per identificare e contrassegnare le attività di traffico dannose. Il Centro sicurezza usa anche il database di intelligence sulle minacce di Microsoft per arricchire gli indirizzi IP.

Alcune configurazioni di rete potrebbero impedire al Centro sicurezza di generare avvisi sulle attività di rete sospette. Per consentire al Centro sicurezza di generare avvisi di rete, verificare che:
- La macchina virtuale abbia un indirizzo IP pubblico o si trovi in un servizio di bilanciamento del carico con un indirizzo IP pubblico.
- Il traffico di rete in uscita della macchina virtuale non sia bloccato da una soluzione IDS esterna.
- Alla macchina virtuale fosse assegnato lo stesso indirizzo IP per l'intera ora in cui si sono verificate le comunicazioni sospette. Questo vale anche per le macchine virtuali create come parte di un servizio gestito, ad esempio servizio Azure Kubernetes o Databricks.

Per un elenco degli avvisi a livello di rete di Azure, vedere la [tabella di riferimento degli avvisi](alerts-reference.md#alerts-azurenetlayer).


## <a name="threat-protection-for-azure-resource-manager-preview"></a>Protezione dalle minacce per Azure Resource Manager (anteprima)<a name ="management-layer"></a>
Il livello di protezione del Centro sicurezza basato su Azure Resource Manager è attualmente in fase di anteprima.

Il Centro sicurezza offre un ulteriore livello di protezione usando gli eventi di Azure Resource Manager, che è considerato il piano di controllo per Azure. Analizzando i record di Azure Resource Manager, il Centro sicurezza rileva le operazioni insolite o potenzialmente dannose nell'ambiente della sottoscrizione di Azure.

Per un elenco degli avvisi di Azure Resource Manager (anteprima), vedere la [tabella di riferimento degli avvisi](alerts-reference.md#alerts-azureresourceman).


>[!NOTE]
> Alcune delle analisi precedenti sono basate su Microsoft Cloud App Security. Per usufruire di queste analisi è necessario attivare una licenza di Cloud App Security. Se si ha una licenza di Cloud App Security, questi avvisi sono abilitati per impostazione predefinita. Per disabilitare gli avvisi:
>
> 1. Scegliere **Prezzi e impostazioni** dal menu del Centro sicurezza.
> 1. Selezionare la sottoscrizione da modificare.
> 1. Selezionare **Rilevamento delle minacce**.
> 1. Deselezionare **consenti Microsoft cloud app Security per accedere ai dati**e selezionare **Salva**.


>[!NOTE]
>Il Centro sicurezza archivia i dati dei clienti correlati alla sicurezza nella stessa area geografica della risorsa. Se Microsoft non ha ancora distribuito il Centro sicurezza nell'area geografica della risorsa, archivia i dati nel Stati Uniti. Quando è abilitato Cloud App Security, queste informazioni vengono archiviate in base alle regole relative alla posizione geografica di Cloud App Security. Per altre informazioni, vedere [Data storage for non-regional services](https://azuredatacentermap.azurewebsites.net/) (Archiviazione dei dati per i servizi non a livello di area).

1. Impostare l'area di lavoro in cui si sta installando l'agente. Assicurarsi che l'area di lavoro si trovi nella stessa sottoscrizione che si usa nel Centro sicurezza e di disporre delle autorizzazioni di lettura/scrittura nell'area di lavoro.

1. Abilitare **Azure Defender**e selezionare **Salva**.


## <a name="threat-protection-for-azure-cosmos-db-preview"></a>Protezione dalle minacce per Azure Cosmos DB (anteprima)<a name="cosmos-db"></a>

Gli avvisi di Azure Cosmos DB vengono generati da tentativi insoliti e potenzialmente dannosi di accesso o exploit degli account Azure Cosmos DB.

Per altre informazioni, vedere:

* [Advanced Threat Protection per Azure Cosmos DB (anteprima)](../cosmos-db/cosmos-db-advanced-threat-protection.md)
* [L'elenco di avvisi di protezione dalle minacce per Azure Cosmos DB (anteprima)](alerts-reference.md#alerts-azurecosmos)



## <a name="threat-protection-for-other-microsoft-services"></a>Protezione dalle minacce per altri servizi Microsoft <a name="alerts-other"></a>

### <a name="threat-protection-for-azure-waf"></a>Protezione dalle minacce per il WAF di Azure <a name="azure-waf"></a>

Il gateway applicazione di Azure offre un web application firewall (WAF) che garantisce alle applicazioni Web una protezione centralizzata da exploit e vulnerabilità comuni.

Le applicazioni Web sono sempre più vittime di attacchi che sfruttano le più comuni e note vulnerabilità. Il WAF del gateway applicazione basa sullo standard CRS (Core Rule Set) 3.0 o 2.2.9 dell'OWASP (Open Web Application Security Project). Il WAF viene aggiornato automaticamente per offrire protezione dalle nuove vulnerabilità. 

Se si ha una licenza per il WAF di Azure, gli avvisi WAF vengono trasmessi al Centro sicurezza senza che sia necessaria alcuna configurazione aggiuntiva. Per altre informazioni sugli avvisi generati da WAF, vedere [Regole e gruppi di regole CRS di Web Application Firewall](../web-application-firewall/ag/application-gateway-crs-rulegroups-rules.md?tabs=owasp31#crs911-31).


### <a name="threat-protection-for-azure-ddos-protection"></a>Protezione dalle minacce per Protezione DDoS di Azure <a name="azure-ddos"></a>

Gli attacchi Distributed Denial of Service (DDoS) sono notoriamente facili da eseguire. Sono diventati un grosso problema di sicurezza, soprattutto se si stanno trasferendo le applicazioni nel cloud. 

Un attacco DDoS tenta di esaurire le risorse di un'applicazione, che quindi non risulta più disponibile per gli utenti legittimi. Gli attacchi DDoS possono avere come obiettivo qualsiasi endpoint raggiungibile tramite Internet.

Per difendersi dagli attacchi DDoS, acquistare una licenza per Protezione DDoS di Azure e attenersi alle procedure consigliate per la progettazione delle applicazioni. Protezione DDoS offre diversi livelli di servizio. Per altre informazioni, vedere [Panoramica di Protezione DDoS di Azure Standard](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview).

Per un elenco degli avvisi di Protezione DDoS di Azure, vedere la [tabella di riferimento degli avvisi](alerts-reference.md#alerts-azureddos).


## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sugli avvisi di sicurezza generati da queste funzionalità di protezione dalle minacce, vedere gli articoli seguenti:

* [Tabella di riferimento per tutti gli avvisi del Centro sicurezza di Azure](alerts-reference.md)
* [Avvisi di sicurezza nel Centro sicurezza di Azure](security-center-alerts-overview.md)
* [Gestione e risposta agli avvisi di sicurezza nel Centro sicurezza di Azure](security-center-managing-and-responding-alerts.md)
* [Esportare avvisi e raccomandazioni sulla sicurezza (anteprima)](continuous-export.md)