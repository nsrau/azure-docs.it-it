---
title: Interfaccia della riga di comando Azure Service Fabric - sfctl partition | Documentazione Microsoft
description: Descrive i comandi dell'interfaccia della riga di comando Service Fabric sfctl partition.
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: cli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 09/22/2017
ms.author: ryanwi
ms.openlocfilehash: a5d0ff59803212403281063f47e706433cee64b4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="sfctl-partition"></a>sfctl partition
Consente di eseguire una query e gestire partizioni per qualsiasi servizio.

## <a name="commands"></a>Comandi:

|Comando|Descrizione|
| --- | --- |
|    data-loss      | Questa API provoca la perdita di dati per la partizione specificata.|
|    data-loss-status  | Mostra lo stato di avanzamento di un'operazione di perdita di dati in una partizione avviata usando l'API StartDataLoss.|
|    health         | Mostra l'integrità della partizione di Service Fabric specificata.|
|    info           | Mostra le informazioni su una partizione di Service Fabric.|
|    list           | Mostra l'elenco delle partizioni di un servizio di Service Fabric.|
|    load           | Mostra il carico della partizione di Service Fabric specificata.|
|    load-reset     | Reimposta il carico corrente di una partizione di Service Fabric.|
|    quorum-loss    | Provoca la perdita di quorum per una partizione di servizio con stato specificato.|
|    quorum-loss-status| Mostra lo stato di avanzamento di un'operazione di perdita di quorum in una partizione avviata usando l'API StartQuorumLoss.|
|    recover        | Indica al cluster di Service Fabric che deve tentare di ripristinare una partizione specifica, che è attualmente bloccata in una perdita di quorum.|
|    recover-all    | Indica al cluster di Service Fabric che deve tentare di ripristinare qualsiasi servizio (inclusi i servizi di sistema) che è attualmente bloccato in una perdita di quorum.|
|    report-health  | Invia un report di integrità sulla partizione di Service Fabric.|
|    restart        | Questa API riavvia alcune o tutte le repliche o le istanze della partizione specificata.|
|    restart-status | Mostra lo stato di avanzamento di un'operazione PartitionRestart avviata utilizzando StartPartitionRestart.|
|    svc-name       | Mostra il nome del servizio Service Fabric per una partizione.|


## <a name="sfctl-partition-health"></a>sfctl partition health
Mostra l'integrità della partizione di Service Fabric specificata.

Mostra le informazioni di integrità della partizione specificata. Utilizzare EventsHealthStateFilter per filtrare la raccolta di eventi di stato riportati nel servizio in base allo stato di integrità.
Utilizzare ReplicasHealthStateFilter per filtrare la raccolta di oggetti ReplicaHealthState nella partizione. Se si specifica una partizione che non esiste nell'archivio integrità, questo cmdlet restituisce un errore. .

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --partition-id      [Obbligatorio]| L'identità della partizione.|
| --events-health-state-filter  | Consente di filtrare la raccolta di oggetti HealthEvent restituiti in base allo stato di integrità. I valori possibili per questo parametro includono il valore intero di uno dei seguenti stati di integrità.                Vengono restituiti solo gli eventi che corrispondono al filtro. Tutti gli eventi vengono utilizzati per valutare lo stato di integrità aggregato. Se non specificato diversamente, vengono restituite tutte le voci. I valori dello stato sono enumerati in base al flag, pertanto il valore potrebbe essere una combinazione di questi valori ottenuti utilizzando l'operatore "OR" bit per bit. Ad esempio, se il valore fornito è 6, vengono restituiti tutti gli eventi con valore dello stato di integrità OK (2) e di Avviso (4). - Default - Valore predefinito. Consente di ricercare qualsiasi stato di integrità. Il valore predefinito è zero. - None - Il filtro non corrisponde ad alcun valore di stato di integrità. Utilizzato per non restituire alcun risultato in un determinato insieme di stati. Il valore è uguale a 1. -Ok - Filtro che ricerca le corrispondenze di input con valore di stato di integrità Ok. Il valore è 2. -Warning - filtro che ricerca le corrispondenze di input con valore di stato di integrità Avviso. Il valore è 4. - Error - Filtro che ricerca le corrispondenze di input con valore di stato di integrità Errore. Il valore è 8.                -All - Filtro che ricerca le corrispondenze di input con qualsiasi valore di stato di integrità.                Il valore è 65535.|
|--exclude-health-statistics   | Indica se le statistiche di integrità devono essere restituite come parte del risultato della query. False per impostazione predefinita. Le statistiche mostrano il numero di elementi figlio in stato di integrità Ok, avviso ed errore.|
| --replicas-health-state-filter| Permette di filtrare la raccolta di oggetti ReplicaHealthState nella partizione. Il valore può essere ottenuto dai membri o da operazioni bit per bit sui membri di HealthStateFilter. Vengono restituite solo repliche che corrispondono al filtro. Tutte le repliche vengono utilizzate per valutare lo stato di integrità aggregato. Se non specificato diversamente, vengono restituite tutte le voci. I valori dello stato sono enumerati in base al flag, pertanto il valore potrebbe essere una combinazione di questi valori ottenuti utilizzando l'operatore "OR" bit per bit. Ad esempio, se il valore fornito è 6, vengono restituiti tutti gli eventi con valore dello stato di integrità OK (2) e di Avviso (4). I valori possibili per questo parametro includono il valore intero di uno dei seguenti stati di integrità. - Default - Valore predefinito. Consente di ricercare qualsiasi stato di integrità. Il valore predefinito è zero. - None - Il filtro non corrisponde ad alcun valore di stato di integrità. Utilizzato per non restituire alcun risultato in un determinato insieme di stati. Il valore è uguale a 1. -Ok - Filtro che ricerca le corrispondenze di input con valore di stato di integrità Ok. Il valore è 2. -Warning - filtro che ricerca le corrispondenze di input con valore di stato di integrità Avviso. Il valore è 4. - Error - Filtro che ricerca le corrispondenze di input con valore di stato di integrità Errore. Il valore è 8. -All - Filtro che ricerca le corrispondenze di input con qualsiasi valore di stato di integrità. Il valore è 65535.|
| --timeout -t               | Timeout del server in secondi.  Predefinito: 60.|

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug                    | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug.|
| --help -h                  | Mostra questo messaggio della Guida e l'uscita.|
| --output -o                | Formato di output.  Valori consentiti: json, jsonc, tabella, tsv.                Predefinito: json.|
| --query                    | Stringa di query JMESPath. Per maggiori informazioni ed esempi, vedere http://jmespath.org/. |
| --verbose                  | Aumenta il livello di dettaglio di registrazione. Utilizzare --debug per i log di debug completi.|

