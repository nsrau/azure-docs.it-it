---
title: 'Interfaccia della riga di comando di Azure Service Fabric: sfctl application| Microsoft Docs'
description: Descrive i comandi dell'interfaccia della riga di comando di Service Fabric per sfctl application.
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
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: 0f608dc89d3a9bc8914fc9be142c442246ce13b5
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/12/2018
ms.locfileid: "53278543"
---
# <a name="sfctl-application"></a>sfctl application
Consente di creare, eliminare e gestire le applicazioni e i tipi di applicazioni.

## <a name="commands"></a>Comandi:

|Comando|Descrizione|
| --- | --- |
| create | Crea un'applicazione di Service Fabric usando la descrizione specificata. |
| delete | Elimina un'applicazione di Service Fabric esistente. |
| deployed | Recupera le informazioni relative a un'applicazione distribuita in un nodo di Service Fabric. |
| deployed-health | Recupera le informazioni sull'integrità di un'applicazione distribuita in un nodo Service Fabric. |
| deployed-list | Mostra l'elenco delle applicazioni distribuite in un nodo di Service Fabric. |
| health | Mostra l'integrità dell'applicazione di Service Fabric. |
| info | Recupera informazioni su un'applicazione di Service Fabric. |
| list | Mostra l'elenco di applicazioni create nel cluster di Service Fabric che corrisponde ai filtri specificati. |
| load | Mostra informazioni di caricamento su un'applicazione di Service Fabric. |
| manifest | Mostra il manifesto che descrive un tipo di applicazione. |
| provision | Esegue il provisioning o registra un tipo di applicazione di Service Fabric con il cluster mediante il pacchetto sfpkg nell'archivio esterno o mediante il pacchetto dell'applicazione nell'archivio immagini. |
| report-health | Invia un report di integrità sull'applicazione di Service Fabric. |
| type | Mostra l'elenco dei tipi di applicazioni nel cluster di Service Fabric che corrispondono esattamente al nome specificato. |
| type-list | Mostra l'elenco dei tipi di applicazioni nel cluster di Service Fabric. |
| unprovision | Rimuove o annulla la registrazione di un tipo di applicazione di Service Fabric dal cluster. |
| Aggiornamento | Avvia l'aggiornamento di un'applicazione nel cluster di Service Fabric. |
| upgrade-resume | Riprende l'aggiornamento di un'applicazione nel cluster di Service Fabric. |
| upgrade-rollback | Avvia il rollback dell'aggiornamento attualmente in corso di un'applicazione nel cluster di Service Fabric. |
| upgrade-status | Mostra i dettagli dell'aggiornamento più recente eseguito sull'applicazione. |
| upload | Copia il pacchetto dell'applicazione di Service Fabric nell'archivio immagini. |

## <a name="sfctl-application-create"></a>sfctl application create
Crea un'applicazione di Service Fabric usando la descrizione specificata.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --app-name [obbligatorio] | Nome dell'applicazione, incluso lo schema URI "fabric\:". |
| -app-type [obbligatorio] | Il nome del tipo di applicazione trovato nel manifesto dell'applicazione. |
| --app-version [obbligatorio] | La versione del tipo di applicazione, come definita nel manifesto dell'applicazione. |
| --max-node-count | Numero massimo di nodi in cui Service Fabric riserverà la capacità per questa applicazione. Si noti che ciò non significa che i servizi dell'applicazione vengono inseriti in tutti i nodi indicati. |
| --metrics | Elenco con codifica JSON di descrizioni relative alla metrica di capacità dell'applicazione . Una metrica è definita come nome, associato a un set di capacità per ogni nodo in cui esiste l'applicazione. |
| --min-node-count | Numero minimo di nodi in cui Service Fabric riserverà la capacità per questa applicazione. Si noti che ciò non significa che i servizi dell'applicazione vengono inseriti in tutti i nodi indicati. |
| --parameters | Elenco con codifica JSON di sostituzioni del parametro dell'applicazione da applicare quando si crea l'applicazione. |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-application-delete"></a>sfctl application delete
Elimina un'applicazione di Service Fabric esistente.

Prima di poter essere eliminata, un'applicazione deve essere creata. Se si elimina un'applicazione, si elimineranno tutti i servizi che fanno parte di tale applicazione. Per impostazione predefinita, Service Fabric proverà a chiudere le repliche del servizio in modo normale, per poi eliminare il servizio. Se tuttavia in un servizio si verificano problemi chiudendo normalmente la replica, l'operazione di eliminazione potrebbe richiedere molto tempo o bloccarsi. Usare il flag facoltativo ForceRemove per ignorare la normale sequenza di chiusura ed eliminare l'applicazione e tutti i relativi servizi in modo forzato.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --application-id [obbligatorio] | Identità dell'applicazione. Si tratta in genere del nome completo dell'applicazione senza lo schema URI "fabric\:". A partire dalla versione 6.0, i nomi gerarchici sono delimitati dal carattere "\~". Ad esempio, se il nome dell'applicazione è "fabric\:/myapp/app1", l'identità dell'applicazione sarà "myapp\~app1" nella versione 6.0 e successive e "myapp/app1" nelle versioni precedenti. |
| --force-remove | Consente di rimuovere un servizio o un'applicazione di Service Fabric in modo forzato senza passare attraverso la sequenza di arresto normale. Questo parametro può essere usato per eliminare in modo forzato un'applicazione o un servizio per il quale l'operazione di eliminazione è prossima al timeout a causa di problemi nel codice del servizio che impediscono la normale chiusura delle repliche. |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-application-deployed"></a>sfctl application deployed
Recupera le informazioni relative a un'applicazione distribuita in un nodo di Service Fabric.

