---
title: Modello di architettura di integrazione aziendale semplice - Azure Integration Services
description: Questa guida di riferimento sull'architettura descrive come implementare un modello di integrazione aziendale semplice usando App per la logica di Azure e Gestione API di Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: mattfarm
ms.author: mattfarm
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 06/15/2018
ms.openlocfilehash: 5ed6fa9f514bae3ea651edba6702714e2680091f
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46955946"
---
# <a name="simple-enterprise-integration-architecture"></a>Architettura di integrazione aziendale semplice

Questo articolo descrive un'architettura di integrazione aziendale che usa procedure comprovate che è possibile applicare a un'applicazione di integrazione quando si usa Azure Integration Services. È possibile usare questa architettura come base per molti modelli di applicazione che richiedono API HTTP, flusso di lavoro e orchestrazione.

![Diagramma dell'architettura: integrazione aziendale semplice](./media/logic-apps-architectures-simple-enterprise-integration/simple_arch_diagram.png)

Questa serie descrive le parti dei componenti riutilizzabili che possono essere applicate per creare un'applicazione di integrazione generica. Poiché la tecnologia di integrazione ha molte applicazioni possibili, dalle app semplici da punto a punto alle app del bus di servizio di Azure aziendali complete, occorre valutare quali componenti è necessario implementare per le app e l'infrastruttura desiderate.

## <a name="architecture-components"></a>Componenti dell'architettura

Questa architettura di integrazione aziendale include i componenti seguenti:

- **Gruppo di risorse**: un [gruppo di risorse](../azure-resource-manager/resource-group-overview.md) è un contenitore logico per le risorse di Azure.

- **Gestione API di Azure**: il servizio [Gestione API](https://docs.microsoft.com/azure/api-management/) è una piattaforma completamente gestita per la pubblicazione, la protezione e la trasformazione di API HTTP.

- **Portale per sviluppatori per Gestione API di Azure**: ogni istanza di Gestione API di Azure consente di accedere al [portale per sviluppatori](../api-management/api-management-customize-styles.md). In questo portale sono disponibili documentazione ed esempi di codice. Nel portale per sviluppatori è possibile anche testare le API.

- **App per la logica di Azure**: [App per la logica](../logic-apps/logic-apps-overview.md) è una piattaforma serverless per la creazione di flussi di lavoro e integrazioni aziendali.

- **Connettori**: App per la logica usa i [connettori](../connectors/apis-list.md) per connettersi ai servizi di uso più comune. App per la logica offre centinaia di connettori, ma è anche possibile crearne uno personalizzato.

- **Indirizzo IP**: il servizio Gestione API di Azure dispone di un [indirizzo IP](../virtual-network/virtual-network-ip-addresses-overview-arm.md) pubblico fisso e di un nome di dominio. Il nome di dominio predefinito è un sottodominio di azure-api.net, ad esempio contoso.azure-api.net, ma è possibile configurare anche [domini personalizzati](../api-management/configure-custom-domain.md). Anche App per la logica e il bus di servizio hanno un indirizzo IP pubblico. In questa architettura tuttavia l'accesso per chiamare gli endpoint di App per la logica è limitato solo all'indirizzo IP di Gestione API per motivi di sicurezza. Le chiamate al bus di servizio sono protette da una firma di accesso condiviso.

- **DNS di Azure**: [DNS di Azure](https://docs.microsoft.com/azure/dns/) è un servizio di hosting per domini DNS che fornisce la risoluzione dei nomi tramite l'infrastruttura di Microsoft Azure. Ospitando i domini in Azure, è possibile gestire i record DNS usando le credenziali, le API, gli strumenti e il sistema di fatturazione degli altri servizi di Azure. Per usare un nome di dominio personalizzato, ad esempio contoso.com, creare record DNS che eseguono il mapping del nome di dominio personalizzato all'indirizzo IP. Per altre informazioni, vedere [Configurare un nome di dominio personalizzato in Gestione API](../api-management/configure-custom-domain.md).

- **Azure Active Directory (Azure AD)**: è possibile usare [Azure AD](https://docs.microsoft.com/azure/active-directory/) o un altro provider di identità per l'autenticazione. Azure AD fornisce l'autenticazione per l'accesso agli endpoint di API passando un [token JSON Web per Gestione API](../api-management/policies/authorize-request-based-on-jwt-claims.md) da convalidare. Per i livelli Standard e Premium Azure AD può proteggere l'accesso al portale per sviluppatori per Gestione API di Azure.

## <a name="patterns"></a>Modelli 

Questa architettura usa alcuni modelli che sono fondamentali per il relativo funzionamento:

* Le API composite vengono create usando app per la logica, che orchestrano le chiamate a sistemi SaaS, a servizi di Azure e a qualsiasi API pubblicata in Gestione API. Anche le app per la logica vengono [pubblicate tramite il portale per sviluppatori per Gestione API di Azure](../api-management/import-logic-app-as-api.md).

* Le applicazioni usano Azure AD per [acquisire un token di sicurezza di OAuth 2.0](../api-management/api-management-howto-protect-backend-with-aad.md), che è necessario per ottenere l'accesso a un'API.

* Gestione API di Azure [convalida il token di sicurezza](../api-management/api-management-howto-protect-backend-with-aad.md) e quindi passa la richiesta all'app per la logica o all'API di back-end.

## <a name="recommendations"></a>Consigli

I requisiti specifici degli utenti possono variare rispetto all'architettura generica descritta in questo articolo. Seguire le raccomandazioni contenute in questa sezione come punto di partenza.

### <a name="azure-api-management-tier"></a>Livello di Gestione API di Azure

Usare i livelli di Gestione API Basic, Standard o Premium perché questi livelli offrono un contratto di servizio (SLA) di produzione e supportano lo scale-out all'interno dell'area di Azure. Il numero di unità varia in base ai livelli. Il livello Premium supporta lo scale-out anche tra più aree di Azure. Scegliere il livello adeguato alle proprie esigenze in base al set di funzionalità e al livello di velocità effettiva necessaria. Per altre informazioni, vedere [Prezzi di Gestione API](https://azure.microsoft.com/pricing/details/api-management/).

Tutte le istanze di Gestione API comportano un addebito quando sono in esecuzione. Se le prestazioni sono state aumentate e non è necessario questo livello di prestazioni in modo costante, prendere in considerazione la fatturazione oraria di Gestione API e ridurre le prestazioni.

### <a name="logic-apps-pricing"></a>Prezzi di App per la logica

App per la logica funziona come modello [senza server](../logic-apps/logic-apps-serverless-overview.md). La fatturazione viene calcolata in base all'esecuzione del connettore e dell'azione. Per altre informazioni, vedere [Prezzi di App per la logica](https://azure.microsoft.com/pricing/details/logic-apps/). Attualmente non sono disponibili considerazioni per la scelta del livello per App per la logica.

### <a name="logic-apps-for-asynchronous-api-calls"></a>App per la logica per le chiamate API asincrone

App per la logica funziona meglio negli scenari che non richiedono una latenza bassa. È particolarmente consigliata ad esempio per le chiamate API asincrone o a esecuzione semi prolungata. Se è richiesta una latenza bassa, ad esempio nel caso di chiamate che bloccano un'interfaccia utente, è consigliabile implementare l'operazione o l'API usando una tecnologia diversa, ad esempio Funzioni di Azure o l'API Web distribuita tramite Servizio app. Usare Gestione API a fronte dell'API per i consumer di API.

### <a name="region"></a>Region

Per ridurre al minimo la latenza di rete, scegliere la stessa area per Gestione API, App per la logica e il bus di servizio. È in genere consigliabile scegliere l'area più vicina agli utenti.

Anche il gruppo di risorse è associato a un'area Questa area specifica il percorso in cui archiviare i metadati di distribuzione e in cui eseguire il modello di distribuzione. Per migliorare la disponibilità durante la distribuzione, aggiungere il gruppo di risorse e le risorse nella stessa area.

## <a name="scalability"></a>Scalabilità

Per migliorare la scalabilità quando si amministra un servizio di Gestione API, aggiungere [criteri di memorizzazione nella cache](../api-management/api-management-howto-cache.md) laddove appropriato. e ridurre il carico sui servizi di back-end.

Per offrire una maggiore capacità, è possibile effettuare lo scale-out dei livelli Basic, Standard e Premium di Gestione API di Azure in un'area di Azure. Per analizzare l'utilizzo per il servizio, nel menu **Metriche** selezionare l'opzione **Metrica della capacità** e quindi aumentare o ridurre le prestazioni in base alle esigenze.

Elementi consigliati per la scalabilità di un servizio Gestione API:

- Quando si scala un servizio, considerare i modelli di traffico. I clienti con modelli di traffico più volatili necessitano di una capacità maggiore.

- Una capacità costante superiore al 66% può indicare la necessità di aumentare le prestazioni.

- Una capacità costante inferiore al 20% può indicare un'opportunità per ridurre le prestazioni.

- Prima di consentire il carico in produzione, è sempre consigliabile testare il carico nel servizio di Gestione API usando un carico rappresentativo.

È possibile eseguire lo scale-out dei servizi di livello Premium tra più aree di Azure. Se si esegue la distribuzione scalando i servizi tra più aree di Azure, è possibile ottenere un contratto di servizio superiore (99,95% rispetto al 99,9%) ed eseguire il provisioning di servizi vicini agli utenti in più aree.

Il modello serverless di App per la logica di Azure prevede che gli amministratori non debbano pianificare la scalabilità dei servizi. Il servizio offre scalabilità automatica per soddisfare la richiesta.

## <a name="availability"></a>Disponibilità

* Per i livelli Basic, Standard e Premium il contratto di servizio (SLA) per Gestione API di Azure è al momento del 99,9%. Per le configurazioni di livello Premium con una distribuzione che dispone di almeno un'unità in due o più aree, il contratto di servizio è del 99,95%.

* Il contratto di servizio per App per la logica di Azure è al momento pari al 99,9%.

### <a name="backups"></a>Backup

In base alla regolarità delle modifiche, [eseguire regolarmente il backup](../api-management/api-management-howto-disaster-recovery-backup-restore.md) della configurazione di Gestione API di Azure in uso. Archiviare i file di backup in un percorso o in un'area di Azure diversa da quella in cui risiede il servizio. È quindi possibile scegliere una delle opzioni come strategia di ripristino di emergenza:

* In caso di ripristino di emergenza effettuare il provisioning di una nuova istanza di Gestione API, ripristinare il backup nella nuova istanza ed eseguire un nuovo puntamento dei record DNS.

* Mantenere una copia passiva del servizio in un'altra area di Azure, che prevede tuttavia costi aggiuntivi. Ripristinare regolarmente i backup a tale copia. Per ripristinare il servizio durante un evento di ripristino di emergenza, occorre solo eseguire un nuovo puntamento dei record DNS.

Poiché è possibile ricreare rapidamente le app per la logica, che sono serverless, eseguirne il backup salvando una copia del modello di Azure Resource Manager associato. È possibile salvare i modelli nel controllo del codice sorgente ed eseguirne l'integrazione con il processo di integrazione continua/distribuzione continua (CI/CD).

Se un'app per la logica che è stata pubblicata tramite Gestione API viene spostata in un data center diverso, aggiornare il percorso dell'app. È possibile aggiornare la proprietà **back-end** dell'API usando uno script di PowerShell di base.

## <a name="manageability"></a>Gestibilità

Creare gruppi di risorse separati per gli ambienti di produzione, sviluppo e test. L'uso di gruppi di risorse separati semplifica la gestione delle distribuzioni, l'eliminazione delle distribuzioni di test e l'assegnazione dei diritti di accesso.

Quando si assegnano risorse a gruppi di risorse, considerare i fattori seguenti:

* **Ciclo di vita**: in generale, includere risorse con lo stesso ciclo di vita nello stesso gruppo di risorse.

* **Accesso**: per applicare i criteri di accesso alle risorse in un gruppo, è possibile usare il [controllo degli accessi in base al ruolo](../role-based-access-control/overview.md).

* **Fatturazione**: è possibile visualizzare i costi di rollup per il gruppo di risorse.

* **Piano tariffario per Gestione API**: usare il livello Developer per gli ambienti di sviluppo e test. Per ridurre al minimo i costi durante la pre-produzione, distribuire una replica dell'ambiente di produzione, eseguire i test e quindi arrestare il sistema.

Per altre informazioni, vedere [Panoramica di Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

## <a name="deployment"></a>Distribuzione

* Per distribuire Gestione API e App per la logica di Azure, usare i [modelli di Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md). I modelli semplificano l'automazione delle distribuzioni tramite PowerShell o l'interfaccia della riga di comando di Azure.

* Aggiungere Gestione API e qualsiasi app per la logica individuale nei rispettivi modelli di Resource Manager separati. L'uso di modelli separati consente di archiviare le risorse in sistemi di controllo del codice sorgente. È quindi possibile distribuire questi modelli insieme o separatamente come parte di un processo di integrazione continua/distribuzione continua (CI/CD).

### <a name="versions"></a>Versioni

Ogni volta che si modifica la configurazione di un'app per la logica o si distribuisce un aggiornamento attraverso un modello di Resource Manager, Azure conserva una copia di tale versione per comodità oltre a tutte le versioni che hanno una cronologia di esecuzione. È possibile usare queste versioni per tenere traccia delle modifiche cronologiche o per promuovere una versione come configurazione corrente dell'app per la logica; è possibile quindi, ad esempio, eseguire il roll-back di un'app per la logica in modo efficace.

Gestione API di Azure include questi due [concetti di controllo delle versioni ](https://blogs.msdn.microsoft.com/apimanagement/2018/01/11/versions-revisions-general-availibility/) distinti ma complementari:

* Versioni che offrono ai consumer di API la possibilità di scegliere una versione dell'API in base alle esigenze, ad esempio, v1, v2, beta o di produzione

* Revisioni che consentono agli amministratori delle API di apportare modifiche in modo sicuro a un'API e successivamente distribuirle agli utenti con commenti facoltativi

Per la distribuzione, le revisioni di Gestione API vanno considerate come un modo per apportare modifiche in modo sicuro, mantenere una cronologia delle modifiche e comunicare le modifiche ai consumer dell'API. È possibile effettuare una revisione in un ambiente di sviluppo e distribuire la modifica in altri ambienti usando i modelli di Resource Manager.

Sebbene sia possibile usare le revisioni per testare un'API prima di renderle "operative" e disponibili agli utenti, si sconsiglia l'utilizzo di questo meccanismo per eseguire i test di carico o di integrazione. Utilizzare invece ambienti di test o di preproduzione separati.

### <a name="configuration-and-sensitive-information"></a>Configurazione e informazioni riservate

Non archiviare mai le password, le chiavi di accesso o le stringhe di connessione nel controllo del codice sorgente, Se questi valori sono necessari, usare le tecniche appropriate per distribuire e proteggere questi valori. 

In App per la logica, se un'app per la logica richiede un qualsiasi valore sensibile che non è possibile creare all'interno di una connessione, archiviare questo valore in Azure Key Vault e farvi riferimento da un modello di Resource Manager. Usare i parametri del modello di distribuzione e i file di parametri per ogni ambiente. Per maggiori informazioni, consultare [Protezione dei parametri e degli input all'interno di un flusso di lavoro](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-and-inputs-within-a-workflow).

In Gestione API i segreti vengono gestiti usando oggetti chiamati *Valori denominati* o *Proprietà*. Questi oggetti archiviano in modo sicuro valori a cui è possibile accedere attraverso i criteri di Gestione API. Per altre informazioni, vedere [Gestione dei segreti in Gestione API](../api-management/api-management-howto-properties.md).

## <a name="diagnostics-and-monitoring"></a>Diagnostica e monitoraggio

Per il monitoraggio operativo è possibile usare [Monitoraggio di Azure](../azure-monitor/overview.md) sia in [Gestione API](../api-management/api-management-howto-use-azure-monitor.md) che in [App per la logica](../logic-apps/logic-apps-monitor-your-logic-apps.md). Il servizio Monitoraggio di Azure include informazioni basate sulle metriche configurate per ogni servizio ed è abilitato per impostazione predefinita.

Ogni servizio dispone inoltre di queste opzioni:

* Per un'analisi approfondita e la creazione di dashboard, è possibile inviare i registri di App per la logica ad [Azure Log Analytics](logic-apps-monitor-your-logic-apps-oms.md).

* Per il monitoraggio DevOps è possibile configurare Azure Application Insights per Gestione API.

* Gestione API supporta il [modello di soluzione Power BI per l'analisi personalizzata delle API](http://aka.ms/apimpbi). È possibile usare questo modello di soluzione per creare una soluzione di analisi personalizzata. Per gli utenti aziendali sono disponibili report in Power BI.

## <a name="security"></a>Sicurezza

Sebbene questo elenco non descriva completamente tutte le procedure consigliate per la sicurezza, ecco alcune considerazioni sulla sicurezza che si applicano nello specifico ai servizi di Azure distribuiti nell'architettura descritta in questo articolo:

* Per assicurarsi che gli utenti dispongano dei livelli di accesso appropriati, usare il controllo degli accessi in base al ruolo.

* Proteggere gli endpoint dell'API pubblici in Gestione API tramite OAuth o OpenID Connect. Per proteggere gli endpoint dell'API pubblici, configurare un provider di identità e aggiungere criteri di convalida JSON Web Token (JWT).

* Connettersi ai servizi back-end da Gestione API usando certificati basati sull'autenticazione reciproca.

* Proteggere le app per la logica basate su trigger creando un elenco di indirizzi IP consentiti che puntano all'indirizzo IP di Gestione API. Con l’elenco di indirizzi IP consentiti si impedisce di chiamare l'app per la logica da un indirizzo Internet pubblico senza passare prima attraverso Gestione API.

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sull'[integrazione aziendale con code ed eventi](../logic-apps/logic-apps-architectures-enterprise-integration-with-queues-events.md)
