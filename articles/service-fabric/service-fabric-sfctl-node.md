---
title: Interfaccia della riga di comando di Azure Service Fabric - nodo sfctlAzure Service Fabric CLI- sfctl node
description: Informazioni su sfctl, l'interfaccia della riga di comando di Azure Service Fabric.Learn about sfctl, the Azure Service Fabric command line interface. Include un elenco di comandi per la gestione dei nodi del cluster.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 5881e6485003abd4fd23a7f6d06a428e768c00fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905872"
---
# <a name="sfctl-node"></a>sfctl node
Consente di gestire i nodi che formano un cluster.

## <a name="commands"></a>Comandi:

|Comando|Descrizione|
| --- | --- |
| add-configuration-parameter-overrides | Aggiunge l'elenco delle sostituzioni di configurazione nel nodo specificato. |
| disabilitare | Disattiva un nodo del cluster di Service Fabric con lo scopo di disattivazione specificato. |
| abilitare | Attiva un nodo del cluster di Service Fabric che è attualmente disattivato. |
| get-configuration-overrides | Ottiene l'elenco di override di configurazione nel nodo specificato. |
| health | Mostra l'integrità di un nodo di Service Fabric. |
| info | Mostra le informazioni su un nodo specifico del cluster di Service Fabric. |
| list | Mostra l'elenco dei nodi del cluster di Service Fabric. |
| load | Mostra le informazioni sul caricamento di un nodo di Service Fabric. |
| remove-configuration-overrides | Rimuove le sostituzioni di configurazione nel nodo specificato. |
| remove-state | Notifica a Service Fabric che lo stato persistente in un nodo è stato rimosso o perso definitivamente. |
| report-health | Invia un report di integrità nel nodo di Service Fabric. |
| restart | Riavvia un nodo del cluster di Service Fabric. |
| transition | Avvia o arresta un nodo del cluster. |
| transition-status | Mostra lo stato di avanzamento di un'operazione avviata utilizzando StartNodeTransition. |

## <a name="sfctl-node-add-configuration-parameter-overrides"></a>Sfctl nodo add-configuration-parameter-overrides
Aggiunge l'elenco delle sostituzioni di configurazione nel nodo specificato.

Questa API consente di aggiungere tutte le sostituzioni di configurazione esistenti nel nodo specificato.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --config-parameter-override-list [Obbligatorio] | Descrizione per l'aggiunta di un elenco di sostituzioni di configurazione. |
| --node-name [Obbligatorio] | Il nome del nodo. |
| --force | Forzare l'aggiunta di sostituzioni di configurazione nei nodi specificati. |
| --timeout -t | Timeout del server per l'esecuzione dell'operazione in secondi. Questo timeout specifica il periodo di tempo durante il quale il client è disposto ad attendere il completamento dell'operazione richiesta. Il valore predefinito per questo parametro è 60 secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio della registrazione per mostrare tutti i log di debug. |
| --help -h | Visualizza questo messaggio della guida ed esce. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio della registrazione. Usare --debug per log di debug completi. |

## <a name="sfctl-node-disable"></a>sfctl node disable
Disattiva un nodo del cluster di Service Fabric con lo scopo di disattivazione specificato.

Disattiva un nodo del cluster di Service Fabric con lo scopo di disattivazione specificato. Quando la disattivazione è in corso, lo scopo di disattivazione può essere aumentato, ma non ridotto. Ad esempio, un nodo che viene disattivato con lo scopo di sospensione può essere disattivato ulteriormente con Riavvio, ma non viceversa. I nodi possono essere riattivati usando l'operazione Attiva un nodi in qualsiasi momento dopo la disattivazione. Se la disattivazione non è completa, verrà annullata. Un nodo che si arresta e torna disponibile mentre è disattivato dovrà essere riattivato prima che vengano inseriti servizi in tale nodo.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --node-name [Required] | Il nome del nodo. |
| --deactivation-intent | Viene descritto lo scopo o il motivo per la disattivazione del nodo. I valori possibili sono i seguenti. |
| --timeout -t | Timeout del server per l'esecuzione dell'operazione in secondi. Questo timeout specifica il periodo di tempo durante il quale il client è disposto ad attendere il completamento dell'operazione richiesta. Il valore predefinito per questo parametro è 60 secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio della registrazione per mostrare tutti i log di debug. |
| --help -h | Visualizza questo messaggio della guida ed esce. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio della registrazione. Usare --debug per log di debug completi. |

