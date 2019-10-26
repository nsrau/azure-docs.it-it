---
title: 'Interfaccia della riga di comando Azure Service Fabric: sfctl mesh secretvalue | Microsoft Docs'
description: Descrive i comandi dell'interfaccia della riga di comando Service Fabric sfctl mesh secretvalue.
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
ms.openlocfilehash: 96ce4efe00d89c136a0c11d445170b2f67be6fcd
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901185"
---
# <a name="sfctl-mesh-secretvalue"></a>sfctl mesh secretvalue
Consente di ottenere ed eliminare le risorse secretvalue mesh.

## <a name="commands"></a>Comandi:

|Comando|Description|
| --- | --- |
| delete | Elimina il valore specificato della risorsa di segreto denominata. |
| list | Elenca i nomi di tutti i valori della risorsa di segreto specificata. |
| mostra | Elenca il valore specificato della risorsa segreta. |

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
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

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
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

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
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |


## <a name="next-steps"></a>Passaggi successivi
- [Configurare](service-fabric-cli.md) l'interfaccia della riga di comando di Service Fabric.
- Informazioni su come usare l'interfaccia della riga di comando Service Fabric usando gli [script di esempio](/azure/service-fabric/scripts/sfctl-upgrade-application).