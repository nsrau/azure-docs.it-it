---
title: INTERFACCIA della riga di comando di Azure Service Fabric-volume mesh sfctl
description: Informazioni su sfctl, l'interfaccia della riga di comando di Azure Service Fabric. Include un elenco di comandi per ottenere ed eliminare le risorse del volume.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 57efca87aefad346fda175b073409868d21564ae
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2020
ms.locfileid: "86245510"
---
# <a name="sfctl-mesh-volume"></a>sfctl mesh volume
Consente di ottenere ed eliminare le risorse di volume.

## <a name="commands"></a>Comandi

|Comando|Descrizione|
| --- | --- |
| eliminazione | Elimina la risorsa di volume. |
| list | Elenca tutte le risorse di volume. |
| show | Ottiene la risorsa di volume con il nome specificato. |

## <a name="sfctl-mesh-volume-delete"></a>sfctl mesh volume delete
Elimina la risorsa di volume.

Elimina la risorsa di volume identificata dal nome.

### <a name="arguments"></a>Argomenti

|Argomento|Description|
| --- | --- |
| --name -n [Obbligatorio] | Il nome del volume. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Description|
| --- | --- |
| --debug | Aumenta il livello di dettaglio della registrazione per mostrare tutti i log di debug. |
| --help -h | Visualizza questo messaggio della guida ed esce. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio della registrazione. Usare --debug per log di debug completi. |

## <a name="sfctl-mesh-volume-list"></a>sfctl mesh volume list
Elenca tutte le risorse di volume.

Ottiene le informazioni su tutte le risorse di volume in un determinato gruppo di risorse. Le informazioni includono la descrizione e altre proprietà del volume.

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Description|
| --- | --- |
| --debug | Aumenta il livello di dettaglio della registrazione per mostrare tutti i log di debug. |
| --help -h | Visualizza questo messaggio della guida ed esce. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio della registrazione. Usare --debug per log di debug completi. |

## <a name="sfctl-mesh-volume-show"></a>sfctl mesh volume show
Ottiene la risorsa di volume con il nome specificato.

Ottiene le informazioni sulla risorsa di volume con il nome specificato. Le informazioni includono la descrizione e altre proprietà del volume.

### <a name="arguments"></a>Argomenti

|Argomento|Description|
| --- | --- |
| --name -n [Obbligatorio] | Il nome del volume. |

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
