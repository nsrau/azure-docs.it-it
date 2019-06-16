---
title: Interfaccia della riga di comando Azure Service Fabric - sfctl cluster | Microsoft Docs
description: Descrive i comandi dell'interfaccia della riga di comando Service Fabric sfctl cluster.
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
ms.openlocfilehash: 7bb399472d7e0ab14e6399fc8652d2eb132a866a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60837323"
---
# <a name="sfctl-cluster"></a>sfctl cluster
Selezionare, gestire e usare dei cluster di Service Fabric.

## <a name="commands"></a>Comandi:

|Comando|DESCRIZIONE|
| --- | --- |
| code-versions | Mostra un elenco di versioni del codice di un'infrastruttura di cui viene effettuato il provisioning in un cluster di Service Fabric. |
| config-versions | Mostra un elenco di versioni di configurazione di un'infrastruttura di cui viene effettuato il provisioning in un cluster di Service Fabric. |
| health | Mostra l'integrità di un cluster di Service Fabric. |
| manifest | Mostra il manifesto del cluster di Service Fabric. |
| operation-cancel | Annulla un'operazione di errore indotta dall'utente. |
| operation-list | Mostra un elenco di operazioni di errore indotte dall'utente filtrato in base all'input specificato. |
| provision | Esegue il provisioning dei pacchetti di codici o configurazioni di un cluster di Service Fabric. |
| recover-system | Indica al cluster di Service Fabric che deve provare a recuperare i servizi di sistema attualmente bloccati in una perdita di quorum. |
| report-health | Invia un report di integrità nel cluster di Service Fabric. |
| seleziona | Si connette a un endpoint di cluster di Service Fabric. |
| show-connection | Mostra i cluster di Service Fabric a cui è connessa l'istanza sfctl. |
| unprovision | Annulla il provisioning dei pacchetti di codici o configurazioni di un cluster di Service Fabric. |
| Aggiornamento | Avvia l'aggiornamento della versione codice o configurazione di un cluster di Service Fabric. |
| upgrade-resume | Consente all'aggiornamento del cluster di passare al dominio di aggiornamento successivo. |
| upgrade-rollback | Esegue il rollback dell'aggiornamento di un cluster di Service Fabric. |
| upgrade-status | Mostra lo stato di avanzamento dell'aggiornamento del cluster corrente. |
| upgrade-update | Aggiorna i parametri di aggiornamento di un cluster di Service Fabric. |

## <a name="sfctl-cluster-code-versions"></a>sfctl cluster code-versions
Mostra un elenco di versioni del codice di un'infrastruttura di cui viene effettuato il provisioning in un cluster di Service Fabric.

Mostra un elenco di informazioni sulle versioni del codice di un'infrastruttura di cui viene effettuato il provisioning nel cluster. Il parametro CodeVersion può essere usato per filtrare facoltativamente l'output solo in tale versione specifica.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --code-version | Versione del prodotto di Service Fabric. |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-cluster-config-versions"></a>sfctl cluster config-versions
Mostra un elenco di versioni di configurazione di un'infrastruttura di cui viene effettuato il provisioning in un cluster di Service Fabric.

Mostra un elenco di informazioni sulle versioni di configurazione di un'infrastruttura di cui viene effettuato il provisioning nel cluster. Il parametro ConfigVersion può essere usato per filtrare facoltativamente l'output solo in tale versione specifica.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --config-version | Versione di configurazione di Service Fabric. |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-cluster-health"></a>sfctl cluster health
Mostra l'integrità di un cluster di Service Fabric.

