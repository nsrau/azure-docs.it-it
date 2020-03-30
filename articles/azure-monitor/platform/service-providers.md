---
title: Registri di Monitoraggio di Azure per i provider di servizi . Documenti Microsoft
description: I log di monitoraggio di Azure possono aiutare Managed Service Provider (MSP), grandi aziende, fornitori di software indipendenti (ISV) e provider di servizi di hosting a gestire e monitorare i server nell'infrastruttura cloud o locale del cliente.
ms.subservice: logs
ms.topic: conceptual
author: MeirMen
ms.author: meirm
ms.date: 02/03/2020
ms.openlocfilehash: ed398e12ee90f2eef2cfa78e2ed02701e6012517
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658881"
---
# <a name="azure-monitor-logs-for-service-providers"></a>Azure Monitor Logs for Service Providers

Le aree di lavoro di Log Analytics in Monitoraggio di Azure consentono di gestire i provider di servizi gestiti (MSP), le grandi aziende, i fornitori di software indipendenti (ISV) e i provider di servizi di hosting a gestire e monitorare i server nell'infrastruttura cloud o locale del cliente.

Le aziende di grandi dimensioni hanno molti punti in comune con i provider di servizi, soprattutto quando c'è un team IT centralizzato che si occupa della gestione dell'IT per molte business unit diverse tra loro. Per semplicità, in questo documento si usa il termine *provider di servizi* ma la stessa funzionalità è disponibile anche per le aziende e gli altri clienti.

