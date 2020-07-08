---
title: INTERFACCIA della riga di comando di Azure Service Fabric-rete mesh sfctl
description: Informazioni su sfctl, l'interfaccia della riga di comando di Azure Service Fabric. Include un elenco di comandi per ottenere ed eliminare Service Fabric risorse di rete mesh.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 9aa0b6795508cb94200c486df092b867c1086316
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "76906005"
---
# <a name="sfctl-mesh-network"></a>sfctl mesh network
Consente di ottenere ed eliminare le risorse di rete mesh.

## <a name="commands"></a>Comandi

|Comando|Descrizione|
| --- | --- |
| eliminare | Elimina la risorsa di rete. |
| list | Elimina tutte le risorse di rete. |
| show | Ottiene la risorsa di rete con il nome specificato. |

## <a name="sfctl-mesh-network-delete"></a>sfctl mesh network delete
Elimina la risorsa di rete.

Elimina la risorsa di rete identificata dal nome.

### <a name="arguments"></a>Arguments

|Argomento|Description|
| --- | --- |
| --name -n [Obbligatorio] | Nome della rete. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Description|
| --- | --- |
| --debug | Aumenta il livello di dettaglio della registrazione per mostrare tutti i log di debug. |
| --help -h | Visualizza questo messaggio della guida ed esce. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio della registrazione. Usare --debug per log di debug completi. |

## <a name="sfctl-mesh-network-list"></a>sfctl mesh network list
Elimina tutte le risorse di rete.

Ottiene le informazioni su tutte le risorse di rete in un determinato gruppo di risorse. Le informazioni includono la descrizione e altre proprietà della rete.

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Description|
| --- | --- |
| --debug | Aumenta il livello di dettaglio della registrazione per mostrare tutti i log di debug. |
| --help -h | Visualizza questo messaggio della guida ed esce. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio della registrazione. Usare --debug per log di debug completi. |

## <a name="sfctl-mesh-network-show"></a>sfctl mesh network show
Ottiene la risorsa di rete con il nome specificato.

Ottiene le informazioni sulla risorsa di rete con il nome specificato. Le informazioni includono la descrizione e altre proprietà della rete.

### <a name="arguments"></a>Arguments

|Argomento|Description|
| --- | --- |
| --name -n [Obbligatorio] | Nome della rete. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Description|
| --- | --- |
| --debug | Aumenta il livello di dettaglio della registrazione per mostrare tutti i log di debug. |
| --help -h | Visualizza questo messaggio della guida ed esce. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio della registrazione. Usare --debug per log di debug completi. |


## <a name="next-steps"></a>Passaggi successivi
- [Configurare](service-fabric-cli.md) l'interfaccia della riga di comando Service Fabric.
- Informazioni su come usare l'interfaccia della riga di comando Service Fabric usando gli [script di esempio](/azure/service-fabric/scripts/sfctl-upgrade-application).