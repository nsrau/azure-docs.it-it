---
title: 'Griglia di eventi di Azure: eventi del partner'
description: Inviare eventi da partner SaaS e PaaS di Griglia di eventi di terze parti direttamente a servizi di Azure con Griglia di eventi di Azure.
ms.topic: conceptual
ms.date: 11/10/2020
ms.openlocfilehash: 31a5fe611871eb4734b6a68e3818592028ebc75c
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/11/2020
ms.locfileid: "94506147"
---
# <a name="partner-events-in-azure-event-grid-preview"></a>Eventi del partner in griglia di eventi di Azure (anteprima)
La funzionalità **eventi partner** consente a un provider SaaS di terze parti di pubblicare eventi dai servizi in modo che i consumer possano effettuare la sottoscrizione a tali eventi. Questa funzionalità offre un'esperienza iniziale alle origini eventi di terze parti esponendo un tipo di [argomento](concepts.md#topics) , un **argomento del partner**. Per utilizzare gli eventi, i sottoscrittori creano sottoscrizioni a questo argomento. Fornisce inoltre un modello di pubblicazione-sottoscrizione pulito, separando i problemi e la proprietà delle risorse utilizzate dai Publisher e dai sottoscrittori di eventi.

> [!NOTE]
> Se non si ha familiarità con l'uso di griglia di eventi, vedere [Panoramica](overview.md), [concetti](concepts.md)e [gestori eventi](event-handlers.md).

## <a name="what-is-partner-events-to-a-publisher"></a>Che cosa sono gli eventi partner per un server di pubblicazione?
Per gli autori di eventi, la funzionalità eventi partner consente agli editori di eseguire le attività seguenti:

- Caricare le origini eventi in griglia di eventi
- Creare uno spazio dei nomi (endpoint) in cui pubblicare gli eventi
- Creare argomenti del partner in Azure che i sottoscrittori possiedano e usino per usare gli eventi

## <a name="what-is-partner-events-to-a-subscriber"></a>Che cosa sono gli eventi partner per un Sottoscrittore?
Per un Sottoscrittore, la funzionalità eventi partner consente loro di creare argomenti dei partner in Azure per l'utilizzo di eventi da origini di eventi di terze parti. L'utilizzo degli eventi viene realizzato creando sottoscrizioni di eventi che inviano (push) eventi al gestore eventi di un Sottoscrittore.

## <a name="why-should-i-use-partner-events"></a>Perché usare gli eventi partner?
È possibile utilizzare gli eventi partner se si dispone di uno o più dei requisiti seguenti.

### <a name="for-publishers"></a>Per gli editori

