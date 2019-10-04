---
title: Monitoraggio di Azure per i provider di servizi | Microsoft Docs
description: Monitoraggio di Azure può aiutare i provider di servizi gestiti (MSPs), le aziende di grandi dimensioni, i fornitori di software indipendenti (ISV) e i provider di servizi di hosting a gestire e monitorare i server nell'infrastruttura locale o cloud del cliente.
services: log-analytics
documentationcenter: ''
author: MeirMen
manager: jochan
editor: ''
ms.assetid: c07f0b9f-ec37-480d-91ec-d9bcf6786464
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: meirm
ms.openlocfilehash: 971757a4778dd50be486bead0c50fd6b3a25002e
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68839268"
---
# <a name="azure-monitor-for-service-providers"></a>Monitoraggio di Azure per i provider di servizi
Log Analytics le aree di lavoro in monitoraggio di Azure possono aiutare i provider di servizi gestiti (MSPs), le grandi aziende, i fornitori di software indipendenti (ISV) e i provider di servizi di hosting a gestire e monitorare i server nell'infrastruttura locale o cloud del cliente. 

Le aziende di grandi dimensioni hanno molti punti in comune con i provider di servizi, soprattutto quando c'è un team IT centralizzato che si occupa della gestione dell'IT per molte business unit diverse tra loro. Per semplicità, in questo documento si usa il termine *provider di servizi* ma la stessa funzionalità è disponibile anche per le aziende e gli altri clienti.

Per i partner e i provider di servizi che fanno parte del programma [Cloud Solution Provider (CSP)](https://partner.microsoft.com/Solutions/cloud-reseller-overview) , log Analytics in monitoraggio di Azure è uno dei servizi di Azure disponibili nelle sottoscrizioni di [Azure CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview). 

## <a name="architectures-for-service-providers"></a>Architetture per i provider di servizi

Log Analytics aree di lavoro forniscono un metodo che consente all'amministratore di controllare il flusso e l'isolamento dei dati di [log](data-platform-logs.md) e di creare un'architettura che soddisfa le esigenze aziendali specifiche. [Questo articolo](design-logs-deployment.md) illustra le considerazioni relative alla progettazione, alla distribuzione e alla migrazione per un'area di lavoro e l'articolo [gestire l'accesso](manage-access.md) illustra come applicare e gestire le autorizzazioni per i dati di log. Per i provider di servizi possono essere presenti considerazioni aggiuntive.

Esistono tre possibili architetture relative alle aree di lavoro di Log Analytics:

### <a name="1-distributed---logs-are-stored-in-workspaces-located-in-the-customers-tenant"></a>1. Distribuita - I log vengono archiviati nelle aree di lavoro che si trovano nel tenant del cliente 

In questa architettura l'area di lavoro viene distribuita nel tenant del cliente usato per tutti i log del cliente stesso.In questa architettura l'area di lavoro viene distribuita nel tenant del cliente usato per tutti i log del cliente stesso. Agli amministratori del provider di servizi viene concesso l'accesso all'area di lavoro tramite le autorizzazioni di [utenti guest di Azure Active Directory (B2B)](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b). Per accedere a tali aree di lavoro, l'amministratore del provider di servizi deve passare alla directory del cliente nel portale di Azure.

I vantaggi di questa architettura sono i seguenti:
* Il cliente può gestire l'accesso ai log tramite il proprio [accesso basato sui ruoli](https://docs.microsoft.com/azure/role-based-access-control/overview).
* Ogni cliente può definire impostazioni diverse per la propria area di lavoro, ad esempio in termini di conservazione e di limitazione sull'uso dei dati.
* Isolamento tra i clienti in termini di normative e conformità.
* Il costo per ogni area di lavoro viene addebitato nella sottoscrizione del cliente.
* I log possono essere raccolti da tutti i tipi di risorse, non solo in base all'agente. Ad esempio, log di controllo di Azure.

Gli svantaggi di questa architettura sono i seguenti:
* È più difficile per il provider di servizi gestire contemporaneamente un numero elevato di tenant dei clienti.
* Gli amministratori del provider di servizi devono eseguire il provisioning per la directory dei clienti.
* Il provider di servizi non può analizzare i dati dei clienti.

