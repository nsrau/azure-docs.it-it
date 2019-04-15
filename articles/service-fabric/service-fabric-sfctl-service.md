---
title: Interfaccia della riga di comando Azure Service Fabric - sfctl service | Microsoft Docs
description: Descrive i comandi dell'interfaccia della riga di comando Service Fabric sfctl service.
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
ms.openlocfilehash: e0454d0124efba04434884fbac9056c5e324710d
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58670881"
---
# <a name="sfctl-service"></a>sfctl service
Creare, eliminare e gestire servizi, tipi di servizio e pacchetti di servizi.

## <a name="commands"></a>Comandi:

|Comando|DESCRIZIONE|
| --- | --- |
| app-name | Mostra il nome dell'applicazione Service Fabric per un servizio. |
| code-package-list | Mostra l'elenco di pacchetti di codice distribuiti in un nodo di Service Fabric. |
| create | Crea il servizio di Service Fabric specificato. |
| delete | Elimina un servizio Service Fabric esistente. |
| deployed-type | Ottiene le informazioni inerenti a un tipo di servizio specifico dell'applicazione distribuito in un nodo di un cluster di Service Fabric. |
| deployed-type-list | Ottiene l'elenco contenente le informazioni sui tipi di servizio delle applicazioni distribuiti in un nodo di un cluster di Service Fabric. |
| description | Mostra la descrizione di un servizio di Service Fabric esistente. |
| get-container-logs | Ottiene i log dei contenitori per il contenitore distribuito in un nodo di Service Fabric. |
| health | Mostra l'integrità del servizio di Service Fabric specificato. |
| info | Ottiene le informazioni sul servizio specifico appartenente all'applicazione di Service Fabric. |
| list | Mostra le informazioni su tutti i servizi appartenenti all'applicazione specificata dall'ID dell'applicazione. |
| manifest | Mostra il manifesto che descrive un tipo di servizio. |
| package-deploy | Scarica i pacchetti associati al manifesto del servizio specificato per la cache delle immagini nel nodo specifico. |
| package-health | Ottiene le informazioni sull'integrità di un pacchetto di servizi per un'applicazione specifica distribuita per un nodo e un'applicazione di Service Fabric. |
| package-info | Ottiene l'elenco di pacchetti di servizi distribuiti su un nodo di Service Fabric e corrispondenti esattamente al nome specificato. |
| package-list | Mostra l'elenco di pacchetti di servizi distribuiti in un nodo di Service Fabric. |
| recover | Indica al cluster di Service Fabric che deve tentare di ripristinare il servizio specificato, attualmente bloccato in una perdita di quorum. |
| report-health | Invia un report di integrità sul servizio di Service Fabric. |
| resolve | per risolvere una partizione di Service Fabric. |
| type-list | Ottiene l'elenco contenente le informazioni sui tipi di servizio supportati da un tipo di applicazione distribuito in un cluster di Service Fabric. |
| update | Aggiorna il servizio specificato usando la descrizione di aggiornamento specificata. |

## <a name="sfctl-service-app-name"></a>sfctl service app-name
Mostra il nome dell'applicazione Service Fabric per un servizio.

Ottiene il nome dell'applicazione per il servizio specificato. Se un servizio con l'ID servizio specificato non esiste, viene restituito un errore A 404 FABRIC_E_SERVICE_DOES_NOT_EXIST.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --service-id [obbligatorio] | L'identità del servizio. L'ID corrisponde in genere al nome completo del servizio senza lo schema URI "fabric\:". A partire dalla versione 6.0, i nomi gerarchici sono delimitati dal carattere "\~". Ad esempio, se il nome del servizio è "fabric\:/myapp/app1/svc1", l'identità del servizio sarà "myapp\~app1\~svc1" nella versione 6.0 e successive e "myapp/app1/svc1" nelle versioni precedenti. |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-service-code-package-list"></a>sfctl service code-package-list
Mostra l'elenco di pacchetti di codice distribuiti in un nodo di Service Fabric.

