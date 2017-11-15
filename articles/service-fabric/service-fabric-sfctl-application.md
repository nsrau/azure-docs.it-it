---
title: 'Interfaccia della riga di comando di Azure Service Fabric: sfctl application| Microsoft Docs'
description: Descrive i comandi dell'interfaccia della riga di comando di Service Fabric per sfctl application.
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
ms.openlocfilehash: 82d2024f567768e784d9d8697784d06b56bc08ed
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2017
---
# <a name="sfctl-application"></a>sfctl application
Consente di creare, eliminare e gestire le applicazioni e i tipi di applicazioni.

## <a name="commands"></a>Comandi:

|Comando|Descrizione|
| --- | --- |
| create       | Crea un'applicazione di Service Fabric usando la descrizione specificata.|
| delete       | Elimina un'applicazione di Service Fabric esistente.|
| deployed     | Recupera le informazioni relative a un'applicazione distribuita in un nodo di Service Fabric.|
| deployed-health | Recupera le informazioni sull'integrità di un'applicazione distribuita in un nodo Service Fabric.|
| deployed-list| Mostra l'elenco delle applicazioni distribuite in un nodo di Service Fabric.|
| health       | Mostra l'integrità dell'applicazione di Service Fabric.|
| info         | Recupera informazioni su un'applicazione di Service Fabric.|
| list         | Mostra l'elenco di applicazioni create nel cluster di Service Fabric che corrisponde ai filtri specificati come parametro.|
| load | Mostra informazioni di caricamento su un'applicazione di Service Fabric. |
| manifest     | Mostra il manifesto che descrive un tipo di applicazione.|
| provision    | Esegue il provisioning o la registrazione di un tipo di applicazione di Service Fabric con il cluster.|
| report-health| Invia un report di integrità sull'applicazione di Service Fabric.|
| type         | Mostra l'elenco dei tipi di applicazioni nel cluster di Service Fabric che corrispondono esattamente al nome specificato.|
| type-list    | Mostra l'elenco dei tipi di applicazioni nel cluster di Service Fabric.|
| unprovision  | Rimuove o annulla la registrazione di un tipo di applicazione di Service Fabric dal cluster.|
| Aggiornamento      | Avvia l'aggiornamento di un'applicazione nel cluster di Service Fabric.|
| upgrade-resume  | Riprende l'aggiornamento di un'applicazione nel cluster di Service Fabric.|
| upgrade-rollback| Avvia il rollback dell'aggiornamento attualmente in corso di un'applicazione nel cluster di Service Fabric.|
| upgrade-status  | Mostra i dettagli dell'aggiornamento più recente eseguito sull'applicazione.|
| upload       | Copia il pacchetto dell'applicazione di Service Fabric nell'archivio immagini.|

## <a name="sfctl-application-create"></a>sfctl application create
Crea un'applicazione di Service Fabric usando la descrizione specificata.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --app-name [obbligatorio]| Il nome dell'applicazione, incluso lo schema URI "fabric:".|
| -app-type [obbligatorio]| Il nome del tipo di applicazione trovato nel manifesto dell'applicazione.|
| --app-version [obbligatorio]| La versione del tipo di applicazione, come definita nel manifesto dell'applicazione.|
| --max-node-count     | Il numero massimo di nodi che Service Fabric riserva per questa applicazione. Ciò non significa che i servizi dell'applicazione vengono inseriti in tutti i nodi indicati.|
| --metrics            | Elenco con codifica JSON di descrizioni relative alla metrica di capacità dell'applicazione . Una metrica è definita come nome, associato a un set di capacità per ogni nodo in cui esiste l'applicazione.|
| --min-node-count     | Il numero minimo di nodi che Service Fabric riserva per questa applicazione. Ciò non significa che i servizi dell'applicazione vengono inseriti in tutti i nodi indicati.|
| --parameters         | Un elenco con codifica JSON di sostituzioni del parametro dell'applicazione da applicare quando si crea l'applicazione.|
| --timeout -t         | Timeout del server in secondi.  Predefinito: 60.|

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug              | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug.|
| --help -h            | Mostra questo messaggio della Guida e l'uscita.|
| --output -o          | Formato di output.  Valori consentiti: json, jsonc, tabella, tsv.  Predefinito: json.|
| --query              | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http://jmespath.org/.|
| --verbose            | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi.|

