---
title: Interfaccia della riga di comando Azure Service Fabric - sfctl replica | Microsoft Docs
description: Descrive i comandi dell'interfaccia della riga di comando Service Fabric sfctl replica.
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
ms.date: 12/22/2017
ms.author: ryanwi
ms.openlocfilehash: 422c19dfa9a204d98a898f76bc1af92a05c054d0
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/18/2018
---
# <a name="sfctl-replica"></a>sfctl replica
Consente di gestire le repliche che appartengono alle partizioni del servizio.

## <a name="commands"></a>Comandi:

|Comando|DESCRIZIONE|
| --- | --- |
|    deployed  | Recupera i dettagli della replica distribuita in un nodo di Service Fabric.|
|    deployed-list| Recupera l'elenco della replica distribuita in un nodo di Service Fabric.|
|    health    | Recupera l'integrità di una replica di un servizio con stato o di un'istanza di un servizio senza stato di Service Fabric.|
|    info      | Recupera le informazioni su una replica di una partizione di Service Fabric.|
|    list      | Recupera le informazioni sulle repliche di una partizione del servizio Service Fabric.|
|    remove    | Rimuove una replica del servizio in esecuzione in un nodo.|
|    report-health| Invia un report di integrità nella replica di Service Fabric.|
|    restart   | Riavvia una replica del servizio di un servizio persistente in esecuzione in un nodo.|


## <a name="sfctl-replica-deployed"></a>sfctl replica deployed
Recupera i dettagli della replica distribuita in un nodo di Service Fabric.

Recupera i dettagli della replica distribuita in un nodo di Service Fabric. Le informazioni includono il tipo di servizio, il nome del servizio, l'operazione del servizio corrente, l'ora e la data di inizio dell'operazione del servizio corrente, l'ID della partizione, l'ID dell'istanza e della replica, il carico segnalato e altre informazioni.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --node-name [Obbligatorio]| Il nome del nodo.|
| --partition-id [Obbligatorio]| L'identità della partizione.|
| --replica-id [Obbligatorio]| Identificatore della replica.|
| --timeout -t          | Timeout del server in secondi.  Predefinito: 60.|

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug               | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug.|
| --help -h             | Mostra questo messaggio della Guida e l'uscita.|
| --output -o           | Formato di output.  Valori consentiti: json, jsonc, tabella, tsv.  Predefinito: json.|
| --query               | Stringa di query JMESPath. Per maggiori informazioni ed esempi, vedere http://jmespath.org/.|
| --verbose             | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi.|

## <a name="sfctl-replica-health"></a>sfctl replica health
Recupera l'integrità di una replica di un servizio con stato o di un'istanza di un servizio senza stato di Service Fabric.

Recupera l'integrità di una replica di Service Fabric. Usare EventsHealthStateFilter per filtrare la raccolta di eventi di integrità riportati nella replica in base allo stato di integrità. .

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --partition-id [Obbligatorio]| L'identità della partizione.|
| --replica-id [Obbligatorio]| Identificatore della replica.|
| --events-health-state-filter| Consente di filtrare la raccolta di oggetti HealthEvent restituiti in base allo stato di integrità. I valori possibili per questo parametro includono il valore intero di uno dei seguenti stati di integrità. Vengono restituiti solo gli eventi che corrispondono al filtro. Tutti gli eventi vengono utilizzati per valutare lo stato di integrità aggregato. Se non specificato diversamente, vengono restituite tutte le voci. I valori dello stato sono enumerati in base al flag, pertanto il valore potrebbe essere una combinazione di questi valori ottenuti usando l'operatore "OR" bit per bit. Ad esempio, se il valore fornito è 6, vengono restituiti tutti gli eventi con valore dello stato di integrità OK (2) e di Avviso (4). - Default - Valore predefinito. Consente di ricercare qualsiasi stato di integrità. Il valore è zero. -None - Il filtro non corrisponde ad alcun valore di stato di integrità. Utilizzato per non restituire alcun risultato in un determinato insieme di stati. Il valore è 1. -Ok - Filtro che ricerca le corrispondenze di input con valore di stato di integrità Ok. Il valore è 2. -Warning - filtro che ricerca le corrispondenze di input con valore di stato di integrità Avviso. Il valore è 4. - Error - Filtro che ricerca le corrispondenze di input con valore di stato di integrità Errore. Il valore è 8. -All - Filtro che ricerca le corrispondenze di input con qualsiasi valore di stato di integrità. Il valore è 65535.|
| --timeout -t             | Timeout del server in secondi.  Predefinito: 60.|

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug                  | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug.|
| --help -h                | Mostra questo messaggio della Guida e l'uscita.|
| --output -o              | Formato di output.  Valori consentiti: json, jsonc, tabella, tsv.  Predefinito: json.|
| --query                  | Stringa di query JMESPath. Per maggiori informazioni ed esempi, vedere http://jmespath.org/.|
| --verbose                | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi.|

## <a name="sfctl-replica-info"></a>sfctl replica info
Recupera le informazioni su una replica di una partizione di Service Fabric.

