---
title: INTERFACCIA della riga di comando di Azure Service Fabric-eventi sfctl | Microsoft Docs
description: Descrive i comandi degli eventi sfctl dell'interfaccia della riga Service Fabric di comando.
services: service-fabric
documentationcenter: na
author: jeffj6123
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: 858fd1971a22b1db2d243838558c3792d3a60cc9
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901976"
---
# <a name="sfctl-events"></a>eventi sfctl
Recuperare gli eventi dall'archivio eventi, se il servizio EventStore è già installato.

Il servizio di sistema EventStore può essere aggiunto tramite un aggiornamento della configurazione a qualsiasi cluster SFRP che esegue > = 6.4. Verificare l'URL seguente\: HTTPS\://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-eventstore.

## <a name="commands"></a>Comandi:

|Comando|Description|
| --- | --- |
| elenco di tutte le applicazioni | Ottiene tutti gli eventi correlati alle applicazioni. |
| elenco tutti i nodi | Ottiene tutti gli eventi correlati ai nodi. |
| elenco tutti-partizioni | Ottiene tutti gli eventi correlati alle partizioni. |
| elenco tutti i servizi | Ottiene tutti gli eventi correlati ai servizi. |
| Elenco applicazioni | Ottiene gli eventi correlati all'applicazione. |
| elenco cluster | Ottiene tutti gli eventi correlati al cluster. |
| elenco di nodi | Ottiene gli eventi correlati al nodo. |
| Partition-all-repliche-elenco | Ottiene tutti gli eventi correlati alle repliche per una partizione. |
| elenco di partizioni | Ottiene gli eventi correlati alla partizione. |
| Partition-replica-List | Ottiene gli eventi correlati alla replica di partizione. |
| elenco dei servizi | Ottiene un evento correlato al servizio. |

## <a name="sfctl-events-all-applications-list"></a>tutti gli eventi di sfctl-elenco di applicazioni
Ottiene tutti gli eventi correlati alle applicazioni.

La risposta è un elenco di oggetti ApplicationEvent.

### <a name="arguments"></a>Argomenti

|Argomento|Description|
| --- | --- |
| --End-time-UTC [obbligatorio] | L'ora di fine di una query di ricerca in formato ISO UTC aaaa-MM-GGThh\:mm\:ssZ. |
| --Start-Time-UTC [obbligatorio] | Ora di inizio di una query di ricerca in formato ISO UTC aaaa-MM-GGThh\:mm\:ssZ. |
| --Events-types-filtro | Si tratta di una stringa separata da virgole che specifica i tipi di FabricEvents che devono essere inclusi solo nella risposta. |
| --Exclude-Analysis-Events | Questo param Disabilita il recupero di AnalysisEvents se true viene passato. |
| --Skip-Correlation-Lookup | Questo param Disabilita la ricerca di informazioni CorrelatedEvents se viene passato true. in caso contrario, il CorrelationEvents viene elaborato e il campo HasCorrelatedEvents in ogni FabricEvent viene popolato. |
| --timeout -t | Timeout del server per l'esecuzione dell'operazione in secondi. Questo timeout specifica la durata del periodo di attesa del client per il completamento dell'operazione richiesta. Il valore predefinito per questo parametro è 60 secondi.  Valore predefinito\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Description|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-events-all-nodes-list"></a>elenco tutti i nodi di eventi sfctl
Ottiene tutti gli eventi correlati ai nodi.

La risposta è un elenco di oggetti NodeEvent.

### <a name="arguments"></a>Argomenti

|Argomento|Description|
| --- | --- |
| --End-time-UTC [obbligatorio] | L'ora di fine di una query di ricerca in formato ISO UTC aaaa-MM-GGThh\:mm\:ssZ. |
| --Start-Time-UTC [obbligatorio] | Ora di inizio di una query di ricerca in formato ISO UTC aaaa-MM-GGThh\:mm\:ssZ. |
| --Events-types-filtro | Si tratta di una stringa separata da virgole che specifica i tipi di FabricEvents che devono essere inclusi solo nella risposta. |
| --Exclude-Analysis-Events | Questo param Disabilita il recupero di AnalysisEvents se true viene passato. |
| --Skip-Correlation-Lookup | Questo param Disabilita la ricerca di informazioni CorrelatedEvents se viene passato true. in caso contrario, il CorrelationEvents viene elaborato e il campo HasCorrelatedEvents in ogni FabricEvent viene popolato. |
| --timeout -t | Timeout del server per l'esecuzione dell'operazione in secondi. Questo timeout specifica la durata del periodo di attesa del client per il completamento dell'operazione richiesta. Il valore predefinito per questo parametro è 60 secondi.  Valore predefinito\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Description|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-events-all-partitions-list"></a>tutti gli eventi di sfctl-partizioni-elenco
Ottiene tutti gli eventi correlati alle partizioni.