Questa query restituisce informazioni sull'applicazione di sistema se l'ID applicazione fornito è relativo all'applicazione di sistema. I risultati includono le applicazioni distribuite con stato "attivo", "attivazione in corso" e "download in corso". Questa query richiede che il nome del nodo corrisponda a un nodo nel cluster. La query ha esito negativo se il nome del nodo specificato non fa riferimento ad alcun nodo di Service Fabric attivo nel cluster.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --application-id [obbligatorio] | Identità dell'applicazione. Si tratta in genere del nome completo dell'applicazione senza lo schema URI "fabric\:". A partire dalla versione 6.0, i nomi gerarchici sono delimitati dal carattere "\~". Ad esempio, se il nome dell'applicazione è "fabric\:/myapp/app1", l'identità dell'applicazione sarà "myapp\~app1" nella versione 6.0 e successive e "myapp/app1" nelle versioni precedenti. |
| --node-name [obbligatorio] | Il nome del nodo. |
| --include-health-state | Include lo stato di integrità di un'entità. Se questo parametro è false o non viene specificato, lo stato di integrità restituito è "Unknown". Quando è impostato su true, la query passa in parallelo al nodo e al servizio di sistema di integrità prima che i risultati vengano uniti. Di conseguenza, la query è più costosa e può richiedere più tempo. |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-application-deployed-health"></a>sfctl application deployed-health
Recupera le informazioni sull'integrità di un'applicazione distribuita in un nodo Service Fabric.

Recupera le informazioni sull'integrità di un'applicazione distribuita in un nodo Service Fabric. Usare EventsHealthStateFilter per eventualmente filtrare la raccolta di oggetti HealthEvent segnalati nell'applicazione distribuita in base allo stato di integrità. Usare DeployedServicePackagesHealthStateFilter per eventualmente filtrare l'elemento figlio DeployedServicePackageHealth in base allo stato di integrità.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --application-id [Obbligatorio] | Identità dell'applicazione. Si tratta in genere del nome completo dell'applicazione senza lo schema URI "fabric\:". A partire dalla versione 6.0, i nomi gerarchici sono delimitati dal carattere "\~". Se ad esempio il nome dell'applicazione è "fabric\:/myapp/app1", l'identità dell'applicazione sarà "myapp\~app1" nella versione 6.0 e successive e "myapp/app1" nelle versioni precedenti. |
| --node-name [Obbligatorio] | Il nome del nodo. |
| --deployed-service-packages-health-state-filter | Consente di filtrare gli oggetti dello stato di integrità dei pacchetti servizio distribuiti restituiti nel risultato della query di integrità dell'applicazione in base al relativo stato di integrità. I valori possibili per questo parametro includono il valore intero di uno dei seguenti stati di integrità. Vengono restituiti solo i pacchetti servizio distribuiti che corrispondono al filtro. Tutte i pacchetti servizio distribuiti vengono usati per valutare lo stato di integrità aggregato dell'applicazione distribuita. Se non specificato diversamente, vengono restituite tutte le voci. I valori dello stato sono enumerati in base al flag, quindi il valore può essere una combinazione di questi valori ottenuti usando l'operatore "OR" bit per bit. Ad esempio, se il valore fornito è 6, viene restituito lo stato di integrità dei pacchetti servizio con valore di HealthState OK (2) e Warning (4).  <br> - Default - Valore predefinito. Consente di ricercare qualsiasi stato di integrità. Il valore predefinito è zero.  <br> - None - Il filtro non corrisponde ad alcun valore di stato di integrità. Usato per non restituire alcun risultato in un determinato insieme di stati. Il valore è uguale a 1.  <br> -Ok - Filtro che ricerca le corrispondenze di input con valore di stato di integrità Ok. Il valore è 2.  <br> -Warning - filtro che ricerca le corrispondenze di input con valore di stato di integrità Avviso. Il valore è 4.  <br> - Error - Filtro che ricerca le corrispondenze di input con valore di stato di integrità Errore. Il valore è 8.  <br> -All - Filtro che ricerca le corrispondenze di input con qualsiasi valore di stato di integrità. Il valore è 65535. |
| --events-health-state-filter | Consente di filtrare la raccolta di oggetti HealthEvent restituiti in base allo stato di integrità. I valori possibili per questo parametro includono il valore intero di uno dei seguenti stati di integrità. Vengono restituiti solo gli eventi che corrispondono al filtro. Tutti gli eventi vengono usati per valutare lo stato di integrità aggregato. Se non specificato diversamente, vengono restituite tutte le voci. I valori dello stato sono enumerati in base al flag, pertanto il valore potrebbe essere una combinazione di questi valori ottenuti usando l'operatore "OR" bit per bit. Ad esempio, se il valore fornito è 6, vengono restituiti tutti gli eventi con valore dello stato di integrità OK (2) e di Avviso (4).  <br> - Default - Valore predefinito. Consente di ricercare qualsiasi stato di integrità. Il valore predefinito è zero.  <br> - None - Il filtro non corrisponde ad alcun valore di stato di integrità. Usato per non restituire alcun risultato in un determinato insieme di stati. Il valore è uguale a 1.  <br> -Ok - Filtro che ricerca le corrispondenze di input con valore di stato di integrità Ok. Il valore è 2.  <br> -Warning - filtro che ricerca le corrispondenze di input con valore di stato di integrità Avviso. Il valore è 4.  <br> - Error - Filtro che ricerca le corrispondenze di input con valore di stato di integrità Errore. Il valore è 8.  <br> -All - Filtro che ricerca le corrispondenze di input con qualsiasi valore di stato di integrità. Il valore è 65535. |
| --exclude-health-statistics | Indica se le statistiche di integrità devono essere restituite come parte del risultato della query. False per impostazione predefinita. Le statistiche indicano il numero di entità figlio il cui stato di integrità è Ok, Avviso ed Errore. |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-application-deployed-list"></a>sfctl application deployed-list
Mostra l'elenco delle applicazioni distribuite in un nodo di Service Fabric.

