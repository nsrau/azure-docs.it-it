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
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 12/22/2017
ms.author: ryanwi
ms.openlocfilehash: d4ca3c35c34736c3b4824f956a6a72002c891877
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/18/2018
---
# <a name="sfctl-store"></a>sfctl store
Esegue operazioni base a livello di file nell'archivio immagini del cluster.

## <a name="commands"></a>Comandi:

|Comando|DESCRIZIONE|
| --- | --- |
|    delete| Elimina il contenuto dell’archivio immagini esistente.|
|    root-info| Mostra le informazioni sul contenuto alla radice dell'archivio immagini.|
|    stat  | Mostra le informazioni sul contenuto dell’archivio immagini.|


## <a name="sfctl-store-delete"></a>sfctl store delete
Elimina il contenuto dell’archivio immagini esistente.

Elimina il contenuto dell’archivio immagini esistente trovato all'interno del percorso relativo dell’archivio immagini specificato. Questo comando può essere utilizzato per eliminare i pacchetti dell’applicazione caricati dopo l'esecuzione del provisioning.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --content-path [Obbligatorio]| Percorso relativo dalla radice al file o cartella nell'archivio immagini.|
| --timeout -t          | Timeout del server in secondi.  Predefinito: 60.|

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug               | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug.|
| --help -h             | Mostra questo messaggio della Guida e l'uscita.|
| --output -o           | Formato di output.  Valori consentiti: json, jsonc, tabella, tsv.  Predefinito: json.|
| --query               | Stringa di query JMESPath. Per maggiori informazioni ed esempi, vedere http://jmespath.org/.|
| --verbose             | Aumenta il livello di dettaglio di registrazione. Utilizzare --debug per i log di debug completi.|

## <a name="sfctl-store-stat"></a>sfctl store stat
Mostra le informazioni sul contenuto dell’archivio immagini.

Restituisce le informazioni sul contenuto dell’archivio immagini al percorso di contenuto specificato relativo alla radice dell'archivio immagini.

### <a name="arguments"></a>Argomenti

|Argomento|DESCRIZIONE|
| --- | --- |
| --content-path [Obbligatorio]| Percorso relativo dalla radice al file o cartella nell'archivio immagini.|
| --timeout -t          | Timeout del server in secondi.  Predefinito: 60.|

### <a name="global-arguments"></a>Argomenti globali

|Argomento|Descrizione|
| --- | --- |
| --debug               | Aumenta il livello di dettaglio di registrazione per mostrare tutti i registri di debug.|
| --help -h             | Mostra questo messaggio della Guida e l'uscita.|
| --output -o           | Formato di output.  Valori consentiti: json, jsonc, tabella, tsv.  Predefinito: json.|
| --query               | Stringa di query JMESPath. Per maggiori informazioni ed esempi, vedere http://jmespath.org/.|
| --verbose             | Aumenta il livello di dettaglio di registrazione. Usare --debug per i log di debug completi.|

## <a name="next-steps"></a>Passaggi successivi
- [Configurare](service-fabric-cli.md) l'interfaccia della riga di comando di Service Fabric.
- Informazioni su come usare l'interfaccia della riga di comando Service Fabric usando gli [script di esempio](/azure/service-fabric/scripts/sfctl-upgrade-application).