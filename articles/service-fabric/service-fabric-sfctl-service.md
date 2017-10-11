---
title: Interfaccia della riga di comando Azure Service Fabric - sfctl service | Microsoft Docs
description: Descrive i comandi dell'interfaccia della riga di comando Service Fabric sfctl service.
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
ms.sourcegitcommit: 469246d6cb64d6aaf995ef3b7c4070f8d24372b1
ms.openlocfilehash: 66649bb6ae317eb227dcdf45aa084905967c117f
ms.contentlocale: it-it
ms.lasthandoff: 09/27/2017

---
# <a name="sfctl-service"></a>sfctl service
Creare, eliminare e gestire servizi, tipi di servizio e pacchetti di servizi.

## <a name="commands"></a>Comandi:

|Comando|Descrizione|
| --- | --- |
|    app-name       | Mostra il nome dell'applicazione Service Fabric per un servizio.|
|    code-package-list | Mostra l'elenco di pacchetti di codice distribuiti in un nodo di Service Fabric.|
|    create         | Crea il servizio Service Fabric specificato dalla descrizione.|
|    delete         | Elimina un servizio Service Fabric esistente.|
|    deployed-type  | Mostra le informazioni inerenti a un tipo di servizio specifico dell'applicazione distribuito in un nodo di un cluster Service Fabric.|
|    deployed-type-list| Mostra l'elenco contenente le informazioni sui tipi di servizio per le applicazioni distribuite in un nodo di un cluster Service Fabric.|
|    description    | Mostra la descrizione di un servizio di Service Fabric esistente.|
|    health         | Mostra l'integrità del servizio di Service Fabric specificato.|
|    info           | Mostra le informazioni relative al servizio specifico appartenente a un'applicazione di Service Fabric.|
|    list           | Mostra le informazioni su tutti i servizi appartenenti all'applicazione specificata dall'ID dell'applicazione.|
|    manifest       | Mostra il manifesto che descrive un tipo di servizio.|
|    package-deploy | Scarica i pacchetti associati al manifesto del servizio specificato per la cache delle immagini nel nodo specifico.|
|    package-health | Mostra le informazioni sull'integrità di un pacchetto di servizi per una specifica applicazione distribuita per un nodo e un'applicazione di Service Fabric.|
|    package-info   | Mostra l'elenco di pacchetti di servizi distribuiti su un nodo di Service Fabric e corrispondenti esattamente al nome specificato.|
|    package-list   | Mostra l'elenco di pacchetti di servizi distribuiti in un nodo di Service Fabric.|
|    recover        | Indica al cluster di Service Fabric che deve tentare di ripristinare il servizio specificato, attualmente bloccato in una perdita di quorum.|
|    report-health  | Invia un report di integrità sul servizio di Service Fabric.|
|    resolve        | per risolvere una partizione di Service Fabric.|
|    type-list      | Mostra l'elenco contenente le informazioni sui tipi di servizio supportati da un tipo di applicazione distribuito in un cluster Service Fabric.|
|    update         | Aggiorna il servizio specificato usando la descrizione di aggiornamento specificata.|


