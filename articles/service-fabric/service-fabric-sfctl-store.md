---
title: Interfaccia della riga di comando di Azure Service Fabric - archivio sfctlAzure Service Fabric CLI- sfctl store
description: Informazioni su sfctl, l'interfaccia della riga di comando di Azure Service Fabric.Learn about sfctl, the Azure Service Fabric command line interface. Include un elenco di comandi per l'esecuzione di operazioni a livello di file nell'archivio immagini del cluster.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 75c62b54ff3aa7f3af344aa3e1ca81d431ae0ab2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905616"
---
# <a name="sfctl-store"></a>sfctl store
Esegue operazioni base a livello di file nell'archivio immagini del cluster.

## <a name="commands"></a>Comandi:

|Comando|Descrizione|
| --- | --- |
| delete | Elimina il contenuto dell’archivio immagini esistente. |
| root-info | Mostra le informazioni sul contenuto alla radice dell'archivio immagini. |
| stat | Mostra le informazioni sul contenuto dell’archivio immagini. |

## <a name="sfctl-store-delete"></a>sfctl store delete
Elimina il contenuto dell’archivio immagini esistente.

Elimina il contenuto dell’archivio immagini esistente trovato all'interno del percorso relativo dell’archivio immagini specificato. Questo comando può essere utilizzato per eliminare i pacchetti dell’applicazione caricati dopo l'esecuzione del provisioning.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --content-path [Obbligatorio] | Percorso relativo dalla radice al file o cartella nell'archivio immagini. |
| --timeout -t | Timeout del server per l'esecuzione dell'operazione in secondi. Questo timeout specifica il periodo di tempo durante il quale il client è disposto ad attendere il completamento dell'operazione richiesta. Il valore predefinito per questo parametro è 60 secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio della registrazione per mostrare tutti i log di debug. |
| --help -h | Visualizza questo messaggio della guida ed esce. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio della registrazione. Usare --debug per log di debug completi. |

## <a name="sfctl-store-root-info"></a>sfctl store root-info
Mostra le informazioni sul contenuto alla radice dell'archivio immagini.

Restituisce le informazioni sul contenuto dell'archivio immagini nella radice dell'archivio immagini.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --timeout -t | Timeout del server per l'esecuzione dell'operazione in secondi. Questo timeout specifica il periodo di tempo durante il quale il client è disposto ad attendere il completamento dell'operazione richiesta. Il valore predefinito per questo parametro è 60 secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio della registrazione per mostrare tutti i log di debug. |
| --help -h | Visualizza questo messaggio della guida ed esce. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio della registrazione. Usare --debug per log di debug completi. |

## <a name="sfctl-store-stat"></a>sfctl store stat
Mostra le informazioni sul contenuto dell’archivio immagini.

Restituisce le informazioni sul contenuto dell'archivio immagini nell'elemento contentPath specificato. contentPath è relativo alla radice dell'archivio immagini.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --content-path [Obbligatorio] | Percorso relativo dalla radice al file o cartella nell'archivio immagini. |
| --timeout -t | Timeout del server per l'esecuzione dell'operazione in secondi. Questo timeout specifica il periodo di tempo durante il quale il client è disposto ad attendere il completamento dell'operazione richiesta. Il valore predefinito per questo parametro è 60 secondi.  Impostazione predefinita\: 60. |

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