Usare EventsHealthStateFilter per filtrare la raccolta di eventi di stato riportati nel cluster in base allo stato di integrità. Analogamente, usare NodesHealthStateFilter e ApplicationsHealthStateFilter per filtrare la raccolta di nodi e applicazioni restituito in base al relativo stato di integrità aggregato.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --applications-health-state-filter | Consente di filtrare gli oggetti dello stato di integrità delle applicazioni restituiti nel risultato della query sull'integrità dei cluster in base al relativo stato di integrità. I valori possibili per questo parametro includono il valore intero ottenuto da membri o operazioni bit per bit sui membri dell'enumerazione HealthStateFilter. Vengono restituite solo le applicazioni che corrispondono al filtro. Tutte le applicazioni vengono usate per valutare lo stato di integrità aggregato. Se non specificato diversamente, vengono restituite tutte le voci. I valori dello stato sono enumerati in base al flag, pertanto il valore potrebbe essere una combinazione di questi valori ottenuti usando l'operatore "OR" bit per bit. Ad esempio, se il valore indicato è 6, viene restituito lo stato di integrità delle applicazioni il cui valore di HealthState è OK (2) e Warning (4).  <br> - Default - Valore predefinito. Consente di ricercare qualsiasi stato di integrità. Il valore predefinito è zero.  <br> - None - Il filtro non corrisponde ad alcun valore di stato di integrità. Usato per non restituire alcun risultato in un determinato insieme di stati. Il valore è uguale a 1.  <br> -Ok - Filtro che ricerca le corrispondenze di input con valore di stato di integrità Ok. Il valore è 2.  <br> -Warning - filtro che ricerca le corrispondenze di input con valore di stato di integrità Avviso. Il valore è 4.  <br> - Error - Filtro che ricerca le corrispondenze di input con valore di stato di integrità Errore. Il valore è 8.  <br> -All - Filtro che ricerca le corrispondenze di input con qualsiasi valore di stato di integrità. Il valore è 65535. |
| --events-health-state-filter | Consente di filtrare la raccolta di oggetti HealthEvent restituiti in base allo stato di integrità. I valori possibili per questo parametro includono il valore intero di uno dei seguenti stati di integrità. Vengono restituiti solo gli eventi che corrispondono al filtro. Tutti gli eventi vengono usati per valutare lo stato di integrità aggregato. Se non specificato diversamente, vengono restituite tutte le voci. I valori dello stato sono enumerati in base al flag, pertanto il valore potrebbe essere una combinazione di questi valori ottenuti usando l'operatore "OR" bit per bit. Ad esempio, se il valore fornito è 6, vengono restituiti tutti gli eventi con valore dello stato di integrità OK (2) e di Avviso (4).  <br> - Default - Valore predefinito. Consente di ricercare qualsiasi stato di integrità. Il valore predefinito è zero.  <br> - None - Il filtro non corrisponde ad alcun valore di stato di integrità. Usato per non restituire alcun risultato in un determinato insieme di stati. Il valore è uguale a 1.  <br> -Ok - Filtro che ricerca le corrispondenze di input con valore di stato di integrità Ok. Il valore è 2.  <br> -Warning - filtro che ricerca le corrispondenze di input con valore di stato di integrità Avviso. Il valore è 4.  <br> - Error - Filtro che ricerca le corrispondenze di input con valore di stato di integrità Errore. Il valore è 8.  <br> -All - Filtro che ricerca le corrispondenze di input con qualsiasi valore di stato di integrità. Il valore è 65535. |
| --exclude-health-statistics | Indica se le statistiche di integrità devono essere restituite come parte del risultato della query. False per impostazione predefinita. Le statistiche indicano il numero di entità figlio il cui stato di integrità è Ok, Avviso ed Errore. |
| --include-system-application-health-statistics | Indica se le statistiche di integrità devono includere le statistiche di integrità di sistema dell'applicazione fabric\:/System. False per impostazione predefinita. Se IncludeSystemApplicationHealthStatistics è impostato su true, le statistiche di integrità includono le entità che appartengono all'applicazione fabric\:/System. In caso contrario, il risultato della query includerà soltanto le statistiche di integrità per le applicazioni utente. Per applicare questo parametro, è necessario includere le statistiche di integrità nel risultato della query. |
| --nodes-health-state-filter | Consente di filtrare gli oggetti dello stato di integrità dei nodi restituiti nel risultato della query sull'integrità dei cluster in base al relativo stato di integrità. I valori possibili per questo parametro includono il valore intero di uno dei seguenti stati di integrità. Vengono restituiti solo i nodi che corrispondono al filtro. Tutti i nodi vengono usati per valutare lo stato di integrità aggregato. Se non specificato diversamente, vengono restituite tutte le voci. I valori dello stato sono enumerati in base al flag, pertanto il valore potrebbe essere una combinazione di questi valori ottenuti usando l'operatore "OR" bit per bit. Ad esempio, se il valore fornito è 6, viene restituito lo stato di integrità dei nodi con valore di HealthState OK (2) e Warning (4).  <br> - Default - Valore predefinito. Consente di ricercare qualsiasi stato di integrità. Il valore predefinito è zero.  <br> - None - Il filtro non corrisponde ad alcun valore di stato di integrità. Usato per non restituire alcun risultato in un determinato insieme di stati. Il valore è uguale a 1.  <br> -Ok - Filtro che ricerca le corrispondenze di input con valore di stato di integrità Ok. Il valore è 2.  <br> -Warning - filtro che ricerca le corrispondenze di input con valore di stato di integrità Avviso. Il valore è 4.  <br> - Error - Filtro che ricerca le corrispondenze di input con valore di stato di integrità Errore. Il valore è 8.  <br> -All - Filtro che ricerca le corrispondenze di input con qualsiasi valore di stato di integrità. Il valore è 65535. |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-cluster-manifest"></a>sfctl cluster manifest
Mostra il manifesto del cluster di Service Fabric.