Ottiene l'elenco di pacchetti di codice distribuiti in un nodo di Service Fabric per l'applicazione specificata.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --application-id [obbligatorio] | Identità dell'applicazione. Si tratta in genere del nome completo dell'applicazione senza lo schema URI "fabric\:". A partire dalla versione 6.0, i nomi gerarchici sono delimitati dal carattere "\~". Ad esempio, se il nome dell'applicazione è "fabric\:/myapp/app1", l'identità dell'applicazione sarà "myapp\~app1" nella versione 6.0 e successive e "myapp/app1" nelle versioni precedenti. |
| --node-name [obbligatorio] | Il nome del nodo. |
| --code-package-name | Nome di un pacchetto di codice specificato nel manifesto del servizio registrato come parte di un tipo di applicazione in un cluster di Service Fabric. |
| --service-manifest-name | Nome di un manifesto del servizio registrato come parte di un tipo di applicazione in un cluster di Service Fabric. |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-service-create"></a>sfctl service create
Crea il servizio di Service Fabric specificato.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --app-id       [obbligatorio] | Identità dell'applicazione. Si tratta in genere del nome completo dell'applicazione senza lo schema URI "fabric\:". A partire dalla versione 6.0, i nomi gerarchici sono delimitati con il carattere "\~". Se ad esempio il nome dell'applicazione è "fabric\:/myapp/app1", l'identità dell'applicazione sarà "myapp\~app1" nella versione 6.0 e successive e "myapp/app1" nelle versioni precedenti. |
| --name         [obbligatorio] | Nome del servizio. Deve trattarsi di un elemento figlio dell'ID dell'applicazione. Corrisponde al nome intero, incluso l'URI `fabric\:`. Ad esempio, il servizio `fabric\:/A/B` è un elemento figlio dell'applicazione `fabric\:/A`. |
| --service-type [obbligatorio] | Nome del tipo di servizio. |
| --activation-mode | Modalità di attivazione del pacchetto di servizi. |
| --constraints | Vincoli di posizionamento sotto forma di stringa. I vincoli di posizionamento sono espressioni booleane nelle proprietà del nodo e consentono di limitare un servizio a nodi specifici in base ai requisiti del servizio. Ad esempio, per inserire un servizio nei nodi in cui NodeType è blu, specificare \:"NodeColor == blue". |
| --correlated-service | Nome del servizio di destinazione con cui creare una corrispondenza. |
| --correlation | Serve a correlare il servizio con un servizio esistente usando un'affinità di allineamento. |
| --dns-name | Nome DNS del servizio da creare. Il servizio di sistema DNS di Service Fabric deve essere abilitato per questa impostazione. |
| --instance-count | Numero di istanze. Si applica solo a servizi senza stato. |
| --int-scheme | Indica che il servizio deve essere partizionato in modo uniforme tra un intervallo di interi senza segno. |
| --int-scheme-count | Numero di partizioni all'interno di tale intervallo di interi chiave da creare se si usa uno schema di partizione di interi uniforme. |
| --int-scheme-high | Fine dell'intervallo di interi chiave, se si usa uno schema di partizione interi uniforme. |
| --int-scheme-low | Inizio dell'intervallo di interi chiave, se si usa uno schema di partizione interi uniforme. |
| --load-metrics | Elenco con codifica JSON di metriche usate quando si esegue il bilanciamento del carico di servizi tra nodi. |
| --min-replica-set-size | Dimensioni minime di un set di repliche sotto forma di numero. Si applica solo a servizi con stato. |
| --move-cost | Specifica il costo di spostamento per il servizio. I valori possibili sono \: "Zero", "Low", "Medium", "High". |
| --named-scheme | Indica che il servizio deve disporre di più partizioni denominate. |
| --named-scheme-list | Elenco con codifica JSON di nomi per eseguire la partizione del servizio, se si usa lo schema di partizione denominato. |
| --no-persisted-state | Se True, indica che il servizio non ha alcuno stato persistente archiviato sul disco locale o che archivia lo stato solo in memoria. |
| --placement-policy-list | Elenco con codifica JSON dei criteri di selezione per il servizio e di eventuali nomi di dominio associati. I criteri possono essere uno o più: \: `NonPartiallyPlaceService`, `PreferPrimaryDomain`, `RequireDomain`, `RequireDomainDistribution`. |
| --quorum-loss-wait | Durata massima in secondi per cui una partizione può trovarsi in uno stato di perdita del quorum. Si applica solo a servizi con stato. |
| --replica-restart-wait | Durata in secondi tra il momento in cui si arresta una replica e la creazione di una nuova replica. Si applica solo a servizi con stato. |
| --scaling-policies | Elenco con codifica JSON dei criteri di ridimensionamento per questo servizio. |
| --singleton-scheme | Indica che il servizio deve avere una singola partizione o essere un servizio non partizionato. |
| --stand-by-replica-keep | Durata massima in secondi per cui le repliche StandBy vengono mantenute prima di essere rimosse. Si applica solo a servizi con stato. |
| --stateful | Indica un servizio con stato. |
| --stateless | Indica un servizio senza stato. |
| --target-replica-set-size | Dimensioni di un set di repliche di destinazione sotto forma di numero. Si applica solo a servizi con stato. |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-service-delete"></a>sfctl service delete
Elimina un servizio Service Fabric esistente.

