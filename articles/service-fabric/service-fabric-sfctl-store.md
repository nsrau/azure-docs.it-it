---
title: INTERFACCIA della riga di comando di Azure Service Fabric-archivio sfctl
description: Informazioni su sfctl, l'interfaccia della riga di comando di Azure Service Fabric. Include un elenco di comandi per l'esecuzione di operazioni a livello di file nell'archivio immagini cluster.
author: jeffj6123
ms.topic: reference
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: 64a8ea963afa9e20dfafb71db69b2baf66959369
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75645260"
---
# <a name="sfctl-store"></a>sfctl store
Esegue operazioni base a livello di file nell'archivio immagini del cluster.

## <a name="commands"></a>Comandi:

|Comando|Description|
| --- | --- |
| delete | Elimina il contenuto dell’archivio immagini esistente. |
| root-info | Mostra le informazioni sul contenuto alla radice dell'archivio immagini. |
| stat | Mostra le informazioni sul contenuto dell’archivio immagini. |

## <a name="sfctl-store-delete"></a>sfctl store delete
Elimina il contenuto dell’archivio immagini esistente.

Elimina il contenuto dell’archivio immagini esistente trovato all'interno del percorso relativo dell’archivio immagini specificato. Questo comando può essere utilizzato per eliminare i pacchetti dell’applicazione caricati dopo l'esecuzione del provisioning.

### <a name="arguments"></a>Argomenti

|Argomento|Description|
| --- | --- |
| --content-path [Obbligatorio] | Percorso relativo dalla radice al file o cartella nell'archivio immagini. |
| --timeout -t | Timeout del server per l'esecuzione dell'operazione in secondi. Questo timeout specifica la durata del periodo di attesa del client per il completamento dell'operazione richiesta. Il valore predefinito per questo parametro è 60 secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Description|
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

|Argomento|Description|
| --- | --- |
| --timeout -t | Timeout del server per l'esecuzione dell'operazione in secondi. Questo timeout specifica la durata del periodo di attesa del client per il completamento dell'operazione richiesta. Il valore predefinito per questo parametro è 60 secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Description|
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

|Argomento|Description|
| --- | --- |
| --content-path [Obbligatorio] | Percorso relativo dalla radice al file o cartella nell'archivio immagini. |
| --timeout -t | Timeout del server per l'esecuzione dell'operazione in secondi. Questo timeout specifica la durata del periodo di attesa del client per il completamento dell'operazione richiesta. Il valore predefinito per questo parametro è 60 secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Description|
| --- | --- |
| --debug | Aumenta il livello di dettaglio della registrazione per mostrare tutti i log di debug. |
| --help -h | Visualizza questo messaggio della guida ed esce. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio della registrazione. Usare --debug per log di debug completi. |


## <a name="next-steps"></a>Passaggi successivi
- [Configurare](service-fabric-cli.md) l'interfaccia della riga di comando di Service Fabric.
- Informazioni su come usare l'interfaccia della riga di comando Service Fabric usando gli [script di esempio](/azure/service-fabric/scripts/sfctl-upgrade-application).