## <a name="sfctl-service-create"></a>sfctl service create
Crea il servizio Service Fabric specificato dalla descrizione.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --app-id       [obbligatorio]| Identità dell'applicazione padre. Si tratta in genere dell'ID completo dell'applicazione senza lo schema URI "fabric:". A partire dalla versione 6.0, i nomi gerarchici sono delimitati con il carattere "~". Ad esempio, se il nome dell'applicazione è "fabric://myapp/app1", l'identità dell'applicazione sarà "myapp~app1" in 6.0+ e "myapp/app1" nelle versioni precedenti.|
| --name         [obbligatorio]| Nome del servizio. Deve trattarsi di un elemento figlio dell'ID dell'applicazione.           Corrisponde al nome intero, incluso l'URI `fabric:`. Ad esempio, il servizio `fabric:/A/B` è un elemento figlio dell'applicazione `fabric:/A`.|
| --service-type [obbligatorio]| Nome del tipo di servizio.|
| --activation-mode     | Modalità di attivazione del pacchetto di servizi.|
| --constraints         | Vincoli di posizionamento sotto forma di stringa. I vincoli di posizionamento sono espressioni booleane nelle proprietà del nodo e consentono di limitare un servizio a nodi specifici in base ai requisiti del servizio. Ad esempio, per inserire un servizio nei nodi in cui NodeType è blu, specificare quanto segue: "NodeColor == blue".|
| --correlated-service  | Nome del servizio di destinazione con cui creare una corrispondenza.|
| --correlation         | Serve a correlare il servizio con un servizio esistente usando un'affinità di allineamento.|
| --dns-name            | Nome DNS del servizio da creare. Il servizio di sistema DNS di Service Fabric deve essere abilitato per questa impostazione.|
| --instance-count      | Numero di istanze. Si applica solo a servizi senza stato.|
| --int-scheme          | Indica che il servizio deve essere partizionato in modo uniforme tra un intervallo di valori interi senza segno.|
| --int-scheme-count    | Numero di partizioni all'interno di tale intervallo di integer chiave (per uno schema di partizione integer uniforme) da creare.|
| --int-scheme-high     | Fine dell'intervallo di integer chiave, se si usa uno schema di partizione integer uniforme.|
| --int-scheme-low      | Inizio dell'intervallo di integer chiave, se si usa uno schema di partizione integer uniforme.|
| --load-metrics        | Elenco con codifica JSON di metriche da usare quando si esegue il bilanciamento del carico di servizi tra nodi.|
| --min-replica-set-size| Dimensioni minime di un set di repliche sotto forma di numero. Si applica solo a servizi con stato.|
| --move-cost           | Specifica il costo di spostamento per il servizio. I valori possibili sono: "Zero", "Low", "Medium" e "High".|
| --named-scheme        | Indica che il servizio deve disporre di più partizioni denominate.|
| --named-scheme-list   | Elenco con codifica JSON di nomi per eseguire la partizione del servizio, se si usa lo schema di partizione denominato.|
| --no-persisted-state  | Se "true", indica che il servizio non ha alcuno stato persistente archiviato sul disco locale o che archivia lo stato solo in memoria.|
| --placement-policy-list  | Elenco con codifica JSON dei criteri di selezione per il servizio e di eventuali nomi di dominio associati. I criteri possono essere uno o più: `NonPartiallyPlaceService`, `PreferPrimaryDomain`, `RequireDomain`, `RequireDomainDistribution`.|
| --quorum-loss-wait    | Durata massima in secondi per cui una partizione può trovarsi in uno stato di perdita del quorum. Si applica solo a servizi con stato.|
| --replica-restart-wait| Durata in secondi tra il momento in cui si arresta una replica e la creazione di una nuova replica. Si applica solo a servizi con stato.|
| --singleton-scheme    | Indica che il servizio deve disporre di una singola partizione o essere un servizio non partizionato.|
| --stand-by-replica-keep  | Durata massima in secondi per cui le repliche StandBy vengono mantenute prima di essere rimosse. Si applica solo a servizi con stato.|
| --stateful            | Indica un servizio con stato.|
| --stateless           | Indica un servizio senza stato.|
| --target-replica-set-size| Dimensioni di un set di repliche di destinazione sotto forma di numero. Si applica solo a servizi con stato.|
| --timeout -t          | Timeout del server in secondi.  Predefinito: 60.|

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug               | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug.|
| --help -h             | Mostra questo messaggio della Guida e l'uscita.|
| --output -o           | Formato di output.  Valori consentiti: json, jsonc, tabella, tsv.  Predefinito: json.|
| --query               | Stringa di query JMESPath. Per maggiori informazioni ed esempi, vedere http://jmespath.org/.|
| --verbose             | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi.|

## <a name="sfctl-service-delete"></a>sfctl service delete
Elimina un servizio Service Fabric esistente.

Elimina un servizio Service Fabric esistente. Prima di poter essere eliminato, un servizio deve essere creato. Per impostazione predefinita, Service Fabric tenta di chiudere le repliche del servizio in modo normale, per poi eliminare il servizio. Tuttavia, se nel servizio si verificano dei problemi chiudendo normalmente la replica, l'operazione di eliminazione potrebbe richiedere molto tempo o bloccarsi. Usare il flag facoltativo ForceRemove per ignorare la normale sequenza di chiusura ed eliminare il servizio in modo forzato.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --service-id [obbligatorio]| L'identità del servizio. Si tratta in genere del nome completo del servizio senza lo schema URI 'fabric:'. A partire dalla versione 6.0, i nomi gerarchici sono delimitati con il carattere "~". Ad esempio, se il nome del servizio è "fabric://myapp/app1/svc1", l'identità del servizio sarà "myapp~app1~svc1" in 6.0+ e "myapp/app1/svc1" nelle versioni precedenti.|
| --force-remove      | Consente di rimuovere un servizio o applicazione di Service Fabric in modo forzato senza passare attraverso la sequenza di arresto normale. Questo parametro può essere usato per eliminare in modo forzato un'applicazione o un servizio la cui eliminazione sta scadendo a causa di problemi nel codice del servizio che impediscono la normale chiusura delle repliche.|
| --timeout -t        | Timeout del server in secondi.  Predefinito: 60.|

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug             | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug.|
| --help -h           | Mostra questo messaggio della Guida e l'uscita.|
| --output -o         | Formato di output.  Valori consentiti: json, jsonc, tabella, tsv.  Predefinito: json.|
| --query             | Stringa di query JMESPath. Per maggiori informazioni ed esempi, vedere http://jmespath.org/.|
| --verbose           | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi.|

