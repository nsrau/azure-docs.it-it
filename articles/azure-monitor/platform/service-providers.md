---
title: Log di monitoraggio di Azure per i provider di servizi | Microsoft Docs
description: I log di monitoraggio di Azure possono aiutare i provider di servizi gestiti (MSPs), le grandi aziende, i fornitori di software indipendenti (ISV) e i provider di servizi di hosting a gestire e monitorare i server nell'infrastruttura locale o cloud del cliente.
ms.subservice: logs
ms.topic: conceptual
author: MeirMen
ms.author: meirm
ms.date: 02/03/2020
ms.openlocfilehash: 766fb9fbe50f8a138eae020082680204872a653a
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87315446"
---
# <a name="azure-monitor-logs-for-service-providers"></a>Log di monitoraggio di Azure per i provider di servizi

Log Analytics le aree di lavoro in monitoraggio di Azure possono aiutare i provider di servizi gestiti (MSPs), le grandi aziende, i fornitori di software indipendenti (ISV) e i provider di servizi di hosting a gestire e monitorare i server nell'infrastruttura locale o cloud del cliente.

Le aziende di grandi dimensioni hanno molti punti in comune con i provider di servizi, soprattutto quando c'è un team IT centralizzato che si occupa della gestione dell'IT per molte business unit diverse tra loro. Per semplicità, in questo documento si usa il termine *provider di servizi* ma la stessa funzionalità è disponibile anche per le aziende e gli altri clienti.

Per i partner e i provider di servizi che fanno parte del programma [Cloud Solution Provider (CSP)](https://partner.microsoft.com/membership/cloud-solution-provider) , log Analytics in monitoraggio di Azure è uno dei servizi di Azure disponibili nelle sottoscrizioni di Azure CSP.

Log Analytics in monitoraggio di Azure può essere usato anche da un provider di servizi che gestisce le risorse dei clienti tramite la funzionalità di gestione delle risorse delegate di Azure nel [Faro di Azure](../../lighthouse/overview.md).

## <a name="architectures-for-service-providers"></a>Architetture per i provider di servizi

Log Analytics aree di lavoro forniscono un metodo che consente all'amministratore di controllare il flusso e l'isolamento dei dati di [log](data-platform-logs.md) e di creare un'architettura che soddisfa le esigenze aziendali specifiche. [Questo articolo](design-logs-deployment.md) illustra le considerazioni relative alla progettazione, alla distribuzione e alla migrazione per un'area di lavoro e l'articolo [gestire l'accesso](manage-access.md) illustra come applicare e gestire le autorizzazioni per i dati di log. Per i provider di servizi possono essere presenti considerazioni aggiuntive.

Esistono tre possibili architetture relative alle aree di lavoro di Log Analytics:

### <a name="1-distributed---logs-are-stored-in-workspaces-located-in-the-customers-tenant"></a>1. i log distribuiti vengono archiviati nelle aree di lavoro situate nel tenant del cliente

In questa architettura l'area di lavoro viene distribuita nel tenant del cliente usato per tutti i log del cliente stesso.In questa architettura l'area di lavoro viene distribuita nel tenant del cliente usato per tutti i log del cliente stesso.

Ci sono due modi in cui gli amministratori del provider di servizi possono accedere a un'area di lavoro Log Analytics in un tenant del cliente:

- Un cliente può aggiungere singoli utenti dal provider di servizi come [Azure Active Directory utenti Guest (B2B)](../../active-directory/b2b/what-is-b2b.md). Gli amministratori del provider di servizi dovranno accedere alla directory di ogni cliente nel portale di Azure per poter accedere a queste aree di lavoro. Questa operazione richiede inoltre ai clienti di gestire l'accesso singolo per ogni amministratore del provider di servizi.
- Per una maggiore scalabilità e flessibilità, i provider di servizi possono usare la funzionalità di [gestione delle risorse delegate di Azure](../../lighthouse/concepts/azure-delegated-resource-management.md) di [Azure Lighthouse](../../lighthouse/overview.md) per accedere al tenant del cliente. Con questo metodo, gli amministratori del provider di servizi sono inclusi in un gruppo di utenti Azure AD nel tenant del provider di servizi e a questo gruppo viene concesso l'accesso durante il processo di onboarding per ogni cliente. Questi amministratori possono quindi accedere alle aree di lavoro di ogni cliente dall'interno del proprio tenant del provider di servizi, anziché dover accedere singolarmente al tenant di ogni cliente. L'accesso alle risorse dell'area di lavoro Log Analytics dei clienti in questo modo riduce il lavoro necessario sul lato cliente e può semplificare la raccolta e l'analisi dei dati tra più clienti gestiti dallo stesso provider di servizi tramite strumenti come le [cartelle](./workbooks-overview.md)di lavoro di monitoraggio di Azure. Per altre informazioni, vedere [monitorare le risorse dei clienti su larga scala](../../lighthouse/how-to/monitor-at-scale.md).

I vantaggi dell'architettura distribuita sono:

