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
ms.openlocfilehash: 1e2a4f7a7bc5db1b6a49f085821f7fa2bde54229
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77523659"
---
# <a name="point-in-time-snapshot"></a>Snapshot temporizzato

App Azure configurazione mantiene un record delle modifiche apportate alle coppie chiave-valore. Questo record fornisce una sequenza temporale di modifiche chiave-valore. È possibile ricostruire la cronologia di qualsiasi valore chiave e fornire il relativo valore passato in qualsiasi momento nei sette giorni precedenti. Utilizzando questa funzionalità, è possibile "Time-Travel" indietro e recuperare un valore chiave precedente. È ad esempio possibile ripristinare le impostazioni di configurazione usate prima della distribuzione più recente per eseguire il rollback dell'applicazione alla configurazione precedente.

## <a name="key-value-retrieval"></a>Recupero di coppie chiave-valore

È possibile utilizzare Azure PowerShell per recuperare i valori di chiave passati.  Usare `az appconfig revision list` , aggiungendo i parametri appropriati per recuperare i valori richiesti.  Specificare l'istanza di configurazione app Azure fornendo il nome dell'archivio ( `--name {app-config-store-name}` ) o utilizzando una stringa di connessione ( `--connection-string {your-connection-string}` ). Limitare l'output specificando un punto nel tempo specifico ( `--datetime` ) e specificando il numero massimo di elementi da restituire ( `--top` ).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Recuperare tutte le modifiche registrate apportate ai valori di chiave.

```azurepowershell
az appconfig revision list --name {your-app-config-store-name}.
```

Recuperare tutte le modifiche registrate per la chiave `environment` e le etichette `test` e `prod` .

```azurepowershell
az appconfig revision list --name {your-app-config-store-name} --key environment --label test,prod
```

Recuperare tutte le modifiche registrate nello spazio delle chiavi gerarchico `environment:prod` .

```azurepowershell
az appconfig revision list --name {your-app-config-store-name} --key environment:prod:* 
```

Recuperare tutte le modifiche registrate per la chiave `color` in un momento specifico.

```azurepowershell
az appconfig revision list --connection-string {your-app-config-connection-string} --key color --datetime "2019-05-01T11:24:12Z" 
```

Recuperare le ultime 10 modifiche registrate apportate ai valori di chiave e restituire solo i valori per `key` , `label` e `last-modified` timestamp.

```azurepowershell
az appconfig revision list --name {your-app-config-store-name} --top 10 --fields key,label,last-modified
```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare un'app Web ASP.NET Core](./quickstart-aspnet-core-app.md)  
