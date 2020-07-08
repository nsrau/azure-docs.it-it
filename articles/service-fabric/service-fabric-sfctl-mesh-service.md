---
title: INTERFACCIA della riga di comando di Azure Service Fabric-servizio mesh sfctl
description: Informazioni su sfctl, l'interfaccia della riga di comando di Azure Service Fabric. Include un elenco di comandi per ottenere i dettagli del servizio per una risorsa dell'applicazione.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 2b9f16062b02d7a1a3403d396643a7989b8976b7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "76905934"
---
# <a name="sfctl-mesh-service"></a>sfctl mesh service
Consente di ottenere i dettagli del servizio ed elencare i servizi di una risorsa dell'applicazione.

## <a name="commands"></a>Comandi

|Comando|Descrizione|
| --- | --- |
| list | Elenca tutte le risorse del servizio. |
| show | Ottiene la risorsa del servizio con il nome specificato. |

## <a name="sfctl-mesh-service-list"></a>sfctl mesh service list
Elenca tutte le risorse del servizio.

Ottiene le informazioni su tutti i servizi di una risorsa dell'applicazione. Le informazioni includono la descrizione e altre proprietà del servizio.

### <a name="arguments"></a>Arguments

|Argomento|Description|
| --- | --- |
| --app-name --application-name [obbligatorio] | Il nome dell'applicazione. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Description|
| --- | --- |
| --debug | Aumenta il livello di dettaglio della registrazione per mostrare tutti i log di debug. |
| --help -h | Visualizza questo messaggio della guida ed esce. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio della registrazione. Usare --debug per log di debug completi. |

## <a name="sfctl-mesh-service-show"></a>sfctl mesh service show
Ottiene la risorsa del servizio con il nome specificato.

Ottiene le informazioni sulla risorsa del servizio con il nome specificato. Le informazioni includono la descrizione e altre proprietà del servizio.

### <a name="arguments"></a>Arguments

|Argomento|Description|
| --- | --- |
| --app-name --application-name [obbligatorio] | Il nome dell'applicazione. |
| --name -n                     [obbligatorio] | Il nome del servizio. |

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