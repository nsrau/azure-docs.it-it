---
title: Interfaccia della riga di comando Azure Service Fabric - sfctl replica | Microsoft Docs
description: Descrive i comandi dell'interfaccia della riga di comando Service Fabric sfctl replica.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 05/23/2018
ms.author: bikang
ms.openlocfilehash: cd09fe906f77bb06f0ac7afaa6c6cce326dbfa5c
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763579"
---
# <a name="sfctl-replica"></a>sfctl replica
Consente di gestire le repliche che appartengono alle partizioni del servizio.

## <a name="commands"></a>Comandi:

|Comando|DESCRIZIONE|
| --- | --- |
| deployed | Recupera i dettagli della replica distribuita in un nodo di Service Fabric. |
| deployed-list | Recupera l'elenco della replica distribuita in un nodo di Service Fabric. |
| health | Recupera l'integrità di una replica di un servizio con stato o di un'istanza di un servizio senza stato di Service Fabric. |
| info | Recupera le informazioni su una replica di una partizione di Service Fabric. |
| list | Recupera le informazioni sulle repliche di una partizione del servizio Service Fabric. |
| remove | Rimuove una replica del servizio in esecuzione in un nodo. |
| report-health | Invia un report di integrità nella replica di Service Fabric. |
| restart | Riavvia una replica del servizio di un servizio persistente in esecuzione in un nodo. |

## <a name="sfctl-replica-deployed"></a>sfctl replica deployed
Recupera i dettagli della replica distribuita in un nodo di Service Fabric.

Recupera i dettagli della replica distribuita in un nodo di Service Fabric. Le informazioni includono il tipo di servizio, il nome del servizio, l'operazione del servizio corrente, l'ora e la data di inizio dell'operazione del servizio corrente, l'ID della partizione, l'ID dell'istanza e della replica, il carico segnalato e altre informazioni.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --node-name [Obbligatorio] | Il nome del nodo. |
| --partition-id [Obbligatorio] | L'identità della partizione. |
| --replica-id [Obbligatorio] | Identificatore della replica. |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, tabella, tsv.  Impostazione predefinita\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-replica-deployed-list"></a>sfctl replica deployed-list
Recupera l'elenco della replica distribuita in un nodo di Service Fabric.

Recupera l'elenco contenente le informazioni relative alle repliche distribuite in un nodo di Service Fabric. Le informazioni includono ID partizione, ID replica, stato della replica, nome del servizio, nome del tipo di servizio e altre informazioni. Usare i parametri di query PartitionId o ServiceManifestName per restituire le informazioni sulle repliche distribuite corrispondenti ai valori specificati per tali parametri.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --application-id [obbligatorio] | Identità dell'applicazione. Si tratta in genere del nome completo dell'applicazione senza lo schema URI "fabric\:". A partire dalla versione 6.0, i nomi gerarchici sono delimitati con il carattere "\~". Se ad esempio il nome dell'applicazione è "fabric\:/myapp/app1", l'identità dell'applicazione sarà "myapp\~app1" nella versione 6.0 e successive e "myapp/app1" nelle versioni precedenti. |
| --node-name [obbligatorio] | Il nome del nodo. |
| --partition-id | L'identità della partizione. |
| --service-manifest-name | Nome di un manifesto del servizio registrato come parte di un tipo di applicazione in un cluster di Service Fabric. |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, tabella, tsv.  Impostazione predefinita\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-replica-health"></a>sfctl replica health
Recupera l'integrità di una replica di un servizio con stato o di un'istanza di un servizio senza stato di Service Fabric.

Recupera l'integrità di una replica di Service Fabric. Usare EventsHealthStateFilter per filtrare la raccolta di eventi di integrità riportati nella replica in base allo stato di integrità.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --partition-id [Obbligatorio] | L'identità della partizione. |
| --replica-id [Obbligatorio] | Identificatore della replica. |
| --events-health-state-filter | Consente di filtrare la raccolta di oggetti HealthEvent restituiti in base allo stato di integrità. I valori possibili per questo parametro includono il valore intero di uno dei seguenti stati di integrità. Vengono restituiti solo gli eventi che corrispondono al filtro. Tutti gli eventi vengono usati per valutare lo stato di integrità aggregato. Se non specificato diversamente, vengono restituite tutte le voci. I valori dello stato sono enumerati in base al flag, pertanto il valore potrebbe essere una combinazione di questi valori ottenuti usando l'operatore "OR" bit per bit. Ad esempio, se il valore fornito è 6, vengono restituiti tutti gli eventi con valore dello stato di integrità OK (2) e di Avviso (4).  <br> - Default - Valore predefinito. Consente di ricercare qualsiasi stato di integrità. Il valore predefinito è zero.  <br> - None - Il filtro non corrisponde ad alcun valore di stato di integrità. Usato per non restituire alcun risultato in un determinato insieme di stati. Il valore è uguale a 1.  <br> -Ok - Filtro che ricerca le corrispondenze di input con valore di stato di integrità Ok. Il valore è 2.  <br> -Warning - filtro che ricerca le corrispondenze di input con valore di stato di integrità Avviso. Il valore è 4.  <br> - Error - Filtro che ricerca le corrispondenze di input con valore di stato di integrità Errore. Il valore è 8.  <br> -All - Filtro che ricerca le corrispondenze di input con qualsiasi valore di stato di integrità. Il valore è 65535. |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, tabella, tsv.  Impostazione predefinita\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-replica-info"></a>sfctl replica info
Recupera le informazioni su una replica di una partizione di Service Fabric.