Mostra il manifesto del cluster di Service Fabric. Il manifesto del cluster contiene le proprietà del cluster che includono i diversi tipi di nodi nel cluster, le configurazioni di sicurezza, gli errori e le topologie del dominio di aggiornamento e così via. Queste proprietà vengono specificate come parte del file ClusterConfig.JSON durante la distribuzione di un cluster autonomo. Tuttavia, la maggior parte delle informazioni contenute nel manifesto del cluster viene generata internamente da Service Fabric durante la distribuzione del cluster in altri scenari di distribuzione (ad esempio, quando si usa il portale di Azure). Il contenuto del manifesto del cluster è solo a scopo informativo; gli utenti non sono tenuti a usare una dipendenza dal formato del contenuto dei file o dalla loro interpretazione.

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

## <a name="sfctl-cluster-operation-cancel"></a>sfctl cluster operation-cancel
Annulla un'operazione di errore indotta dall'utente.

Le API seguenti avviano le operazioni di errore che possono essere annullate tramite CancelOperation\: StartDataLoss, StartQuorumLoss, StartPartitionRestart, StartNodeTransition. Se force è false, l'operazione specificata indotta dall'utente verrà normalmente arrestata e pulita.  Se force è true, il comando verrà interrotto e parte dello stato interno potrebbe permanere.  L'impostazione di force su true deve essere usata con cautela. La chiamata a questa API con force impostato su true è consentita solo se questa API è già stata chiamata prima sullo stesso comando di test con force impostato su false oppure se il valore di un elemento OperationState del comando di test è già OperationState.RollingBack. 

Chiarimento\: OperationState.RollingBack indica che il sistema eseguirà/esegue la pulizia dello stato del sistema interno in seguito all'esecuzione del comando.  Non ripristinerà i dati se il comando di test doveva causare la perdita dei dati.  Se ad esempio si chiama StartDataLoss, quindi si chiama questa API, il sistema pulirà lo stato interno solo a partire dall'esecuzione del comando. Non ripristinerà i dati della partizione di destinazione, se il comando è stato eseguito abbastanza a lungo da causare la perdita di dati. 

