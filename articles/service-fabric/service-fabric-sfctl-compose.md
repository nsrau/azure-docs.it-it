---
title: Interfaccia della riga di comando Azure Service Fabric - sfctl compose | Documentazione Microsoft
description: Descrive i comandi dell'interfaccia della riga di comando Service Fabric sfctl compose.
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
ms.openlocfilehash: 4b5cbb4a24b61de7e64a52ef950deedab3eec263
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60837267"
---
# <a name="sfctl-compose"></a>sfctl compose
Creare, eliminare e gestire applicazioni Docker Compose.

## <a name="commands"></a>Comandi:

|Comando|Descrizione|
| --- | --- |
| create | Crea una distribuzione di composizione di Service Fabric. |
| list | Mostra l'elenco di distribuzioni di composizione create nel cluster di Service Fabric. |
| remove | Elimina una distribuzione di composizione Service Fabric esistente dal cluster. |
| status | Mostra informazioni su una distribuzione di composizione Service Fabric. |
| Aggiornamento | Avvia l'aggiornamento di una distribuzione di composizione nel cluster di Service Fabric. |
| upgrade-rollback | Avvia il rollback di un aggiornamento di una distribuzione Compose nel cluster di Service Fabric. |
| upgrade-status | Mostra i dettagli dell'aggiornamento più recente eseguito sulla distribuzione Compose per Service Fabric corrente. |

## <a name="sfctl-compose-create"></a>sfctl compose create
Crea una distribuzione di composizione di Service Fabric.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --deployment-name [Obbligatorio] | Nome della distribuzione. |
| --file-path [Obbligatorio] | Percorso al file di destinazione Docker Compose. |
| --encrypted-pass | Invece di richiedere una password di registro contenitori, usare una passphrase già crittografata. |
| --has-pass | Richiederà una password al registro contenitori. |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |
| --user | Nome utente per connettersi al registro contenitori. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-compose-list"></a>sfctl compose list
Mostra l'elenco di distribuzioni di composizione create nel cluster di Service Fabric.

Mostra lo stato delle distribuzioni di composizione che sono state create oppure sono in corso di creazione nel cluster di Service Fabric. La risposta include il nome, lo stato e altri dettagli relativi alle distribuzioni di composizione. Se l'elenco di distribuzioni non rientra in una pagina, vengono restituiti una pagina di risultati e un token di continuazione, che può essere usato per accedere alla pagina successiva.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --continuation-token | Il parametro del token di continuazione viene utilizzato per ottenere il set di risultati successivo. Un token di continuazione con un valore non vuoto è incluso nella risposta dell'API quando i risultati del sistema non rientrano in una singola risposta. Quando questo valore viene passato alla successiva chiamata API, l'API restituisce il set di risultati successivo. Se non sono presenti altri risultati, il token di continuazione non contiene alcun valore. Il valore di questo parametro non deve essere codificato in URL. |
| --max-results | Il numero massimo di risultati che devono essere restituiti come parte delle query di paging. Questo parametro definisce il limite massimo di risultati restituiti. Se non rientrano nel messaggio in base ai limiti di dimensione massima per i messaggi definiti nella configurazione, il numero dei risultati restituiti può essere inferiore al numero massimo di risultati specificato. Se questo parametro è uguale a zero o non specificato, le query di paging includono il numero massimo di risultati possibili che rientrano nel messaggio restituito. |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-compose-remove"></a>sfctl compose remove
Elimina una distribuzione di composizione Service Fabric esistente dal cluster.

Elimina una distribuzione di composizione Service Fabric esistente dal cluster.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --deployment-name [Obbligatorio] | L'identità della distribuzione. |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-compose-status"></a>sfctl compose status
Mostra informazioni su una distribuzione di composizione Service Fabric.

Restituisce lo stato della distribuzione di composizione che è stata creata o è in corso di creazione nel cluster di Service Fabric e il cui nome corrisponde a quello specificato come parametro. La risposta include il nome, lo stato e altri dettagli relativi alla distribuzione.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --deployment-name [Obbligatorio] | L'identità della distribuzione. |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-compose-upgrade"></a>sfctl compose upgrade
Avvia l'aggiornamento di una distribuzione di composizione nel cluster di Service Fabric.