La risposta è un elenco di oggetti PartitionEvent.

### <a name="arguments"></a>Argomenti

|Argomento|Description|
| --- | --- |
| --End-time-UTC [obbligatorio] | L'ora di fine di una query di ricerca in formato ISO UTC aaaa-MM-GGThh\:mm\:ssZ. |
| --Start-Time-UTC [obbligatorio] | Ora di inizio di una query di ricerca in formato ISO UTC aaaa-MM-GGThh\:mm\:ssZ. |
| --Events-types-filtro | Si tratta di una stringa separata da virgole che specifica i tipi di FabricEvents che devono essere inclusi solo nella risposta. |
| --Exclude-Analysis-Events | Questo param Disabilita il recupero di AnalysisEvents se true viene passato. |
| --Skip-Correlation-Lookup | Questo param Disabilita la ricerca di informazioni CorrelatedEvents se viene passato true. in caso contrario, il CorrelationEvents viene elaborato e il campo HasCorrelatedEvents in ogni FabricEvent viene popolato. |
| --timeout -t | Timeout del server per l'esecuzione dell'operazione in secondi. Questo timeout specifica la durata del periodo di attesa del client per il completamento dell'operazione richiesta. Il valore predefinito per questo parametro è 60 secondi.  Valore predefinito\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Description|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-events-all-services-list"></a>tutti gli eventi di sfctl-Services-List
Ottiene tutti gli eventi correlati ai servizi.

La risposta è un elenco di oggetti ServiceEvent.

### <a name="arguments"></a>Argomenti

|Argomento|Description|
| --- | --- |
| --End-time-UTC [obbligatorio] | L'ora di fine di una query di ricerca in formato ISO UTC aaaa-MM-GGThh\:mm\:ssZ. |
| --Start-Time-UTC [obbligatorio] | Ora di inizio di una query di ricerca in formato ISO UTC aaaa-MM-GGThh\:mm\:ssZ. |
| --Events-types-filtro | Si tratta di una stringa separata da virgole che specifica i tipi di FabricEvents che devono essere inclusi solo nella risposta. |
| --Exclude-Analysis-Events | Questo param Disabilita il recupero di AnalysisEvents se true viene passato. |
| --Skip-Correlation-Lookup | Questo param Disabilita la ricerca di informazioni CorrelatedEvents se viene passato true. in caso contrario, il CorrelationEvents viene elaborato e il campo HasCorrelatedEvents in ogni FabricEvent viene popolato. |
| --timeout -t | Timeout del server per l'esecuzione dell'operazione in secondi. Questo timeout specifica la durata del periodo di attesa del client per il completamento dell'operazione richiesta. Il valore predefinito per questo parametro è 60 secondi.  Valore predefinito\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Description|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-events-application-list"></a>applicazione eventi sfctl-elenco
Ottiene gli eventi correlati all'applicazione.

La risposta è un elenco di oggetti ApplicationEvent.

### <a name="arguments"></a>Argomenti

|Argomento|Description|
| --- | --- |
| --application-id [obbligatorio] | Identità dell'applicazione. Si tratta in genere del nome completo dell'applicazione senza lo schema URI "fabric\:". A partire dalla versione 6.0, i nomi gerarchici sono delimitati dal carattere "\~". Ad esempio, se il nome dell'applicazione è "fabric\:/myapp/app1", l'identità dell'applicazione sarà "myapp\~app1" nella versione 6.0 e successive e "myapp/app1" nelle versioni precedenti. |
| --End-time-UTC [obbligatorio] | L'ora di fine di una query di ricerca in formato ISO UTC aaaa-MM-GGThh\:mm\:ssZ. |
| --Start-Time-UTC [obbligatorio] | Ora di inizio di una query di ricerca in formato ISO UTC aaaa-MM-GGThh\:mm\:ssZ. |
| --Events-types-filtro | Si tratta di una stringa separata da virgole che specifica i tipi di FabricEvents che devono essere inclusi solo nella risposta. |
| --Exclude-Analysis-Events | Questo param Disabilita il recupero di AnalysisEvents se true viene passato. |
| --Skip-Correlation-Lookup | Questo param Disabilita la ricerca di informazioni CorrelatedEvents se viene passato true. in caso contrario, il CorrelationEvents viene elaborato e il campo HasCorrelatedEvents in ogni FabricEvent viene popolato. |
| --timeout -t | Timeout del server per l'esecuzione dell'operazione in secondi. Questo timeout specifica la durata del periodo di attesa del client per il completamento dell'operazione richiesta. Il valore predefinito per questo parametro è 60 secondi.  Valore predefinito\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Description|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-events-cluster-list"></a>cluster di eventi sfctl-elenco
Ottiene tutti gli eventi correlati al cluster.