## <a name="sfctl-node-enable"></a>sfctl node enable
Attiva un nodo del cluster di Service Fabric che è attualmente disattivato.

Attiva un nodo del cluster di Service Fabric che è attualmente disattivato. Dopo essere stato attivato, il nodo diventerà nuovamente una destinazione valida in cui inserire le nuove repliche e tutte le repliche disattivate rimanenti nel nodo verranno riattivate.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --node-name [Required] | Il nome del nodo. |
| --timeout -t | Timeout del server per l'esecuzione dell'operazione in secondi. Questo timeout specifica il periodo di tempo durante il quale il client è disposto ad attendere il completamento dell'operazione richiesta. Il valore predefinito per questo parametro è 60 secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio della registrazione per mostrare tutti i log di debug. |
| --help -h | Visualizza questo messaggio della guida ed esce. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio della registrazione. Usare --debug per log di debug completi. |

## <a name="sfctl-node-get-configuration-overrides"></a>Sfctl nodo get-configuration-overrides
Ottiene l'elenco di override di configurazione nel nodo specificato.

Questa API consente di ottenere tutte le sostituzioni di configurazione esistenti nel nodo specificato.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --node-name [Required] | Il nome del nodo. |
| --timeout -t | Timeout del server per l'esecuzione dell'operazione in secondi. Questo timeout specifica il periodo di tempo durante il quale il client è disposto ad attendere il completamento dell'operazione richiesta. Il valore predefinito per questo parametro è 60 secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio della registrazione per mostrare tutti i log di debug. |
| --help -h | Visualizza questo messaggio della guida ed esce. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio della registrazione. Usare --debug per log di debug completi. |

## <a name="sfctl-node-health"></a>sfctl node health
Mostra l'integrità di un nodo di Service Fabric.

Mostra l'integrità di un nodo di Service Fabric. Utilizzare EventsHealthStateFilter per filtrare la raccolta di eventi di stato riportati nel nodo in base allo stato di integrità. Se il nodo di cui si specifica il nome non esiste nell'archivio integrità, questo restituisce un errore.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --node-name [Obbligatorio] | Il nome del nodo. |
| --events-health-state-filter | Consente di filtrare la raccolta di oggetti HealthEvent restituiti in base allo stato di integrità. I valori possibili per questo parametro includono il valore intero di uno dei seguenti stati di integrità. Vengono restituiti solo gli eventi che corrispondono al filtro. Tutti gli eventi vengono usati per valutare lo stato di integrità aggregato. Se non specificato diversamente, vengono restituite tutte le voci. I valori dello stato sono enumerati in base al flag, pertanto il valore potrebbe essere una combinazione di questi valori ottenuti usando l'operatore "OR" bit per bit. Ad esempio, se il valore fornito è 6, vengono restituiti tutti gli eventi con valore dello stato di integrità OK (2) e di Avviso (4).  <br> - Default - Valore predefinito. Consente di ricercare qualsiasi stato di integrità. Il valore predefinito è zero.  <br> - None - Il filtro non corrisponde ad alcun valore di stato di integrità. Usato per non restituire alcun risultato in un determinato insieme di stati. Il valore è uguale a 1.  <br> -Ok - Filtro che ricerca le corrispondenze di input con valore di stato di integrità Ok. Il valore è 2.  <br> -Warning - filtro che ricerca le corrispondenze di input con valore di stato di integrità Avviso. Il valore è 4.  <br> - Error - Filtro che ricerca le corrispondenze di input con valore di stato di integrità Errore. Il valore è 8.  <br> -All - Filtro che ricerca le corrispondenze di input con qualsiasi valore di stato di integrità. Il valore è 65535. |
| --timeout -t | Timeout del server per l'esecuzione dell'operazione in secondi. Questo timeout specifica il periodo di tempo durante il quale il client è disposto ad attendere il completamento dell'operazione richiesta. Il valore predefinito per questo parametro è 60 secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio della registrazione per mostrare tutti i log di debug. |
| --help -h | Visualizza questo messaggio della guida ed esce. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio della registrazione. Usare --debug per log di debug completi. |