La risposta include l'ID, il ruolo, lo stato, l'integrità, il nome del nodo, il tempo di attività e altri dettagli relativi alla replica.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --partition-id [Obbligatorio] | L'identità della partizione. |
| --replica-id [Obbligatorio] | Identificatore della replica. |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, tabella, tsv.  Impostazione predefinita\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-replica-list"></a>sfctl replica list
Recupera le informazioni sulle repliche di una partizione del servizio Service Fabric.

L'endpoint GetReplicas restituisce le informazioni sulle repliche della partizione specificata. La risposta include l'ID, il ruolo, lo stato, l'integrità, il nome del nodo, il tempo di attività e altri dettagli relativi alla replica.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --partition-id [Obbligatorio] | L'identità della partizione. |
| --continuation-token | Il parametro del token di continuazione viene utilizzato per ottenere il set di risultati successivo. Un token di continuazione con un valore non vuoto è incluso nella risposta dell'API quando i risultati del sistema non rientrano in una singola risposta. Quando questo valore viene passato alla successiva chiamata API, l'API restituisce il set di risultati successivo. Se non sono presenti altri risultati, il token di continuazione non contiene alcun valore. Il valore di questo parametro non deve essere codificato in URL. |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, tabella, tsv.  Impostazione predefinita\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-replica-remove"></a>sfctl replica remove
Rimuove una replica del servizio in esecuzione in un nodo.

Questa API consente di simulare un errore di replica di Service Fabric rimuovendo una replica da un cluster di Service Fabric. La rimozione chiude la replica, la passa al ruolo Nessuno e quindi rimuove tutte le informazioni sullo stato della replica dal cluster. Questa API verifica il percorso di rimozione dello stato della replica e simula il percorso permanente di errore del report tramite le API del client. Avviso: quando si usa questa API non viene eseguito alcun controllo di sicurezza. L'uso non corretto di questa API può causare la perdita di dati per i servizi con stato. Il flag forceRemove ha anche effetto su tutte le altre repliche ospitate nello stesso processo.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --node-name [Obbligatorio] | Il nome del nodo. |
| --partition-id [Obbligatorio] | L'identità della partizione. |
| --replica-id [Obbligatorio] | Identificatore della replica. |
| --force-remove | Consente di rimuovere un servizio o un'applicazione di Service Fabric in modo forzato senza passare attraverso la sequenza di arresto normale. Questo parametro può essere usato per eliminare in modo forzato un'applicazione o un servizio per il quale l'operazione di eliminazione è prossima al timeout a causa di problemi nel codice del servizio che impediscono la normale chiusura delle repliche. |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, tabella, tsv.  Impostazione predefinita\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-replica-report-health"></a>sfctl replica report-health
Invia un report di integrità nella replica di Service Fabric.