Prima di poter essere eliminato, un servizio deve essere creato. Per impostazione predefinita, Service Fabric tenterà di chiudere le repliche del servizio normalmente, per poi eliminare il servizio. Tuttavia, se nel servizio si verificano dei problemi chiudendo normalmente la replica, l'operazione di eliminazione potrebbe richiedere molto tempo o bloccarsi. Usare il flag facoltativo ForceRemove per ignorare la normale sequenza di chiusura ed eliminare il servizio in modo forzato.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --service-id [obbligatorio] | L'identità del servizio. L'ID corrisponde in genere al nome completo del servizio senza lo schema URI "fabric\:". A partire dalla versione 6.0, i nomi gerarchici sono delimitati dal carattere "\~". Ad esempio, se il nome del servizio è "fabric\:/myapp/app1/svc1", l'identità del servizio sarà "myapp\~app1\~svc1" nella versione 6.0 e successive e "myapp/app1/svc1" nelle versioni precedenti. |
| --force-remove | Consente di rimuovere un servizio o un'applicazione di Service Fabric in modo forzato senza passare attraverso la sequenza di arresto normale. Questo parametro può essere usato per eliminare in modo forzato un'applicazione o un servizio per il quale l'operazione di eliminazione è prossima al timeout a causa di problemi nel codice del servizio che impediscono la normale chiusura delle repliche. |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-service-deployed-type"></a>sfctl service deployed-type
Ottiene le informazioni inerenti a un tipo di servizio specifico dell'applicazione distribuito in un nodo di un cluster di Service Fabric.

Ottiene l'elenco contenente le informazioni su un tipo di servizio specifico dalle applicazioni distribuite in un nodo di un cluster di Service Fabric. La risposta include il nome del tipo di servizio e il relativo stato di registrazione, il pacchetto di codice che lo ha registrato e l'ID di attivazione del pacchetto del servizio. Ogni voce rappresenta un'attivazione di un tipo di servizio, che si differenzia per ID di attivazione.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --application-id    [obbligatorio] | Identità dell'applicazione. Si tratta in genere del nome completo dell'applicazione senza lo schema URI "fabric\:". A partire dalla versione 6.0, i nomi gerarchici sono delimitati dal carattere "\~". Ad esempio, se il nome dell'applicazione è "fabric\:/myapp/app1", l'identità dell'applicazione sarà "myapp\~app1" nella versione 6.0 e successive e "myapp/app1" nelle versioni precedenti. |
| --node-name         [obbligatorio] | Il nome del nodo. |
| --service-type-name [obbligatorio] | Specifica il nome di un tipo di servizio di Service Fabric. |
| --service-manifest-name | Nome del manifesto del servizio da usare per filtrare l'elenco delle informazioni sul tipo di servizio distribuito. Se specificato, la risposta conterrà solo le informazioni sui tipi di servizio definiti in questo manifesto del servizio. |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-service-deployed-type-list"></a>sfctl service deployed-type-list
Ottiene l'elenco contenente le informazioni sui tipi di servizio delle applicazioni distribuiti in un nodo di un cluster di Service Fabric.

Ottiene l'elenco contenente le informazioni sui tipi di servizio delle applicazioni distribuiti in un nodo di un cluster di Service Fabric. La risposta include il nome del tipo di servizio e il relativo stato di registrazione, il pacchetto di codice che lo ha registrato e l'ID di attivazione del pacchetto del servizio.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --application-id [obbligatorio] | Identità dell'applicazione. Si tratta in genere del nome completo dell'applicazione senza lo schema URI "fabric\:". A partire dalla versione 6.0, i nomi gerarchici sono delimitati dal carattere "\~". Ad esempio, se il nome dell'applicazione è "fabric\:/myapp/app1", l'identità dell'applicazione sarà "myapp\~app1" nella versione 6.0 e successive e "myapp/app1" nelle versioni precedenti. |
| --node-name [obbligatorio] | Il nome del nodo. |
| --service-manifest-name | Nome del manifesto del servizio da usare per filtrare l'elenco delle informazioni sul tipo di servizio distribuito. Se specificato, la risposta conterrà solo le informazioni sui tipi di servizio definiti in questo manifesto del servizio. |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-service-description"></a>Descrizione del servizio sfctl
Mostra la descrizione di un servizio di Service Fabric esistente.

