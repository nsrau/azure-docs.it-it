---
title: 'Interfaccia della riga di comando Azure Service Fabric: sfctl mesh deployment | Microsoft Docs'
description: Descrive i comandi dell'interfaccia della riga di comando Service Fabric sfctl mesh deployment.
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
ms.openlocfilehash: dbecf8e96c6cb5d0d6d873309d848304fe33eaaa
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901259"
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
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

### <a name="examples"></a>esempi

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