## <a name="sfctl-application-delete"></a>sfctl application delete
Elimina un'applicazione di Service Fabric esistente.

Elimina un'applicazione di Service Fabric esistente. Prima di poter essere eliminata, un'applicazione deve essere creata. Se si elimina un'applicazione, si eliminano tutti i servizi che fanno parte di tale applicazione. Per impostazione predefinita, Service Fabric tenta di chiudere le repliche del servizio in modo normale, per poi eliminare il servizio. Tuttavia, se nel servizio si verificano dei problemi chiudendo normalmente la replica, l'operazione di eliminazione potrebbe richiedere molto tempo o bloccarsi. Usare il flag facoltativo ForceRemove per ignorare la normale sequenza di chiusura ed eliminare l'applicazione e tutti i relativi servizi in modo forzato.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --application-id [obbligatorio]| Identità dell'applicazione. Si tratta in genere del nome completo dell'applicazione senza lo schema URI "fabric:". A partire dalla versione 6.0, i nomi gerarchici sono delimitati dal carattere "~". Ad esempio, se il nome dell'applicazione è "fabric://myapp/app1", l'identità dell'applicazione sarà "myapp~app1" in 6.0+ e "myapp/app1" nelle versioni precedenti.|
| --force-remove          | Consente di rimuovere un servizio o un'applicazione di Service Fabric in modo forzato senza passare attraverso la sequenza di arresto normale. Questo parametro può essere usato per eliminare in modo forzato un'applicazione o un servizio la cui eliminazione sta scadendo a causa di problemi nel codice del servizio che impediscono la normale chiusura delle repliche.|
| --timeout -t            | Timeout del server in secondi.  Predefinito: 60.|

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug                 | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug.|
| --help -h               | Mostra questo messaggio della Guida e l'uscita.|
| --output -o             | Formato di output.  Valori consentiti: json, jsonc, tabella, tsv.  Predefinito: json.|
| --query                 | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http://jmespath.org/.|
| --verbose               | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi.|

## <a name="sfctl-application-deployed"></a>sfctl application deployed
Recupera le informazioni relative a un'applicazione distribuita in un nodo di Service Fabric.
     
### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --application-id [obbligatorio]| Identità dell'applicazione. Si tratta in genere del nome completo dell'applicazione senza lo schema URI "fabric:". A partire dalla versione 6.0, i nomi gerarchici sono delimitati dal carattere "~". Ad esempio, se il nome dell'applicazione è "fabric://myapp/app1", l'identità dell'applicazione sarà "myapp~app1" in 6.0+ e "myapp/app1" nelle versioni precedenti.|
| --node-name [Obbligatorio]| Il nome del nodo.|
| --timeout -t            | Timeout del server in secondi.  Predefinito: 60.|

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug                 | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug.|
| --help -h               | Mostra questo messaggio della Guida e l'uscita.|
| --output -o             | Formato di output.  Valori consentiti: json, jsonc, tabella, tsv.  Predefinito: json.|
| --query                 | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http://jmespath.org/.|
| --verbose               | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi.|

## <a name="sfctl-application-health"></a>sfctl application health
Mostra l'integrità dell'applicazione di Service Fabric.