La risposta è un elenco di oggetti ClusterEvent.

### <a name="arguments"></a>Argomenti

|Argomento|Description|
| --- | --- |
| --End-time-UTC [obbligatorio] | L'ora di fine di una query di ricerca in formato ISO UTC aaaa-MM-GGThh\:mm\:ssZ. |
| --Start-Time-UTC [obbligatorio] | Ora di inizio di una query di ricerca in formato ISO UTC aaaa-MM-GGThh\:mm\:ssZ. |
| --Events-types-filtro | Si tratta di una stringa separata da virgole che specifica i tipi di FabricEvents che devono essere inclusi solo nella risposta. |
| --Exclude-Analysis-Events | Questo param Disabilita il recupero di AnalysisEvents se true viene passato. |
| --Skip-Correlation-Lookup | Questo param Disabilita la ricerca di informazioni CorrelatedEvents se viene passato true. in caso contrario, il CorrelationEvents viene elaborato e il campo HasCorrelatedEvents in ogni FabricEvent viene popolato. |
| --timeout -t | Timeout del server per l'esecuzione dell'operazione in secondi. Questo timeout specifica la durata del periodo di attesa del client per il completamento dell'operazione richiesta. Il valore predefinito per questo parametro è 60 secondi.  Valore predefinito\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Description|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-events-node-list"></a>elenco di nodi degli eventi di sfctl
Ottiene gli eventi correlati al nodo.

La risposta è un elenco di oggetti NodeEvent.

### <a name="arguments"></a>Argomenti

|Argomento|Description|
| --- | --- |
| --End-time-UTC [obbligatorio] | L'ora di fine di una query di ricerca in formato ISO UTC aaaa-MM-GGThh\:mm\:ssZ. |
| --node-name [obbligatorio] | Il nome del nodo. |
| --Start-Time-UTC [obbligatorio] | Ora di inizio di una query di ricerca in formato ISO UTC aaaa-MM-GGThh\:mm\:ssZ. |
| --Events-types-filtro | Si tratta di una stringa separata da virgole che specifica i tipi di FabricEvents che devono essere inclusi solo nella risposta. |
| --Exclude-Analysis-Events | Questo param Disabilita il recupero di AnalysisEvents se true viene passato. |
| --Skip-Correlation-Lookup | Questo param Disabilita la ricerca di informazioni CorrelatedEvents se viene passato true. in caso contrario, il CorrelationEvents viene elaborato e il campo HasCorrelatedEvents in ogni FabricEvent viene popolato. |
| --timeout -t | Timeout del server per l'esecuzione dell'operazione in secondi. Questo timeout specifica la durata del periodo di attesa del client per il completamento dell'operazione richiesta. Il valore predefinito per questo parametro è 60 secondi.  Valore predefinito\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Description|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-events-partition-all-replicas-list"></a>sfctl di eventi partizione-tutti-repliche-elenco
Ottiene tutti gli eventi correlati alle repliche per una partizione.

La risposta è un elenco di oggetti ReplicaEvent.

### <a name="arguments"></a>Argomenti

|Argomento|Description|
| --- | --- |
| --End-time-UTC [obbligatorio] | L'ora di fine di una query di ricerca in formato ISO UTC aaaa-MM-GGThh\:mm\:ssZ. |
| --partition-id   [obbligatorio] | L'identità della partizione. |
| --Start-Time-UTC [obbligatorio] | Ora di inizio di una query di ricerca in formato ISO UTC aaaa-MM-GGThh\:mm\:ssZ. |
| --Events-types-filtro | Si tratta di una stringa separata da virgole che specifica i tipi di FabricEvents che devono essere inclusi solo nella risposta. |
| --Exclude-Analysis-Events | Questo param Disabilita il recupero di AnalysisEvents se true viene passato. |
| --Skip-Correlation-Lookup | Questo param Disabilita la ricerca di informazioni CorrelatedEvents se viene passato true. in caso contrario, il CorrelationEvents viene elaborato e il campo HasCorrelatedEvents in ogni FabricEvent viene popolato. |
| --timeout -t | Timeout del server per l'esecuzione dell'operazione in secondi. Questo timeout specifica la durata del periodo di attesa del client per il completamento dell'operazione richiesta. Il valore predefinito per questo parametro è 60 secondi.  Valore predefinito\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Description|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-events-partition-list"></a>elenco di partizioni degli eventi di sfctl
Ottiene gli eventi correlati alla partizione.

La risposta è un elenco di oggetti PartitionEvent.

### <a name="arguments"></a>Argomenti

