---
title: Interfaccia della riga di comando Azure Service Fabric - sfctl store | Documentazione Microsoft
description: Descrive i comandi dell'interfaccia della riga di comando Service Fabric sfctl store.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 05/23/2018
ms.author: bikang
ms.openlocfilehash: 39ecf568c5c41c0007b358670af755be1dd5d99e
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763239"
---
# <a name="sfctl-store"></a>sfctl store
Esegue operazioni base a livello di file nell'archivio immagini del cluster.

## <a name="commands"></a>Comandi:

|Comando|DESCRIZIONE|
| --- | --- |
| delete | Elimina il contenuto dell’archivio immagini esistente. |
| root-info | Mostra le informazioni sul contenuto alla radice dell'archivio immagini. |
| stat | Mostra le informazioni sul contenuto dell’archivio immagini. |

## <a name="sfctl-store-delete"></a>sfctl store delete
Elimina il contenuto dell’archivio immagini esistente.

Elimina il contenuto dell’archivio immagini esistente trovato all'interno del percorso relativo dell’archivio immagini specificato. Questo comando può essere utilizzato per eliminare i pacchetti dell’applicazione caricati dopo l'esecuzione del provisioning.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --content-path [Obbligatorio] | Percorso relativo dalla radice al file o cartella nell'archivio immagini. |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, tabella, tsv.  Impostazione predefinita\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-store-root-info"></a>sfctl store root-info
Mostra le informazioni sul contenuto alla radice dell'archivio immagini.

Restituisce le informazioni sul contenuto dell'archivio immagini nella radice dell'archivio immagini.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, tabella, tsv.  Impostazione predefinita\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Utilizzare --debug per i log di debug completi. |

## <a name="sfctl-store-stat"></a>sfctl store stat
Mostra le informazioni sul contenuto dell’archivio immagini.

Restituisce le informazioni sul contenuto dell'archivio immagini nell'elemento contentPath specificato. contentPath è relativo alla radice dell'archivio immagini.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --content-path [Obbligatorio] | Percorso relativo dalla radice al file o cartella nell'archivio immagini. |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, tabella, tsv.  Impostazione predefinita\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="next-steps"></a>Passaggi successivi
- [Configurare](service-fabric-cli.md) l'interfaccia della riga di comando di Service Fabric.
- Informazioni su come usare l'interfaccia della riga di comando Service Fabric usando gli [script di esempio](/azure/service-fabric/scripts/sfctl-upgrade-application).