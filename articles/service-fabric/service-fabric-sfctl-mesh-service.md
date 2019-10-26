---
title: Interfaccia della riga di comando di Azure Service Fabric - sfctl mesh service | Microsoft Docs
description: Descrive i comandi sfctl mesh service dell'interfaccia della riga di comando di Service Fabric.
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
ms.openlocfilehash: d2ddc34f8b298a4037a1c8286eceb9ef13b1f416
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901145"
---
# <a name="sfctl-mesh-service"></a>sfctl mesh service
Consente di ottenere i dettagli del servizio ed elencare i servizi di una risorsa dell'applicazione.

## <a name="commands"></a>Comandi:

|Comando|Description|
| --- | --- |
| list | Elenca tutte le risorse del servizio. |
| mostra | Ottiene la risorsa del servizio con il nome specificato. |

## <a name="sfctl-mesh-service-list"></a>sfctl mesh service list
Elenca tutte le risorse del servizio.

Ottiene le informazioni su tutti i servizi di una risorsa dell'applicazione. Le informazioni includono la descrizione e altre proprietà del servizio.

### <a name="arguments"></a>Argomenti

|Argomento|Description|
| --- | --- |
| --app-name --application-name [obbligatorio] | Il nome dell'applicazione. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Description|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-mesh-service-show"></a>sfctl mesh service show
Ottiene la risorsa del servizio con il nome specificato.

Ottiene le informazioni sulla risorsa del servizio con il nome specificato. Le informazioni includono la descrizione e altre proprietà del servizio.

### <a name="arguments"></a>Argomenti

|Argomento|Description|
| --- | --- |
| --app-name --application-name [obbligatorio] | Il nome dell'applicazione. |
| --name -n                     [obbligatorio] | Il nome del servizio. |

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