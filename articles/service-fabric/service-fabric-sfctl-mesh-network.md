---
title: INTERFACCIA della riga di comando di Azure Service Fabric-rete mesh sfctl
description: Informazioni su sfctl, l'interfaccia della riga di comando di Azure Service Fabric. Include un elenco di comandi per ottenere ed eliminare Service Fabric risorse di rete mesh.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 2b2b2c444bb492fa6c6b945a82090e91963fb1a8
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2020
ms.locfileid: "86245861"
---
# <a name="sfctl-mesh-network"></a>sfctl mesh network
Consente di ottenere ed eliminare le risorse di rete mesh.

## <a name="commands"></a>Comandi

|Comando|Descrizione|
| --- | --- |
| eliminazione | Elimina la risorsa di rete. |
| list | Elimina tutte le risorse di rete. |
| show | Ottiene la risorsa di rete con il nome specificato. |

## <a name="sfctl-mesh-network-delete"></a>sfctl mesh network delete
Elimina la risorsa di rete.

Elimina la risorsa di rete identificata dal nome.

### <a name="arguments"></a>Argomenti

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

### <a name="arguments"></a>Argomenti

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
- Informazioni su come usare l'interfaccia della riga di comando Service Fabric usando gli [script di esempio](./scripts/sfctl-upgrade-application.md).
