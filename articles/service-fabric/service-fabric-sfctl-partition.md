---
title: Interfaccia della riga di comando Azure Service Fabric - sfctl partition | Documentazione Microsoft
description: Descrive i comandi dell'interfaccia della riga di comando Service Fabric sfctl partition.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: f7c9bcc51757100cb1fc957dee12213bc8bf2eec
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58666767"
---
# <a name="sfctl-partition"></a>sfctl partition
Consente di eseguire una query e gestire partizioni per qualsiasi servizio.

## <a name="commands"></a>Comandi:

|Comando|DESCRIZIONE|
| --- | --- |
| data-loss | Questa API determinerà la perdita di dati per la partizione specificata. |
| data-loss-status | Indica lo stato di avanzamento di un'operazione di perdita di dati avviata usando l'API StartDataLoss. |
| health | Mostra l'integrità della partizione di Service Fabric specificata. |
| info | Mostra le informazioni su una partizione di Service Fabric. |
| list | Mostra l'elenco delle partizioni di un servizio di Service Fabric. |
| load | Ottiene le informazioni sul carico della partizione di Service Fabric specificata. |
| load-reset | Reimposta il carico corrente di una partizione di Service Fabric. |
| quorum-loss | Provoca la perdita di quorum per una partizione di servizio con stato specificato. |
| quorum-loss-status | Indica lo stato di avanzamento di un'operazione di perdita di quorum in una partizione avviata usando l'API StartQuorumLoss. |
| recover | Indica al cluster di Service Fabric che deve tentare di ripristinare una partizione specifica, che è attualmente bloccata in una perdita di quorum. |
| recover-all | Indica al cluster di Service Fabric che deve tentare di ripristinare tutti i servizio (inclusi i servizi di sistema) che sono attualmente bloccati in una perdita di quorum. |
| report-health | Invia un report di integrità sulla partizione di Service Fabric. |
| restart | Questa API riavvierà alcune o tutte le repliche o le istanze della partizione specificata. |
| restart-status | Indica lo stato di avanzamento di un'operazione PartitionRestart avviata usando StartPartitionRestart. |
| svc-name | Mostra il nome del servizio Service Fabric per una partizione. |

## <a name="sfctl-partition-data-loss"></a>sfctl partition data-loss
Questa API determinerà la perdita di dati per la partizione specificata.

Avvierà una chiamata all'API OnDataLossAsync della partizione.  Questa API determinerà la perdita di dati per la partizione specificata. Avvierà una chiamata all'API OnDataLoss della partizione. La perdita di dati effettiva dipenderà dalla modalità DataLossMode specificata.  <br> - PartialDataLoss: viene rimosso solo un quorum di repliche e viene attivata un'API OnDataLoss per la partizione, ma la perdita di dati effettiva dipende dalla presenza di replica in elaborazione.  <br> - FullDataLoss: vengono rimosse tutte le repliche quindi si verifica la perdita di tutti i dati e l'API OnDataLoss viene attivata. Questa API deve essere chiamata solo con un servizio con stato come destinazione. Non è consigliabile chiamare questa API con un servizio di sistema come destinazione.

> [!NOTE] 
> Dopo aver chiamato questa API, la chiamata non può essere annullata. La chiamata di CancelOperation arresterà solo l'esecuzione e pulirà lo stato di sistema interno. Non ripristinerà i dati se il comando è stato eseguito abbastanza a lungo da causare la perdita di dati. Chiamare l'API GetDataLossProgress con lo stesso OperationId per restituire le informazioni sull'operazione avviata con questa API.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --data-loss-mode [obbligatorio] | Questa enumerazione viene passata all'API StartDataLoss per indicare il tipo di perdita di dati da eseguire. |
| --operation-id   [obbligatorio] | Una GUID che identifica una chiamata dell'API.  Viene passato all'API GetProgress corrispondente. |
| --partition-id   [obbligatorio] | L'identità della partizione. |
| --service-id             [obbligatorio] | L'identità del servizio. L'ID corrisponde in genere al nome completo del servizio senza lo schema URI "fabric\:". A partire dalla versione 6.0, i nomi gerarchici sono delimitati dal carattere "\~". Ad esempio, se il nome del servizio è "fabric\:/myapp/app1/svc1", l'identità del servizio sarà "myapp\~app1\~svc1" nella versione 6.0 e successive e "myapp/app1/svc1" nelle versioni precedenti. |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-partition-data-loss-status"></a>sfctl partition data-loss-status
Indica lo stato di avanzamento di un'operazione di perdita di dati avviata usando l'API StartDataLoss.