### <a name="2-central---logs-are-stored-in-a-workspace-located-in-the-service-provider-tenant"></a>2. Centrale - I log vengono archiviati nell'area di lavoro che si trova nel tenant del provider di servizi

In questa architettura i log non vengono archiviati nel tenant del cliente, ma solo in una posizione centrale nell'ambito di una sottoscrizione del provider di servizi. Gli agenti installati su macchine virtuali del cliente sono configurati per inviare i log all'area di lavoro tramite l'ID e la chiave privata dell'area di lavoro.

I vantaggi di questa architettura sono i seguenti:
* Facilità di gestione di un numero elevato di clienti e di integrarli in diversi sistemi back-end.

* Il provider di servizi dispone della proprietà completa dei log e dei diversi elementi, ad esempio funzioni e query salvate.

* Il provider di servizio può eseguire funzioni di analisi su tutti i propri clienti.

Gli svantaggi di questa architettura sono i seguenti:
* Questa architettura è applicabile solo ai dati di macchine virtuale basati su agenti e non può essere usata con origini dati PaaS, SaaS e dell'infrastruttura di Microsoft Azure.

* Potrebbe essere difficile separare i dati tra i clienti quando sono uniti in una singola area di lavoro. L'unico metodo per eseguire questa operazione è quello di usare il nome di dominio completo del computer (FQDN) o l'ID sottoscrizione di Azure. 

* Tutti i dati di tutti i clienti verranno archiviati nella stessa area con un'unica fattura e con le stesse impostazioni di conservazione e di configurazione.

* Per i servizi PaaS e di infrastruttura di Microsoft Azure, ad esempio Diagnostica di Azure e log di controllo di Azure, l'area di lavoro deve essere nello stesso tenant della risorsa in modo che i logo non vengano inviati all'area di lavoro centrale.

* Tutti gli agenti di macchine virtuali di tutti i clienti vengono autenticati nell'area di lavoro centrale usando lo stesso ID e la stessa chiave dell'area di lavoro. Non esiste alcun metodo per bloccare i log di un cliente specifico senza interrompere l'attività di altri clienti.

### <a name="3-hybrid---logs-are-stored-in-workspace-located-in-the-customers-tenant-and-some-of-them-are-pulled-to-a-central-location"></a>3. Ibrida - I log vengono archiviati nell'area di lavoro che si trova nel tenant del cliente e per alcuni viene eseguito il pull in una posizione centrale.

La terza architettura è una combinazione delle due opzioni. Tale architettura si basa su quella distribuita in cui i log sono locali per ogni cliente, usando tuttavia un meccanismo per creare un repository centrale di log. Per una parte dei log viene eseguito il pull in una posizione centrale per la creazione di report e analitica. Questa parte può essere costituita da un numero ridotto di tipi di dati o da un riepilogo dell'attività, ad esempio statistiche giornaliere.

Sono disponibili due opzioni per implementare i log in una posizione centrale:

1. Area di lavoro centrale: il provider di servizi può creare un'area di lavoro nel proprio tenant e usare uno script che usi l'[API di query](https://dev.loganalytics.io/) con l'[API di raccolta dati](../../azure-monitor/platform/data-collector-api.md) per spostare i dati dalle diverse aree di lavoro nella posizione centrale. Un'alternativa all'uso degli script consiste nell'usare le [app per la logica di Azure](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview).

2. Power BI come posizione centrale: Power BI può fungere da posizione centralizzata quando le varie aree di lavoro esportano i dati utilizzando l'integrazione tra l'area di lavoro Log Analytics e [Power bi](../../azure-monitor/platform/powerbi.md). 

## <a name="next-steps"></a>Passaggi successivi

* Automatizzare la creazione e la configurazione delle aree di lavoro usando i [modelli di Resource Manager](template-workspace-configuration.md)

* Automatizzare la creazione delle aree di lavoro usando [PowerShell](../../azure-monitor/platform/powershell-workspace-configuration.md) 

* Usare la funzione [Avvisi](../../azure-monitor/platform/alerts-overview.md) per l'integrazione con i sistemi esistenti

* Generare report di riepilogo usando [Power BI](../../azure-monitor/platform/powerbi.md)

* Esaminare il processo di [configurazione di Log Analytics e Power BI per monitorare più clienti CSP](https://docs.microsoft.com/azure/cloud-solution-provider/support/monitor-multiple-customers)
