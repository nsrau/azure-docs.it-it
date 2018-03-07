---
title: Interfaccia della riga di comando Azure Service Fabric - sfctl node | Documentazione Microsoft
description: Descrive i comandi dell'interfaccia della riga di comando Service Fabric sfctl node.
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 02/22/2018
ms.author: ryanwi
ms.openlocfilehash: 50c7fe38d8bf7b14adf437f85c758e465e7d231d
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="sfctl-node"></a>sfctl node
Consente di gestire i nodi che formano un cluster.

## <a name="commands"></a>Comandi:

|Comando|DESCRIZIONE|
| --- | --- |
|    disable       | Disattiva un nodo del cluster di Service Fabric con lo scopo di disattivazione specificato.|
|    enable        | Attiva un nodo del cluster di Service Fabric che è attualmente disattivato.|
|    health        | Mostra l'integrità di un nodo di Service Fabric.|
|    info          | Mostra le informazioni su un nodo specifico del cluster di Service Fabric.|
|    list          | Mostra l'elenco dei nodi del cluster di Service Fabric.|
|    load          | Mostra le informazioni sul caricamento di un nodo di Service Fabric.|
|    remove-state  | Notifica a Service Fabric che lo stato persistente in un nodo è stato rimosso o perso definitivamente.|
|    report-health | Invia un report di integrità nel nodo di Service Fabric.|
|    restart       | Riavvia un nodo del cluster di Service Fabric.|
|    transition    | Avvia o arresta un nodo del cluster.|
|    transition-status| Mostra lo stato di avanzamento di un'operazione avviata utilizzando StartNodeTransition.|


## <a name="sfctl-node-disable"></a>sfctl node disable
Disattiva un nodo del cluster di Service Fabric con lo scopo di disattivazione specificato.

Disattiva un nodo del cluster di Service Fabric con lo scopo di disattivazione specificato. Quando la disattivazione è in corso, lo scopo di disattivazione può essere aumentato, ma non ridotto (ad esempio, un nodo che viene disattivato con lo scopo di arresto può essere disattivato ulteriormente con Riavvio, ma non viceversa). I nodi possono essere riattivati usando l'operazione Attiva un nodi in qualsiasi momento dopo la disattivazione. Se la disattivazione non è completa, la disattivazione viene annullata. Un nodo che si arresta e torna mentre è disattivato deve essere riattivato prima che possano essere inseriti servizi in tale nodo.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --node-name [Required]| Il nome del nodo.|
| --deactivation-intent | Viene descritto lo scopo o il motivo per la disattivazione del nodo. |
| --timeout -t       | Timeout del server in secondi.  Predefinito: 60.|

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug            | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug.|
| --help -h          | Mostra questo messaggio della Guida e l'uscita.|
| --output -o        | Formato di output.  Valori consentiti: json, jsonc, tabella, tsv.  Predefinito: json.|
| --query            | Stringa di query JMESPath. Per maggiori informazioni ed esempi, vedere http://jmespath.org/.|
| --verbose          | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi.|

## <a name="sfctl-node-enable"></a>sfctl node enable
Attiva un nodo del cluster di Service Fabric che è attualmente disattivato.

Attiva un nodo del cluster di Service Fabric che è attualmente disattivato. Una volta attivato, il nodo diventa nuovamente una destinazione valida dove posizionare le nuove repliche, e tutte le repliche disattivate rimanenti nel nodo vengono riattivate.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --node-name [Required]| Il nome del nodo.|
| --timeout -t       | Timeout del server in secondi.  Predefinito: 60.|

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug            | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug.|
| --help -h          | Mostra questo messaggio della Guida e l'uscita.|
| --output -o        | Formato di output.  Valori consentiti: json, jsonc, tabella, tsv.  Predefinito: json.|
| --query            | Stringa di query JMESPath. Per maggiori informazioni ed esempi, vedere http://jmespath.org/.|
| --verbose          | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi.|

## <a name="sfctl-node-health"></a>sfctl node health
Mostra l'integrità di un nodo di Service Fabric.

