---
title: 'Integrazione aziendale con i servizi di integrazione di Azure: architettura di riferimento'
description: Questo articolo presenta un'architettura di riferimento che illustra come implementare un modello di integrazione aziendale usando App per la logica, Gestione API, il bus di servizio e Griglia di eventi.
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
ms.openlocfilehash: a86c4c4227795a712dd51ace1fbefe9d2b96518a
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/18/2018
ms.locfileid: "39116113"
---
# <a name="reference-architecture-enterprise-integration-with-queues-and-events"></a>Architettura di riferimento: integrazione aziendale con code ed eventi

L'architettura di riferimento seguente illustra un set di procedure collaudate che è possibile applicare a un'applicazione di integrazione che usa i servizi di integrazione di Azure. Questa architettura può essere usata come base per molti modelli di applicazione diversi che richiedono API HTTP, flusso di lavoro e orchestrazione.

![Diagramma dell'architettura: integrazione aziendale con code ed eventi](media/logic-apps-architectures-enterprise-integration-with-queues-events/integr_queues_events_arch_diagram.png)

*La tecnologia di integrazione può essere usata per numerose applicazioni, dalle più semplici, da punto a punto, alle applicazioni aziendali complete del bus di servizio di Azure. La serie relativa all'architettura descrive le parti componenti riutilizzabili che possono essere applicate per creare un'applicazione di integrazione generica. I progettisti dovranno valutare quali componenti è necessario implementare per la propria applicazione e la specifica infrastruttura.*

## <a name="architecture"></a>Architettura

Questa architettura *si basa* su quella illustrata nell'[integrazione aziendale semplice](logic-apps-architectures-simple-enterprise-integration.md). Anche i [consigli per l'architettura aziendale semplice](logic-apps-architectures-simple-enterprise-integration.md#recommendations) sono applicabili a questo caso, ma sono stati omessi dai [consigli](#recommendations) riportati in questo articolo per motivi di brevità. 

L'architettura include i componenti seguenti:

- **Gruppo di risorse**. Un [gruppo di risorse](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) è un contenitore logico per le risorse di Azure.
- **Gestione API di Azure**. [Gestione API](https://docs.microsoft.com/azure/api-management/) è una piattaforma completamente gestita che consente di pubblicare, proteggere e trasformare API HTTP.
- **Portale per sviluppatori di Gestione API di Azure**. Ogni istanza di Gestione API di Azure è dotata dell'accesso al [portale per sviluppatori](https://docs.microsoft.com/azure/api-management/api-management-customize-styles). Il portale per sviluppatori di Gestione API consente di accedere alla documentazione e agli esempi di codice. In questo portale è inoltre possibile testare le API.
- **App per la logica di Azure**. [App per la logica](https://docs.microsoft.com/azure/logic-apps/logic-apps-overview) è una piattaforma senza server che viene usata per la creazione del flusso di lavoro e dell'integrazione aziendali.
- **Connettori**. I [connettori](https://docs.microsoft.com/azure/connectors/apis-list) vengono usati da App per la logica per la connessione ai servizi di uso comune. App per la logica dispone già di centinaia di connettori diversi, ma è anche possibile creare connettori personalizzati.
- **Bus di servizio di Azure**. Il [bus di servizio](https://docs.microsoft.com/en-us/azure/service-bus-messaging/service-bus-messaging-overview) fornisce un servizio di messaggistica protetto e affidabile. La messaggistica può essere usata per separare le applicazioni e consentirne l'integrazione con altri sistemi basati sui messaggi.
- **Griglia di eventi di Azure**. [Griglia di eventi](https://docs.microsoft.com/azure/event-grid/overview) è una piattaforma senza server che viene usata per pubblicare e distribuire eventi di applicazioni.
- **Indirizzo IP**. Il servizio Gestione API di Azure ha un [indirizzo IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm) pubblico fisso e un nome di dominio. Il nome di dominio è un sottodominio di azure-api.net, ad esempio contoso.azure-api.net. Anche App per la logica e il bus di servizio hanno un indirizzo IP pubblico. Tuttavia, in questa architettura l'accesso per chiamare gli endpoint di App per la logica è limitato all'indirizzo IP di Gestione API per motivi di sicurezza. Le chiamate al bus di servizio sono protette da una firma di accesso condiviso.
- **DNS di Azure**. [DNS di Azure](https://docs.microsoft.com/azure/dns/) è un servizio di hosting per domini DNS che fornisce la risoluzione dei nomi tramite l'infrastruttura di Microsoft Azure. Ospitando i domini in Azure, è possibile gestire i record DNS usando le credenziali, le API, gli strumenti e il sistema di fatturazione degli altri servizi di Azure. Per usare un nome di dominio personalizzato, ad esempio contoso.com, creare record DNS in cui il nome di dominio personalizzato è mappato all'indirizzo IP. Per altre informazioni, vedere [Configurare un nome di dominio personalizzato in Gestione API](https://docs.microsoft.com/en-us/azure/api-management/configure-custom-domain).
- **Azure Active Directory (Azure AD)**. Usare [Azure AD](https://docs.microsoft.com/azure/active-directory/) o un altro provider di identità per l'autenticazione. Azure AD fornisce l'autenticazione per l'accesso agli endpoint di API passando un [token JSON Web per Gestione API](https://docs.microsoft.com/azure/api-management/policies/authorize-request-based-on-jwt-claims) da convalidare. Azure AD può proteggere l'accesso al portale per sviluppatori di Gestione API (solo per i piani Standard e Premium).

L'architettura è basata su alcuni criteri che sono fondamentali per il suo funzionamento:

* Le API HTTP di back-end esistenti vengono pubblicate tramite il portale per sviluppatori di Gestione API. Nel portale gli sviluppatori (interni e/o esterni all'organizzazione) possono integrare le chiamate a queste API nelle applicazioni.
* Le API composite vengono create usando app per la logica e orchestrando chiamate a sistemi SaaS, a servizi di Azure e a qualsiasi API pubblicata in Gestione API. [Anche le app per la logica vengono pubblicate](https://docs.microsoft.com/azure/api-management/import-logic-app-as-api) tramite il portale per sviluppatori di Gestione API.
- Le applicazioni usano Azure AD per [acquisire un token di sicurezza di OAuth 2.0](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad) necessario per ottenere l'accesso a un'API.
- Gestione API [convalida il token di sicurezza](https://docs.microsoft.com/azure/api-management/api-management-howto-protect-backend-with-aad) e quindi passa la richiesta all'app per la logica o all'API di back-end.
- Le code del bus di servizio vengono usate per [separare](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-messaging-overview) le attività delle applicazioni e [uniformare i picchi in fase di caricamento](https://docs.microsoft.com/azure/architecture/patterns/queue-based-load-leveling). I messaggi vengono aggiunti alle code da app per la logica o applicazioni di terze parti oppure (non nell'immagine) pubblicando la coda come API HTTP tramite Gestione API.
- Quando i messaggi vengono aggiunti a una coda del bus di servizio, viene generato un evento. Quest'ultimo attiva un'app per la logica che quindi elabora il messaggio.
- Anche altri servizi di Azure, come Archiviazione BLOB e Hub eventi, pubblicano eventi in Griglia di eventi. Questi servizi attivano app per la logica per la ricezione dell'evento e quindi eseguono le azioni successive.

## <a name="recommendations"></a>Consigli

I requisiti specifici degli utenti possono variare rispetto all'architettura generica descritta in questo articolo. Seguire le raccomandazioni contenute in questa sezione come punto di partenza.

### <a name="service-bus-tier"></a>Piano del bus di servizio

Per il bus di servizio usare il piano tariffario Premium che supporta le notifiche di Griglia di eventi. Per altre informazioni, vedere [Prezzi del bus di servizio](https://azure.microsoft.com/pricing/details/service-bus/).

### <a name="event-grid-pricing"></a>Prezzi di Griglia di eventi

Griglia di eventi usa un modello senza server. La fatturazione viene calcolata in base al numero di operazioni (esecuzioni di eventi). Per altre informazioni, vedere [Prezzi di Griglia di eventi](https://azure.microsoft.com/pricing/details/event-grid/). Attualmente non sono previste indicazioni per la scelta del piano tariffario per Griglia di eventi.

### <a name="use-peeklock-to-consume-service-bus-messages"></a>Usare PeekLock per gestire i messaggi del bus di servizio

Quando si crea un'app per la logica per gestire i messaggi del bus di servizio, usare [PeekLock](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md#queues) in tale app per accedere a un gruppo di messaggi. Con PeekLock, l'app per la logica può eseguire i passaggi per convalidare ogni messaggio prima di completarlo o abbandonarlo. Questo approccio protegge dalla perdita accidentale dei messaggi.

### <a name="check-for-multiple-objects-when-an-event-grid-trigger-fires"></a>Verificare più oggetti quando viene attivato un trigger di Griglia di eventi

Quando viene attivato un trigger di Griglia di eventi, significa semplicemente che "si è verificato almeno uno degli eventi specificati". Ad esempio, quando Griglia di eventi attiva un'app per la logica su un messaggio visualizzato in una coda del bus di servizio, l'app per la logica deve sempre presupporre che siano presenti messaggi da elaborare.

### <a name="region"></a>Area

Effettuare il provisioning di Gestione API, di App per la logica e del bus di servizio nella stessa area per ridurre al minimo la latenza di rete. È in genere consigliabile scegliere l'area più vicina agli utenti.

Anche il gruppo di risorse è associato a un'area che specifica la posizione in cui vengono archiviati i metadati di distribuzione e da cui viene eseguito il modello di distribuzione. Inserire il gruppo di risorse e le relative risorse nella stessa area per migliorare la disponibilità durante la distribuzione.

## <a name="scalability"></a>Scalabilità

Il piano Premium del bus di servizio consente di aumentare il numero di unità di messaggistica per offrire una maggiore scalabilità. Le configurazioni del piano Premium possono prevedere una, due o quattro unità di messaggistica. Per altre informazioni sul ridimensionamento del bus di servizio, vedere [Procedure consigliate per il miglioramento delle prestazioni tramite la messaggistica del bus di servizio](../service-bus-messaging/service-bus-performance-improvements.md).

## <a name="availability"></a>Disponibilità

Attualmente, il contratto di servizio (SLA, Service Level Agreement) per Gestione API di Azure definisce uno standard pari al 99,9% per i piani Basic, Standard e Premium. Le configurazioni del piano Premium con la distribuzione di almeno un'unità in due o più aree sono associate a un contratto di servizio con una disponibilità di almeno 99,95%.

Attualmente, il contratto di servizio per App per la logica di Azure definisce uno standard pari al 99,9%.

### <a name="disaster-recovery"></a>Ripristino di emergenza

Nell'ambito del piano Premium del bus di servizio è consigliabile implementare il ripristino di emergenza geografico per abilitare il failover in caso di grave interruzione. Per altre informazioni, vedere [Ripristino di emergenza geografico per il bus di servizio di Azure](../service-bus-messaging/service-bus-geo-dr.md).

## <a name="manageability"></a>Gestibilità

Creare gruppi di risorse separati per gli ambienti di produzione, sviluppo e test. L'uso di gruppi di risorse separati consente di semplificare la gestione delle distribuzioni, l'eliminazione delle distribuzioni di test e l'assegnazione dei diritti di accesso.

Quando si assegnano risorse ai gruppi di risorse, considerare i fattori seguenti:

- **Ciclo di vita**. In generale, includere le risorse con un ciclo di vita identico nello stesso gruppo.
- **Accesso**. È possibile usare il [controllo degli accessi in base al ruolo](../role-based-access-control/overview.md) per applicare i criteri di accesso alle risorse in un gruppo.
- **Fatturazione**. È possibile visualizzare il riepilogo dei costi per il gruppo di risorse.
- **Piano tariffario per Gestione API**. Per gli ambienti di sviluppo e test è opportuno usare un piano Developer. Per la pre-produzione è consigliabile eseguire la distribuzione di una replica dell'ambiente di produzione, eseguire i test e quindi arrestare la replica per ridurre al minimo i costi.

Per altre informazioni, vedere [Panoramica di Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

### <a name="deployment"></a>Distribuzione

Per distribuire Gestione API di Azure, App per la logica, Griglia di eventi e il bus di servizio, è consigliabile usare i [modelli di Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md). I modelli semplificano l'automazione delle distribuzioni tramite PowerShell o l'interfaccia della riga di comando di Azure.

È opportuno inserire Gestione API, le singole app per la logica, gli argomenti di Griglia di eventi e gli spazi dei nomi del bus di servizio in modelli di Resource Manager separati. Usando modelli separati è possibile archiviare le risorse nei sistemi di controllo del codice sorgente. È quindi possibile distribuire questi modelli insieme o separatamente come parte di un processo di integrazione continua/distribuzione continua (CI/CD).

### <a name="diagnostics-and-monitoring"></a>Diagnostica e monitoraggio

Analogamente a Gestione API e App per la logica, è possibile monitorare il bus di servizio usando Monitoraggio di Azure, che fornisce informazioni basate sulle metriche configurate per ogni servizio. Monitoraggio di Azure è abilitato per impostazione predefinita.

## <a name="security"></a>Sicurezza

Proteggere il bus di servizio usando una firma di accesso condiviso. È possibile usare l'[autenticazione basata sulla firma di accesso condiviso](../service-bus-messaging/service-bus-sas.md) per concedere agli utenti l'accesso alle risorse del bus di servizio con diritti specifici. Per altre informazioni, vedere [Autenticazione e autorizzazione del bus di servizio](../service-bus-messaging/service-bus-authentication-and-authorization.md).

Se una coda del bus di servizio deve essere esposta come endpoint HTTP (per la pubblicazione di nuovi messaggi), è consigliabile usare Gestione API per proteggerla mettendo al sicuro l'endpoint. L'endpoint può quindi essere protetto tramite certificati o OAuth, a seconda delle esigenze. Il modo più semplice per proteggere un endpoint è quello di usare un'app per la logica con un trigger HTTP di tipo richiesta/risposta come intermediario.

Griglia di eventi consente di proteggere il recapito dell'evento tramite un codice di convalida. Se si usa App per la logica per gestire l'evento, la convalida viene eseguita automaticamente. Per altre informazioni, vedere [Event Grid security and authentication](../event-grid/security-authentication.md) (Sicurezza e autenticazione di Griglia di eventi).

## <a name="next-steps"></a>Passaggi successivi

- Informazioni sull'[integrazione aziendale semplice](logic-apps-architectures-simple-enterprise-integration.md).