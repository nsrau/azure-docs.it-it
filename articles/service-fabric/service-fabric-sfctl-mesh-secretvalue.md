---
title: INTERFACCIA della riga di comando di Azure Service Fabric-sfctl mesh secretvalue
description: Informazioni su sfctl, l'interfaccia della riga di comando di Azure Service Fabric. Include un elenco di comandi per ottenere ed eliminare Service Fabric mesh secretvalue risorse.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: b2fe3a7995cdd88a8f63d0d865e72e9f161cf9d7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76905955"
---
# <a name="sfctl-mesh-secretvalue"></a>sfctl mesh secretvalue
Consente di ottenere ed eliminare le risorse secretvalue mesh.

## <a name="commands"></a>Comandi:

|Comando|Descrizione|
| --- | --- |
| Elimina | Elimina il valore specificato della risorsa di segreto denominata. |
| list | Elenca i nomi di tutti i valori della risorsa di segreto specificata. |
| show | Elenca il valore specificato della risorsa segreta. |

## <a name="sfctl-mesh-secretvalue-delete"></a>sfctl mesh secretvalue delete
Elimina il valore specificato della risorsa di segreto denominata.

Elimina la risorsa di valore di segreto identificata dal nome. Il nome della risorsa è in genere la versione associata a tale valore. L'eliminazione non riuscirà se il valore specificato è in uso.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --secret-name -n [Obbligatorio] | Nome della risorsa di segreto. |
| --version -v     [Obbligatorio] | Il nome della versione del segreto. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
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

|Argomento|Descrizione|
| --- | --- |
| --secret-name -n [Obbligatorio] | Nome della risorsa di segreto. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio della registrazione per mostrare tutti i log di debug. |
| --help -h | Visualizza questo messaggio della guida ed esce. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio della registrazione. Usare --debug per log di debug completi. |

## <a name="sfctl-mesh-secretvalue-show"></a>sfctl mesh secretvalue show
Elenca il valore specificato della risorsa segreta.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --secret-name -n [Obbligatorio] | Nome della risorsa di segreto. |
| --version -v     [Obbligatorio] | Il nome della versione del segreto. |
| --show-value | Mostra il valore effettivo della versione del segreto. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio della registrazione per mostrare tutti i log di debug. |
| --help -h | Visualizza questo messaggio della guida ed esce. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio della registrazione. Usare --debug per log di debug completi. |


## <a name="next-steps"></a>Passaggi successivi
- [Configurare](service-fabric-cli.md) l'interfaccia della riga di comando Service Fabric.
- Informazioni su come usare l'interfaccia della riga di comando Service Fabric usando gli [script di esempio](/azure/service-fabric/scripts/sfctl-upgrade-application).