## <a name="sfctl-node-info"></a>sfctl node info
Mostra le informazioni su un nodo specifico del cluster di Service Fabric.

La risposta include il nome, lo stato, l’ID, l’integrità, il tempo di attività e altri dettagli relativi al nodo.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --node-name [Required] | Il nome del nodo. |
| --timeout -t | Timeout del server per l'esecuzione dell'operazione in secondi. Questo timeout specifica il periodo di tempo durante il quale il client è disposto ad attendere il completamento dell'operazione richiesta. Il valore predefinito per questo parametro è 60 secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio della registrazione per mostrare tutti i log di debug. |
| --help -h | Visualizza questo messaggio della guida ed esce. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio della registrazione. Usare --debug per log di debug completi. |

## <a name="sfctl-node-list"></a>sfctl node list
Mostra l'elenco dei nodi del cluster di Service Fabric.

La risposta include il nome, lo stato, l'ID, l'integrità, il tempo di attività e altri dettagli relativi ai nodi.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --continuation-token | Il parametro del token di continuazione viene utilizzato per ottenere il set di risultati successivo. Un token di continuazione con un valore non vuoto è incluso nella risposta dell'API quando i risultati del sistema non rientrano in una singola risposta. Quando questo valore viene passato alla successiva chiamata API, l'API restituisce il set di risultati successivo. Se non sono presenti altri risultati, il token di continuazione non contiene alcun valore. Il valore di questo parametro non deve essere codificato in URL. |
| --max-results | Il numero massimo di risultati che devono essere restituiti come parte delle query di paging. Questo parametro definisce il limite massimo di risultati restituiti. Se non rientrano nel messaggio in base ai limiti di dimensione massima per i messaggi definiti nella configurazione, il numero dei risultati restituiti può essere inferiore al numero massimo di risultati specificato. Se questo parametro è uguale a zero o non specificato, le query di paging includono il numero massimo di risultati possibili che rientrano nel messaggio restituito. |
| --node-status-filter | Consente di filtrare i nodi in base allo stato del nodo. Verranno restituiti solo i nodi che corrispondono al valore di filtro specificato. Il valore del filtro può essere uno dei seguenti.  Impostazione predefinita\: default. |
| --timeout -t | Timeout del server per l'esecuzione dell'operazione in secondi. Questo timeout specifica il periodo di tempo durante il quale il client è disposto ad attendere il completamento dell'operazione richiesta. Il valore predefinito per questo parametro è 60 secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio della registrazione per mostrare tutti i log di debug. |
| --help -h | Visualizza questo messaggio della guida ed esce. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio della registrazione. Usare --debug per log di debug completi. |

## <a name="sfctl-node-load"></a>sfctl node load
Mostra le informazioni sul caricamento di un nodo di Service Fabric.

Recupera le informazioni sul caricamento di un nodo di Service Fabric per tutte le metriche per cui sono definiti il caricamento o la capacità.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --node-name [Required] | Il nome del nodo. |
| --timeout -t | Timeout del server per l'esecuzione dell'operazione in secondi. Questo timeout specifica il periodo di tempo durante il quale il client è disposto ad attendere il completamento dell'operazione richiesta. Il valore predefinito per questo parametro è 60 secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio della registrazione per mostrare tutti i log di debug. |
| --help -h | Visualizza questo messaggio della guida ed esce. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio della registrazione. Usare --debug per log di debug completi. |

## <a name="sfctl-node-remove-configuration-overrides"></a>Sfctl nodo remove-configuration-overrides
Rimuove le sostituzioni di configurazione nel nodo specificato.

