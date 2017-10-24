---
title: Interfaccia della riga di comando Azure Service Fabric - sfctl compose | Documentazione Microsoft
description: Descrive i comandi dell'interfaccia della riga di comando Service Fabric sfctl compose.
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
ms.openlocfilehash: 3010c298cf227c761288365e3663ffe3fb67d863
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="sfctl-compose"></a>sfctl compose
Creare, eliminare e gestire distribuzioni Docker Compose.

## <a name="commands"></a>Comandi:

|Comando|Descrizione|
| --- | --- |
|    create| Distribuisce un'applicazione di Service Fabric da un file di Compose.|
|    list  | Mostra l'elenco di distribuzioni di composizione create nel cluster di Service Fabric.|
|   remove| Elimina una distribuzione di composizione Service Fabric esistente dal cluster.|
|   status| Mostra informazioni su una distribuzione di composizione Service Fabric.|
|Aggiornamento       | Avvia l'aggiornamento di una distribuzione di composizione nel cluster di Service Fabric.|
|    upgrade-status| Mostra i dettagli dell'aggiornamento più recente eseguito sulla distribuzione Compose per Service Fabric corrente.|


## <a name="sfctl-compose-create"></a>sfctl compose create
Crea una distribuzione di composizione di Service Fabric.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --file-path [Obbligatorio]| Percorso al file di destinazione Docker Compose.|
 |   --deployment-name [Obbligatorio]| Nome della distribuzione.|
|    --encrypted-pass             | Invece di richiedere una password di registro contenitori, usare una passphrase già crittografata.|
|    --has-pass                   | Richiede una password al registro contenitori.|
|    --timeout -t                 | Timeout del server in secondi.  Predefinito: 60.|
 |   --user                       | Nome utente per connettersi al registro contenitori.|

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug                 | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug.|
| --help -h               | Mostra questo messaggio della Guida e l'uscita.|
| --output -o             | Formato di output.  Valori consentiti: json, jsonc, tabella, tsv.  Predefinito:             json.|
| --query                 | Stringa di query JMESPath. Per maggiori informazioni ed esempi, vedere http://jmespath.org/.|
| --verbose               | Aumenta il livello di dettaglio di registrazione. Utilizzare --debug per i log di debug completi.|

## <a name="sfctl-compose-list"></a>sfctl compose list
Mostra l'elenco di distribuzioni di composizione create nel cluster di Service Fabric.

Mostra lo stato delle distribuzioni di composizione che sono stati creati oppure sono in corso di creazione nel cluster di Service Fabric. La risposta include il nome, lo stato e altri dettagli relativi alla distribuzione di composizione. Se le distribuzioni non rientrano in una pagina, viene restituita una pagina di risultati e un token di continuazione, che può essere utilizzato per accedere alla pagina successiva.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --continuation-token| Il parametro del token di continuazione viene utilizzato per ottenere il set di risultati successivo. Un token di continuazione con un valore non vuoto è incluso nella risposta dell'API quando i risultati dal sistema non rientrano in una singola risposta.      Quando questo valore viene passato alla successiva chiamata API, l'API restituisce il set di risultati successivo. Se non sono presenti altri risultati, il token di continuazione non contiene alcun valore. Il valore di questo parametro non deve essere codificato in URL.|
| --max-results    | Il numero massimo di risultati che devono essere restituiti come parte delle query di paging.      Questo parametro definisce il limite massimo di risultati restituiti.      Se non rientrano nel messaggio in base ai limiti di dimensione massima per i messaggi definiti nella configurazione, i risultati restituiti possono essere pari a un numero minore rispetto al numero massimo di risultati specificato. Se questo parametro è uguale a zero o non specificato, le query di paging includono il numero massimo di risultati possibili che rientrano nel messaggio restituito.|
| --timeout -t     | Timeout del server in secondi.  Predefinito: 60.|

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug          | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug.|
| --help -h        | Mostra questo messaggio della Guida e l'uscita.|
| --output -o      | Formato di output.  Valori consentiti: json, jsonc, tabella, tsv.  Predefinito: json.|
| --query          | Stringa di query JMESPath. Per maggiori informazioni ed esempi, vedere http://jmespath.org/.|
| --verbose        | Aumenta il livello di dettaglio di registrazione. Utilizzare --debug per i log di debug completi.|

## <a name="sfctl-compose-remove"></a>sfctl compose remove
Elimina una distribuzione di composizione Service Fabric esistente dal cluster.

Elimina una distribuzione di composizione Service Fabric esistente dal cluster. 

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --deployment-name [Obbligatorio]| L'identità della distribuzione. Si tratta in genere del nome completo dell'applicazione senza lo schema URI 'fabric:'.|
| --timeout -t            | Timeout del server in secondi.  Predefinito: 60.|

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug                 | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug.|
| --help -h               | Mostra questo messaggio della Guida e l'uscita.|
| --output -o             | Formato di output.  Valori consentiti: json, jsonc, tabella, tsv.  Predefinito:             json.|
| --query                 | Stringa di query JMESPath. Per maggiori informazioni ed esempi, vedere http://jmespath.org/.|
| --verbose               | Aumenta il livello di dettaglio di registrazione. Utilizzare --debug per i log di debug completi.|