Mostra la descrizione di un servizio di Service Fabric esistente. Prima di poter ottenere la descrizione, è necessario creare un servizio.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --service-id [obbligatorio] | L'identità del servizio. L'ID corrisponde in genere al nome completo del servizio senza lo schema URI "fabric\:". A partire dalla versione 6.0, i nomi gerarchici sono delimitati dal carattere "\~". Ad esempio, se il nome del servizio è "fabric\:/myapp/app1/svc1", l'identità del servizio sarà "myapp\~app1\~svc1" nella versione 6.0 e successive e "myapp/app1/svc1" nelle versioni precedenti. |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-service-get-container-logs"></a>sfctl service get-container-logs
Ottiene i log dei contenitori per il contenitore distribuito in un nodo di Service Fabric.

Ottiene i log dei contenitori per il contenitore distribuito in un nodo di Service Fabric per il pacchetto di codice specificato.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --application-id        [obbligatorio] | Identità dell'applicazione. Si tratta in genere del nome completo dell'applicazione senza lo schema URI "fabric\:". A partire dalla versione 6.0, i nomi gerarchici sono delimitati dal carattere "\~". Ad esempio, se il nome dell'applicazione è "fabric\:/myapp/app1", l'identità dell'applicazione sarà "myapp\~app1" nella versione 6.0 e successive e "myapp/app1" nelle versioni precedenti. |
| --code-package-name     [obbligatorio] | Nome di un pacchetto di codice specificato nel manifesto del servizio registrato come parte di un tipo di applicazione in un cluster di Service Fabric. |
| --node-name             [obbligatorio] | Il nome del nodo. |
| --service-manifest-name [obbligatorio] | Nome di un manifesto del servizio registrato come parte di un tipo di applicazione in un cluster di Service Fabric. |
| --previous | Specifica se ottenere i log dei contenitori da contenitori dell'istanza del pacchetto di codice da cui si è usciti/che non sono usati. |
| --tail | Numero di righe da visualizzare alla fine dei log. Il valore predefinito è 100. "all" per visualizzare i log completi. |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-service-health"></a>Integrità dei servizi sfctl
Mostra l'integrità del servizio di Service Fabric specificato.

Mostra le informazioni di integrità del servizio specificato. Usare EventsHealthStateFilter per filtrare la raccolta di eventi di stato riportati nel servizio in base allo stato di integrità. Usare PartitionsHealthStateFilter per filtrare la raccolta di partizioni restituita. Se si specifica un servizio che non esiste nell'archivio integrità, questa richiesta restituisce un errore.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --service-id          [obbligatorio] | L'identità del servizio. L'ID corrisponde in genere al nome completo del servizio senza lo schema URI "fabric\:". A partire dalla versione 6.0, i nomi gerarchici sono delimitati dal carattere "\~". Ad esempio, se il nome del servizio è "fabric\:/myapp/app1/svc1", l'identità del servizio sarà "myapp\~app1\~svc1" nella versione 6.0 e successive e "myapp/app1/svc1" nelle versioni precedenti. |
| --events-health-state-filter | Consente di filtrare la raccolta di oggetti HealthEvent restituiti in base allo stato di integrità. I valori possibili per questo parametro includono il valore intero di uno dei seguenti stati di integrità. Vengono restituiti solo gli eventi che corrispondono al filtro. Tutti gli eventi vengono usati per valutare lo stato di integrità aggregato. Se non specificato diversamente, vengono restituite tutte le voci. I valori dello stato sono enumerati in base al flag, pertanto il valore potrebbe essere una combinazione di questi valori ottenuti usando l'operatore "OR" bit per bit. Ad esempio, se il valore fornito è 6, vengono restituiti tutti gli eventi con valore dello stato di integrità OK (2) e di Avviso (4).  <br> - Default - Valore predefinito. Consente di ricercare qualsiasi stato di integrità. Il valore predefinito è zero.  <br> - None - Il filtro non corrisponde ad alcun valore di stato di integrità. Usato per non restituire alcun risultato in un determinato insieme di stati. Il valore è uguale a 1.  <br> -Ok - Filtro che ricerca le corrispondenze di input con valore di stato di integrità Ok. Il valore è 2.  <br> -Warning - filtro che ricerca le corrispondenze di input con valore di stato di integrità Avviso. Il valore è 4.  <br> - Error - Filtro che ricerca le corrispondenze di input con valore di stato di integrità Errore. Il valore è 8.  <br> -All - Filtro che ricerca le corrispondenze di input con qualsiasi valore di stato di integrità. Il valore è 65535. |
| --exclude-health-statistics | Indica se le statistiche di integrità devono essere restituite come parte del risultato della query. False per impostazione predefinita. Le statistiche indicano il numero di entità figlio il cui stato di integrità è Ok, Avviso ed Errore. |
| --partitions-health-state-filter | Filtra gli oggetti dello stato di integrità delle partizioni restituiti nel risultato della query sull'integrità del servizio in base al relativo stato di integrità. I valori possibili per questo parametro includono il valore intero di uno dei seguenti stati di integrità. Vengono restituite solo partizioni che corrispondono al filtro. Tutte le partizioni vengono usate per valutare lo stato di integrità aggregato. Se non specificato diversamente, vengono restituite tutte le voci. I valori dello stato sono enumerati in base al flag, pertanto il valore potrebbe essere una combinazione di questi valori ottenuti usando l'operatore "OR" bit per bit. Ad esempio, se il valore specificato è 6, vengono restituite le partizioni con valore dello stato di integrità OK (2) e di Avviso (4).  <br> - Default - Valore predefinito. Consente di ricercare qualsiasi stato di integrità. Il valore predefinito è zero.  <br> - None - Il filtro non corrisponde ad alcun valore di stato di integrità. Usato per non restituire alcun risultato in un determinato insieme di stati. Il valore è uguale a 1.  <br> -Ok - Filtro che ricerca le corrispondenze di input con valore di stato di integrità Ok. Il valore è 2.  <br> -Warning - filtro che ricerca le corrispondenze di input con valore di stato di integrità Avviso. Il valore è 4.  <br> - Error - Filtro che ricerca le corrispondenze di input con valore di stato di integrità Errore. Il valore è 8.  <br> -All - Filtro che ricerca le corrispondenze di input con qualsiasi valore di stato di integrità. Il valore è 65535. |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-service-info"></a>sfctl service info
Ottiene le informazioni sul servizio specifico appartenente all'applicazione di Service Fabric.