Mostra l'elenco delle applicazioni distribuite in un nodo di Service Fabric. I risultati non includono informazioni sulle applicazioni di sistema distribuite, a meno che non vengano eseguite query in base all'ID. I risultati includono le applicazioni distribuite con stato "attivo", "attivazione in corso" e "download in corso". Questa query richiede che il nome del nodo corrisponda a un nodo nel cluster. La query ha esito negativo se il nome del nodo specificato non fa riferimento ad alcun nodo di Service Fabric attivo nel cluster.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --node-name [Required] | Il nome del nodo. |
| --continuation-token | Il parametro del token di continuazione viene utilizzato per ottenere il set di risultati successivo. Un token di continuazione con un valore non vuoto è incluso nella risposta dell'API quando i risultati del sistema non rientrano in una singola risposta. Quando questo valore viene passato alla successiva chiamata API, l'API restituisce il set di risultati successivo. Se non sono presenti altri risultati, il token di continuazione non contiene alcun valore. Il valore di questo parametro non deve essere codificato in URL. |
| --include-health-state | Include lo stato di integrità di un'entità. Se questo parametro è false o non viene specificato, lo stato di integrità restituito è "Unknown". Quando è impostato su true, la query passa in parallelo al nodo e al servizio di sistema di integrità prima che i risultati vengano uniti. Di conseguenza, la query è più costosa e può richiedere più tempo. |
| --max-results | Il numero massimo di risultati che devono essere restituiti come parte delle query di paging. Questo parametro definisce il limite massimo di risultati restituiti. Se non rientrano nel messaggio in base ai limiti di dimensione massima per i messaggi definiti nella configurazione, il numero dei risultati restituiti può essere inferiore al numero massimo di risultati specificato. Se questo parametro è uguale a zero o non specificato, le query di paging includono il numero massimo di risultati possibili che rientrano nel messaggio restituito. |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-application-health"></a>sfctl application health
Mostra l'integrità dell'applicazione di Service Fabric.

