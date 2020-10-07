---
title: Novità Note sulla versione - Griglia di eventi di Azure
description: Informazioni sulle novità di Griglia di eventi di Azure, tra cui le note sulla versione più aggiornate, i problemi noti, le correzioni di bug, le funzionalità deprecate e le modifiche imminenti.
ms.topic: overview
ms.date: 07/23/2020
ms.openlocfilehash: 1edfa3e2bc4c8adae113b2215b7fb0483fba4c02
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "87172700"
---
# <a name="whats-new-in-azure-event-grid"></a>Novità di Griglia di eventi di Azure

>Per ricevere notifiche che indicano che occorre visitare di nuovo questa pagina per visualizzare gli aggiornamenti, copiare e incollare questo URL `https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Event+Grid%22&locale=en-us` nel lettore di feed ![icona del lettore di feed RSS](./media/whats-new/feed-icon-16x16.png).

Griglia di eventi di Azure viene regolarmente migliorato. Per stare al passo con gli sviluppi più recenti, questo articolo fornisce informazioni sugli argomenti seguenti:

- Versioni più recenti
- Problemi noti
- Correzioni di bug
- Funzionalità deprecate
- Modifiche pianificate

## <a name="600-2020-06"></a>6.0.0 (2020-06)
- Aggiunta di supporto alla nuova versione dell'API del servizio 2020-06-01 disponibile a livello generale.
- Queste nuove funzionalità sono ora disponibili a livello generale:
    - [Mapping di input](input-mappings.md)
    - [Schema di input personalizzato](input-mappings.md)
    - [Schema evento cloud v10](cloud-event-schema.md)
    - [Argomento del bus di servizio come destinazione](handler-service-bus.md)
    - [Funzione di Azure come destinazione](handler-functions.md)
    - [Invio in batch di webhook](./edge/delivery-output-batching.md)
    - [Webhook protetto (supporto di Azure Active Directory)](secure-webhook-delivery.md)
    - [Filtri IP](configure-firewall.md)
    - [Supporto del servizio Collegamento privato](configure-private-endpoints.md)
    - [Scema di recapito di eventi](event-schema.md)