Restituisce le informazioni sul servizio specifico appartenente all'applicazione di Service Fabric specificata.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --application-id [obbligatorio] | Identità dell'applicazione. Si tratta in genere del nome completo dell'applicazione senza lo schema URI "fabric\:". A partire dalla versione 6.0, i nomi gerarchici sono delimitati dal carattere "\~". Ad esempio, se il nome dell'applicazione è "fabric\:/myapp/app1", l'identità dell'applicazione sarà "myapp\~app1" nella versione 6.0 e successive e "myapp/app1" nelle versioni precedenti. |
| --service-id             [obbligatorio] | L'identità del servizio. L'ID corrisponde in genere al nome completo del servizio senza lo schema URI "fabric\:". A partire dalla versione 6.0, i nomi gerarchici sono delimitati dal carattere "\~". Ad esempio, se il nome del servizio è "fabric\:/myapp/app1/svc1", l'identità del servizio sarà "myapp\~app1\~svc1" nella versione 6.0 e successive e "myapp/app1/svc1" nelle versioni precedenti. |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-service-list"></a>sfctl service list
Mostra le informazioni su tutti i servizi appartenenti all'applicazione specificata dall'ID dell'applicazione.

Restituisce le informazioni su tutti i servizi appartenenti all'applicazione specificata dall'ID dell'applicazione.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --application-id [obbligatorio] | Identità dell'applicazione. Si tratta in genere del nome completo dell'applicazione senza lo schema URI "fabric\:". A partire dalla versione 6.0, i nomi gerarchici sono delimitati dal carattere "\~". Ad esempio, se il nome dell'applicazione è "fabric\:/myapp/app1", l'identità dell'applicazione sarà "myapp\~app1" nella versione 6.0 e successive e "myapp/app1" nelle versioni precedenti. |
| --continuation-token | Il parametro del token di continuazione viene utilizzato per ottenere il set di risultati successivo. Un token di continuazione con un valore non vuoto è incluso nella risposta dell'API quando i risultati del sistema non rientrano in una singola risposta. Quando questo valore viene passato alla successiva chiamata API, l'API restituisce il set di risultati successivo. Se non sono presenti altri risultati, il token di continuazione non contiene alcun valore. Il valore di questo parametro non deve essere codificato in URL. |
| --service-type-name | Nome del tipo servizio usato per filtrare i servizi da cercare. |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-service-manifest"></a>sfctl service manifest
Mostra il manifesto che descrive un tipo di servizio.