Restituisce lo stato di integrità dell'applicazione di Service Fabric. La risposta indica lo stato di integrità com OK, Errore o Avviso. Se l'entità non viene trovata nell'archivio integrità, verrà restituito Error.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --application-id [obbligatorio] | Identità dell'applicazione. Si tratta in genere del nome completo dell'applicazione senza lo schema URI "fabric\:". A partire dalla versione 6.0, i nomi gerarchici sono delimitati dal carattere "\~". Ad esempio, se il nome dell'applicazione è "fabric\:/myapp/app1", l'identità dell'applicazione sarà "myapp\~app1" nella versione 6.0 e successive e "myapp/app1" nelle versioni precedenti. |
| --deployed-applications-health-state-filter | Consente di filtrare gli oggetti dello stato di integrità delle applicazioni distribuite restituiti nel risultato della query di integrità dell'applicazione in base al relativo stato di integrità. I valori possibili per questo parametro includono il valore intero di uno dei seguenti stati di integrità. Verranno restituite solo le applicazioni distribuite che corrispondono al filtro. Tutte le applicazioni distribuite vengono usate per valutare lo stato di integrità aggregato. Se non specificato diversamente, vengono restituite tutte le voci. I valori dello stato sono enumerati in base al flag, pertanto il valore potrebbe essere una combinazione di questi valori ottenuti usando l'operatore "OR" bit per bit. Ad esempio, se il valore indicato è 6, viene restituito lo stato di integrità delle applicazioni distribuite il cui valore di HealthState è OK (2) e Avviso (4).  <br> - Default - Valore predefinito. Consente di ricercare qualsiasi stato di integrità. Il valore predefinito è zero.  <br> - None - Il filtro non corrisponde ad alcun valore di stato di integrità. Usato per non restituire alcun risultato in un determinato insieme di stati. Il valore è uguale a 1.  <br> -Ok - Filtro che ricerca le corrispondenze di input con valore di stato di integrità Ok. Il valore è 2.  <br> -Warning - filtro che ricerca le corrispondenze di input con valore di stato di integrità Avviso. Il valore è 4.  <br> - Error - Filtro che ricerca le corrispondenze di input con valore di stato di integrità Errore. Il valore è 8.  <br> -All - Filtro che ricerca le corrispondenze di input con qualsiasi valore di stato di integrità. Il valore è 65535. |
| --events-health-state-filter | Consente di filtrare la raccolta di oggetti HealthEvent restituiti in base allo stato di integrità. I valori possibili per questo parametro includono il valore intero di uno dei seguenti stati di integrità. Vengono restituiti solo gli eventi che corrispondono al filtro. Tutti gli eventi vengono usati per valutare lo stato di integrità aggregato. Se non specificato diversamente, vengono restituite tutte le voci. I valori dello stato sono enumerati in base al flag, pertanto il valore potrebbe essere una combinazione di questi valori ottenuti usando l'operatore "OR" bit per bit. Ad esempio, se il valore fornito è 6, vengono restituiti tutti gli eventi con valore dello stato di integrità OK (2) e di Avviso (4).  <br> - Default - Valore predefinito. Consente di ricercare qualsiasi stato di integrità. Il valore predefinito è zero.  <br> - None - Il filtro non corrisponde ad alcun valore di stato di integrità. Usato per non restituire alcun risultato in un determinato insieme di stati. Il valore è uguale a 1.  <br> -Ok - Filtro che ricerca le corrispondenze di input con valore di stato di integrità Ok. Il valore è 2.  <br> -Warning - filtro che ricerca le corrispondenze di input con valore di stato di integrità Avviso. Il valore è 4.  <br> - Error - Filtro che ricerca le corrispondenze di input con valore di stato di integrità Errore. Il valore è 8.  <br> -All - Filtro che ricerca le corrispondenze di input con qualsiasi valore di stato di integrità. Il valore è 65535. |
| --exclude-health-statistics | Indica se le statistiche di integrità devono essere restituite come parte del risultato della query. False per impostazione predefinita. Le statistiche indicano il numero di entità figlio il cui stato di integrità è Ok, Avviso ed Errore. |
| --services-health-state-filter | Consente di filtrare gli oggetti dello stato di integrità del servizio restituiti nel risultato della query sull'integrità del servizio in base al relativo stato di integrità. I valori possibili per questo parametro includono il valore intero di uno dei seguenti stati di integrità. Vengono restituiti solo i servizi che corrispondono al filtro. Tutti i servizi vengono usati per valutare lo stato di integrità aggregato. Se non specificato diversamente, vengono restituite tutte le voci. I valori dello stato sono enumerati in base al flag, pertanto il valore potrebbe essere una combinazione di questi valori ottenuti usando l'operatore "OR" bit per bit. Ad esempio, se il valore indicato è 6, viene restituito lo stato di integrità dei servizi con valore HealthState OK (2) e Warning (4).  <br> - Default - Valore predefinito. Consente di ricercare qualsiasi stato di integrità. Il valore predefinito è zero.  <br> - None - Il filtro non corrisponde ad alcun valore di stato di integrità. Usato per non restituire alcun risultato in un determinato insieme di stati. Il valore è uguale a 1.  <br> -Ok - Filtro che ricerca le corrispondenze di input con valore di stato di integrità Ok. Il valore è 2.  <br> -Warning - filtro che ricerca le corrispondenze di input con valore di stato di integrità Avviso. Il valore è 4.  <br> - Error - Filtro che ricerca le corrispondenze di input con valore di stato di integrità Errore. Il valore è 8.  <br> -All - Filtro che ricerca le corrispondenze di input con qualsiasi valore di stato di integrità. Il valore è 65535. |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-application-info"></a>sfctl application info
Recupera informazioni su un'applicazione di Service Fabric.

Restituisce le informazioni sull'applicazione creata o in corso di creazione nel cluster di Service Fabric e il cui nome corrisponde a quello specificato come parametro. La risposta include il nome, il tipo, lo stato, i parametri e altri dettagli relativi all'applicazione.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --application-id [obbligatorio] | Identità dell'applicazione. Si tratta in genere del nome completo dell'applicazione senza lo schema URI "fabric\:". A partire dalla versione 6.0, i nomi gerarchici sono delimitati dal carattere "\~". Ad esempio, se il nome dell'applicazione è "fabric\:/myapp/app1", l'identità dell'applicazione sarà "myapp\~app1" nella versione 6.0 e successive e "myapp/app1" nelle versioni precedenti. |
| --exclude-application-parameters | Flag che specifica se i parametri dell'applicazione verranno esclusi dal risultato. |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-application-list"></a>sfctl application list
Mostra l'elenco di applicazioni create nel cluster di Service Fabric che corrisponde ai filtri specificati.

