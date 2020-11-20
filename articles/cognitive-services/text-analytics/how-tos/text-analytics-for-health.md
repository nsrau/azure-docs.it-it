---
title: Come usare Analisi del testo per l'integrità
titleSuffix: Azure Cognitive Services
description: Informazioni su come estrarre ed etichettare le informazioni mediche da testo clinico non strutturato con Analisi del testo per l'integrità.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 11/19/2020
ms.author: aahi
ms.custom: references_regions
ms.openlocfilehash: e7f017c1f3dc189af2b0fc053912decca3459478
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94952761"
---
# <a name="how-to-use-text-analytics-for-health-preview"></a>Procedura: usare Analisi del testo per l'integrità (anteprima)

> [!IMPORTANT] 
> Analisi del testo per l'integrità è una funzionalità di anteprima fornita "così com'è" e "con tutti gli errori". Di conseguenza, **analisi del testo per Health (Preview) non devono essere implementate o distribuite in alcun uso in produzione.** Analisi del testo per l'integrità non è intenzionale o reso disponibile per l'utilizzo come dispositivo medico, supporto clinico, strumento di diagnostica o altra tecnologia da utilizzare per la diagnosi, la cura, la mitigazione, il trattamento o la prevenzione di patologie o altre condizioni, né la concessione di alcuna licenza o diritto da parte di Microsoft di utilizzare questa funzionalità per tali scopi. Questa funzionalità non è progettata o progettata per essere implementata o distribuita come sostituto di consigli medici professionali o di opinione sanitaria, diagnosi, trattamento o giudizio clinico di un professionista sanitario e non deve essere usato come tali. Il cliente è esclusivamente responsabile dell'utilizzo di Analisi del testo per l'integrità. Microsoft non garantisce che Analisi del testo per l'integrità o i materiali forniti in relazione alla funzionalità saranno sufficienti per qualsiasi scopo medico o per soddisfare i requisiti sanitari o medici di qualsiasi persona. 


Analisi del testo per l'integrità è una funzionalità del servizio API Analisi del testo che estrae ed etichetta le informazioni mediche rilevanti da testi non strutturati, come le note del medico, i riepiloghi di scaricamento, i documenti clinici e i record di integrità elettronici.  È possibile utilizzare questo servizio in due modi: 

* API basata sul Web (asincrona) 
* Un contenitore Docker (sincrono)   

## <a name="features"></a>Funzionalità

Analisi del testo per l'integrità esegue il riconoscimento delle entità denominate (NER), l'estrazione della relazione, la negazione dell'entità e il collegamento di entità sul testo in lingua inglese per individuare informazioni dettagliate in testo clinico e biomedico non strutturato.

### <a name="named-entity-recognition"></a>[Riconoscimento di entità denominate](#tab/ner)

Il riconoscimento delle entità denominato rileva le parole e le frasi indicate nel testo non strutturato che possono essere associate a uno o più tipi semantici, ad esempio la diagnosi, il nome del farmaco, il sintomo/segno o l'età.

> [!div class="mx-imgBorder"]
> ![NER integrità](../media/ta-for-health/health-named-entity-recognition.png)

### <a name="relation-extraction"></a>[Estrazione di relazioni](#tab/relation-extraction)

L'estrazione di relazioni identifica connessioni significative tra i concetti indicati nel testo. Ad esempio, una relazione "ora della condizione" viene rilevata associando il nome di una condizione a un'ora. 

> [!div class="mx-imgBorder"]
> ![Integrità RE](../media/ta-for-health/health-relation-extraction.png)


### <a name="entity-linking"></a>[Collegamento delle entità](#tab/entity-linking)

Il collegamento di entità ambiguità entità distinte associando entità denominate indicate in testo ai concetti trovati in un database di concetti predefiniti. Ad esempio, Unified Medical Language System (UMLS).

> [!div class="mx-imgBorder"]
> ![EL di stato](../media/ta-for-health/health-entity-linking.png)