Restituisce lo stato di integrità dell'applicazione di Service Fabric. La risposta indica lo stato di integrità com OK, Errore o Avviso. Se l'entità non viene trovata nell'archivio integrità, viene restituito l'errore.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --application-id [obbligatorio]| Identità dell'applicazione. Si tratta in genere del nome completo dell'applicazione senza lo schema URI "fabric:". A partire dalla versione 6.0, i nomi gerarchici sono delimitati dal carattere "~". Ad esempio, se il nome dell'applicazione è "fabric://myapp/app1", l'identità dell'applicazione sarà "myapp~app1" in 6.0+ e "myapp/app1" nelle versioni precedenti.|
| --deployed-applications-health-state-filter| Consente di filtrare gli oggetti dello stato di integrità delle applicazioni distribuite restituiti nel risultato della query di integrità dell'applicazione in base al relativo stato di integrità. I valori possibili per questo parametro includono il valore intero di uno dei seguenti stati di integrità. Verranno restituite solo le applicazioni distribuite che corrispondono al filtro. Tutte le applicazioni distribuite vengono usate per valutare lo stato di integrità aggregato. Se non specificato diversamente, vengono restituite tutte le voci. I valori dello stato sono enumerati in base al flag, pertanto il valore potrebbe essere una combinazione di questi valori ottenuti usando l'operatore "OR" bit per bit. Ad esempio, se il valore indicato è 6, viene restituito lo stato di integrità delle applicazioni distribuite il cui valore di HealthState è OK (2) e Avviso (4). - Default - Valore predefinito. Consente di ricercare qualsiasi stato di integrità. Il valore predefinito è zero. - None - Il filtro non corrisponde ad alcun valore di stato di integrità. Usato per non restituire alcun risultato in un determinato insieme di stati. Il valore è uguale a 1. -Ok - Filtro che ricerca le corrispondenze di input con valore di stato di integrità Ok. Il valore è 2. -Warning - filtro che ricerca le corrispondenze di input con valore di stato di integrità Avviso. Il valore è 4. - Error - Filtro che ricerca le corrispondenze di input con valore di stato di integrità Errore. Il valore è 8. -All - Filtro che ricerca le corrispondenze di input con qualsiasi valore di stato di integrità. Il valore è 65535.|
| --events-health-state-filter            | Consente di filtrare la raccolta di oggetti HealthEvent restituiti in base allo stato di integrità. I valori possibili per questo parametro includono il valore intero di uno dei seguenti stati di integrità. Vengono restituiti solo gli eventi che corrispondono al filtro. Tutti gli eventi vengono usati per valutare lo stato di integrità aggregato. Se non specificato diversamente, vengono restituite tutte le voci. I valori dello stato sono enumerati in base al flag, pertanto il valore potrebbe essere una combinazione di questi valori ottenuti usando l'operatore "OR" bit per bit. Ad esempio, se il valore fornito è 6, vengono restituiti tutti gli eventi con valore dello stato di integrità OK (2) e di Avviso (4). - Default - Valore predefinito. Consente di ricercare qualsiasi stato di integrità. Il valore predefinito è zero. -None - Il filtro che non corrisponde ad alcun valore di HealthState. Usato per non restituire alcun risultato in un determinato insieme di stati. Il valore è uguale a 1. -Ok - Filtro che ricerca le corrispondenze di input con valore di stato di integrità Ok. Il valore è 2. -Warning - filtro che ricerca le corrispondenze di input con valore di stato di integrità Avviso. Il valore è 4. - Error - Filtro che ricerca le corrispondenze di input con valore di stato di integrità Errore. Il valore è 8. -All - Filtro che ricerca le corrispondenze di input con qualsiasi valore di stato di integrità. Il valore è 65535.|
| --exclude-health-statistics | Indica se le statistiche di integrità devono essere restituite come parte del risultato della query. False per impostazione predefinita. Le statistiche mostrano il numero di entità figlio il cui stato di integrità è Ok, Avviso ed Errore.|
| --services-health-state-filter          | Consente di filtrare gli oggetti dello stato di integrità del servizio restituiti nel risultato della query sull'integrità del servizio in base al relativo stato di integrità. I valori possibili per questo parametro includono il valore intero di uno dei seguenti stati di integrità. Vengono restituiti solo i servizi che corrispondono al filtro. Tutti i servizi vengono usati per valutare lo stato di integrità aggregato. Se non specificato diversamente, vengono restituite tutte le voci. I valori dello stato sono enumerati in base al flag, pertanto il valore potrebbe essere una combinazione di questi valori ottenuti usando l'operatore "OR" bit per bit. Ad esempio, se il valore indicato è 6, viene restituito lo stato di integrità dei servizi con valore HealthState OK (2) e Warning (4). - Default - Valore predefinito. Consente di ricercare qualsiasi stato di integrità. Il valore predefinito è zero. - None - Il filtro non corrisponde ad alcun valore di stato di integrità. Usato per non restituire alcun risultato in un determinato insieme di stati. Il valore è uguale a 1. -Ok - Filtro che ricerca le corrispondenze di input con valore di stato di integrità Ok. Il valore è 2. -Warning - filtro che ricerca le corrispondenze di input con valore di stato di integrità Avviso. Il valore è 4. - Error - Filtro che ricerca le corrispondenze di input con valore di stato di integrità Errore. Il valore è 8. -All - Filtro che ricerca le corrispondenze di input con qualsiasi valore di stato di integrità. Il valore è 65535.|
| --timeout -t                            | Timeout del server in secondi.  Predefinito: 60.|

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug                                 | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug.|
| --help -h                               | Mostra questo messaggio della Guida e l'uscita.|
| --output -o                             | Formato di output.  Valori consentiti: json, jsonc, tabella, tsv.  Predefinito: json.|
| --query                                 | Stringa di query JMESPath. Per maggiori informazioni ed esempi, vedere http://jmespath.org/.|
| --verbose                               | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi.|