Recupera le informazioni sulle applicazioni create o in corso di creazione nel cluster di Service Fabric e che corrispondono ai filtri specificati. La risposta include il nome, il tipo, lo stato, i parametri e altri dettagli relativi all'applicazione. Se le applicazioni non rientrano in una pagina, viene restituita una pagina di risultati e un token di continuazione, che possono essere usati per accedere alla pagina successiva. Non è possibile specificare contemporaneamente i filtri ApplicationTypeName e ApplicationDefinitionKindFilter.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --application-definition-kind-filter | Usato per filtrare in base ad ApplicationDefinitionKind, che è il meccanismo usato per definire un'applicazione di Service Fabric.  <br> - Default - Valore predefinito, che esegue la stessa funzione della selezione di "All". Il valore è 0.  <br> - All - Filtro che corrisponde all'input con qualsiasi valore di ApplicationDefinitionKind. Il valore è 65535.  <br> -ServiceFabricApplicationDescription - Filtro che corrisponde all'input con valore di ApplicationDefinitionKind uguale a ServiceFabricApplicationDescription. Il valore è uguale a 1.  <br> - Compose - Filtro che corrisponde all'input con valore di ApplicationDefinitionKind uguale a Compose. Il valore è 2. |
| --application-type-name | Nome del tipo di applicazione usato per filtrare le applicazioni per cui eseguire le query. Questo valore non deve contenere la versione del tipo di applicazione. |
| --continuation-token | Il parametro del token di continuazione viene utilizzato per ottenere il set di risultati successivo. Un token di continuazione con un valore non vuoto è incluso nella risposta dell'API quando i risultati del sistema non rientrano in una singola risposta. Quando questo valore viene passato alla successiva chiamata API, l'API restituisce il set di risultati successivo. Se non sono presenti altri risultati, il token di continuazione non contiene alcun valore. Il valore di questo parametro non deve essere codificato in URL. |
| --exclude-application-parameters | Flag che specifica se i parametri dell'applicazione verranno esclusi dal risultato. |
| --max-results | Il numero massimo di risultati che devono essere restituiti come parte delle query di paging. Questo parametro definisce il limite massimo di risultati restituiti. Se non rientrano nel messaggio in base ai limiti di dimensione massima per i messaggi definiti nella configurazione, il numero dei risultati restituiti può essere inferiore al numero massimo di risultati specificato. Se questo parametro è uguale a zero o non specificato, le query di paging includono il numero massimo di risultati possibili che rientrano nel messaggio restituito. |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-application-load"></a>sfctl application load
Mostra informazioni di caricamento su un'applicazione di Service Fabric.

Restituisce le informazioni di carico sull'applicazione creata o in corso di creazione nel cluster di Service Fabric e il cui nome corrisponde a quello specificato come parametro. La risposta include il nome, il numero di nodi minimo e massimo, il numero di nodi che l'applicazione occupa al momento e le informazioni sulla metrica di carico dell'applicazione.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --application-id [obbligatorio] | Identità dell'applicazione. Si tratta in genere del nome completo dell'applicazione senza lo schema URI "fabric\:". A partire dalla versione 6.0, i nomi gerarchici sono delimitati dal carattere "\~". Ad esempio, se il nome dell'applicazione è "fabric\:/myapp/app1", l'identità dell'applicazione sarà "myapp\~app1" nella versione 6.0 e successive e "myapp/app1" nelle versioni precedenti. |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-application-manifest"></a>sfctl application manifest
Mostra il manifesto che descrive un tipo di applicazione.

La risposta contiene il manifesto dell'applicazione XML sotto forma di stringa.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --application-type-name    [obbligatorio] | Nome del tipo di applicazione. |
| --application-type-version [obbligatorio] | Versione del tipo di applicazione. |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-application-provision"></a>sfctl application provision
Esegue il provisioning o registra un tipo di applicazione di Service Fabric con il cluster mediante il pacchetto sfpkg nell'archivio esterno o mediante il pacchetto dell'applicazione nell'archivio immagini.

Esegue il provisioning di un tipo di applicazione di Service Fabric con il cluster. È necessario eseguire questa operazione prima di creare un'istanza per una nuova applicazione. L'operazione di provisioning può essere effettuata sul pacchetto dell'applicazione specificato da relativePathInImageStore o usando l'URI del pacchetto SFPKG esterno. A meno che non sia impostato --external-provision, questo comando prevede il provisioning dell'archivio immagini.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --application-package-download-uri | Percorso del pacchetto dell'applicazione "sfpkg", da cui può essere scaricato il pacchetto dell'applicazione tramite i protocolli HTTP o HTTPS. <br><br> Solo per il provisioning dall'archivio esterno. Il pacchetto dell'applicazione può essere archiviato in un archivio esterno che fornisce l'operazione GET per il download del file. I protocolli supportati sono HTTP e HTTPS e il percorso deve consentire l'accesso in lettura. |
| --application-type-build-path | Solo per il provisioning dall'archivio immagini. Percorso relativo del pacchetto dell'applicazione nell'archivio immagini specificato durante l'operazione di caricamento precedente. |
| --application-type-name | Solo per il provisioning dall'archivio esterno. Il nome del tipo di applicazione rappresenta il nome del tipo di applicazione trovato nel manifesto dell'applicazione. |
| --application-type-version | Solo per il provisioning dall'archivio esterno. La versione del tipo di applicazione rappresenta la versione del tipo di applicazione trovata nel manifesto dell'applicazione. |
| --external-provision | Percorso da in cui è possibile eseguire la registrazione o il provisioning del pacchetto dell'applicazione. Indica che il provisioning è relativo a un pacchetto dell'applicazione che è stato caricato in precedenza in un archivio esterno. Il pacchetto dell'applicazione termina con l'estensione *.sfpkg. |
| --no-wait | Indica se il provisioning deve essere eseguito o meno in modo asincrono. <br><br> Se impostato su true, l'operazione di provisioning restituisce una valore quando la richiesta viene accettata dal sistema e l'operazione di provisioning continua senza alcun limite di timeout. Il valore predefinito è False. Per i pacchetti dell'applicazione di grandi dimensioni, è consigliabile impostare il valore su true. |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-application-report-health"></a>sfctl application report-health
Invia un report di integrità sull'applicazione di Service Fabric.

