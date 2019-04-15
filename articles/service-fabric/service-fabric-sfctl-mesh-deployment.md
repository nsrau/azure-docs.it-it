---
title: 'Interfaccia della riga di comando Azure Service Fabric: sfctl mesh deployment | Microsoft Docs'
description: Descrive i comandi dell'interfaccia della riga di comando Service Fabric sfctl mesh deployment.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: e6b484dabd77a142961db2d97242896790fa3d8b
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58668467"
---
# <a name="sfctl-mesh-deployment"></a>sfctl mesh deployment
Creare le risorse di mesh Service Fabric.

## <a name="commands"></a>Comandi:

|Comando|Descrizione|
| --- | --- |
| create | Crea una distribuzione delle risorse di mesh Service Fabric. |

## <a name="sfctl-mesh-deployment-create"></a>sfctl mesh deployment create
Crea una distribuzione delle risorse di mesh Service Fabric.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --input-yaml-files [Obbligatorio] | Percorsi di file relativi/assoluti delimitati da virgole di tutti i file yaml o percorso relativo/assoluto della directory (ricorsiva) che contiene file yaml. |
| --parameters | Un percorso relativo/assoluto al file yaml o a un oggetto json che contiene i parametri che devono essere sottoposti a override. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

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

Consente di consolidare e di distribuire tutte le risorse in una directory nel cluster sostituendo i parametri che vengono passati direttamente come oggetto json

```
sfctl mesh deployment create --input-yaml-files ./resources --parameters "{ 'my_param' :
{'value' : 'my_value'} }"
```


## <a name="next-steps"></a>Passaggi successivi
- [Configurare](service-fabric-cli.md) l'interfaccia della riga di comando di Service Fabric.
- Informazioni su come usare l'interfaccia della riga di comando Service Fabric usando gli [script di esempio](/azure/service-fabric/scripts/sfctl-upgrade-application).