Analisi del testo per l'integrità supporta il collegamento all'integrità e ai vocabolari biomedici disponibili nella Knowledge base del Metathesaurus[UMLS](https://www.nlm.nih.gov/research/umls/sourcereleasedocs/index.html)(Unified Medical Language System).

### <a name="negation-detection"></a>[Rilevamento della negazione](#tab/negation-detection) 

Il significato del contenuto medico è molto influenzato dai modificatori, ad esempio la negazione, che può avere una implicazione critica se non viene diagnosticata. Analisi del testo per Health supporta il rilevamento delle negazioni per le diverse entità indicate nel testo. 

> [!div class="mx-imgBorder"]
> ![Stato negativo](../media/ta-for-health/health-negation.png)

---

Vedere le [categorie di entità](../named-entity-types.md?tabs=health) restituite da analisi del testo per l'integrità per un elenco completo delle entità supportate.

### <a name="supported-languages-and-regions"></a>Lingue e aree supportate

Analisi del testo per l'integrità supporta solo documenti in lingua inglese. 

Il Analisi del testo per l'API Web ospitata sull'integrità è attualmente disponibile solo in queste aree: Stati Uniti occidentali 2, Stati Uniti orientali 2, Stati Uniti centrali, Europa settentrionale ed Europa occidentale.

## <a name="request-access-to-the-public-preview"></a>Richiedere l'accesso all'anteprima pubblica

Compilare e inviare il [modulo di richiesta di servizi cognitivi](https://aka.ms/csgate) per richiedere l'accesso alla analisi del testo per l'anteprima pubblica dell'integrità. Non verranno addebitate Analisi del testo per l'utilizzo dell'integrità. 

Il modulo richiede informazioni sull'utente, sull'azienda e sullo scenario utente per cui si userà il contenitore. Dopo aver inviato il modulo, il team di servizi cognitivi di Azure lo esaminerà e invierà un messaggio di posta elettronica con una decisione.

> [!IMPORTANT]
> * Nel modulo è necessario usare un indirizzo di posta elettronica associato a un ID sottoscrizione di Azure.
> * La risorsa di Azure usata deve essere stata creata con l'ID sottoscrizione di Azure approvato. 
> * Controllare la posta elettronica (cartella posta in arrivo e cartelle indesiderate) per gli aggiornamenti sullo stato dell'applicazione da Microsoft.

## <a name="using-the-docker-container"></a>Uso del contenitore Docker 

Per eseguire il Analisi del testo per il contenitore di integrità nel proprio ambiente, seguire queste [istruzioni per scaricare e installare il contenitore](../how-tos/text-analytics-how-to-install-containers.md?tabs=healthcare).

## <a name="using-the-client-library"></a>Uso della libreria client

La versione provvisoria più recente della libreria client di Analisi del testo consente di chiamare Analisi del testo per l'integrità tramite un oggetto client. Fare riferimento alla documentazione di riferimento e vedere gli esempi su GitHub:
* [C#](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/textanalytics/Azure.AI.TextAnalytics)
* [Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/textanalytics/azure-ai-textanalytics/)
* [Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/textanalytics/azure-ai-textanalytics)



## <a name="sending-a-rest-api-request"></a>Invio di una richiesta all'API REST 

### <a name="preparation"></a>Preparazione

Analisi del testo per l'integrità produce un risultato di qualità superiore quando si assegnano più piccole quantità di testo da utilizzare. Questo è contrario ad alcune delle altre Analisi del testo funzionalità, ad esempio l'estrazione di frasi chiave, che garantisce prestazioni migliori in blocchi di testo di dimensioni maggiori. Per ottenere risultati ottimali da queste operazioni, provare a ristrutturare gli input di conseguenza.

È necessario disporre di documenti JSON nel formato seguente: ID, testo e lingua. 

Le dimensioni dei documenti devono essere inferiori a 5.120 caratteri per documento. Per il numero massimo di documenti consentiti in una raccolta, vedere l'articolo sui [limiti dei dati](../concepts/data-limits.md?tabs=version-3) in concetti. La raccolta viene inviata nel corpo della richiesta.

### <a name="structure-the-api-request-for-the-hosted-asynchronous-web-api"></a>Strutturare la richiesta API per l'API Web asincrona ospitata

Sia per il contenitore che per l'API Web ospitata, è necessario creare una richiesta POST. Per creare rapidamente e inviare una richiesta POST all'API Web ospitata nella propria area geografica, è possibile [usare post](text-analytics-how-to-call-api.md), un comando curl o la [analisi del testo](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/Health) **console test API** . 

Di seguito è riportato un esempio di un file JSON collegato alla Analisi del testo per il corpo POST della richiesta dell'API di integrità:

```json
example.json

{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Subject was administered 100mg remdesivir intravenously over a period of 120 min"
    }
  ]
}
```

### <a name="hosted-asynchronous-web-api-response"></a>Risposta API Web asincrona ospitata 

Poiché questa richiesta POST viene utilizzata per inviare un processo per l'operazione asincrona, non è presente alcun testo nell'oggetto Response.  Tuttavia, per eseguire una richiesta GET per verificare lo stato del processo e l'output, è necessario il valore della chiave Operation-location nelle intestazioni di risposta.  Di seguito è riportato un esempio del valore della chiave Operation-location nell'intestazione Response della richiesta POST:

`https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.1-preview.3/entities/health/jobs/<jobID>`

Per controllare lo stato del processo, effettuare una richiesta GET all'URL nel valore dell'intestazione Operation-location KEY della risposta POST.  Per riflettere lo stato di un processo vengono usati gli Stati seguenti: `NotStarted` , `running` , `succeeded` , `failed` , `rejected` , `cancelling` e `cancelled` .  

È possibile annullare un processo con uno `NotStarted` `running` stato o con una chiamata http delete allo stesso URL della richiesta GET.  Altre informazioni sulla chiamata DELETE sono disponibili nella [analisi del testo per informazioni di riferimento sull'API ospitata in integrità](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1-preview-3/operations/CancelHealthJob).

Di seguito è riportato un esempio della risposta di una richiesta GET.  Si noti che l'output è disponibile per il recupero finché non viene `expirationDateTime` superato il (24 ore dal momento della creazione del processo) dopo il quale l'output viene eliminato.

```json
{
    "jobId": "b672c6f5-7c0d-4783-ba8c-4d0c47213454",
    "lastUpdateDateTime": "2020-11-18T01:45:00Z",
    "createdDateTime": "2020-11-18T01:44:55Z",
    "expirationDateTime": "2020-11-19T01:44:55Z",
    "status": "succeeded",
    "errors": [],
    "results": {
        "documents": [
            {
                "id": "1",
                "entities": [
                    {
                        "offset": 25,
                        "length": 5,
                        "text": "100mg",
                        "category": "Dosage",
                        "confidenceScore": 1.0,
                        "isNegated": false
                    },
                    {
                        "offset": 31,
                        "length": 10,
                        "text": "remdesivir",
                        "category": "MedicationName",
                        "confidenceScore": 1.0,
                        "isNegated": false,
                        "links": [
                            {
                                "dataSource": "UMLS",
                                "id": "C4726677"
                            },
                            {
                                "dataSource": "MSH",
                                "id": "C000606551"
                            },
                            {
                                "dataSource": "NCI",
                                "id": "C152185"
                            },
                            {
                                "dataSource": "NCI_FDA",
                                "id": "3QKI37EEHE"
                            }
                        ]
                    },
                    {
                        "offset": 42,
                        "length": 13,
                        "text": "intravenously",
                        "category": "MedicationRoute",
                        "confidenceScore": 1.0,
                        "isNegated": false
                    },
                    {
                        "offset": 56,
                        "length": 4,
                        "text": "over",
                        "category": "Time",
                        "confidenceScore": 0.87,
                        "isNegated": false
                    },
                    {
                        "offset": 73,
                        "length": 7,
                        "text": "120 min",
                        "category": "Time",
                        "confidenceScore": 0.99,
                        "isNegated": false
                    }
                ],
                "relations": [
                    {
                        "relationType": "DosageOfMedication",
                        "bidirectional": false,
                        "source": "#/results/documents/0/entities/0",
                        "target": "#/results/documents/0/entities/1"
                    },
                    {
                        "relationType": "RouteOfMedication",
                        "bidirectional": false,
                        "source": "#/results/documents/0/entities/2",
                        "target": "#/results/documents/0/entities/1"
                    },
                    {
                        "relationType": "TimeOfMedication",
                        "bidirectional": false,
                        "source": "#/results/documents/0/entities/3",
                        "target": "#/results/documents/0/entities/1"
                    },
                    {
                        "relationType": "TimeOfMedication",
                        "bidirectional": false,
                        "source": "#/results/documents/0/entities/4",
                        "target": "#/results/documents/0/entities/1"
                    }
                ],
                "warnings": []
            }
        ],
        "errors": [],
        "modelVersion": "2020-09-03"
    }
}
```


### <a name="structure-the-api-request-for-the-container"></a>Strutturare la richiesta API per il contenitore

Per inviare [use Postman](text-analytics-how-to-call-api.md) una query al contenitore distribuito, sostituendo la `serverURL` variabile con il valore appropriato, è possibile usare il metodo post o la richiesta curl di esempio riportata di seguito.  Si noti che la versione dell'API nell'URL per il contenitore è diversa da quella dell'API ospitata.

```bash
curl -X POST 'http://<serverURL>:5000/text/analytics/v3.2-preview.1/entities/health' --header 'Content-Type: application/json' --header 'accept: application/json' --data-binary @example.json

```

Il codice JSON seguente è un esempio di file JSON collegato alla Analisi del testo per il corpo POST della richiesta dell'API di integrità:

```json
example.json

{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Patient reported itchy sores after swimming in the lake."
    },
    {
      "language": "en",
      "id": "2",
      "text": "Prescribed 50mg benadryl, taken twice daily."
    }
  ]
}
```

### <a name="container-response-body"></a>Corpo della risposta del contenitore

Il codice JSON seguente è un esempio di Analisi del testo per il corpo della risposta dell'API di integrità dalla chiamata sincrona in contenitori:

```json
{
    "documents": [
        {
            "id": "1",
            "entities": [
                {
                    "id": "0",
                    "offset": 25,
                    "length": 5,
                    "text": "100mg",
                    "category": "Dosage",
                    "confidenceScore": 1.0,
                    "isNegated": false
                },
                {
                    "id": "1",
                    "offset": 31,
                    "length": 10,
                    "text": "remdesivir",
                    "category": "MedicationName",
                    "confidenceScore": 1.0,
                    "isNegated": false,
                    "links": [
                        {
                            "dataSource": "UMLS",
                            "id": "C4726677"
                        },
                        {
                            "dataSource": "MSH",
                            "id": "C000606551"
                        },
                        {
                            "dataSource": "NCI",
                            "id": "C152185"
                        },
                        {
                            "dataSource": "NCI_FDA",
                            "id": "3QKI37EEHE"
                        }
                    ]
                },
                {
                    "id": "2",
                    "offset": 42,
                    "length": 13,
                    "text": "intravenously",
                    "category": "MedicationRoute",
                    "confidenceScore": 1.0,
                    "isNegated": false
                },
                {
                    "id": "3",
                    "offset": 56,
                    "length": 4,
                    "text": "over",
                    "category": "Time",
                    "confidenceScore": 0.87,
                    "isNegated": false
                },
                {
                    "id": "4",
                    "offset": 73,
                    "length": 7,
                    "text": "120 min",
                    "category": "Time",
                    "confidenceScore": 0.99,
                    "isNegated": false
                }
            ],
            "relations": [
                {
                    "relationType": "DosageOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/0/entities/0",
                    "target": "#/documents/0/entities/1"
                },
                {
                    "relationType": "RouteOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/0/entities/2",
                    "target": "#/documents/0/entities/1"
                },
                {
                    "relationType": "TimeOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/0/entities/3",
                    "target": "#/documents/0/entities/1"
                },
                {
                    "relationType": "TimeOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/0/entities/4",
                    "target": "#/documents/0/entities/1"
                }
            ]
        }
    ],
    "errors": [],
    "modelVersion": "2020-09-03"
}
```

### <a name="negation-detection-output"></a>Output rilevamento negazione

Quando si usa il rilevamento delle negazioni, in alcuni casi un singolo termine di negazione può indirizzare più termini in una sola volta. La negazione di un'entità riconosciuta viene rappresentata nell'output JSON dal valore booleano del `isNegated` flag, ad esempio:

```json
{
  "id": "2",
  "offset": 90,
  "length": 10,
  "text": "chest pain",
  "category": "SymptomOrSign",
  "score": 0.9972,
  "isNegated": true,
  "links": [
    {
      "dataSource": "UMLS",
      "id": "C0008031"
    },
    {
      "dataSource": "CHV",
      "id": "0000023593"
    },
    ...
```

### <a name="relation-extraction-output"></a>Output estrazione relazione

L'output dell'estrazione della relazione contiene riferimenti URI all' *origine* della relazione e alla relativa *destinazione*. Le entità con ruolo relazione di `ENTITY` vengono assegnate al `target` campo. Le entità con ruolo relazione di `ATTRIBUTE` vengono assegnate al `source` campo. Le relazioni di abbreviazione contengono bidirezionali `source` e `target` campi e `bidirectional` verranno impostate su `true` . 

```json
"relations": [
                {
                    "relationType": "DosageOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/1/entities/0",
                    "target": "#/documents/1/entities/1"
                },
                {
                    "relationType": "FrequencyOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/1/entities/2",
                    "target": "#/documents/1/entities/1"
                }
            ]
  },
...
]
```

## <a name="see-also"></a>Vedere anche

* [Panoramica di Analisi del testo](../overview.md)
* [Categorie di entità denominate](../named-entity-types.md)
* [Novità](../whats-new.md)
