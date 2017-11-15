---
title: Interfaccia della riga di comando Azure Service Fabric - sfctl cluster | Microsoft Docs
description: Descrive i comandi dell'interfaccia della riga di comando Service Fabric sfctl cluster.
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
ms.openlocfilehash: 2af214a9aa3c67818e8ce64f204ebda32c35abc7
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2017
---
# <a name="sfctl-cluster"></a>sfctl cluster
Selezionare, gestire e usare dei cluster di Service Fabric.

## <a name="commands"></a>Comandi:

|Comando|Descrizione|
| --- | --- |
|    code-versions| Mostra un elenco di versioni del codice di un'infrastruttura di cui viene effettuato il provisioning in un cluster di Service Fabric.|
|    config-versions | Mostra un elenco di versioni di configurazione di un'infrastruttura di cui viene effettuato il provisioning in un cluster di Service Fabric.|
|    health       | Mostra l'integrità di un cluster di Service Fabric.|
|    manifest     | Mostra il manifesto del cluster di Service Fabric.|
|    operation-cancel| Annulla un'operazione di errore indotta dall'utente.|
|    operationgit | Mostra un elenco di operazioni di errore indotte dall'utente filtrato in base all'input specificato.|
|    provision     | Esegue il provisioning dei pacchetti di codici o configurazioni di un cluster di Service Fabric.|
|    recover-system  | Indica al cluster di Service Fabric che deve tentare di recuperare i servizi di sistema  attualmente bloccati in una perdita di quorum.|
|report-health   | Invia un report di integrità nel cluster di Service Fabric.|
|    seleziona       | Si connette a un endpoint di cluster di Service Fabric.|
| unprovision     | Annulla il provisioning dei pacchetti di codici o configurazioni di un cluster di Service Fabric.|
|    Aggiornamento         | Avvia l'aggiornamento della versione codice o configurazione di un cluster di Service Fabric.|
|    upgrade-resume  | Consente all'aggiornamento del cluster di passare al dominio di aggiornamento successivo.|
|    upgrade-rollback| Esegue il rollback dell'aggiornamento di un cluster di Service Fabric.|
|    upgrade-status  | Mostra lo stato di avanzamento dell'aggiornamento del cluster corrente.|
|upgrade-update  | Aggiorna i parametri di aggiornamento di un cluster di Service Fabric.|


## <a name="sfctl-cluster-health"></a>sfctl cluster health
Mostra l'integrità di un cluster di Service Fabric.