Mostra l'integrità di un nodo di Service Fabric. Utilizzare EventsHealthStateFilter per filtrare la raccolta di eventi di stato riportati nel nodo in base allo stato di integrità. Se il nodo di cui si specifica il nome non esiste nell'archivio integrità, questo restituisce un errore.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --node-name [Obbligatorio]| Il nome del nodo.|
| --events-health-state-filter| Consente di filtrare la raccolta di oggetti HealthEvent restituiti in base allo stato di integrità. I valori possibili per questo parametro includono il valore intero di uno dei seguenti stati di integrità. Vengono restituiti solo gli eventi che corrispondono al filtro. Tutti gli eventi vengono utilizzati per valutare lo stato di integrità aggregato. Se non specificato diversamente, vengono restituite tutte le voci. I valori dello stato sono enumerati in base al flag, pertanto il valore potrebbe essere una combinazione di questi valori ottenuti utilizzando l'operatore "OR" bit per bit. Ad esempio, se il valore fornito è 6, vengono restituiti tutti gli eventi con valore dello stato di integrità OK (2) e di Avviso (4). - Default - Valore predefinito. Consente di ricercare qualsiasi stato di integrità. Il valore è zero. -None - Il filtro non corrisponde ad alcun valore di stato di integrità. Utilizzato per non restituire alcun risultato in un determinato insieme di stati. Il valore è 1. -Ok - Filtro che ricerca le corrispondenze di input con valore di stato di integrità Ok. Il valore è 2. -Warning - filtro che ricerca le corrispondenze di input con valore di stato di integrità Avviso. Il valore è 4. - Error - Filtro che ricerca le corrispondenze di input con valore di stato di integrità Errore. Il valore è 8. -All - Filtro che ricerca le corrispondenze di input con qualsiasi valore di stato di integrità. Il valore è 65535.|
| --timeout -t             | Timeout del server in secondi.  Predefinito: 60.|

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug                  | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug.|
| --help -h                | Mostra questo messaggio della Guida e l'uscita.|
| --output -o              | Formato di output.  Valori consentiti: json, jsonc, tabella, tsv.  Predefinito: json.|
| --query                  | Stringa di query JMESPath. Per maggiori informazioni ed esempi, vedere http://jmespath.org/.|
| --verbose                | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi.|

## <a name="sfctl-node-info"></a>sfctl node info
Mostra le informazioni su un nodo specifico del cluster di Service Fabric.

Mostra le informazioni su un nodo specifico del cluster di Service Fabric. La risposta include il nome, lo stato, l'ID, l'integrità, il tempo di attività e altri dettagli relativi al nodo.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --node-name [Required]| Il nome del nodo.|
| --timeout -t       | Timeout del server in secondi.  Predefinito: 60.|

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug            | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug.|
| --help -h          | Mostra questo messaggio della Guida e l'uscita.|
| --output -o        | Formato di output.  Valori consentiti: json, jsonc, tabella, tsv.  Predefinito: json.|
| --query            | Stringa di query JMESPath. Per maggiori informazioni ed esempi, vedere http://jmespath.org/.|
| --verbose          | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi.|

## <a name="sfctl-node-list"></a>sfctl node list
Mostra l'elenco dei nodi del cluster di Service Fabric.

Mostra l'elenco dei nodi del cluster di Service Fabric. La risposta include il nome, lo stato, l'ID, l'integrità, il tempo di attività e altri dettagli relativi al nodo.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --continuation-token| Il parametro del token di continuazione viene utilizzato per ottenere il set di risultati successivo. Un token di continuazione con un valore non vuoto è incluso nella risposta dell'API quando i risultati dal sistema non rientrano in una singola risposta.      Quando questo valore viene passato alla successiva chiamata API, l'API restituisce il set di risultati successivo. Se non sono presenti altri risultati, il token di continuazione non contiene alcun valore. Il valore di questo parametro non deve essere codificato in URL.|
| --node-status-filter| Consente di filtrare i nodi in base allo stato del nodo. Vengono restituiti solo i nodi che corrispondono al valore di filtro specificato. Il valore del filtro può essere uno dei seguenti. Valore predefinito: default.|
| --timeout -t     | Timeout del server in secondi.  Predefinito: 60.|

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug          | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug.|
| --help -h        | Mostra questo messaggio della Guida e l'uscita.|
| --output -o      | Formato di output.  Valori consentiti: json, jsonc, tabella, tsv.  Predefinito: json.|
| --query          | Stringa di query JMESPath. Per maggiori informazioni ed esempi, vedere http://jmespath.org/.|
| --verbose        | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi.|

