---
title: Interfaccia della riga di comando Azure Service Fabric - sfctl container | Microsoft Docs
description: Descrive i comandi dell'interfaccia della riga di comando Service Fabric sfctl container.
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
ms.openlocfilehash: 13de6ff7b3e5a41eced5ca49a3af38fab60ba0a0
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901302"
---
# <a name="sfctl-container"></a>sfctl container
Esegue i comandi relativi al contenitore in un nodo del cluster.

## <a name="commands"></a>Comandi:

|Comando|Description|
| --- | --- |
| invoke-api | Richiama l'API del contenitore in un contenitore distribuito in un nodo di Service Fabric per il pacchetto di codice specificato. |
| log | Ottiene i log dei contenitori per il contenitore distribuito in un nodo di Service Fabric. |

## <a name="sfctl-container-invoke-api"></a>sfctl container invoke-api
Richiama l'API del contenitore in un contenitore distribuito in un nodo di Service Fabric per il pacchetto di codice specificato.

### <a name="arguments"></a>Argomenti

|Argomento|Description|
| --- | --- |
| --application-id [Obbligatorio] | Identità dell'applicazione. <br><br> Si tratta in genere del nome completo dell'applicazione senza lo schema URI "fabric\:". A partire dalla versione 6.0, i nomi gerarchici sono delimitati dal carattere "\~". Ad esempio, se il nome dell'applicazione è "fabric\:/myapp/app1", l'identità dell'applicazione sarà "myapp\~app1" nella versione 6.0 e successive e "myapp/app1" nelle versioni precedenti. |
| --code-package-instance-id [Obbligatorio] | ID che identifica in modo univoco un'istanza di pacchetto di codice distribuita in un nodo di Service Fabric. <br><br> Il valore può essere recuperato tramite "service code-package-list". |
| --code-package-name [Obbligatorio] | Nome di un pacchetto di codice specificato nel manifesto del servizio registrato come parte di un tipo di applicazione in un cluster di Service Fabric. |
| --container-api-uri-path [Obbligatorio] | Percorso dell'URI dell'API REST del contenitore. Usare "{ID}" al posto del nome/ID del contenitore. |
| --node-name                [Obbligatorio] | Il nome del nodo. |
| --service-manifest-name    [obbligatorio] | Nome di un manifesto del servizio registrato come parte di un tipo di applicazione in un cluster di Service Fabric. |
| --container-api-body | Corpo della richiesta HTTP per l'API REST del contenitore. |
| --container-api-content-type | Tipo di contenuto per l'API REST del contenitore. L'impostazione predefinita è "application/json". |
| --container-api-http-verb | Verbo HTTP per l'API REST del contenitore. L'impostazione predefinita è GET. |
| --timeout -t | Valore predefinito\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Description|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i log di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-container-logs"></a>sfctl container logs
Ottiene i log dei contenitori per il contenitore distribuito in un nodo di Service Fabric.

### <a name="arguments"></a>Argomenti

|Argomento|Description|
| --- | --- |
| --application-id [Obbligatorio] | Identità dell'applicazione. <br><br> Si tratta in genere del nome completo dell'applicazione senza lo schema URI "fabric\:". A partire dalla versione 6.0, i nomi gerarchici sono delimitati dal carattere "\~". Ad esempio, se il nome dell'applicazione è "fabric\:/myapp/app1", l'identità dell'applicazione sarà "myapp\~app1" nella versione 6.0 e successive e "myapp/app1" nelle versioni precedenti. |
| --code-package-instance-id [Obbligatorio] | ID istanza del pacchetto di codice, che può essere recuperato da "service code-package-list". |
| --code-package-name [Obbligatorio] | Nome di un pacchetto di codice specificato nel manifesto del servizio registrato come parte di un tipo di applicazione in un cluster di Service Fabric. |
| --node-name                [Obbligatorio] | Il nome del nodo. |
| --service-manifest-name    [obbligatorio] | Nome di un manifesto del servizio registrato come parte di un tipo di applicazione in un cluster di Service Fabric. |
| --tail | Numero di righe da visualizzare alla fine dei log. Il valore predefinito è 100. "all" per visualizzare i log completi. |
| --timeout -t | Valore predefinito\: 60. |

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