## <a name="sfctl-service-description"></a>Descrizione del servizio sfctl
Mostra la descrizione di un servizio di Service Fabric esistente.

Mostra la descrizione di un servizio di Service Fabric esistente. Prima di poter ottenere la descrizione, è necessario creare un servizio.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --service-id [obbligatorio]| L'identità del servizio. Si tratta in genere del nome completo del servizio senza lo schema URI 'fabric:'. A partire dalla versione 6.0, i nomi gerarchici sono delimitati con il carattere "~". Ad esempio, se il nome del servizio è "fabric://myapp/app1/svc1", l'identità del servizio sarà "myapp~app1~svc1" in 6.0+ e "myapp/app1/svc1" nelle versioni precedenti.|
| --timeout -t        | Timeout del server in secondi.  Predefinito: 60.|

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug             | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug.|
| --help -h           | Mostra questo messaggio della Guida e l'uscita.|
| --output -o         | Formato di output.  Valori consentiti: json, jsonc, tabella, tsv.  Predefinito: json.|
| --query             | Stringa di query JMESPath. Per maggiori informazioni ed esempi, vedere http://jmespath.org/.|
| --verbose           | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi.|

## <a name="sfctl-service-health"></a>Integrità dei servizi sfctl
Mostra l'integrità del servizio di Service Fabric specificato.

Mostra le informazioni di integrità del servizio specificato. Usare EventsHealthStateFilter per filtrare la raccolta di eventi di stato riportati nel servizio in base allo stato di integrità. Usare PartitionsHealthStateFilter per filtrare la raccolta di partizioni restituita. Se si specifica un servizio che non esiste nell'archivio integrità, questo cmdlet restituisce un errore. .

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --service-id          [obbligatorio]| L'identità del servizio. Si tratta in genere del nome completo del servizio senza lo schema URI "fabric:". A partire dalla versione 6.0, i nomi gerarchici sono delimitati con il carattere "~". Ad esempio, se il nome del servizio è "fabric://myapp/app1/svc1", l'identità del servizio sarà "myapp~app1~svc1" in 6.0+ e "myapp/app1/svc1" nelle versioni precedenti.|
| --events-health-state-filter | Consente di filtrare la raccolta di oggetti HealthEvent restituiti in base allo stato di integrità. I valori possibili per questo parametro includono il valore intero di uno dei seguenti stati di integrità. Vengono restituiti solo gli eventi che corrispondono al filtro. Tutti gli eventi vengono usati per valutare lo stato di integrità aggregato. Se non specificato diversamente, vengono restituite tutte le voci. I valori dello stato sono enumerati in base al flag, pertanto il valore potrebbe essere una combinazione di questi valori ottenuti usando l'operatore "OR" bit per bit. Ad esempio, se il valore fornito è 6, vengono restituiti tutti gli eventi con valore dello stato di integrità OK (2) e di Avviso (4). - Default - Valore predefinito. Consente di ricercare qualsiasi stato di integrità. Il valore predefinito è zero. - None - Il filtro non corrisponde ad alcun valore di stato di integrità. Usato per non restituire alcun risultato in un determinato insieme di stati. Il valore è uguale a 1. -Ok - Filtro che ricerca le corrispondenze di input con valore di stato di integrità Ok. Il valore è 2. -Warning - filtro che ricerca le corrispondenze di input con valore di stato di integrità Avviso. Il valore è 4. - Error - Filtro che ricerca le corrispondenze di input con valore di stato di integrità Errore. Il valore è 8. -All - Filtro che ricerca le corrispondenze di input con qualsiasi valore di stato di integrità. Il valore è 65535.|
|--exclude-health-statistics     | Indica se le statistiche di integrità devono essere restituite come parte del risultato della query. False per impostazione predefinita. Le statistiche mostrano il numero di elementi figlio in stato di integrità Ok, avviso ed errore.|
| --partitions-health-state-filter| Consente di filtrare gli oggetti dello stato di integrità delle partizioni restituiti nel risultato della query sull'integrità del servizio in base al relativo stato di integrità. I valori possibili per questo parametro includono il valore intero di uno dei seguenti stati di integrità. Vengono restituite solo partizioni che corrispondono al filtro. Tutte le partizioni vengono usate per valutare lo stato di integrità aggregato. Se non specificato diversamente, vengono restituite tutte le voci. I valori dello stato sono enumerati in base al flag, pertanto il valore potrebbe essere una combinazione di questi valori ottenuti usando l'operatore "OR" bit per bit. Ad esempio, se il valore fornito è "6", vengono restituite le partizioni con valore dello stato di integrità OK (2) e di Avviso (4). - Default - Valore predefinito. Consente di ricercare qualsiasi stato di integrità.                  Il valore predefinito è zero. - None - Il filtro non corrisponde ad alcun valore di stato di integrità. Usato per non restituire alcun risultato in un determinato insieme di stati. Il valore è uguale a 1. -Ok - Filtro che ricerca le corrispondenze di input con valore di stato di integrità Ok. Il valore è 2. -Warning - filtro che ricerca le corrispondenze di input con valore di stato di integrità Avviso. Il valore è 4. - Error - Filtro che ricerca le corrispondenze di input con valore di stato di integrità Errore. Il valore è 8. -All - Filtro che ricerca le corrispondenze di input con qualsiasi valore di stato di integrità. Il valore è 65535.|
| --timeout -t                 | Timeout del server in secondi.  Predefinito: 60.|

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug                      | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug.|
| --help -h                    | Mostra questo messaggio della Guida e l'uscita.|
| --output -o                  | Formato di output.  Valori consentiti: json, jsonc, tabella, tsv.                  Predefinito: json.|
| --query                      | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http://jmespath.org/.|
| --verbose                    | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi.|

