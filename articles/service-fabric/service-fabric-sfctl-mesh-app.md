---
title: INTERFACCIA della riga di comando di Azure Service Fabric-app mesh sfctl
description: Informazioni su sfctl, l'interfaccia della riga di comando di Azure Service Fabric. Include un elenco di comandi per la gestione delle risorse dell'applicazione Service Fabric mesh.
author: jeffj6123
ms.topic: reference
ms.date: 9/17/2019
ms.author: jejarry
ms.openlocfilehash: 96f628cb1a54b0c68f81bbafea42e5b9313f42ec
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75645379"
---
# <a name="sfctl-mesh-app"></a>sfctl mesh app
Consente di ottenere ed eliminare le risorse dell'applicazione.

## <a name="commands"></a>Comandi:

|Comando|Description|
| --- | --- |
| delete | Elimina la risorsa dell'applicazione. |
| list | Elenca tutte le risorse dell'applicazione. |
| mostra | Ottiene la risorsa dell'applicazione con il nome specificato. |

## <a name="sfctl-mesh-app-delete"></a>sfctl mesh app delete
Elimina la risorsa dell'applicazione.

Elimina la risorsa dell'applicazione identificata dal nome.

### <a name="arguments"></a>Argomenti

|Argomento|Description|
| --- | --- |
| --name -n [Obbligatorio] | Il nome dell'applicazione. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Description|
| --- | --- |
| --debug | Aumenta il livello di dettaglio della registrazione per mostrare tutti i log di debug. |
| --help -h | Visualizza questo messaggio della guida ed esce. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio della registrazione. Usare --debug per log di debug completi. |

## <a name="sfctl-mesh-app-list"></a>sfctl mesh app list
Elenca tutte le risorse dell'applicazione.

Ottiene le informazioni su tutte le risorse dell'applicazione in un determinato gruppo di risorse. Le informazioni includono la descrizione e altre proprietà dell'applicazione.

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Description|
| --- | --- |
| --debug | Aumenta il livello di dettaglio della registrazione per mostrare tutti i log di debug. |
| --help -h | Visualizza questo messaggio della guida ed esce. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio della registrazione. Usare --debug per log di debug completi. |

## <a name="sfctl-mesh-app-show"></a>sfctl mesh app show
Ottiene la risorsa dell'applicazione con il nome specificato.

Ottiene le informazioni sulla risorsa dell'applicazione con il nome specificato. Le informazioni includono la descrizione e altre proprietà dell'applicazione.

### <a name="arguments"></a>Argomenti

|Argomento|Description|
| --- | --- |
| --name -n [Obbligatorio] | Il nome dell'applicazione. |

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