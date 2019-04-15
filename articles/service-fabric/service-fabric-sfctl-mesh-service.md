---
title: Interfaccia della riga di comando di Azure Service Fabric - sfctl mesh service | Microsoft Docs
description: Descrive i comandi sfctl mesh service dell'interfaccia della riga di comando di Service Fabric.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/06/2018
ms.author: bikang
ms.openlocfilehash: e8b735780f4ed3402845d9d401f8e37701b9a1a6
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58667583"
---
# <a name="sfctl-mesh-service"></a>sfctl mesh service
Consente di ottenere i dettagli del servizio ed elencare i servizi di una risorsa dell'applicazione.

## <a name="commands"></a>Comandi:

|Comando|DESCRIZIONE|
| --- | --- |
| list | Elenca tutte le risorse del servizio. |
| show | Ottiene la risorsa del servizio con il nome specificato. |

## <a name="sfctl-mesh-service-list"></a>sfctl mesh service list
Elenca tutte le risorse del servizio.

Ottiene le informazioni su tutti i servizi di una risorsa dell'applicazione. Le informazioni includono la descrizione e altre proprietà del servizio.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --app-name --application-name [obbligatorio] | Il nome dell'applicazione. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
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

|Argomento|DESCRIZIONE|
| --- | --- |
| --app-name --application-name [obbligatorio] | Il nome dell'applicazione. |
| --name -n                     [obbligatorio] | Il nome del servizio. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |


## <a name="next-steps"></a>Passaggi successivi
- [Configurare](service-fabric-cli.md) l'interfaccia della riga di comando di Service Fabric.
- Informazioni su come usare l'interfaccia della riga di comando Service Fabric usando gli [script di esempio](/azure/service-fabric/scripts/sfctl-upgrade-application).