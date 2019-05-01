---
title: "Riferimento all'API di v2 monitoraggio dello stato di Azure: Abilitare il motore di strumentazione | Microsoft Docs"
description: Stato monitoraggio v2 API riferimento Enable-InstrumentationEngine. Monitorare le prestazioni di siti Web senza ridistribuire il sito Web. Questa funzionalità può essere usata con app Web ASP.NET ospitate in locale, in macchine virtuali o in Azure.
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
ms.openlocfilehash: 7d337c9b6b22f3abfcb4aea1c47127706ed9e9d7
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2019
ms.locfileid: "64870508"
---
# <a name="status-monitor-v2-api-enable-instrumentationengine-v021-alpha"></a>API v2 di Status Monitor: Enable-InstrumentationEngine (v0.2.1-alpha)

Questo documento descrive un cmdlet che viene fornito come un membro del [modulo di Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> Stato monitoraggio v2 è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [condizioni per l'utilizzo per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="description"></a>DESCRIZIONE

Questo cmdlet abiliterà il motore di strumentazione impostando alcune chiavi del Registro di sistema.
Riavviare IIS per rendere effettive queste modifiche.

Il motore di strumentazione possibile integrare i dati raccolti dagli SDK .NET.
Gli eventi e i messaggi verranno raccolti che descrivono l'esecuzione di un processo gestito. Tra cui i codici di risultato delle dipendenze, verbi HTTP e testo comando SQL. 

Abilitare il motore di strumentazione se:
- È già stato attivato il monitoraggio usando il cmdlet Abilita ma non è stata abilitata la InstrumentationEngine.
- È stato manualmente instrumentate dell'applicazione con gli SDK di .NET e raccogliere dati di telemetria aggiuntivi.

> [!IMPORTANT] 
> Questo cmdlet richiede una sessione di PowerShell con autorizzazioni di amministratore.

> [!NOTE] 
> Questo cmdlet è necessario leggere e accettare la licenza e informativa sulla privacy.

> [!NOTE] 
> Il motore di strumentazione consente di aggiungere overhead ed è disattivata per impostazione predefinita.

## <a name="examples"></a>Esempi

```powershell
PS C:\> Enable-InstrumentationEngine
```

## <a name="parameters"></a>Parametri 

### <a name="-acceptlicense"></a>-AcceptLicense
**Facoltativo.** Utilizzare questa opzione per accettare la licenza e informativa sulla privacy in installazioni headless.

### <a name="-verbose"></a>-Verbose
**Parametro comune.** Usare questa opzione è attivata per i log dettagliati di output.

## <a name="output"></a>Output


#### <a name="example-output-from-successfully-enabling-the-instrumentation-engine"></a>Output di esempio da abilitato correttamente il motore di strumentazione

```
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
```