## <a name="sfctl-compose-status"></a>sfctl compose status
Mostra informazioni su una distribuzione di composizione Service Fabric.

Restituisce lo stato di distribuzione di composizione che è stato creato o è in corso di creazione nel cluster di Service Fabric e il cui nome corrisponde a quello specificato come parametro. La risposta include il nome, lo stato e altri dettagli relativi all’applicazione.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --deployment-name [Obbligatorio]| L'identità della distribuzione. |
| --timeout -t            | Timeout del server in secondi.  Predefinito: 60.|

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug                 | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug.|
| --help -h               | Mostra questo messaggio della Guida e l'uscita.|
| --output -o             | Formato di output.  Valori consentiti: json, jsonc, tabella, tsv.  Predefinito:             json.|
| --query                 | Stringa di query JMESPath. Per maggiori informazioni ed esempi, vedere http://jmespath.org/.|
| --verbose               | Aumenta il livello di dettaglio di registrazione. Utilizzare --debug per i log di debug completi.|

## <a name="sfctl-compose-upgrade"></a>sfctl compose upgrade
Avvia l'aggiornamento di una distribuzione di composizione nel cluster di Service Fabric.

Convalida i parametri di aggiornamento forniti e avvia l'aggiornamento della distribuzione.

### <a name="arguments"></a>Argomenti
|Argomento|Descrizione|
| --- | --- |
|    --file-path        [Obbligatorio]| Percorso al file di destinazione Docker Compose.|
|    --deployment-name [Obbligatorio]| Nome della distribuzione.|
|    --default-svc-type-health-map| Dizionario con codifica JSON che descrive i criteri di integrità usati per valutare l'integrità dei servizi.|
|    --encrypted-pass             | Anziché richiedere una password di registro contenitori, usare una passphrase già crittografata.|
 |   --failure-action             | I valori possibili sono: 'Invalid', 'Rollback', 'Manual'.|
|    --force-restart              | Forza il riavvio.|
 |   --has-pass                   | Richiede una password al registro contenitori.|
|    --health-check-retry         | Timeout nuovo tentativo controllo integrità misurata in millisecondi.|
|    --health-check-stable        | Durata stabile controllo integrità misurata in millisecondi.|
|    --health-check-wait          | Durata attesa controllo integrità misurata in millisecondi.|
|    --replica-set-check          | Timeout controllo del set di repliche di aggiornamento misurato in secondi.|
|    --svc-type-health-map        | Elenco con codifica JSON di oggetti che descrivono i criteri di integrità utilizzati per valutare l'integrità dei diversi tipi di servizio.|
|    --timeout -t                 | Timeout del server in secondi.  Predefinito: 60.|
|    --unhealthy-app              | La percentuale massima consentita di applicazioni non integre prima che venga segnalato un errore.        Ad esempio, per consentire il 10% di applicazioni non integre, questo valore deve corrispondere a 10. La percentuale rappresenta la percentuale massima tollerata di applicazioni che possono risultare non integre prima che per il cluster venga impostato lo stato Error. Se la percentuale viene rispettata ma esiste almeno un'applicazione non integra, l'integrità viene valutata come Avviso. Tale percentuale viene calcolata dividendo il numero delle applicazioni non integre per il numero totale di istanze di applicazione nel cluster.|
|    --upgrade-domain-timeout     | Timeout dominio di aggiornamento misurato in millisecondi.|
|    --upgrade-kind               | Predefinito: in sequenza.|
|    --upgrade-mode               | I valori possibili sono: 'Invalid', 'UnmonitoredAuto', 'UnmonitoredManual', 'Monitored'.  Predefinito: UnmonitoredAuto.|
|    --upgrade-timeout            | Timeout aggiornamento misurato in millisecondi.|
|    --user                       | Nome utente per connettersi al registro contenitori.|
|    --warning-as-error           | Gli avvisi vengono considerati con lo stello livello di gravità degli errori.|

### <a name="global-arguments"></a>Argomenti globali
 |Argomento|Descrizione|
| --- | --- |
|   --debug                      | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug.|
|    --help -h                    | Mostra questo messaggio della Guida e l'uscita.|
 |   --output -o                  | Formato di output.  Valori consentiti: json, jsonc, tabella, tsv.
                                   Predefinito: json.|
 |   --query                      | Stringa di query JMESPath. Per maggiori informazioni ed esempi, vedere http://jmespath.org/.|
 |   --verbose                    | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi.|

## <a name="next-steps"></a>Passaggi successivi
- [Configurare](service-fabric-cli.md) l'interfaccia della riga di comando di Service Fabric.
- Informazioni su come usare l'interfaccia della riga di comando Service Fabric usando gli [script di esempio](/azure/service-fabric/scripts/sfctl-upgrade-application).