Segnala lo stato di integrità dell'applicazione di Service Fabric specificata. Il report deve contenere le informazioni relative all'origine del report sull'integrità e alla proprietà in cui viene segnalato. Il report viene inviato a un'applicazione del gateway di Service Fabric, che lo inoltra all'archivio integrità. Il report può essere accettato dal gateway, ma rifiutato dall'archivio integrità dopo la convalida aggiuntiva. L'archivio integrità, ad esempio, può rifiutare il report a causa di un parametro non valido, come un numero di sequenza non aggiornato. Per determinare se il report è stato applicato nell'archivio integrità, ottenere l'integrità dell'applicazione e controllare che il report venga visualizzato.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --application-id [Obbligatorio] | Identità dell'applicazione. <br><br> Si tratta in genere del nome completo dell'applicazione senza lo schema URI "fabric\:". A partire dalla versione 6.0, i nomi gerarchici sono delimitati con il carattere "\~". Se ad esempio il nome dell'applicazione è "fabric\:/myapp/app1", l'identità dell'applicazione sarà "myapp\~app1" nella versione 6.0 e successive e "myapp/app1" nelle versioni precedenti. |
| --health-property [Obbligatorio] | Proprietà delle informazioni sull'integrità. <br><br> Un'entità può avere report sull'integrità per proprietà diverse. La proprietà è una stringa e non un'enumerazione fissa, che assicura al reporter la flessibilità necessaria per definire la categoria della condizione di stato che attiva il report. Ad esempio, un reporter con SourceId "LocalWatchdog" può monitorare lo stato del disco disponibile in un nodo e può quindi segnalare la proprietà "AvailableDisk" in tale nodo. Lo stesso reporter può monitorare la connettività del nodo e può quindi segnalare una proprietà "Connectivity" nello stesso nodo. Entrambi questi report vengono considerati come eventi di integrità separati nell'archivio integrità per il nodo specificato. Insieme a SourceId, la proprietà identifica in modo univoco le informazioni sull'integrità. |
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
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-application-type"></a>sfctl application type
Mostra l'elenco dei tipi di applicazioni nel cluster di Service Fabric che corrispondono esattamente al nome specificato.

Restituisce le informazioni sui tipi di applicazione per cui si effettua o è in corso il provisioning del cluster di Service Fabric. Questi risultati sono tipi di applicazioni il cui nome corrisponde esattamente a quello specificato come parametro e che rispettano i parametri di query specificati. Vengono restituite tutte le versioni del tipo di applicazione corrispondenti al nome del tipo di applicazione, ogni versione viene restituita come un tipo di applicazione. La risposta include il nome, la versione, lo stato e altri dettagli relativi al tipo di applicazione. Si tratta di una query di paging, il che vuol dire che se i tipi di applicazione non rientrano tutti in una pagina, viene restituita una pagina di risultati e un token di continuazione, che possono essere usati per accedere alla pagina successiva. Se ad esempio sono presenti 10 tipi di applicazione, ma una pagina contiene solo i primi tre tipi di applicazione oppure il risultato massimo è impostato su 3, ne vengono restituiti tre. Per accedere al resto dei risultati, recuperare le pagine successive usando il token di continuazione restituito nella query successiva. Se non ci sono pagine successive, viene restituito un token di continuazione vuoto.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --application-type-name [obbligatorio] | Nome del tipo di applicazione. |
| --application-type-version | Versione del tipo di applicazione. |
| --continuation-token | Il parametro del token di continuazione viene utilizzato per ottenere il set di risultati successivo. Un token di continuazione con un valore non vuoto è incluso nella risposta dell'API quando i risultati del sistema non rientrano in una singola risposta. Quando questo valore viene passato alla successiva chiamata API, l'API restituisce il set di risultati successivo. Se non sono presenti altri risultati, il token di continuazione non contiene alcun valore. Il valore di questo parametro non deve essere codificato in URL. |
| --exclude-application-parameters | Flag che specifica se i parametri dell'applicazione verranno esclusi dal risultato. |
| --max-results | Il numero massimo di risultati che devono essere restituiti come parte delle query di paging. Questo parametro definisce il limite massimo di risultati restituiti. Se non rientrano nel messaggio in base ai limiti di dimensione massima per i messaggi definiti nella configurazione, il numero dei risultati restituiti può essere inferiore al numero massimo di risultati specificato. Se questo parametro è uguale a zero o non specificato, le query di paging includono il numero massimo di risultati possibili che rientrano nel messaggio restituito. |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-application-type-list"></a>sfctl application type-list
Mostra l'elenco dei tipi di applicazioni nel cluster di Service Fabric.