Questa API consente di rimuovere tutte le sostituzioni di configurazione esistenti nel nodo specificato.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --node-name [Required] | Il nome del nodo. |
| --timeout -t | Timeout del server per l'esecuzione dell'operazione in secondi. Questo timeout specifica il periodo di tempo durante il quale il client è disposto ad attendere il completamento dell'operazione richiesta. Il valore predefinito per questo parametro è 60 secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio della registrazione per mostrare tutti i log di debug. |
| --help -h | Visualizza questo messaggio della guida ed esce. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio della registrazione. Usare --debug per log di debug completi. |

## <a name="sfctl-node-remove-state"></a>sfctl node remove-state
Notifica a Service Fabric che lo stato persistente in un nodo è stato rimosso o perso definitivamente.

Ciò implica che non è possibile ripristinare lo stato persistente di tale nodo, come in genere si verifica se il contenuto di un disco rigido è stato cancellato o se un disco rigido si arresta in modo anomalo. Il nodo deve essere inattivo perché l'operazione abbia esito positivo. Questa operazione consente a Service Fabric di sapere che le repliche in tale nodo non esistono più e che deve smettere di attendere che tali repliche tornino disponibili. Non eseguire questo cmdlet se lo stato del nodo non è stato rimosso e il nodo può tornare disponibile con lo stato intatto. A partire da Service Fabric 6.5, per usare questa API per i nodi di cui, modificare i nodi di cui in seguito in nodi normali (non seme) e quindi richiamare questa API per rimuovere lo stato del nodo. Se il cluster è in esecuzione in Azure, dopo l'arresto del nodo di serie, Service Fabric tenterà di modificarlo automaticamente in un nodo non di cui è stato eseguito il seme. A tale scopo, assicurarsi che il numero di nodi non di cui non si esegue il seeding nel tipo di nodo primario non sia inferiore al numero di nodi di cui al di livello inferiore. Se necessario, aggiungere più nodi al tipo di nodo primario per ottenere questo risultato. Per il cluster autonomo, se non è previsto che il nodo Down seed venga ripristinato\:con lo stato intatto, rimuovere il nodo dal cluster, vedere https //docs.microsoft.com/en-us/azure/service-fabric/service-fabric-cluster-windows-server-add-remove-nodes.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --node-name [Required] | Il nome del nodo. |
| --timeout -t | Timeout del server per l'esecuzione dell'operazione in secondi. Questo timeout specifica il periodo di tempo durante il quale il client è disposto ad attendere il completamento dell'operazione richiesta. Il valore predefinito per questo parametro è 60 secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio della registrazione per mostrare tutti i log di debug. |
| --help -h | Visualizza questo messaggio della guida ed esce. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio della registrazione. Usare --debug per log di debug completi. |

## <a name="sfctl-node-report-health"></a>sfctl node report-health
Invia un report di integrità nel nodo di Service Fabric.