## <a name="sfctl-partition-info"></a>sfctl partition info
Mostra le informazioni su una partizione di Service Fabric.

L'endpoint delle partizioni restituisce informazioni sulla partizione specificata. La risposta include l'ID di partizione, le informazioni dello schema di partizionamento, le chiavi supportate dalla partizione, lo stato, l’integrità e altri dettagli sulla partizione.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --partition-id [Obbligatorio]| L'identità della partizione.|
| --timeout -t          | Timeout del server in secondi.  Predefinito: 60.|

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug               | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug.|
| --help -h             | Mostra questo messaggio della Guida e l'uscita.|
| --output -o           | Formato di output.  Valori consentiti: json, jsonc, tabella, tsv.  Predefinito: json.|
| --query               | Stringa di query JMESPath. Per maggiori informazioni ed esempi, vedere http://jmespath.org/.|
| --verbose             | Aumenta il livello di dettaglio di registrazione. Utilizzare --debug per i log di debug completi.|

## <a name="sfctl-partition-list"></a>sfctl partition list
Mostra l'elenco delle partizioni di un servizio di Service Fabric.

Mostra l'elenco delle partizioni di un servizio di Service Fabric. Include l'ID di partizione, le informazioni dello schema di partizionamento, le chiavi supportate dalla partizione, lo stato, l’integrità e altri dettagli sulla partizione.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --service-id [obbligatorio]| L'identità del servizio. Si tratta in genere del nome completo del servizio senza lo schema URI 'fabric:'. A partire dalla versione 6.0, i nomi gerarchici sono delimitati con il carattere "~". Ad esempio, se il nome del servizio è "fabric://myapp/app1/svc1", l'identità del servizio sarà "myapp~app1~svc1" in 6.0+ e "myapp/app1/svc1" nelle versioni precedenti.|
| --continuation-token| Il parametro del token di continuazione viene utilizzato per ottenere il set di risultati successivo.         Un token di continuazione con un valore non vuoto è incluso nella risposta dell'API quando i risultati dal sistema non rientrano in una singola risposta. Quando questo valore viene passato alla successiva chiamata API, l'API restituisce il set di risultati successivo. Se non sono presenti altri risultati, il token di continuazione non contiene alcun valore. Il valore di questo parametro non deve essere codificato in URL.|
| --timeout -t        | Timeout del server in secondi.  Predefinito: 60.|

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug             | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug.|
| --help -h           | Mostra questo messaggio della Guida e l'uscita.|
| --output -o         | Formato di output.  Valori consentiti: json, jsonc, tabella, tsv.  Predefinito: json.|
| --query             | Stringa di query JMESPath. Per maggiori informazioni ed esempi, vedere http://jmespath.org/.|
| --verbose           | Aumenta il livello di dettaglio di registrazione. Utilizzare --debug per i log di debug completi.|

