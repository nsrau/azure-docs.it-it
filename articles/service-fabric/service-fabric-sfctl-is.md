---
title: 'INTERFACCIA della riga di comando di Azure Service Fabric: sfctl'
description: Informazioni su sfctl, l'interfaccia della riga di comando di Azure Service Fabric. Include un elenco di comandi per la gestione dell'infrastruttura.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: a64aaca97c9df61d795c82d64e6048d6f9ae032f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "76906086"
---
# <a name="sfctl-is"></a>sfctl is
Consente di eseguire query e inviare comandi al servizio di infrastruttura.

## <a name="commands"></a>Comandi

|Comando|Descrizione|
| --- | --- |
| . | Richiama un comando amministrativo sull'istanza del servizio di infrastruttura specificato. |
| query | Richiama una query di sola lettura sull'istanza del servizio di infrastruttura specificato. |

## <a name="sfctl-is-command"></a>sfctl is command
Richiama un comando amministrativo sull'istanza del servizio di infrastruttura specificato.

Per i cluster che dispongono di una o più istanze del servizio di infrastruttura configurato, questo API fornisce un modo per inviare comandi specifici dell'infrastruttura a una particolare istanza del servizio di infrastruttura. I comandi disponibili e i relativi formati di risposta corrispondente variano a seconda dell'infrastruttura in esecuzione sul cluster. Questa API supporta la piattaforma Service Fabric. non deve essere utilizzata direttamente dal codice.

### <a name="arguments"></a>Arguments

|Argomento|Description|
| --- | --- |
| --command [Obbligatorio] | Il testo del comando da richiamare. Il contenuto del comando è specifico dell'infrastruttura. |
| --service-id | L'identità del servizio di infrastruttura. <br><br> Si tratta del nome completo del servizio di infrastruttura senza lo schema URI "fabric\:". Questo parametro è obbligatorio solo per il cluster con più di un'istanza del servizio di infrastruttura in esecuzione. |
| --timeout -t | Impostazione predefinita\: 60. |

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Description|
| --- | --- |
| --debug | Aumenta il livello di dettaglio della registrazione per mostrare tutti i log di debug. |
| --help -h | Visualizza questo messaggio della guida ed esce. |
| --output -o | Formato di output.  Valori consentiti\: json, jsonc, table, tsv.  Valore predefinito\: json. |
| --query | Stringa di query JMESPath. Per altre informazioni ed esempi, vedere http\://jmespath.org/. |
| --verbose | Aumenta il livello di dettaglio della registrazione. Usare --debug per log di debug completi. |

## <a name="sfctl-is-query"></a>sfctl is query
Richiama una query di sola lettura sull'istanza del servizio di infrastruttura specificato.

Per i cluster che dispongono di una o più istanze del servizio di infrastruttura configurato, questo API fornisce un modo per inviare query specifiche dell'infrastruttura a una particolare istanza del servizio di infrastruttura. I comandi disponibili e i relativi formati di risposta corrispondente variano a seconda dell'infrastruttura in esecuzione sul cluster. Questa API supporta la piattaforma Service Fabric. non deve essere utilizzata direttamente dal codice.

### <a name="arguments"></a>Arguments

|Argomento|Description|
| --- | --- |
| --command [Obbligatorio] | Il testo del comando da richiamare. Il contenuto del comando è specifico dell'infrastruttura. |
| --service-id | L'identità del servizio di infrastruttura. <br><br> Si tratta del nome completo del servizio di infrastruttura senza lo schema URI "fabric\:". Questo parametro è obbligatorio solo per il cluster con più di un'istanza del servizio di infrastruttura in esecuzione. |
| --timeout -t | Impostazione predefinita\: 60. |

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
