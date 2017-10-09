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
ms.translationtype: HT
ms.sourcegitcommit: cb9130243bdc94ce58d6dfec3b96eb963cdaafb0
ms.openlocfilehash: dc2ed59d6adaca97b23dddcb7ec968d90171b483
ms.contentlocale: it-it
ms.lasthandoff: 09/26/2017

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
| --applications-health-state-filter| Consente di filtrare lo stato di integrità dell'applicazione
                                                    objects returned in the result of cluster health
                                                    query based on their health state. The possible
                                                    values for this parameter include integer value
                                                    obtained from members or bitwise operations on
                                                    members of HealthStateFilter enumeration. Only
                                                    applications that match the filter are returned.
                                                    All applications are used to evaluate the
                                                    aggregated health state. If not specified, all
                                                    entries are returned. The state values are flag
                                                    based enumeration, so the value could be a
                                                    combination of these values obtained using
                                                    bitwise 'OR' operator. For example, if the
                                                    provided value is 6 then health state of
                                                    applications with HealthState value of OK (2)
                                                    and Warning (4) are returned. - Default -
                                                    Default value. Matches any HealthState. The
                                                    value is zero. - None - Filter that doesn't
                                                    match any HealthState value. Used in order to
                                                    return no results on a given collection of
                                                    states. The value is 1. - Ok - Filter that
                                                    matches input with HealthState value Ok. The
                                                    value is 2. - Warning - Filter that matches
                                                    input with HealthState value Warning. The value
                                                    is 4. - Error - Filter that matches input with
                                                    HealthState value Error. The value is 8. - All -
                                                    Filter that matches input with any HealthState value. The value is 65535.|
| --events-health-state-filter |Consente di filtrare la raccolta di oggetti HealthEvent restituiti in base allo stato di integrità. I valori possibili per questo parametro includono il valore intero di uno dei seguenti stati di integrità. Vengono restituiti solo gli eventi che corrispondono al filtro. Tutti gli eventi vengono usati per valutare lo stato di integrità aggregato. Se non specificato diversamente, vengono restituite tutte le voci. I valori dello stato sono enumerati in base al flag, pertanto il valore potrebbe essere una combinazione di questi valori ottenuti usando l'operatore "OR" bit per bit. Ad esempio, se il valore fornito è 6, vengono restituiti tutti gli eventi con valore dello stato di integrità OK (2) e di Avviso (4). - Default - Valore predefinito. Consente di ricercare qualsiasi stato di integrità. Il valore predefinito è zero. - None - Il filtro non corrisponde ad alcun valore di stato di integrità. Usato per non restituire alcun risultato in un determinato insieme di stati. Il valore è uguale a 1. -Ok - Filtro che ricerca le corrispondenze di input con valore di stato di integrità Ok. Il valore è 2. -Warning - filtro che ricerca le corrispondenze di input con valore di stato di integrità Avviso.
Il valore è 4. - Error - Filtro che ricerca le corrispondenze di input con valore di stato di integrità Errore. Il valore è 8. -All - Filtro che ricerca le corrispondenze di input con qualsiasi valore di stato di integrità. Il valore è 65535.| |--exclude-health-statistics                   | Indica se le statistiche di integrità devono essere restituite come parte del risultato della query. False per impostazione predefinita. Le statistiche mostrano il numero di entità figlie con integrità OK, Avviso ed Errore. | |--include-system-application-health-statistics| Indica se le statistiche di integrità devono includere le statistiche di integrità dell'applicazione fabric:/System. False per impostazione predefinita. Se IncludeSystemApplicationHealthStatistics è impostato su true, le statistiche di integrità includono le entità che appartengono all'applicazione fabric:/System. In caso contrario, il risultato della query includerà soltanto le statistiche di integrità per le applicazioni utente. Le statistiche di integrità devono essere incluse nel risultato della query perché questo parametro venga applicato.| | --nodes-health-state-filter    | Consente di filtrare gli oggetti dello stato di integrità di un nodo restituiti nei risultati della query di integrità del cluster in base al relativo stato di integrità. I valori possibili per questo parametro includono il valore intero di uno dei seguenti stati di integrità. Vengono restituiti solo i nodi che corrispondono al filtro. Tutti i nodi vengono usati per valutare lo stato di integrità aggregato. Se non specificato diversamente, vengono restituite tutte le voci. I valori dello stato sono enumerati in base al flag, pertanto il valore potrebbe essere una combinazione di questi valori ottenuti usando l'operatore "OR" bit per bit. Ad esempio, se il valore fornito è "6", viene restituito lo stato di integrità dei nodi con valore HealthState OK (2) e Warning (4). - Default - Valore predefinito. Consente di ricercare qualsiasi stato di integrità. Il valore predefinito è zero. - None - Il filtro non corrisponde ad alcun valore di stato di integrità. Usato per non restituire alcun risultato in un determinato insieme di stati. Il valore è uguale a 1. -Ok - Filtro che ricerca le corrispondenze di input con valore di stato di integrità Ok. Il valore è 2. -Warning - filtro che ricerca le corrispondenze di input con valore di stato di integrità Avviso.
Il valore è 4. - Error - Filtro che ricerca le corrispondenze di input con valore di stato di integrità Errore. Il valore è 8. -All - Filtro che ricerca le corrispondenze di input con qualsiasi valore di stato di integrità. Il valore è 65535.| | --timeout -t                   | Timeout del server in secondi.  Impostazione predefinita: 60.|

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

        Validate and provision the code or configuration packages of a Service Fabric cluster.

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

        Unprovision the code or configuration packages of a Service Fabric cluster.

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
 |   --query         | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http://jmespath.org/
                      .|
 |   --verbose       | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi.|