Convalida i parametri di aggiornamento forniti e avvia l'aggiornamento della distribuzione se i parametri sono validi.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --deployment-name [Obbligatorio] | Nome della distribuzione. |
| --file-path        [Obbligatorio] | Percorso al file di destinazione Docker Compose. |
| --default-svc-type-health-map | Dizionario con codifica JSON che descrive i criteri di integrità usati per valutare l'integrità dei servizi. |
| --encrypted-pass | Invece di richiedere una password di registro contenitori, usare una passphrase già crittografata. |
| --failure-action | I valori possibili sono\: "Invalid", "Rollback", "Manual". |
| --force-restart | I processi vengono riavviati in modo forzato durante l'aggiornamento anche quando la versione del codice non è stata modificata. <br><br> L'aggiornamento modifica solo la configurazione o i dati. |
| --has-pass | Richiederà una password al registro contenitori. |
| --health-check-retry | Intervallo di tempo tra i tentativi di esecuzione dei controlli integrità se l'applicazione o il cluster non è integro. |
| --health-check-stable | Tempo di attesa per cui l'applicazione o il cluster devono rimanere integri prima di passare al dominio di aggiornamento successivo. <br><br> Viene prima interpretato come stringa che rappresenta una durata ISO 8601. Se l'esito è negativo, viene interpretato come numero che rappresenta il numero totale di millisecondi. |
| --health-check-wait | Intervallo di tempo di attesa dopo il completamento di un dominio di aggiornamento prima di avviare il processo dei controlli integrità. |
| --replica-set-check | Tempo massimo per bloccare l'elaborazione di un dominio di aggiornamento ed evitare la perdita di disponibilità quando si verificano problemi imprevisti. <br><br> Quando il timeout scade, l'elaborazione del dominio di aggiornamento procede indipendentemente dai problemi di perdita di disponibilità. Il timeout viene reimpostato all'inizio di ogni dominio di aggiornamento. I valori validi sono compresi tra 0 e 42949672925 inclusi. |
| --svc-type-health-map | Elenco con codifica JSON di oggetti che descrivono i criteri di integrità usati per valutare l'integrità dei diversi tipi di servizio. |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |
| --unhealthy-app | Percentuale massima consentita di applicazioni non integre prima che venga segnalato un errore. <br><br> Ad esempio, per consentire il 10% di applicazioni non integre, questo valore deve corrispondere a 10. La percentuale rappresenta la percentuale massima tollerata di applicazioni che possono risultare non integre prima che per il cluster venga impostato lo stato Error. Se la percentuale viene rispettata ma esiste almeno un'applicazione non integra, l'integrità viene valutata come Avviso. Tale valore viene calcolato dividendo il numero delle applicazioni non integre per il numero totale di istanze di applicazione nel cluster. |
| --upgrade-domain-timeout | Tempo necessario al completamento di ogni dominio di aggiornamento prima dell'esecuzione di FailureAction. <br><br> Viene prima interpretato come stringa che rappresenta una durata ISO 8601. Se l'esito è negativo, viene interpretato come numero che rappresenta il numero totale di millisecondi. |
| --upgrade-kind | Impostazione predefinita\: Rolling. |
| --upgrade-mode | I valori possibili sono\: "Invalid", "UnmonitoredAuto", "UnmonitoredManual", "Monitored".  Impostazione predefinita\: UnmonitoredAuto. |
| --upgrade-timeout | Tempo necessario al completamento dell'aggiornamento prima dell'esecuzione di FailureAction. <br><br> Viene prima interpretato come stringa che rappresenta una durata ISO 8601. Se l'esito è negativo, viene interpretato come numero che rappresenta il numero totale di millisecondi. |
| --user | Nome utente per connettersi al registro contenitori. |
| --warning-as-error | Indica se gli avvisi vengono considerati con lo stesso livello di gravità degli errori. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-compose-upgrade-rollback"></a>sfctl compose upgrade-rollback
Avvia il rollback di un aggiornamento di una distribuzione Compose nel cluster di Service Fabric.

Consente di eseguire il rollback di un aggiornamento di una distribuzione Compose di Service Fabric.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --deployment-name [Obbligatorio] | L'identità della distribuzione. |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-compose-upgrade-status"></a>sfctl compose upgrade-status
Mostra i dettagli dell'aggiornamento più recente eseguito sulla distribuzione Compose per Service Fabric corrente.

Restituisce le informazioni sullo stato dell'aggiornamento della distribuzione di composizione, con dettagli che agevolano il debug dei problemi di integrità dell'applicazione.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --deployment-name [Obbligatorio] | L'identità della distribuzione. |
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