Segnala lo stato di integrità della replica di Service Fabric specificata. Il report deve contenere le informazioni relative all'origine del report sull'integrità e alla proprietà in cui viene segnalato. Il report viene inviato a una replica del gateway di Service Fabric, che lo inoltra all'archivio integrità. Il report può essere accettato dal gateway, ma rifiutato dall'archivio integrità dopo la convalida aggiuntiva. L'archivio integrità, ad esempio, può rifiutare il report a causa di un parametro non valido, come un numero di sequenza non aggiornato. Per determinare se il report è stato applicato nell'archivio integrità, controllare che il report venga visualizzato nella sezione events.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --health-property [Obbligatorio] | Proprietà delle informazioni sull'integrità. <br><br> Un'entità può avere report sull'integrità per proprietà diverse. La proprietà è una stringa e non un'enumerazione fissa, per consentire la flessibilità del generatore di report necessaria per definire la categoria della condizione di stato che attiva il report. Ad esempio, un generatore di report con SourceId "LocalWatchdog" può monitorare lo stato del disco disponibile in un nodo e può quindi segnalare la proprietà "AvailableDisk" in tale nodo. Lo stesso generatore di report può monitorare la connettività del nodo e può quindi segnalare una proprietà "Connectivity" nello stesso nodo. Entrambi questi report vengono considerati come eventi di integrità separati nell'archivio integrità per il nodo specificato. Insieme a SourceId, la proprietà identifica in modo univoco le informazioni sull'integrità. |
| --health-state [Obbligatorio] | I valori possibili sono\: "Invalid", "Ok", "Warning", "Error", "Unknown". |
| --partition-id [Obbligatorio] | L'identità della partizione. |
| --replica-id [Obbligatorio] | L'identità della partizione. |
| --source-id [Obbligatorio] | Nome di origine che identifica il componente client/watchdog/sistema che ha generato le informazioni sull'integrità. |
| --description | Descrizione delle informazioni sull'integrità. <br><br> Rappresenta il testo libero usato per aggiungere informazioni leggibili sul report. La lunghezza massima della stringa per la descrizione è pari a 4096 caratteri. Se la stringa immessa è più lunga, verrà automaticamente troncata. Quando viene troncata, gli ultimi caratteri della descrizione contengono un marcatore "[Truncated]" e la dimensione totale della stringa è pari a 4096 caratteri. La presenza del marcatore indica agli utenti che si è verificato un troncamento. Si noti che quando viene troncata, la descrizione contiene meno di 4096 caratteri della stringa originale. |
| --immediate | Flag che indica se il report deve essere inviato immediatamente. <br><br> Un report sull'integrità viene inviato a un'applicazione del gateway di Service Fabric, che lo inoltra all'archivio integrità. Se Immediate è impostato su true, il report viene inviato immediatamente dal gateway HTTP all'archivio integrità, indipendentemente dalle impostazioni del client Fabric usate dall'applicazione gateway HTTP. Questo è utile per i report critici che devono essere inviati appena possibile. A seconda della tempistica e di altre condizioni, l'invio del report potrebbe tuttavia avere esito negativo, ad esempio se il gateway HTTP è chiuso o il messaggio non raggiunge il gateway. Se Immediate è impostato su false, il report viene inviato in base alle impostazioni del client di integrità dal gateway HTTP. Verrà quindi inviato in batch in base alla configurazione di HealthReportSendInterval. Questa è l'impostazione consigliata perché consente al client di integrità di ottimizzare la segnalazione di messaggi sull'integrità all'archivio integrità e l'elaborazione di report sull'integrità. Per impostazione predefinita, i report non vengono inviati immediatamente. |
| --remove-when-expired | Valore che indica se il report viene rimosso dall'archivio integrità quando scade. <br><br> Se impostato su true, il report viene rimosso dall'archivio integrità dopo la scadenza. Se impostato su false, il report viene considerato come errore quando è scaduto. Il valore di questa proprietà è false per impostazione predefinita. Quando i client creano report periodicamente, RemoveWhenExpired sarà impostato su false (valore predefinito). In questo modo, se il generatore di report presenta problemi (ad esempio, un deadlock) e non può creare report, l'entità viene considerata in stato di errore quando scade il report sull'integrità. L'entità viene quindi contrassegnata con lo stato di integrità di errore. |
| --sequence-number | Numero di sequenza per questo report sull'integrità come stringa numerica. <br><br> Il numero di sequenza del report viene usato dall'archivio integrità per rilevare i report non aggiornati. Se non specificato, un numero di sequenza viene generato automaticamente dal client di integrità quando viene aggiunto un report. |
| --service-kind | Tipo di replica del servizio (senza stato o con stato) per cui viene segnalata l'integrità. I valori possibili sono i seguenti\: "Stateless", "Stateful".  Impostazione predefinita\: Stateful. |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |
| --ttl | Durata in cui questo report sull'integrità è valido. Questo campo usa il formato ISO8601 per specificare la durata. <br><br> Quando i client creano report periodicamente, invieranno i report con una frequenza maggiore della durata (TTL). Se i client inviano report in caso di transizione, possono impostare la durata (TTL) come infinita. Quando la durata (TTL) scade, l'evento di integrità che contiene le informazioni sull'integrità viene rimosso dall'archivio integrità, se RemoveWhenExpired è true, o considerato in stato di errore, se RemoveWhenExpired è false. Se non è specificato, la durata (TTL) viene impostata su un valore infinito. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, tabella, tsv.  Impostazione predefinita\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-replica-restart"></a>sfctl replica restart
Riavvia una replica del servizio di un servizio persistente in esecuzione in un nodo.

Riavvia una replica del servizio di un servizio persistente in esecuzione in un nodo. Avviso: quando si usa questa API non viene eseguito alcun controllo di sicurezza. L'uso non corretto di questa API può causare la perdita di disponibilità per i servizi con stato.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --node-name [Obbligatorio] | Il nome del nodo. |
| --partition-id [Obbligatorio] | L'identità della partizione. |
| --replica-id [Obbligatorio] | Identificatore della replica. |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, tabella, tsv.  Impostazione predefinita\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="next-steps"></a>Passaggi successivi
- [Configurare](service-fabric-cli.md) l'interfaccia della riga di comando di Service Fabric.
- Informazioni su come usare l'interfaccia della riga di comando Service Fabric usando gli [script di esempio](/azure/service-fabric/scripts/sfctl-upgrade-application).
