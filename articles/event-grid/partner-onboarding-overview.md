---
title: Eseguire l'onboarding come partner di Griglia di eventi di Azure
description: Eseguire l'onboarding come tipo di argomento partner di Griglia di eventi di Azure. Comprendere il modello di risorse e il flusso di pubblicazione per gli argomenti partner.
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/18/2020
ms.author: babanisa
ms.openlocfilehash: 2a1f35b86e21099c9fdd0397ae8a3b20aed3cd5d
ms.sourcegitcommit: 0690ef3bee0b97d4e2d6f237833e6373127707a7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83758827"
---
# <a name="onboard-as-an-azure-event-grid-partner"></a>Eseguire l'onboarding come partner di Griglia di eventi di Azure

Questo articolo descrive come usare privatamente le risorse partner di Griglia di eventi e come diventare un tipo di argomento partner disponibile pubblicamente.

Non è necessaria un'autorizzazione speciale per iniziare a usare i tipi di risorse di Griglia di eventi associati alla pubblicazione di eventi come partner di Griglia di eventi. In effetti, se si sta valutando la possibilità di diventare partner, è già possibile usarli sia per pubblicare eventi privatamente nelle sottoscrizioni di Azure che per testare il modello di risorse.

## <a name="becoming-an-event-grid-partner"></a>Diventare partner di Griglia di eventi

