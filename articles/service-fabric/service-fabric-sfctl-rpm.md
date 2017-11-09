---
title: Interfaccia della riga di comando di Azure Service Fabric - sfctl rpm | Microsoft Docs
description: Descrive i comandi dell'interfaccia della riga di comando di Service Fabric sfctl rpm.
services: service-fabric
documentationcenter: na
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: cli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 09/26/2017
ms.author: ryanwi
ms.openlocfilehash: f032af4714ad458fa6ad6fb0741f689d44f4098b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="sfctl-rpm"></a>sfctl rpm
Consente di eseguire query e inviare comandi al servizio di gestione di ripristino.

## <a name="commands"></a>Comandi:
|Comando|Descrizione|
| --- | --- |
|    approve-force| Forza l'approvazione dell'attività di ripristino specificata.|
|    delete       | Elimina un'attività di ripristino completata.|
|    list         | Recupera un elenco di attività di ripristino corrispondenti ai filtri specificati.|

## <a name="sfctl-rpm-delete"></a>sfctl rpm delete
Elimina un'attività di ripristino completata.

Questa API supporta la piattaforma Service Fabric. non deve essere utilizzata direttamente dal codice. 

### <a name="arguments"></a>Argomenti
|Argomento|Descrizione|
| --- | --- |
|    --task-id [obbligatorio]| L'ID dell'attività di ripristino completata da eliminare.|
|    --version           | Il numero di versione corrente dell'attività di ripristino. Se è diverso da zero, la richiesta verrà completata solo se questo valore corrisponde alla versione corrente effettiva dell'attività di ripristino. Se è zero, non viene eseguito alcun controllo di versione.|

### <a name="global-arguments"></a>Argomenti globali
|Argomento|Descrizione|
| --- | --- |
|    --debug             | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug.|
|    --help -h           | Mostra questo messaggio della Guida e l'uscita.|
|    --output -o         | Formato di output.  Valori consentiti: json, jsonc, tabella, tsv.  Predefinito: json.
|    --query             | Stringa di query JMESPath. Per maggiori informazioni ed esempi, vedere http://jmespath.org/.|
|    --verbose           | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi.|


## <a name="sfctl-rpm-list"></a>sfctl rpm list
Recupera un elenco di attività di ripristino corrispondenti ai filtri specificati.

Questa API supporta la piattaforma Service Fabric. non deve essere utilizzata direttamente dal codice. 

### <a name="arguments"></a>Argomenti
|Argomento|Descrizione|
| --- | --- |
|    --executor-filter| Il nome dell'executor di ripristino le cui attività richieste devono essere incluse nell'elenco.|
|    --state-filter   | Un operatore OR bit per bit dei valori seguenti, che specifica gli stati delle attività che devono essere inclusi nell'elenco dei risultati. - 1 - Creato - 2 - Richiesto - 4 - Preparazione in corso - 8 - Approvato - 16 - Esecuzione in corso - 32 - Ripristino in corso - 64 - Completato.|
|    --task-id-filter | Il prefisso dell'ID dell'attività di ripristino da ricercare.|

### <a name="global-arguments"></a>Argomenti globali
|Argomento|Descrizione|
| --- | --- |
|    --debug          | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug.|
|    --help -h        | Mostra questo messaggio della Guida e l'uscita.|
|    --output -o      | Formato di output.  Valori consentiti: json, jsonc, tabella, tsv.  Default| json.|
|    --query          | Stringa di query JMESPath. Per maggiori informazioni ed esempi, vedere http://jmespath.org/.|
|    --verbose        | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi.|

## <a name="next-steps"></a>Passaggi successivi
- [Configurare](service-fabric-cli.md) l'interfaccia della riga di comando di Service Fabric.
- Informazioni su come usare l'interfaccia della riga di comando Service Fabric usando gli [script di esempio](/azure/service-fabric/scripts/sfctl-upgrade-application).