## <a name="sfctl-application-info"></a>sfctl application info
Recupera informazioni su un'applicazione di Service Fabric.

Restituisce le informazioni sull'applicazione creata o in corso di creazione nel cluster di Service Fabric e il cui nome corrisponde a quello specificato come parametro. La risposta include il nome, il tipo, lo stato, i parametri e altri dettagli relativi all'applicazione.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --application-id [obbligatorio]| Identità dell'applicazione. Si tratta in genere del nome completo dell'applicazione senza lo schema URI "fabric:". A partire dalla versione 6.0, i nomi gerarchici sono delimitati dal carattere "~". Ad esempio, se il nome dell'applicazione è "fabric://myapp/app1", l'identità dell'applicazione sarà "myapp~app1" in 6.0+ e "myapp/app1" nelle versioni precedenti.|
| --exclude-application-parameters| Il flag che specifica se i parametri dell'applicazione verranno esclusi dal risultato.|
| --timeout -t                 | Timeout del server in secondi.  Predefinito: 60.|

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug                      | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug.|
| --help -h                    | Mostra questo messaggio della Guida e l'uscita.|
| --output -o                  | Formato di output.  Valori consentiti: json, jsonc, tabella, tsv.             Predefinito: json.|
| --query                      | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http://jmespath.org/.|
| --verbose                    | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi.|

## <a name="sfctl-application-list"></a>sfctl application list
Mostra l'elenco di applicazioni create nel cluster di Service Fabric che corrisponde ai filtri specificati come parametro.

Recupera le informazioni sulle applicazioni create o in corso di creazione nel cluster di Service Fabric e che corrispondono ai filtri specificati come parametro. La risposta include il nome, il tipo, lo stato, i parametri e altri dettagli relativi all'applicazione. Se le applicazioni non rientrano in una pagina, viene restituita una pagina di risultati e un token di continuazione, che possono essere usati per accedere alla pagina successiva.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
|--application-definition-kind-filter| Usato per filtrare in ApplicationDefinitionKind per le operazioni di query dell'applicazione. - Default - Valore predefinito. Filtro che corrisponde all'input con qualsiasi valore di ApplicationDefinitionKind. Il valore è 0. - All - Filtro che corrisponde all'input con qualsiasi valore di ApplicationDefinitionKind. Il valore è 65535. -ServiceFabricApplicationDescription - Filtro che corrisponde all'input con valore di ApplicationDefinitionKind uguale a ServiceFabricApplicationDescription. Il valore è uguale a 1. - Compose - Filtro che corrisponde all'input con valore di ApplicationDefinitionKind uguale a Compose. Il valore è 2. Valore predefinito: 65535.|
| --application-type-name      | Nome del tipo di applicazione usato per filtrare le applicazioni per cui eseguire le query. Questo valore non deve contenere la versione del tipo di applicazione.|
| --continuation-token         | Il parametro del token di continuazione viene usato per ottenere il set di risultati successivo. Un token di continuazione con un valore non vuoto è incluso nella risposta dell'API quando i risultati del sistema non rientrano in una singola risposta. Quando questo valore viene passato alla successiva chiamata API, l'API restituisce il set di risultati successivo. Se non sono presenti altri risultati, il token di continuazione non contiene alcun valore. Il valore di questo parametro non deve essere codificato in URL.|
| --exclude-application-parameters| Il flag che specifica se i parametri dell'applicazione verranno esclusi dal risultato.|
| --timeout -t                 | Timeout del server in secondi.  Predefinito: 60.|

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug                      | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug.|
| --help -h                    | Mostra questo messaggio della Guida e l'uscita.|
| --output -o                  | Formato di output.  Valori consentiti: json, jsonc, tabella, tsv.             Predefinito: json.|
| --query                      | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http://jmespath.org/.|
| --verbose                    | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi.|

