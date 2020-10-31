---
title: Usare gli endpoint Azure Machine Learning Studio (classico) in analisi di flusso di Azure
description: Questo articolo descrive come usare funzioni di Machine Learning definite dall'utente in Analisi di flusso di Azure.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/11/2019
ms.openlocfilehash: 236191710dac19a08db0e8ce94dc695d393009a7
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93127128"
---
# <a name="azure-machine-learning-studio-classic-integration-in-stream-analytics-preview"></a>Integrazione di Azure Machine Learning Studio (versione classica) in Analisi di flusso (anteprima)
Analisi di flusso supporta funzioni definite dall'utente che chiamano gli endpoint di Azure Machine Learning Studio (versione classica). Il supporto dell'API REST per questa funzionalità è illustrato in dettaglio nella [libreria delle API REST di Analisi di flusso](/rest/api/streamanalytics/). Questo articolo fornisce le informazioni supplementari necessarie per la corretta implementazione di questa funzionalità in Analisi di flusso. È stata pubblicata anche un'esercitazione che è disponibile [qui](stream-analytics-machine-learning-integration-tutorial.md).

## <a name="overview-azure-machine-learning-studio-classic-terminology"></a>Panoramica: Terminologia di Azure Machine Learning Studio (versione classica)
Microsoft Machine Learning Studio (versione classica) fornisce uno strumento di trascinamento della selezione collaborativo che consente di compilare, testare e distribuire soluzioni di analisi predittiva ai dati. Questo strumento è denominato *Azure Machine Learning Studio (classico)* . Studio (classico) viene usato per interagire con le risorse di machine learning e creare, testare e iterare con facilità la progettazione. Di seguito sono riportate le risorse e le rispettive definizioni.

* **Area di lavoro** : l' *area di lavoro* è un contenitore che include tutte le altre risorse di Machine Learning insieme in un contenitore per la gestione e il controllo.
* **Esperimento** : gli *esperimenti* vengono creati dagli esperti di gestione dati per utilizzare i set di dati ed eseguire il training di un modello di Machine Learning.
* **Endpoint** : gli *endpoint* sono l'oggetto Studio (classico) usato per acquisire le funzionalità come input, applicare un modello di apprendimento automatico specificato e restituire un output con punteggio.
* **Servizio Web di assegnazione dei punteggi** : un *servizio Web di assegnazione dei punteggi* è una raccolta di endpoint, come indicato sopra.

Ogni endpoint ha API per l'esecuzione batch e per l'esecuzione sincrona. Analisi di flusso usa l'esecuzione sincrona. Il servizio specifico è detto [servizio di richiesta/risposta](../machine-learning/classic/consume-web-services.md) in Azure Machine Learning Studio (versione classica).

## <a name="studio-classic-resources-needed-for-stream-analytics-jobs"></a>Risorse di studio (classiche) necessarie per i processi di analisi di flusso
Ai fini dell'elaborazione dei processi di Analisi di flusso, per la corretta esecuzione sono necessari un endpoint di richiesta/risposta, una [chiave API](../machine-learning/classic/consume-web-services.md)e una definizione swagger. Analisi di flusso ha un endpoint aggiuntivo che crea l'URL per l'endpoint swagger, cerca l'interfaccia e restituisce all'utente una definizione UDF predefinita.

## <a name="configure-a-stream-analytics-and-studio-classic-udf-via-rest-api"></a>Configurare una UDF di analisi di flusso e studio (classica) tramite l'API REST
Usando le API REST, è possibile configurare il processo per chiamare le funzioni Studio (classiche). Attenersi alla procedura seguente:

1. Creare un processo di Analisi di flusso.
2. Definire un input
3. Definire un output
4. Creare una funzione definita dall'utente
5. Scrivere una trasformazione di Analisi di flusso che chiami la funzione definita dall'utente
6. Avviare il processo

## <a name="creating-a-udf-with-basic-properties"></a>Creazione di una funzione definita dall'utente con proprietà di base
Il codice di esempio seguente crea una funzione definita dall'utente scalare denominata *newudf* che viene associata a un endpoint di Azure Machine Learning Studio (versione classica). Si noti che l' *endpoint* (URI del servizio) è disponibile nella pagina della Guida dell'API per il servizio scelto e la *chiave API* nella pagina principale dei servizi.

```
    PUT : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>
```

Corpo della richiesta di esempio:

```json
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77fb4b46bf2a30c63c078dca/services/b7be5e40fd194258796fb402c1958eaf/execute ",
                        "apiKey": "replacekeyhere"
                    }
                }
            }
        }
    }
```

