---
title: Interfaccia della riga di comando di Azure Service Fabric - sfctl mesh service-replica | Microsoft Docs
description: Descrive i comandi sfctl mesh service-replica dell'interfaccia della riga di comando di Service Fabric.
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
ms.openlocfilehash: e8eaa5fd7c9eeeff3f70a949b04d0fbf5f6c388f
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901165"
---
# <a name="sfctl-mesh-service-replica"></a>sfctl mesh service-replica
Consente di ottenere i dettagli della replica ed elencare le repliche di un servizio specifico in una risorsa dell'applicazione.

## <a name="commands"></a>Comandi:

|Comando|Description|
| --- | --- |
| list | Elenca tutte le repliche di un servizio. |
| mostra | Ottiene la replica specificata del servizio di un'applicazione. |

## <a name="sfctl-mesh-service-replica-list"></a>sfctl mesh service-replica list
Elenca tutte le repliche di un servizio.

Ottiene le informazioni su tutte le repliche di un servizio. Le informazioni includono la descrizione e altre proprietà della replica del servizio.

### <a name="arguments"></a>Argomenti

|Argomento|Description|
| --- | --- |
| --app-name --application-name [obbligatorio] | Il nome dell'applicazione. |
| --service-name                [obbligatorio] | Il nome del servizio. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Description|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-mesh-service-replica-show"></a>sfctl mesh service-replica show
Ottiene la replica specificata del servizio di un'applicazione.

Ottiene le informazioni sulla replica del servizio con il nome specificato. Le informazioni includono la descrizione e altre proprietà della replica del servizio.

### <a name="arguments"></a>Argomenti

|Argomento|Description|
| --- | --- |
| --app-name --application-name [obbligatorio] | Il nome dell'applicazione. |
| --name -n                     [obbligatorio] | Nome della replica del servizio. |
| --service-name                [obbligatorio] | Il nome del servizio. |

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