## <a name="sfctl-service-info"></a>sfctl service info
Mostra le informazioni relative al servizio specifico appartenente a un'applicazione di Service Fabric.

Mostra le informazioni relative al servizio specifico appartenente all'applicazione di Service Fabric specificata.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --application-id [obbligatorio]| Identità dell'applicazione. Si tratta in genere del nome completo dell'applicazione senza lo schema URI 'fabric:'. A partire dalla versione 6.0, i nomi gerarchici sono delimitati con il carattere "~". Ad esempio, se il nome dell'applicazione è "fabric://myapp/app1", l'identità dell'applicazione sarà "myapp~app1" in 6.0+ e "myapp/app1" nelle versioni precedenti.|
| --service-id             [obbligatorio]| L'identità del servizio. Si tratta in genere del nome completo del servizio senza lo schema URI 'fabric:'. A partire dalla versione 6.0, i nomi gerarchici sono delimitati con il carattere "~". Ad esempio, se il nome del servizio è "fabric://myapp/app1/svc1", l'identità del servizio sarà "myapp~app1~svc1" in 6.0+ e "myapp/app1/svc1" nelle versioni precedenti.|
| --timeout -t            | Timeout del server in secondi.  Predefinito: 60.|

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug                 | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug.|
| --help -h               | Mostra questo messaggio della Guida e l'uscita.|
| --output -o             | Formato di output.  Valori consentiti: json, jsonc, tabella, tsv.  Predefinito:             json.|
| --query                 | Stringa di query JMESPath. Per maggiori informazioni ed esempi, vedere http://jmespath.org/.|
| --verbose               | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi.|

## <a name="sfctl-service-list"></a>sfctl service list
Mostra le informazioni su tutti i servizi appartenenti all'applicazione specificata dall'ID dell'applicazione.