## <a name="sfctl-cluster-upgrade"></a>sfctl cluster upgrade
Avvia l'aggiornamento della versione codice o configurazione di un cluster di Service Fabric.

        Validate the supplied upgrade parameters and start upgrading the code or configuration
        version of a Service Fabric cluster if the parameters are valid.

### <a name="arguments"></a>Argomenti
|Argomento|Descrizione|
| --- | --- |
|    --app-health-map                      | Dizionario con codifica JSON delle coppie di nomi dell'applicazione e
                                            percentuale massima di valori non integri prima che venga generato l'errore.|
 |   --app-type-health-map                 | Dizionario con codifica JSON delle coppie di tipi di applicazioni
                                            name and maximum percentage unhealthy before raising
                                            error.|
 |   --code-version                        | Versione del codice del cluster.| |   --config-version                      | Versione di configurazione del cluster.| |   --delta-health-evaluation             | Consente di valutare l'integrità delta anziché l'integrità assoluta dopo il completamento di ogni dominio di aggiornamento.| |   --delta-unhealthy-nodes               | Percentuale massima consentita di riduzione delle prestazioni di integrità dei nodi durante l'aggiornamento del cluster.  Predefinito: 10.
Il valore delta è misurato tra lo stato dei nodi e all'inizio dell'aggiornamento e lo stato dei nodi al momento della valutazione dell'integrità. Il controllo viene eseguito dopo il completamento di ogni aggiornamento di dominio per assicurarsi che lo stato complessivo del cluster rientri nei limiti di tolleranza.| |   --failure-action                      | I valori possibili sono: "Invalid", "Rollback", "Manual".| |   --force-restart                       | Forza il riavvio.| |   --health-check-retry                  | Timeout dei tentativi di controllo integrità misurato in millisecondi.| |   --health-check-stable                 | Durata stabile del controllo di integrità misurata in millisecondi.| |  --health-check-wait                   | Durata di attesa del controllo integrità misurata in millisecondi.| |  --replica-set-check-timeout           | Timeout in secondi del controllo del set di repliche di aggiornamento.| |   --rolling-upgrade-mode                | I valori possibili sono: "Invalid", "UnmonitoredAuto", "UnmonitoredManual", "Monitored".  Impostazione predefinita: UnmonitoredAuto.| |  --timeout -t                          | Timeout del server in secondi.  Impostazione predefinita: 60.| |  --unhealthy-applications              | Percentuale massima consentita di applicazioni non integre prima che venga segnalato un errore.
Ad esempio, per consentire il 10% di applicazioni non integre, questo valore deve corrispondere a 10. La percentuale rappresenta la percentuale massima tollerata di applicazioni che possono risultare non integre prima che per il cluster venga impostato lo stato Error. Se la percentuale viene rispettata ma esiste almeno un'applicazione non integra, l'integrità viene valutata come Avviso. Questa viene calcolata dividendo il numero di applicazioni non integre rispetto al numero totale di istanze dell'applicazione nel cluster, escluse le applicazioni di tipi inclusi nel parametro ApplicationTypeHealthPolicyMap. Il calcolo arrotonda per eccesso per tollerare un errore in un numero limitato di applicazioni.| |   --unhealthy-nodes                     | Percentuale massima consentita di nodi non integri prima di segnalare un errore.
Ad esempio, per consentire il 10% di nodi non integri, questo valore deve corrispondere a 10. La percentuale rappresenta la percentuale massima tollerata di nodi che possono risultare non integri prima che per il cluster venga impostato lo stato Error. Se la percentuale viene rispettata ma esiste almeno un nodo non integro, l'integrità viene valutata come Avviso. Tale percentuale viene calcolata dividendo il numero dei nodi non integri per il numero totale di nodi nel cluster. Il calcolo viene arrotondato per eccesso per tollerare un errore su un numero limitato di nodi. Nei cluster di grandi dimensioni, alcuni nodi saranno sempre down o out per i ripristini, pertanto questa percentuale dovrà essere configurata in modo da tollerare tale condizione.| |   --upgrade-domain-delta-unhealthy-nodes| Percentuale massima consentita di riduzione delle prestazioni di integrità dei nodi di dominio di aggiornamento durante gli aggiornamenti del cluster.
Impostazione predefinita: 15.
Il valore delta è misurato tra lo stato dei nodi di dominio all'inizio dell'aggiornamento e lo stato dei nodi di dominio al momento della valutazione dell'integrità. Il controllo viene eseguito dopo il completamento di ogni aggiornamento del dominio per tutti i domini di aggiornamento completati per verificare che lo stato dei domini di aggiornamento rientri nei limiti di tolleranza.| |   --upgrade-domain-timeout              | Timeout del dominio di aggiornamento misurato in millisecondi.| |   --upgrade-timeout                     | Timeout di aggiornamento misurato in millisecondi.| |   --warning-as-error                    | Gli avvisi vengono considerati con lo stesso livello di gravità degli errori. |

### <a name="global-arguments"></a>Argomenti globali
    |Argomento|Descrizione|
| --- | --- |
|--debug                               | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug.|
|    --help -h                             | Mostra questo messaggio della Guida e l'uscita.|
|    --output -o                           | Formato di output.  Valori consentiti: json, jsonc, tabella, tsv.
                                            Predefinito: json.|
|    --query                               | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http://jmespath.org/
                                            .|
|    --verbose                             | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug
                                            completi.|

## <a name="next-steps"></a>Passaggi successivi
- [Configurare](service-fabric-cli.md) l'interfaccia della riga di comando di Service Fabric.
- Informazioni su come usare l'interfaccia della riga di comando Service Fabric usando gli [script di esempio](/azure/service-fabric/scripts/sfctl-upgrade-application).
