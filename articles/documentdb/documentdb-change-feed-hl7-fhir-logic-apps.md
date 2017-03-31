---
title: Feed delle modifiche per le risorse HL7 FHIR in Azure DocumentDB | Microsoft Docs
description: Informazioni su come impostare le notifiche di modifica i record sanitari HL7 FHIR dei pazienti usando le App per la logica di Azure, DocumentDB e il bus di servizio.
keywords: hl7 fhir
services: documentdb
author: hedidin
manager: jhubbard
editor: mimig
documentationcenter: 
ms.assetid: 0d25c11f-9197-419a-aa19-4614c6ab2d06
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: b-hoedid
translationtype: Human Translation
ms.sourcegitcommit: c25274ad48edb0c89e3f177277af1a4ae5fb3eec
ms.openlocfilehash: dafd6aa1172661e82bccb35dc29fd59b2c04dd6e


---

# <a name="notifying-patients-of-hl7-fhir-health-care-record-changes-using-logic-apps-and-documentdb"></a>Notifica ai pazienti in merito a modifiche dei record sanitari HL7 FHIR usando le App per la logica e DocumentDB

Howard Edidin, Azure MVP, è stato recentemente contattato da un'organizzazione del settore di sanitario che desiderava aggiungere una nuova funzionalità al proprio portale dei pazienti. L'organizzazione aveva bisogno di inviare notifiche ai pazienti in caso di aggiornamenti ai loro record sanitari. I pazienti dovevano anche essere in grado di iscriversi a tali aggiornamenti. 

Questo articolo illustra la soluzione di notifica del feed delle modifiche creata per questa organizzazione sanitaria usando DocumentDB, le App per la logica e il bus di servizio. 