- Si vuole un meccanismo per rendere disponibili gli eventi in Azure. Gli utenti possono filtrare e instradare tali eventi usando gli argomenti dei partner e le sottoscrizioni di eventi che possiedono e gestiscono. È possibile utilizzare altri approcci di integrazione, ad esempio [argomenti](custom-topics.md) e [domini](event-domains.md). Tuttavia, non consentiranno una netta separazione della proprietà, della gestione e della fatturazione tra autori e sottoscrittori. Questo approccio offre inoltre un'esperienza utente più intuitiva che semplifica l'individuazione e l'utilizzo degli argomenti del partner.
- Si desidera pubblicare eventi in un singolo endpoint, ovvero l'endpoint dello spazio dei nomi. Si desidera quindi filtrare gli eventi in modo che siano disponibili solo un subset di tali eventi. 
- Si vuole avere visibilità sulle metriche relative agli eventi pubblicati.
- Si vuole usare [gli eventi Cloud 1,0](https://cloudevents.io/) schema per gli eventi.

### <a name="for-subscribers"></a>Per Sottoscrittori

- Si vuole sottoscrivere gli eventi di [autori di terze parti](#available-third-party-event-publishers) e gestire gli eventi usando i gestori eventi che si trovano in Azure o altrove.
- Si desidera sfruttare il set completo di funzionalità di routing e [destinazioni/gestori eventi](overview.md#event-handlers) per elaborare gli eventi da origini di terze parti. 
- Si desidera implementare architetture a regime di controllo libero in cui il gestore dell'evento o del Sottoscrittore non è a conoscenza dell'esistenza del broker di messaggi utilizzato. 
- È necessario un meccanismo di recapito push resiliente con supporto per i tentativi di invio e semantica at-least-once.
- Si vuole usare [gli eventi Cloud 1,0](https://cloudevents.io/) schema per gli eventi. 


## <a name="available-third-party-event-publishers"></a>Autori di eventi di terze parti disponibili
Un autore di eventi di terze parti deve eseguire un [processo di onboarding](partner-onboarding-overview.md) prima che un Sottoscrittore possa iniziare a usare gli eventi. 

Se si è un Sottoscrittore e si vuole che un servizio di terze parti esponga gli eventi tramite griglia di eventi, 

### <a name="auth0"></a>Auth0
**Auth0** è il primo editore partner disponibile. È possibile creare un [argomento del partner Auth0](auth0-overview.md) per connettere gli account Auth0 e Azure. Questa integrazione consente di reagire, registrare e monitorare gli eventi Auth0 in tempo reale. Per provarlo, vedere [integrare griglia di eventi di Azure con Auto0](auth0-how-to.md)

Se si vuole che un servizio di terze parti esponga gli eventi tramite griglia di eventi, inviare l'idea nel portale per la [voce dell'utente](https://feedback.azure.com/forums/909934-azure-event-grid).
 
## <a name="resources-managed-by-event-publishers"></a>Risorse gestite da autori di eventi
I publisher di eventi creano e gestiscono le risorse seguenti:

### <a name="partner-registration"></a>Registrazione partner
Una registrazione include informazioni generali relative a un server di pubblicazione. Definisce un tipo di argomento partner che mostra nel portale di Azure come opzione quando gli utenti tentano di creare un argomento del partner. Un server di pubblicazione può esporre più di uno o più tipi di argomento partner per soddisfare le esigenze dei propri Sottoscrittori. Ovvero un server di pubblicazione può creare registrazioni separate (tipi di argomento partner) per gli eventi di diversi servizi. Ad esempio, per il servizio risorse umane (HR), il server di pubblicazione può definire un argomento partner per eventi quali il dipendente aggiunto, il dipendente promosso e il dipendente che ha lasciato l'azienda. 

Tenere presente quanto segue:

- Sono visibili solo le registrazioni partner approvate da Azure. 
- Le registrazioni sono globali. Ovvero non sono associati a una determinata area di Azure.
- Una registrazione è una risorsa facoltativa. Tuttavia, si consiglia di creare una registrazione (come autore). Consente agli utenti di individuare gli argomenti nella pagina **Crea argomento partner** del [portale di Azure](https://portal.azure.com/#create/Microsoft.EventGridPartnerTopic). Quindi, l'utente può selezionare i tipi di evento (ad esempio, Employee join, Employee Left e così via) durante la creazione delle sottoscrizioni di eventi.

### <a name="namespace"></a>Spazio dei nomi
Analogamente ad argomenti e [domini](event-domains.md) [personalizzati](custom-topics.md) , uno spazio dei nomi partner è un endpoint a livello di area per la pubblicazione di eventi. Si tratta di spazi dei nomi che i Publisher creano e gestiscono canali di eventi. Uno spazio dei nomi funge anche da risorsa contenitore per i canali di eventi.

### <a name="event-channels"></a>Canali evento
Un canale di eventi è una risorsa con mirroring per un argomento del partner. Quando un server di pubblicazione crea un canale di eventi nella sottoscrizione di Azure del server di pubblicazione, crea anche un argomento partner nella sottoscrizione di Azure di un Sottoscrittore. Le operazioni eseguite su un canale di eventi (ad eccezione di GET) verranno applicate al corrispondente argomento del partner del Sottoscrittore, anche all'eliminazione. Tuttavia, solo gli argomenti del partner sono il tipo di risorse in cui è possibile configurare le sottoscrizioni e il recapito degli eventi.

## <a name="resources-managed-by-subscribers"></a>Risorse gestite dai Sottoscrittori 
Gli abbonati possono usare gli argomenti dei partner definiti da un editore ed è l'unico tipo di risorsa che vedono e gestiscono. Una volta creato un argomento partner, un utente del Sottoscrittore può creare sottoscrizioni di eventi che definiscono regole di filtro per [destinazioni e gestori eventi](overview.md#event-handlers). Per i sottoscrittori, un argomento partner e le sottoscrizioni di eventi associate forniscono le stesse funzionalità avanzate degli [argomenti personalizzati](custom-topics.md) e delle sottoscrizioni correlate con una differenza rilevante: gli argomenti del partner supportano solo lo [schema degli eventi Cloud 1,0](cloudevents-schema.md), che fornisce un set più completo di funzionalità rispetto ad altri schemi supportati.

Nell'immagine seguente viene illustrato il flusso delle operazioni del piano di controllo.

:::image type="content" source="./media/partner-events-overview/partner-control-plane-flow.png" alt-text="Eventi del partner-flusso del piano di controllo":::

1. Publisher crea una **registrazione partner**. Le registrazioni partner sono globali. Ovvero non sono associate a una determinata area di Azure. Questo passaggio è facoltativo.
1. Publisher crea uno **spazio dei nomi partner** in un'area specifica.
1. Quando il Sottoscrittore 1 tenta di creare un argomento del partner, viene creato per primo un **canale di eventi** , il canale di eventi 1, nella sottoscrizione di Azure del server di pubblicazione.
1. Viene quindi creato un **argomento partner** , argomento partner 1, nella sottoscrizione di Azure del Sottoscrittore. Il Sottoscrittore deve attivare l'argomento partner. 
1. Il Sottoscrittore 1 crea una sottoscrizione di app per la **logica di Azure** all'argomento del partner 1.
1. Il Sottoscrittore 1 crea una **sottoscrizione di archiviazione BLOB di Azure** per l'argomento del partner 1. 
1. Quando il Sottoscrittore 2 tenta di creare un argomento del partner, viene creato un altro **canale di eventi** , il canale di eventi 2, nella sottoscrizione di Azure del server di pubblicazione. 
1. Quindi, l' **argomento del partner** , argomento del partner 2, viene creato nella sottoscrizione di Azure del secondo sottoscrittore. Il Sottoscrittore deve attivare l'argomento partner. 
1. Il Sottoscrittore 2 crea una **sottoscrizione di funzioni di Azure** per l'argomento 2 del partner. 

## <a name="pricing"></a>Prezzi
Per gli argomenti relativi ai partner viene addebitato il numero di operazioni eseguite quando si usa griglia di eventi. Per ulteriori informazioni su tutti i tipi di operazioni utilizzate come base per la fatturazione e informazioni dettagliate sui prezzi, vedere [prezzi di griglia di eventi](https://azure.microsoft.com/pricing/details/event-grid/).

## <a name="limits"></a>Limiti
Per informazioni dettagliate sui limiti per gli argomenti dei partner, vedere [limiti del servizio griglia di eventi](../azure-resource-manager/management/azure-subscription-service-limits.md#event-grid-limits) .


## <a name="next-steps"></a>Passaggi successivi

- [Argomento del partner Auth0](auth0-overview.md)
- [Come usare l'argomento partner Auth0](auth0-how-to.md)
- [Diventare un partner di Griglia di eventi](partner-onboarding-overview.md)