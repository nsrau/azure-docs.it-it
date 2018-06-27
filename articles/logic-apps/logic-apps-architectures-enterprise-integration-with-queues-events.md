---
title: Architettura di riferimento dei servizi di integrazione di Azure
description: Architettura di riferimento che illustra come implementare un modello di integrazione aziendale con App per la logica, Gestione API, il bus di servizio e Griglia di eventi
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
ms.openlocfilehash: db3350b3c70f6e6f35949e8423334061849b7b37
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/19/2018
ms.locfileid: "36232213"
---
# <a name="enterprise-integration-with-queues-and-events-reference-architecture"></a>Integrazione aziendale con code ed eventi: architettura di riferimento

## <a name="overview"></a>Panoramica

Questa architettura di riferimento illustra un set di procedure collaudate per compilare un'applicazione di integrazione che usa i servizi di integrazione di Azure. Questa architettura può fungere da base per molti diversi modelli di applicazione che richiedono API HTTP, flusso di lavoro e orchestrazione.

*La tecnologia di integrazione ha molte possibili applicazioni, da una semplice applicazione point-to-point a un bus di servizio aziendale completo. Questa serie di architetture definisce le parti di componenti riutilizzabili per la compilazione di qualsiasi applicazione di integrazione: i progettisti di architetture devono valutare con attenzione i componenti di cui avranno bisogno per le loro applicazioni e infrastrutture.*

