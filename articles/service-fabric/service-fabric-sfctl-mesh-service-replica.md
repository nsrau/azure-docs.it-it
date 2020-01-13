---
title: INTERFACCIA della riga di comando di Azure Service Fabric-servizio mesh sfctl-replica
description: Informazioni su sfctl, l'interfaccia della riga di comando di Azure Service Fabric. Include un elenco di comandi per ottenere i dettagli della replica per le risorse di un'applicazione.
author: jeffj6123
ms.topic: reference
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: 219a691dd1b74ec2214c156018e6e3f62366f523
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75645328"
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
| --debug | Aumenta il livello di dettaglio della registrazione per mostrare tutti i log di debug. |
| --help -h | Visualizza questo messaggio della guida ed esce. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio della registrazione. Usare --debug per log di debug completi. |

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
| --debug | Aumenta il livello di dettaglio della registrazione per mostrare tutti i log di debug. |
| --help -h | Visualizza questo messaggio della guida ed esce. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio della registrazione. Usare --debug per log di debug completi. |


## <a name="next-steps"></a>Passaggi successivi
- [Configurare](service-fabric-cli.md) l'interfaccia della riga di comando di Service Fabric.
- Informazioni su come usare l'interfaccia della riga di comando Service Fabric usando gli [script di esempio](/azure/service-fabric/scripts/sfctl-upgrade-application).