> [!NOTE]
> Se questa API viene richiamata con force==true, lo stato interno può permanere.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --operation-id [obbligatorio] | Una GUID che identifica una chiamata dell'API.  Viene passato all'API GetProgress corrispondente. |
| --force | Indica se eseguire normalmente il rollback e pulire lo stato di sistema interno modificato eseguendo l'operazione indotta dall'utente. |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-cluster-operation-list"></a>sfctl cluster operation-list
Mostra un elenco di operazioni di errore indotte dall'utente filtrato in base all'input specificato.

Mostra l'elenco di operazioni di errore indotte dall'utente filtrato in base all'input specificato.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --state-filter | Usato per filtrare le operazioni indotte dall'utente negli elementi OperationState. <br> 65535 - Seleziona tutto <br> 1 - Seleziona Running <br> 2 - Seleziona RollingBack <br>8 - Seleziona Completed <br>16 - Seleziona Faulted <br>32 - Seleziona Cancelled <br>64 - Seleziona ForceCancelled  <br>Impostazione predefinita\: 65535. |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |
| --type-filter | Usato per filtrare le operazioni indotte dall'utente negli elementi OperationType. <br> 65535 - Seleziona tutto <br> 1 - Seleziona PartitionDataLoss. <br> 2 - Seleziona PartitionQuorumLoss. <br> 4 - Seleziona PartitionRestart. <br> 8 - Seleziona NodeTransition.  <br> Impostazione predefinita\: 65535. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-cluster-provision"></a>sfctl cluster provision
Esegue il provisioning dei pacchetti di codici o configurazioni di un cluster di Service Fabric.

Esegue la convalida e il provisioning dei pacchetti di codici o configurazioni di un cluster di Service Fabric.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --cluster-manifest-file-path | Percorso file del manifesto del cluster. |
| --code-file-path | Percorso file del pacchetto di codice del cluster. |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-cluster-recover-system"></a>sfctl cluster recover-system
Indica al cluster di Service Fabric che deve provare a recuperare i servizi di sistema attualmente bloccati in una perdita di quorum.

Indica al cluster di Service Fabric che deve provare a recuperare i servizi di sistema attualmente bloccati in una perdita di quorum. Questa operazione può essere eseguita solo se è noto che non è possibile recuperare le repliche che non sono attive. Un uso non corretto di questa API può causare una perdita di dati.

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

## <a name="sfctl-cluster-report-health"></a>sfctl cluster report-health
Invia un report di integrità nel cluster di Service Fabric.

Il report deve contenere le informazioni relative all'origine del report sull'integrità e alla proprietà in cui viene segnalato. Il report viene inviato a un nodo del gateway di Service Fabric, che lo inoltra all'archivio integrità. Il report può essere accettato dal gateway, ma rifiutato dall'archivio integrità dopo la convalida aggiuntiva. L'archivio integrità, ad esempio, può rifiutare il report a causa di un parametro non valido, come un numero di sequenza non aggiornato. Per determinare se il report è stato applicato nell'archivio integrità, controllare che il report venga visualizzato nella sezione HealthEvents del cluster.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --health-property [obbligatorio] | Proprietà delle informazioni sull'integrità. <br><br> Un'entità può avere report sull'integrità per proprietà diverse. La proprietà è una stringa e non un'enumerazione fissa, che assicura al reporter la flessibilità necessaria per definire la categoria della condizione di stato che attiva il report. Ad esempio, un reporter con SourceId "LocalWatchdog" può monitorare lo stato del disco disponibile in un nodo e può quindi segnalare la proprietà "AvailableDisk" in tale nodo. Lo stesso reporter può monitorare la connettività del nodo e può quindi segnalare una proprietà "Connectivity" nello stesso nodo. Entrambi questi report vengono considerati come eventi di integrità separati nell'archivio integrità per il nodo specificato. Insieme a SourceId, la proprietà identifica in modo univoco le informazioni sull'integrità. |
| --health-state    [obbligatorio] | I valori possibili sono\: "Invalid", "Ok", "Warning", "Error", "Unknown". |
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