Mostra il manifesto che descrive un tipo di servizio. La risposta contiene il manifesto del servizio XML sotto forma di stringa.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --application-type-name    [obbligatorio] | Nome del tipo di applicazione. |
| --application-type-version [obbligatorio] | Versione del tipo di applicazione. |
| --service-manifest-name    [obbligatorio] | Nome di un manifesto del servizio registrato come parte di un tipo di applicazione in un cluster di Service Fabric. |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-service-package-deploy"></a>sfctl service package-deploy
Scarica i pacchetti associati al manifesto del servizio specificato per la cache delle immagini nel nodo specifico.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --app-type-name         [obbligatorio] | Nome del manifesto dell'applicazione per il manifesto del servizio richiesto corrispondente. |
| --app-type-version      [obbligatorio] | Versione del manifesto dell'applicazione per il manifesto del servizio richiesto corrispondente. |
| --node-name             [obbligatorio] | Il nome del nodo. |
| --service-manifest-name [obbligatorio] | Nome del manifesto del servizio associato ai pacchetti che verranno scaricati. |
| --share-policy | Elenco con codifica JSON dei criteri di condivisione. Ogni elemento dei criteri di condivisione è costituito da un elemento "name" e "scope". Il nome corrisponde al nome del codice, alla configurazione o al pacchetto dati che deve essere condiviso. L'ambito può avere il valore "None", "All", "Code", "Config" o "Data". |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-service-package-health"></a>sfctl service package-health
Ottiene le informazioni sull'integrità di un pacchetto di servizi per un'applicazione specifica distribuita per un nodo e un'applicazione di Service Fabric.

Ottiene le informazioni sull'integrità di un pacchetto di servizi per una specifica applicazione distribuita in un nodo di Service Fabric. Usare EventsHealthStateFilter per eventualmente filtrare la raccolta di oggetti HealthEvent segnalati nel pacchetto del servizio distribuito in base allo stato di integrità.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --application-id       [obbligatorio] | Identità dell'applicazione. Si tratta in genere del nome completo dell'applicazione senza lo schema URI "fabric\:". A partire dalla versione 6.0, i nomi gerarchici sono delimitati dal carattere "\~". Ad esempio, se il nome dell'applicazione è "fabric\:/myapp/app1", l'identità dell'applicazione sarà "myapp\~app1" nella versione 6.0 e successive e "myapp/app1" nelle versioni precedenti. |
| --node-name            [obbligatorio] | Il nome del nodo. |
| --service-package-name [obbligatorio] | Nome del pacchetto del servizio. |
| --events-health-state-filter | Consente di filtrare la raccolta di oggetti HealthEvent restituiti in base allo stato di integrità. I valori possibili per questo parametro includono il valore intero di uno dei seguenti stati di integrità. Vengono restituiti solo gli eventi che corrispondono al filtro. Tutti gli eventi vengono usati per valutare lo stato di integrità aggregato. Se non specificato diversamente, vengono restituite tutte le voci. I valori dello stato sono enumerati in base al flag, pertanto il valore potrebbe essere una combinazione di questi valori ottenuti usando l'operatore "OR" bit per bit. Ad esempio, se il valore fornito è 6, vengono restituiti tutti gli eventi con valore dello stato di integrità OK (2) e di Avviso (4).  <br> - Default - Valore predefinito. Consente di ricercare qualsiasi stato di integrità. Il valore predefinito è zero.  <br> - None - Il filtro non corrisponde ad alcun valore di stato di integrità. Usato per non restituire alcun risultato in un determinato insieme di stati. Il valore è uguale a 1.  <br> -Ok - Filtro che ricerca le corrispondenze di input con valore di stato di integrità Ok. Il valore è 2.  <br> -Warning - filtro che ricerca le corrispondenze di input con valore di stato di integrità Avviso. Il valore è 4.  <br> - Error - Filtro che ricerca le corrispondenze di input con valore di stato di integrità Errore. Il valore è 8.  <br> -All - Filtro che ricerca le corrispondenze di input con qualsiasi valore di stato di integrità. Il valore è 65535. |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-service-package-info"></a>sfctl service package-info
Ottiene l'elenco di pacchetti di servizi distribuiti su un nodo di Service Fabric e corrispondenti esattamente al nome specificato.

Restituisce le informazioni sui pacchetti del servizio distribuiti in un nodo di Service Fabric per l'applicazione specificata. Questi risultati si riferiscono ai pacchetti del servizio il cui nome corrisponde esattamente al nome del pacchetto del servizio specificato come parametro.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --application-id       [obbligatorio] | Identità dell'applicazione. Si tratta in genere del nome completo dell'applicazione senza lo schema URI "fabric\:". A partire dalla versione 6.0, i nomi gerarchici sono delimitati dal carattere "\~". Ad esempio, se il nome dell'applicazione è "fabric\:/myapp/app1", l'identità dell'applicazione sarà "myapp\~app1" nella versione 6.0 e successive e "myapp/app1" nelle versioni precedenti. |
| --node-name            [obbligatorio] | Il nome del nodo. |
| --service-package-name [obbligatorio] | Nome del pacchetto del servizio. |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-service-package-list"></a>sfctl service package-list
Mostra l'elenco di pacchetti di servizi distribuiti in un nodo di Service Fabric.

