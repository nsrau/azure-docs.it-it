---
title: "Riferimento all'API di v2 monitoraggio dello stato di Azure: Disabilitare motore Strumentazione | Microsoft Docs"
description: Stato monitoraggio v2 API riferimento Disable-InstrumentationEngine. Monitorare le prestazioni di siti Web senza ridistribuire il sito Web. Questa funzionalità può essere usata con app Web ASP.NET ospitate in locale, in macchine virtuali o in Azure.
services: application-insights
documentationcenter: .net
author: MS-TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: 8166ee1549355f165c7c86c99bc40afdc299c9e2
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2019
ms.locfileid: "64870538"
---
# <a name="status-monitor-v2-api-disable-instrumentationengine-v021-alpha"></a>API v2 di Status Monitor: Disable-InstrumentationEngine (v0.2.1-alfa)

Questo documento descrive un cmdlet che viene fornito come un membro del [modulo di Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> Stato monitoraggio v2 è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [condizioni per l'utilizzo per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="description"></a>DESCRIZIONE

Questo cmdlet verrà disabilitato il motore di strumentazione tramite la rimozione di alcune chiavi del Registro di sistema.
Riavviare IIS per rendere effettive queste modifiche.

> [!IMPORTANT] 
> Questo cmdlet richiede una sessione di PowerShell con autorizzazioni di amministratore.

## <a name="examples"></a>Esempi

```powershell
PS C:\> Disable-InstrumentationEngine
```

## <a name="parameters"></a>Parametri 

### <a name="-verbose"></a>-Verbose
**Parametro comune.** Usare questa opzione è attivata per i log dettagliati di output.

## <a name="output"></a>Output


#### <a name="example-output-from-successfully-disabling-the-instrumentation-engine"></a>Output di esempio di disabilitare correttamente il motore di strumentazione

```
Configuring IIS Environment for instrumentation engine...
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]'
Configuring registry for instrumentation engine...
```