## <a name="sfctl-application-load"></a>sfctl application load
Mostra informazioni di caricamento su un'applicazione di Service Fabric.

Restituisce le informazioni di carico sull'applicazione creata o in corso di creazione nel cluster di Service Fabric e il cui nome corrisponde a quello specificato come parametro. La risposta include il nome, i numero di nodi minimo e massimo, il numero di nodi che l'applicazione occupa al momento e le informazioni sulla metrica di carico dell'applicazione.

### <a name="arguments"></a>Argomenti
|Argomento|Descrizione|
| --- | --- |
|--application-id [obbligatorio]| Identità dell'applicazione. Si tratta in genere del nome completo dell'applicazione senza lo schema URI "fabric:". A partire dalla versione 6.0, i nomi gerarchici sono delimitati dal carattere "~". Ad esempio, se il nome dell'applicazione è "fabric://myapp/app1", l'identità dell'applicazione sarà "myapp~app1" in 6.0+ e "myapp/app1" nelle versioni precedenti. |
| --timeout -t               | Timeout del server in secondi.  Predefinito: 60.|

### <a name="global-arguments"></a>Argomenti globali
|Argomento|Descrizione|
| --- | --- |
|--debug                    | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug.|
    --help -h                  | Mostra questo messaggio della Guida e l'uscita.|
    --output -o                | Formato di output.  Valori consentiti: json, jsonc, tabella, tsv.  Predefinito: json.|
    --query                    | Stringa di query JMESPath. Per maggiori informazioni ed esempi, vedere http://jmespath.org/.|
    --verbose                  | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi.|

## <a name="sfctl-application-manifest"></a>sfctl application manifest
Mostra il manifesto che descrive un tipo di applicazione.
        
Mostra il manifesto che descrive un tipo di applicazione. La risposta contiene il manifesto dell'applicazione XML sotto forma di stringa.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --application-type-name    [obbligatorio]| Nome del tipo di applicazione.|
| --application-type-version [obbligatorio]| Versione del tipo di applicazione.|
| --timeout -t                      | Timeout del server in secondi.  Predefinito: 60.|

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug                           | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug.|
| --help -h                         | Mostra questo messaggio della Guida e l'uscita.|
| --output -o                       | Formato di output.  Valori consentiti: json, jsonc, tabella, tsv.                  Predefinito: json.|
| --query                           | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http://jmespath.org/.|
| --verbose                         | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi.|

## <a name="sfctl-application-provision"></a>sfctl application provision
Esegue il provisioning o la registrazione di un tipo di applicazione di Service Fabric con il cluster.
        
Esegue il provisioning o la registrazione di un tipo di applicazione di Service Fabric con il cluster. È necessario eseguire questa operazione prima di creare un'istanza per una nuova applicazione.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --application-type-build-path [obbligatorio]| Il percorso dell'archivio di immagini per il pacchetto dell'applicazione.|
| --timeout -t                         | Timeout del server in secondi.  Predefinito: 60.|

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug                              | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug.|
| --help -h                            | Mostra questo messaggio della Guida e l'uscita.|
| --output -o                          | Formato di output.  Valori consentiti: json, jsonc, tabella, tsv.  Predefinito: json.|
| --query                              | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http://jmespath.org/.|
| --verbose                            | Aumenta il livello di dettaglio di registrazione. Utilizzare --debug per i log di debug completi.|

## <a name="sfctl-application-type"></a>sfctl application type

Mostra l'elenco dei tipi di applicazioni nel cluster di Service Fabric che corrispondono esattamente al nome specificato.