## <a name="call-retrievedefaultdefinition-endpoint-for-default-udf"></a>Chiamare l'endpoint RetrieveDefaultDefinition per la funzione definita dall'utente predefinita
Una volta creata la struttura della funzione definita dall'utente, è necessaria la definizione completa della funzione. L'endpoint RetrieveDefaultDefinition consente di ottenere la definizione predefinita per una funzione scalare associata a un endpoint di Azure Machine Learning Studio (versione classica). Il payload seguente richiede di ottenere la definizione UDF predefinita per una funzione scalare associata a un endpoint di studio (classico). Non specifica l'endpoint effettivo perché è già stato fornito durante la richiesta PUT. Analisi di flusso chiama l'endpoint fornito nella richiesta se viene specificato in modo esplicito. In caso contrario, usa quello a cui si è fatto riferimento in origine. Qui la funzione definita dall'utente accetta un singolo parametro di stringa (una frase) e restituisce un singolo output di tipo stringa indicante l'etichetta "sentiment" per tale frase.

```
POST : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>/RetrieveDefaultDefinition?api-version=<apiVersion>
```

Corpo della richiesta di esempio:

```json
    {
        "bindingType": "Microsoft.MachineLearning/WebService",
        "bindingRetrievalProperties": {
            "executeEndpoint": null,
            "udfType": "Scalar"
        }
    }
```

Un output di esempio dovrebbe essere simile al seguente.

```json
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "inputs": [{
                    "dataType": "nvarchar(max)",
                    "isConfigurationParameter": null
                }],
                "output": {
                    "dataType": "nvarchar(max)"
                },
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77ga4a4bbf2a30c63c078dca/services/b7be5e40fd194258896fb602c1858eaf/execute",
                        "apiKey": null,
                        "inputs": {
                            "name": "input1",
                            "columnNames": [{
                                "name": "tweet",
                                "dataType": "string",
                                "mapTo": 0
                            }]
                        },
                        "outputs": [{
                            "name": "Sentiment",
                            "dataType": "string"
                        }],
                        "batchSize": 10
                    }
                }
            }
        }
    }
```

## <a name="patch-udf-with-the-response"></a>Inserire la risposta nella funzione definita dall'utente
Ora è necessario inserire la risposta precedente nella funzione definita dall'utente, come illustrato di seguito.

```
PATCH : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>
```

Corpo della richiesta (output da RetrieveDefaultDefinition):

```json
    {
        "name": "newudf",
        "properties": {
            "type": "Scalar",
            "properties": {
                "inputs": [{
                    "dataType": "nvarchar(max)",
                    "isConfigurationParameter": null
                }],
                "output": {
                    "dataType": "nvarchar(max)"
                },
                "binding": {
                    "type": "Microsoft.MachineLearning/WebService",
                    "properties": {
                        "endpoint": "https://ussouthcentral.services.azureml.net/workspaces/f80d5d7a77ga4a4bbf2a30c63c078dca/services/b7be5e40fd194258896fb602c1858eaf/execute",
                        "apiKey": null,
                        "inputs": {
                            "name": "input1",
                            "columnNames": [{
                                "name": "tweet",
                                "dataType": "string",
                                "mapTo": 0
                            }]
                        },
                        "outputs": [{
                            "name": "Sentiment",
                            "dataType": "string"
                        }],
                        "batchSize": 10
                    }
                }
            }
        }
    }
```

## <a name="implement-stream-analytics-transformation-to-call-the-udf"></a>Implementare una trasformazione di Analisi di flusso per chiamare la funzione definita dall'utente
Cercare ora nella funzione definita dall'utente (denominata qui scoreTweet) ogni evento di input e scrivere una risposta per ogni evento in un output.

```json
    {
        "name": "transformation",
        "properties": {
            "streamingUnits": null,
            "query": "select *,scoreTweet(Tweet) TweetSentiment into blobOutput from blobInput"
        }
    }
```


## <a name="get-help"></a>Ottenere aiuto
Per maggiore supporto, provare la [Pagina delle domande di Domande e risposte Microsoft per Analisi di flusso di Azure](/answers/topics/azure-stream-analytics.html)

## <a name="next-steps"></a>Passaggi successivi
* [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
* [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-real-time-fraud-detection.md)
* [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi di flusso di Azure](/stream-analytics-query/stream-analytics-query-language-reference)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](/rest/api/streamanalytics/)