---
title: Interfaccia della riga di comando di Azure Service Fabric - sfctl property | Microsoft Docs
description: Descrive i comandi dell'interfaccia della riga di comando Service Fabric sfctl property.
services: service-fabric
documentationcenter: na
author: jeffj6123
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: 368dad54c611e4532b46f11669bcf8e363b9a740
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901063"
---
# <a name="sfctl-property"></a>sfctl property
Consente di archiviare proprietà ed eseguire query su di esse con i nomi di Service Fabric.

## <a name="commands"></a>Comandi:

|Comando|Description|
| --- | --- |
| delete | Elimina la proprietà di Service Fabric specificata. |
| get | Ottiene la proprietà di Service Fabric specificata. |
| list | Ottiene informazioni su tutte le proprietà di Service Fabric con un determinato nome. |
| put | Crea o aggiorna una proprietà di Service Fabric. |

## <a name="sfctl-property-delete"></a>sfctl property delete
Elimina la proprietà di Service Fabric specificata.

Elimina la proprietà di Service Fabric specificata con un determinato nome. Prima di poter essere eliminata, una proprietà deve essere creata.

### <a name="arguments"></a>Argomenti

|Argomento|Description|
| --- | --- |
| --name-id [Obbligatorio] | Nome di Service Fabric, senza lo schema URI "fabric\:". |
| --property-name [Obbligatorio] | Specifica il nome della proprietà da ottenere. |
| --timeout -t | Timeout del server per l'esecuzione dell'operazione in secondi. Questo timeout specifica la durata del periodo di attesa del client per il completamento dell'operazione richiesta. Il valore predefinito per questo parametro è 60 secondi.  Valore predefinito\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Description|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-property-get"></a>sfctl property get
Ottiene la proprietà di Service Fabric specificata.

Ottiene la proprietà di Service Fabric specificata con un determinato nome. Questa proprietà restituirà sempre sia il valore che i metadati.

### <a name="arguments"></a>Argomenti

|Argomento|Description|
| --- | --- |
| --name-id [Obbligatorio] | Nome di Service Fabric, senza lo schema URI "fabric\:". |
| --property-name [Obbligatorio] | Specifica il nome della proprietà da ottenere. |
| --timeout -t | Timeout del server per l'esecuzione dell'operazione in secondi. Questo timeout specifica la durata del periodo di attesa del client per il completamento dell'operazione richiesta. Il valore predefinito per questo parametro è 60 secondi.  Valore predefinito\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Description|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-property-list"></a>sfctl property list
Ottiene informazioni su tutte le proprietà di Service Fabric con un determinato nome.

Un nome di Service Fabric può avere una o più proprietà denominate che archiviano informazioni personalizzate. Questa operazione ottiene le informazioni su queste proprietà in un elenco di paging. Le informazioni includono nome, valore e metadati di ogni proprietà.

### <a name="arguments"></a>Argomenti

|Argomento|Description|
| --- | --- |
| --name-id [Obbligatorio] | Nome di Service Fabric, senza lo schema URI "fabric\:". |
| --continuation-token | Il parametro del token di continuazione viene utilizzato per ottenere il set di risultati successivo. Un token di continuazione con un valore non vuoto è incluso nella risposta dell'API quando i risultati del sistema non rientrano in una singola risposta. Quando questo valore viene passato alla successiva chiamata API, l'API restituisce il set di risultati successivo. Se non sono presenti altri risultati, il token di continuazione non contiene alcun valore. Il valore di questo parametro non deve essere codificato in URL. |
| --include-values | Consente di specificare se includere i valori delle proprietà restituite. True se i valori devono essere restituiti con i metadati. False per restituire solo i metadati della proprietà. |
| --timeout -t | Timeout del server per l'esecuzione dell'operazione in secondi. Questo timeout specifica la durata del periodo di attesa del client per il completamento dell'operazione richiesta. Il valore predefinito per questo parametro è 60 secondi.  Valore predefinito\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Description|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-property-put"></a>sfctl property put
Crea o aggiorna una proprietà di Service Fabric.

Crea o aggiorna la proprietà di Service Fabric specificata con un determinato nome.

### <a name="arguments"></a>Argomenti

|Argomento|Description|
| --- | --- |
| --name-id [Obbligatorio] | Nome di Service Fabric, senza lo schema URI "fabric\:". |
| --property-name [Obbligatorio] | Nome della proprietà di Service Fabric. |
| --value [Obbligatorio] | Descrive un valore della proprietà di Service Fabric. Si tratta di una stringa JSON. <br><br> La stringa json ha due campi relativi ai dati, "Kind" e "Value". Il valore "Kind" deve essere il primo elemento visualizzato nella stringa JSON e i valori possono essere "Binary", "Int64", "Double", "String" o "Guid". Il valore deve poter eseguire la serializzazione per i tipi specificati. Entrambi i valori "Kind" e "Data" devono essere specificati come stringhe. |
| --custom-id-type | ID del tipo personalizzato della proprietà. Usando questa proprietà, l'utente può contrassegnare il tipo del valore della proprietà. |
| --timeout -t | Valore predefinito\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Description|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |


## <a name="next-steps"></a>Passaggi successivi
- [Configurare](service-fabric-cli.md) l'interfaccia della riga di comando di Service Fabric.
- Informazioni su come usare l'interfaccia della riga di comando Service Fabric usando gli [script di esempio](/azure/service-fabric/scripts/sfctl-upgrade-application).