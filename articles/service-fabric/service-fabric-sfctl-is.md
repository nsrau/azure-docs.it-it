---
title: Interfaccia della riga di comando Azure Service Fabric - sfctl is | Documentazione Microsoft
description: Descrive i comandi dell'interfaccia della riga di comando Service Fabric sfctl is.
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: cli
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 02/22/2018
ms.author: ryanwi
ms.openlocfilehash: d3a4c7a308bdf7132cebffc13a5e7214ec73eaf2
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="sfctl-is"></a>sfctl is
Consente di eseguire query e inviare comandi al servizio di infrastruttura.

## <a name="commands"></a>Comandi:

|Comando|DESCRIZIONE|
| --- | --- |
|    command| Richiama un comando amministrativo sull'istanza del servizio di infrastruttura specificato.|
|    query  | Richiama una query di sola lettura sull'istanza del servizio di infrastruttura specificato.|


## <a name="sfctl-is-command"></a>sfctl is command
Richiama un comando amministrativo sull'istanza del servizio di infrastruttura specificato.

Per i cluster che dispongono di una o più istanze del servizio di infrastruttura configurato, questo API fornisce un modo per inviare comandi specifici dell'infrastruttura a una particolare istanza del servizio di infrastruttura. I comandi disponibili e i relativi formati di risposta corrispondente variano a seconda dell'infrastruttura in esecuzione sul cluster. Questa API supporta la piattaforma Service Fabric. non deve essere utilizzata direttamente dal codice. 

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --command [Obbligatorio]| Il testo del comando da richiamare. Il contenuto del comando è specifico dell'infrastruttura. |
| --service-id     | L'identità del servizio di infrastruttura. L'ID è il nome completo del servizio di infrastruttura senza lo schema URI "fabric:". Questo parametro è obbligatorio solo per i cluster con più di un'istanza del servizio di infrastruttura in esecuzione.|
| --timeout -t     | Timeout del server in secondi.  Predefinito: 60.|

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug          | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug.|
| --help -h        | Mostra questo messaggio della Guida e l'uscita.|
| --output -o      | Formato di output.  Valori consentiti: json, jsonc, tabella, tsv.  Predefinito: json.|
| --query          | Stringa di query JMESPath. Per maggiori informazioni ed esempi, vedere http://jmespath.org/.|
| --verbose        | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi.|

## <a name="sfctl-is-query"></a>sfctl is query
Richiama una query di sola lettura sull'istanza del servizio di infrastruttura specificato.

Per i cluster che dispongono di una o più istanze del servizio di infrastruttura configurato, questo API fornisce un modo per inviare query specifiche dell'infrastruttura a una particolare istanza del servizio di infrastruttura. I comandi disponibili e i relativi formati di risposta corrispondente variano a seconda dell'infrastruttura in esecuzione sul cluster. Questa API supporta la piattaforma Service Fabric. non deve essere utilizzata direttamente dal codice.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --command [Obbligatorio]| Il testo del comando da richiamare. Il contenuto del comando è specifico dell'infrastruttura.|
| --service-id     | L'identità del servizio di infrastruttura. L'ID è il nome completo del servizio di infrastruttura senza lo schema URI "fabric:". Questo parametro è obbligatorio solo per i cluster con più di un'istanza del servizio di infrastruttura in esecuzione.|
| --timeout -t     | Timeout del server in secondi.  Predefinito: 60.|

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug          | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug.|
| --help -h        | Mostra questo messaggio della Guida e l'uscita.|
| --output -o      | Formato di output.  Valori consentiti: json, jsonc, tabella, tsv.  Predefinito: json.|
| --query          | Stringa di query JMESPath. Per maggiori informazioni, vedere http://jmespath.org/.|
| --verbose        | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi.|

## <a name="next-steps"></a>Passaggi successivi
- [Configurare](service-fabric-cli.md) l'interfaccia della riga di comando di Service Fabric.
- Informazioni su come usare l'interfaccia della riga di comando Service Fabric usando gli [script di esempio](/azure/service-fabric/scripts/sfctl-upgrade-application).