---
title: Interfaccia di comando di Azure Service Fabric - proprietà sfctlAzure Service Fabric CLI- sfctl property
description: Informazioni su sfctl, l'interfaccia della riga di comando di Azure Service Fabric.Learn about sfctl, the Azure Service Fabric command line interface. Include un elenco di comandi per l'archiviazione e l'esecuzione di query sulle proprietà.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: a9bd75e0b7f8bfceb50a71ca83b60ff1e7b45508
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905819"
---
# <a name="sfctl-property"></a>sfctl property
Consente di archiviare proprietà ed eseguire query su di esse con i nomi di Service Fabric.

## <a name="commands"></a>Comandi:

|Comando|Descrizione|
| --- | --- |
| delete | Elimina la proprietà di Service Fabric specificata. |
| get | Ottiene la proprietà di Service Fabric specificata. |
| list | Ottiene informazioni su tutte le proprietà di Service Fabric con un determinato nome. |
| put | Crea o aggiorna una proprietà di Service Fabric. |

## <a name="sfctl-property-delete"></a>sfctl property delete
Elimina la proprietà di Service Fabric specificata.

Elimina la proprietà di Service Fabric specificata con un determinato nome. Prima di poter essere eliminata, una proprietà deve essere creata.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --name-id [Obbligatorio] | Nome di Service Fabric, senza lo schema URI "fabric\:". |
| --property-name [Obbligatorio] | Specifica il nome della proprietà da ottenere. |
| --timeout -t | Timeout del server per l'esecuzione dell'operazione in secondi. Questo timeout specifica il periodo di tempo durante il quale il client è disposto ad attendere il completamento dell'operazione richiesta. Il valore predefinito per questo parametro è 60 secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio della registrazione per mostrare tutti i log di debug. |
| --help -h | Visualizza questo messaggio della guida ed esce. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio della registrazione. Usare --debug per log di debug completi. |

## <a name="sfctl-property-get"></a>sfctl property get
Ottiene la proprietà di Service Fabric specificata.

Ottiene la proprietà di Service Fabric specificata con un determinato nome. Questa proprietà restituirà sempre sia il valore che i metadati.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --name-id [Obbligatorio] | Nome di Service Fabric, senza lo schema URI "fabric\:". |
| --property-name [Obbligatorio] | Specifica il nome della proprietà da ottenere. |
| --timeout -t | Timeout del server per l'esecuzione dell'operazione in secondi. Questo timeout specifica il periodo di tempo durante il quale il client è disposto ad attendere il completamento dell'operazione richiesta. Il valore predefinito per questo parametro è 60 secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio della registrazione per mostrare tutti i log di debug. |
| --help -h | Visualizza questo messaggio della guida ed esce. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio della registrazione. Usare --debug per log di debug completi. |

## <a name="sfctl-property-list"></a>sfctl property list
Ottiene informazioni su tutte le proprietà di Service Fabric con un determinato nome.

Un nome di Service Fabric può avere una o più proprietà denominate che archiviano informazioni personalizzate. Questa operazione ottiene le informazioni su queste proprietà in un elenco di paging. Le informazioni includono nome, valore e metadati di ogni proprietà.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --name-id [Obbligatorio] | Nome di Service Fabric, senza lo schema URI "fabric\:". |
| --continuation-token | Il parametro del token di continuazione viene utilizzato per ottenere il set di risultati successivo. Un token di continuazione con un valore non vuoto è incluso nella risposta dell'API quando i risultati del sistema non rientrano in una singola risposta. Quando questo valore viene passato alla successiva chiamata API, l'API restituisce il set di risultati successivo. Se non sono presenti altri risultati, il token di continuazione non contiene alcun valore. Il valore di questo parametro non deve essere codificato in URL. |
| --include-values | Consente di specificare se includere i valori delle proprietà restituite. True se i valori devono essere restituiti con i metadati. False per restituire solo i metadati della proprietà. |
| --timeout -t | Timeout del server per l'esecuzione dell'operazione in secondi. Questo timeout specifica il periodo di tempo durante il quale il client è disposto ad attendere il completamento dell'operazione richiesta. Il valore predefinito per questo parametro è 60 secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio della registrazione per mostrare tutti i log di debug. |
| --help -h | Visualizza questo messaggio della guida ed esce. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio della registrazione. Usare --debug per log di debug completi. |

## <a name="sfctl-property-put"></a>sfctl property put
Crea o aggiorna una proprietà di Service Fabric.

Crea o aggiorna la proprietà di Service Fabric specificata con un determinato nome.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --name-id [Obbligatorio] | Nome di Service Fabric, senza lo schema URI "fabric\:". |
| --property-name [Obbligatorio] | Nome della proprietà di Service Fabric. |
| --value [Obbligatorio] | Descrive un valore della proprietà di Service Fabric. Si tratta di una stringa JSON. <br><br> La stringa json ha due campi, il 'Tipo' dei dati e il valore, immesso come 'Dati' dei dati. Il valore "Kind" deve essere il primo elemento visualizzato nella stringa JSON e i valori possono essere "Binary", "Int64", "Double", "String" o "Guid". Il valore deve poter eseguire la serializzazione per i tipi specificati. Entrambi i valori "Kind" e "Data" devono essere specificati come stringhe. |
| --custom-id-type | ID del tipo personalizzato della proprietà. Usando questa proprietà, l'utente può contrassegnare il tipo del valore della proprietà. |
| --timeout -t | Impostazione predefinita\: 60. |

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