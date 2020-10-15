---
title: INTERFACCIA della riga di comando di Azure Service Fabric-sfctl mesh code-package-log
description: Informazioni su sfctl, l'interfaccia della riga di comando di Azure Service Fabric. Include un elenco di comandi per ottenere i log per un pacchetto di codice specificato.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 9ac1d85a1a498f9f6fcd0a03f8f819d1cdfcac33
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86257308"
---
# <a name="sfctl-mesh-code-package-log"></a>sfctl mesh code-package-log
Consente di ottenere i log per il contenitore del pacchetto di codice specificato per la replica del servizio specificata.

## <a name="commands"></a>Comandi

|Comando|Descrizione|
| --- | --- |
| get | Ottiene i log dal contenitore. |

## <a name="sfctl-mesh-code-package-log-get"></a>sfctl mesh code-package-log get
Ottiene i log dal contenitore.

Ottiene i log per il contenitore del pacchetto di codice specificato per la replica del servizio.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --app-name --application-name [obbligatorio] | Il nome dell'applicazione. |
| --code-package-name           [obbligatorio] | Nome del pacchetto di codice del servizio. |
| --replica-name                [obbligatorio] | Nome della replica di Service Fabric. |
| --service-name                [obbligatorio] | Il nome del servizio. |
| --tail | Numero di righe da visualizzare alla fine dei log. Il valore predefinito è 100. "all" per visualizzare i log completi. |

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
- Informazioni su come usare l'interfaccia della riga di comando Service Fabric usando gli [script di esempio](./scripts/sfctl-upgrade-application.md).