|Argomento|Description|
| --- | --- |
| --End-time-UTC [obbligatorio] | L'ora di fine di una query di ricerca in formato ISO UTC aaaa-MM-GGThh\:mm\:ssZ. |
| --partition-id   [obbligatorio] | L'identità della partizione. |
| --Start-Time-UTC [obbligatorio] | Ora di inizio di una query di ricerca in formato ISO UTC aaaa-MM-GGThh\:mm\:ssZ. |
| --Events-types-filtro | Si tratta di una stringa separata da virgole che specifica i tipi di FabricEvents che devono essere inclusi solo nella risposta. |
| --Exclude-Analysis-Events | Questo param Disabilita il recupero di AnalysisEvents se true viene passato. |
| --Skip-Correlation-Lookup | Questo param Disabilita la ricerca di informazioni CorrelatedEvents se viene passato true. in caso contrario, il CorrelationEvents viene elaborato e il campo HasCorrelatedEvents in ogni FabricEvent viene popolato. |
| --timeout -t | Timeout del server per l'esecuzione dell'operazione in secondi. Questo timeout specifica la durata del periodo di attesa del client per il completamento dell'operazione richiesta. Il valore predefinito per questo parametro è 60 secondi.  Valore predefinito\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Description|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-events-partition-replica-list"></a>partizione degli eventi di sfctl-replica-elenco
Ottiene gli eventi correlati alla replica di partizione.

La risposta è un elenco di oggetti ReplicaEvent.

### <a name="arguments"></a>Argomenti

|Argomento|Description|
| --- | --- |
| --End-time-UTC [obbligatorio] | L'ora di fine di una query di ricerca in formato ISO UTC aaaa-MM-GGThh\:mm\:ssZ. |
| --partition-id   [obbligatorio] | L'identità della partizione. |
| --replica-ID [obbligatorio] | Identificatore della replica. |
| --Start-Time-UTC [obbligatorio] | Ora di inizio di una query di ricerca in formato ISO UTC aaaa-MM-GGThh\:mm\:ssZ. |
| --Events-types-filtro | Si tratta di una stringa separata da virgole che specifica i tipi di FabricEvents che devono essere inclusi solo nella risposta. |
| --Exclude-Analysis-Events | Questo param Disabilita il recupero di AnalysisEvents se true viene passato. |
| --Skip-Correlation-Lookup | Questo param Disabilita la ricerca di informazioni CorrelatedEvents se viene passato true. in caso contrario, il CorrelationEvents viene elaborato e il campo HasCorrelatedEvents in ogni FabricEvent viene popolato. |
| --timeout -t | Timeout del server per l'esecuzione dell'operazione in secondi. Questo timeout specifica la durata del periodo di attesa del client per il completamento dell'operazione richiesta. Il valore predefinito per questo parametro è 60 secondi.  Valore predefinito\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Description|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-events-service-list"></a>servizio eventi sfctl-elenco
Ottiene un evento correlato al servizio.

La risposta è un elenco di oggetti ServiceEvent.

### <a name="arguments"></a>Argomenti

|Argomento|Description|
| --- | --- |
| --End-time-UTC [obbligatorio] | L'ora di fine di una query di ricerca in formato ISO UTC aaaa-MM-GGThh\:mm\:ssZ. |
| --service-id             [obbligatorio] | L'identità del servizio. L'ID corrisponde in genere al nome completo del servizio senza lo schema URI "fabric\:". A partire dalla versione 6.0, i nomi gerarchici sono delimitati dal carattere "\~". Ad esempio, se il nome del servizio è "fabric\:/myapp/app1/svc1", l'identità del servizio sarà "myapp\~app1\~svc1" nella versione 6.0 e successive e "myapp/app1/svc1" nelle versioni precedenti. |
| --Start-Time-UTC [obbligatorio] | Ora di inizio di una query di ricerca in formato ISO UTC aaaa-MM-GGThh\:mm\:ssZ. |
| --Events-types-filtro | Si tratta di una stringa separata da virgole che specifica i tipi di FabricEvents che devono essere inclusi solo nella risposta. |
| --Exclude-Analysis-Events | Questo param Disabilita il recupero di AnalysisEvents se true viene passato. |
| --Skip-Correlation-Lookup | Questo param Disabilita la ricerca di informazioni CorrelatedEvents se viene passato true. in caso contrario, il CorrelationEvents viene elaborato e il campo HasCorrelatedEvents in ogni FabricEvent viene popolato. |
| --timeout -t | Timeout del server per l'esecuzione dell'operazione in secondi. Questo timeout specifica la durata del periodo di attesa del client per il completamento dell'operazione richiesta. Il valore predefinito per questo parametro è 60 secondi.  Valore predefinito\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Description|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

