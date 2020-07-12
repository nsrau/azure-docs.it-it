---
title: INTERFACCIA della riga di comando di Azure Service Fabric-sfctl mesh Secret
description: Informazioni su sfctl, l'interfaccia della riga di comando di Azure Service Fabric. Include un elenco di comandi per ottenere ed eliminare Service Fabric risorse segrete della rete.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 2c91a59e57613934c946e3cd56089b330893a51a
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2020
ms.locfileid: "86260347"
---
# <a name="sfctl-mesh-secret"></a>sfctl mesh secret
Consente di ottenere ed eliminare le risorse di tipo segreto mesh.

## <a name="commands"></a>Comandi

|Comando|Descrizione|
| --- | --- |
| eliminazione | Elimina la risorsa di tipo segreto. |
| list | Elenca tutte le risorse di tipo segreto. |
| show | Ottiene la risorsa di tipo segreto con il nome specificato. |

## <a name="sfctl-mesh-secret-delete"></a>sfctl mesh secret delete
Elimina la risorsa di tipo segreto.

Elimina la risorsa di tipo segreto specificata e tutti i relativi valori denominati.

### <a name="arguments"></a>Argomenti

|Argomento|Description|
| --- | --- |
| --name -n [Obbligatorio] | Nome della risorsa di segreto. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Description|
| --- | --- |
| --debug | Aumenta il livello di dettaglio della registrazione per mostrare tutti i log di debug. |
| --help -h | Visualizza questo messaggio della guida ed esce. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio della registrazione. Usare --debug per log di debug completi. |

## <a name="sfctl-mesh-secret-list"></a>sfctl mesh secret list
Elenca tutte le risorse di tipo segreto.

Ottiene le informazioni su tutte le risorse di tipo segreto in un determinato gruppo di risorse. Le informazioni includono la descrizione e altre proprietà del segreto.

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Description|
| --- | --- |
| --debug | Aumenta il livello di dettaglio della registrazione per mostrare tutti i log di debug. |
| --help -h | Visualizza questo messaggio della guida ed esce. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio della registrazione. Usare --debug per log di debug completi. |

## <a name="sfctl-mesh-secret-show"></a>sfctl mesh secret show
Ottiene la risorsa di tipo segreto con il nome specificato.

Ottiene le informazioni sulla risorsa di tipo segreto con il nome specificato. Le informazioni includono la descrizione e altre proprietà del segreto.

### <a name="arguments"></a>Argomenti

|Argomento|Description|
| --- | --- |
| --name -n [Obbligatorio] | Nome della risorsa di segreto. |

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