Restituisce le informazioni sui pacchetti del servizio distribuiti in un nodo di Service Fabric per l'applicazione specificata.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --application-id [obbligatorio] | Identità dell'applicazione. Si tratta in genere del nome completo dell'applicazione senza lo schema URI "fabric\:". A partire dalla versione 6.0, i nomi gerarchici sono delimitati dal carattere "\~". Ad esempio, se il nome dell'applicazione è "fabric\:/myapp/app1", l'identità dell'applicazione sarà "myapp\~app1" nella versione 6.0 e successive e "myapp/app1" nelle versioni precedenti. |
| --node-name [obbligatorio] | Il nome del nodo. |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-service-recover"></a>sfctl service recover
Indica al cluster di Service Fabric che deve tentare di ripristinare il servizio specificato, attualmente bloccato in una perdita di quorum.

Indica al cluster di Service Fabric che deve tentare di ripristinare il servizio specificato, attualmente bloccato in una perdita di quorum. Questa operazione può essere eseguita solo se è noto che non è possibile recuperare le repliche che non sono attive. Un uso non corretto di questa API può causare una perdita di dati.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --service-id [obbligatorio] | L'identità del servizio. L'ID corrisponde in genere al nome completo del servizio senza lo schema URI "fabric\:". A partire dalla versione 6.0, i nomi gerarchici sono delimitati dal carattere "\~". Ad esempio, se il nome del servizio è "fabric\:/myapp/app1/svc1", l'identità del servizio sarà "myapp\~app1\~svc1" nella versione 6.0 e successive e "myapp/app1/svc1" nelle versioni precedenti. |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-service-report-health"></a>sfctl service report-health
Invia un report di integrità sul servizio di Service Fabric.

Segnala lo stato di integrità del servizio di Service Fabric specificato. Il report deve contenere le informazioni relative all'origine del report sull'integrità e alla proprietà in cui viene segnalato. Il report viene inviato a un servizio del gateway di Service Fabric, che lo inoltra all'archivio integrità. Il report può essere accettato dal gateway, ma rifiutato dall'archivio integrità dopo la convalida aggiuntiva. L'archivio integrità, ad esempio, può rifiutare il report a causa di un parametro non valido, come un numero di sequenza non aggiornato. Per determinare se il report è stato applicato nell'archivio integrità, controllare che il report venga visualizzato negli eventi di integrità del servizio.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --health-property [obbligatorio] | Proprietà delle informazioni sull'integrità. <br><br> Un'entità può avere report sull'integrità per proprietà diverse. La proprietà è una stringa e non un'enumerazione fissa, che assicura al reporter la flessibilità necessaria per definire la categoria della condizione di stato che attiva il report. Ad esempio, un reporter con SourceId "LocalWatchdog" può monitorare lo stato del disco disponibile in un nodo e può quindi segnalare la proprietà "AvailableDisk" in tale nodo. Lo stesso reporter può monitorare la connettività del nodo e può quindi segnalare una proprietà "Connectivity" nello stesso nodo. Entrambi questi report vengono considerati come eventi di integrità separati nell'archivio integrità per il nodo specificato. Insieme a SourceId, la proprietà identifica in modo univoco le informazioni sull'integrità. |
| --health-state    [obbligatorio] | I valori possibili sono\: "Invalid", "Ok", "Warning", "Error", "Unknown". |
| --service-id      [obbligatorio] | L'identità del servizio. <br><br> Si tratta in genere del nome completo del servizio senza lo schema URI "fabric\:". A partire dalla versione 6.0, i nomi gerarchici sono delimitati dal carattere "\~". Ad esempio, se il nome del servizio è "fabric\:/myapp/app1/svc1", l'identità del servizio sarà "myapp\~app1\~svc1" nella versione 6.0 e successive e "myapp/app1/svc1" nelle versioni precedenti. |
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

## <a name="sfctl-service-resolve"></a>sfctl service resolve
Consente di risolvere una partizione di Service Fabric.