## <a name="sfctl-partition-load"></a>sfctl partition load
Mostra il carico della partizione di Service Fabric specificata.

Restituisce informazioni sulla partizione specificata. La risposta include un elenco di informazioni sul caricamento. Tutte le informazioni includono il nome della metrica carico, il valore e l’ultimo time riportato in UTC. .

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --partition-id [Obbligatorio]| L'identità della partizione.|
| --timeout -t          | Timeout del server in secondi.  Predefinito: 60.|

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug               | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug.|
| --help -h             | Mostra questo messaggio della Guida e l'uscita.|
| --output -o           | Formato di output.  Valori consentiti: json, jsonc, tabella, tsv.  Predefinito: json.|
| --query               | Stringa di query JMESPath. Per maggiori informazioni ed esempi, vedere http://jmespath.org/.|
| --verbose             | Aumenta il livello di dettaglio di registrazione. Utilizzare --debug per i log di debug completi.|

## <a name="sfctl-partition-recover"></a>sfctl partition recover
Indica al cluster di Service Fabric che deve tentare di ripristinare una partizione specifica, che è attualmente bloccata in una perdita di quorum.

Indica al cluster di Service Fabric che deve tentare di ripristinare una partizione specifica, che è attualmente bloccata in una perdita di quorum. Questa operazione può essere eseguita solo se è noto che non è possibile recuperare le repliche che non sono attive. Un uso non corretto di questa API può causare una perdita di dati.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --partition-id [Obbligatorio]| L'identità della partizione.|
| --timeout -t          | Timeout del server in secondi.  Predefinito: 60.|

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug               | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug.|
| --help -h             | Mostra questo messaggio della Guida e l'uscita.|
| --output -o           | Formato di output.  Valori consentiti: json, jsonc, tabella, tsv.  Predefinito: json.|
| --query               | Stringa di query JMESPath. Per maggiori informazioni ed esempi, vedere http://jmespath.org/.|
| --verbose             | Aumenta il livello di dettaglio di registrazione. Utilizzare --debug per i log di debug completi.|

## <a name="sfctl-partition-restart"></a>sfctl partition restart
Questa API riavvia alcune o tutte le repliche o le istanze della partizione specificata.

Questa API è utile per il test del failover. Se utilizzato come destinazione di una partizione del servizio senza stato, RestartPartitionMode deve essere AllReplicasOrInstances. Richiamare l'API GetPartitionRestartProgress utilizzando la stessa OperationId per ottenere lo stato di avanzamento. .

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --operation-id             [Obbligatorio]| Una GUID che identifica una chiamata dell'API.  Questo viene passato all'API GetProgress corrispondente.|
| --partition-id [Obbligatorio]| L'identità della partizione.|
| --restart-partition-mode [Obbligatorio]| - Invalid - Reserved.  Non vengono passate all'API. -AllReplicasOrInstances - Tutte le repliche o istanze nella partizione vengono riavviate contemporaneamente. - OnlyActiveSecondaries -                     Solo le repliche secondarie vengono riattivate. .|
| --service-id             [Obbligatorio]| L'identità del servizio. Si tratta in genere del nome completo del servizio senza lo schema URI 'fabric:'. A partire dalla versione 6.0, i nomi gerarchici sono delimitati con il carattere "~". Ad esempio, se il nome del servizio è "fabric://myapp/app1/svc1", l'identità del servizio sarà "myapp~app1~svc1" in 6.0+ e "myapp/app1/svc1" nelle versioni precedenti.|
| --timeout -t                    | Timeout del server in secondi.  Predefinito: 60.|

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug                         | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug.|
| --help -h                       | Mostra questo messaggio della Guida e l'uscita.|
| --output -o                     | Formato di output.  Valori consentiti: json, jsonc, tabella, tsv.                     Predefinito: json.|
| --query                         | Stringa di query JMESPath. Per maggiori informazioni ed esempi, vedere http://jmespath.org/.|
| --verbose                       | Aumenta il livello di dettaglio di registrazione. Utilizzare --debug per i log di debug completi.|

## <a name="next-steps"></a>Passaggi successivi
- [Configurare](service-fabric-cli.md) l'interfaccia della riga di comando di Service Fabric.
- Informazioni su come usare l'interfaccia della riga di comando Service Fabric usando gli [script di esempio](/azure/service-fabric/scripts/sfctl-upgrade-application).