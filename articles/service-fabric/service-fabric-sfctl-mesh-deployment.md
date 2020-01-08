---
title: INTERFACCIA della riga di comando di Azure Service Fabric-distribuzione mesh sfctl
description: Informazioni su sfctl, l'interfaccia della riga di comando di Azure Service Fabric. Include un elenco di comandi per la creazione di Service Fabric risorse mesh.
author: jeffj6123
ms.topic: reference
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: 6512cce075906fc8708a39fa0747ec27be33e961
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75645362"
---
# <a name="sfctl-mesh-deployment"></a>sfctl mesh deployment
Consente di creare le risorse mesh Service Fabric.

## <a name="commands"></a>Comandi:

|Comando|Description|
| --- | --- |
| create | Crea una distribuzione delle risorse di mesh Service Fabric. |

## <a name="sfctl-mesh-deployment-create"></a>sfctl mesh deployment create
Crea una distribuzione delle risorse di mesh Service Fabric.

### <a name="arguments"></a>Argomenti

|Argomento|Description|
| --- | --- |
| --input-yaml-files [Obbligatorio] | Percorsi di file relativi/assoluti delimitati da virgole di tutti i file YAML o del percorso relativo/assoluto della directory (ricorsivo) che contengono file YAML. |
| --parameters | Un percorso relativo/assoluto al file YAML o un oggetto JSON che contiene i parametri di cui è necessario eseguire l'override. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Description|
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

Consolida e distribuisce tutte le risorse di una directory nel cluster eseguendo l'override dei parametri passati direttamente come oggetto JSON
``` 
sfctl mesh deployment create --input-yaml-files ./resources --parameters "{ 'my_param' :    
{'value' : 'my_value'} }"   
```

## <a name="next-steps"></a>Passaggi successivi
- [Configurare](service-fabric-cli.md) l'interfaccia della riga di comando di Service Fabric.
- Informazioni su come usare l'interfaccia della riga di comando Service Fabric usando gli [script di esempio](/azure/service-fabric/scripts/sfctl-upgrade-application).