Consente di risolvere una partizione del servizio di Service Fabric per ottenere gli endpoint delle repliche del servizio.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --service-id [obbligatorio] | L'identità del servizio. L'ID corrisponde in genere al nome completo del servizio senza lo schema URI "fabric\:". A partire dalla versione 6.0, i nomi gerarchici sono delimitati dal carattere "\~". Ad esempio, se il nome del servizio è "fabric\:/myapp/app1/svc1", l'identità del servizio sarà "myapp\~app1\~svc1" nella versione 6.0 e successive e "myapp/app1/svc1" nelle versioni precedenti. |
| --partition-key-type | Tipo di chiave per la partizione. Questo parametro è obbligatorio se lo schema di partizione per il servizio è Int64Range o Named. I valori possibili sono i seguenti. -None (1): indica che il parametro PartitionKeyValue non è specificato. È valido per le partizioni con schema di partizionamento Singleton. Si tratta del valore predefinito. Il valore è uguale a 1. -Int64Range (2): indica che il parametro PartitionKeyValue è una chiave di partizione int64. È valido per le partizioni con schema di partizionamento Int64Range. Il valore è 2. - Named (3): indica che il parametro PartitionKeyValue è un nome della partizione. È valido per le partizioni con schema di partizionamento Named. Il valore è 3. |
| --partition-key-value | Chiave di partizione. Questo parametro è obbligatorio se lo schema di partizione per il servizio è Int64Range o Named. Non si tratta dell'ID di partizione, ma piuttosto del valore chiave intero o del nome dell'ID di partizione. Se, ad esempio, il servizio usa partizioni a intervalli da 0 a 10, il valore di PartitionKeyValue sarà un numero intero compreso in tale intervallo. Eseguire una query sulla descrizione del servizio per visualizzare l'intervallo o il nome. |
| --previous-rsp-version | Valore nel campo Version della risposta ricevuta in precedenza. È obbligatorio se l'utente riconosce che il risultato ottenuto in precedenza non è aggiornato. |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-service-type-list"></a>sfctl service type-list
Ottiene l'elenco contenente le informazioni sui tipi di servizio supportati da un tipo di applicazione distribuito in un cluster di Service Fabric.

Ottiene l'elenco contenente le informazioni sui tipi di servizio supportati da un tipo di applicazione distribuito in un cluster di Service Fabric. Il tipo di applicazione specificato deve esistere. In caso contrario, viene restituito uno stato 404.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --application-type-name    [obbligatorio] | Nome del tipo di applicazione. |
| --application-type-version [obbligatorio] | Versione del tipo di applicazione. |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-service-update"></a>sfctl service update
Aggiorna il servizio specificato usando la descrizione di aggiornamento specificata.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --service-id   [obbligatorio] | L'identità del servizio. Si tratta in genere del nome completo del servizio senza lo schema URI "fabric\:". A partire dalla versione 6.0, i nomi gerarchici sono delimitati dal carattere "\~". Ad esempio, se il nome del servizio è "fabric\:/myapp/app1/svc1", l'identità del servizio sarà "myapp\~app1\~svc1" nella versione 6.0 e successive e "myapp/app1/svc1" nelle versioni precedenti. |
| --constraints | Vincoli di posizionamento sotto forma di stringa. I vincoli di posizionamento sono espressioni booleane nelle proprietà del nodo e consentono di limitare un servizio a nodi specifici in base ai requisiti del servizio. Ad esempio, per inserire un servizio nei nodi in cui NodeType è blu, specificare \: "NodeColor == blue". |
| --correlated-service | Nome del servizio di destinazione con cui creare una corrispondenza. |
| --correlation | Serve a correlare il servizio con un servizio esistente usando un'affinità di allineamento. |
| --instance-count | Numero di istanze. Si applica solo a servizi senza stato. |
| --load-metrics | Elenco con codifica JSON di metriche da usare quando si esegue il bilanciamento del carico tra nodi. |
| --min-replica-set-size | Dimensioni minime di un set di repliche sotto forma di numero. Si applica solo a servizi con stato. |
| --move-cost | Specifica il costo di spostamento per il servizio. I valori possibili sono \: "Zero", "Low", "Medium", "High". |
| --placement-policy-list | Elenco con codifica JSON dei criteri di selezione per il servizio e di eventuali nomi di dominio associati. I criteri possono essere uno o più: \: `NonPartiallyPlaceService`, `PreferPrimaryDomain`, `RequireDomain`, `RequireDomainDistribution`. |
| --quorum-loss-wait | Durata massima in secondi per cui una partizione può trovarsi in uno stato di perdita del quorum. Si applica solo a servizi con stato. |
| --replica-restart-wait | Durata in secondi tra il momento in cui si arresta una replica e la creazione di una nuova replica. Si applica solo a servizi con stato. |
| --scaling-policies | Elenco con codifica JSON dei criteri di ridimensionamento per questo servizio. |
| --stand-by-replica-keep | Durata massima in secondi per cui le repliche StandBy vengono mantenute prima di essere rimosse. Si applica solo a servizi con stato. |
| --stateful | Indica un servizio di destinazione con stato. |
| --stateless | Indica un servizio di destinazione senza stato. |
| --target-replica-set-size | Dimensioni di un set di repliche di destinazione sotto forma di numero. Si applica solo a servizi con stato. |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |

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
