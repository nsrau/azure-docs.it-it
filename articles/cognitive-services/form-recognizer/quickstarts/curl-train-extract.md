---
title: 'Guida introduttiva: Eseguire il training di un modello ed estrarre dati dai moduli con cURL - Riconoscimento modulo'
titleSuffix: Azure Cognitive Services
description: In questo argomento di avvio rapido si userà l'API REST di riconoscimento modulo con cURL per eseguire il training di un modello ed estrarre dati dai moduli.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: quickstart
ms.date: 05/27/2020
ms.author: pafarley
ms.openlocfilehash: 0abc98c95c03e3dd2e12a601188d9c5f7cb4523d
ms.sourcegitcommit: 73ac360f37053a3321e8be23236b32d4f8fb30cf
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/30/2020
ms.locfileid: "85558987"
---
# <a name="quickstart-train-a-form-recognizer-model-and-extract-form-data-by-using-the-rest-api-with-curl"></a>Guida introduttiva: Eseguire il training di un modello di riconoscimento modulo ed estrarre dati dai moduli usando l'API REST con cURL

In questo argomento di avvio rapido si userà l'API REST di riconoscimento modulo di Azure con cURL per eseguire il training e assegnare punteggi ai moduli in modo da estrarre coppie chiave-valore e tabelle.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per completare questo argomento di avvio rapido è necessario disporre di quanto segue:
- [cURL](https://curl.haxx.se/windows/) installato.
- Un set di almeno sei moduli dello stesso tipo. Cinque di questi moduli verranno usati per eseguire il training del modello, che quindi verrà testato con il sesto. I moduli possono essere di tipi di file diversi, ma devono essere dello stesso tipo di documento. Per questa guida di avvio rapido, è possibile usare un [set di dati di esempio](https://go.microsoft.com/fwlink/?linkid=2090451). Caricare i file di training nella radice di un contenitore di archiviazione BLOB in un account di archiviazione di Azure. È possibile inserire i file di test in una cartella separata.

## <a name="create-a-form-recognizer-resource"></a>Creare una risorsa di riconoscimento modulo

[!INCLUDE [create resource](../includes/create-resource.md)]

## <a name="train-a-form-recognizer-model"></a>Eseguire il training di un modello di Riconoscimento modulo

È prima di tutto necessario un set di dati di training in un BLOB del servizio di archiviazione di Azure. È necessario avere almeno cinque moduli compilati (documenti PDF e/o immagini) dello stesso tipo/struttura dei dati di input principali. In alternativa, è possibile usare un singolo modulo vuoto con due moduli compilati. Il nome file del modulo vuoto deve includere la parola "empty". Consultare [Compilare un training set per un modello personalizzato](../build-training-data-set.md) per suggerimenti e opzioni per la creazione di dati di training.

> [!NOTE]
> È possibile usare la funzionalità dei dati etichettati per applicare manualmente le etichette ad alcuni o a tutti i dati di training in anticipo. Si tratta di una procedura più complessa, ma il risultato è un training del modello più efficace. Per altre informazioni su questa funzionalità, vedere la sezione [Eseguire il training con le etichette](../overview.md#train-with-labels) della panoramica.

Per eseguire il training di un modello di Riconoscimento modulo con i documenti del contenitore BLOB di Azure, chiamare l'API **[Train Custom Model](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync)** eseguendo il comando cURL seguente. Prima di eseguire il comando, apportare queste modifiche:

1. Sostituire `<Endpoint>` con l'endpoint ottenuto con la sottoscrizione di riconoscimento modulo.
1. Sostituire `<subscription key>` con la chiave di sottoscrizione copiata nel passaggio precedente.
1. Sostituire `<SAS URL>` con l'URL della firma di accesso condiviso (SAS) del contenitore di archiviazione BLOB di Azure. Per recuperare l'URL SAS, aprire Microsoft Azure Storage Explorer, fare clic con il pulsante destro del mouse sul contenitore e scegliere **Ottieni firma di accesso condiviso**. Assicurarsi che le autorizzazioni **Lettura** ed **Elenco** siano selezionate e fare clic su **Crea**. A questo punto, copiare il valore dalla sezione **URL**. Dovrebbe essere in questo formato: `https://<storage account>.blob.core.windows.net/<container name>?<SAS value>`.

```bash
curl -i -X POST "https://<Endpoint>/formrecognizer/v2.0/custom/models" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{ \"source\": \""<SAS URL>"\"}"
```

Si riceverà una risposta `201 (Success)` con l'intestazione **Location**. Il valore di questa intestazione corrisponde all'ID del nuovo modello da sottoporre a training. 

## <a name="get-training-results"></a>Ottenere i risultati del training

Dopo aver avviato il training, usare una nuova operazione, **[Get Custom Model](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/GetCustomModel)** , per verificare lo stato dell'operazione. Passare l'ID modello in questa chiamata API per controllare lo stato del training:

1. Sostituire `<Endpoint>` con l'endpoint ottenuto con la chiave di sottoscrizione di riconoscimento modulo,
1. Sostituire `<subscription key>` con la chiave di sottoscrizione
1. Sostituire `<model ID>` con l'ID modello ricevuto nel passaggio precedente

```bash
curl -X GET "https://<Endpoint>/formrecognizer/v2.0/custom/models/<model ID>" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```

Si riceverà una risposta `200 (Success)` con un corpo JSON nel formato seguente. Osservare il campo `"status"`. Al termine del training, avrà il valore `"ready"`. Se il training del modello non è stato completato, è necessario eseguire di nuovo una query sul servizio ripetendo il comando. Si consiglia di attendere almeno un secondo tra le chiamate.

Il campo `"modelId"` contiene l'ID del modello da sottoporre a training. Servirà per il passaggio successivo.
    
```json
{ 
  "modelInfo":{ 
    "status":"ready",
    "createdDateTime":"2019-10-08T10:20:31.957784",
    "lastUpdatedDateTime":"2019-10-08T14:20:41+00:00",
    "modelId":"1cfb372bab404ba3aa59481ab2c63da5"
  },
  "trainResult":{ 
    "trainingDocuments":[ 
      { 
        "documentName":"invoices\\Invoice_1.pdf",
        "pages":1,
        "errors":[ 

        ],
        "status":"succeeded"
      },
      { 
        "documentName":"invoices\\Invoice_2.pdf",
        "pages":1,
        "errors":[ 

        ],
        "status":"succeeded"
      },
      { 
        "documentName":"invoices\\Invoice_3.pdf",
        "pages":1,
        "errors":[ 

        ],
        "status":"succeeded"
      },
      { 
        "documentName":"invoices\\Invoice_4.pdf",
        "pages":1,
        "errors":[ 

        ],
        "status":"succeeded"
      },
      { 
        "documentName":"invoices\\Invoice_5.pdf",
        "pages":1,
        "errors":[ 

        ],
        "status":"succeeded"
      }
    ],
    "errors":[ 

    ]
  },
  "keys":{ 
    "0":[ 
      "Address:",
      "Invoice For:",
      "Microsoft",
      "Page"
    ]
  }
}
```

## <a name="analyze-forms-for-key-value-pairs-and-tables"></a>Analizzare i moduli per trovare le coppie chiave-valore e le tabelle

Quindi, usare il modello appena sottoposto a training per analizzare un documento da cui estrarre le coppie chiave-valore e le tabelle. Chiamare l'API **[Analyze Form](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm)** eseguendo il comando cURL seguente. Prima di eseguire il comando, apportare queste modifiche:

1. Sostituire `<Endpoint>` con l'endpoint ottenuto dalla chiave di sottoscrizione di riconoscimento modulo, disponibile nella scheda **Overview** (Panoramica) della risorsa di riconoscimento modulo.
1. Sostituire `<model ID>` con l'ID modello ricevuto nella sezione precedente.
1. Sostituire `<SAS URL>` con un URL di firma di accesso condiviso per il file in archiviazione di Azure. Seguire la procedura decritta nella sezione Training, ma invece di ottenere un URL di firma di accesso condiviso per l'intero contenitore BLOB ottenerne uno per il file specifico da analizzare.
1. Sostituire `<subscription key>` con la chiave di sottoscrizione.

```bash
curl -v "https://<Endpoint>/formrecognizer/v2.0/custom/models/<model ID>/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" -d "{ \"source\": \""<SAS URL>"\" } "
```

Si riceverà una risposta `202 (Success)` che include l'intestazione **Operation-Location**. Il valore di questa intestazione include un ID risultati da usare per tenere traccia dei risultati dell'operazione di analisi. Salvare questo ID risultati per il passaggio successivo.

## <a name="get-the-analyze-results"></a>Ottenere i risultati dell'analisi

Usare l'API seguente per eseguire una query sui risultati dell'operazione di analisi.

1. Sostituire `<Endpoint>` con l'endpoint ottenuto dalla chiave di sottoscrizione di riconoscimento modulo, disponibile nella scheda **Overview** (Panoramica) della risorsa di riconoscimento modulo.
1. Sostituire `<result ID>` con l'ID ricevuto nella sezione precedente.
1. Sostituire `<subscription key>` con la chiave di sottoscrizione.

```bash
curl -X GET "https://<Endpoint>/formrecognizer/v2.0/custom/models/<model ID>/analyzeResults/<result ID>" -H "Ocp-Apim-Subscription-Key: <subscription key>"
```

Si riceverà una risposta `200 (Success)` con un corpo JSON nel formato seguente. L'output è stato abbreviato per semplicità. Osservare il campo `"status"` verso la fine. Al termine dell'operazione di analisi, avrà il valore `"succeeded"`. Se l'operazione di analisi non è stata completata, è necessario eseguire di nuovo una query sul servizio ripetendo il comando. Si consiglia di attendere almeno un secondo tra le chiamate.

Le tabelle e le associazioni chiave-valore principali si trovano nel nodo `"pageResults"`. Se è stata specificata anche l'estrazione di testo normale tramite il parametro URL *includeTextDetails*, il nodo `"readResults"` mostrerà il contenuto e le posizioni di tutto il testo nel documento.

```json
{
  "analyzeResult":{ 
    "readResults":[ 
      { 
        "page":1,
        "width":8.5,
        "height":11.0,
        "angle":0,
        "unit":"inch",
        "lines":[ 
          { 
            "text":"Contoso",
            "boundingBox":[ 
              0.5278,
              1.0597,
              1.4569,
              1.0597,
              1.4569,
              1.4347,
              0.5278,
              1.4347
            ],
            "words":[ 
              { 
                "text":"Contoso",
                "boundingBox":[ 
                  0.5278,
                  1.0597,
                  1.4569,
                  1.0597,
                  1.4569,
                  1.4347,
                  0.5278,
                  1.4347
                ]
              }
            ]
          },
          ...
          { 
            "text":"PT",
            "boundingBox":[ 
              6.2181,
              3.3528,
              6.3944,
              3.3528,
              6.3944,
              3.5417,
              6.2181,
              3.5417
            ],
            "words":[ 
              { 
                "text":"PT",
                "boundingBox":[ 
                  6.2181,
                  3.3528,
                  6.3944,
                  3.3528,
                  6.3944,
                  3.5417,
                  6.2181,
                  3.5417
                ]
              }
            ]
          }
        ]
      }
    ],
    "version":"2.0.0",
    "errors":[ 

    ],
    "documentResults":[ 

    ],
    "pageResults":[ 
      { 
        "page":1,
        "clusterId":1,
        "keyValuePairs":[ 
          { 
            "key":{ 
              "text":"Address:",
              "boundingBox":[ 
                0.7972,
                1.5125,
                1.3958,
                1.5125,
                1.3958,
                1.6431,
                0.7972,
                1.6431
              ],
              "elements":[ 
                "#/readResults/0/lines/1/words/0"
              ]
            },
            "value":{ 
              "text":"1 Redmond way Suite 6000 Redmond, WA 99243",
              "boundingBox":[ 
                0.7972,
                1.6764,
                2.15,
                1.6764,
                2.15,
                2.2181,
                0.7972,
                2.2181
              ],
              "elements":[ 
                "#/readResults/0/lines/4/words/0",
                "#/readResults/0/lines/4/words/1",
                "#/readResults/0/lines/4/words/2",
                "#/readResults/0/lines/4/words/3",
                "#/readResults/0/lines/6/words/0",
                "#/readResults/0/lines/6/words/1",
                "#/readResults/0/lines/6/words/2",
                "#/readResults/0/lines/8/words/0"
              ]
            },
            "confidence":0.86
          },
          { 
            "key":{ 
              "text":"Invoice For:",
              "boundingBox":[ 
                4.3903,
                1.5125,
                5.1139,
                1.5125,
                5.1139,
                1.6431,
                4.3903,
                1.6431
              ],
              "elements":[ 
                "#/readResults/0/lines/2/words/0",
                "#/readResults/0/lines/2/words/1"
              ]
            },
            "value":{ 
              "text":"Microsoft 1020 Enterprise Way Sunnayvale, CA 87659",
              "boundingBox":[ 
                5.1917,
                1.4458,
                6.6583,
                1.4458,
                6.6583,
                2.0347,
                5.1917,
                2.0347
              ],
              "elements":[ 
                "#/readResults/0/lines/3/words/0",
                "#/readResults/0/lines/5/words/0",
                "#/readResults/0/lines/5/words/1",
                "#/readResults/0/lines/5/words/2",
                "#/readResults/0/lines/7/words/0",
                "#/readResults/0/lines/7/words/1",
                "#/readResults/0/lines/7/words/2"
              ]
            },
            "confidence":0.86
          },
          ...
        ],
        "tables":[ 
          { 
            "caption":null,
            "rows":2,
            "columns":5,
            "cells":[ 
              { 
                "rowIndex":0,
                "colIndex":0,
                "header":true,
                "text":"Invoice Number",
                "boundingBox":[ 
                  0.5347,
                  2.8722,
                  1.575,
                  2.8722,
                  1.575,
                  3.0028,
                  0.5347,
                  3.0028
                ],
                "elements":[ 
                  "#/readResults/0/lines/9/words/0",
                  "#/readResults/0/lines/9/words/1"
                ]
              },
              { 
                "rowIndex":0,
                "colIndex":1,
                "header":true,
                "text":"Invoice Date",
                "boundingBox":[ 
                  1.9403,
                  2.8722,
                  2.7569,
                  2.8722,
                  2.7569,
                  3.0028,
                  1.9403,
                  3.0028
                ],
                "elements":[ 
                  "#/readResults/0/lines/10/words/0",
                  "#/readResults/0/lines/10/words/1"
                ]
              },
              { 
                "rowIndex":0,
                "colIndex":2,
                "header":true,
                "text":"Invoice Due Date",
                "boundingBox":[ 
                  3.3403,
                  2.8722,
                  4.4583,
                  2.8722,
                  4.4583,
                  3.0028,
                  3.3403,
                  3.0028
                ],
                "elements":[ 
                  "#/readResults/0/lines/11/words/0",
                  "#/readResults/0/lines/11/words/1",
                  "#/readResults/0/lines/11/words/2"
                ]
              },
              ...
            ]
          }
        ]
      }
    ]
  },
  "lastUpdatedDateTime":"2019-10-07T19:32:18+00:00",
  "status":"succeeded",
  "createdDateTime":"2019-10-07T19:32:15+00:00"
}
```

## <a name="improve-results"></a>Migliorare i risultati

[!INCLUDE [improve results](../includes/improve-results-unlabeled.md)]

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stata usata l'API REST di riconoscimento modulo con cURL per eseguire il training di un modello e quindi eseguire il modello in uno scenario di esempio. A questo punto, vedere la documentazione di riferimento per esplorare l'API di Riconoscimento modulo in maggior dettaglio.

> [!div class="nextstepaction"]
> [Documentazione di riferimento delle API REST](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm)