Restituisce le informazioni sui tipi di applicazione per cui si effettua o è in corso il provisioning del cluster di Service Fabric. Ogni versione di un tipo di applicazione viene restituito come tipo di applicazione. La risposta include il nome, la versione, lo stato e altri dettagli relativi al tipo di applicazione. Si tratta di una query di paging, il che vuol dire che se i tipi di applicazione non rientrano tutti in una pagina, viene restituita una pagina di risultati e un token di continuazione, che possono essere usati per accedere alla pagina successiva. Se ad esempio sono presenti 10 tipi di applicazione, ma una pagina contiene solo i primi tre tipi di applicazione oppure il risultato massimo è impostato su 3, ne vengono restituiti tre. Per accedere al resto dei risultati, recuperare le pagine successive usando il token di continuazione restituito nella query successiva. Se non ci sono pagine successive, viene restituito un token di continuazione vuoto.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --application-type-definition-kind-filter | Usato per filtrare in base ad ApplicationTypeDefinitionKind, che è il meccanismo usato per definire un tipo di applicazione di Service Fabric.  <br> - Default - Valore predefinito, che esegue la stessa funzione della selezione di "All". Il valore è 0.  <br> - All - Filtro che corrisponde all'input con qualsiasi valore di ApplicationTypeDefinitionKind. Il valore è 65535.  <br> - ServiceFabricApplicationPackage - Filtro che corrisponde all'input con valore di ApplicationTypeDefinitionKind uguale a ServiceFabricApplicationPackage. Il valore è uguale a 1.  <br> - Compose - Filtro che corrisponde all'input con valore di ApplicationTypeDefinitionKind uguale a Compose. Il valore è 2. |
| --continuation-token | Il parametro del token di continuazione viene utilizzato per ottenere il set di risultati successivo. Un token di continuazione con un valore non vuoto è incluso nella risposta dell'API quando i risultati del sistema non rientrano in una singola risposta. Quando questo valore viene passato alla successiva chiamata API, l'API restituisce il set di risultati successivo. Se non sono presenti altri risultati, il token di continuazione non contiene alcun valore. Il valore di questo parametro non deve essere codificato in URL. |
| --exclude-application-parameters | Flag che specifica se i parametri dell'applicazione verranno esclusi dal risultato. |
| --max-results | Il numero massimo di risultati che devono essere restituiti come parte delle query di paging. Questo parametro definisce il limite massimo di risultati restituiti. Se non rientrano nel messaggio in base ai limiti di dimensione massima per i messaggi definiti nella configurazione, il numero dei risultati restituiti può essere inferiore al numero massimo di risultati specificato. Se questo parametro è uguale a zero o non specificato, le query di paging includono il numero massimo di risultati possibili che rientrano nel messaggio restituito. |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-application-unprovision"></a>sfctl application unprovision
Rimuove o annulla la registrazione di un tipo di applicazione di Service Fabric dal cluster.

Questa operazione può essere eseguita solo se tutte le istanze dell'applicazione del tipo di applicazione sono state eliminate. Dopo aver annullato la registrazione del tipo di applicazione, non è possibile creare nuove istanze di applicazione per questo particolare tipo di applicazione.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --application-type-name    [obbligatorio] | Nome del tipo di applicazione. |
| --application-type-version [obbligatorio] | La versione del tipo di applicazione, come definita nel manifesto dell'applicazione. |
| --async-parameter | Flag che indica se l'annullamento del provisioning deve essere eseguito o meno in modo asincrono. Se impostato su true, l'operazione di annullamento del provisioning restituisce una valore quando la richiesta viene accettata dal sistema e l'operazione di annullamento del provisioning continua senza alcun limite di timeout. Il valore predefinito è False. È tuttavia consigliabile impostare il valore true per i pacchetti dell'applicazione di grandi dimensioni di cui è stato effettuato il provisioning. |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-application-upgrade"></a>sfctl application upgrade
Avvia l'aggiornamento di un'applicazione nel cluster di Service Fabric.