Segnala lo stato di integrità del nodo di Service Fabric specificato. Il report deve contenere le informazioni relative all'origine del report sull'integrità e alla proprietà in cui viene segnalato. Il report viene inviato a un nodo del gateway di Service Fabric, che lo inoltra all'archivio integrità. Il report può essere accettato dal gateway, ma rifiutato dall'archivio integrità dopo la convalida aggiuntiva. L'archivio integrità, ad esempio, può rifiutare il report a causa di un parametro non valido, come un numero di sequenza non aggiornato. Per determinare se il report è stato applicato nell'archivio integrità, controllare che il report venga visualizzato nella sezione HealthEvents.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --health-property [obbligatorio] | Proprietà delle informazioni sull'integrità. <br><br> Un'entità può avere report sull'integrità per proprietà diverse. La proprietà è una stringa e non un'enumerazione fissa, che assicura al reporter la flessibilità necessaria per definire la categoria della condizione di stato che attiva il report. Ad esempio, un reporter con SourceId "LocalWatchdog" può monitorare lo stato del disco disponibile in un nodo e può quindi segnalare la proprietà "AvailableDisk" in tale nodo. Lo stesso reporter può monitorare la connettività del nodo e può quindi segnalare una proprietà "Connectivity" nello stesso nodo. Entrambi questi report vengono considerati come eventi di integrità separati nell'archivio integrità per il nodo specificato. Insieme a SourceId, la proprietà identifica in modo univoco le informazioni sull'integrità. |
| --health-state    [obbligatorio] | I valori possibili sono\: "Invalid", "Ok", "Warning", "Error", "Unknown". |
| --node-name [Obbligatorio] | Il nome del nodo. |
| --source-id       [obbligatorio] | Nome di origine che identifica il componente client/watchdog/sistema che ha generato le informazioni sull'integrità. |
| --description | Descrizione delle informazioni sull'integrità. <br><br> Rappresenta il testo libero usato per aggiungere informazioni leggibili sul report. La lunghezza massima della stringa per la descrizione è pari a 4096 caratteri. Se la stringa immessa è più lunga, verrà automaticamente troncata. Quando viene troncata, gli ultimi caratteri della descrizione contengono un marcatore "[Truncated]" e la dimensione totale della stringa è pari a 4096 caratteri. La presenza del marcatore indica agli utenti che si è verificato un troncamento. Si noti che quando viene troncata, la descrizione contiene meno di 4096 caratteri della stringa originale. |
| --immediate | Flag che indica se il report deve essere inviato immediatamente. <br><br> Un report sull'integrità viene inviato a un'applicazione del gateway di Service Fabric, che lo inoltra all'archivio integrità. Se Immediate è impostato su True, il report viene inviato immediatamente dal gateway HTTP all'archivio integrità, indipendentemente dalle impostazioni del client Fabric usate dall'applicazione gateway HTTP. Questo è utile per i report critici che devono essere inviati appena possibile. A seconda della tempistica e di altre condizioni, l'invio del report può tuttavia avere esito negativo, ad esempio se il gateway HTTP è chiuso o il messaggio non raggiunge il gateway. Se Immediate è impostato su False, il report viene inviato in base alle impostazioni del client di integrità dal gateway HTTP. Verrà quindi inviato in batch in base alla configurazione di HealthReportSendInterval. Questa è l'impostazione consigliata perché consente al client di integrità di ottimizzare la segnalazione di messaggi sull'integrità all'archivio integrità e l'elaborazione di report sull'integrità. Per impostazione predefinita, i report non vengono inviati immediatamente. |
| --remove-when-expired | Valore che indica se il report viene rimosso dall'archivio integrità quando scade. <br><br> Se impostato su True, il report viene rimosso dall'archivio integrità dopo la scadenza. Se impostato su False, il report viene considerato come errore quando è scaduto. Il valore di questa proprietà è False per impostazione predefinita. Quando i client creano report periodicamente, RemoveWhenExpired sarà impostato su False (valore predefinito). In questo modo, se il reporter presenta problemi (ad esempio, un deadlock) e non può creare report, l'entità viene considerata in stato di errore quando scade il report sull'integrità. L'entità viene quindi contrassegnata con lo stato di integrità di errore. |
| --sequence-number | Numero di sequenza per questo report sull'integrità come stringa numerica. <br><br> Il numero di sequenza del report viene usato dall'archivio integrità per rilevare i report non aggiornati. Se non specificato, un numero di sequenza viene generato automaticamente dal client di integrità quando viene aggiunto un report. |
| --timeout -t | Impostazione predefinita\: 60. |
| --ttl | Durata in cui questo report sull'integrità è valido. Questo campo usa il formato ISO8601 per specificare la durata. <br><br> Quando i client creano report periodicamente, devono inviare i report con una frequenza maggiore della durata (TTL). Se i client inviano report in caso di transizione, possono impostare la durata (TTL) come infinita. Quando la durata (TTL) scade, l'evento di integrità che contiene le informazioni sull'integrità viene rimosso dall'archivio integrità, se RemoveWhenExpired è True, o considerato in stato di errore, se RemoveWhenExpired è False. Se non è specificato, la durata (TTL) viene impostata su un valore infinito. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio della registrazione per mostrare tutti i log di debug. |
| --help -h | Visualizza questo messaggio della guida ed esce. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio della registrazione. Usare --debug per log di debug completi. |

## <a name="sfctl-node-restart"></a>sfctl node restart
Riavvia un nodo del cluster di Service Fabric.