## <a name="sfctl-node-load"></a>sfctl node load
Mostra le informazioni sul caricamento di un nodo di Service Fabric.

Recupera le informazioni sul caricamento di un nodo di Service Fabric per tutte le metriche per cui sono definiti il caricamento o la capacità.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --node-name [Required]| Il nome del nodo.|
| --timeout -t       | Timeout del server in secondi.  Predefinito: 60.|

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug            | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug.|
| --help -h          | Mostra questo messaggio della Guida e l'uscita.|
| --output -o        | Formato di output.  Valori consentiti: json, jsonc, tabella, tsv.  Predefinito: json.|
| --query            | Stringa di query JMESPath. Per maggiori informazioni ed esempi, vedere http://jmespath.org/.|
| --verbose          | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi.|

## <a name="sfctl-node-restart"></a>sfctl node restart
Riavvia un nodo del cluster di Service Fabric.

Riavvia un nodo del cluster di Service Fabric che è già stato avviato.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --node-name [Required]| Il nome del nodo.|
| --create-fabric-dump  | Specificare True per creare un dump del processo del nodo di infrastruttura. Questo valore fa distinzione tra maiuscole e minuscole.  Valore predefinito: False.|
| --node-instance-id | L'ID di istanza del nodo di destinazione. Se l'ID di istanza viene specificato, il nodo viene riavviato solo se corrisponde all'istanza corrente del nodo. Il valore predefinito è "0" corrisponde a qualsiasi ID dell'istanza. L'ID di istanza può essere ottenuto utilizzando la query get node.  Valore predefinito: 0.|
| --timeout -t       | Timeout del server in secondi.  Predefinito: 60.|

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug            | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug.|
| --help -h          | Mostra questo messaggio della Guida e l'uscita.|
| --output -o        | Formato di output.  Valori consentiti: json, jsonc, tabella, tsv.  Predefinito: json.|
| --query            | Stringa di query JMESPath. Per maggiori informazioni ed esempi, vedere http://jmespath.org/.|
| --verbose          | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi.|

## <a name="sfctl-node-transition"></a>sfctl node transition
Avvia o arresta un nodo del cluster.

Avvia o arresta un nodo del cluster.  Un nodo del cluster è un processo, non l’istanza del sistema operativo.
Per avviare un nodo, passare a "Start" per il parametro NodeTransitionType. Per arrestare un nodo, passare a "Stop" per il parametro NodeTransitionType. Questa API avvia l'operazione; quando l'API ritorna, il nodo può essere ancora in fase di transizione. Richiamare GetNodeTransitionProgress con lo stesso OperationId per ottenere lo stato di avanzamento dell'operazione. 

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --node-instance-id         [Obbligatorio]| L'ID di istanza del nodo di destinazione. Ciò può essere determinato tramite l'API GetNodeInfo.|
| --node-name                [Obbligatorio]| Il nome del nodo.|
| --node-transition-type     [Obbligatorio]| Indica il tipo di transizione da eseguire.                       NodeTransitionType.Start avvia un nodo arrestato.                       NodeTransitionType. Stop arresta un nodo attivo. |
| --operation-id             [Obbligatorio]| Una GUID che identifica una chiamata dell'API.  Questo viene passato all'API GetProgress corrispondente.|
| --stop-duration-in-seconds [Obbligatorio]| La durata, in secondi, di mantenimento del nodo arrestato.  Il valore minimo è 600, quello massimo 14400. Trascorso questo tempo, il nodo automaticamente torna allo stato attivo.|
| --timeout -t                      | Timeout del server in secondi.  Predefinito: 60.|

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug                           | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug.|
| --help -h                         | Mostra questo messaggio della Guida e l'uscita.|
| --output -o                       | Formato di output.  Valori consentiti: json, jsonc, tabella, tsv.                       Predefinito: json.|
| --query                           | Stringa di query JMESPath. Per maggiori informazioni ed esempi, vedere http://jmespath.org/.|
| --verbose                         | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi.|

## <a name="next-steps"></a>Passaggi successivi
- [Configurare](service-fabric-cli.md) l'interfaccia della riga di comando di Service Fabric.
- Informazioni su come usare l'interfaccia della riga di comando Service Fabric usando gli [script di esempio](/azure/service-fabric/scripts/sfctl-upgrade-application).