* Il cliente può verificare specifici livelli di autorizzazioni tramite la [gestione delle risorse delegate di Azure](../../lighthouse/concepts/azure-delegated-resource-management.md)o può gestire l'accesso ai log usando il proprio [accesso basato sui ruoli](../../role-based-access-control/overview.md).
* I log possono essere raccolti da tutti i tipi di risorse, non solo dai dati della macchina virtuale basata su agenti. Ad esempio, log di controllo di Azure.
* Ogni cliente può definire impostazioni diverse per la propria area di lavoro, ad esempio in termini di conservazione e di limitazione sull'uso dei dati.
* Isolamento tra i clienti in termini di normative e conformità.
* Il costo per ogni area di lavoro viene addebitato nella sottoscrizione del cliente.

Gli svantaggi dell'architettura distribuita sono:

* La visualizzazione e l'analisi centralizzata dei dati nei tenant dei clienti con strumenti come le cartelle di lavoro di monitoraggio di Azure possono comportare esperienze più lente, specialmente quando si analizzano i dati tra più di 50 aree di lavoro.
* Se i clienti non vengono caricati per la gestione delle risorse delegata di Azure, è necessario eseguire il provisioning degli amministratori del provider di servizi nella directory del cliente ed è più difficile per il provider di servizi gestire contemporaneamente un numero elevato di tenant del cliente.

### <a name="2-central---logs-are-stored-in-a-workspace-located-in-the-service-provider-tenant"></a>2. i log centrali vengono archiviati in un'area di lavoro situata nel tenant del provider di servizi

In questa architettura i log non vengono archiviati nel tenant del cliente, ma solo in una posizione centrale nell'ambito di una sottoscrizione del provider di servizi. Gli agenti installati su macchine virtuali del cliente sono configurati per inviare i log all'area di lavoro tramite l'ID e la chiave privata dell'area di lavoro.

I vantaggi dell'architettura centralizzata sono i seguenti:

* Facilità di gestione di un numero elevato di clienti e di integrarli in diversi sistemi back-end.
* Il provider di servizi dispone della proprietà completa dei log e dei diversi elementi, ad esempio funzioni e query salvate.
* Il provider di servizio può eseguire funzioni di analisi su tutti i propri clienti.

Gli svantaggi dell'architettura centralizzata sono:

* Questa architettura è applicabile solo ai dati di macchine virtuale basati su agenti e non può essere usata con origini dati PaaS, SaaS e dell'infrastruttura di Microsoft Azure.
* Potrebbe essere difficile separare i dati tra i clienti quando sono uniti in una singola area di lavoro. L'unico metodo per eseguire questa operazione è quello di usare il nome di dominio completo del computer (FQDN) o l'ID sottoscrizione di Azure.
* Tutti i dati di tutti i clienti verranno archiviati nella stessa area con un'unica fattura e con le stesse impostazioni di conservazione e di configurazione.
* Per i servizi PaaS e di infrastruttura di Microsoft Azure, ad esempio Diagnostica di Azure e log di controllo di Azure, l'area di lavoro deve essere nello stesso tenant della risorsa in modo che i logo non vengano inviati all'area di lavoro centrale.
* Tutti gli agenti di macchine virtuali di tutti i clienti vengono autenticati nell'area di lavoro centrale usando lo stesso ID e la stessa chiave dell'area di lavoro. Non esiste alcun metodo per bloccare i log di un cliente specifico senza interrompere l'attività di altri clienti.

### <a name="3-hybrid---logs-are-stored-in-workspace-located-in-the-customers-tenant-and-some-of-them-are-pulled-to-a-central-location"></a>3. i log ibridi vengono archiviati nell'area di lavoro che si trova nel tenant del cliente e ne viene effettuato il pull in una posizione centrale.

La terza architettura è una combinazione delle due opzioni. Tale architettura si basa su quella distribuita in cui i log sono locali per ogni cliente, usando tuttavia un meccanismo per creare un repository centrale di log. Per una parte dei log viene eseguito il pull in una posizione centrale per la creazione di report e analitica. Questa parte può essere costituita da un numero ridotto di tipi di dati o da un riepilogo dell'attività, ad esempio statistiche giornaliere.

Sono disponibili due opzioni per implementare i log in una posizione centrale:

1. Area di lavoro centrale: il provider di servizi può creare un'area di lavoro nel proprio tenant e usare uno script che usi l'[API di query](https://dev.loganalytics.io/) con l'[API di raccolta dati](./data-collector-api.md) per spostare i dati dalle diverse aree di lavoro nella posizione centrale. Un'alternativa all'uso degli script consiste nell'usare le [app per la logica di Azure](../../logic-apps/logic-apps-overview.md).

2. Power BI come posizione centrale: Power BI può fungere da posizione centrale quando le varie aree di lavoro esportano i dati usando l'integrazione tra l'area di lavoro di Log Analytics e [Power bi](./powerbi.md).

## <a name="next-steps"></a>Passaggi successivi

* Automatizzare la creazione e la configurazione delle aree di lavoro usando i [modelli di Resource Manager](template-workspace-configuration.md)

* Automatizzare la creazione delle aree di lavoro usando [PowerShell](./powershell-workspace-configuration.md)

* Usare la funzione [Avvisi](./alerts-overview.md) per l'integrazione con i sistemi esistenti

* Generare report di riepilogo usando [Power BI](./powerbi.md)

* Caricare i clienti nella [gestione delle risorse delegata di Azure](../../lighthouse/concepts/azure-delegated-resource-management.md).

