---
title: Interfaccia della riga di comando di Azure Service Fabric - Eventi sfctlAzure Service Fabric CLI- sfctl events
description: Descrive i comandi degli eventi sfctl dell'interfaccia della riga di comando di Service Fabric.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 635bddef1a98d6ed2b112662d8c9c44fe65e9e32
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906099"
---
# <a name="sfctl-events"></a>eventi sfctl
Recuperare gli eventi dall'archivio eventi (se il servizio EventStore è già installato).

Il servizio di sistema EventStore può essere aggiunto tramite un aggiornamento di configurazione a qualsiasi cluster SFRP in esecuzione >6,4. Si prega di\: \:controllare il seguente url https //docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-eventstore.

## <a name="commands"></a>Comandi:

|Comando|Descrizione|
| --- | --- |
| all-applications-list | Ottiene tutti gli eventi correlati alle applicazioni. |
| all-nodes-list | Ottiene tutti gli eventi correlati ai nodi. |
| all-partitions-list | Ottiene tutti gli eventi correlati alle partizioni. |
| all-services-list | Ottiene tutti gli eventi correlati ai servizi. |
| elenco applicazioni | Ottiene un evento correlato all'applicazione. |
| elenco di cluster | Ottiene tutti gli eventi correlati al cluster. |
| node-list | Ottiene un Node-correlati eventi. |
| partition-all-replicas-list | Ottiene tutti gli eventi correlati alle repliche per una partizione. |
| elenco di partizioni | Ottiene un partition-correlati eventi. |
| elenco di partizioni | Ottiene un evento correlato a Replica partizione. |
| elenco dei servizi | Ottiene un evento correlato al servizio. |

## <a name="sfctl-events-all-applications-list"></a>Eventi sfctl all-applications-list
Ottiene tutti gli eventi correlati alle applicazioni.

La risposta è un elenco di oggetti ApplicationEvent.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --end-time-utc [Obbligatorio] | Ora di fine di una query di ricerca in ISO UTC\:\:aaaa-MM-ddTHH mm ss. |
| --start-time-utc [Obbligatorio] | L'ora di inizio di una query di ricerca in ISO\:\:UTC aaaa-MM-ddTHH mm ss. |
| --events-types-filter | Si tratta di una stringa separata da virgole che specifica i tipi di FabricEvents che devono essere inclusi solo nella risposta. |
| --exclude-analysis-events | Questo parametro disabilita il recupero di AnalysisEvents se viene passato true. |
| --skip-correlation-lookup | Questo parametro disabilita la ricerca di informazioni CorrelatedEvents se viene passato true. in caso contrario, il CorrelationEvents viene elaborato e il campo HasCorrelatedEvents in ogni FabricEvent viene popolato. |
| --timeout -t | Timeout del server per l'esecuzione dell'operazione in secondi. Questo timeout specifica il periodo di tempo durante il quale il client è disposto ad attendere il completamento dell'operazione richiesta. Il valore predefinito per questo parametro è 60 secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio della registrazione per mostrare tutti i log di debug. |
| --help -h | Visualizza questo messaggio della guida ed esce. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio della registrazione. Usare --debug per log di debug completi. |

## <a name="sfctl-events-all-nodes-list"></a>Eventi sfctl all-nodes-list
Ottiene tutti gli eventi correlati ai nodi.