Restituisce le informazioni sui tipi di applicazione per cui si effettua o è in corso il provisioning del cluster di Service Fabric. Questi risultati sono tipi di applicazioni il cui nome corrisponde esattamente a quello specificato come parametro e che rispettano i parametri di query specificati. Vengono restituite tutte le versioni del tipo di applicazione corrispondenti al nome del tipo di applicazione, ogni versione viene restituita come un tipo di applicazione. La risposta include il nome, la versione, lo stato e altri dettagli relativi al tipo di applicazione. Si tratta di una query di paging, il che vuol dire che se i tipi di applicazione non rientrano tutti in una pagina, viene restituita una pagina di risultati e un token di continuazione, che possono essere usati per accedere alla pagina successiva. Ad esempio, se ci sono 10 tipi di applicazione, ma una pagina contiene solo i primi 3 tipi di applicazione, oppure se il risultato massimo è impostato su 3, viene restituito 3. Per accedere al resto dei risultati, recuperare le pagine successive usando il token di continuazione restituito nella query successiva. Se non ci sono pagine successive, viene restituito un token di continuazione vuoto.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --application-type-name [obbligatorio]| Nome del tipo di applicazione.|
| --continuation-token           | Il parametro del token di continuazione viene usato per ottenere il set di risultati successivo. Un token di continuazione con un valore non vuoto è incluso nella risposta dell'API quando i risultati del sistema non rientrano in una singola risposta. Quando questo valore viene passato alla successiva chiamata API, l'API restituisce il set di risultati successivo. Se non sono presenti altri risultati, il token di continuazione non contiene alcun valore. Il valore di questo parametro non deve essere codificato in URL.|
| --exclude-application-parameters  | Il flag che specifica se i parametri dell'applicazione verranno esclusi dal risultato.|
| --max-results                  | Il numero massimo di risultati che devono essere restituiti come parte delle query di paging. Questo parametro definisce il limite massimo di risultati restituiti. Se non rientrano nel messaggio in base ai limiti di dimensione massima per i messaggi definiti nella configurazione, i risultati restituiti possono essere pari a un numero minore rispetto al numero massimo di risultati specificato. Se questo parametro è uguale a zero o non specificato, le query di paging includono il numero massimo di risultati possibili che rientrano nel messaggio restituito.|
| --timeout -t                   | Timeout del server in secondi.  Predefinito: 60.|

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug                        | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug.|
| --help -h                      | Mostra questo messaggio della Guida e l'uscita.|
| --output -o                    | Formato di output.  Valori consentiti: json, jsonc, tabella, tsv.               Predefinito: json.|
| --query                        | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http://jmespath.org/.|
| --verbose                      | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi.|

## <a name="sfctl-application-unprovision"></a>sfctl application unprovision
Rimuove o annulla la registrazione di un tipo di applicazione di Service Fabric dal cluster.

Rimuove o annulla la registrazione di un tipo di applicazione di Service Fabric dal cluster. Questa operazione può essere eseguita solo se tutte le istanze dell'applicazione del tipo di applicazione sono state eliminate. Dopo aver annullato la registrazione del tipo di applicazione, non è possibile creare una nuova istanza di applicazione per questo particolare tipo di applicazione.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --application-type-name    [obbligatorio]| Nome del tipo di applicazione.|
| --application-type-version [obbligatorio]| Versione del tipo di applicazione.|
| --timeout -t                      | Timeout del server in secondi.  Predefinito: 60.|

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug                           | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug.|
| --help -h                         | Mostra questo messaggio della Guida e l'uscita.|
| --output -o                       | Formato di output.  Valori consentiti: json, jsonc, tabella, tsv.                  Predefinito: json.|
| --query                           | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http://jmespath.org/.|
| --verbose                         | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi.|

## <a name="sfctl-application-upgrade"></a>sfctl application upgrade
Avvia l'aggiornamento di un'applicazione nel cluster di Service Fabric.

