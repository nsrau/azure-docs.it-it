---
title: 'Informazioni di riferimento sulle API di Azure Status Monitor V2: Disabilitare il motore di strumentazione | Microsoft Docs'
description: Riferimento all'API Status Monitor V2. Disable-InstrumentationEngine. Monitora le prestazioni del sito Web senza ridistribuire il sito Web. Funziona con le app Web ASP.NET ospitate in locale, in macchine virtuali o in Azure.
services: application-insights
documentationcenter: .net
author: TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: 98f70a457b1e09f755a854a28cfd31a8fc9ee9f2
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71033185"
---
# <a name="status-monitor-v2-api-disable-instrumentationengine"></a>API Status Monitor V2: Disable-InstrumentationEngine

Questo articolo descrive un cmdlet che fa parte del modulo di [PowerShell AZ. ApplicationMonitor](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Descrizione
Disabilita il motore di strumentazione rimuovendo alcune chiavi del registro di sistema.
Riavviare IIS per rendere effettive le modifiche.

> [!IMPORTANT] 
> Questo cmdlet richiede una sessione di PowerShell con autorizzazioni di amministratore.

## <a name="examples"></a>Esempi

```powershell
PS C:\> Disable-InstrumentationEngine
```

## <a name="parameters"></a>Parametri 

### <a name="-verbose"></a>-Verbose
**Parametro comune.** Usare questa opzione per restituire i log dettagliati.

## <a name="output"></a>Output


#### <a name="example-output-from-successfully-disabling-the-instrumentation-engine"></a>Output di esempio dalla disabilitazione corretta del motore di strumentazione

```
Configuring IIS Environment for instrumentation engine...
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]'
Configuring registry for instrumentation engine...
```


## <a name="next-steps"></a>Passaggi successivi

 Eseguire altre operazioni con Status Monitor V2:
 - Usare la guida per la [risoluzione dei problemi](status-monitor-v2-troubleshoot.md) Status Monitor V2.
