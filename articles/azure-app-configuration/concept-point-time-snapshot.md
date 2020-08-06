---
title: Recuperare coppie chiave-valore da un punto nel tempo
titleSuffix: Azure App Configuration
description: Recuperare coppie chiave-valore obsolete usando snapshot temporizzati nella configurazione app Azure
services: azure-app-configuration
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: b706b5d5ec68daa10fd6eac237b7b7416764167b
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87830105"
---
# <a name="point-in-time-snapshot"></a>Snapshot temporizzato

App Azure configurazione mantiene un record delle modifiche apportate ai valori di chiave. Questo record fornisce una sequenza temporale di modifiche chiave-valore. È possibile ricostruire la cronologia di qualsiasi valore chiave e fornire il valore precedente in qualsiasi momento nel periodo di cronologia delle chiavi (7 giorni per gli archivi di livello gratuito o 30 giorni per gli archivi di livello standard). Utilizzando questa funzionalità, è possibile "Time-Travel" indietro e recuperare un valore chiave precedente. È ad esempio possibile ripristinare le impostazioni di configurazione usate prima della distribuzione più recente per eseguire il rollback dell'applicazione alla configurazione precedente.

## <a name="key-value-retrieval"></a>Recupero di coppie chiave-valore

È possibile usare portale di Azure o l'interfaccia della riga di comando per recuperare i valori di chiave passati. Nell'interfaccia della riga di comando di Azure usare `az appconfig revision list` , aggiungendo i parametri appropriati per recuperare i valori richiesti.  Specificare l'istanza di configurazione app Azure fornendo il nome dell'archivio ( `--name <app-config-store-name>` ) o utilizzando una stringa di connessione ( `--connection-string <your-connection-string>` ). Limitare l'output specificando un punto nel tempo specifico ( `--datetime` ) e specificando il numero massimo di elementi da restituire ( `--top` ).

Se l'interfaccia della riga di comando di Azure non è installata localmente, è possibile usare facoltativamente Azure Cloud Shell.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Recuperare tutte le modifiche registrate apportate ai valori di chiave.

```azurecli-interactive
az appconfig revision list --name <your-app-config-store-name>.
```

Recuperare tutte le modifiche registrate per la chiave `environment` e le etichette `test` e `prod` .

```azurecli-interactive
az appconfig revision list --name <your-app-config-store-name> --key environment --label test,prod
```

Recuperare tutte le modifiche registrate nello spazio delle chiavi gerarchico `environment:prod` .

```azurecli-interactive
az appconfig revision list --name <your-app-config-store-name> --key environment:prod:* 
```

Recuperare tutte le modifiche registrate per la chiave `color` in un momento specifico.

```azurecli-interactive
az appconfig revision list --connection-string <your-app-config-connection-string> --key color --datetime "2019-05-01T11:24:12Z" 
```

Recuperare le ultime 10 modifiche registrate apportate ai valori di chiave e restituire solo i valori per `key` , `label` e `last_modified` timestamp.

```azurecli-interactive
az appconfig revision list --name <your-app-config-store-name> --top 10 --fields key label last_modified
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare un'app Web ASP.NET Core](./quickstart-aspnet-core-app.md)  