Indica lo stato di avanzamento di un'operazione di perdita di dati avviata con StartDataLoss, usando il valore OperationId.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --operation-id [obbligatorio] | Una GUID che identifica una chiamata dell'API.  Viene passato all'API GetProgress corrispondente. |
| --partition-id [Obbligatorio] | L'identità della partizione. |
| --service-id   [obbligatorio] | L'identità del servizio. L'ID corrisponde in genere al nome completo del servizio senza lo schema URI "fabric\:". A partire dalla versione 6.0, i nomi gerarchici sono delimitati dal carattere "\~". Ad esempio, se il nome del servizio è "fabric\:/myapp/app1/svc1", l'identità del servizio sarà "myapp\~app1\~svc1" nella versione 6.0 e successive e "myapp/app1/svc1" nelle versioni precedenti. |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-partition-health"></a>sfctl partition health
Mostra l'integrità della partizione di Service Fabric specificata.

Usare EventsHealthStateFilter per filtrare la raccolta di eventi di stato riportati nel servizio in base allo stato di integrità. Utilizzare ReplicasHealthStateFilter per filtrare la raccolta di oggetti ReplicaHealthState nella partizione. Se si specifica una partizione che non esiste nell'archivio integrità, questa richiesta restituisce un errore.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --partition-id      [Obbligatorio] | L'identità della partizione. |
| --events-health-state-filter | Consente di filtrare la raccolta di oggetti HealthEvent restituiti in base allo stato di integrità. I valori possibili per questo parametro includono il valore intero di uno dei seguenti stati di integrità. Vengono restituiti solo gli eventi che corrispondono al filtro. Tutti gli eventi vengono usati per valutare lo stato di integrità aggregato. Se non specificato diversamente, vengono restituite tutte le voci. I valori dello stato sono enumerati in base al flag, pertanto il valore potrebbe essere una combinazione di questi valori ottenuti usando l'operatore "OR" bit per bit. Ad esempio, se il valore fornito è 6, vengono restituiti tutti gli eventi con valore dello stato di integrità OK (2) e di Avviso (4).  <br> - Default - Valore predefinito. Consente di ricercare qualsiasi stato di integrità. Il valore predefinito è zero.  <br> - None - Il filtro non corrisponde ad alcun valore di stato di integrità. Usato per non restituire alcun risultato in un determinato insieme di stati. Il valore è uguale a 1.  <br> -Ok - Filtro che ricerca le corrispondenze di input con valore di stato di integrità Ok. Il valore è 2.  <br> -Warning - filtro che ricerca le corrispondenze di input con valore di stato di integrità Avviso. Il valore è 4.  <br> - Error - Filtro che ricerca le corrispondenze di input con valore di stato di integrità Errore. Il valore è 8.  <br> -All - Filtro che ricerca le corrispondenze di input con qualsiasi valore di stato di integrità. Il valore è 65535. |
| --exclude-health-statistics | Indica se le statistiche di integrità devono essere restituite come parte del risultato della query. False per impostazione predefinita. Le statistiche indicano il numero di entità figlio il cui stato di integrità è Ok, Avviso ed Errore. |
| --replicas-health-state-filter | Consente di filtrare la raccolta di oggetti ReplicaHealthState nella partizione. Il valore può essere ottenuto da membri o da operazioni bit per bit sui membri di HealthStateFilter. Verranno restituite solo repliche che corrispondono al filtro. Verranno usate tutte le repliche per valutare lo stato di integrità aggregato. Se non specificato diversamente, verranno restituite tutte le voci. I valori dello stato sono enumerati in base al flag, pertanto il valore potrebbe essere una combinazione di questi valori ottenuti usando l'operatore "OR" bit per bit. Ad esempio, se il valore specificato è 6, verranno restituiti tutti gli eventi con valore dello stato di integrità OK (2) e di Avviso (4). I valori possibili per questo parametro includono il valore intero di uno dei seguenti stati di integrità.  <br> - Default - Valore predefinito. Consente di ricercare qualsiasi stato di integrità. Il valore predefinito è zero.  <br> - None - Il filtro non corrisponde ad alcun valore di stato di integrità. Usato per non restituire alcun risultato in un determinato insieme di stati. Il valore è uguale a 1.  <br> -Ok - Filtro che ricerca le corrispondenze di input con valore di stato di integrità Ok. Il valore è 2.  <br> -Warning - filtro che ricerca le corrispondenze di input con valore di stato di integrità Avviso. Il valore è 4.  <br> - Error - Filtro che ricerca le corrispondenze di input con valore di stato di integrità Errore. Il valore è 8.  <br> -All - Filtro che ricerca le corrispondenze di input con qualsiasi valore di stato di integrità. Il valore è 65535. |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-partition-info"></a>sfctl partition info
Mostra le informazioni su una partizione di Service Fabric.

