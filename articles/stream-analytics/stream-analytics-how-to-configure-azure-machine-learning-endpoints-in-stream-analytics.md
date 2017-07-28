---
title: Usare gli endpoint di Azure Machine Learning in Analisi di flusso | Documentazione Microsoft
description: Funzioni definite dall'utente di Machine Learning in Analisi di flusso
keywords: 
documentationcenter: 
services: stream-analytics
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 406b258f-b8c2-4e55-953c-b7f84e8e5354
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: jeffstok
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: df60686e7eeb4902bb9df045e28442ade2ad2cb7
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---
# <a name="machine-learning-integration-in-stream-analytics"></a>Integrazione di Machine Learning in Analisi di flusso
Analisi di flusso supporta funzioni definite dall'utente che chiamano gli endpoint di Azure Machine Learning. Il supporto dell'API REST per questa funzionalità è illustrato in dettaglio nella [libreria delle API REST di Analisi di flusso](https://msdn.microsoft.com/library/azure/dn835031.aspx). Questo articolo fornisce le informazioni supplementari necessarie per la corretta implementazione di questa funzionalità in Analisi di flusso. È stata pubblicata anche un'esercitazione che è disponibile [qui](stream-analytics-machine-learning-integration-tutorial.md).

## <a name="overview-azure-machine-learning-terminology"></a>Panoramica: Terminologia di Azure Machine Learning
Microsoft Azure Machine Learning fornisce uno strumento di trascinamento collaborativo che consente di compilare, testare e distribuire soluzioni di analisi predittiva ai dati. Questo strumento si chiama *Azure Machine Learning Studio*. e viene usato per interagire con le risorse di Machine Learning ed eseguire facilmente la compilazione, il test e l'iterazione del progetto. Di seguito sono riportate le risorse e le rispettive definizioni.

* **Area di lavoro**: l' *area di lavoro* è un contenitore che include tutte le altre risorse di Machine Learning per poterle gestire e controllare.
* **Esperimento**: gli *esperimenti* vengono creati dagli esperti di gestione dati per utilizzare i set di dati ed eseguire il training di un modello di Machine Learning.
* **Endpoint**: gli *endpoint* sono gli oggetti di Azure Machine Learning usati per accettare le funzionalità come input, applicare un modello di apprendimento automatico specificato e restituire un output con punteggio.
* **Servizio Web di assegnazione dei punteggi**: un *servizio Web di assegnazione dei punteggi* è una raccolta di endpoint, come indicato sopra.

Ogni endpoint ha API per l'esecuzione batch e per l'esecuzione sincrona. Analisi di flusso usa l'esecuzione sincrona. Il servizio specifico è detto [servizio di richiesta/risposta](../machine-learning/machine-learning-consume-web-services.md) in Azure ML Studio.

## <a name="machine-learning-resources-needed-for-stream-analytics-jobs"></a>Risorse di Machine Learning necessarie per i processi di analisi di flusso
Ai fini dell'elaborazione dei processi di Analisi di flusso, per la corretta esecuzione sono necessari un endpoint di richiesta/risposta, una [chiave API](../machine-learning/machine-learning-connect-to-azure-machine-learning-web-service.md)e una definizione swagger. Analisi di flusso ha un endpoint aggiuntivo che crea l'URL per l'endpoint swagger, cerca l'interfaccia e restituisce all'utente una definizione UDF predefinita.

## <a name="configure-a-stream-analytics-and-machine-learning-udf-via-rest-api"></a>Configurare una funzione definita dall'utente di Analisi di flusso e di Machine Learning con l'API REST
Usando le API REST è possibile configurare il processo per chiamare le funzioni di Azure Machine Learning. Attenersi alla procedura seguente:

1. Creare un processo di Analisi di flusso.
2. Definire un input
3. Definire un output
4. Creare una funzione definita dall'utente
5. Scrivere una trasformazione di Analisi di flusso che chiami la funzione definita dall'utente
6. Avviare il processo

## <a name="creating-a-udf-with-basic-properties"></a>Creazione di una funzione definita dall'utente con proprietà di base
Il codice di esempio seguente crea una funzione definita dall'utente scalare denominata *newudf* che viene associata a un endpoint di Azure Machine Learning. Si noti che l'*endpoint* (URI del servizio) è disponibile nella pagina della Guida dell'API per il servizio scelto e la *chiave API* nella pagina principale dei servizi.

````
    PUT : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>  
````

Corpo della richiesta di esempio:  

````
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
````

## <a name="call-retrievedefaultdefinition-endpoint-for-default-udf"></a>Chiamare l'endpoint RetrieveDefaultDefinition per la funzione definita dall'utente predefinita
Una volta creata la struttura della funzione definita dall'utente, è necessaria la definizione completa della funzione. L'endpoint RetreiveDefaultDefinition consente di ottenere la definizione predefinita per una funzione scalare associata a un endpoint di Azure Machine Learning. Per il payload seguente è necessario ottenere la definizione di definizione definita dall'utente predefinita per una funzione scalare associata a un endpoint di Azure Machine Learning. Il payload non specifica l'endpoint effettivo perché è già stato fornito durante la richiesta PUT. Analisi di flusso chiama l'endpoint fornito nella richiesta se viene specificato in modo esplicito. In caso contrario, usa quello a cui si è fatto riferimento in origine. Qui la funzione definita dall'utente accetta un singolo parametro di stringa (una frase) e restituisce un singolo output di tipo stringa indicante l'etichetta "sentiment" per tale frase.

````
POST : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>/RetrieveDefaultDefinition?api-version=<apiVersion>
````

Corpo della richiesta di esempio:  

````
    {
        "bindingType": "Microsoft.MachineLearning/WebService",
        "bindingRetrievalProperties": {
            "executeEndpoint": null,
            "udfType": "Scalar"
        }
    }
````

Un output di esempio dovrebbe essere simile al seguente.  

````
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
````

## <a name="patch-udf-with-the-response"></a>Inserire la risposta nella funzione definita dall'utente
Ora è necessario inserire la risposta precedente nella funzione definita dall'utente, come illustrato di seguito.

````
PATCH : /subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.StreamAnalytics/streamingjobs/<streamingjobName>/functions/<udfName>?api-version=<apiVersion>
````

Corpo della richiesta (output da RetrieveDefaultDefinition):

````
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
````

## <a name="implement-stream-analytics-transformation-to-call-the-udf"></a>Implementare una trasformazione di Analisi di flusso per chiamare la funzione definita dall'utente
Cercare ora nella funzione definita dall'utente (denominata qui scoreTweet) ogni evento di input e scrivere una risposta per ogni evento in un output.  

````
    {
        "name": "transformation",
        "properties": {
            "streamingUnits": null,
            "query": "select *,scoreTweet(Tweet) TweetSentiment into blobOutput from blobInput"
        }
    }
````


## <a name="get-help"></a>Ottenere aiuto
Per assistenza, provare il [Forum di Analisi di flusso di Azure](https://social.msdn.microsoft.com/Forums/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Passaggi successivi
* [Introduzione ad Analisi dei flussi di Azure](stream-analytics-introduction.md)
* [Introduzione all'uso di Analisi dei flussi di Azure](stream-analytics-real-time-fraud-detection.md)
* [Ridimensionare i processi di Analisi dei flussi di Azure](stream-analytics-scale-jobs.md)
* [Informazioni di riferimento sul linguaggio di query di Analisi dei flussi di Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Informazioni di riferimento sulle API REST di gestione di Analisi di flusso di Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)