Riavvia un nodo del cluster di Service Fabric che è già stato avviato.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --node-name [Required] | Il nome del nodo. |
| --create-fabric-dump | Specificare True per creare un dump del processo del nodo di infrastruttura. Questo valore fa distinzione tra maiuscole e minuscole.  Impostazione predefinita\: false. |
| --node-instance-id | L'ID di istanza del nodo di destinazione. Se l'ID di istanza viene specificato, il nodo viene riavviato solo se corrisponde all'istanza corrente del nodo. Un valore predefinito "0" corrisponde a qualsiasi ID dell'istanza. L'ID dell'istanza può essere ottenuto usando la query get node.  Impostazione predefinita\: 0. |
| --timeout -t | Timeout del server per l'esecuzione dell'operazione in secondi. Questo timeout specifica il periodo di tempo durante il quale il client è disposto ad attendere il completamento dell'operazione richiesta. Il valore predefinito per questo parametro è 60 secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio della registrazione per mostrare tutti i log di debug. |
| --help -h | Visualizza questo messaggio della guida ed esce. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio della registrazione. Usare --debug per log di debug completi. |

## <a name="sfctl-node-transition"></a>sfctl node transition
Avvia o arresta un nodo del cluster.

Avvia o arresta un nodo del cluster.  Un nodo del cluster è un processo, non l’istanza del sistema operativo.  Per avviare un nodo, passare a "Start" per il parametro NodeTransitionType. Per arrestare un nodo, passare a "Stop" per il parametro NodeTransitionType. Questa API avvia l'operazione; quando l'API ritorna, il nodo può essere ancora in fase di transizione. Richiamare GetNodeTransitionProgress con lo stesso OperationId per ottenere lo stato di avanzamento dell'operazione.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --node-instance-id         [Obbligatorio] | L'ID di istanza del nodo di destinazione. Ciò può essere determinato tramite l'API GetNodeInfo. |
| --node-name                [Obbligatorio] | Il nome del nodo. |
| --node-transition-type     [Obbligatorio] | Indica il tipo di transizione da eseguire.  NodeTransitionType.Start avvierà un nodo arrestato. NodeTransitionType.Stop arresterà un nodo che è attivo. |
| --operation-id             [Obbligatorio] | Una GUID che identifica una chiamata dell'API.  Viene passato all'API GetProgress corrispondente. |
| --stop-duration-in-seconds [Obbligatorio] | La durata, in secondi, di mantenimento del nodo arrestato.  Il valore minimo è 600, quello massimo 14400.  Trascorso questo tempo, il nodo tornerà automaticamente allo stato attivo. |
| --timeout -t | Timeout del server per l'esecuzione dell'operazione in secondi. Questo timeout specifica il periodo di tempo durante il quale il client è disposto ad attendere il completamento dell'operazione richiesta. Il valore predefinito per questo parametro è 60 secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio della registrazione per mostrare tutti i log di debug. |
| --help -h | Visualizza questo messaggio della guida ed esce. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio della registrazione. Usare --debug per log di debug completi. |

## <a name="sfctl-node-transition-status"></a>sfctl node transition-status
Mostra lo stato di avanzamento di un'operazione avviata utilizzando StartNodeTransition.

Ottiene lo stato di avanzamento di un'operazione avviata con StartNodeTransition usando l'ID operazione specificato.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --node-name [Obbligatorio] | Il nome del nodo. |
| --operation-id [obbligatorio] | Una GUID che identifica una chiamata dell'API.  Viene passato all'API GetProgress corrispondente. |
| --timeout -t | Timeout del server per l'esecuzione dell'operazione in secondi. Questo timeout specifica il periodo di tempo durante il quale il client è disposto ad attendere il completamento dell'operazione richiesta. Il valore predefinito per questo parametro è 60 secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio della registrazione per mostrare tutti i log di debug. |
| --help -h | Visualizza questo messaggio della guida ed esce. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio della registrazione. Usare --debug per log di debug completi. |


## <a name="next-steps"></a>Passaggi successivi
- [Configurare](service-fabric-cli.md) l'interfaccia della riga di comando di Service Fabric.
- Informazioni su come usare l'interfaccia della riga di comando Service Fabric usando gli [script di esempio](/azure/service-fabric/scripts/sfctl-upgrade-application).