Mostra l'integrità di un cluster di Service Fabric. Usare EventsHealthStateFilter per filtrare la raccolta di eventi di stato riportati nel cluster in base allo stato di integrità. Analogamente, usare NodesHealthStateFilter e ApplicationsHealthStateFilter per filtrare la raccolta di nodi e applicazioni restituito in base al relativo stato di integrità aggregato. 

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --applications-health-state-filter| Consente di filtrare gli oggetti dello stato di integrità delle applicazioni restituiti nel risultato della query sull'integrità dei cluster in base al relativo stato di integrità. I valori possibili per questo parametro includono il valore intero ottenuto da membri o operazioni bit per bit sui membri dell'enumerazione HealthStateFilter. Vengono restituite solo le applicazioni che corrispondono al filtro.  Tutte le applicazioni vengono usate per valutare lo stato di integrità aggregato. Se non specificato diversamente, vengono restituite tutte le voci. I valori dello stato sono enumerati in base al flag. Pertanto il valore potrebbe essere una combinazione di questi valori ottenuti usando l'operatore "OR" bit per bit. Ad esempio, se il valore indicato è 6, viene restituito lo stato di integrità delle applicazioni il cui valore di HealthState è OK (2) e Avviso (4). - Default - Valore predefinito. Consente di ricercare qualsiasi stato di integrità. Il valore predefinito è zero. -None - Il filtro non corrisponde ad alcun valore di HealthState. Usato per non restituire alcun risultato in una determinata raccolta di stati. Il valore è uguale a 1. -Ok - Filtro che ricerca le corrispondenze di input con valore di HealthState Ok. Il valore è 2. -Warning - Filtro che ricerca le corrispondenze di input con valore di HealthState Avviso. Il valore è 4. - Error - Filtro che ricerca le corrispondenze di input con valore di HealthState Errore. Il valore è 8. -All - Filtro che ricerca le corrispondenze di input con qualsiasi valore di HealthState. Il valore è 65535.|
| --events-health-state-filter   | Consente di filtrare la raccolta di oggetti HealthEvent restituiti in base allo stato di integrità. I valori possibili per questo parametro includono il valore intero di uno degli stati di integrità seguenti. Vengono restituiti solo gli eventi che corrispondono al filtro. Tutti gli eventi vengono usati per valutare lo stato di integrità aggregato. Se non specificato diversamente, vengono restituite tutte le voci. I valori dello stato sono enumerati in base al flag. Pertanto il valore potrebbe essere una combinazione di questi valori ottenuti usando l'operatore "OR" bit per bit. Ad esempio, se il valore fornito è 6, vengono restituiti tutti gli eventi con valore di HealthState OK (2) e Avviso (4). - Default - Valore predefinito. Consente di ricercare le corrispondenze con qualsiasi valore di HealthState. Il valore predefinito è zero. -None - Il filtro non restituisce corrispondenze ad alcun valore di HealthState. Usato per non restituire alcun risultato in una determinata raccolta di stati. Il valore è uguale a 1. -Ok - Filtro che ricerca le corrispondenze di input con valore di HealthState Ok. Il valore è 2. -Warning - Filtro che ricerca le corrispondenze di input con valore di HealthState Avviso.  Il valore è 4. - Error - Filtro che ricerca le corrispondenze di input con valore di HealthState Errore. Il valore è 8. -All - Filtro che ricerca le corrispondenze di input con qualsiasi valore di HealthState. Il valore è 65535.|
|--exclude-health-statistics                   | Indica se le statistiche di integrità devono essere restituite come parte del risultato della query. False per impostazione predefinita. Le statistiche mostrano il numero di elementi figlio in stato di integrità Ok, Avviso ed Errore.|
 |   --include-system-application-health-statistics| Indica se le statistiche di integrità devono includere le statistiche di integrità di sistema dell'applicazione fabric:/System. False per impostazione predefinita. Se IncludeSystemApplicationHealthStatistics è impostato su true, le statistiche di integrità includono le entità che appartengono all'applicazione fabric:/System. In caso contrario, il risultato della query includerà soltanto le statistiche di integrità per le applicazioni utente. Per applicare questo parametro, è necessario includere le statistiche di integrità nel risultato della query.|
| --nodes-health-state-filter    | Consente di filtrare gli oggetti dello stato di integrità dei nodi restituiti nel risultato della query sull'integrità dei cluster in base al relativo stato di integrità. I valori possibili per questo parametro includono il valore intero di uno degli stati di integrità seguenti. Vengono restituiti solo i nodi che corrispondono al filtro. Tutti i nodi vengono usati per valutare lo stato di integrità aggregato. Se non specificato diversamente, vengono restituite tutte le voci. I valori dello stato sono enumerati in base al flag. Pertanto il valore potrebbe essere una combinazione di questi valori ottenuti usando l'operatore "OR" bit per bit. Ad esempio, se il valore fornito è "6", viene restituito lo stato di integrità dei nodi con valore di HealthState OK (2) e Warning (4). - Default - Valore predefinito. Consente di ricercare le corrispondenze con qualsiasi valore di HealthState. Il valore predefinito è zero. -None - Il filtro non restituisce corrispondenze ad alcun valore di HealthState. Usato per non restituire alcun risultato in una determinata raccolta di stati. Il valore è uguale a 1. -Ok - Filtro che ricerca le corrispondenze di input con valore di HealthState Ok. Il valore è 2. -Warning - Filtro che ricerca le corrispondenze di input con valore di HealthState Avviso.  Il valore è 4. - Error - Filtro che ricerca le corrispondenze di input con valore di HealthState Errore. Il valore è 8. -All - Filtro che ricerca le corrispondenze di input con qualsiasi valore di HealthState. Il valore è 65535.|
| --timeout -t                   | Timeout del server in secondi.  Predefinito: 60.|

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug                        | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug.|
| --help -h                      | Mostra questo messaggio della Guida e l'uscita.|
| --output -o                    | Formato di output.  Valori consentiti: json, jsonc, tabella, tsv.                    Predefinito: json.|
| --query                        | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http://jmespath.org/.|
| --verbose                      | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi.|

