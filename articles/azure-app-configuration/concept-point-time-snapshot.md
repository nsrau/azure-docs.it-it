---
title: Recuperare coppie chiave-valore da un punto nel tempoRetrieve key-value pairs from a point-in-time
titleSuffix: Azure App Configuration
description: Recuperare le vecchie coppie chiave-valore usando snapshot point-in-time nella configurazione delle app di AzureRetrieve old key-value pairs using point-in-time snapshots in Azure App Configuration
services: azure-app-configuration
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 1e2a4f7a7bc5db1b6a49f085821f7fa2bde54229
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77523659"
---
# <a name="point-in-time-snapshot"></a>Snapshot temporizzato

Configurazione app di Azure mantiene un record delle modifiche apportate alle coppie chiave-valore. Questo record fornisce una sequenza temporale delle modifiche chiave-valore. È possibile ricostruire la cronologia di qualsiasi chiave-valore e fornire il suo valore passato in qualsiasi momento nei sette giorni precedenti. Utilizzando questa funzione, è possibile "viaggiare nel tempo" all'indietro e recuperare un vecchio valore-chiave. Ad esempio, è possibile ripristinare le impostazioni di configurazione utilizzate prima della distribuzione più recente per eseguire il rollback dell'applicazione alla configurazione precedente.

## <a name="key-value-retrieval"></a>Recupero di coppie chiave-valore

È possibile usare Azure PowerShell per recuperare i valori delle chiavi passati.  Utilizzare `az appconfig revision list`, aggiungendo i parametri appropriati per recuperare i valori necessari.  Specificare l'istanza di Configurazione app`--name {app-config-store-name}`di Azure specificando il`--connection-string {your-connection-string}`nome dell'archivio ( ) o usando una stringa di connessione ( ). Limitare l'output specificando un momento`--datetime`specifico ( ) e specificando il`--top`numero massimo di elementi da restituire ( ).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Recuperare tutte le modifiche registrate alle chiave-valore.

```azurepowershell
az appconfig revision list --name {your-app-config-store-name}.
```

Recuperare tutte le modifiche `environment` registrate `test` per `prod`la chiave, le etichette e .

```azurepowershell
az appconfig revision list --name {your-app-config-store-name} --key environment --label test,prod
```

Recuperare tutte le modifiche registrate `environment:prod`nello spazio chiave gerarchico .

```azurepowershell
az appconfig revision list --name {your-app-config-store-name} --key environment:prod:* 
```

Recuperare tutte le modifiche `color` registrate per la chiave in un momento specifico.

```azurepowershell
az appconfig revision list --connection-string {your-app-config-connection-string} --key color --datetime "2019-05-01T11:24:12Z" 
```

Recuperare le ultime 10 modifiche registrate alle proprie coppie `key` `label`chiave-valore e restituire solo i valori per , e `last-modified` data e ora.

```azurepowershell
az appconfig revision list --name {your-app-config-store-name} --top 10 --fields key,label,last-modified
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare un'app Web ASP.NET Core](./quickstart-aspnet-core-app.md)  