La risposta è un elenco di oggetti NodeEvent.The response is list of NodeEvent objects.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --end-time-utc [Obbligatorio] | Ora di fine di una query di ricerca in ISO UTC\:\:aaaa-MM-ddTHH mm ss. |
| --start-time-utc [Obbligatorio] | L'ora di inizio di una query di ricerca in ISO\:\:UTC aaaa-MM-ddTHH mm ss. |
| --events-types-filter | Si tratta di una stringa separata da virgole che specifica i tipi di FabricEvents che devono essere inclusi solo nella risposta. |
| --exclude-analysis-events | Questo parametro disabilita il recupero di AnalysisEvents se viene passato true. |
| --skip-correlation-lookup | Questo parametro disabilita la ricerca di informazioni CorrelatedEvents se viene passato true. in caso contrario, il CorrelationEvents viene elaborato e il campo HasCorrelatedEvents in ogni FabricEvent viene popolato. |
| --timeout -t | Timeout del server per l'esecuzione dell'operazione in secondi. Questo timeout specifica il periodo di tempo durante il quale il client è disposto ad attendere il completamento dell'operazione richiesta. Il valore predefinito per questo parametro è 60 secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio della registrazione per mostrare tutti i log di debug. |
| --help -h | Visualizza questo messaggio della guida ed esce. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio della registrazione. Usare --debug per log di debug completi. |

## <a name="sfctl-events-all-partitions-list"></a>sfctl eventi all-partitions-list
Ottiene tutti gli eventi correlati alle partizioni.

La risposta è l'elenco di oggetti PartitionEvent.The response is list of PartitionEvent objects.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --end-time-utc [Obbligatorio] | Ora di fine di una query di ricerca in ISO UTC\:\:aaaa-MM-ddTHH mm ss. |
| --start-time-utc [Obbligatorio] | L'ora di inizio di una query di ricerca in ISO\:\:UTC aaaa-MM-ddTHH mm ss. |
| --events-types-filter | Si tratta di una stringa separata da virgole che specifica i tipi di FabricEvents che devono essere inclusi solo nella risposta. |
| --exclude-analysis-events | Questo parametro disabilita il recupero di AnalysisEvents se viene passato true. |
| --skip-correlation-lookup | Questo parametro disabilita la ricerca di informazioni CorrelatedEvents se viene passato true. in caso contrario, il CorrelationEvents viene elaborato e il campo HasCorrelatedEvents in ogni FabricEvent viene popolato. |
| --timeout -t | Timeout del server per l'esecuzione dell'operazione in secondi. Questo timeout specifica il periodo di tempo durante il quale il client è disposto ad attendere il completamento dell'operazione richiesta. Il valore predefinito per questo parametro è 60 secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio della registrazione per mostrare tutti i log di debug. |
| --help -h | Visualizza questo messaggio della guida ed esce. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio della registrazione. Usare --debug per log di debug completi. |

## <a name="sfctl-events-all-services-list"></a>sfctl eventi all-services-list
Ottiene tutti gli eventi correlati ai servizi.

La risposta è l'elenco di oggetti ServiceEvent.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --end-time-utc [Obbligatorio] | Ora di fine di una query di ricerca in ISO UTC\:\:aaaa-MM-ddTHH mm ss. |
| --start-time-utc [Obbligatorio] | L'ora di inizio di una query di ricerca in ISO\:\:UTC aaaa-MM-ddTHH mm ss. |
| --events-types-filter | Si tratta di una stringa separata da virgole che specifica i tipi di FabricEvents che devono essere inclusi solo nella risposta. |
| --exclude-analysis-events | Questo parametro disabilita il recupero di AnalysisEvents se viene passato true. |
| --skip-correlation-lookup | Questo parametro disabilita la ricerca di informazioni CorrelatedEvents se viene passato true. in caso contrario, il CorrelationEvents viene elaborato e il campo HasCorrelatedEvents in ogni FabricEvent viene popolato. |
| --timeout -t | Timeout del server per l'esecuzione dell'operazione in secondi. Questo timeout specifica il periodo di tempo durante il quale il client è disposto ad attendere il completamento dell'operazione richiesta. Il valore predefinito per questo parametro è 60 secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio della registrazione per mostrare tutti i log di debug. |
| --help -h | Visualizza questo messaggio della guida ed esce. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio della registrazione. Usare --debug per log di debug completi. |

## <a name="sfctl-events-application-list"></a>eventi sfctl elenco applicazioni-list
Ottiene un evento correlato all'applicazione.