## <a name="sfctl-cluster-manifest"></a>sfctl cluster manifest
Mostra il manifesto del cluster di Service Fabric.

Mostra il manifesto del cluster di Service Fabric. Il manifesto del cluster contiene le proprietà del cluster che includono i diversi tipi di nodi nel cluster, le configurazioni di protezione, gli errori e le topologie del dominio di aggiornamento e così via. Queste proprietà vengono specificate come parte del file ClusterConfig.JSON durante la distribuzione di un cluster autonomo. Tuttavia, la maggior parte delle informazioni contenute nel manifesto del cluster viene generata internamente dall'infrastruttura del servizio durante la distribuzione del cluster in altri scenari di distribuzione (ad esempio, quando si usa il [portale di Azure](https://portal.azure.com)). Il contenuto del manifesto del cluster è solo a scopo informativo; gli utenti non sono tenuti a usare una dipendenza dal formato del contenuto dei file o dalla loro interpretazione. 

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --timeout -t| Timeout del server in secondi.  Predefinito: 60.|

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug  | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug.|
| --help -h| Mostra questo messaggio della Guida e l'uscita.|
| --output -o | Formato di output.  Valori consentiti: json, jsonc, tabella, tsv.  Predefinito: json.|
| --query  | Stringa di query JMESPath. Per maggiori informazioni ed esempi, vedere http://jmespath.org/.|
| --verbose| Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi.|

## <a name="sfctl-cluster-provision"></a>sfctl cluster provision
Esegue il provisioning dei pacchetti di codici o configurazioni di un cluster di Service Fabric.
Esegue la convalida e il provisioning dei pacchetti di codici o configurazioni di un cluster di Service Fabric.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
|--cluster-manifest-file-path| Percorso file del manifesto del cluster.|
|    --code-file-path            | Percorso file del pacchetto di codice del cluster.|
|    --timeout -t                | Timeout del server in secondi.  Predefinito: 60.|

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug.|
| --help -h  | Mostra questo messaggio della Guida e l'uscita.|
| --output -o| Formato di output.  Valori consentiti: json, jsonc, tabella, tsv.  Predefinito: json.|
| --query | Stringa di query JMESPath. Per maggiori informazioni ed esempi, vedere http://jmespath.org/.|
| --verbose  | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi.|

## <a name="sfctl-cluster-select"></a>sfctl cluster select
Si connette a un endpoint di cluster di Service Fabric.