Convalida i parametri di aggiornamento dell'applicazione offerta e avvia l'aggiornamento dell'applicazione se i parametri sono validi. Si noti che la descrizione dell'aggiornamento sostituisce la descrizione dell'applicazione esistente. Ciò significa che se i parametri vengono omessi, i parametri esistenti per le applicazioni verranno sovrascritti con l'elenco di parametri vuoto. Il risultato sarà l'applicazione che usa il valore predefinito dei parametri del manifesto dell'applicazione.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --application-id [obbligatorio] | Identità dell'applicazione. <br><br> Si tratta in genere del nome completo dell'applicazione senza lo schema URI "fabric\:". A partire dalla versione 6.0, i nomi gerarchici sono delimitati dal carattere "\~". Ad esempio, se il nome dell'applicazione è "fabric\:/myapp/app1", l'identità dell'applicazione sarà "myapp\~app1" nella versione 6.0 e successive e "myapp/app1" nelle versioni precedenti. |
| --application-version [Obbligatorio] | Versione del tipo di applicazione di destinazione (presente nel manifesto dell'applicazione) per l'aggiornamento dell'applicazione. |
| --parameters [Obbligatorio] | Elenco con codifica JSON di sostituzioni del parametro dell'applicazione da applicare quando si esegue l'aggiornamento dell'applicazione. |
| --default-service-health-policy | Specifica con codifica JSON dei criteri di integrità usati per impostazione predefinita per la valutazione dell'integrità di un tipo di servizio. |
| --failure-action | Azione da eseguire quando un aggiornamento di monitoraggio rileva violazioni dei criteri di integrità o dei criteri di monitoraggio. |
| --force-restart | Riavvia in modo forzato i processi durante l'aggiornamento anche quando la versione del codice non è stata modificata. |
| --health-check-retry-timeout | Intervallo di tempo tra i tentativi di esecuzione dei controlli integrità se l'applicazione o il cluster non è integro.  Impostazione predefinita\: PT0H10M0S. |
| --health-check-stable-duration | Tempo di attesa per cui l'applicazione o il cluster devono rimanere integri prima di passare al dominio di aggiornamento successivo.  Impostazione predefinita\: PT0H2M0S. <br><br> Viene prima interpretato come stringa che rappresenta una durata ISO 8601. Se l'esito è negativo, viene interpretato come numero che rappresenta il numero totale di millisecondi. |
| --health-check-wait-duration | Intervallo di tempo di attesa dopo il completamento di un dominio di aggiornamento prima di avviare il processo dei controlli integrità.  Impostazione predefinita\: 0. |
| --max-unhealthy-apps | Percentuale massima consentita di applicazioni distribuite non integre. Rappresentato come un numero compreso tra 0 e 100. |
| --mode | La modalità usata per monitorare l'integrità durante un aggiornamento in sequenza.  Impostazione predefinita\: UnmonitoredAuto. |
| --replica-set-check-timeout | Tempo massimo per bloccare l'elaborazione di un dominio di aggiornamento ed evitare la perdita di disponibilità quando si verificano problemi imprevisti. Il valore è espresso in secondi. |
| --service-health-policy | Mappa con codifica JSON con criteri di integrità del tipo di servizio per ogni nome di tipo di servizio. Per impostazione predefinita la mappa è vuota. |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |
| --upgrade-domain-timeout | Tempo necessario al completamento di ogni dominio di aggiornamento prima dell'esecuzione di FailureAction.  Impostazione predefinita\: P10675199DT02H48M05.4775807S. <br><br> Viene prima interpretato come stringa che rappresenta una durata ISO 8601. Se l'esito è negativo, viene interpretato come numero che rappresenta il numero totale di millisecondi. |
| --upgrade-timeout | Tempo necessario al completamento dell'aggiornamento prima dell'esecuzione di FailureAction.  Impostazione predefinita\: P10675199DT02H48M05.4775807S. <br><br> Viene prima interpretato come stringa che rappresenta una durata ISO 8601. Se l'esito è negativo, viene interpretato come numero che rappresenta il numero totale di millisecondi. |
| --warning-as-error | Indica se gli avvisi vengono considerati con lo stesso livello di gravità degli errori. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-application-upgrade-resume"></a>sfctl application upgrade-resume
Riprende l'aggiornamento di un'applicazione nel cluster di Service Fabric.

Riprende l'aggiornamento manuale non monitorato di un'applicazione di Service Fabric. Service Fabric aggiorna un dominio di aggiornamento alla volta. Per gli aggiornamenti manuali non monitorati, dopo che Service Fabric ha terminato un dominio di aggiornamento, attende la chiamata a questa API prima di procedere al dominio di aggiornamento successivo.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --application-id [obbligatorio] | Identità dell'applicazione. Si tratta in genere del nome completo dell'applicazione senza lo schema URI "fabric\:". A partire dalla versione 6.0, i nomi gerarchici sono delimitati dal carattere "\~". Se ad esempio il nome dell'applicazione è "fabric\:/myapp/app1", l'identità dell'applicazione sarà "myapp\~app1" nella versione 6.0 e successive e "myapp/app1" nelle versioni precedenti. |
| --upgrade-domain-name [Obbligatorio] | Nome del dominio di aggiornamento in cui si vuole riprendere l'aggiornamento. |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-application-upgrade-rollback"></a>sfctl application upgrade-rollback
Avvia il rollback dell'aggiornamento attualmente in corso di un'applicazione nel cluster di Service Fabric.

Avvia il rollback dell'aggiornamento dell'applicazione corrente alla versione precedente. Questa API può essere usata solo per eseguire il rollback dell'aggiornamento corrente in corso, ovvero il roll forward alla nuova versione. Se attualmente non è in corso l'aggiornamento dell'applicazione, usare l'API StartApplicationUpgrade per aggiornarla alla versione desiderata, incluso il roll back a una versione precedente.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --application-id [obbligatorio] | Identità dell'applicazione. Si tratta in genere del nome completo dell'applicazione senza lo schema URI "fabric\:". A partire dalla versione 6.0, i nomi gerarchici sono delimitati dal carattere "\~". Ad esempio, se il nome dell'applicazione è "fabric\:/myapp/app1", l'identità dell'applicazione sarà "myapp\~app1" nella versione 6.0 e successive e "myapp/app1" nelle versioni precedenti. |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-application-upgrade-status"></a>sfctl application upgrade-status
Mostra i dettagli dell'aggiornamento più recente eseguito sull'applicazione.

Restituisce informazioni sullo stato dell'aggiornamento più recente dell'applicazione, con dettagli che agevolano il debug dei problemi di integrità dell'applicazione.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --application-id [obbligatorio] | Identità dell'applicazione. Si tratta in genere del nome completo dell'applicazione senza lo schema URI "fabric\:". A partire dalla versione 6.0, i nomi gerarchici sono delimitati dal carattere "\~". Ad esempio, se il nome dell'applicazione è "fabric\:/myapp/app1", l'identità dell'applicazione sarà "myapp\~app1" nella versione 6.0 e successive e "myapp/app1" nelle versioni precedenti. |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-application-upload"></a>sfctl application upload
Copia il pacchetto dell'applicazione di Service Fabric nell'archivio immagini.

Facoltativamente, è possibile visualizzare lo stato di caricamento per ogni file nel pacchetto. Lo stato di caricamento viene inviato a `stderr`.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --path [Obbligatorio] | Percorso del pacchetto di applicazione locale. |
| --imagestore-string | Archivio di immagini di destinazione in cui caricare il pacchetto dell'applicazione.  Impostazione predefinita\: fabric\:ImageStore. |
| --show-progress | Mostra lo stato di caricamento del file per i pacchetti di grandi dimensioni. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |


## <a name="next-steps"></a>Passaggi successivi
- [Configurare](service-fabric-cli.md) l'interfaccia della riga di comando di Service Fabric.
- Informazioni su come usare l'interfaccia della riga di comando Service Fabric usando gli [script di esempio](/azure/service-fabric/scripts/sfctl-upgrade-application).
