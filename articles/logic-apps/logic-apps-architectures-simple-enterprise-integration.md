---
title: 'Integrazione aziendale semplice con i servizi di integrazione di Azure: architettura di riferimento'
description: Di seguito viene descritta l’architettura di riferimento che illustra come implementare un modello di integrazione aziendale semplice con App per la logica di Azure e Gestione API di Azure.
author: mattfarm
manager: jonfan
editor: ''
services: logic-apps api-management
documentationcenter: ''
ms.assetid: ''
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 06/15/2018
ms.author: LADocs; estfan
ms.openlocfilehash: f73a9e59c0add664128b506172182afe566ca670
ms.sourcegitcommit: fab878ff9aaf4efb3eaff6b7656184b0bafba13b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/22/2018
ms.locfileid: "42444511"
---
# <a name="reference-architecture-simple-enterprise-integration"></a>Architettura di riferimento: integrazione aziendale semplice

L'architettura di riferimento seguente illustra un set di procedure collaudate che è possibile applicare a un'applicazione di integrazione che usa i servizi di integrazione di Azure. Questa architettura può essere usata come base per molti modelli di applicazione diversi che richiedono API HTTP, flusso di lavoro e orchestrazione.

![Diagramma dell'architettura: integrazione aziendale semplice](./media/logic-apps-architectures-simple-enterprise-integration/simple_arch_diagram.png)

*La tecnologia di integrazione può essere usata per numerose applicazioni, dalle più semplici, da punto a punto, alle applicazioni aziendali complete del bus di servizio di Azure. La serie relativa all'architettura descrive le parti componenti riutilizzabili che possono essere applicate per creare un'applicazione di integrazione generica. I progettisti dovranno valutare quali componenti è necessario implementare per la propria applicazione e la specifica infrastruttura.*

## <a name="architecture"></a>Architettura

L'architettura include i componenti seguenti:

- **Gruppo di risorse**. Un [gruppo di risorse](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) è un contenitore logico per le risorse di Azure.
- **Gestione API di Azure**. [Gestione API](https://docs.microsoft.com/azure/api-management/) è una piattaforma completamente gestita che consente di pubblicare, proteggere e trasformare API HTTP.
- **Portale per sviluppatori di Gestione API di Azure**. Ogni istanza di Gestione API di Azure è dotata dell'accesso al [portale per sviluppatori](https://docs.microsoft.com/azure/api-management/api-management-customize-styles). Il portale per sviluppatori di Gestione API consente di accedere alla documentazione e agli esempi di codice. In questo portale è inoltre possibile testare le API.
- **App per la logica di Azure**. [App per la logica](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview) è una piattaforma senza server che viene usata per la creazione del flusso di lavoro e dell'integrazione aziendali.
- **Connettori**. I [connettori](https://docs.microsoft.com/azure/connectors/apis-list) vengono usati da App per la logica per la connessione ai servizi di uso comune. App per la logica dispone già di centinaia di connettori diversi, ma è anche possibile creare connettori personalizzati.
- **Indirizzo IP**. Il servizio Gestione API di Azure ha un [indirizzo IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm) pubblico fisso e un nome di dominio. Il nome di dominio predefinito è un sottodominio di azure-api.net, ad esempio contoso.azure-api.net, ma è possibile configurare anche [domini personalizzati](https://docs.microsoft.com/azure/api-management/configure-custom-domain). Anche App per la logica e il bus di servizio hanno un indirizzo IP pubblico. Tuttavia, in questa architettura l'accesso per chiamare gli endpoint di App per la logica è limitato all'indirizzo IP di Gestione API per motivi di sicurezza. Le chiamate al bus di servizio sono protette da una firma di accesso condiviso.
- **DNS di Azure**. [DNS di Azure](https://docs.microsoft.com/azure/dns/) è un servizio di hosting per domini DNS che fornisce la risoluzione dei nomi tramite l'infrastruttura di Microsoft Azure. Ospitando i domini in Azure, è possibile gestire i record DNS usando le credenziali, le API, gli strumenti e il sistema di fatturazione degli altri servizi di Azure. Per usare un nome di dominio personalizzato, ad esempio contoso.com, creare record DNS in cui il nome di dominio personalizzato è mappato all'indirizzo IP. Per altre informazioni, vedere [Configurare un nome di dominio personalizzato in Gestione API](https://docs.microsoft.com/en-us/azure/api-management/configure-custom-domain).
- **Azure Active Directory (Azure AD)**. Usare [Azure AD](https://docs.microsoft.com/azure/active-directory/) o un altro provider di identità per l'autenticazione. Azure AD fornisce l'autenticazione per l'accesso agli endpoint di API passando un [token JSON Web per Gestione API](https://docs.microsoft.com/azure/api-management/policies/authorize-request-based-on-jwt-claims) da convalidare. Azure AD può proteggere l'accesso al portale per sviluppatori di Gestione API (solo per i piani Standard e Premium).

L'architettura è basata su alcuni criteri che sono fondamentali per il suo funzionamento:

- Le API composite vengono compilate usando le app per la logica che orchestrano chiamate a sistemi software come un servizio (SaaS), a servizi di Azure e a qualsiasi API pubblicata in Gestione API. [Anche le app per la logica vengono pubblicate](https://docs.microsoft.com/azure/api-management/import-logic-app-as-api) tramite il portale per sviluppatori di Gestione API.
- Le applicazioni usano Azure AD per [acquisire un token di sicurezza di OAuth 2.0](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad) necessario per ottenere l'accesso a un'API.
- Gestione API di Azure [convalida il token di sicurezza](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad) e quindi passa la richiesta all'app per la logica o all'API di back-end.

## <a name="recommendations"></a>Consigli

I requisiti specifici degli utenti possono variare rispetto all'architettura generica descritta in questo articolo. Seguire le raccomandazioni contenute in questa sezione come punto di partenza.

### <a name="azure-api-management-tier"></a>Livello di Gestione API di Azure

Usare i livelli di Gestione API Basic, Standard o Premium perché questi livelli offrono un contratto di servizio (SLA) e supportano lo scale-out all'interno dell'area di Azure; il numero di unità varia in base al livello. Il livello Premium supporta anche lo scale-out tra più aree di Azure. Adattare il livello scelto al livello di velocità effettiva richiesto e al proprio set di funzionalità. Per altre informazioni, vedere [Prezzi di Gestione API](https://azure.microsoft.com/pricing/details/api-management/).

Tutte le istanze di Gestione API comportano un addebito quando sono in esecuzione. Se le prestazioni sono state aumentate e non è necessario questo livello di prestazioni in modo costante, prendere in considerazione la fatturazione oraria di Gestione API e ridurre le prestazioni.

### <a name="logic-apps-pricing"></a>Prezzi di App per la logica

App per la logica funziona come modello [senza server](logic-apps-serverless-overview.md). La fatturazione viene calcolata in base all'esecuzione del connettore e dell'azione. Per altre informazioni, vedere [Prezzi di App per la logica](https://azure.microsoft.com/pricing/details/logic-apps/). Attualmente non sono disponibili considerazioni per la scelta del livello per App per la logica.

### <a name="logic-apps-for-asynchronous-api-calls"></a>App per la logica per le chiamate API asincrone

App per la logica funziona meglio negli scenari che non richiedono una latenza bassa. Ad esempio, è particolarmente consigliata per chiamate API asincrone o a esecuzione semi prolungata. Se è necessaria una bassa latenza, ad esempio nel caso di chiamate che bloccano un'interfaccia utente, è consigliabile implementare questa operazione o API tramite una tecnologia diversa, ad esempio Funzioni di Azure o l'API Web distribuita tramite Servizio app. È comunque consigliabile che venga eseguito il fronting di questa API per i consumer dell'API usando Gestione API.

### <a name="region"></a>Region

Eseguire il provisioning di Gestione API e App per la logica nella stessa area per ridurre al minimo la latenza di rete. È in genere consigliabile scegliere l'area più vicina agli utenti.

Anche il gruppo di risorse è associato a un'area che specifica la posizione in cui vengono archiviati i metadati di distribuzione e da cui viene eseguito il modello di distribuzione. Inserire il gruppo di risorse e le relative risorse nella stessa area per migliorare la disponibilità durante la distribuzione.

## <a name="scalability"></a>Scalabilità

Gli amministratori di Gestione API devono aggiungere i [criteri di memorizzazione nella cache](../api-management/api-management-howto-cache.md), dove appropriato, per aumentare la scalabilità del servizio e ridurre il carico sui servizi di back-end.

I livelli Basic, Standard e Premium di Gestione API di Azure possono essere aumentati in un'area di Azure per offrire maggiore capacità. Gli amministratori possono usare l’opzione **Metrica della capacità** all'interno del menu **Metriche** per analizzare l'utilizzo del proprio servizio e quindi aumentare o ridurre le prestazioni a seconda dei casi.

Elementi consigliati per la scalabilità di un servizio Gestione API:

- Requisiti di ridimensionamento per tener conto dei modelli di traffico. I clienti con modelli di traffico più volatili hanno maggiore esigenza di aumentare la capacità.
- Una capacità costante superiore al 66% può indicare la necessità di aumentare le prestazioni.
- Una capacità costante inferiore al 20% può indicare un'opportunità di ridurre le prestazioni.
- È sempre consigliabile caricare il test del servizio Gestione API con un carico rappresentativo prima di abilitarlo in produzione.

I servizi di livello Premium possono essere aumentati in più aree di Azure. I clienti che utilizzano servizi di scalatura in più aree di Azure ottengono un contratto di servizio più elevato (99,95% contro 99,9%) e possono fornire servizi nei dintorni degli utenti in più aree.

Il modello di App per la logica senza server consente agli amministratori di non dover pianificare la scalabilità del servizio. Il servizio offre scalabilità automatica per soddisfare la richiesta.

## <a name="availability"></a>Disponibilità

Attualmente, il contratto di servizio per Gestione API di Azure definisce uno standard pari al 99,9% per i piani Basic, Standard e Premium. Le configurazioni del livello Premium con la distribuzione di almeno un'unità in due o più aree sono associate a un contratto di servizio con una disponibilità di almeno 99,95%.

Attualmente, il contratto di servizio per App per la logica di Azure definisce uno standard pari al 99,9%.

### <a name="backups"></a>Backup

La configurazione di Gestione API di Azure deve essere [sottoposta a backup regolarmente](../api-management/api-management-howto-disaster-recovery-backup-restore.md) (basato sul principio di sistematicità di modifica). I file di backup devono essere archiviati in una posizione o area di Azure è diverso da in cui risiede il servizio. I clienti possono scegliere tra due opzioni per la strategia di ripristino di emergenza:

- In un evento di ripristino di emergenza viene effettuato il provisioning di una nuova istanza di Gestione API, il backup viene ripristinato nella nuova istanza e viene eseguito un nuovo puntamento dei record DNS.
- I clienti mantengono una copia passiva del servizio in un'altra area di Azure, incorrendo in costi aggiuntivi. I backup vengono ripristinati regolarmente nella copia. In un evento di ripristino di emergenza è necessario eseguire un nuovo puntamento solo dei record DNS per ripristinare il servizio.

Poiché è possibile creare nuovamente App per la logica rapidamente ed è un modello senza server, è possibile eseguire un backup salvando una copia del modello di Azure Resource Manager associato. Il salvataggio dei modelli può essere eseguito nel controllo del codice sorgente e possono essere integrati nel processo di integrazione continua/distribuzione continua (CI/CD) continua dei clienti.

Se un'app per la logica che è stata pubblicata tramite Gestione API viene spostata in un Data Center diverso, aggiornare il percorso dell'app. Questa operazione può essere eseguita tramite un semplice script di PowerShell per aggiornare la proprietà **Back-end** dell'API.

## <a name="manageability"></a>Gestibilità

Creare gruppi di risorse separati per gli ambienti di produzione, sviluppo e test. L'uso di gruppi di risorse separati consente di semplificare la gestione delle distribuzioni, l'eliminazione delle distribuzioni di test e l'assegnazione dei diritti di accesso.

Quando si assegnano risorse ai gruppi di risorse, considerare i fattori seguenti:

- **Ciclo di vita**. In generale, includere le risorse con un ciclo di vita identico nello stesso gruppo.
- **Accesso**. È possibile usare il [controllo degli accessi in base al ruolo](../role-based-access-control/overview.md) per applicare i criteri di accesso alle risorse in un gruppo.
- **Fatturazione**. È possibile visualizzare il riepilogo dei costi per il gruppo di risorse.
- **Piano tariffario per Gestione API**. Per gli ambienti di sviluppo e test è opportuno usare un piano Developer. Per la pre-produzione è consigliabile eseguire la distribuzione di una replica dell'ambiente di produzione, eseguire i test e quindi arrestare la replica per ridurre al minimo i costi.

Per altre informazioni, vedere [Panoramica di Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

### <a name="deployment"></a>Distribuzione

È consigliabile usare i [modelli di Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) per distribuire Gestione API e App per la logica. I modelli semplificano l'automazione delle distribuzioni tramite PowerShell o l'interfaccia della riga di comando di Azure.

È consigliabile inserire Gestione API di Azure e le singole app per la logica in modelli di Resource Manager separati. Usando modelli separati è possibile archiviare le risorse nei sistemi di controllo del codice sorgente. È anche possibile distribuire le risorse insieme o separatamente come parte di un processo di distribuzione CI/CD.

### <a name="versions"></a>Versioni

Ogni volta che si apporta una modifica della configurazione in un'app per la logica o si distribuisce un aggiornamento tramite un modello di Resource Manager, una copia di questa versione viene conservata per comodità dell'utente; verranno conservate tutte le versioni con una cronologia di esecuzione. È possibile usare queste versioni per tenere traccia delle modifiche cronologiche e per alzare di livello una versione in modo che sia la configurazione corrente dell'app per la logica; è possibile quindi, ad esempio, eseguire il roll-back di un'app per la logica in modo efficace.

Gestione API ha due distinti [ concetti di controllo delle versioni ](https://blogs.msdn.microsoft.com/apimanagement/2018/01/11/versions-revisions-general-availibility/), entrambi gratuiti:

- Le versioni usate per fornire ai consumer delle API una scelta di API che possono usare in base alle loro esigenze, ad esempio v1, v2 o beta, produzione.
- Le revisioni che consentono agli amministratori delle API di apportare modifiche in modo sicuro a un'API e distribuirle agli utenti con commenti facoltativi.

Nel contesto della distribuzione, le revisioni di Gestione API devono essere considerate come un modo per apportare modifiche in modo sicuro, gestire una cronologia delle modifiche e rendere i consumer delle API consapevoli di tali modifiche. È possibile creare una revisione in un ambiente di sviluppo e distribuirla negli altri ambienti usando i modelli di Resource Manager.

Sebbene sia possibile utilizzare le revisioni per testare un'API prima di renderla "corrente" e accessibile agli utenti, si sconsiglia l'utilizzo di questo meccanismo per i test di carico o di integrazione. Utilizzare invece ambienti di test o di preproduzione separati.

### <a name="configuration"></a>Configurazione

Non archiviare mai le password, le chiavi di accesso o le stringhe di connessione nel controllo del codice sorgente. Se questi valori sono necessari, usare la tecnica appropriata per distribuire e proteggere questi valori. 

In App per la logica tutti i valori sensibili necessari nell'app per la logica (che non possono essere creati sotto forma di connessione) devono essere archiviati in Azure Key Vault e referenziati da un modello di Resource Manager. È consigliabile anche usare i parametri dei modelli di distribuzione e ai file di parametri per ogni ambiente. Per maggiori informazioni, consultare [Protezione dei parametri e degli input all'interno di un flusso di lavoro](logic-apps-securing-a-logic-app.md#secure-parameters-and-inputs-within-a-workflow).

In Gestione API i segreti vengono gestiti usando oggetti *Valori denominati* o *Proprietà denominate*. Questi oggetti archiviano i valori a cui è possibile accedere nei criteri di Gestione API. Per altre informazioni, vedere [Gestione dei segreti in Gestione API](../api-management/api-management-howto-properties.md).

### <a name="diagnostics-and-monitoring"></a>Diagnostica e monitoraggio

Sia [Gestione API](../api-management/api-management-howto-use-azure-monitor.md) che [App per la logica](logic-apps-monitor-your-logic-apps.md) supportano il monitoraggio operativo tramite [Monitoraggio di Azure](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md) che fornisce informazioni basate sulle metriche configurate per ogni servizio. Monitoraggio di Azure è abilitato per impostazione predefinita.

Queste opzioni sono disponibili anche per ogni servizio:

- Le app per la logica possono essere inviate ad [Azure Log Analytics](logic-apps-monitor-your-logic-apps-oms.md) per un'analisi più approfondita e la visualizzazione nel dashboard.
- Gestione API supporta la configurazione di Azure Application Insights per il monitoraggio DevOps.
- Gestione API supporta il [modello di soluzione Power BI per l'analisi personalizzata delle API](http://aka.ms/apimpbi). I clienti possono usare il modello di soluzione per creare la propria soluzione analitica personalizzata. I report sono disponibili in Power BI per gli utenti aziendali.

## <a name="security"></a>Sicurezza

Questa sezione contiene alcune considerazioni sulla sicurezza specifiche dei servizi di Azure descritti in questo articolo e distribuite nell'architettura come descritto. Non è un elenco completo delle procedure di sicurezza consigliate.

- Usare il controllo degli accessi in base al ruolo per garantire livelli di accesso appropriati per gli utenti.
- Proteggere gli endpoint delle API pubbliche in Gestione API tramite OAuth/OpenID Connect. Per proteggere gli endpoint pubblici dell'API, configurare un provider di identità e aggiungere un criterio di convalida JSON Web Token (JWT).
- Connettersi ai servizi back-end da Gestione API usando certificati basati sull'autenticazione reciproca.
- Proteggere le app per la logica basate su trigger creando un elenco di indirizzi IP consentiti che puntano all'indirizzo IP di Gestione API. Con l’elenco di indirizzi IP consentiti si impedisce di chiamare l'app per la logica da un indirizzo Internet pubblico senza passare prima attraverso Gestione API.

## <a name="next-steps"></a>Passaggi successivi

- Maggiori informazione sull’[integrazione aziendale con code ed eventi](logic-apps-architectures-enterprise-integration-with-queues-events.md).