In caso di connessione a un cluster sicuro, specificare un certificato (con estensione .crt), un file chiave (.key) o un singolo file con entrambi (.pem). Non specificare entrambi. Facoltativamente, in caso di connessione a un cluster protetto, specificare anche un percorso a un file di aggregazione CA o a una directory di CA considerate attendibili.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| -endpoint [obbligatorio]| URL dell'endpoint del cluster, incluso la porta e il prefisso HTTP o HTTPS.|
| --aad             | Consente di usare l'autenticazione tramite Azure Active Directory.|
| --ca              | Percorso verso la directory contenente i certificati delle autorità di certificazione da considerare come validi o verso il file di aggregazione delle autorità di certificazione.|
| --cert            | Percorso verso un file di certificato client.|
| --key             | Percorso verso un file chiave di certificato client.|
| --no-verify       | Disabilita la verifica dei certificati quando si usa il protocollo HTTPS. Nota: si tratta di un'opzione non protetta, che non deve essere usata per gli ambienti di produzione.|
| --pem             | Percorso verso un certificato client, sotto forma di file con estensione PEM.|

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug           | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug.|
| --help -h         | Mostra questo messaggio della Guida e l'uscita.|
| --output -o       | Formato di output.  Valori consentiti: json, jsonc, tabella, tsv.  Predefinito: json.|
| --query           | Stringa di query JMESPath. Per maggiori informazioni ed esempi, vedere http://jmespath.org/.|
| --verbose         | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi.|

## <a name="sfctl-cluster-unprovision"></a>sfctl cluster unprovision
Annulla il provisioning dei pacchetti di codici o configurazioni di un cluster di Service Fabric.

Annulla il provisioning dei pacchetti di codici o configurazioni di un cluster di Service Fabric.

### <a name="arguments"></a>Argomenti
|Argomento|Descrizione|
| --- | --- |
|--code-version  | Versione del pacchetto di codice del cluster.|
|    --config-version| Versione del manifesto del cluster.|
|    --timeout -t    | Timeout del server in secondi.  Predefinito: 60.|

### <a name="global-arguments"></a>Argomenti globali
|Argomento|Descrizione|
| --- | --- |
|--debug         | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug.|
 |   --help -h       | Mostra questo messaggio della Guida e l'uscita.|
 |   --output -o     | Formato di output.  Valori consentiti: json, jsonc, tabella, tsv.  Predefinito: json.|
 |   --query         | Stringa di query JMESPath. Per maggiori informazioni ed esempi, vedere http://jmespath.org/.|
 |   --verbose       | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi.|


## <a name="sfctl-cluster-upgrade"></a>sfctl cluster upgrade
Avvia l'aggiornamento della versione codice o configurazione di un cluster di Service Fabric.
Convalida i parametri di aggiornamento forniti e avvia l'aggiornamento della versione del codice o della configurazione di un cluster di Service Fabric se i parametri sono validi.