Ottiene le informazioni sulla partizione specificata. La risposta include l'ID di partizione, le informazioni dello schema di partizionamento, le chiavi supportate dalla partizione, lo stato, l’integrità e altri dettagli sulla partizione.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --partition-id [Obbligatorio] | L'identità della partizione. |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-partition-list"></a>sfctl partition list
Mostra l'elenco delle partizioni di un servizio di Service Fabric.

La risposta include l'ID di partizione, le informazioni dello schema di partizionamento, le chiavi supportate dalla partizione, lo stato, l’integrità e altri dettagli sulla partizione.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --service-id [obbligatorio] | L'identità del servizio. L'ID corrisponde in genere al nome completo del servizio senza lo schema URI "fabric\:". A partire dalla versione 6.0, i nomi gerarchici sono delimitati dal carattere "\~". Ad esempio, se il nome del servizio è "fabric\:/myapp/app1/svc1", l'identità del servizio sarà "myapp\~app1\~svc1" nella versione 6.0 e successive e "myapp/app1/svc1" nelle versioni precedenti. |
| --continuation-token | Il parametro del token di continuazione viene utilizzato per ottenere il set di risultati successivo. Un token di continuazione con un valore non vuoto è incluso nella risposta dell'API quando i risultati del sistema non rientrano in una singola risposta. Quando questo valore viene passato alla successiva chiamata API, l'API restituisce il set di risultati successivo. Se non sono presenti altri risultati, il token di continuazione non contiene alcun valore. Il valore di questo parametro non deve essere codificato in URL. |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-partition-load"></a>sfctl partition load
Ottiene le informazioni sul carico della partizione di Service Fabric specificata.

Restituisce informazioni sul carico di una partizione specificata. La risposta include un elenco di report di carico per una partizione di Service Fabric. Ogni report include il nome e il valore della metrica del carico e l’ultimo orario di segnalazione riportato in UTC.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --partition-id [Obbligatorio] | L'identità della partizione. |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-partition-load-reset"></a>sfctl partition load-reset
Reimposta il carico corrente di una partizione di Service Fabric.

Reimposta il carico corrente di una partizione di Service Fabric sul carico predefinito per il servizio.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --partition-id [Obbligatorio] | L'identità della partizione. |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-partition-quorum-loss"></a>sfctl partition quorum-loss
Provoca la perdita di quorum per una partizione di servizio con stato specificato.

Questa API è utile per un caso di perdita di quorum temporaneo sul servizio. Chiamare l'API GetQuorumLossProgress con lo stesso OperationId per restituire le informazioni sull'operazione avviata con questa API. È possibile eseguire la chiamata solo in servizi permanenti con stato (HasPersistedState==true).  Non usare questa API in servizi senza stato o in servizi con stato solo in memoria.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --operation-id         [obbligatorio] | Una GUID che identifica una chiamata dell'API.  Viene passato all'API GetProgress corrispondente. |
| --partition-id         [obbligatorio] | L'identità della partizione. |
| --quorum-loss-duration [obbligatorio] | Periodo di tempo in cui la partizione rimarrà in perdita di quorum.  La durata deve essere specificata in secondi. |
| --quorum-loss-mode     [obbligatorio] | Questa enumerazione viene passata all'API StartQuorumLoss per indicare il tipo di perdita di dati da eseguire. |
| --service-id           [obbligatorio] | L'identità del servizio. L'ID corrisponde in genere al nome completo del servizio senza lo schema URI "fabric\:". A partire dalla versione 6.0, i nomi gerarchici sono delimitati dal carattere "\~". Ad esempio, se il nome del servizio è "fabric\:/myapp/app1/svc1", l'identità del servizio sarà "myapp\~app1\~svc1" nella versione 6.0 e successive e "myapp/app1/svc1" nelle versioni precedenti. |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-partition-quorum-loss-status"></a>sfctl partition quorum-loss-status
Indica lo stato di avanzamento di un'operazione di perdita di quorum in una partizione avviata usando l'API StartQuorumLoss.