La risposta è un elenco di oggetti ApplicationEvent.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --application-id [obbligatorio] | Identità dell'applicazione. Si tratta in genere del nome completo dell'applicazione senza lo schema URI "fabric\:". A partire dalla versione 6.0, i nomi gerarchici sono delimitati dal carattere "\~". Ad esempio, se il nome dell'applicazione è "fabric\:/myapp/app1", l'identità dell'applicazione sarà "myapp\~app1" nella versione 6.0 e successive e "myapp/app1" nelle versioni precedenti. |
| --end-time-utc [Obbligatorio] | Ora di fine di una query di ricerca in ISO UTC\:\:aaaa-MM-ddTHH mm ss. |
| --start-time-utc [Obbligatorio] | L'ora di inizio di una query di ricerca in ISO\:\:UTC aaaa-MM-ddTHH mm ss. |
| --events-types-filter | Si tratta di una stringa separata da virgole che specifica i tipi di FabricEvents che devono essere inclusi solo nella risposta. |
| --exclude-analysis-events | Questo parametro disabilita il recupero di AnalysisEvents se viene passato true. |
| --skip-correlation-lookup | Questo parametro disabilita la ricerca di informazioni CorrelatedEvents se viene passato true. in caso contrario, il CorrelationEvents viene elaborato e il campo HasCorrelatedEvents in ogni FabricEvent viene popolato. |
| --timeout -t | Timeout del server per l'esecuzione dell'operazione in secondi. Questo timeout specifica il periodo di tempo durante il quale il client è disposto ad attendere il completamento dell'operazione richiesta. Il valore predefinito per questo parametro è 60 secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio della registrazione per mostrare tutti i log di debug. |
| --help -h | Visualizza questo messaggio della guida ed esce. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio della registrazione. Usare --debug per log di debug completi. |

## <a name="sfctl-events-cluster-list"></a>elenco cluster di eventi sfctl
Ottiene tutti gli eventi correlati al cluster.

La risposta è un elenco di oggetti ClusterEvent.The response is list of ClusterEvent objects.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --end-time-utc [Obbligatorio] | Ora di fine di una query di ricerca in ISO UTC\:\:aaaa-MM-ddTHH mm ss. |
| --start-time-utc [Obbligatorio] | L'ora di inizio di una query di ricerca in ISO\:\:UTC aaaa-MM-ddTHH mm ss. |
| --events-types-filter | Si tratta di una stringa separata da virgole che specifica i tipi di FabricEvents che devono essere inclusi solo nella risposta. |
| --exclude-analysis-events | Questo parametro disabilita il recupero di AnalysisEvents se viene passato true. |
| --skip-correlation-lookup | Questo parametro disabilita la ricerca di informazioni CorrelatedEvents se viene passato true. in caso contrario, il CorrelationEvents viene elaborato e il campo HasCorrelatedEvents in ogni FabricEvent viene popolato. |
| --timeout -t | Timeout del server per l'esecuzione dell'operazione in secondi. Questo timeout specifica il periodo di tempo durante il quale il client è disposto ad attendere il completamento dell'operazione richiesta. Il valore predefinito per questo parametro è 60 secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio della registrazione per mostrare tutti i log di debug. |
| --help -h | Visualizza questo messaggio della guida ed esce. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio della registrazione. Usare --debug per log di debug completi. |

## <a name="sfctl-events-node-list"></a>Elenco di nodi di eventi sfctl
Ottiene un Node-correlati eventi.