Se interessa diventare partner pubblico di Griglia di eventi, iniziare compilando [questo modulo](https://aka.ms/gridpartnerform) e quindi contattare il team di Griglia di eventi all'indirizzo [GridPartner@microsoft.com](mailto:gridpartner@microsoft.com).

## <a name="how-partner-topics-work"></a>Come funzionano gli argomenti partner
Gli argomenti partner riprendono l'architettura esistente già usata da Griglia di eventi per pubblicare eventi da risorse di Azure, ad esempio Archiviazione e hub IoT, e rendono tali strumenti disponibili pubblicamente per l'uso da parte di chiunque. Per impostazione predefinita, l'uso di questi strumenti è privato e riservato alla propria sottoscrizione di Azure. Per rendere gli eventi pubblicamente disponibili, compilare il modulo precedente e [contattare il team di Griglia di eventi](mailto:gridpartner@microsoft.com).

Gli argomenti partner consentono di pubblicare eventi in Griglia di eventi di Azure per l'utilizzo multi-tenant.

### <a name="onboarding-and-event-publishing-overview"></a>Panoramica dell'onboarding e della pubblicazione di eventi

#### <a name="partner-flow"></a>Flusso partner

1. Creare un account di Azure, se non se ne ha già uno.
1. Usando l'interfaccia della riga di comando creare una nuova `partnerRegistration` di Griglia di eventi. Questa risorsa include informazioni quali il nome visualizzato, la descrizione, l'URI di installazione e così via.

    ![Creare un argomento partner](./media/partner-onboarding-how-to/create-partner-registration.png)

1. Creare uno o più `partnerNamespaces` in ogni area in cui si vogliono pubblicare eventi. Nell'ambito di questa attività, il servizio Griglia di eventi effettua il provisioning di un endpoint di pubblicazione (ad esempio, `https://contoso.westus-1.eventgrid.azure.net/api/events`) e delle chiavi di accesso.

    ![Creare uno spazio dei nomi partner](./media/partner-onboarding-how-to/create-partner-namespace.png)

1. Offrire ai clienti un metodo per registrare nel sistema che desiderano un argomento partner.
1. Comunicare al team di Griglia di eventi che si desidera che il tipo di argomento partner diventi pubblico.

#### <a name="customer-flow"></a>Flusso del cliente

1. Il cliente visiterà il portale di Azure per annotare l'ID della sottoscrizione di Azure e il gruppo di risorse in cui vuole che venga creato l'argomento partner.
1. Il cliente richiederà un argomento partner tramite il sistema. In risposta, si creerà un tunnel evento per lo spazio dei nomi partner.
1. Griglia di eventi creerà un argomento partner **in sospeso** nella sottoscrizione e nel gruppo di risorse di Azure del cliente.

    ![Creare un canale eventi](./media/partner-onboarding-how-to/create-event-tunnel-partner-topic.png)

1. Il cliente attiva l'argomento partner tramite il portale di Azure. Gli eventi possono ora fluire dal servizio alla sottoscrizione di Azure del cliente.

    ![Attivare l'argomento partner](./media/partner-onboarding-how-to/activate-partner-topic.png)

## <a name="resource-model"></a>Modello di risorsa

Di seguito è riportato il modello di risorsa per gli argomenti partner.

### <a name="partner-registrations"></a>Registrazioni dei partner
* Risorsa: `partnerRegistrations`
* Usata da: Partner
* Descrizione: Acquisisce i metadati globali del partner SaaS, ad esempio nome, nome visualizzato, descrizione, URI di installazione.
    
    La creazione e l'aggiornamento di una registrazione partner è un'operazione self-service per i partner. Questa funzionalità self-service consente ai partner di creare e testare il flusso end-to-end completo.
    
    Solo risorse partnerRegistrations approvate da Microsoft sono individuabili da parte dei clienti.
* Ambito: Creata nella sottoscrizione di Azure del partner. Una volta pubblici, i metadati sono visibili per i clienti.

### <a name="partner-namespaces"></a>Spazi dei nomi dei partner
* Risorsa: partnerNamespaces
* Usata da: Partner
* Descrizione: Fornisce una risorsa a livello di area per la pubblicazione di eventi del cliente. Ogni spazio dei nomi partner ha un endpoint di pubblicazione e chiavi di autenticazione. Lo spazio dei nomi è anche il mezzo con cui il partner richiede un argomento partner per un cliente specifico ed elenca i clienti attivi.
* Ambito: Risiede nella sottoscrizione del partner.

### <a name="event-channel"></a>Canale eventi
* Risorsa: `partnerNamespaces/eventChannels`
* Usata da: Partner
* Descrizione: I tunnel eventi sono un mirror dell'argomento partner del cliente. Creando un tunnel eventi e specificando la sottoscrizione e il gruppo di risorse di Azure del cliente nei metadati, si indica a Griglia di eventi di creare un argomento partner per il cliente. Griglia di eventi genererà una chiamata ARM per creare una risorsa partnerTopic corrispondente nella sottoscrizione del cliente. L'argomento partner verrà creato con stato "In sospeso". Esiste un collegamento 1-1 tra ogni risorsa eventTunnel e una risorsa partnerTopic.
* Ambito: Risiede nella sottoscrizione del partner.

### <a name="partner-topics"></a>Argomenti partner
* Risorsa: `partnerTopics`
* Usata da: Clienti
* Descrizione: Gli argomenti partner sono simili agli argomenti personalizzati e agli argomenti di sistema in Griglia di eventi. Ogni argomento partner è associato a un'"origine" specifica (ad esempio `Contoso:myaccount`) e a un tipo di argomento partner specifico (ad esempio, "Contoso"). I clienti creano sottoscrizioni di eventi nell'argomento partner per indirizzare gli eventi a diversi gestori dell'evento.

    I clienti non possono creare direttamente questa risorsa. L'unico modo per creare un argomento partner è tramite un'operazione partner di creazione di un tunnel eventi.
* Ambito: Risiede nella sottoscrizione del cliente.

### <a name="partner-topic-types"></a>Tipi di argomento partner
* Risorsa: `partnerTopicTypes`
* Usata da: Clienti
* Descrizione: I tipi di argomento partner sono tipi di risorsa a livello di tenant che consentono ai clienti di individuare l'elenco dei tipi di argomento partner approvati. L'URL ha l'aspetto seguente: https://management.azure.com/providers/Microsoft.EventGrid/partnerTopicTypes)
* Ambito: Global

## <a name="publishing-events-to-event-grid"></a>Pubblicazione di eventi in Griglia di eventi
Quando si crea una risorsa partnerNamespace in un'area di Azure, si ottiene un endpoint a livello di area e le chiavi di autenticazione corrispondenti. Pubblicare batch di eventi in questo endpoint per i tunnel eventi del cliente in tale spazio dei nomi. In base al campo di "origine" nell'evento, Griglia di eventi di Azure esegue il mapping di ogni evento agli argomenti partner corrispondenti.

### <a name="event-schema-cloudevents-v10"></a>Schema di eventi: CloudEvents v1.0
Pubblicare eventi in Griglia di eventi di Azure usando lo schema CloudEvents 1.0. Griglia di eventi supporta sia la modalità strutturata che la modalità in batch. CloudEvents 1.0 è l'unico schema di eventi supportato per gli spazi dei nomi dei partner.

### <a name="example-flow"></a>Esempio di flusso

1.  Il servizio di pubblicazione esegue una richiesta POST HTTP a `https://contoso.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01`.
2.  Nella richiesta includere il valore di intestazione aeg-sas-key che contiene una chiave per l'autenticazione. Il provisioning di questa chiave viene effettuato durante la creazione della risorsa partnerNamespace. Un valore valido, ad esempio, è aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==.
3.  Impostare l'intestazione Content-Type su "application/cloudevents-batch+json; charset=UTF-8".
4.  Eseguire una richiesta POST HTTP all'URL di pubblicazione precedente con un batch di eventi corrispondente all'area. Ad esempio:

``` json
[
{
    "specversion" : "1.0-rc1",
    "type" : "com.contoso.ticketcreated",
    "source" : " com.contoso.account1",
    "subject" : "tickets/123",
    "id" : "A234-1234-1234",
    "time" : "2019-04-05T17:31:00Z",
    "comexampleextension1" : "value",
    "comexampleothervalue" : 5,
    "datacontenttype" : "application/json",
    "data" : {
          object-unique-to-each-publisher
    }
},
{
    "specversion" : "1.0-rc1",
    "type" : "com.contoso.ticketclosed",
    "source" : "https://contoso.com/account2",
    "subject" : "tickets/456",
    "id" : "A234-1234-1234",
    "time" : "2019-04-05T17:31:00Z",
    "comexampleextension1" : "value",
    "comexampleothervalue" : 5,
    "datacontenttype" : "application/json",
    "data" : {
          object-unique-to-each-publisher
    }
}
]
```

Dopo la pubblicazione nell'endpoint partnerNamespace, si riceve una risposta. La risposta è un codice di risposta HTTP standard. Alcune risposte comuni sono:

| Risultato                             | Risposta              |
|------------------------------------|-----------------------|
| Operazione completata                            | 200 - OK                |
| I dati di evento hanno un formato non corretto    | 400 - Richiesta non valida       |
| Chiave di accesso non valida                 | 401 - Non autorizzato      |
| Endpoint non corretto                 | 404 - Non trovato         |
| Una matrice o un evento supera i limiti delle dimensioni | 413 Payload Too Large (413 Payload troppo grande) |

## <a name="reference"></a>Informazioni di riferimento

  * [Swagger](https://github.com/ahamad-MS/azure-rest-api-specs/blob/master/specification/eventgrid/resource-manager/Microsoft.EventGrid/preview/2020-04-01-preview/EventGrid.json)
  * [Modello ARM](https://docs.microsoft.com/azure/templates/microsoft.eventgrid/allversions)
  * [Schema del modello ARM](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2020-04-01-preview/Microsoft.EventGrid.json)
  * [API REST](https://docs.microsoft.com/rest/api/eventgrid/version2020-04-01-preview/partnernamespaces)
  * [Estensione dell'interfaccia della riga di comando](https://docs.microsoft.com/cli/azure/ext/eventgrid/?view=azure-cli-latest)

### <a name="sdks"></a>SDK
  * [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid/5.3.1-preview)
  * [Python](https://pypi.org/project/azure-mgmt-eventgrid/3.0.0rc6/)
  * [Java](https://search.maven.org/artifact/com.microsoft.azure.eventgrid.v2020_04_01_preview/azure-mgmt-eventgrid/1.0.0-beta-3/jar)
  * [Ruby](https://rubygems.org/gems/azure_mgmt_event_grid/versions/0.19.0)
  * [JS](https://www.npmjs.com/package/@azure/arm-eventgrid/v/7.0.0)
  * [Go](https://github.com/Azure/azure-sdk-for-go)


## <a name="next-steps"></a>Passaggi successivi
- [Panoramica degli argomenti partner](partner-topics-overview.md)
- [Modulo di onboarding per gli argomenti partner](https://aka.ms/gridpartnerform)
- [Argomento partner Auth0](auth0-overview.md)
- [Come usare l'argomento partner Auth0](auth0-how-to.md)
