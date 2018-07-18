---
title: Servizi di integrazione di Azure - Integrazione aziendale semplice
description: Architettura di riferimento che illustra come implementare un modello di integrazione aziendale semplice con App per la logica di Azure e Gestione API di Azure
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
ms.openlocfilehash: 17f591a470bf65d3c9365bca9c5ae2d5a6c9574f
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/19/2018
ms.locfileid: "36231977"
---
# <a name="simple-enterprise-integration---reference-architecture"></a>Integrazione aziendale semplice: architettura di riferimento

## <a name="overview"></a>Panoramica

Questa architettura di riferimento illustra un set di procedure collaudate per compilare un'applicazione di integrazione che usa i servizi di integrazione di Azure. Questa architettura può fungere da base per molti diversi modelli di applicazione che richiedono API HTTP, flusso di lavoro e orchestrazione.

*La tecnologia di integrazione ha molte possibili applicazioni, da una semplice applicazione point-to-point a un bus di servizio aziendale completo. Questa serie di architetture definisce le parti di componenti riutilizzabili per la compilazione di qualsiasi applicazione di integrazione: i progettisti di architetture devono valutare con attenzione i componenti di cui avranno bisogno per le loro applicazioni e infrastrutture.*

   ![Diagramma dell'architettura: integrazione aziendale semplice](./media/logic-apps-architectures-simple-enterprise-integration/simple_arch_diagram.png)

## <a name="architecture"></a>Architecture

L'architettura include i componenti seguenti:

- Gruppo di risorse. È un contenitore logico per le risorse di Azure.
- Gestione API di Azure. È una piattaforma completamente gestita per la pubblicazione, la protezione e la trasformazione di API HTTP.
- Portale per sviluppatori di Gestione API di Azure. Ogni istanza di Gestione API di Azure viene fornita con un portale per sviluppatori, che consente di accedere alla documentazione e agli esempi di codice e di testare le API.
- App per la logica di Azure. È una piattaforma senza server per la creazione del flusso di lavoro e dell'integrazione aziendali.
- Connettori. Vengono utilizzati da App per la logica per la connessione ai servizi di uso comune. App per la logica dispone già di centinaia di connettori diversi, ma è possibile anche crearli tramite un connettore personalizzato.
- Indirizzo IP. Il servizio Gestione API di Azure ha un indirizzo IP pubblico fisso e un nome di dominio Il nome di dominio è un sottodominio di azure-api.net, ad esempio contoso. azure-api.net. Anche App per la logica di Azure ha un indirizzo IP pubblico; tuttavia, in questa architettura l'accesso per chiamare gli endpoint di App per la logica è limitato solo all'indirizzo IP di Gestione API per motivi di sicurezza.
- DNS di Azure. DNS di Azure è un servizio di hosting per i domini DNS, che fornisce la risoluzione dei nomi usando l'infrastruttura di Microsoft Azure. Ospitando i domini in Azure, è possibile gestire i record DNS usando le stesse credenziali, API, strumenti e fatturazione come per gli altri servizi Azure. Per usare un nome di dominio personalizzato, ad esempio contoso.com, creare record DNS che eseguono il mapping del nome di dominio personalizzato all'indirizzo IP. Per altre informazioni, vedere Configurare un nome di dominio personalizzato in Gestione API di Azure.
- Azure Active Directory (Azure AD). Usare Azure AD o un altro provider di identità per l'autenticazione. Azure AD fornisce l'autenticazione per accedere agli endpoint delle API passando un token JSON Web che verrà convalidato da Gestione API e consente di proteggere l'accesso al portale per sviluppatori di Gestione API, solo i livelli Standard e Premium.

Il funzionamento di questa architettura prevede alcuni criteri fondamentali:

1. Le API HTTP di back-end esistenti vengono pubblicate tramite il portale per sviluppatori di Gestione API, consentendo agli sviluppatori (interni o esterni all'organizzazione o a entrambi) di integrare le chiamate a queste API nelle applicazioni.
2. Le API composite vengono compilate usando App per la logica, orchestrando le chiamate ai sistemi SAAS, ai servizi di Azure e a tutte le API pubblicate in Gestione API. Le app per la logica vengono pubblicate anche tramite il portale per sviluppatori di Gestione API.
3. Le applicazioni acquisiscono un token di sicurezza OAuth 2.0 necessario per ottenere l'accesso a un'API usando Azure Active Directory.
4. Gestione API di Azure convalida il token di sicurezza e passa la richiesta all'app per la logica/API di back-end.

## <a name="recommendations"></a>Raccomandazioni

I requisiti della propria organizzazione potrebbero essere diversi da quelli dell'architettura descritta in questo articolo. Seguire le raccomandazioni contenute in questa sezione come punto di partenza.

### <a name="azure-api-management-tier"></a>Livello di Gestione API di Azure

Usare i livelli Basic, Standard o Premium perché offrono un contratto di servizio per la produzione e supportano lo scale-out all'interno dell'area di Azure; il numero di unità varia in base al livello. Il livello Premium supporta anche lo scale-out tra più aree di Azure. Adattare il livello scelto al livello di velocità effettiva richiesto e al set di funzionalità. Per altre informazioni, vedere [Prezzi di Gestione API](https://azure.microsoft.com/pricing/details/api-management/).

Tutte le istanze di Gestione API comportano un addebito quando sono in esecuzione. Se le prestazioni sono state aumentate e non è necessario questo livello di prestazioni in modo costante, prendere in considerazione la fatturazione oraria di Gestione API e ridurre le prestazioni.

### <a name="logic-apps-pricing"></a>Prezzi di App per la logica

App per la logica funziona come modello [senza server](logic-apps-serverless-overview.md): la fatturazione viene calcolata in base all'esecuzione del connettore e dell'azione. Per altre informazioni, vedere [Prezzi di App per la logica](https://azure.microsoft.com/pricing/details/logic-apps/). Attualmente non sono disponibili considerazioni per la scelta del livello per App per la logica.

### <a name="logic-apps-for-asynchronous-api-calls"></a>App per la logica per le chiamate API asincrone

App per la logica funziona in modo ottimale in scenari che non richiedono una bassa latenza, ad esempio le chiamate API asincrone o ad esecuzione quasi lunga. Se è necessaria una bassa latenza, ad esempio nel caso di chiamate che bloccano un'interfaccia utente, è consigliabile implementare questa operazione o API tramite una tecnologia diversa, ad esempio Funzioni di Azure o l'API Web distribuita tramite Servizio app. È comunque consigliabile che venga eseguito il fronting di questa API usando Gestione API per i consumer dell'API.

### <a name="region"></a>Region

Eseguire il provisioning di Gestione API e App per la logica nella stessa area per ridurre al minimo la latenza di rete. In genere, è consigliabile selezionare l'area più vicina agli utenti.

Anche il gruppo di risorse è associato a un'area, che specifica dove vengono archiviati i metadati di distribuzione e da dove viene eseguito il modello di distribuzione. Inserire il gruppo e le relative risorse nella stessa area, per provare a migliorare la disponibilità durante la distribuzione.

## <a name="scalability-considerations"></a>Considerazioni sulla scalabilità

Gli amministratori di Gestione API devono aggiungere i [criteri di memorizzazione nella cache](../api-management/api-management-howto-cache.md), dove appropriato, per aumentare la scalabilità del servizio e ridurre il carico sui servizi di back-end.

I livelli Basic, Standard e Premium di Gestione API di Azure possono essere aumentati in un'area di Azure per offrire maggiore capacità. Gli amministratori possono usare la metrica della capacità all'interno del menu Metriche per analizzare l'utilizzo del proprio servizio e aumentare o ridurre le prestazioni a seconda dei casi.

Elementi consigliati per la scalabilità di un servizio Gestione API:

- La scalabilità deve tenere conto dei modelli di traffico; i clienti con modelli di traffico più volatili avranno più necessità di maggiore capacità.
- Una capacità costante superiore al 66% può indicare la necessità di aumentare le prestazioni.
- Una capacità costante inferiore al 20% può indicare un'opportunità di ridurre le prestazioni.
- È sempre consigliabile caricare il test del servizio Gestione API con un carico rappresentativo prima di abilitarlo in produzione.

I servizi di livello Premium possono essere aumentati in più aree di Azure. I clienti che effettuano la distribuzione in questo modo otterranno un contratto di servizio superiore (99,95% rispetto a 99,9%) e potranno eseguire il provisioning dei servizi vicino agli utenti in più regioni.

Il modello senza server di App per la logica implica che gli amministratori non devono effettuare considerazioni aggiuntive per la scalabilità del servizio; il servizio si ridimensiona automaticamente in modo da soddisfare le richieste.

## <a name="availability-considerations"></a>Considerazioni sulla disponibilità

Al momento della scrittura, il contratto di servizio (SLA, Service Level Agreement) per Gestione API di Azure definisce lo standard di 99,9% per i livelli Basic, Standard e Premium. Le configurazioni del livello Premium con la distribuzione di almeno un'unità in due o più aree sono associate a un contratto di servizio con una disponibilità di almeno 99,95%.

Al momento della scrittura, il contratto di servizio (SLA, Service Level Agreement) per App per la logica di Azure definisce lo standard di 99,9%.

### <a name="backups"></a>Backup

È necessario eseguire [con regolarità un backup](../api-management/api-management-howto-disaster-recovery-backup-restore.md) della configurazione di Gestione API di Azure, con intervalli in base alla frequenza di modifica, e dei file di backup archiviati in un percorso o in un'area di Azure diversa dalla posizione in cui risiede il servizio. I clienti possono scegliere tra due opzioni per la strategia di ripristino di emergenza:

1. In un evento di ripristino di emergenza viene effettuato il provisioning di una nuova istanza di Gestione API, il backup viene ripristinato nella nuova istanza e viene eseguito un nuovo puntamento dei record DNS.
2. I clienti mantengono una copia passiva del servizio in un'altra area di Azure, incorrendo in costi aggiuntivi. I backup vengono ripristinati regolarmente in questa area. In un evento di ripristino di emergenza è necessario eseguire un nuovo puntamento solo dei record DNS per ripristinare il servizio.

Poiché è possibile creare nuovamente App per la logica molto rapidamente ed è un modello senza server, è possibile eseguire un backup salvando una copia del modello di Azure Resource Manager associato. Il salvataggio può essere eseguito nel controllo del codice sorgente/integrato nel processo di integrazione continua/distribuzione continua (CI/CD) continua dei clienti.

Le app per la logica che sono state pubblicate tramite Gestione API necessiteranno delle posizioni aggiornate in caso di trasferimento in un data center diverso. Questa operazione può essere eseguita tramite un semplice script di PowerShell per aggiornare la proprietà Backend dell'API.

## <a name="manageability-considerations"></a>Considerazioni sulla gestibilità

Creare gruppi di risorse separati per gli ambienti di produzione, sviluppo e test. Questo semplifica la gestione delle distribuzioni, l'eliminazione delle distribuzioni dei test e l'assegnazione dei diritti di accesso.
Quando si assegnano risorse a gruppi di risorse, considerare quanto segue:

- Ciclo di vita. In generale, includere le risorse con un ciclo di vita identico nello stesso gruppo di risorse.
- Accesso. È possibile usare il [controllo degli accessi in base al ruolo](../role-based-access-control/overview.md) per applicare i criteri di accesso alle risorse in un gruppo.
- Fatturazione. È possibile visualizzare il riepilogo dei costi per il gruppo di risorse.
- Piano tariffario per Gestione API: è consigliabile usare un livello Developer per gli ambienti di sviluppo e test. Per la pre-produzione è consigliabile eseguire la distribuzione di una replica dell'ambiente di produzione, eseguire i test e quindi arrestare la replica per ridurre al minimo i costi.

Per altre informazioni, vedere la panoramica del [gruppo di risorse](../azure-resource-manager/resource-group-overview.md).

### <a name="deployment"></a>Distribuzione

È consigliabile usare i [modelli di Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) per distribuire Gestione API di Azure e App per la logica di Azure. I modelli semplificano l'automazione delle distribuzioni tramite PowerShell o l'interfaccia della riga di comando di Azure.

È consigliabile inserire Gestione API di Azure e le singole app per la logica in modelli di Resource Manager separati. In questo modo verranno archiviate nei sistemi del controllo del codice sorgente. Questi modelli possono quindi essere distribuiti insieme o separatamente come parte di un processo di distribuzione continua/integrazione continua (CI/CD).

### <a name="versions"></a>Versioni

Ogni volta che si apporta una modifica della configurazione in un'app per la logica o si distribuisce un aggiornamento tramite un modello di Resource Manager, una copia di questa versione viene conservata per comodità dell'utente; verranno conservate tutte le versioni con una cronologia di esecuzione. È possibile usare queste versioni per tenere traccia delle modifiche cronologiche e per alzare di livello una versione in modo che sia la configurazione corrente dell'app per la logica; è possibile quindi, ad esempio, eseguire il roll-back di un'app per la logica in modo efficace.

Gestione API ha due distinti [ concetti di controllo delle versioni ](https://blogs.msdn.microsoft.com/apimanagement/2018/01/11/versions-revisions-general-availibility/), entrambi gratuiti:

- Le versioni usate per fornire ai consumer delle API una scelta di API che possono usare in base alle loro esigenze, ad esempio v1, v2 o beta, produzione.
- Le revisioni che consentono agli amministratori delle API di apportare modifiche in modo sicuro a un'API e distribuirle agli utenti con commenti facoltativi.

Nel contesto della distribuzione, le revisioni di Gestione API devono essere considerate come un modo per apportare modifiche in modo sicuro, gestire una cronologia delle modifiche e rendere i consumer delle API consapevoli di tali modifiche. È possibile creare una revisione in un ambiente di sviluppo e distribuirla negli altri ambienti usando i modelli di Resource Manager.

Sebbene le revisioni possano essere usate per testare un'API prima che sia resa "corrente" e accessibile agli utenti, non è consigliabile usare questo meccanismo per i test di carico o di integrazione; in alternativa si devono usare preferibilmente ambienti di test e di pre-produzione separati.

### <a name="configuration"></a>Configurazione

Non archiviare mai le password, le chiavi di accesso o le stringhe di connessione nel controllo del codice sorgente. Se sono necessari, usare la tecnica appropriata per distribuire e proteggere questi valori. 

In App per la logica tutti i valori sensibili necessari all'interno dell'app per la logica (che non possono essere creati sotto forma di connessione) devono essere archiviati in Azure Key Vault e referenziati da un modello di Resource Manager. È consigliabile anche usare i parametri dei modelli di distribuzione insieme ai file di parametri per ogni ambiente. Informazioni aggiuntive sulla [protezione dei parametri e degli input all'interno di un flusso di lavoro](logic-apps-securing-a-logic-app.md#secure-parameters-and-inputs-within-a-workflow).

In Gestione API i segreti vengono gestiti usando oggetti Valori denominati/Proprietà denominate. Questi oggetti archiviano i valori a cui è possibile accedere nei criteri di Gestione API. Vedere come [gestire i segreti in Gestione API](../api-management/api-management-howto-properties.md).

### <a name="diagnostics-and-monitoring"></a>Diagnostica e monitoraggio

Sia [Gestione API](../api-management/api-management-howto-use-azure-monitor.md) che [App per la logica](logic-apps-monitor-your-logic-apps.md) supportano il monitoraggio operativo tramite [Monitoraggio di Azure](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md). La piattaforma Monitoraggio di Azure è abilitata per impostazione predefinita e fornirà le informazioni in base alle diverse metriche configurate per ogni servizio.

Inoltre sono disponibili ulteriori opzioni per ogni servizio:

- Le app per la logica possono essere inviate a [Log Analytics](logic-apps-monitor-your-logic-apps-oms.md) per un'analisi più approfondita e la visualizzazione nel dashboard.
- Gestione API supporta la configurazione di Application Insights per il monitoraggio Dev Ops.
- Gestione API supporta il [modello di soluzione Power BI per l'analisi personalizzata delle API](http://aka.ms/apimpbi). Questo modello di soluzione consente ai clienti di creare una soluzione di analisi personalizzata, con report disponibili in Power BI per gli utenti aziendali.

## <a name="security-considerations"></a>Considerazioni relative alla sicurezza

Questa sezione contiene alcune considerazioni sulla sicurezza specifiche dei servizi di Azure descritti in questo articolo e distribuite nell'architettura come descritto. Non è un elenco completo delle procedure di sicurezza consigliate.

- Usare il controllo degli accessi in base al ruolo per garantire livelli di accesso appropriati per gli utenti.
- Proteggere gli endpoint delle API pubbliche in Gestione API tramite OAuth/Open IDConnect. Eseguire questa operazione configurando un provider di identità e aggiungendo i criteri di convalida JWT.
- Connettersi ai servizi back-end da Gestione API usando certificati basati sull'autenticazione reciproca.
- Proteggere le app per la logica basate su trigger creando un elenco di indirizzi IP consentiti che puntano all'indirizzo IP di Gestione API. In questo modo si impedisce di chiamare l'app per la logica da un indirizzo Internet pubblico senza passare prima attraverso Gestione API.

Questa architettura di riferimento ha illustrato come compilare una semplice piattaforma di integrazione aziendale usando i servizi di integrazione di Azure.

## <a name="next-steps"></a>Passaggi successivi

* [Integrazione aziendale con code ed eventi](logic-apps-architectures-enterprise-integration-with-queues-events.md)