La risposta è un elenco di oggetti NodeEvent.The response is list of NodeEvent objects.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --end-time-utc [Obbligatorio] | Ora di fine di una query di ricerca in ISO UTC\:\:aaaa-MM-ddTHH mm ss. |
| --node-name [obbligatorio] | Il nome del nodo. |
| --start-time-utc [Obbligatorio] | L'ora di inizio di una query di ricerca in ISO\:\:UTC aaaa-MM-ddTHH mm ss. |
| --events-types-filter | Si tratta di una stringa separata da virgole che specifica i tipi di FabricEvents che devono essere inclusi solo nella risposta. |
| --exclude-analysis-events | Questo parametro disabilita il recupero di AnalysisEvents se viene passato true. |
| --skip-correlation-lookup | Questo parametro disabilita la ricerca di informazioni CorrelatedEvents se viene passato true. in caso contrario, il CorrelationEvents viene elaborato e il campo HasCorrelatedEvents in ogni FabricEvent viene popolato. |
| --timeout -t | Timeout del server per l'esecuzione dell'operazione in secondi. Questo timeout specifica il periodo di tempo durante il quale il client è disposto ad attendere il completamento dell'operazione richiesta. Il valore predefinito per questo parametro è 60 secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio della registrazione per mostrare tutti i log di debug. |
| --help -h | Visualizza questo messaggio della guida ed esce. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio della registrazione. Usare --debug per log di debug completi. |

## <a name="sfctl-events-partition-all-replicas-list"></a>Sfctl eventi partition-all-replicas-list
Ottiene tutti gli eventi correlati alle repliche per una partizione.

La risposta è un elenco di oggetti ReplicaEvent.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --end-time-utc [Obbligatorio] | Ora di fine di una query di ricerca in ISO UTC\:\:aaaa-MM-ddTHH mm ss. |
| --partition-id   [obbligatorio] | L'identità della partizione. |
| --start-time-utc [Obbligatorio] | L'ora di inizio di una query di ricerca in ISO\:\:UTC aaaa-MM-ddTHH mm ss. |
| --events-types-filter | Si tratta di una stringa separata da virgole che specifica i tipi di FabricEvents che devono essere inclusi solo nella risposta. |
| --exclude-analysis-events | Questo parametro disabilita il recupero di AnalysisEvents se viene passato true. |
| --skip-correlation-lookup | Questo parametro disabilita la ricerca di informazioni CorrelatedEvents se viene passato true. in caso contrario, il CorrelationEvents viene elaborato e il campo HasCorrelatedEvents in ogni FabricEvent viene popolato. |
| --timeout -t | Timeout del server per l'esecuzione dell'operazione in secondi. Questo timeout specifica il periodo di tempo durante il quale il client è disposto ad attendere il completamento dell'operazione richiesta. Il valore predefinito per questo parametro è 60 secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio della registrazione per mostrare tutti i log di debug. |
| --help -h | Visualizza questo messaggio della guida ed esce. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio della registrazione. Usare --debug per log di debug completi. |

## <a name="sfctl-events-partition-list"></a>elenco di partizioni di eventi sfctl
Ottiene un partition-correlati eventi.

La risposta è l'elenco di oggetti PartitionEvent.The response is list of PartitionEvent objects.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --end-time-utc [Obbligatorio] | Ora di fine di una query di ricerca in ISO UTC\:\:aaaa-MM-ddTHH mm ss. |
| --partition-id   [obbligatorio] | L'identità della partizione. |
| --start-time-utc [Obbligatorio] | L'ora di inizio di una query di ricerca in ISO\:\:UTC aaaa-MM-ddTHH mm ss. |
| --events-types-filter | Si tratta di una stringa separata da virgole che specifica i tipi di FabricEvents che devono essere inclusi solo nella risposta. |
| --exclude-analysis-events | Questo parametro disabilita il recupero di AnalysisEvents se viene passato true. |
| --skip-correlation-lookup | Questo parametro disabilita la ricerca di informazioni CorrelatedEvents se viene passato true. in caso contrario, il CorrelationEvents viene elaborato e il campo HasCorrelatedEvents in ogni FabricEvent viene popolato. |
| --timeout -t | Timeout del server per l'esecuzione dell'operazione in secondi. Questo timeout specifica il periodo di tempo durante il quale il client è disposto ad attendere il completamento dell'operazione richiesta. Il valore predefinito per questo parametro è 60 secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio della registrazione per mostrare tutti i log di debug. |
| --help -h | Visualizza questo messaggio della guida ed esce. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio della registrazione. Usare --debug per log di debug completi. |