## <a name="sfctl-cluster-select"></a>sfctl cluster select
Si connette a un endpoint di cluster di Service Fabric.

In caso di connessione a un cluster sicuro, specificare un percorso assoluto a un certificato (CRT), un file di chiave (KEY) o un singolo file con entrambi (PEM). Non specificare entrambi. Facoltativamente, in caso di connessione a un cluster sicuro, specificare anche un percorso assoluto a un file di aggregazione CA o a una directory di certificati della CA attendibili. Se si usa una directory di certificati della CA, è prima necessario eseguire il comando `c_rehash <directory>` di OpenSSL per calcolare gli hash dei certificati e creare i collegamenti simbolici appropriati.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| -endpoint [obbligatorio] | URL dell'endpoint del cluster, incluso la porta e il prefisso HTTP o HTTPS. |
| --aad | Consente di usare l'autenticazione tramite Azure Active Directory. |
| --ca | Percorso assoluto alla directory contenente i certificati delle autorità di certificazione da considerare come validi o al file di aggregazione delle autorità di certificazione. |
| --cert | Percorso assoluto a un file di certificato client. |
| --key | Percorso assoluto al file della chiave del certificato client. |
| --no-verify | Disabilita la verifica dei certificati quando si usa il protocollo HTTPS. Nota\: si tratta di un'opzione non sicura, che non deve essere usata per gli ambienti di produzione. |
| --pem | Percorso assoluto a un certificato client, sotto forma di file con estensione PEM. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-cluster-show-connection"></a>sfctl-cluster-show-connection
Mostra i cluster di Service Fabric a cui è connessa l'istanza sfctl.

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-cluster-unprovision"></a>sfctl cluster unprovision
Annulla il provisioning dei pacchetti di codici o configurazioni di un cluster di Service Fabric.

È supportato per annullare il provisioning di codici e configurazioni separatamente.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --code-version | Versione del pacchetto di codice del cluster. |
| --config-version | Versione del manifesto del cluster. |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-cluster-upgrade"></a>sfctl cluster upgrade
Avvia l'aggiornamento della versione codice o configurazione di un cluster di Service Fabric.