Restituisce le informazioni su tutti i servizi appartenenti all'applicazione specificata dall'ID dell'applicazione.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --application-id [obbligatorio]| Identità dell'applicazione. Si tratta in genere del nome completo dell'applicazione senza lo schema URI 'fabric:'. A partire dalla versione 6.0, i nomi gerarchici sono delimitati con il carattere "~". Ad esempio, se il nome dell'applicazione è "fabric://myapp/app1", l'identità dell'applicazione sarà "myapp~app1" in 6.0+ e "myapp/app1" nelle versioni precedenti.|
| --continuation-token    | Il parametro del token di continuazione viene usato per ottenere il set di risultati successivo. Un token di continuazione con un valore non vuoto è incluso nella risposta dell'API quando i risultati dal sistema non rientrano in una singola risposta. Quando questo valore viene passato alla successiva chiamata API, l'API restituisce il set di risultati successivo. Se non sono presenti altri risultati, il token di continuazione non contiene alcun valore. Il valore di questo parametro non deve essere codificato in URL.|
| --service-type-name     | Nome del tipo servizio usato per filtrare i servizi da cercare.|
| --timeout -t            | Timeout del server in secondi.  Predefinito: 60.|

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug                 | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug.|
| --help -h               | Mostra questo messaggio della Guida e l'uscita.|
| --output -o             | Formato di output.  Valori consentiti: json, jsonc, tabella, tsv.  Predefinito:             json.|
| --query                 | Stringa di query JMESPath. Per maggiori informazioni ed esempi, vedere http://jmespath.org/.|
| --verbose               | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi.|

## <a name="sfctl-service-manifest"></a>sfctl service manifest
Mostra il manifesto che descrive un tipo di servizio.

Mostra il manifesto che descrive un tipo di servizio. La risposta contiene il manifesto del servizio XML sotto forma di stringa.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --application-type-name    [obbligatorio]| Nome del tipo di applicazione.|
| --application-type-version [obbligatorio]| Versione del tipo di applicazione.|
| --service-manifest-name    [obbligatorio]| Nome di un manifesto del servizio registrato come parte di un tipo di applicazione in un cluster di Service Fabric.|
| --timeout -t                      | Timeout del server in secondi.  Predefinito: 60.|

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug                           | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug.|
| --help -h                         | Mostra questo messaggio della Guida e l'uscita.|
| --output -o                       | Formato di output.  Valori consentiti: json, jsonc, tabella, tsv.                       Predefinito: json.|
| --query                           | Stringa di query JMESPath. Per maggiori informazioni ed esempi, vedere http://jmespath.org/.|
| --verbose                         | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi.|

## <a name="sfctl-service-recover"></a>sfctl service recover
Indica al cluster di Service Fabric che deve tentare di ripristinare il servizio specificato, attualmente bloccato in una perdita di quorum.

Indica al cluster di Service Fabric che deve tentare di ripristinare il servizio specificato, attualmente bloccato in una perdita di quorum. Questa operazione può essere eseguita solo se non è possibile recuperare le repliche che non sono attive. Un uso non corretto di questa API può causare una perdita di dati.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --service-id [obbligatorio]| L'identità del servizio. Si tratta in genere del nome completo del servizio senza lo schema URI 'fabric:'. A partire dalla versione 6.0, i nomi gerarchici sono delimitati con il carattere "~". Ad esempio, se il nome del servizio è "fabric://myapp/app1/svc1", l'identità del servizio sarà "myapp~app1~svc1" in 6.0+ e "myapp/app1/svc1" nelle versioni precedenti.|
| --timeout -t        | Timeout del server in secondi.  Predefinito: 60.|

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug             | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug.|
| --help -h           | Mostra questo messaggio della Guida e l'uscita.|
| --output -o         | Formato di output.  Valori consentiti: json, jsonc, tabella, tsv.  Predefinito: json.|
| --query             | Stringa di query JMESPath. Per maggiori informazioni ed esempi, vedere http://jmespath.org/.|
| --verbose           | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi.|

## <a name="sfctl-service-resolve"></a>sfctl service resolve
Consente di risolvere una partizione di Service Fabric.

Consente di risolvere una partizione del servizio Service Fabric per ottenere gli endpoint delle repliche del servizio.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --service-id [obbligatorio]| L'identità del servizio. Si tratta in genere del nome completo del servizio senza lo schema URI 'fabric:'. A partire dalla versione 6.0, i nomi gerarchici sono delimitati con il carattere "~". Ad esempio, se il nome del servizio è "fabric://myapp/app1/svc1", l'identità del servizio sarà "myapp~app1~svc1" in 6.0+ e "myapp/app1/svc1" nelle versioni precedenti.|
| --partition-key-type| Tipo di chiave per la partizione. Questo parametro è obbligatorio se lo schema di partizione per il servizio è Int64Range o Named. I valori possibili sono i seguenti. -None (1) - Indica che il parametro PartitionKeyValue non è specificato. È valido per le partizioni con schema di partizionamento Singleton. Si tratta del valore predefinito. Il valore è uguale a 1. -Int64Range (2) - indica che il parametro PartitionKeyValue è una chiave di partizione int64. È valido per le partizioni con schema di partizionamento Int64Range. Il valore è 2. - Named (3) - Indica che il parametro PartitionKeyValue è un nome della partizione. È valido per le partizioni con schema di partizionamento Named. Il valore è 3.|
| --partition-key-value  | Chiave di partizione. Questo parametro è obbligatorio se lo schema di partizione per il servizio è Int64Range o Named.|
| --previous-rsp-version | Valore nel campo Version della risposta ricevuta in precedenza. È obbligatorio se l'utente riconosce che il risultato ottenuto in precedenza non è aggiornato.|
| --timeout -t        | Timeout del server in secondi.  Predefinito: 60.|

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug             | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug.|
| --help -h           | Mostra questo messaggio della Guida e l'uscita.|
| --output -o         | Formato di output.  Valori consentiti: json, jsonc, tabella, tsv.  Predefinito: json.|
| --query             | Stringa di query JMESPath. Per maggiori informazioni ed esempi, vedere http://jmespath.org/.|
| --verbose           | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi.|