Per i partner e i provider di servizi che fanno parte del programma [Cloud Solution Provider (CSP),](https://partner.microsoft.com/Solutions/cloud-reseller-overview) Log Analytics in Monitoraggio di Azure è uno dei servizi di Azure disponibili nelle sottoscrizioni di Azure CSP.

Log Analytics in Monitoraggio di Azure può essere usato anche da un provider di servizi che gestisce le risorse dei clienti tramite la funzionalità di gestione delle risorse delegate di Azure in [Azure Lighthouse.](https://docs.microsoft.com/azure/lighthouse/overview)

## <a name="architectures-for-service-providers"></a>Architetture per i provider di servizi

Le aree di lavoro di Log Analytics forniscono all'amministratore un metodo per controllare il flusso e l'isolamento dei dati di [log](data-platform-logs.md) e creare un'architettura che risponda alle specifiche esigenze aziendali. [In questo articolo](design-logs-deployment.md) vengono illustrate le considerazioni relative alla progettazione, alla distribuzione e alla migrazione per un'area di lavoro e l'articolo sulla gestione dell'accesso illustra come applicare e gestire le autorizzazioni per registrare i dati. [manage access](manage-access.md) Per i provider di servizi possono essere presenti considerazioni aggiuntive.

Esistono tre possibili architetture relative alle aree di lavoro di Log Analytics:

### <a name="1-distributed---logs-are-stored-in-workspaces-located-in-the-customers-tenant"></a>1. Distribuito: i log vengono archiviati in aree di lavoro situate nel tenant del cliente

In questa architettura l'area di lavoro viene distribuita nel tenant del cliente usato per tutti i log del cliente stesso.In questa architettura l'area di lavoro viene distribuita nel tenant del cliente usato per tutti i log del cliente stesso.

Gli amministratori del provider di servizi possono accedere a un'area di lavoro di Log Analytics in un tenant del cliente:There are two ways that service provider administrators can gain access to a Log Analytics workspace in a customer tenant:

- Un cliente può aggiungere singoli utenti dal provider di servizi come utenti guest di [Azure Active Directory (B2B).](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b) Gli amministratori del provider di servizi dovranno accedere alla directory di ogni cliente nel portale di Azure per poter accedere a queste aree di lavoro. Ciò richiede inoltre che i clienti gestiscano l'accesso individuale per ogni amministratore del provider di servizi.
- Per una maggiore scalabilità e flessibilità, i provider di servizi possono usare la funzionalità di [gestione delle risorse delegate](https://docs.microsoft.com/azure/lighthouse/concepts/azure-delegated-resource-management) di Azure di Azure [Lighthouse](https://docs.microsoft.com/azure/lighthouse/overview) per accedere al tenant del cliente. Con questo metodo, gli amministratori del provider di servizi sono inclusi in un gruppo di utenti di Azure AD nel tenant del provider di servizi e a questo gruppo viene concesso l'accesso durante il processo di onboarding per ogni cliente. Questi amministratori possono quindi accedere alle aree di lavoro di ogni cliente dall'interno del proprio tenant del provider di servizi, anziché dover accedere al tenant di ogni cliente singolarmente. L'accesso alle risorse delle aree di lavoro di Log Analytics dei clienti in questo modo riduce il lavoro richiesto sul lato cliente e può semplificare la raccolta e l'analisi dei dati tra più clienti gestiti dallo stesso provider di servizi tramite strumenti come le cartelle di lavoro di Monitoraggio di [Azure.](https://docs.microsoft.com/azure//azure-monitor/platform/workbooks-overview) Per altre info, vedi [Monitorare le risorse dei clienti su larga scala.](https://docs.microsoft.com/azure/lighthouse/how-to/monitor-at-scale)

I vantaggi dell'architettura distribuita sono:

* Il cliente può confermare livelli specifici di autorizzazioni tramite la gestione delle risorse delegate di Azure oppure gestire l'accesso [ai](https://docs.microsoft.com/azure/lighthouse/concepts/azure-delegated-resource-management)log usando il proprio accesso [basato sui ruoli](https://docs.microsoft.com/azure/role-based-access-control/overview).
* I log possono essere raccolti da tutti i tipi di risorse, non solo dai dati delle macchine virtuali basati su agenti. Ad esempio, log di controllo di Azure.
* Ogni cliente può definire impostazioni diverse per la propria area di lavoro, ad esempio in termini di conservazione e di limitazione sull'uso dei dati.
* Isolamento tra i clienti in termini di normative e conformità.
* Il costo per ogni area di lavoro viene addebitato nella sottoscrizione del cliente.

Gli svantaggi dell'architettura distribuita sono:

* La visualizzazione e l'analisi centralizzata dei dati tra i tenant dei clienti con strumenti quali Monitoraggio cartelle di lavoro di Azure possono causare un aumento delle esperienze, soprattutto quando si analizzano i dati in più di 50 aree di lavoro.
* Se i clienti non sono onboardati per la gestione delle risorse delegate di Azure, è necessario eseguire il provisioning degli amministratori del provider di servizi nella directory dei clienti ed è più difficile per il provider di servizi gestire un numero elevato di tenant dei clienti contemporaneamente.

### <a name="2-central---logs-are-stored-in-a-workspace-located-in-the-service-provider-tenant"></a>2. Centrale: i registri vengono archiviati in un'area di lavoro situata nel tenant del provider di servizi

In questa architettura i log non vengono archiviati nel tenant del cliente, ma solo in una posizione centrale nell'ambito di una sottoscrizione del provider di servizi. Gli agenti installati su macchine virtuali del cliente sono configurati per inviare i log all'area di lavoro tramite l'ID e la chiave privata dell'area di lavoro.

I vantaggi dell'architettura centralizzata sono:

* Facilità di gestione di un numero elevato di clienti e di integrarli in diversi sistemi back-end.
* Il provider di servizi dispone della proprietà completa dei log e dei diversi elementi, ad esempio funzioni e query salvate.
* Il provider di servizio può eseguire funzioni di analisi su tutti i propri clienti.

Gli svantaggi dell'architettura centralizzata sono:

* Questa architettura è applicabile solo ai dati di macchine virtuale basati su agenti e non può essere usata con origini dati PaaS, SaaS e dell'infrastruttura di Microsoft Azure.
* Potrebbe essere difficile separare i dati tra i clienti quando sono uniti in una singola area di lavoro. L'unico metodo per eseguire questa operazione è quello di usare il nome di dominio completo del computer (FQDN) o l'ID sottoscrizione di Azure. 
* Tutti i dati di tutti i clienti verranno archiviati nella stessa area con un'unica fattura e con le stesse impostazioni di conservazione e di configurazione.
* Per i servizi PaaS e di infrastruttura di Microsoft Azure, ad esempio Diagnostica di Azure e log di controllo di Azure, l'area di lavoro deve essere nello stesso tenant della risorsa in modo che i logo non vengano inviati all'area di lavoro centrale.
* Tutti gli agenti di macchine virtuali di tutti i clienti vengono autenticati nell'area di lavoro centrale usando lo stesso ID e la stessa chiave dell'area di lavoro. Non esiste alcun metodo per bloccare i log di un cliente specifico senza interrompere l'attività di altri clienti.

### <a name="3-hybrid---logs-are-stored-in-workspace-located-in-the-customers-tenant-and-some-of-them-are-pulled-to-a-central-location"></a>3. Ibrido: i log vengono archiviati nell'area di lavoro situata nel tenant del cliente e alcuni di essi vengono estratti in una posizione centrale.

La terza architettura è una combinazione delle due opzioni. Tale architettura si basa su quella distribuita in cui i log sono locali per ogni cliente, usando tuttavia un meccanismo per creare un repository centrale di log. Per una parte dei log viene eseguito il pull in una posizione centrale per la creazione di report e analitica. Questa parte può essere costituita da un numero ridotto di tipi di dati o da un riepilogo dell'attività, ad esempio statistiche giornaliere.

Sono disponibili due opzioni per implementare i log in una posizione centrale:There are two options to implement logs in a central location:

1. Area di lavoro centrale: il provider di servizi può creare un'area di lavoro nel proprio tenant e usare uno script che usi l'[API di query](https://dev.loganalytics.io/) con l'[API di raccolta dati](../../azure-monitor/platform/data-collector-api.md) per spostare i dati dalle diverse aree di lavoro nella posizione centrale. Un'alternativa all'uso degli script consiste nell'usare le [app per la logica di Azure](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview).

2. Power BI come posizione centrale: Power BI può fungere da posizione centrale quando le varie aree di lavoro esportano i dati in essa tramite l'integrazione tra l'area di lavoro di Log Analytics e [Power BI.](../../azure-monitor/platform/powerbi.md) 

## <a name="next-steps"></a>Passaggi successivi

* Automatizzare la creazione e la configurazione delle aree di lavoro usando i [modelli di Resource Manager](template-workspace-configuration.md)

* Automatizzare la creazione delle aree di lavoro usando [PowerShell](../../azure-monitor/platform/powershell-workspace-configuration.md) 

* Usare la funzione [Avvisi](../../azure-monitor/platform/alerts-overview.md) per l'integrazione con i sistemi esistenti

* Generare report di riepilogo usando [Power BI](../../azure-monitor/platform/powerbi.md)

* Clienti in scheda di amministrazione per la gestione delle risorse delegate di [Azure.](https://docs.microsoft.com/azure/lighthouse/concepts/azure-delegated-resource-management)