Indica lo stato di avanzamento di un'operazione di perdita di quorum avviata con StartQuorumLoss, usando l'OperationId specificato.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --operation-id [obbligatorio] | Una GUID che identifica una chiamata dell'API.  Viene passato all'API GetProgress corrispondente. |
| --partition-id [Obbligatorio] | L'identità della partizione. |
| --service-id   [obbligatorio] | L'identità del servizio. L'ID corrisponde in genere al nome completo del servizio senza lo schema URI "fabric\:". A partire dalla versione 6.0, i nomi gerarchici sono delimitati dal carattere "\~". Ad esempio, se il nome del servizio è "fabric\:/myapp/app1/svc1", l'identità del servizio sarà "myapp\~app1\~svc1" nella versione 6.0 e successive e "myapp/app1/svc1" nelle versioni precedenti. |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-partition-recover"></a>sfctl partition recover
Indica al cluster di Service Fabric che deve tentare di ripristinare una partizione specifica, che è attualmente bloccata in una perdita di quorum.

Questa operazione può essere eseguita solo se è noto che non è possibile recuperare le repliche che non sono attive. Un uso non corretto di questa API può causare una perdita di dati.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --partition-id [Obbligatorio] | L'identità della partizione. |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-partition-recover-all"></a>sfctl partition recover-all
Indica al cluster di Service Fabric che deve tentare di ripristinare tutti i servizio (inclusi i servizi di sistema) che sono attualmente bloccati in una perdita di quorum.

Questa operazione può essere eseguita solo se è noto che non è possibile recuperare le repliche che non sono attive. Un uso non corretto di questa API può causare una perdita di dati.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-partition-report-health"></a>sfctl partition report-health
Invia un report di integrità sulla partizione di Service Fabric.

Segnala lo stato di integrità della partizione di Service Fabric specificata. Il report deve contenere le informazioni relative all'origine del report sull'integrità e alla proprietà in cui viene segnalato. Il report viene inviato a una partizione del gateway di Service Fabric, che lo inoltra all'archivio integrità. Il report può essere accettato dal gateway, ma rifiutato dall'archivio integrità dopo la convalida aggiuntiva. L'archivio integrità, ad esempio, può rifiutare il report a causa di un parametro non valido, come un numero di sequenza non aggiornato. Per determinare se il report è stato applicato nell'archivio integrità, controllare che il report venga visualizzato nella sezione degli eventi.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --health-property [obbligatorio] | Proprietà delle informazioni sull'integrità. <br><br> Un'entità può avere report sull'integrità per proprietà diverse. La proprietà è una stringa e non un'enumerazione fissa, che assicura al reporter la flessibilità necessaria per definire la categoria della condizione di stato che attiva il report. Ad esempio, un reporter con SourceId "LocalWatchdog" può monitorare lo stato del disco disponibile in un nodo e può quindi segnalare la proprietà "AvailableDisk" in tale nodo. Lo stesso reporter può monitorare la connettività del nodo e può quindi segnalare una proprietà "Connectivity" nello stesso nodo. Entrambi questi report vengono considerati come eventi di integrità separati nell'archivio integrità per il nodo specificato. Insieme a SourceId, la proprietà identifica in modo univoco le informazioni sull'integrità. |
| --health-state    [obbligatorio] | I valori possibili sono\: "Invalid", "Ok", "Warning", "Error", "Unknown". |
| --partition-id [Obbligatorio] | L'identità della partizione. |
| --source-id       [obbligatorio] | Nome di origine che identifica il componente client/watchdog/sistema che ha generato le informazioni sull'integrità. |
| --description | Descrizione delle informazioni sull'integrità. <br><br> Rappresenta il testo libero usato per aggiungere informazioni leggibili sul report. La lunghezza massima della stringa per la descrizione è pari a 4096 caratteri. Se la stringa immessa è più lunga, verrà automaticamente troncata. Quando viene troncata, gli ultimi caratteri della descrizione contengono un marcatore "[Truncated]" e la dimensione totale della stringa è pari a 4096 caratteri. La presenza del marcatore indica agli utenti che si è verificato un troncamento. Si noti che quando viene troncata, la descrizione contiene meno di 4096 caratteri della stringa originale. |
| --immediate | Flag che indica se il report deve essere inviato immediatamente. <br><br> Un report sull'integrità viene inviato a un'applicazione del gateway di Service Fabric, che lo inoltra all'archivio integrità. Se Immediate è impostato su True, il report viene inviato immediatamente dal gateway HTTP all'archivio integrità, indipendentemente dalle impostazioni del client Fabric usate dall'applicazione gateway HTTP. Questo è utile per i report critici che devono essere inviati appena possibile. A seconda della tempistica e di altre condizioni, l'invio del report può tuttavia avere esito negativo, ad esempio se il gateway HTTP è chiuso o il messaggio non raggiunge il gateway. Se Immediate è impostato su False, il report viene inviato in base alle impostazioni del client di integrità dal gateway HTTP. Verrà quindi inviato in batch in base alla configurazione di HealthReportSendInterval. Questa è l'impostazione consigliata perché consente al client di integrità di ottimizzare la segnalazione di messaggi sull'integrità all'archivio integrità e l'elaborazione di report sull'integrità. Per impostazione predefinita, i report non vengono inviati immediatamente. |
| --remove-when-expired | Valore che indica se il report viene rimosso dall'archivio integrità quando scade. <br><br> Se impostato su True, il report viene rimosso dall'archivio integrità dopo la scadenza. Se impostato su False, il report viene considerato come errore quando è scaduto. Il valore di questa proprietà è False per impostazione predefinita. Quando i client creano report periodicamente, RemoveWhenExpired sarà impostato su False (valore predefinito). In questo modo, se il reporter presenta problemi (ad esempio, un deadlock) e non può creare report, l'entità viene considerata in stato di errore quando scade il report sull'integrità. L'entità viene quindi contrassegnata con lo stato di integrità di errore. |
| --sequence-number | Numero di sequenza per questo report sull'integrità come stringa numerica. <br><br> Il numero di sequenza del report viene usato dall'archivio integrità per rilevare i report non aggiornati. Se non specificato, un numero di sequenza viene generato automaticamente dal client di integrità quando viene aggiunto un report. |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |
| --ttl | Durata in cui questo report sull'integrità è valido. Questo campo usa il formato ISO8601 per specificare la durata. <br><br> Quando i client creano report periodicamente, devono inviare i report con una frequenza maggiore della durata (TTL). Se i client inviano report in caso di transizione, possono impostare la durata (TTL) come infinita. Quando la durata (TTL) scade, l'evento di integrità che contiene le informazioni sull'integrità viene rimosso dall'archivio integrità, se RemoveWhenExpired è True, o considerato in stato di errore, se RemoveWhenExpired è False. Se non è specificato, la durata (TTL) viene impostata su un valore infinito. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-partition-restart"></a>sfctl partition restart
Questa API riavvierà alcune o tutte le repliche o le istanze della partizione specificata.

