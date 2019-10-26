---
title: Interfaccia della riga di comando Azure Service Fabric - sfctl store | Documentazione Microsoft
description: Descrive i comandi dell'interfaccia della riga di comando Service Fabric sfctl store.
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
ms.openlocfilehash: e8a085c4aa6df34441f22da5542231999930d89f
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900950"
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
| --timeout -t | Timeout del server per l'esecuzione dell'operazione in secondi. Questo timeout specifica la durata del periodo di attesa del client per il completamento dell'operazione richiesta. Il valore predefinito per questo parametro è 60 secondi.  Valore predefinito\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Description|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-store-root-info"></a>sfctl store root-info
Mostra le informazioni sul contenuto alla radice dell'archivio immagini.

Restituisce le informazioni sul contenuto dell'archivio immagini nella radice dell'archivio immagini.

### <a name="arguments"></a>Argomenti

|Argomento|Description|
| --- | --- |
| --timeout -t | Timeout del server per l'esecuzione dell'operazione in secondi. Questo timeout specifica la durata del periodo di attesa del client per il completamento dell'operazione richiesta. Il valore predefinito per questo parametro è 60 secondi.  Valore predefinito\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Description|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-store-stat"></a>sfctl store stat
Mostra le informazioni sul contenuto dell’archivio immagini.

Restituisce le informazioni sul contenuto dell'archivio immagini nell'elemento contentPath specificato. contentPath è relativo alla radice dell'archivio immagini.

### <a name="arguments"></a>Argomenti

|Argomento|Description|
| --- | --- |
| --content-path [Obbligatorio] | Percorso relativo dalla radice al file o cartella nell'archivio immagini. |
| --timeout -t | Timeout del server per l'esecuzione dell'operazione in secondi. Questo timeout specifica la durata del periodo di attesa del client per il completamento dell'operazione richiesta. Il valore predefinito per questo parametro è 60 secondi.  Valore predefinito\: 60. |

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