## <a name="532-preview-2020-05"></a>5.3.2-preview (2020-05)
- Questa release include correzioni aggiuntive di bug per migliorare la qualità.
- Questa release, con versione 5.3.1-preview, corrisponde alla versione 2020-04-01-Preview dell'API che include le nuove funzionalità seguenti: 
    - [Supporto per i filtri IP durante la pubblicazione di eventi in domini e argomenti](configure-firewall.md)
    - [Argomenti partner](partner-topics-overview.md)
    - [Argomenti di sistema come risorse rilevate nel portale di Azure](system-topics.md)
    - [Distribuzione di eventi con l'identità del servizio gestita](managed-service-identity.md) 
    - [Supporto del servizio Collegamento privato](configure-private-endpoints.md)

## <a name="531-preview-2020-04"></a>5.3.1-preview (2020-04)
- Questa release include diverse correzioni di bug per migliorare la qualità.
- Questa release, con versione 5.3.0-preview, corrisponde alla versione 2020-04-01-Preview dell'API che include le nuove funzionalità seguenti: 
    - [Supporto per i filtri IP durante la pubblicazione di eventi in domini e argomenti](configure-firewall.md)
    - [Argomenti partner](partner-topics-overview.md)
    - [Argomenti di sistema come risorse rilevate nel portale di Azure](system-topics.md)
    - [Distribuzione di eventi con l'identità del servizio gestita](managed-service-identity.md) 
    - [Supporto del servizio Collegamento privato](configure-private-endpoints.md)

## <a name="530-preview-2020-03"></a>5.3.0-preview (2020-03)
- Vengono introdotte nuove funzionalità oltre alle funzionalità già aggiunte nella versione 5.2.0-preview. 
- Questa release, con versione 5.2.0-preview, corrisponde alla versione 2020-04-01-Preview dell'API.
- Aggiunge supporto per le nuove funzionalità seguenti: 
    - [Supporto per i filtri IP durante la pubblicazione di eventi in domini e argomenti](configure-firewall.md)
    - [Argomenti partner](partner-topics-overview.md)
    - [Argomenti di sistema come risorse rilevate nel portale di Azure](system-topics.md)
    - [Distribuzione di eventi con l'identità del servizio gestita](managed-service-identity.md) 
    - [Supporto del servizio Collegamento privato](configure-private-endpoints.md)

## <a name="520-preview-2020-01"></a>5.2.0-preview (2020-01)
- Questa release corrisponde alla versione 2020-04-01-Preview dell'API.
- Aggiunge supporto per la nuova funzionalità seguente:
    - [Supporto per i filtri IP durante la pubblicazione di eventi in domini e argomenti](configure-firewall.md)

## <a name="500-2019-05"></a>5.0.0 (2019-05)
- Questa release corrisponde alla versione `2019-06-01` dell'API.
- Aggiunge supporto per le nuove funzionalità seguenti:
    * [Domini](event-domains.md)
    * Paginazione e filtro di ricerca per operazioni dell'elenco di risorse. Per un esempio, vedere [Argomento - Elenco in base a sottoscrizione](/rest/api/eventgrid/version2020-04-01-preview/topics/listbysubscription).
    * [Coda del bus di servizio come destinazione](handler-service-bus.md)
    * [Filtro avanzato](event-filtering.md#advanced-filtering)

## <a name="410-preview-2019-03"></a>4.1.0-preview (2019-03)
- Questa release corrisponde alla versione 2019-02-01-preview dell'API.
- Aggiunge supporto per le nuove funzionalità seguenti:
    * Paginazione e filtro di ricerca per operazioni dell'elenco di risorse. Per un esempio, vedere [Argomento - Elenco in base a sottoscrizione](/rest/api/eventgrid/version2020-04-01-preview/topics/listbysubscription).
    * [Creazione/Eliminazione manuale degli argomenti del dominio](how-to-event-domains.md)
    * [Coda del bus di servizio come destinazione](handler-service-bus.md)

## <a name="400-2018-12"></a>4.0.0 (2018-12)
- Questa release corrisponde alla versione stabile `2019-01-01` dell'API.
- Supporta la disponibilità generale delle funzionalità seguenti correlate alle sottoscrizioni di eventi:
    * [Destinazione di messaggi non recapitabili](manage-event-delivery.md)
    * [Coda di Archiviazione di Azure come destinazione](handler-storage-queues.md)
    * [Inoltro di Azure - Connessione ibrida come destinazione](handler-relay-hybrid-connections.md)
    * [Convalida dell'handshake manuale](webhook-event-delivery.md)
    * [Supporto per i criteri di ripetizione dei tentativi](delivery-and-retry.md)
- Le funzionalità ancora disponibili in anteprima, ad esempio [domini di Griglia di eventi](event-domains.md) o [supporto per il filtro avanzato](event-filtering.md#advanced-filtering), sono accessibili tramite la versione 3.0.1-preview dell'SDK."

## <a name="301-preview-2018-10"></a>3.0.1-preview (2018-10)
- Accettazione della dipendenza dalla [versione 10.0.3 del pacchetto Newtonsoft NuGet](https://www.nuget.org/packages/Newtonsoft.Json/10.0.3).

## <a name="300-preview-2018-10"></a>3.0.0-preview (2018-10)
- Questa release è un SDK in anteprima per le nuove funzionalità introdotte nella versione 2018-09-15-preview dell'API. - Questa versione include il supporto per:
    - [Dominio e argomenti del dominio](event-domains.md)
    - Introduzione della [data di scadenza per la sottoscrizione di eventi](concepts.md#event-subscription-expiration)
    - Introduzione dei [filtri avanzati](event-filtering.md#advanced-filtering) per le sottoscrizioni di eventi
    - La versione stabile dell'SDK che fa riferimento alla versione `2018-01-01` dell'API continua a esistere come versione 1.3.0

## <a name="next-steps"></a>Passaggi successivi
