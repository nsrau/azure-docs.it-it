---
title: Modello di architettura di integrazione aziendale - Azure Integration Services
description: Questa guida di riferimento sull'architettura descrive come implementare un modello di integrazione aziendale usando App per la logica di Azure, Gestione API di Azure, bus di servizio di Azure e Griglia di eventi di Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: mattfarm
ms.author: mattfarm
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 06/15/2018
ms.openlocfilehash: 8fbc84b4016659b6d0d6ce9ec47c05a0b241c3d9
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2018
ms.locfileid: "48855375"
---
# <a name="enterprise-integration-architecture-with-queues-and-events"></a>Integrazione dell’architettura aziendale con code ed eventi

Questo articolo descrive un'architettura di integrazione aziendale che usa procedure comprovate che è possibile applicare a un'applicazione di integrazione quando si usa Azure Integration Services. È possibile usare questa architettura come base per molti modelli di applicazione che richiedono API HTTP, flusso di lavoro e orchestrazione.

![Diagramma dell'architettura: integrazione aziendale con code ed eventi](media/logic-apps-architectures-enterprise-integration-with-queues-events/integr_queues_events_arch_diagram.png)

Questa serie descrive le parti dei componenti riutilizzabili che possono essere applicate per creare un'applicazione di integrazione generica. Poiché la tecnologia di integrazione ha molte applicazioni possibili, dalle app semplici da punto a punto alle app del bus di servizio di Azure aziendali complete, occorre valutare quali componenti è necessario implementare per le app e l'infrastruttura desiderate.

## <a name="architecture-components"></a>Componenti dell'architettura

Questa architettura si basa sull'architettura descritta nell'articolo [Architettura di riferimento: integrazione aziendale semplice](../logic-apps/logic-apps-architectures-simple-enterprise-integration.md). Questi [elementi consigliati](../logic-apps/logic-apps-architectures-simple-enterprise-integration.md#recommendations) per l’architettura si applicano anche qui, ma per brevità, questo articolo omette tali elementi consigliati dalla sezione [Elementi consigliati](#recommendations). Questa architettura di integrazione aziendale include i componenti seguenti:

- **Gruppo di risorse**: un [gruppo di risorse](../azure-resource-manager/resource-group-overview.md) è un contenitore logico per le risorse di Azure.

- **Gestione API di Azure**: il servizio [Gestione API](https://docs.microsoft.com/azure/api-management/) è una piattaforma completamente gestita per la pubblicazione, la protezione e la trasformazione di API HTTP.

- **Portale per sviluppatori per Gestione API di Azure**: ogni istanza di Gestione API di Azure consente di accedere al [portale per sviluppatori](../api-management/api-management-customize-styles.md). In questo portale sono disponibili documentazione ed esempi di codice. Nel portale per sviluppatori è possibile anche testare le API.

- **App per la logica di Azure**: [App per la logica](../logic-apps/logic-apps-overview.md) è una piattaforma serverless per la creazione di flussi di lavoro e integrazioni aziendali.

- **Connettori**: App per la logica usa i [connettori](../connectors/apis-list.md) per connettersi ai servizi di uso più comune. App per la logica offre centinaia di connettori, ma è anche possibile crearne uno personalizzato.

- **Bus di servizio di Azure**: il [bus di servizio](../service-bus-messaging/service-bus-messaging-overview.md) fornisce un servizio di messaggistica protetto e affidabile. È possibile utilizzare la messaggistica per separare le applicazioni e per l'integrazione con altri sistemi basati su messaggi.

- **Griglia di eventi di Azure**: [Griglia di eventi](../event-grid/overview.md) è una piattaforma senza server per la pubblicazione e la distribuzione di eventi di applicazioni.

- **Indirizzo IP**: il servizio Gestione API di Azure dispone di un [indirizzo IP](../virtual-network/virtual-network-ip-addresses-overview-arm.md) pubblico fisso e di un nome di dominio. Il nome di dominio predefinito è un sottodominio di azure-api.net, ad esempio contoso.azure-api.net, ma è possibile configurare anche [domini personalizzati](../api-management/configure-custom-domain.md). Anche App per la logica e il bus di servizio hanno un indirizzo IP pubblico. In questa architettura tuttavia l'accesso per chiamare gli endpoint di App per la logica è limitato solo all'indirizzo IP di Gestione API per motivi di sicurezza. Le chiamate al bus di servizio sono protette da una firma di accesso condiviso.

- **DNS di Azure**: [DNS di Azure](https://docs.microsoft.com/azure/dns/) è un servizio di hosting per domini DNS che fornisce la risoluzione dei nomi tramite l'infrastruttura di Microsoft Azure. Ospitando i domini in Azure, è possibile gestire i record DNS usando le credenziali, le API, gli strumenti e il sistema di fatturazione degli altri servizi di Azure. Per usare un nome di dominio personalizzato, ad esempio contoso.com, creare record DNS che eseguono il mapping del nome di dominio personalizzato all'indirizzo IP. Per altre informazioni, vedere [Configurare un nome di dominio personalizzato in Gestione API](../api-management/configure-custom-domain.md).

- **Azure Active Directory (Azure AD)**: è possibile usare [Azure AD](https://docs.microsoft.com/azure/active-directory/) o un altro provider di identità per l'autenticazione. Azure AD fornisce l'autenticazione per l'accesso agli endpoint di API passando un [token JSON Web per Gestione API](../api-management/policies/authorize-request-based-on-jwt-claims.md) da convalidare. Per i livelli Standard e Premium Azure AD può proteggere l'accesso al portale per sviluppatori per Gestione API di Azure.

## <a name="patterns"></a>Modelli 

Questa architettura usa alcuni modelli che sono fondamentali per il relativo funzionamento:

* Le API HTTP di back-end esistenti vengono pubblicate tramite il portale per sviluppatori di Gestione API. Nel portale, gli sviluppatori, che sono interni all’organizzazione, esterni, o entrambi,  
possono integrare le chiamate a queste API nelle applicazioni.

* Le API composite vengono create usando app per la logica, che orchestrano le chiamate a sistemi SaaS, a servizi di Azure e a qualsiasi API pubblicata in Gestione API. Anche le app per la logica vengono [pubblicate tramite il portale per sviluppatori per Gestione API di Azure](../api-management/import-logic-app-as-api.md).

* Le applicazioni usano Azure AD per [acquisire un token di sicurezza di OAuth 2.0](../api-management/api-management-howto-protect-backend-with-aad.md), che è necessario per ottenere l'accesso a un'API.

* Gestione API di Azure [convalida il token di sicurezza](../api-management/api-management-howto-protect-backend-with-aad.md) e quindi passa la richiesta all'app per la logica o all'API di back-end.

* Le code del bus di servizio di Azure vengono usate per [separare](../service-bus-messaging/service-bus-messaging-overview.md) le attività delle applicazioni e [uniformare i picchi in fase di caricamento](https://docs.microsoft.com/azure/architecture/patterns/queue-based-load-leveling). I messaggi vengono aggiunti alle code da app per la logica o applicazioni di terze parti oppure (non nell'immagine) pubblicando la coda come API HTTP tramite Gestione API.

* Quando i messaggi vengono aggiunti a una coda del bus di servizio, viene generato un evento. L'evento attiva un'app per la logica, che quindi elabora il messaggio.

* Anche altri servizi di Azure, come ad esempio Archiviazione BLOB e Hub eventi, pubblicano eventi in Griglia di eventi. Questi servizi attivano app per la logica per la ricezione dell'evento e quindi eseguono le azioni successive.

## <a name="recommendations"></a>Consigli

I requisiti specifici degli utenti possono variare rispetto all'architettura generica descritta in questo articolo. Seguire le raccomandazioni contenute in questa sezione come punto di partenza.

### <a name="service-bus-tier"></a>Livello del bus di servizio

Utilizzare il livello bus di servizio Premium, che supporta le notifiche della Griglia di eventi. Per altre informazioni, vedere [Prezzi del bus di servizio](https://azure.microsoft.com/pricing/details/service-bus/).

### <a name="event-grid-pricing"></a>Prezzi di Griglia di eventi

Griglia di eventi usa un modello senza server. La fatturazione viene calcolata in base al numero di operazioni (esecuzioni di eventi). Per altre informazioni, vedere [Prezzi di Griglia di eventi](https://azure.microsoft.com/pricing/details/event-grid/). Attualmente non sono previste indicazioni per la scelta del piano tariffario per Griglia di eventi.

### <a name="use-peeklock-to-consume-service-bus-messages"></a>Usare PeekLock per gestire i messaggi del bus di servizio

Quando si crea un'app per la logica per utilizzare i messaggi del bus di servizio, utilizzare l'app per la logica [PeekLock](../service-bus-messaging/service-bus-messaging-overview.md#queues) per accedere a un gruppo di messaggi. Con PeekLock, l'app per la logica può eseguire i passaggi per convalidare ogni messaggio prima di completarlo o abbandonarlo. Questo approccio protegge dalla perdita accidentale dei messaggi.

### <a name="check-for-multiple-objects-when-an-event-grid-trigger-fires"></a>Verificare più oggetti quando viene attivato un trigger di Griglia di eventi.

Quando viene attivato un trigger di Griglia di eventi, questa azione significa semplicemente che "si è verificato almeno uno degli eventi specificati". Ad esempio, quando Griglia di eventi attiva un'app per la logica su un messaggio visualizzato in una coda del bus di servizio, l'app per la logica deve sempre presupporre che siano presenti messaggi da elaborare.

### <a name="region"></a>Region

Per ridurre al minimo la latenza di rete, scegliere la stessa area per Gestione API, App per la logica e il bus di servizio. È in genere consigliabile scegliere l'area più vicina agli utenti.

Anche il gruppo di risorse è associato a un'area Questa area specifica il percorso in cui archiviare i metadati di distribuzione e in cui eseguire il modello di distribuzione. Per migliorare la disponibilità durante la distribuzione, aggiungere il gruppo di risorse e le risorse nella stessa area.

## <a name="scalability"></a>Scalabilità

Il piano Premium del bus di servizio consente di aumentare il numero di unità di messaggistica per offrire una maggiore scalabilità. Le configurazioni del piano Premium possono prevedere una, due o quattro unità di messaggistica. Per altre informazioni sul ridimensionamento del bus di servizio, vedere [Procedure consigliate per il miglioramento delle prestazioni tramite la messaggistica del bus di servizio](../service-bus-messaging/service-bus-performance-improvements.md).

## <a name="availability"></a>Disponibilità

* Per i livelli Basic, Standard e Premium il contratto di servizio (SLA) per Gestione API di Azure è al momento del 99,9%. Per le configurazioni di livello Premium con una distribuzione che dispone di almeno un'unità in due o più aree, il contratto di servizio è del 99,95%.

* Il contratto di servizio per App per la logica di Azure è al momento pari al 99,9%.

### <a name="disaster-recovery"></a>Ripristino di emergenza

Nell'ambito del piano Premium del bus di servizio è consigliabile implementare il ripristino di emergenza geografico per abilitare il failover in caso di grave interruzione. Per altre informazioni, vedere [Ripristino di emergenza geografico per il bus di servizio di Azure](../service-bus-messaging/service-bus-geo-dr.md).

## <a name="manageability"></a>Gestibilità

Creare gruppi di risorse separati per gli ambienti di produzione, sviluppo e test. L'uso di gruppi di risorse separati semplifica la gestione delle distribuzioni, l'eliminazione delle distribuzioni di test e l'assegnazione dei diritti di accesso.

Quando si assegnano risorse a gruppi di risorse, considerare i fattori seguenti:

* **Ciclo di vita**: in generale, includere risorse con lo stesso ciclo di vita nello stesso gruppo di risorse.

* **Accesso**: per applicare i criteri di accesso alle risorse in un gruppo, è possibile usare il [controllo degli accessi in base al ruolo](../role-based-access-control/overview.md).

* **Fatturazione**: è possibile visualizzare i costi di rollup per il gruppo di risorse.

* **Piano tariffario per Gestione API**: usare il livello Developer per gli ambienti di sviluppo e test. Per ridurre al minimo i costi durante la pre-produzione, distribuire una replica dell'ambiente di produzione, eseguire i test e quindi arrestare il sistema.

Per altre informazioni, vedere [Panoramica di Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

## <a name="deployment"></a>Distribuzione

* Per distribuire Gestione API di Azure, App per la logica, Griglia di eventi e il bus di servizio, usare i [modelli di Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md). I modelli semplificano l'automazione delle distribuzioni tramite PowerShell o l'interfaccia della riga di comando di Azure.

* Inserire Gestione API, le singole app per la logica, gli argomenti di Griglia di eventi e gli spazi dei nomi del bus di servizio in modelli di Resource Manager separati. L'uso di modelli separati consente di archiviare le risorse in sistemi di controllo del codice sorgente. È quindi possibile distribuire questi modelli insieme o separatamente come parte di un processo di integrazione continua/distribuzione continua (CI/CD).

## <a name="diagnostics-and-monitoring"></a>Diagnostica e monitoraggio

Analogamente a Gestione API e App per la logica, è possibile monitorare il bus di servizio usando Monitoraggio di Azure, abilitato per impostazione predefinita e che fornisce informazioni basate sulle metriche configurate per ogni servizio. 

## <a name="security"></a>Sicurezza

Per proteggere il bus di servizio, usare una firma di accesso condiviso (SAS). Ad esempio, è possibile usare l'[autenticazione basata sulla firma di accesso condiviso](../service-bus-messaging/service-bus-sas.md) per concedere agli utenti l'accesso alle risorse del bus di servizio con diritti specifici. Per altre informazioni, vedere [Autenticazione e autorizzazione del bus di servizio](../service-bus-messaging/service-bus-authentication-and-authorization.md).

Se è necessario esporre una coda del bus di servizio come endpoint HTTP, ad esempio per la pubblicazione nuovi messaggi, utilizzare Gestione API per proteggere la coda mettendo al sicuro l'endpoint. L'endpoint può quindi essere protetto tramite certificati o autenticazione OAuth, a seconda delle esigenze. Il modo più semplice per proteggere un endpoint è quello di usare un'app per la logica con un trigger di richiesta/risposta HTTP come intermediario.

Il servizio Griglia di eventi consente di proteggere il recapito dell'evento tramite un codice di convalida. Se si usa App per la logica per gestire l'evento, la convalida viene eseguita automaticamente. Per altre informazioni, vedere [Event Grid security and authentication](../event-grid/security-authentication.md) (Sicurezza e autenticazione di Griglia di eventi).

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sull'[integrazione aziendale semplice](logic-apps-architectures-simple-enterprise-integration.md)
