---
title: Interfaccia della riga di comando Azure Service Fabric - sfctl container | Microsoft Docs
description: Descrive i comandi dell'interfaccia della riga di comando Service Fabric sfctl container.
services: service-fabric
documentationcenter: na
author: Christina-Kang
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 07/31/2018
ms.author: bikang
ms.openlocfilehash: 27108d27ee27346e4cba44e6778faff56df70a36
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2018
ms.locfileid: "39495129"
---
# <a name="sfctl-container"></a>sfctl container
Esegue i comandi relativi al contenitore in un nodo del cluster.

## <a name="commands"></a>Comandi:

|Comando|DESCRIZIONE|
| --- | --- |
| invoke-api | Richiama l'API REST del contenitore. |
| logs | Recupero dei log dei contenitori. |

## <a name="sfctl-container-invoke-api"></a>sfctl container invoke-api
Richiama l'API REST del contenitore.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --application-id [Obbligatorio] | Identità dell'applicazione. |
| --code-package-instance-id [Obbligatorio] | ID istanza del pacchetto di codice, che può essere recuperato da "service code-package-list". |
| --code-package-name [Obbligatorio] | Nome del pacchetto di codice. |
| --container-api-uri-path [Obbligatorio] | Percorso dell'URI dell'API REST del contenitore. Usare "{id}" al posto del nome/ID del contenitore. |
| --node-name                [Obbligatorio] | Il nome del nodo. |
| --service-manifest-name    [obbligatorio] | Nome del manifesto del servizio. |
| --container-api-body | Corpo della richiesta HTTP per l'API REST del contenitore. |
| --container-api-content-type | Tipo di contenuto per l'API REST del contenitore. L'impostazione predefinita è "application/json". |
| --container-api-http-verb | Verbo HTTP per l'API REST del contenitore. L'impostazione predefinita è GET. |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |

## <a name="sfctl-container-logs"></a>sfctl container logs
Recupero dei log dei contenitori.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --application-id [Obbligatorio] | Identità dell'applicazione. |
| --code-package-instance-id [Obbligatorio] | ID istanza del pacchetto di codice, che può essere recuperato da "service code-package-list". |
| --code-package-name [Obbligatorio] | Nome del pacchetto di codice. |
| --node-name                [Obbligatorio] | Il nome del nodo. |
| --service-manifest-name    [obbligatorio] | Nome del manifesto del servizio. |
| --tail | Restituisce solo questo numero di righe di log dalla fine dei log. Specificare un numero intero oppure all per restituire tutte le righe del log. L'impostazione predefinita è "all". |
| --timeout -t | Timeout del server in secondi.  Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug. |
| --help -h | Mostra questo messaggio della Guida e l'uscita. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi. |


## <a name="next-steps"></a>Passaggi successivi
- [Configurare](service-fabric-cli.md) l'interfaccia della riga di comando di Service Fabric.
- Informazioni su come usare l'interfaccia della riga di comando Service Fabric usando gli [script di esempio](/azure/service-fabric/scripts/sfctl-upgrade-application).