## <a name="sfctl-service-update"></a>sfctl service update
Aggiorna il servizio specificato usando la descrizione di aggiornamento specificata.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --service-id   [obbligatorio]| Servizio di destinazione da aggiornare. Si tratta in genere dell'ID completo del servizio senza lo schema URI "fabric:". A partire dalla versione 6.0, i nomi gerarchici sono delimitati con il carattere "~". Ad esempio, se il nome del servizio è "fabric://myapp/app1/svc1", l'identità del servizio sarà "myapp~app1~svc1" in 6.0+ e "myapp/app1/svc1" nelle versioni precedenti.|
| --constraints         | Vincoli di posizionamento sotto forma di stringa. I vincoli di posizionamento sono espressioni booleane nelle proprietà del nodo e consentono di limitare un servizio a nodi specifici in base ai requisiti del servizio. Ad esempio, per inserire un servizio nei nodi in cui NodeType è blu, specificare quanto segue: "NodeColor == blue".|
| --correlated-service  | Nome del servizio di destinazione con cui creare una corrispondenza.|
| --correlation         | Serve a correlare il servizio con un servizio esistente usando un'affinità di allineamento.|
| --instance-count      | Numero di istanze. Si applica solo a servizi senza stato.|
| --load-metrics        | Elenco con codifica JSON di metriche da usare quando si esegue il bilanciamento del carico tra nodi.|
| --min-replica-set-size| Dimensioni minime di un set di repliche sotto forma di numero. Si applica solo a servizi con stato.|
| --move-cost           | Specifica il costo di spostamento per il servizio. I valori possibili sono: "Zero", "Low", "Medium" e "High".|
| --placement-policy-list  | Elenco con codifica JSON dei criteri di selezione per il servizio e di eventuali nomi di dominio associati. I criteri possono essere uno o più: `NonPartiallyPlaceService`, `PreferPrimaryDomain`, `RequireDomain`, `RequireDomainDistribution`.|
| --quorum-loss-wait    | Durata massima in secondi per cui una partizione può trovarsi in uno stato di perdita del quorum. Si applica solo a servizi con stato.|
| --replica-restart-wait| Durata in secondi tra il momento in cui si arresta una replica e la creazione di una nuova replica. Si applica solo a servizi con stato.|
| --stand-by-replica-keep  | Durata massima in secondi per cui le repliche StandBy vengono mantenute prima di essere rimosse. Si applica solo a servizi con stato.|
| --stateful            | Indica un servizio di destinazione con stato.|
| --stateless           | Indica un servizio di destinazione senza stato.|
| --target-replica-set-size| Dimensioni di un set di repliche di destinazione sotto forma di numero. Si applica solo a servizi con stato.|
| --timeout -t          | Timeout del server in secondi.  Predefinito: 60.|

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug               | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug.|
| --help -h             | Mostra questo messaggio della Guida e l'uscita.|
| --output -o           | Formato di output.  Valori consentiti: json, jsonc, tabella, tsv.  Predefinito: json.|
| --query               | Stringa di query JMESPath. Per maggiori informazioni ed esempi, vedere http://jmespath.org/.|
| --verbose             | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi.|

## <a name="next-steps"></a>Passaggi successivi
- [Configurare](service-fabric-cli.md) l'interfaccia della riga di comando di Service Fabric.
- Informazioni su come usare l'interfaccia della riga di comando Service Fabric usando gli [script di esempio](/azure/service-fabric/scripts/sfctl-upgrade-application).
