---
title: Ricevute-riconoscimento form
titleSuffix: Azure Cognitive Services
description: Informazioni sui concetti relativi all'analisi della ricezione con il modulo API Recognizer-uso e limiti.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 08/17/2019
ms.author: pafarley
ms.openlocfilehash: 00709ca5e842e51edbf5b26c53fe0a18e80bb896
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91262349"
---
# <a name="receipt-concepts"></a>Concetti relativi alle ricevute

Il riconoscitore di form di Azure può analizzare le ricevute usando uno dei modelli predefiniti. L'API di ricezione estrae le informazioni chiave dalle ricevute di vendita in inglese, ad esempio nome Merchant, data transazione, totale transazione, voci e altro ancora. 

## <a name="understanding-receipts"></a>Informazioni sulle ricevute 

Molte aziende e persone si basano ancora sull'estrazione manuale dei dati dalle ricevute di vendita, sia per le segnalazioni spese aziendali, i rimborsi, il controllo, le imposte, il budget, il marketing o altri scopi. Spesso in questi scenari, le immagini della ricevuta fisica sono necessarie ai fini della convalida.  

L'estrazione automatica dei dati da queste ricevute può essere complessa. Le ricevute potrebbero essere sgualcite e difficili da leggere, le parti stampate o scritte a mano e le immagini smartphone delle ricevute potrebbero essere di bassa qualità. Inoltre, i modelli e i campi di ricezione possono variare significativamente in base al mercato, all'area e al Merchant. Queste problematiche nell'estrazione dei dati e nel rilevamento dei campi rendono la ricezione un problema univoco.  

Usando il riconoscimento ottico dei caratteri (OCR) e il modello di ricezione predefinito, l'API di ricezione consente questi scenari di elaborazione della ricezione ed estrae i dati dalle ricevute, ad esempio nome esercente, suggerimento, totale, elementi linea e altro ancora. Con questa API non è necessario eseguire il training di un modello. si invia semplicemente la ricevuta all'API di ricezione Analyze e i dati vengono estratti.

![ricevuta di esempio](./media/contoso-receipt-small.png)

## <a name="what-does-the-receipt-api-do"></a>Che cosa fa l'API di ricezione? 

L'API di ricezione precompilata estrae il contenuto delle ricevute di vendita &mdash; il tipo di ricezione che si otterrebbe normalmente presso un ristorante, un rivenditore o un negozio di alimentari.

### <a name="fields-extracted"></a>Campi estratti

* Nome esercente 
* Indirizzo esercente 
* Numero di telefono esercente 
* Data della transazione 
* Tempo transazione 
* Subtotale 
* Imposta 
* Totale 
* Suggerimento 
* Estrazione di elementi riga (ad esempio, quantità di elementi, prezzo dell'elemento, nome dell'elemento)

### <a name="additional-features"></a>Funzionalità aggiuntive

L'API di ricezione restituisce anche le informazioni seguenti:

* Tipo di ricezione (ad esempio, voce, carta di credito e così via)
* Livello di confidenza del campo (ogni campo restituisce un valore di attendibilità associato)
* Testo non elaborato OCR (output di testo estratto dall'OCR per l'intera ricezione)
* Rettangolo di delimitazione per ogni valore, riga e parola

## <a name="input-requirements"></a>Requisiti di input

[!INCLUDE [input reqs](./includes/input-requirements-receipts.md)]

## <a name="supported-locales"></a>Impostazioni locali supportate 

* La **ricezione precompilata v 2.0** (GA) supporta le ricevute di vendita nelle impostazioni locali en-US
* **Ricezione precompilata v 2.1-Preview. 1** (anteprima pubblica) aggiunge ulteriore supporto per le impostazioni locali di ricezione seguenti: 
  * IT-AU 
  * EN-CA 
  * IT-GB 
  * IT-IN 

  > [!NOTE]
  > Input lingua 
  >
  > La ricezione precompilata v 2.1-Preview. 1 include un parametro request facoltativo per specificare le impostazioni locali di ricezione da altri mercati in lingua inglese. Per le ricevute di vendita in inglese da Australia (EN-AU), Canada (EN-CA), Gran Bretagna (EN-GB) e India (EN-IN), è possibile specificare le impostazioni locali per ottenere risultati migliori. Se non è specificata alcuna impostazione locale in v 2.1-Preview. 1, per impostazione predefinita il modello verrà impostato sul modello EN-US.