Convalida i parametri di aggiornamento forniti e avvia l'aggiornamento della versione del codice o della configurazione di un cluster di Service Fabric se i parametri sono validi.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --app-health-map | Dizionario con codifica JSON delle coppie di nomi dell'applicazione e percentuale massima di stato non integro prima che venga generato l'errore. |
| --app-type-health-map | Dizionario con codifica JSON delle coppie di nomi del tipo di applicazione e percentuale massima di stato non integro prima che venga generato l'errore. |
| --code-version | Versione del codice del cluster. |
| --config-version | Versione della configurazione del cluster. |
| --delta-health-evaluation | Abilita la valutazione dell'integrità delta anziché la valutazione dell'integrità assoluta dopo il completamento di ogni dominio di aggiornamento. |
| --delta-unhealthy-nodes | Percentuale massima di riduzione dell'integrità dei nodi consentita durante gli aggiornamenti del cluster.  Impostazione predefinita\: 10. <br><br> Il valore delta è misurato tra lo stato dei nodi e all'inizio dell'aggiornamento e lo stato dei nodi al momento della valutazione dell'integrità. Il controllo viene eseguito dopo il completamento dell'aggiornamento di ciascun dominio di aggiornamento per assicurarsi che lo stato complessivo del cluster rientri nei limiti di tolleranza. |
| --failure-action | I valori possibili sono\: "Invalid", "Rollback", "Manual". |
| --force-restart | I processi vengono riavviati in modo forzato durante l'aggiornamento anche quando la versione del codice non è stata modificata. <br><br> L'aggiornamento modifica solo la configurazione o i dati. |
| --health-check-retry | Intervallo di tempo tra i tentativi di esecuzione dei controlli integrità se l'applicazione o il cluster non è integro. |
| --health-check-stable | Tempo di attesa per cui l'applicazione o il cluster devono rimanere integri prima di passare al dominio di aggiornamento successivo. <br><br> Viene prima interpretato come stringa che rappresenta una durata ISO 8601. Se l'esito è negativo, viene interpretato come numero che rappresenta il numero totale di millisecondi. |
| --health-check-wait | Intervallo di tempo di attesa dopo il completamento di un dominio di aggiornamento prima di avviare il processo dei controlli integrità. |
| --replica-set-check-timeout | Tempo massimo per bloccare l'elaborazione di un dominio di aggiornamento ed evitare la perdita di disponibilità quando si verificano problemi imprevisti. <br><br> Quando il timeout scade, l'elaborazione del dominio di aggiornamento procede indipendentemente dai problemi di perdita di disponibilità. Il timeout viene reimpostato all'inizio di ogni dominio di aggiornamento. I valori validi sono compresi tra 0 e 42949672925 inclusi. |
| --rolling-upgrade-mode | I valori possibili sono\: "Invalid", "UnmonitoredAuto", "UnmonitoredManual", "Monitored".  Impostazione predefinita\: UnmonitoredAuto. |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |
| --unhealthy-applications | Percentuale massima consentita di applicazioni non integre prima che venga segnalato un errore. <br><br> Ad esempio, per consentire il 10% di applicazioni non integre, questo valore deve corrispondere a 10. La percentuale rappresenta la percentuale massima tollerata di applicazioni che possono risultare non integre prima che per il cluster venga impostato lo stato Error. Se la percentuale viene rispettata ma esiste almeno un'applicazione non integra, l'integrità viene valutata come Avviso. Questa viene calcolata dividendo il numero di applicazioni non integre rispetto al numero totale di istanze dell'applicazione nel cluster, escluse le applicazioni di tipi inclusi nel parametro ApplicationTypeHealthPolicyMap. Il calcolo viene arrotondato per eccesso per tollerare un errore su un numero limitato di applicazioni. |
| --unhealthy-nodes | Percentuale massima consentita di nodi non integri prima che venga segnalato un errore. <br><br> Ad esempio, per consentire il 10% di nodi non integri, questo valore deve corrispondere a 10. La percentuale rappresenta la percentuale massima tollerata di nodi che possono risultare non integri prima che per il cluster venga impostato lo stato Error. Se la percentuale viene rispettata ma esiste almeno un nodo non integro, l'integrità viene valutata come Avviso. Tale percentuale viene calcolata dividendo il numero dei nodi non integri per il numero totale di nodi nel cluster. Il calcolo viene arrotondato per eccesso per tollerare un errore su un numero limitato di nodi. Questa percentuale dovrà essere configurata in modo da tenere conto del fatto che in cluster di grandi dimensioni sono sempre presenti nodi inattivi o in fase di riparazione. |
| --upgrade-domain-delta-unhealthy-nodes | Percentuale massima di riduzione dell'integrità dei nodi del dominio di aggiornamento consentita durante gli aggiornamenti del cluster.  Impostazione predefinita\: 15. <br><br> Il valore delta è misurato tra lo stato dei nodi di dominio all'inizio dell'aggiornamento e lo stato dei nodi di dominio al momento della valutazione dell'integrità. Il controllo viene eseguito dopo il completamento dell'aggiornamento di ciascun dominio di aggiornamento per tutti i domini di aggiornamento completati per assicurarsi che lo stato complessivo dei domini di aggiornamento rientri nei limiti di tolleranza. |
| --upgrade-domain-timeout | Tempo necessario al completamento di ogni dominio di aggiornamento prima dell'esecuzione di FailureAction. <br><br> Viene prima interpretato come stringa che rappresenta una durata ISO 8601. Se l'esito è negativo, viene interpretato come numero che rappresenta il numero totale di millisecondi. |
| --upgrade-timeout | Tempo necessario al completamento dell'aggiornamento prima dell'esecuzione di FailureAction. <br><br> Viene prima interpretato come stringa che rappresenta una durata ISO 8601. Se l'esito è negativo, viene interpretato come numero che rappresenta il numero totale di millisecondi. |
| --warning-as-error | Indica se gli avvisi vengono considerati con lo stesso livello di gravità degli errori. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-cluster-upgrade-resume"></a>sfctl cluster upgrade-resume
Consente all'aggiornamento del cluster di passare al dominio di aggiornamento successivo.