## <a name="project-requirements"></a>Requisiti del progetto
- I provider inviano documenti HL7 Consolidated-Clinical Document Architecture (C-CDA) in formato XML. I documenti C-CDA comprendono sostanzialmente ogni tipo di documento clinico, incluse informazioni familiari e registri immunologici, oltre a documenti amministrati, finanziari e su flussi di lavoro. 
- I documenti C-CDA vengono convertiti in [risorse HL7 FHIR](http://hl7.org/fhir/2017Jan/resourcelist.html) in formato JSON.
- I documenti di risorse FHIR modificati vengono inviati via e-mail in formato JSON.

## <a name="solution-workflow"></a>Flusso di lavoro della soluzione 

A livello generale, il progetto richiedeva i seguenti passaggi del flusso di lavoro: 
1. Convertire documenti C-CDA in risorse FHIR.
2. Eseguire un trigger ricorrente per il polling di risorse FHIR modificate. 
2. Chiamare un'app personalizzata, FhirNotificationApi, per connettersi a DocumentDB ed eseguire query in merito a documenti nuovi o modificati.
3. Salvare la risposta nella coda del bus di servizio.
4. Effettuare il polling per i nuovi messaggi nella coda del bus di servizio.
5. Inviare notifiche e-mail ai pazienti.

## <a name="solution-architecture"></a>Architettura della soluzione
Questa soluzione richiede tre App per la logica per soddisfare i requisiti precedenti e completare il flusso di lavoro della soluzione. Le tre App per la logica sono:
1. **App HL7-FHIR-Mapping**: riceve il documento HL7 C-CDA, lo trasforma nella risorsa FHIR e lo salva in DocumentDB.
2. **App EHR**: esegue una query nel repository FHIR di DocumentDB e salva la risposta in una coda del bus di servizio. Questa App per la logica usa un'[app per le API](#api-app) per recuperare i documenti nuovi e modificati.
3. **App per le notifiche sul processo**: invia una notifica e-mail contenente i documenti della risorsa FHIR.

![Le tre App per la logica usate in questa soluzione per la sanità HL7 FHIR](./media/documentdb-change-feed-hl7-fhir-logic-apps/documentdb-health-care-solution-hl7-fhir.png)



### <a name="azure-services-used-in-the-solution"></a>I servizi di Azure usati nella soluzione

#### <a name="documentdb"></a>DocumentDB
DocumentDB è il repository per le risorse FHIR come illustrato nella figura seguente.

![L'account di Azure DocumentDB usato in questa esercitazione per la soluzione HL7 FHIR del settore sanitario](./media/documentdb-change-feed-hl7-fhir-logic-apps/documentdb-account.png)

#### <a name="logic-apps"></a>App per la logica
Le App per la logica gestiscono il processo del flusso di lavoro. Gli screenshot seguenti mostrano le App per la logica create per questa soluzione. 


1. **App HL7-FHIR-Mapping**: riceve il documento HL7 C-CDA e lo trasforma in una risorsa FHIR usando Enterprise Integration Pack per App per la logica. Enterprise Integration Pack gestisce il mapping da C-CDA alle risorse FHIR.

    ![L'App per la logica usata per ricevere record sanitari HL7 FHIR](./media/documentdb-change-feed-hl7-fhir-logic-apps/documentdb-hl7-fhir-logic-apps-json-transform.png)


2. **App EHR**: esegue una query nel repository FHIR di DocumentDB e salva la risposta in una coda del bus di servizio. Il codice per l'app GetNewOrModifiedFHIRDocuments è indicato di seguito.

    ![L'App per la logica usata per eseguire query in Azure DocumentDB](./media/documentdb-change-feed-hl7-fhir-logic-apps/documentdb-hl7-fhir-logic-apps-api-app.png)

3. **App per le notifiche sul processo**: invia una notifica e-mail contenente i documenti della risorsa FHIR.

    ![L'App per la logica per inviare e-mail ai pazienti contenenti la risorsa HL7 FHIR](./media/documentdb-change-feed-hl7-fhir-logic-apps/documentdb-hl7-fhir-logic-apps-send-email.png)

#### <a name="service-bus"></a>Bus di servizio
La figura seguente mostra la coda di pazienti. Il valore della proprietà Tag viene usato per l'oggetto dell'email.

![La coda del bus di servizio usata in questa esercitazione HL7 FHIR](./media/documentdb-change-feed-hl7-fhir-logic-apps/documentdb-hl7-fhir-service-bus-queue.png)

<a id="api-app"></a>

#### <a name="api-app"></a>App per le API
Un'app per le API si connette a DocumentDB ed esegue query per documenti FHIR nuovi o modificati in base al tipo di risorsa. Questa applicazione dispone di un controller, **FhirNotificationApi** con una unica operazione **GetNewOrModifiedFhirDocuments**, vedere l'[origine di app per le API](#api-app-source).

Viene usata la classe [`CreateDocumentChangeFeedQuery`](https://msdn.microsoft.com/en-us/library/azure/microsoft.azure.documents.client.documentclient.createdocumentchangefeedquery.aspx) per l'API DocumentDB .NET. Per altre informazioni, vedere l'[articolo sul feed delle modifiche di DocumentDB](https://docs.microsoft.com/en-us/azure/documentdb/documentdb-change-feed). 

##### <a name="getnewormodifiedfhirdocuments-operation"></a>Operazione GetNewOrModifiedFhirDocuments

**Input**
- DatabaseId
- CollectionId
- Nome del tipo di risorsa HL7 FHIR
- Valore booleano: Avvia dall'inizio
- Int: Numero di documenti restituiti

**Outputs**
- Operazione riuscita: Codice di stato: 200, Risposta: Elenco di documenti (matrice JSON)
- Errore: Codice di stato: 404, Risposta: "No Documents found for '*resource name'* Resource Type" (Nessun documento trovato per il tipo di risorsa "nome risorsa")

<a id="api-app-source"></a>

**Origine per l'app per le API**

```C#

    using System.Collections.Generic;
    using System.Linq;
    using System.Net;
    using System.Net.Http;
    using System.Threading.Tasks;
    using System.Web.Http;
    using Microsoft.Azure.Documents;
    using Microsoft.Azure.Documents.Client;
    using Swashbuckle.Swagger.Annotations;
    using TRex.Metadata;
    
    namespace FhirNotificationApi.Controllers
    {
        /// <summary>
        ///     FHIR Resource Type Controller
        /// </summary>
        /// <seealso cref="System.Web.Http.ApiController" />
        public class FhirResourceTypeController : ApiController
        {
            /// <summary>
            ///     Gets the new or modified FHIR documents from Last Run Date 
            ///        or create date of the collection
            /// </summary>
            /// <param name="databaseId"></param>
            /// <param name="collectionId"></param>
            /// <param name="resourceType"></param>
            /// <param name="startfromBeginning"></param>
            /// <param name="maximumItemCount">-1 returns all (default)</param>
            /// <returns></returns>
            [Metadata("Get New or Modified FHIR Documents",
                "Query for new or modifed FHIR Documents By Resource Type " +
                "from Last Run Date or Begining of Collection creation"
            )]
            [SwaggerResponse(HttpStatusCode.OK, type: typeof(Task<dynamic>))]
            [SwaggerResponse(HttpStatusCode.NotFound, "No New or Modifed Documents found")]
            [SwaggerOperation("GetNewOrModifiedFHIRDocuments")]
            public async Task<dynamic> GetNewOrModifiedFhirDocuments(
                [Metadata("Database Id", "Database Id")] string databaseId,
                [Metadata("Collection Id", "Collection Id")] string collectionId,
                [Metadata("Resource Type", "FHIR resource type name")] string resourceType,
                [Metadata("Start from Beginning ", "Change Feed Option")] bool startfromBeginning,
                [Metadata("Maximum Item Count", "Number of documents returned. '-1 returns all' (default)")] int maximumItemCount = -1
            )
            {
                var collectionLink = UriFactory.CreateDocumentCollectionUri(databaseId, collectionId);
    
                var context = new DocumentDbContext();    
    
                var docs = new List<dynamic>();
    
                var partitionKeyRanges = new List<PartitionKeyRange>();
                FeedResponse<PartitionKeyRange> pkRangesResponse;
    
                do
                {
                    pkRangesResponse = await context.Client.ReadPartitionKeyRangeFeedAsync(collectionLink);
                    partitionKeyRanges.AddRange(pkRangesResponse);
                } while (pkRangesResponse.ResponseContinuation != null);
    
                foreach (var pkRange in partitionKeyRanges)
                {
                    var changeFeedOptions = new ChangeFeedOptions
                    {
                        StartFromBeginning = startfromBeginning,
                        RequestContinuation = null,
                        MaxItemCount = maximumItemCount,
                        PartitionKeyRangeId = pkRange.Id
                    };
    
                    using (var query = context.Client.CreateDocumentChangeFeedQuery(collectionLink, changeFeedOptions))
                    {
                        do
                        {
                            if (query != null)
                            {
                                var results = await query.ExecuteNextAsync<dynamic>().ConfigureAwait(false);
                                if (results.Count > 0)
                                    docs.AddRange(results.Where(doc => doc.resourceType == resourceType));
                            }
                            else
                            {
                                throw new HttpResponseException(new HttpResponseMessage(HttpStatusCode.NotFound));
                            }
                        } while (query.HasMoreResults);
                    }
                }
                if (docs.Count > 0)
                    return docs;
                var msg = new StringContent("No documents found for " + resourceType + " Resource");
                var response = new HttpResponseMessage
                {
                    StatusCode = HttpStatusCode.NotFound,
                    Content = msg
                };
                return response;
            }
        }
    }
    
```

### <a name="testing-the-fhirnotificationapi"></a>Test di FhirNotificationApi 

Nell'immagine seguente viene illustrato l'uso di Swagger per testare [FhirNotificationApi](#api-app-source).

![Il file Swagger utilizzato per testare l'app per le API](./media/documentdb-change-feed-hl7-fhir-logic-apps/documentdb-hl7-fhir-testing-app.png)


### <a name="azure-portal-dashboard"></a>Dashboard del portale di Azure

L'immagine seguente mostra tutti i servizi di Azure per questa soluzione in esecuzione nel portale di Azure.

![Il portale di Azure con tutti i servizi usati in questa esercitazione HL7 FHIR](./media/documentdb-change-feed-hl7-fhir-logic-apps/documentdb-hl7-fhir-portal.png)


## <a name="summary"></a>Riepilogo

- Si è appreso che DocumentDB dispone di supporto nativo per le notifiche per documenti nuovi o modificati, con notevole facilità d'uso. 
- Sfruttando le App per la logica è possibile creare flussi di lavoro senza scrivere codice.
- Uso delle code del bus di servizio di Azure per gestire la distribuzione dei documenti HL7 FHIR.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su DocumentDB, vedere la [home page di DocumentDB](https://azure.microsoft.com/en-us/services/documentdb/). Per altre informazioni sulle App per la logica, vedere [App per la logica](https://azure.microsoft.com/en-us/services/logic-apps/).





<!--HONumber=Feb17_HO2-->