La risposta include l'ID, il ruolo, lo stato, l'integrità, il nome del nodo, il tempo di attività e altri dettagli relativi alla replica.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --partition-id [Obbligatorio]| L'identità della partizione.|
| --replica-id [Obbligatorio]| Identificatore della replica.|
| --continuation-token  | Il parametro del token di continuazione viene usato per ottenere il set di risultati successivo. Un token di continuazione con un valore non vuoto è incluso nella risposta dell'API quando i risultati dal sistema non rientrano in una singola risposta. Quando questo valore viene passato alla successiva chiamata API, l'API restituisce il set di risultati successivo. Se non sono presenti altri risultati, il token di continuazione non contiene alcun valore. Il valore di questo parametro non deve essere codificato in URL.|
| --timeout -t          | Timeout del server in secondi.  Predefinito: 60.|

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug               | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug.|
| --help -h             | Mostra questo messaggio della Guida e l'uscita.|
| --output -o           | Formato di output.  Valori consentiti: json, jsonc, tabella, tsv.  Predefinito: json.|
| --query               | Stringa di query JMESPath. Per maggiori informazioni ed esempi, vedere http://jmespath.org/.|
| --verbose             | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi.|

## <a name="sfctl-replica-list"></a>sfctl replica list
Recupera le informazioni sulle repliche di una partizione del servizio Service Fabric.

L'endpoint GetReplicas restituisce le informazioni sulle repliche della partizione specificata.
La risposta include l'ID, il ruolo, lo stato, l'integrità, il nome del nodo, il tempo di attività e altri dettagli relativi alla replica.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --partition-id [Obbligatorio]| L'identità della partizione.|
| --continuation-token  | Il parametro del token di continuazione viene usato per ottenere il set di risultati successivo. Un token di continuazione con un valore non vuoto è incluso nella risposta dell'API quando i risultati dal sistema non rientrano in una singola risposta. Quando questo valore viene passato alla successiva chiamata API, l'API restituisce il set di risultati successivo. Se non sono presenti altri risultati, il token di continuazione non contiene alcun valore. Il valore di questo parametro non deve essere codificato in URL.|
| --timeout -t          | Timeout del server in secondi.  Predefinito: 60.|

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug               | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug.|
| --help -h             | Mostra questo messaggio della Guida e l'uscita.|
| --output -o           | Formato di output.  Valori consentiti: json, jsonc, tabella, tsv.  Predefinito: json.|
| --query               | Stringa di query JMESPath. Per maggiori informazioni ed esempi, vedere http://jmespath.org/.|
| --verbose             | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi.|

## <a name="sfctl-replica-remove"></a>sfctl replica remove
Rimuove una replica del servizio in esecuzione in un nodo.

Questa API consente di simulare un errore di replica di Service Fabric rimuovendo una replica da un cluster di Service Fabric. La rimozione chiude la replica, la passa al ruolo Nessuno e quindi rimuove tutte le informazioni sullo stato della replica dal cluster. Questa API verifica il percorso di rimozione dello stato della replica e simula il percorso permanente di errore del report tramite le API del client. Avviso: quando si usa questa API non viene eseguito alcun controllo di sicurezza. L'uso errato di questa API può causare la perdita di dati per i servizi con stato. In aggiunta, il flag forceRemove influisce su tutte le altre repliche ospitate nello stesso processo.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --node-name [Obbligatorio]| Il nome del nodo.|
| --partition-id [Obbligatorio]| L'identità della partizione.|
| --replica-id [Obbligatorio]| Identificatore della replica.|
| --force-remove        | Consente di rimuovere un servizio o un'applicazione di Service Fabric in modo forzato senza passare attraverso la sequenza di arresto normale. Questo parametro può essere usato per eliminare in modo forzato un'applicazione o un servizio per il quale l'operazione di eliminazione è prossima al timeout a causa di problemi nel codice del servizio che impediscono la normale chiusura delle repliche.|
| --timeout -t          | Timeout del server in secondi.  Predefinito: 60.|

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug               | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug.|
| --help -h             | Mostra questo messaggio della Guida e l'uscita.|
| --output -o           | Formato di output.  Valori consentiti: json, jsonc, tabella, tsv.  Predefinito: json.|
| --query               | Stringa di query JMESPath. Per maggiori informazioni ed esempi, vedere http://jmespath.org/.|
| --verbose             | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi.|

## <a name="sfctl-replica-restart"></a>sfctl replica restart
Riavvia una replica del servizio di un servizio persistente in esecuzione in un nodo.

Riavvia una replica del servizio di un servizio persistente in esecuzione in un nodo. Avviso: quando si usa questa API non viene eseguito alcun controllo di sicurezza. L'uso non corretto di questa API può causare la perdita di disponibilità per i servizi con stato.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --node-name [Obbligatorio]| Il nome del nodo.|
| --partition-id [Obbligatorio]| L'identità della partizione.|
| --replica-id [Obbligatorio]| Identificatore della replica.|
| --timeout -t          | Timeout del server in secondi.  Predefinito: 60.|

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug               | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug.|
| --help -h             | Mostra questo messaggio della Guida e l'uscita.|
| --output -o           | Formato di output.  Valori consentiti: json, jsonc, tabella, tsv.  Predefinito: json.|
| --query               | Stringa di query JMESPath. Per maggiori informazioni ed esempi, vedere http://jmespath.org/.|
| --verbose             | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi.|

## <a name="next-steps"></a>Passaggi successivi
- [Configurare](service-fabric-cli.md) l'interfaccia della riga di comando di Service Fabric.
- Informazioni su come usare l'interfaccia della riga di comando Service Fabric usando gli [script di esempio](/azure/service-fabric/scripts/sfctl-upgrade-application).
