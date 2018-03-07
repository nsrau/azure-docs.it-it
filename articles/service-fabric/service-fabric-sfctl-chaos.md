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
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 02/22/2018
ms.author: ryanwi
ms.openlocfilehash: 34e4d47b1de509c2053996d9d1078733d7055447
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="sfctl-chaos"></a>sfctl chaos
Consente di avviare, arrestare e creare report nel servizio di test chaos.

## <a name="commands"></a>Comandi:

|Comando|DESCRIZIONE|
| --- | --- |
|    report| Recupera il segmento successivo del report di Chaos in base al token di continuazione passato o all'intervallo di tempo passato.|
|    start | Avvia Chaos nel cluster.|
|    stop  | Arresta Chaos nel cluster se è già in esecuzione, in caso contrario non esegue alcuna operazione.|


## <a name="sfctl-chaos-report"></a>sfctl chaos report
Recupera il segmento successivo del report di Chaos in base al token di continuazione passato o all'intervallo di tempo passato.

È possibile specificare ContinuationToken per ottenere il segmento successivo del report di Chaos oppure è possibile specificare l'intervallo di tempo tramite StartTimeUtc ed EndTimeUtc, ma non è possibile specificare sia ContinuationToken che l'intervallo di tempo nella stessa chiamata. Quando ci sono più di 100 eventi di Chaos, il report di Chaos viene restituito in segmenti e ogni segmento contiene non più di 100 eventi. 

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --continuation-token| Il parametro del token di continuazione viene utilizzato per ottenere il set di risultati successivo. Un token di continuazione con un valore non vuoto è incluso nella risposta dell'API quando i risultati dal sistema non rientrano in una singola risposta. Quando questo valore viene passato alla successiva chiamata API, l'API restituisce il set di risultati successivo. Se non sono presenti altri risultati, il token di continuazione non contiene alcun valore. Il valore di questo parametro non deve essere codificato in URL.|
| --end-time-utc   | Ora di file di Windows che rappresenta l'ora di fine dell'intervallo di tempo per cui verrà generato un report di Chaos. Per informazioni dettagliate, vedere [Metodo DateTime.ToFileTimeUtc](https://msdn.microsoft.com/library/system.datetime.tofiletimeutc(v=vs.110).aspx).|
| --start-time-utc | Ora di file di Windows che rappresenta l'ora di inizio dell'intervallo di tempo per cui verrà generato un report di Chaos. Per informazioni dettagliate, vedere [Metodo DateTime.ToFileTimeUtc](https://msdn.microsoft.com/library/system.datetime.tofiletimeutc(v=vs.110).aspx).|
| --timeout -t     | Timeout del server in secondi.  Predefinito: 60.|

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug          | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug.|
| --help -h        | Mostra questo messaggio della Guida e l'uscita.|
| --output -o      | Formato di output.  Valori consentiti: json, jsonc, tabella, tsv.  Predefinito: json.|
| --query          | Stringa di query JMESPath. Per maggiori informazioni ed esempi, vedere http://jmespath.org/.|
| --verbose        | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi.|

## <a name="sfctl-chaos-start"></a>sfctl chaos start
Avvia Chaos nel cluster.

Se Chaos non è già in esecuzione nel cluster, avvia Chaos con i parametri di Chaos passati. Se Chaos è già in esecuzione quando viene effettuata la chiamata, la chiamata ha esito negativo con codice di errore FABRIC_E_CHAOS_ALREADY_RUNNING.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --app-type-health-policy-map  | Elenco con codifica JSON con una percentuale massima di applicazioni non integre per tipi di applicazione specifici. Ogni voce specifica come chiave il nome del tipo di applicazione e come valore un valore intero che rappresenta la percentuale MaxPercentUnhealthyApplications usata per valutare le applicazioni del tipo di applicazione specificato. Definisce una mappa con una percentuale massima di applicazioni non integre per tipi di applicazione specifici. Ogni voce specifica come chiave il nome del tipo di applicazione e come valore un valore intero che rappresenta la percentuale MaxPercentUnhealthyApplications usata per valutare le applicazioni del tipo di applicazione specificato. La mappa dei criteri di integrità dei tipi di applicazioni può essere usata durante la valutazione dell'integrità del cluster per descrivere i tipi di applicazioni speciali. I tipi di applicazioni inclusi nella mappa vengono valutati rispetto alla percentuale specificata nella mappa, non con il valore MaxPercentUnhealthyApplications globale definito nei criteri di integrità del cluster. Le applicazioni dei tipi specificati nella mappa non vengono conteggiate nel pool globale di applicazioni. Ad esempio, se alcune applicazioni di un tipo sono di importanza critica, l'amministratore del cluster può aggiungere una voce alla mappa per tale tipo di applicazioni e assegnarle un valore pari a 0% (ovvero, non tollerare alcun errore). Tutte le altre applicazioni possono essere valutate con MaxPercentUnhealthyApplications impostato sul 20%, in modo da tollerare alcuni errori dalle migliaia di istanze dell'applicazione. La mappa dei criteri di integrità dei tipi di applicazioni viene usata solo se il manifesto del cluster consente la valutazione dell'integrità dei tipi di applicazioni usando la voce di configurazione per HealthManager/EnableApplicationTypeHealthEvaluation.|
|--chaos-target-filter         | Dizionario con codifica JSON con due chiavi di tipo stringa. Le due chiavi sono NodeTypeInclusionList e ApplicationInclusionList. I valori per entrambe queste chiavi sono un elenco di stringhe. chaos_target_filter definisce tutti i filtri per gli errori Chaos di destinazione, ad esempio generando errori solo per alcuni tipi di nodi o solo per determinate applicazioni. Se non si usa chaos_target_filter, gli errori Chaos interessano tutte le entità del cluster. Se si usa chaos_target_filter, gli errori Chaos interessano solo le entità che soddisfano le specifiche di chaos_target_filter. NodeTypeInclusionList e ApplicationInclusionList consentono solo una semantica di unione. Non è possibile specificare un'intersezione tra NodeTypeInclusionList e ApplicationInclusionList. Non è ad esempio possibile specificare "genera un errore in questa applicazione solo quando si trova in quel tipo di nodo". Dopo che un'entità è stata inclusa in NodeTypeInclusionList o in ApplicationInclusionList, tale entità non può essere esclusa tramite ChaosTargetFilter. Anche se in ApplicationInclusionList non compare applicationX, in alcune iterazioni di Chaos applicationX può presentare un errore, perché si trova per caso in un nodo di tipo nodeTypeY incluso in NodeTypeInclusionList. Se NodeTypeInclusionList e ApplicationInclusionList sono entrambi vuoti, viene generata un'eccezione ArgumentException. Tutti i tipi di errore (riavvio di nodo, riavvio di pacchetto di codice, rimozione di replica, riavvio di replica, spostamento di replica primaria e spostamento di replica secondaria) sono abilitati per i nodi di questi tipi di nodo. Se un tipo di nodo (ad esempio NodeTypeX) non compare in NodeTypeInclusionList, gli errori a livello di nodo (ad esempio NodeRestart) non verranno mai abilitati per i nodi di tipo NodeTypeX. Gli errori di pacchetto di codice e di replica, tuttavia, possono comunque essere abilitati per NodeTypeX se un'applicazione in ApplicationInclusionList si trova in un nodo di tipo NodeTypeX. In questo elenco possono essere inclusi al massimo 100 tipi di nodo. Per aumentare questo numero, è necessario un aggiornamento della configurazione di MaxNumberOfNodeTypesInChaosEntityFilter. Tutte le repliche appartenenti ai servizi di queste applicazioni sono soggette a errori di replica (riavvio di replica, rimozione di replica, spostamento di replica primaria, spostamento di replica secondaria) generati da Chaos. Chaos può riavviare un pacchetto di codice solo se quest'ultimo ospita solo repliche di queste applicazioni. Se un'applicazione non compare in questo elenco, può comunque presentare errori in alcune iterazioni di Chaos se l'applicazione finisce per trovarsi in un nodo di un tipo incluso in NodeTypeInclusionList. Se tuttavia applicationX è associata a nodeTypeY tramite vincoli di posizionamento, applicationX non è presente in ApplicationInclusionList e nodeTypeY non è presente in NodeTypeInclusionList, applicationX non presenterà mai errori. In questo elenco possono essere inclusi al massimo 1000 nomi di applicazione. Per aumentare questo numero, è necessario un aggiornamento della configurazione di MaxNumberOfApplicationsInChaosEntityFilter.|
|--context                     | Mappa con codifica JSON di coppie chiave-valore di tipo (string, string). La mappa può essere usata per registrare le informazioni sull'esecuzione di Chaos. Non possono esserci più di 100 coppie di questo tipo e ogni stringa (chiave o valore) può essere costituita da un massimo di 4095 caratteri. Questa mappa viene impostata dalla funzione di avvio dell'esecuzione di Chaos per l'archiviazione facoltativa del contesto dell'esecuzione specifica.|
| --disable-move-replica-faults | Disabilita lo spostamento primario e sposta gli errori secondari.|
| --max-cluster-stabilization| Il tempo di attesa massimo affinché tutte le entità del cluster diventino stabili e integre.  Predefinito: 60. Chaos viene eseguito secondo iterazioni e all'inizio di ogni iterazione convalida l'integrità delle entità del cluster. Se durante la convalida un'entità del cluster non risulta stabile e integra entro il periodo di tempo specificato da MaxClusterStabilizationTimeoutInSeconds, Chaos genera un evento di convalida non riuscita.|
| --max-concurrent-faults    | Il numero massimo di errori simultanei indotti per ogni iterazione.           Impostazione predefinita: 1. Chaos viene eseguito secondo iterazioni e due iterazioni consecutive sono separate da una fase di convalida. Maggiore è la concorrenza, più aggressivo è l'inserimento degli errori, generando serie più complesse di stati per il rilevamento dei bug. È consigliabile iniziare con un valore di 2 o 3 e prestare attenzione durante l'incremento del valore.|
| --max-percent-unhealthy-apps  | Quando si esegue la valutazione dell'integrità del cluster durante l'esecuzione di Chaos, la percentuale massima consentita di applicazioni non integre prima che venga segnalato un errore. Percentuale massima consentita di applicazioni non integre prima che venga segnalato un errore. Ad esempio, per consentire il 10% di applicazioni non integre, questo valore deve corrispondere a 10. La percentuale rappresenta la percentuale massima tollerata di applicazioni che possono risultare non integre prima che per il cluster venga impostato lo stato Error. Se la percentuale viene rispettata ma esiste almeno un'applicazione non integra, l'integrità viene valutata come Avviso. Questa viene calcolata dividendo il numero di applicazioni non integre rispetto al numero totale di istanze dell'applicazione nel cluster, escluse le applicazioni di tipi inclusi nel parametro ApplicationTypeHealthPolicyMap. Il calcolo viene arrotondato per eccesso per tollerare un errore su un numero limitato di applicazioni. La percentuale predefinita è zero.|
| --max-percent-unhealthy-nodes | Quando si esegue la valutazione dell'integrità del cluster durante l'esecuzione di Chaos, la percentuale massima consentita di nodi non integri prima che venga segnalato un errore. Percentuale massima consentita di nodi non integri prima che venga segnalato un errore. Ad esempio, per consentire il 10% di nodi non integri, questo valore deve corrispondere a 10. La percentuale rappresenta la percentuale massima tollerata di nodi che possono risultare non integri prima che per il cluster venga impostato lo stato Error. Se la percentuale viene rispettata ma esiste almeno un nodo non integro, l'integrità viene valutata come Avviso. Tale percentuale viene calcolata dividendo il numero dei nodi non integri per il numero totale di nodi nel cluster. Il calcolo viene arrotondato per eccesso per tollerare un errore su un numero limitato di nodi. La percentuale predefinita è zero. Questa percentuale dovrà essere configurata in modo da tenere conto del fatto che in cluster di grandi dimensioni sono sempre presenti nodi inattivi o in fase di riparazione.|
| --time-to-run              | Tempo totale, in secondi, in cui Chaos verrà eseguito prima di interrompersi automaticamente. Il valore massimo consentito è 4.294.967.295 (System.UInt32.MaxValue).  Valore predefinito: 4294967295.|
| --timeout -t               | Timeout del server in secondi.  Predefinito: 60.|
| --wait-time-between-faults | Tempo di attesa, in secondi, tra errori consecutivi all'interno di una singola iterazione.  Valore predefinito: 20. Maggiore è il valore, minore è la sovrapposizione tra gli errori e più semplice la sequenza delle transizioni di stato che attraversa il cluster. È consigliabile iniziare con un valore compreso tra 1 e 5 e prestare attenzione durante l'incremento del valore.|
| --wait-time-between-iterations| Intervallo di tempo, in secondi, tra due iterazioni consecutive di Chaos. Maggiore è il valore, minore è la frequenza di inserimento degli errori. Valore predefinito: 30.|
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

|Argomento|DESCRIZIONE|
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