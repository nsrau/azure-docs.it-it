---
title: 'Interfaccia della riga di comando Azure Service Fabric: sfctl choas | Microsoft Docs'
description: Descrive i comandi dell'interfaccia della riga di comando Service Fabric sfctl chaos.
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
ms.openlocfilehash: 7118ec3f5aeae40fa7ecc592309a28b585c651a1
ms.contentlocale: it-it
ms.lasthandoff: 09/26/2017

---
# <a name="sfctl-chaos"></a>sfctl chaos
Consente di avviare, arrestare e creare report nel servizio di test chaos.

## <a name="commands"></a>Comandi:

|Comando|Descrizione|
| --- | --- |
|    report| Recupera il segmento successivo del report di Chaos in base al token di continuazione passato o all'intervallo di tempo passato.|
|    start | Se Chaos non è già in esecuzione nel cluster, lo avvia con i parametri di Chaos specificati.|
|    stop  | Arresta Chaos nel cluster se è già in esecuzione, in caso contrario non esegue alcuna operazione.|


## <a name="sfctl-chaos-report"></a>sfctl chaos report
Recupera il segmento successivo del report di Chaos in base al token di continuazione passato o all'intervallo di tempo passato.

È possibile specificare ContinuationToken per ottenere il segmento successivo del report di Chaos oppure è possibile specificare l'intervallo di tempo tramite StartTimeUtc ed EndTimeUtc, ma non è possibile specificare sia ContinuationToken che l'intervallo di tempo nella stessa chiamata. Quando ci sono più di 100 eventi di Chaos, il report di Chaos viene restituito in segmenti e ogni segmento contiene non più di 100 eventi. 

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --continuation-token| Il parametro del token di continuazione viene utilizzato per ottenere il set di risultati successivo. Un token di continuazione con un valore non vuoto è incluso nella risposta dell'API quando i risultati dal sistema non rientrano in una singola risposta. Quando questo valore viene passato alla successiva chiamata API, l'API restituisce il set di risultati successivo. Se non sono presenti altri risultati, il token di continuazione non contiene alcun valore. Il valore di questo parametro non deve essere codificato in URL.|
| --end-time-utc   | Il numero di tick che rappresenta l'ora di fine dell'intervallo di tempo per cui un
                          Chaos report is to be generated. Please consult [DateTime.Ticks
                          Property](https://msdn.microsoft.com/en-
                          us/library/system.datetime.ticks%28v=vs.110%29) for details about tick.|
| --start-time-utc | Il numero di tick che rappresenta l'ora di inizio dell'intervallo di tempo per cui viene generato un report di Chaos. Consultare [proprietà DateTime.Ticks] (https://msdn.microsoft.com/en- us/library/system.datetime.ticks%28v=vs.110%29) per informazioni dettagliate sui tick.| | --timeout -t | Timeout del server in secondi.  Impostazione predefinita: 60.|

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug          | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug.|
| --help -h        | Mostra questo messaggio della Guida e l'uscita.|
| --output -o      | Formato di output.  Valori consentiti: json, jsonc, tabella, tsv.  Predefinito: json.|
| --query          | Stringa di query JMESPath. Per maggiori informazioni ed esempi, vedere http://jmespath.org/.|
| --verbose        | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi.|

## <a name="sfctl-chaos-start"></a>sfctl chaos start
Se Chaos non è già in esecuzione nel cluster, lo avvia con i parametri di Chaos specificati.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --app-type-health-policy-map  | Elenco con codifica JSON con una percentuale massima di applicazioni non integre per tipi di applicazione specifici. Ogni voce specifica come chiave il nome del tipo di applicazione e come valore un valore intero che rappresenta la percentuale MaxPercentUnhealthyApplications usata per valutare le applicazioni del tipo di applicazione specificato.|
| --disable-move-replica-faults | Disabilita lo spostamento primario e sposta gli errori secondari.|
| --max-cluster-stabilization| Il tempo di attesa massimo affinché tutte le entità del cluster diventino stabili e integre.  Predefinito: 60.|
| --max-concurrent-faults    | Il numero massimo di errori simultanei indotti per ogni iterazione.           Impostazione predefinita: 1.|
| --max-percent-unhealthy-apps  | Quando si esegue la valutazione dell'integrità del cluster durante l'esecuzione di Chaos, la percentuale massima consentita di applicazioni non integre prima che venga segnalato un errore.|
| --max-percent-unhealthy-nodes | Quando si esegue la valutazione dell'integrità del cluster durante l'esecuzione di Chaos, la percentuale massima consentita di nodi non integri prima che venga segnalato un errore.|
| --time-to-run              | Tempo totale, in secondi, in cui Chaos verrà eseguito prima di interrompersi automaticamente. Il valore massimo consentito è 4.294.967.295 (System.UInt32.MaxValue).  Valore predefinito: 4294967295.|
| --timeout -t               | Timeout del server in secondi.  Predefinito: 60.|
| --wait-time-between-faults | Tempo di attesa, in secondi, tra errori consecutivi all'interno di una singola iterazione.  Valore predefinito: 20.|
| --wait-time-between-iterations| Intervallo di tempo, in secondi, tra due iterazioni consecutive di Chaos.  Valore predefinito: 30.|
| --warning-as-error         | Quando si valuta l'integrità del cluster durante l'esecuzione di Chaos, considerare gli avvisi con lo stesso livello di gravità degli errori.|

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug                    | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug.|
| --help -h                  | Mostra questo messaggio della Guida e l'uscita.|
| --output -o                | Formato di output.  Valori consentiti: json, jsonc, tabella, tsv.           Predefinito: json.|
| --query                    | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http://jmespath.org/.|
| --verbose                  | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi.|

## <a name="sfctl-chaos-stop"></a>sfctl chaos stop
Arresta Chaos nel cluster se è già in esecuzione, in caso contrario non esegue alcuna operazione.

Arresta la pianificazione di altri errori di Chaos. Tuttavia, gli errori in transito non vengono presi in considerazione.

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

## <a name="next-steps"></a>Passaggi successivi
- [Configurare](service-fabric-cli.md) l'interfaccia della riga di comando di Service Fabric.
- Informazioni su come usare l'interfaccia della riga di comando Service Fabric usando gli [script di esempio](/azure/service-fabric/scripts/sfctl-upgrade-application).