![Diagramma dell'architettura: Integrazione aziendale con code ed eventi](media/logic-apps-architectures-enterprise-integration-with-queues-events/integr_queues_events_arch_diagram.png)

## <a name="architecture"></a>Architecture

Questa architettura si basa su quella illustrata nell'[integrazione aziendale semplice](logic-apps-architectures-simple-enterprise-integration.md). Include i componenti seguenti:

- Gruppo di risorse. È un contenitore logico per le risorse di Azure.
- Gestione API di Azure. È una piattaforma completamente gestita per la pubblicazione, la protezione e la trasformazione di API HTTP.
- Portale per sviluppatori di Gestione API di Azure. Ogni istanza di Gestione API di Azure viene fornita con un portale per sviluppatori, che consente di accedere alla documentazione e agli esempi di codice e di testare le API.
- App per la logica di Azure. È una piattaforma senza server per la creazione del flusso di lavoro e dell'integrazione aziendali.
- Connettori. Vengono utilizzati da App per la logica per la connessione ai servizi di uso comune. App per la logica dispone già di centinaia di connettori diversi, ma è possibile anche crearli tramite un connettore personalizzato.
- Bus di servizio di Azure. Bus di servizio fornisce una messaggistica protetta e affidabile. La messaggistica può essere usata per separare le applicazioni l'una dall'altra e consentire l'integrazione con altri sistemi basati sui messaggi.
- Griglia di eventi di Azure. Griglia eventi è una piattaforma senza server per la pubblicazione e la distribuzione di eventi di applicazioni.
- Indirizzo IP. Il servizio Gestione API di Azure ha un indirizzo IP pubblico fisso e un nome di dominio Il nome di dominio è un sottodominio di azure-api.net, ad esempio contoso.azure-api.net. Anche App per la logica di Azure e il bus di servizio hanno un indirizzo IP pubblico; tuttavia, in questa architettura l'accesso per chiamare gli endpoint di App per la logica è limitato solo all'indirizzo IP di Gestione API per motivi di sicurezza. Le chiamate al bus di servizio sono protette da una firma di accesso condiviso.
- DNS di Azure. DNS di Azure è un servizio di hosting per i domini DNS, che fornisce la risoluzione dei nomi usando l'infrastruttura di Microsoft Azure. Ospitando i domini in Azure, è possibile gestire i record DNS usando le stesse credenziali, API, strumenti e fatturazione come per gli altri servizi Azure. Per usare un nome di dominio personalizzato, ad esempio contoso.com, creare record DNS che eseguono il mapping del nome di dominio personalizzato all'indirizzo IP. Per altre informazioni, vedere Configurare un nome di dominio personalizzato in Gestione API di Azure.
- Azure Active Directory (Azure AD). Usare Azure AD o un altro provider di identità per l'autenticazione. Azure AD fornisce l'autenticazione per accedere agli endpoint delle API passando un token JSON Web che verrà convalidato da Gestione API e consente di proteggere l'accesso al portale per sviluppatori di Gestione API, solo i livelli Standard e Premium.

Il funzionamento di questa architettura prevede alcuni criteri fondamentali:

1. Le API HTTP di back-end esistenti vengono pubblicate tramite il portale per sviluppatori di Gestione API, consentendo agli sviluppatori (interni o esterni all'organizzazione o a entrambi) di integrare le chiamate a queste API nelle applicazioni.
2. Le API composite vengono compilate usando App per la logica, orchestrando le chiamate ai sistemi SAAS, ai servizi di Azure e a tutte le API pubblicate in Gestione API. Le app per la logica vengono pubblicate anche tramite il portale per sviluppatori di Gestione API.
3. Le applicazioni acquisiscono un token di sicurezza OAuth 2.0 necessario per ottenere l'accesso a un'API usando Azure Active Directory.
4. Gestione API di Azure convalida il token di sicurezza e passa la richiesta all'app per la logica/API di back-end.
5. Le code del bus di servizio vengono usate per separare l'attività dell'applicazione e uniformare i picchi in fase di carico. I messaggi vengono aggiunti alle code da App per la logica, applicazioni di terze parti o (non nell'immagine) pubblicando la coda come API HTTP tramite Gestione API.
6. Quando i messaggi vengono aggiunti a una coda del bus di servizio, viene generato un evento. Un'app per la logica viene attivata da questo evento ed elabora il messaggio.
7. Analogamente, anche altri servizi di Azure (ad esempio, Archiviazione BLOB, Hub eventi) pubblicano eventi nella Griglia di eventi. Questi attivano App per la logica per ricevere l'evento ed eseguire le azioni successive.

## <a name="recommendations"></a>Raccomandazioni

I requisiti della propria organizzazione potrebbero essere diversi da quelli dell'architettura descritta in questo articolo. Seguire le raccomandazioni contenute in questa sezione come punto di partenza.

### <a name="service-bus-tier"></a>Livello del bus di servizio

Usare il livello Premium del bus di servizio perché attualmente supporta le notifiche della griglia eventi; è previsto il supporto in tutti i livelli. Vedere [Prezzi del bus di servizio](https://azure.microsoft.com/pricing/details/service-bus/).

### <a name="event-grid-pricing"></a>Prezzi di Griglia di eventi

Griglia di eventi funziona usando un modello senza server: la fatturazione viene calcolata in base al numero di operazioni (esecuzione dell'evento). Vedere [Prezzi di Griglia di eventi](https://azure.microsoft.com/pricing/details/event-grid/) per altre informazioni. Attualmente non esistono considerazioni per la scelta del livello per Griglia di eventi.

### <a name="use-peeklock-when-consuming-service-bus-messages"></a>Usare PeekLock quando si usano i messaggi del bus di servizio

Durante la creazione di app per la logica per usare i messaggi del bus di servizio, usare [PeekLock](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md#queues) all'interno dell'app per la logica per accedere a un gruppo di messaggi. L'app per la logica può quindi eseguire i passaggi per convalidare ogni messaggio prima del completamento o della chiusura. Questo approccio protegge dalla perdita accidentale dei messaggi.

### <a name="check-for-multiple-objects-when-an-event-grid-trigger-fires"></a>Verificare più oggetti quando viene attivato un trigger di Griglia di eventi.

L'esecuzione di trigger di Griglia di eventi significa semplicemente che "si è verificato almeno 1 di questi elementi". Ad esempio, quando Griglia di eventi attiva un'app per la logica per un messaggio visualizzato in una coda del bus di servizio, l'app per la logica deve presupporre sempre che possano essere presenti uno o più messaggi disponibili per l'elaborazione.

### <a name="region"></a>Region

Eseguire il provisioning di Gestione API, App per la logica e il bus del servizio nella stessa area per ridurre al minimo la latenza di rete. In genere, è consigliabile selezionare l'area più vicina agli utenti.

Anche il gruppo di risorse è associato a un'area, che specifica dove vengono archiviati i metadati di distribuzione e da dove viene eseguito il modello di distribuzione. Inserire il gruppo e le relative risorse nella stessa area, per provare a migliorare la disponibilità durante la distribuzione.

## <a name="scalability-considerations"></a>Considerazioni sulla scalabilità

Il livello Premium del bus di servizio di Azure può eseguire lo scale-out del numero di unità di messaggistica per ottenere maggiore scalabilità. Il livello Premium può avere 1, 2 o 4 unità di messaggistica. Per altre informazioni sulla scalabilità del bus di servizio di Azure, vedere [Procedure consigliate per il miglioramento delle prestazioni tramite la messaggistica del bus di servizio](../service-bus-messaging/service-bus-performance-improvements.md).

## <a name="availability-considerations"></a>Considerazioni sulla disponibilità

Al momento della scrittura, il contratto di servizio (SLA, Service Level Agreement) per Gestione API di Azure definisce lo standard di 99,9% per i livelli Basic, Standard e Premium. Le configurazioni del livello Premium con la distribuzione di almeno un'unità in due o più aree sono associate a un contratto di servizio con una disponibilità di almeno 99,95%.

Al momento della scrittura, il contratto di servizio (SLA, Service Level Agreement) per App per la logica di Azure definisce lo standard di 99,9%.

### <a name="disaster-recovery"></a>Ripristino di emergenza

È consigliabile implementare il ripristino di emergenza geografico nel livello Premium del bus di servizio per abilitare il failover in caso di gravi interruzioni. Altre informazioni su [Ripristino di emergenza geografico per il bus di servizio di Azure](../service-bus-messaging/service-bus-geo-dr.md).

## <a name="manageability-considerations"></a>Considerazioni sulla gestibilità

Creare gruppi di risorse separati per gli ambienti di produzione, sviluppo e test. Questo semplifica la gestione delle distribuzioni, l'eliminazione delle distribuzioni dei test e l'assegnazione dei diritti di accesso.
Quando si assegnano risorse a gruppi di risorse, considerare quanto segue:

- Ciclo di vita. In generale, includere le risorse con un ciclo di vita identico nello stesso gruppo di risorse.
- Accesso. È possibile usare il [controllo degli accessi in base al ruolo](../role-based-access-control/overview.md) per applicare i criteri di accesso alle risorse in un gruppo.
- Fatturazione. È possibile visualizzare il riepilogo dei costi per il gruppo di risorse.
- Piano tariffario per Gestione API: è consigliabile usare un livello Developer per gli ambienti di sviluppo e test. Per la pre-produzione è consigliabile eseguire la distribuzione di una replica dell'ambiente di produzione, eseguire i test e quindi arrestare la replica per ridurre al minimo i costi.

Per altre informazioni, vedere la panoramica del [gruppo di risorse](../azure-resource-manager/resource-group-overview.md).

### <a name="deployment"></a>Distribuzione

È consigliabile usare i [modelli di Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) per distribuire Gestione API di Azure, App per la logica di Azure, Griglia di eventi e il bus di servizio. I modelli semplificano l'automazione delle distribuzioni tramite PowerShell o l'interfaccia della riga di comando di Azure.

È consigliabile inserire Gestione API di Azure e le singole app per la logica, gli argomenti della Griglia di eventi e gli spazi dei nomi del bus di servizio in modelli di Resource Manager separati. In questo modo verranno archiviati nei sistemi del controllo del codice sorgente. Questi modelli possono quindi essere distribuiti insieme o separatamente come parte di un processo di distribuzione continua/integrazione continua (CI/CD).

### <a name="diagnostics-and-monitoring"></a>Diagnostica e monitoraggio

È possibile monitorare il bus di servizio, come Gestione API e App per la logica, usando Monitoraggio di Azure. La piattaforma Monitoraggio di Azure è abilitata per impostazione predefinita e fornirà le informazioni in base alle diverse metriche configurate per ogni servizio.

## <a name="security-considerations"></a>Considerazioni relative alla sicurezza

Proteggere il bus di servizio usando una firma di accesso condiviso. L'[autenticazione della firma di accesso condiviso](../service-bus-messaging/service-bus-sas.md) garantisce l'accesso dell'utente alle risorse del bus di servizio con diritti specifici. Altre informazioni su [Autenticazione e autorizzazione del bus di servizio](../service-bus-messaging/service-bus-authentication-and-authorization.md).

Inoltre, se è necessario esporre una coda del bus di servizio come endpoint HTTP per consentire la registrazione di nuovi messaggi, usare Gestione API per eseguire il fronting dell'endpoint. Per la protezione usare quindi i certificati o lo standard OAuth in base alle esigenze. Il modo più semplice per farlo è usare un'app per la logica con un trigger HTTP Request/Response come intermediario.

Griglia di eventi consente di proteggere il recapito dell'evento tramite un codice di convalida. Se si usa App per la logica per usare l'evento, l'operazione viene eseguita automaticamente. Alti dettagli su [Sicurezza e autenticazione di Griglia di eventi](../event-grid/security-authentication.md).

## <a name="next-steps"></a>Passaggi successivi

* [Integrazione aziendale semplice](logic-apps-architectures-simple-enterprise-integration.md)
