---
title: Interfaccia di comando di Azure Service Fabric - distribuzione mesh sfctlAzure Service Fabric CLI- sfctl mesh deployment
description: Informazioni su sfctl, l'interfaccia della riga di comando di Azure Service Fabric.Learn about sfctl, the Azure Service Fabric command-line interface. Include un elenco di comandi per la creazione di risorse mesh di Service Fabric.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 108389407221779ed20e81310f084b7b5c23b8c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906034"
---
# <a name="sfctl-mesh-deployment"></a>sfctl mesh deployment
Consente di creare le risorse mesh Service Fabric.

## <a name="commands"></a>Comandi:

|Comando|Descrizione|
| --- | --- |
| create | Crea una distribuzione delle risorse di mesh Service Fabric. |

## <a name="sfctl-mesh-deployment-create"></a>sfctl mesh deployment create
Crea una distribuzione delle risorse di mesh Service Fabric.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --input-yaml-files [Obbligatorio] | Percorsi di file relativi o assoluti delimitati da virgole di tutti i file yaml o il percorso relativo o assoluto della directory (ricorsivo) che contengono i file yaml. |
| --parameters | Percorso relativo o assoluto di un file yaml o di un oggetto json contenente i parametri che devono essere sottoposti a override. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio della registrazione per mostrare tutti i log di debug. |
| --help -h | Visualizza questo messaggio della guida ed esce. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio della registrazione. Usare --debug per log di debug completi. |

### <a name="examples"></a>Esempi

Consente di consolidare e di distribuire tutte le risorse nel cluster sostituendo i parametri indicati nel file yaml
``` 
sfctl mesh deployment create --input-yaml-files ./app.yaml,./network.yaml --parameters  
./param.yaml    
```

Consente di consolidare e di distribuire tutte le risorse in una directory nel cluster sostituendo i parametri indicati nel file yaml

``` 
sfctl mesh deployment create --input-yaml-files ./resources --parameters ./param.yaml
```

Consolida e distribuisce tutte le risorse di una directory per il cluster eseguendo l'override dei parametri passati direttamente come oggetto json
``` 
sfctl mesh deployment create --input-yaml-files ./resources --parameters "{ 'my_param' :    
{'value' : 'my_value'} }"   
```

## <a name="next-steps"></a>Passaggi successivi
- [Configurare](service-fabric-cli.md) l'interfaccia della riga di comando di Service Fabric.
- Informazioni su come usare l'interfaccia della riga di comando Service Fabric usando gli [script di esempio](/azure/service-fabric/scripts/sfctl-upgrade-application).