Consente all'aggiornamento del codice o della configurazione del cluster di passare al dominio di aggiornamento successivo, se appropriato.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --upgrade-domain [Obbligatorio] | Dominio di aggiornamento successivo per questo aggiornamento del cluster. |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-cluster-upgrade-rollback"></a>sfctl cluster upgrade-rollback
Esegue il rollback dell'aggiornamento di un cluster di Service Fabric.

Esegue il rollback dell'aggiornamento di codici o configurazioni di un cluster di Service Fabric.

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

## <a name="sfctl-cluster-upgrade-status"></a>sfctl cluster upgrade-status
Mostra lo stato di avanzamento dell'aggiornamento del cluster corrente.

Mostra lo stato di avanzamento corrente dell'aggiornamento del cluster in corso. Se attualmente non sono in corso aggiornamenti, ottenere lo stato più recente dell'aggiornamento del cluster precedente.

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

## <a name="sfctl-cluster-upgrade-update"></a>sfctl cluster upgrade-update
Aggiorna i parametri di aggiornamento di un cluster di Service Fabric.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --app-health-map | Dizionario con codifica JSON delle coppie di nomi dell'applicazione e percentuale massima di stato non integro prima che venga generato l'errore. |
| --app-type-health-map | Dizionario con codifica JSON delle coppie di nomi del tipo di applicazione e percentuale massima di stato non integro prima che venga generato l'errore. |
| --delta-health-evaluation | Abilita la valutazione dell'integrità delta anziché la valutazione dell'integrità assoluta dopo il completamento di ogni dominio di aggiornamento. |
| --delta-unhealthy-nodes | Percentuale massima di riduzione dell'integrità dei nodi consentita durante gli aggiornamenti del cluster.  Impostazione predefinita\: 10. <br><br> Il valore delta è misurato tra lo stato dei nodi e all'inizio dell'aggiornamento e lo stato dei nodi al momento della valutazione dell'integrità. Il controllo viene eseguito dopo il completamento dell'aggiornamento di ciascun dominio di aggiornamento per assicurarsi che lo stato complessivo del cluster rientri nei limiti di tolleranza. |
| --failure-action | I valori possibili sono\: "Invalid", "Rollback", "Manual". |
| --force-restart | I processi vengono riavviati in modo forzato durante l'aggiornamento anche quando la versione del codice non è stata modificata. <br><br> L'aggiornamento modifica solo la configurazione o i dati. |
| --health-check-retry | Intervallo di tempo tra i tentativi di esecuzione dei controlli integrità se l'applicazione o il cluster non è integro. |
| --health-check-stable | Tempo di attesa per cui l'applicazione o il cluster devono rimanere integri prima di passare al dominio di aggiornamento successivo. <br><br> Viene prima interpretato come stringa che rappresenta una durata ISO 8601. Se l'esito è negativo, viene interpretato come numero che rappresenta il numero totale di millisecondi. |
| --health-check-wait | Intervallo di tempo di attesa dopo il completamento di un dominio di aggiornamento prima di avviare il processo dei controlli integrità. |
| --replica-set-check-timeout | Tempo massimo per bloccare l'elaborazione di un dominio di aggiornamento ed evitare la perdita di disponibilità quando si verificano problemi imprevisti. <br><br> Quando il timeout scade, l'elaborazione del dominio di aggiornamento procede indipendentemente dai problemi di perdita di disponibilità. Il timeout viene reimpostato all'inizio di ogni dominio di aggiornamento. I valori validi sono compresi tra 0 e 42949672925 inclusi. |
| --rolling-upgrade-mode | I valori possibili sono\: "Invalid", "UnmonitoredAuto", "UnmonitoredManual", "Monitored".  Impostazione predefinita\: UnmonitoredAuto. |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |
| --unhealthy-applications | Percentuale massima consentita di applicazioni non integre prima che venga segnalato un errore. <br><br> Ad esempio, per consentire il 10% di applicazioni non integre, questo valore deve corrispondere a 10. La percentuale rappresenta la percentuale massima tollerata di applicazioni che possono risultare non integre prima che per il cluster venga impostato lo stato Error. Se la percentuale viene rispettata ma esiste almeno un'applicazione non integra, l'integrità viene valutata come Avviso. Questa viene calcolata dividendo il numero di applicazioni non integre rispetto al numero totale di istanze dell'applicazione nel cluster, escluse le applicazioni di tipi inclusi nel parametro ApplicationTypeHealthPolicyMap. Il calcolo viene arrotondato per eccesso per tollerare un errore su un numero limitato di applicazioni. |
| --unhealthy-nodes | Percentuale massima consentita di nodi non integri prima che venga segnalato un errore. <br><br> Ad esempio, per consentire il 10% di nodi non integri, questo valore deve corrispondere a 10. La percentuale rappresenta la percentuale massima tollerata di nodi che possono risultare non integri prima che per il cluster venga impostato lo stato Error. Se la percentuale viene rispettata ma esiste almeno un nodo non integro, l'integrità viene valutata come Avviso. Tale percentuale viene calcolata dividendo il numero dei nodi non integri per il numero totale di nodi nel cluster. Il calcolo viene arrotondato per eccesso per tollerare un errore su un numero limitato di nodi. Questa percentuale dovrà essere configurata in modo da tenere conto del fatto che in cluster di grandi dimensioni sono sempre presenti nodi inattivi o in fase di riparazione. |
| --upgrade-domain-delta-unhealthy-nodes | Percentuale massima di riduzione dell'integrità dei nodi del dominio di aggiornamento consentita durante gli aggiornamenti del cluster.  Impostazione predefinita\: 15. <br><br> Il valore delta è misurato tra lo stato dei nodi di dominio all'inizio dell'aggiornamento e lo stato dei nodi di dominio al momento della valutazione dell'integrità. Il controllo viene eseguito dopo il completamento dell'aggiornamento di ciascun dominio di aggiornamento per tutti i domini di aggiornamento completati per assicurarsi che lo stato complessivo dei domini di aggiornamento rientri nei limiti di tolleranza. |
| --upgrade-domain-timeout | Tempo necessario al completamento di ogni dominio di aggiornamento prima dell'esecuzione di FailureAction. <br><br> Viene prima interpretato come stringa che rappresenta una durata ISO 8601. Se l'esito è negativo, viene interpretato come numero che rappresenta il numero totale di millisecondi. |
| --upgrade-kind | I valori possibili includono\: "Invalid", "Rolling", "Rolling_ForceRestart".  Impostazione predefinita\: Rolling. |
| --upgrade-timeout | Tempo necessario al completamento dell'aggiornamento prima dell'esecuzione di FailureAction. <br><br> Viene prima interpretato come stringa che rappresenta una durata ISO 8601. Se l'esito è negativo, viene interpretato come numero che rappresenta il numero totale di millisecondi. |
| --warning-as-error | Indica se gli avvisi vengono considerati con lo stesso livello di gravità degli errori. |

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