---
title: "Riferimento all'API di v2 monitoraggio dello stato di Azure: Impostare la configurazione di | Microsoft Docs"
description: Stato monitoraggio v2 API riferimento Set-ApplicationInsightsMonitoringConfig. Monitorare le prestazioni di siti Web senza ridistribuire il sito Web. Questa funzionalità può essere usata con app Web ASP.NET ospitate in locale, in macchine virtuali o in Azure.
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
ms.openlocfilehash: 2ca738d5d79fc73f892922825d4b731e8ee92b72
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2019
ms.locfileid: "64870493"
---
# <a name="status-monitor-v2-api-set-applicationinsightsmonitoringconfig-v021-alpha"></a>API v2 di Status Monitor: Set-ApplicationInsightsMonitoringConfig (v0.2.1-alpha)

Questo documento descrive un cmdlet che viene fornito come un membro del [modulo di Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> Stato monitoraggio v2 è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [condizioni per l'utilizzo per le anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="description"></a>DESCRIZIONE

Impostare il file di configurazione senza ripetere una reinstallazione completa. Riavviare IIS per rendere effettive le modifiche.

> [!IMPORTANT] 
> Questo cmdlet richiede una sessione di PowerShell con autorizzazioni di amministratore.


## <a name="examples"></a>Esempi

### <a name="example-with-single-instrumentation-key"></a>Esempio con la chiave di strumentazione singola
In questo esempio, tutte le applicazioni nel computer corrente verranno assegnate una chiave di strumentazione singola.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

### <a name="example-with-instrumentation-key-map"></a>Esempio con mappa di chiavi di strumentazione
In questo esempio 
- `MachineFilter` corrisponderà a computer corrente usando il `'.*'` con caratteri jolly.
- `AppFilter='WebAppExclude'` fornisce un `null` chiave di strumentazione. Non è possibile instrumentare l'app.
- `AppFilter='WebAppOne'` verrà assegnato a questa app specifica una chiave di strumentazione univoca.
- `AppFilter='WebAppTwo'` si assegna una chiave di strumentazione univoca questa specifica app.
- Infine `AppFilter` Usa anche il `'.*'` con caratteri jolly per la corrispondenza di tutte le altre App web non trovare una corrispondenza con le regole precedenti e assegna una chiave di strumentazione predefinita.
- Per migliorare la leggibilità solo spazi.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap 
    @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
      @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'},
      @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'},
      @{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'})

```


## <a name="parameters"></a>Parametri 

### <a name="-instrumentationkey"></a>-InstrumentationKey
**Obbligatorio.** Usare questo parametro per specificare un'unica iKey per l'uso in tutte le applicazioni nel computer di destinazione.

### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**Obbligatorio.** Usare questo parametro per specificare più valori iKey e un mapping tra le app da usare la chiave di strumentazione. È possibile creare uno script di installazione singola per diversi computer impostando il MachineFilter. 

> [!IMPORTANT] 
> Le applicazioni corrisponderanno rispetto alle regole nell'ordine in cui è indicati. Di conseguenza è necessario specificare le regole più specifiche prima e ultima le regole più generiche.

#### <a name="schema"></a>SCHEMA
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'})`

- **MachineFilter** è un'espressione regolare obbligatorio c# del nome del computer o macchina virtuale.
    - '. * "corrisponderanno a tutti
    - 'ComputerName' corrisponderà solo i computer con lo stesso nome esatto.
- **AppFilter** è un'espressione regolare obbligatorio c# del nome del computer o macchina virtuale.
    - '. * "corrisponderanno a tutti
    - 'ApplicationName' corrisponderanno solo le applicazioni di IIS con quel nome esatto.
- **Chiave di strumentazione** è necessaria per abilitare il monitoraggio delle applicazioni che corrispondono ai due filtri precedenti.
    - Lasciare questo valore null se si vuole definire regole per escludere il monitoraggio


### <a name="-verbose"></a>-Verbose
**Parametro comune.** Usare questa opzione è attivata per i log dettagliati di output.


## <a name="output"></a>Output

Nessun Output per impostazione predefinita.

#### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkey"></a>Esempio dettagliato di output impostando il file di configurazione tramite - chiave di strumentazione

```
VERBOSE: Operation: InstallWithIkey
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

#### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkeymap"></a>Esempio dettagliato di output impostando il file di configurazione tramite - InstrumentationKeyMap

```
VERBOSE: Operation: InstallWithIkeyMap
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```