Questa API è utile per il test del failover. Se utilizzato come destinazione di una partizione del servizio senza stato, RestartPartitionMode deve essere AllReplicasOrInstances. Richiamare l'API GetPartitionRestartProgress utilizzando la stessa OperationId per ottenere lo stato di avanzamento.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --operation-id             [Obbligatorio] | Una GUID che identifica una chiamata dell'API.  Viene passato all'API GetProgress corrispondente. |
| --partition-id [Obbligatorio] | L'identità della partizione. |
| --restart-partition-mode [Obbligatorio] | Specifica quali partizioni riavviare. |
| --service-id             [Obbligatorio] | L'identità del servizio. L'ID corrisponde in genere al nome completo del servizio senza lo schema URI "fabric\:". A partire dalla versione 6.0, i nomi gerarchici sono delimitati dal carattere "\~". Ad esempio, se il nome del servizio è "fabric\:/myapp/app1/svc1", l'identità del servizio sarà "myapp\~app1\~svc1" nella versione 6.0 e successive e "myapp/app1/svc1" nelle versioni precedenti. |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-partition-restart-status"></a>sfctl partition restart-status
Indica lo stato di avanzamento di un'operazione PartitionRestart avviata usando StartPartitionRestart.

Indica lo stato di avanzamento di un'operazione PartitionRestart avviata con StartPartitionRestart, usando l'OperationId specificato.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --operation-id [obbligatorio] | Una GUID che identifica una chiamata dell'API.  Viene passato all'API GetProgress corrispondente. |
| --partition-id [Obbligatorio] | L'identità della partizione. |
| --service-id   [obbligatorio] | L'identità del servizio. L'ID corrisponde in genere al nome completo del servizio senza lo schema URI "fabric\:". A partire dalla versione 6.0, i nomi gerarchici sono delimitati dal carattere "\~". Ad esempio, se il nome del servizio è "fabric\:/myapp/app1/svc1", l'identità del servizio sarà "myapp\~app1\~svc1" nella versione 6.0 e successive e "myapp/app1/svc1" nelle versioni precedenti. |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-partition-svc-name"></a>sfctl partition svc-name
Mostra il nome del servizio Service Fabric per una partizione.

Ottiene il nome del servizio per la partizione specificata. Se l'ID di partizione non esiste nel cluster, viene restituito un errore 404.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --partition-id [Obbligatorio] | L'identità della partizione. |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |


## <a name="next-steps"></a>Passaggi successivi
- [Configurare](service-fabric-cli.md) l'interfaccia della riga di comando di Service Fabric.
- Informazioni su come usare l'interfaccia della riga di comando Service Fabric usando gli [script di esempio](/azure/service-fabric/scripts/sfctl-upgrade-application).