## <a name="sfctl-events-partition-replica-list"></a>Eventi sfctl partition-replica-list
Ottiene un evento correlato a Replica partizione.

La risposta è un elenco di oggetti ReplicaEvent.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --end-time-utc [Obbligatorio] | Ora di fine di una query di ricerca in ISO UTC\:\:aaaa-MM-ddTHH mm ss. |
| --partition-id   [obbligatorio] | L'identità della partizione. |
| --replica-id [Obbligatorio] | Identificatore della replica. |
| --start-time-utc [Obbligatorio] | L'ora di inizio di una query di ricerca in ISO\:\:UTC aaaa-MM-ddTHH mm ss. |
| --events-types-filter | Si tratta di una stringa separata da virgole che specifica i tipi di FabricEvents che devono essere inclusi solo nella risposta. |
| --exclude-analysis-events | Questo parametro disabilita il recupero di AnalysisEvents se viene passato true. |
| --skip-correlation-lookup | Questo parametro disabilita la ricerca di informazioni CorrelatedEvents se viene passato true. in caso contrario, il CorrelationEvents viene elaborato e il campo HasCorrelatedEvents in ogni FabricEvent viene popolato. |
| --timeout -t | Timeout del server per l'esecuzione dell'operazione in secondi. Questo timeout specifica il periodo di tempo durante il quale il client è disposto ad attendere il completamento dell'operazione richiesta. Il valore predefinito per questo parametro è 60 secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio della registrazione per mostrare tutti i log di debug. |
| --help -h | Visualizza questo messaggio della guida ed esce. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio della registrazione. Usare --debug per log di debug completi. |

## <a name="sfctl-events-service-list"></a>Elenco di service per gli eventi sfctl
Ottiene un evento correlato al servizio.

La risposta è l'elenco di oggetti ServiceEvent.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --end-time-utc [Obbligatorio] | Ora di fine di una query di ricerca in ISO UTC\:\:aaaa-MM-ddTHH mm ss. |
| --service-id             [obbligatorio] | L'identità del servizio. L'ID corrisponde in genere al nome completo del servizio senza lo schema URI "fabric\:". A partire dalla versione 6.0, i nomi gerarchici sono delimitati dal carattere "\~". Ad esempio, se il nome del servizio è "fabric\:/myapp/app1/svc1", l'identità del servizio sarà "myapp\~app1\~svc1" nella versione 6.0 e successive e "myapp/app1/svc1" nelle versioni precedenti. |
| --start-time-utc [Obbligatorio] | L'ora di inizio di una query di ricerca in ISO\:\:UTC aaaa-MM-ddTHH mm ss. |
| --events-types-filter | Si tratta di una stringa separata da virgole che specifica i tipi di FabricEvents che devono essere inclusi solo nella risposta. |
| --exclude-analysis-events | Questo parametro disabilita il recupero di AnalysisEvents se viene passato true. |
| --skip-correlation-lookup | Questo parametro disabilita la ricerca di informazioni CorrelatedEvents se viene passato true. in caso contrario, il CorrelationEvents viene elaborato e il campo HasCorrelatedEvents in ogni FabricEvent viene popolato. |
| --timeout -t | Timeout del server per l'esecuzione dell'operazione in secondi. Questo timeout specifica il periodo di tempo durante il quale il client è disposto ad attendere il completamento dell'operazione richiesta. Il valore predefinito per questo parametro è 60 secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio della registrazione per mostrare tutti i log di debug. |
| --help -h | Visualizza questo messaggio della guida ed esce. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio della registrazione. Usare --debug per log di debug completi. |

