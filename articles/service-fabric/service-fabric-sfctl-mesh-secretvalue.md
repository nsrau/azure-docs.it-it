---
title: INTERFACCIA della riga di comando di Azure Service Fabric-sfctl mesh secretvalue
description: Informazioni su sfctl, l'interfaccia della riga di comando di Azure Service Fabric. Include un elenco di comandi per ottenere ed eliminare Service Fabric mesh secretvalue risorse.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 985fb505aae96f4ebd1ba8aeb61679081f303243
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2020
ms.locfileid: "86245773"
---
# <a name="sfctl-mesh-secretvalue"></a>sfctl mesh secretvalue
Consente di ottenere ed eliminare le risorse secretvalue mesh.

## <a name="commands"></a>Comandi

|Comando|Descrizione|
| --- | --- |
| eliminazione | Elimina il valore specificato della risorsa di segreto denominata. |
| list | Elenca i nomi di tutti i valori della risorsa di segreto specificata. |
| show | Elenca il valore specificato della risorsa segreta. |

## <a name="sfctl-mesh-secretvalue-delete"></a>sfctl mesh secretvalue delete
Elimina il valore specificato della risorsa di segreto denominata.

Elimina la risorsa di valore di segreto identificata dal nome. Il nome della risorsa è in genere la versione associata a tale valore. L'eliminazione non riuscirà se il valore specificato è in uso.

### <a name="arguments"></a>Argomenti

|Argomento|Description|
| --- | --- |
| --secret-name -n [Obbligatorio] | Nome della risorsa di segreto. |
| --version -v     [Obbligatorio] | Il nome della versione del segreto. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Description|
| --- | --- |
| --debug | Aumenta il livello di dettaglio della registrazione per mostrare tutti i log di debug. |
| --help -h | Visualizza questo messaggio della guida ed esce. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio della registrazione. Usare --debug per log di debug completi. |

## <a name="sfctl-mesh-secretvalue-list"></a>sfctl mesh secretvalue list
Elenca i nomi di tutti i valori della risorsa di segreto specificata.

Ottiene informazioni su tutte le risorse di valore del segreto della risorsa di segreto specificata. Le informazioni includono i nomi delle risorse di valore del segreto, ma non i valori effettivi.

### <a name="arguments"></a>Argomenti

|Argomento|Description|
| --- | --- |
| --secret-name -n [Obbligatorio] | Nome della risorsa di segreto. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Description|
| --- | --- |
| --debug | Aumenta il livello di dettaglio della registrazione per mostrare tutti i log di debug. |
| --help -h | Visualizza questo messaggio della guida ed esce. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio della registrazione. Usare --debug per log di debug completi. |

## <a name="sfctl-mesh-secretvalue-show"></a>sfctl mesh secretvalue show
Elenca il valore specificato della risorsa segreta.

### <a name="arguments"></a>Argomenti

|Argomento|Description|
| --- | --- |
| --secret-name -n [Obbligatorio] | Nome della risorsa di segreto. |
| --version -v     [Obbligatorio] | Il nome della versione del segreto. |
| --show-value | Mostra il valore effettivo della versione del segreto. |

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