### <a name="arguments"></a>Argomenti
|Argomento|Descrizione|
| --- | --- |
|    --app-health-map                      | Dizionario con codifica JSON delle coppie di nomi dell'applicazione e percentuale massima di stato non integro prima che venga generato l'errore.|
 |   --app-type-health-map                 | Dizionario con codifica JSON delle coppie di nomi del tipo di applicazione e percentuale massima di stato non integro prima che venga generato l'errore.|
 |   --code-version                        | Versione del codice del cluster.|
 |   --config-version                      | Versione della configurazione del cluster.|
 |   --delta-health-evaluation             | Abilita la valutazione dell'integrità delta anziché la valutazione dell'integrità assoluta dopo il completamento di ogni dominio di aggiornamento.|
 |   --delta-unhealthy-nodes               | Percentuale massima di riduzione dell'integrità dei nodi consentita durante gli aggiornamenti del cluster.  Predefinito: 10. Il valore delta è misurato tra lo stato dei nodi e all'inizio dell'aggiornamento e lo stato dei nodi al momento della valutazione dell'integrità. Il controllo viene eseguito dopo il completamento dell'aggiornamento di ciascun dominio di aggiornamento per assicurarsi che lo stato complessivo del cluster rientri nei limiti di tolleranza.|
 |   --failure-action                      | I valori possibili sono: 'Invalid', 'Rollback', 'Manual'.|
 |   --force-restart                       | Forza il riavvio.|
 |   --health-check-retry                  | Timeout del nuovo tentativo di controllo dell'integrità misurato in millisecondi.|
 |   --health-check-stable                 | Durata stabile controllo integrità misurata in millisecondi.|
  |  --health-check-wait                   | Durata attesa controllo integrità misurata in millisecondi.|
  |  --replica-set-check-timeout           | Timeout del controllo del set di repliche di aggiornamento misurato in secondi.|
 |   --rolling-upgrade-mode                | I valori possibili sono: 'Invalid', 'UnmonitoredAuto', 'UnmonitoredManual', 'Monitored'.  Predefinito: UnmonitoredAuto.|
  |  --timeout -t                          | Timeout del server in secondi.  Predefinito: 60.|
  |  --unhealthy-applications              | Percentuale massima consentita di applicazioni non integre prima che venga segnalato un errore. Ad esempio, per consentire il 10% di applicazioni non integre, questo valore deve corrispondere a 10. La percentuale rappresenta la percentuale massima tollerata di applicazioni che possono risultare non integre prima che per il cluster venga impostato lo stato Error. Se la percentuale viene rispettata ma esiste almeno un'applicazione non integra, l'integrità viene valutata come Avviso. Questa viene calcolata dividendo il numero di applicazioni non integre rispetto al numero totale di istanze dell'applicazione nel cluster, escluse le applicazioni di tipi inclusi nel parametro ApplicationTypeHealthPolicyMap. Il calcolo viene arrotondato per eccesso per tollerare un errore su un numero limitato di applicazioni.|
 |   --unhealthy-nodes                     | Percentuale massima consentita di nodi non integri prima che venga segnalato un errore. Ad esempio, per consentire il 10% di nodi non integri, questo valore deve corrispondere a 10. La percentuale rappresenta la percentuale massima tollerata di nodi che possono risultare non integri prima che per il cluster venga impostato lo stato Error. Se la percentuale viene rispettata ma esiste almeno un nodo non integro, l'integrità viene valutata come Avviso. Tale percentuale viene calcolata dividendo il numero dei nodi non integri per il numero totale di nodi nel cluster. Il calcolo viene arrotondato per eccesso per tollerare un errore su un numero limitato di nodi. Questa percentuale dovrà essere configurata in modo da tenere conto del fatto che in cluster di grandi dimensioni sono sempre presenti nodi inattivi o in fase di riparazione.|
 |   --upgrade-domain-delta-unhealthy-nodes| Percentuale massima di riduzione dell'integrità dei nodi del dominio di aggiornamento consentita durante gli aggiornamenti del cluster. Impostazione predefinita: 15. Il valore delta è misurato tra lo stato dei nodi di dominio all'inizio dell'aggiornamento e lo stato dei nodi di dominio al momento della valutazione dell'integrità. Il controllo viene eseguito dopo il completamento dell'aggiornamento di ciascun dominio di aggiornamento per tutti i domini di aggiornamento completati per assicurarsi che lo stato complessivo dei domini di aggiornamento rientri nei limiti di tolleranza.|
 |   --upgrade-domain-timeout              | Timeout del dominio di aggiornamento misurato in millisecondi.|
 |   --upgrade-timeout                     | Timeout dell'aggiornamento misurato in millisecondi.|
 |   --warning-as-error                    | Gli avvisi vengono considerati con lo stello livello di gravità degli errori.|

### <a name="global-arguments"></a>Argomenti globali
|Argomento|Descrizione|
| --- | --- |
|--debug                               | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug.|
|    --help -h                             | Mostra questo messaggio della Guida e l'uscita.|
|    --output -o                           | Formato di output.  Valori consentiti: json, jsonc, tabella, tsv. Predefinito: json.|
|    --query                               | Stringa di query JMESPath. Per maggiori informazioni ed esempi, vedere http://jmespath.org/.|
|    --verbose                             | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi.|

## <a name="next-steps"></a>Passaggi successivi
- [Configurare](service-fabric-cli.md) l'interfaccia della riga di comando di Service Fabric.
- Informazioni su come usare l'interfaccia della riga di comando Service Fabric usando gli [script di esempio](/azure/service-fabric/scripts/sfctl-upgrade-application).