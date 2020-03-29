---
title: Interfaccia di rete di Azure Service Fabric - volume mesh sfctlAzure Service Fabric CLI- sfctl mesh volume
description: Informazioni su sfctl, l'interfaccia della riga di comando di Azure Service Fabric.Learn about sfctl, the Azure Service Fabric command line interface. Include un elenco di comandi per ottenere ed eliminare le risorse di volume.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 0e5ba1a311bd0480c56ee40edf01fc13ec01664a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76905919"
---
# <a name="sfctl-mesh-volume"></a>sfctl mesh volume
Consente di ottenere ed eliminare le risorse di volume.

## <a name="commands"></a>Comandi:

|Comando|Descrizione|
| --- | --- |
| delete | Elimina la risorsa di volume. |
| list | Elenca tutte le risorse di volume. |
| show | Ottiene la risorsa di volume con il nome specificato. |

## <a name="sfctl-mesh-volume-delete"></a>sfctl mesh volume delete
Elimina la risorsa di volume.

Elimina la risorsa di volume identificata dal nome.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --name -n [Obbligatorio] | Il nome del volume. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio della registrazione per mostrare tutti i log di debug. |
| --help -h | Visualizza questo messaggio della guida ed esce. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio della registrazione. Usare --debug per log di debug completi. |

## <a name="sfctl-mesh-volume-list"></a>sfctl mesh volume list
Elenca tutte le risorse di volume.

Ottiene le informazioni su tutte le risorse di volume in un determinato gruppo di risorse. Le informazioni includono la descrizione e altre proprietà del volume.

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio della registrazione per mostrare tutti i log di debug. |
| --help -h | Visualizza questo messaggio della guida ed esce. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio della registrazione. Usare --debug per log di debug completi. |

## <a name="sfctl-mesh-volume-show"></a>sfctl mesh volume show
Ottiene la risorsa di volume con il nome specificato.

Ottiene le informazioni sulla risorsa di volume con il nome specificato. Le informazioni includono la descrizione e altre proprietà del volume.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --name -n [Obbligatorio] | Il nome del volume. |

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