## <a name="the-analyze-receipt-operation"></a>Operazione di ricezione dell'analisi

La [ricezione dell'analisi](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-1/operations/AnalyzeReceiptAsync) accetta un'immagine o un PDF di una ricevuta come input ed estrae i valori di interesse e testo. La chiamata restituisce un campo di intestazione della risposta denominato `Operation-Location` . Il `Operation-Location` valore è un URL che contiene l'ID risultato da usare nel passaggio successivo.

|Intestazione risposta| URL risultato |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/formrecognizer/v2.0/prebuilt/receipt/analyzeResults/56a36454-fc4d-4354-aa07-880cfbf0064f` |

## <a name="the-get-analyze-receipt-result-operation"></a>Operazione Get Analyze result result

Il secondo passaggio consiste nel chiamare l'operazione [Get Analyze result result](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-1/operations/GetAnalyzeReceiptResult) . Questa operazione accetta come input l'ID del risultato creato dall'operazione di ricezione dell'analisi. Restituisce una risposta JSON che contiene un campo di **stato** con i valori possibili seguenti. Questa operazione viene chiamata in modo iterativo fino a quando non viene restituita con il valore **succeeded** . Utilizzare un intervallo da 3 a 5 secondi per evitare il superamento della frequenza di richieste al secondo (RPS).

|Campo| Type | Valori possibili |
|:-----|:----:|:----|
|status | string | notStarted: l'operazione di analisi non è stata avviata. |
| |  | Running: l'operazione di analisi è in corso. |
| |  | non riuscito: l'operazione di analisi non è riuscita. |
| |  | Succeeded: l'operazione di analisi ha avuto esito positivo. |

Quando il campo **stato** presenta il valore **succeeded** , la risposta JSON includerà i risultati relativi alla ricezione e al riconoscimento del testo. Il risultato della comprensione della ricezione è organizzato come un dizionario di valori di campo denominati, dove ogni valore contiene il testo estratto, il valore normalizzato, il rettangolo di delimitazione, la confidenza e gli elementi di parola corrispondenti. Il risultato del riconoscimento del testo è organizzato come una gerarchia di righe e parole, con testo, rettangolo di delimitazione e informazioni sulla confidenza.

![Risultati della ricezione di esempio](./media/contoso-receipt-2-information.png)

### <a name="sample-json-output"></a>Output JSON di esempio

Vedere l'esempio seguente di una risposta JSON riuscita: il nodo "readResults" contiene tutto il testo riconosciuto. Il testo è organizzato in base alla pagina, quindi alla riga, infine in base a singole parole. Il nodo "documentResults" contiene i valori specifici della scheda business individuati dal modello. Qui sono disponibili coppie chiave/valore utili, ad esempio il nome, il cognome, il nome della società e altro ancora.

```json
{ 
  "status":"succeeded",
  "createdDateTime":"2019-12-17T04:11:24Z",
  "lastUpdatedDateTime":"2019-12-17T04:11:32Z",
  "analyzeResult":{ 
    "version":"2.0.0",
    "readResults":[ 
      { 
        "page":1,
        "angle":0.6893,
        "width":1688,
        "height":3000,
        "unit":"pixel",
        "language":"en",
        "lines":[ 
          { 
            "text":"Contoso",
            "boundingBox":[ 
              635,
              510,
              1086,
              461,
              1098,
              558,
              643,
              604
            ],
            "words":[ 
              { 
                "text":"Contoso",
                "boundingBox":[ 
                  639,
                  510,
                  1087,
                  461,
                  1098,
                  551,
                  646,
                  604
                ],
                "confidence":0.955
              }
            ]
          },
          ...
        ]
      }
    ],
    "documentResults":[ 
      { 
        "docType":"prebuilt:receipt",
        "pageRange":[ 
          1,
          1
        ],
        "fields":{ 
          "ReceiptType":{ 
            "type":"string",
            "valueString":"Itemized",
            "confidence":0.692
          },
          "MerchantName":{ 
            "type":"string",
            "valueString":"Contoso Contoso",
            "text":"Contoso Contoso",
            "boundingBox":[ 
              378.2,
              292.4,
              1117.7,
              468.3,
              1035.7,
              812.7,
              296.3,
              636.8
            ],
            "page":1,
            "confidence":0.613,
            "elements":[ 
              "#/readResults/0/lines/0/words/0",
              "#/readResults/0/lines/1/words/0"
            ]
          },
          "MerchantAddress":{ 
            "type":"string",
            "valueString":"123 Main Street Redmond, WA 98052",
            "text":"123 Main Street Redmond, WA 98052",
            "boundingBox":[ 
              302,
              675.8,
              848.1,
              793.7,
              809.9,
              970.4,
              263.9,
              852.5
            ],
            "page":1,
            "confidence":0.99,
            "elements":[ 
              "#/readResults/0/lines/2/words/0",
              "#/readResults/0/lines/2/words/1",
              "#/readResults/0/lines/2/words/2",
              "#/readResults/0/lines/3/words/0",
              "#/readResults/0/lines/3/words/1",
              "#/readResults/0/lines/3/words/2"
            ]
          },
          "MerchantPhoneNumber":{ 
            "type":"phoneNumber",
            "valuePhoneNumber":"+19876543210",
            "text":"987-654-3210",
            "boundingBox":[ 
              278,
              1004,
              656.3,
              1054.7,
              646.8,
              1125.3,
              268.5,
              1074.7
            ],
            "page":1,
            "confidence":0.99,
            "elements":[ 
              "#/readResults/0/lines/4/words/0"
            ]
          },
          "TransactionDate":{ 
            "type":"date",
            "valueDate":"2019-06-10",
            "text":"6/10/2019",
            "boundingBox":[ 
              265.1,
              1228.4,
              525,
              1247,
              518.9,
              1332.1,
              259,
              1313.5
            ],
            "page":1,
            "confidence":0.99,
            "elements":[ 
              "#/readResults/0/lines/5/words/0"
            ]
          },
          "TransactionTime":{ 
            "type":"time",
            "valueTime":"13:59:00",
            "text":"13:59",
            "boundingBox":[ 
              541,
              1248,
              677.3,
              1261.5,
              668.9,
              1346.5,
              532.6,
              1333
            ],
            "page":1,
            "confidence":0.977,
            "elements":[ 
              "#/readResults/0/lines/5/words/1"
            ]
          },
          "Items":{ 
            "type":"array",
            "valueArray":[ 
              { 
                "type":"object",
                "valueObject":{ 
                  "Quantity":{ 
                    "type":"number",
                    "text":"1",
                    "boundingBox":[ 
                      245.1,
                      1581.5,
                      300.9,
                      1585.1,
                      295,
                      1676,
                      239.2,
                      1672.4
                    ],
                    "page":1,
                    "confidence":0.92,
                    "elements":[ 
                      "#/readResults/0/lines/7/words/0"
                    ]
                  },
                  "Name":{ 
                    "type":"string",
                    "valueString":"Cappuccino",
                    "text":"Cappuccino",
                    "boundingBox":[ 
                      322,
                      1586,
                      654.2,
                      1601.1,
                      650,
                      1693,
                      317.8,
                      1678
                    ],
                    "page":1,
                    "confidence":0.923,
                    "elements":[ 
                      "#/readResults/0/lines/7/words/1"
                    ]
                  },
                  "TotalPrice":{ 
                    "type":"number",
                    "valueNumber":2.2,
                    "text":"$2.20",
                    "boundingBox":[ 
                      1107.7,
                      1584,
                      1263,
                      1574,
                      1268.3,
                      1656,
                      1113,
                      1666
                    ],
                    "page":1,
                    "confidence":0.918,
                    "elements":[ 
                      "#/readResults/0/lines/8/words/0"
                    ]
                  }
                }
              },
              ...
            ]
          },
          "Subtotal":{ 
            "type":"number",
            "valueNumber":11.7,
            "text":"11.70",
            "boundingBox":[ 
              1146,
              2221,
              1297.3,
              2223,
              1296,
              2319,
              1144.7,
              2317
            ],
            "page":1,
            "confidence":0.955,
            "elements":[ 
              "#/readResults/0/lines/13/words/1"
            ]
          },
          "Tax":{ 
            "type":"number",
            "valueNumber":1.17,
            "text":"1.17",
            "boundingBox":[ 
              1190,
              2359,
              1304,
              2359,
              1304,
              2456,
              1190,
              2456
            ],
            "page":1,
            "confidence":0.979,
            "elements":[ 
              "#/readResults/0/lines/15/words/1"
            ]
          },
          "Tip":{ 
            "type":"number",
            "valueNumber":1.63,
            "text":"1.63",
            "boundingBox":[ 
              1094,
              2479,
              1267.7,
              2485,
              1264,
              2591,
              1090.3,
              2585
            ],
            "page":1,
            "confidence":0.941,
            "elements":[ 
              "#/readResults/0/lines/17/words/1"
            ]
          },
          "Total":{ 
            "type":"number",
            "valueNumber":14.5,
            "text":"$14.50",
            "boundingBox":[ 
              1034.2,
              2617,
              1387.5,
              2638.2,
              1380,
              2763,
              1026.7,
              2741.8
            ],
            "page":1,
            "confidence":0.985,
            "elements":[ 
              "#/readResults/0/lines/19/words/0"
            ]
          }
        }
      }
    ]
  }
}
```


## <a name="customer-scenarios"></a>Scenari dei clienti  

I dati estratti con l'API di ricezione possono essere usati per eseguire varie attività. Di seguito sono riportati alcuni esempi di ciò che i clienti hanno ottenuto con l'API di ricezione. 

### <a name="business-expense-reporting"></a>Report spese aziendali  

L'archiviazione delle spese aziendali comporta spesso un tempo di spesa per l'immissione manuale dei dati dalle immagini delle ricevute. Con l'API di ricezione, è possibile usare i campi estratti per automatizzare parzialmente questo processo e analizzare rapidamente le ricevute.  

Poiché l'API di ricezione ha un semplice output JSON, è possibile usare i valori dei campi estratti in diversi modi. Eseguire l'integrazione con le applicazioni di spesa interne per popolare i report spese. Per ulteriori informazioni su questo scenario, vedere la pagina relativa alla modalità di utilizzo dell'API di ricezione da Acumatica per [la segnalazione di un processo meno doloroso](https://customers.microsoft.com/en-us/story/762684-acumatica-partner-professional-services-azure).  

### <a name="auditing-and-accounting"></a>Controllo e contabilità 

L'output dell'API di ricezione può essere usato anche per eseguire analisi su un numero elevato di spese in diversi punti del processo di segnalazione spese e rimborso. È possibile elaborare le ricevute per la valutazione per il controllo manuale o per le approvazioni rapide.  

L'output della ricevuta è utile anche per la contabilità generale per l'uso aziendale o personale. Usare l'API di ricezione per trasformare i dati di un'immagine di ricezione non elaborata/PDF in un output digitale azionabile.

### <a name="consumer-behavior"></a>Comportamento del consumer 

Le ricevute contengono dati utili che è possibile usare per analizzare il comportamento degli utenti e le tendenze degli acquisti.

L'API di ricezione consente inoltre di alimentare la [funzionalità di elaborazione della ricezione di AIBuilder](https://docs.microsoft.com/ai-builder/prebuilt-receipt-processing).

## <a name="next-steps"></a>Passaggi successivi

- Completare una [Guida introduttiva alla libreria client di riconoscimento moduli](quickstarts/client-library.md) per iniziare a scrivere un'app di elaborazione della ricevuta con il riconoscimento del modulo nel linguaggio scelto.
- In alternativa, seguire la [Guida introduttiva all'API di ricezione Python](./quickstarts/python-receipts.md) per riconoscere le ricevute tramite l'API REST.

## <a name="see-also"></a>Vedere anche

* [Informazioni su Riconoscimento modulo](./overview.md)
* [Documentazione di riferimento per l'API REST](https://westcentralus.dev.cognitive.microsoft.com/docs/services/form-recognizer/api)
