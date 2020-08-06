---
title: Eseguire l'onboarding come partner di Griglia di eventi di Azure
description: Onboarding come tipo di argomento del partner di griglia di eventi di Azure. Informazioni sul modello di risorse e sul flusso di pubblicazione per gli argomenti del partner.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 36f2178b7c21af016f9074d6f973a01cedb873d7
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87826790"
---
# <a name="onboard-as-an-azure-event-grid-partner"></a>Eseguire l'onboarding come partner di Griglia di eventi di Azure

Questo articolo descrive come usare privatamente le risorse del partner di griglia di eventi di Azure e come diventare un tipo di argomento partner disponibile pubblicamente.

Non è necessaria un'autorizzazione speciale per iniziare a usare i tipi di risorse di Griglia di eventi associati alla pubblicazione di eventi come partner di Griglia di eventi. Attualmente, è possibile usarli per pubblicare gli eventi privatamente nelle sottoscrizioni di Azure e per testare il modello di risorse se si sta valutando la possibilità di diventare partner.

## <a name="become-an-event-grid-partner"></a>Diventa un partner di griglia di eventi

Se si è interessati a diventare un partner di griglia di eventi pubblici, iniziare compilando [questo modulo](https://aka.ms/gridpartnerform). Contattare il team di griglia di eventi all'indirizzo [GridPartner@microsoft.com](mailto:gridpartner@microsoft.com) .

## <a name="how-partner-topics-work"></a>Come funzionano gli argomenti partner
Gli argomenti dei partner prendono l'architettura esistente che griglia di eventi USA già per pubblicare gli eventi dalle risorse di Azure, ad esempio archiviazione di Azure e l'hub Azure e rende disponibili pubblicamente tali strumenti per chiunque possa usare. Per impostazione predefinita, l'uso di questi strumenti è privato solo per la sottoscrizione di Azure. Per rendere disponibili pubblicamente gli eventi, compila il modulo e [Contatta il team di griglia di eventi](mailto:gridpartner@microsoft.com).

Gli argomenti partner consentono di pubblicare eventi in Griglia di eventi di Azure per l'utilizzo multi-tenant.

### <a name="onboarding-and-event-publishing-overview"></a>Panoramica dell'onboarding e della pubblicazione di eventi

#### <a name="partner-flow"></a>Flusso partner

1. Creare un account di Azure, se non se ne ha già uno.
1. Usare l'interfaccia della riga di comando di Azure per creare una nuova griglia di eventi `partnerRegistration` . Questa risorsa include informazioni quali il nome visualizzato, la descrizione, l'URI di installazione e così via.

    ![Creazione di un argomento partner](./media/partner-onboarding-how-to/create-partner-registration.png)

1. Creare uno o più spazi dei nomi partner in ogni area in cui si desidera pubblicare gli eventi. Il servizio griglia di eventi effettua il provisioning di un endpoint di pubblicazione (ad esempio, `https://contoso.westus-1.eventgrid.azure.net/api/events` ) e delle chiavi di accesso.

    ![Creare uno spazio dei nomi partner](./media/partner-onboarding-how-to/create-partner-namespace.png)

1. Consente ai clienti di eseguire la registrazione nel sistema che vogliono un argomento partner.
1. Contattare il team di griglia di eventi per indicare che si desidera che il tipo di argomento del partner diventi pubblico.

#### <a name="customer-flow"></a>Flusso del cliente

1. Il cliente visita il portale di Azure per prendere nota dell'ID sottoscrizione di Azure e del gruppo di risorse in cui si vuole creare l'argomento del partner.
1. Il cliente richiede un argomento del partner tramite il sistema. In risposta, si crea un tunnel di eventi per lo spazio dei nomi del partner.
1. Griglia di eventi crea un argomento partner in **sospeso** nella sottoscrizione e nel gruppo di risorse di Azure del cliente.

    ![Creare un canale di eventi](./media/partner-onboarding-how-to/create-event-tunnel-partner-topic.png)

1. Il cliente attiva l'argomento del partner tramite il portale di Azure. Gli eventi possono ora fluire dal servizio alla sottoscrizione di Azure del cliente.

    ![Attivare un argomento del partner](./media/partner-onboarding-how-to/activate-partner-topic.png)

## <a name="resource-model"></a>Modello di risorsa


Il modello di risorse seguente è per gli argomenti del partner.

### <a name="partner-registrations"></a>Registrazioni partner
* Risorsa: `partnerRegistrations`
* Usata da: Partner
* Descrizione: acquisisce i metadati globali del partner Software as a Service (SaaS), ad esempio nome, nome visualizzato, descrizione, URI di installazione.
    
    La creazione o l'aggiornamento di una registrazione partner è un'operazione self-service per i partner. Questa funzionalità self-service consente ai partner di compilare e testare il flusso completo end-to-end.
    
    Solo le registrazioni dei partner approvate da Microsoft sono individuabili dai clienti.
* Ambito: creato nella sottoscrizione di Azure del partner. I metadati sono visibili ai clienti dopo che sono stati resi pubblici.

### <a name="partner-namespaces"></a>Spazi dei nomi partner
* Risorsa: partnerNamespaces
* Usata da: Partner
* Descrizione: Fornisce una risorsa a livello di area per la pubblicazione di eventi del cliente. Ogni spazio dei nomi partner ha un endpoint di pubblicazione e le chiavi di autenticazione. Lo spazio dei nomi è anche il modo in cui il partner richiede un argomento partner per un determinato cliente ed elenca i clienti attivi.
* Ambito: risiede nella sottoscrizione del partner.

### <a name="event-channel"></a>Canale eventi
* Risorsa: `partnerNamespaces/eventChannels`
* Usata da: Partner
* Descrizione: i tunnel di eventi sono un mirror dell'argomento del partner del cliente. Creando un tunnel di eventi e specificando la sottoscrizione e il gruppo di risorse di Azure del cliente nei metadati, si segnala a griglia di eventi di creare un argomento del partner per il cliente. Griglia di eventi invia una chiamata ARM per creare un partnerTopic corrispondente nella sottoscrizione del cliente. L'argomento del partner viene creato in uno stato in sospeso. C'è un collegamento uno-a-uno tra ogni argomento del tunnel di eventi e del partner.
* Ambito: risiede nella sottoscrizione del partner.

### <a name="partner-topics"></a>Argomenti per i partner
* Risorsa: `partnerTopics`
* Usata da: Clienti
* Descrizione: gli argomenti del partner sono simili agli argomenti personalizzati e agli argomenti di sistema in griglia di eventi. Ogni argomento del partner è associato a un'origine specifica (ad esempio, `Contoso:myaccount` ) e a un tipo di argomento partner specifico, ad esempio contoso. I clienti creano sottoscrizioni di eventi nell'argomento partner per indirizzare gli eventi a diversi gestori eventi.

    I clienti non possono creare direttamente questa risorsa. L'unico modo per creare un argomento partner è tramite un'operazione del partner che crea un tunnel di eventi.
* Ambito: risiede nella sottoscrizione del cliente.

### <a name="partner-topic-types"></a>Tipi di argomento partner
* Risorsa: `partnerTopicTypes`
* Usata da: Clienti
* Descrizione: i tipi di argomento partner sono tipi di risorse a livello di tenant che consentono ai clienti di individuare l'elenco dei tipi di argomento Partner approvati. L'URL è simile ahttps://management.azure.com/providers/Microsoft.EventGrid/partnerTopicTypes)
* Ambito: Global

## <a name="publish-events-to-event-grid"></a>Pubblica eventi in griglia di eventi
Quando si crea uno spazio dei nomi partner in un'area di Azure, si ottiene un endpoint regionale e le chiavi di autenticazione corrispondenti. Consente di pubblicare batch di eventi in questo endpoint per tutti i tunnel di eventi del cliente nello spazio dei nomi. In base al campo di origine nell'evento, griglia di eventi di Azure esegue il mapping di ogni evento con gli argomenti partner corrispondenti.

### <a name="event-schema-cloudevents-v10"></a>Schema dell'evento: CloudEvents v 1.0
Pubblicare eventi in griglia di eventi di Azure usando lo schema CloudEvents 1,0. Griglia di eventi supporta sia la modalità strutturata che la modalità in batch. CloudEvents 1,0 è l'unico schema di eventi supportato per gli spazi dei nomi del partner.

### <a name="example-flow"></a>Flusso di esempio

1.  Il servizio di pubblicazione esegue una richiesta POST HTTP a `https://contoso.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01`.
1.  Nella richiesta includere il valore di intestazione aeg-sas-key che contiene una chiave per l'autenticazione. Viene eseguito il provisioning di questa chiave durante la creazione dello spazio dei nomi partner. Un valore valido, ad esempio, è aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==.
1.  Impostare l'intestazione Content-Type su "Application/cloudevents-batch + JSON; charset = UTF-8a ".
1.  Eseguire un POST HTTP nell'URL di pubblicazione con un batch di eventi corrispondenti a tale area. Ad esempio:

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

Dopo aver pubblicato l'endpoint partnerNamespace, si riceve una risposta. La risposta è un codice di risposta HTTP standard. Alcune risposte comuni sono:

| Risultato                             | Risposta              |
|------------------------------------|-----------------------|
| Operazione completata                            | 200 - OK                |
| I dati di evento hanno un formato non corretto    | 400 - Richiesta non valida       |
| Chiave di accesso non valida                 | 401 - Non autorizzato      |
| Endpoint non corretto                 | 404 - Non trovato         |
| Una matrice o un evento supera i limiti delle dimensioni | 413 Payload Too Large (413 Payload troppo grande) |

## <a name="references"></a>Riferimenti

  * [Swagger](https://github.com/ahamad-MS/azure-rest-api-specs/blob/master/specification/eventgrid/resource-manager/Microsoft.EventGrid/preview/2020-04-01-preview/EventGrid.json)
  * [Modello ARM](/azure/templates/microsoft.eventgrid/allversions)
  * [Schema modello ARM](https://github.com/Azure/azure-resource-manager-schemas/blob/master/schemas/2020-04-01-preview/Microsoft.EventGrid.json)
  * [API REST](/rest/api/eventgrid/version2020-04-01-preview/partnernamespaces)
  * [Estensione dell'interfaccia della riga di comando](/cli/azure/ext/eventgrid/?view=azure-cli-latest)

### <a name="sdks"></a>SDK
  * [.NET](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid/5.3.1-preview)
  * [Python](https://pypi.org/project/azure-mgmt-eventgrid/3.0.0rc6/)
  * [Java](https://search.maven.org/artifact/com.microsoft.azure.eventgrid.v2020_04_01_preview/azure-mgmt-eventgrid/1.0.0-beta-3/jar)
  * [Ruby](https://rubygems.org/gems/azure_mgmt_event_grid/versions/0.19.0)
  * [JS](https://www.npmjs.com/package/@azure/arm-eventgrid/v/7.0.0)
  * [Go](https://github.com/Azure/azure-sdk-for-go)


## <a name="next-steps"></a>Passaggi successivi
- [Panoramica degli argomenti partner](partner-topics-overview.md)
- [Modulo di onboarding degli argomenti del partner](https://aka.ms/gridpartnerform)
- [Argomento del partner Auth0](auth0-overview.md)
- [Come usare l'argomento partner Auth0](auth0-how-to.md)
