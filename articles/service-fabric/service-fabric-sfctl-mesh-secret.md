---
title: Interfaccia della riga di comando di Azure Service Fabric - sfctl mesh secret | Microsoft Docs
description: Descrive i comandi sfctl mesh secret dell'interfaccia della riga di comando di Service Fabric.
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
ms.openlocfilehash: acc5862a8794da4fde991025eacfcccf6e16916a
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58661208"
---
# <a name="sfctl-mesh-secret"></a>sfctl mesh secret
Consente di ottenere ed eliminare le risorse di tipo segreto mesh.

## <a name="commands"></a>Comandi:

|Comando|DESCRIZIONE|
| --- | --- |
| delete | Elimina la risorsa di tipo segreto. |
| list | Elenca tutte le risorse di tipo segreto. |
| show | Ottiene la risorsa di tipo segreto con il nome specificato. |

## <a name="sfctl-mesh-secret-delete"></a>sfctl mesh secret delete
Elimina la risorsa di tipo segreto.

Elimina la risorsa di tipo segreto specificata e tutti i relativi valori denominati.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --name -n [Obbligatorio] | Nome del segreto. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-mesh-secret-list"></a>sfctl mesh secret list
Elenca tutte le risorse di tipo segreto.

Ottiene le informazioni su tutte le risorse di tipo segreto in un determinato gruppo di risorse. Le informazioni includono la descrizione e altre proprietà del segreto.

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-mesh-secret-show"></a>sfctl mesh secret show
Ottiene la risorsa di tipo segreto con il nome specificato.

Ottiene le informazioni sulla risorsa di tipo segreto con il nome specificato. Le informazioni includono la descrizione e altre proprietà del segreto.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --name -n [Obbligatorio] | Nome del segreto. |

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