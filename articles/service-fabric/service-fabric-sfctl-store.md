---
title: Interfaccia della riga di comando Azure Service Fabric - sfctl store | Documentazione Microsoft
description: Descrive i comandi dell'interfaccia della riga di comando Service Fabric sfctl store.
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
ms.date: 09/22/2017
ms.author: ryanwi
ms.translationtype: HT
ms.sourcegitcommit: cb9130243bdc94ce58d6dfec3b96eb963cdaafb0
ms.openlocfilehash: 2af6dff4ffcdf295731f2d61b5f9e35af40615e5
ms.contentlocale: it-it
ms.lasthandoff: 09/26/2017

---
# <a name="sfctl-store"></a>sfctl store
Esegue operazioni base a livello di file nell'archivio immagini del cluster.

## <a name="commands"></a>Comandi:

|Comando|Descrizione|
| --- | --- |
|    delete| Elimina il contenuto dell’archivio immagini esistente.|
|    root-info| Mostra le informazioni sul contenuto alla radice dell'archivio immagini.|
|    stat  | Mostra le informazioni sul contenuto dell’archivio immagini.|


## <a name="sfctl-store-delete"></a>sfctl store delete
Elimina il contenuto dell’archivio immagini esistente.

Elimina il contenuto dell’archivio immagini esistente trovato all'interno del percorso relativo dell’archivio immagini specificato. Questo comando può essere utilizzato per eliminare i pacchetti dell’applicazione caricati dopo l'esecuzione del provisioning.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --content-path [Obbligatorio]| Percorso relativo dalla radice al file o cartella nell'archivio immagini.|
| --timeout -t          | Timeout del server in secondi.  Predefinito: 60.|

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug               | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug.|
| --help -h             | Mostra questo messaggio della Guida e l’uscita.|
| --output -o           | Formato di output.  Valori consentiti: json, jsonc, tabella, tsv.  Predefinito: json.|
| --query               | Stringa di query JMESPath. Per maggiori informazioni ed esempi, vedere http://jmespath.org/.|
| --verbose             | Aumenta il livello di dettaglio di registrazione. Utilizzare --debug per i log di debug completi.|

## <a name="sfctl-store-stat"></a>sfctl store stat
Mostra le informazioni sul contenuto dell’archivio immagini.

Restituisce le informazioni sul contenuto dell’archivio immagini al percorso di contenuto specificato relativo alla radice dell'archivio immagini.

### <a name="arguments"></a>Argomenti

|Argomento|Descrizione|
| --- | --- |
| --content-path [Obbligatorio]| Percorso relativo dalla radice al file o cartella nell'archivio immagini.|
| --timeout -t          | Timeout del server in secondi.  Predefinito: 60.|

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug               | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug.|
| --help -h             | Mostra questo messaggio della Guida e l’uscita.|
| --output -o           | Formato di output.  Valori consentiti: json, jsonc, tabella, tsv.  Predefinito: json.|
| --query               | Stringa di query JMESPath. Per maggiori informazioni ed esempi, vedere http://jmespath.org/.|
| --verbose             | Aumenta il livello di dettaglio di registrazione. Utilizzare --debug per i log di debug completi.|

## <a name="next-steps"></a>Passaggi successivi
- [Configurare](service-fabric-cli.md) l'interfaccia della riga di comando di Service Fabric.
- Informazioni su come utilizzare l’interfaccia della riga di comando Service Fabric usando gli [script di esempio](/azure/service-fabric/scripts/sfctl-upgrade-application).