Convalida i parametri di aggiornamento dell'applicazione offerta e avvia l'aggiornamento dell'applicazione se i parametri sono validi. Si noti che la descrizione dell'aggiornamento sostituisce la descrizione dell'applicazione esistente. Ciò significa che se i parametri vengono omessi, i parametri esistenti per le applicazioni verranno sovrascritti con l'elenco di parametri vuoto. Il risultato è un'applicazione che usa il valore predefinito dei parametri del manifesto dell'applicazione.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --app-id [obbligatorio]| Identità dell'applicazione. Si tratta in genere del nome completo dell'applicazione senza lo schema URI "fabric:". A partire dalla versione 6.0, i nomi gerarchici sono delimitati con il carattere "~". Ad esempio, se il nome dell'applicazione è "fabric://myapp/app1", l'identità dell'applicazione sarà "myapp~app1" in 6.0+ e "myapp/app1" nelle versioni precedenti.|
| --app-version [obbligatorio]| Versione dell'applicazione di destinazione.|
| --parameters         [obbligatorio]| Un elenco con codifica JSON di sostituzioni del parametro dell'applicazione da applicare quando si esegue l'aggiornamento dell'applicazione.|
| --default-service-health-policy| Specifica con codifica JSON dei criteri di integrità usati per impostazione predefinita per la valutazione dell'integrità di un tipo di servizio.|
| --failure-action            | L'azione da eseguire quando un aggiornamento di monitoraggio rileva violazioni dei criteri di integrità o dei criteri di monitoraggio.|
| --force-restart             | Riavvia in modo forzato i processi durante l'aggiornamento anche quando la versione del codice non è stata modificata.|
| --health-check-retry-timeout| La quantità di tempo per ripetere le valutazioni di integrità, quando l'applicazione o il cluster non è integro prima di eseguire l'azione di errore. Misurato in millisecondi.  Valore predefinito: PT0H10M0S.|
| --health-check-stable-duration | Il tempo di attesa per cui l'applicazione o il cluster devono rimanere integri prima di passare al dominio di aggiornamento successivo.            Misurato in millisecondi.  Valore predefinito: PT0H2M0S.|
| --health-check-wait-duration| Il tempo di attesa dopo il completamento di un dominio di aggiornamento prima di applicare i criteri di integrità. Misurato in millisecondi.            Valore predefinito: 0.|
| --max-unhealthy-apps        | La percentuale massima consentita di applicazioni distribuite non integre. Rappresentato come un numero compreso tra 0 e 100.|
| --mode                      | La modalità usata per monitorare l'integrità durante un aggiornamento in sequenza.            Predefinito: UnmonitoredAuto.|
| --replica-set-check-timeout | Il tempo massimo per bloccare l'elaborazione di un dominio di aggiornamento ed evitare la perdita di disponibilità quando ci sono problemi imprevisti. Il valore è espresso in secondi.|
| --service-health-policy     | Mappa con codifica JSON con criteri di integrità del tipo di servizio per ogni nome di tipo di servizio. Per impostazione predefinita la mappa è vuota.|
| --timeout -t                | Timeout del server in secondi.  Predefinito: 60.|
| --upgrade-domain-timeout    | Il tempo necessario al completamento di ogni dominio di aggiornamento prima dell'esecuzione di FailureAction. Misurato in millisecondi.  Valore predefinito: P10675199DT02H48M05.4775807S.|
| --upgrade-timeout           | Il tempo necessario al completamento dell'aggiornamento prima dell'esecuzione di FailureAction. Misurato in millisecondi.  Valore predefinito: P10675199DT02H48M05.4775807S.|
| --warning-as-error          | Considera gli avvisi di valutazione di integrità con lo stesso livello di gravità degli errori.|

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug                     | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug.|
| --help -h                   | Mostra questo messaggio della Guida e l'uscita.|
| --output -o                 | Formato di output.  Valori consentiti: json, jsonc, tabella, tsv.            Predefinito: json.|
| --query                     | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http://jmespath.org/.|
| --verbose                   | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi.|

## <a name="sfctl-application-upload"></a>sfctl application upload
Copia il pacchetto dell'applicazione di Service Fabric nell'archivio immagini.

Facoltativamente, è possibile visualizzare lo stato di caricamento per ogni file nel pacchetto. Lo stato di caricamento viene inviato a `stderr`.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --path [obbligatorio]| Percorso del pacchetto di applicazione locale.|
|--imagestore-string| Archivio di immagini di destinazione in cui caricare il pacchetto dell'applicazione.  Valore predefinito: fabric:ImageStore.|
| --show-progress  | Mostra lo stato di caricamento del file per i pacchetti di grandi dimensioni.|

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug       | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug.|
| --help -h     | Mostra questo messaggio della Guida e l'uscita.|
| --output -o   | Formato di output.  Valori consentiti: json, jsonc, tabella, tsv.  Predefinito: json.|
| --query       | Stringa di query JMESPath. Per maggiori informazioni ed esempi, vedere http://jmespath.org/.|
| --verbose     | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi.|

## <a name="next-steps"></a>Passaggi successivi
- [Configurare](service-fabric-cli.md) l'interfaccia della riga di comando di Service Fabric.
- Informazioni su come usare l'interfaccia della riga di comando Service Fabric usando gli [script di esempio](/azure/service-fabric/scripts/sfctl-upgrade-application).