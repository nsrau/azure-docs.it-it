---
title: Risoluzione dei problemi relativi a Rilevamento modifiche e inventario
description: Informazioni su come risolvere i problemi relativi alla soluzione di inventario e Rilevamento modifiche di automazione di Azure.
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 4f230cd0965d58f690d333cd62f2c7c1d499e8d1
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82582156"
---
# <a name="troubleshoot-change-tracking-and-inventory-issues"></a>Risolvere i problemi di Rilevamento modifiche e inventario

Questo articolo descrive come risolvere i problemi relativi all'inventario e al Rilevamento modifiche di automazione di Azure.

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per le istruzioni di installazione del modulo Az sul ruolo di lavoro ibrido per runbook, vedere [Installare il modulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Per l'account di automazione, è possibile aggiornare i moduli alla versione più recente usando [come aggiornare i moduli Azure PowerShell in automazione di Azure](../automation-update-azure-modules.md).

## <a name="windows"></a>Windows

### <a name="scenario-change-tracking-and-inventory-records-arent-showing-for-windows-machines"></a><a name="records-not-showing-windows"></a>Scenario: i record di Rilevamento modifiche e inventario non vengono visualizzati per i computer Windows

#### <a name="issue"></a>Problema

Non viene visualizzato alcun Rilevamento modifiche e i risultati dell'inventario per i computer Windows caricati.

#### <a name="cause"></a>Causa

Questo errore può avere le seguenti cause:

* L'agente di Log Analytics di Azure per Windows non è in esecuzione.
* Viene bloccata la comunicazione con l'account di automazione.
* I Management Pack Rilevamento modifiche e Inventory non vengono scaricati.
* La VM da caricare potrebbe provenire da un computer clonato che non è stato preparata con Sysprep con l'agente di Log Analytics per Windows installato.

#### <a name="resolution"></a>Risoluzione

Nel computer agente Log Analytics passare a **C:\Programmi\Microsoft Monitoring Agent\Agent\Tools** ed eseguire i comandi seguenti:

```cmd
net stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

Se è ancora necessaria assistenza, è possibile raccogliere informazioni di diagnostica e contattare il supporto tecnico.

> [!NOTE]
> Per impostazione predefinita, l'agente di Log Analytics Abilita la traccia degli errori. Per abilitare i messaggi di errore dettagliati come nell'esempio precedente, usare `VER` il parametro. Per informazioni sulle tracce, `INF` usare quando si `StartTracing.cmd`richiama.

##### <a name="log-analytics-agent-for-windows-not-running"></a>Agente di Log Analytics per Windows non in esecuzione

Verificare che l'agente di Log Analytics per Windows (**HealthService. exe**) sia in esecuzione nel computer.

##### <a name="communication-to-automation-account-blocked"></a>Comunicazione con l'account di automazione bloccata

Controllare Visualizzatore eventi nel computer e cercare gli eventi che contengono la parola `changetracking` .

Per informazioni sugli indirizzi e sulle porte che devono essere consentiti per il funzionamento di Rilevamento modifiche e inventario, vedere [automatizzare le risorse nel Data Center o nel cloud usando Hybrid Runbook Workers](../automation-hybrid-runbook-worker.md#network-planning).

##### <a name="management-packs-not-downloaded"></a>Management Pack non scaricati

Verificare che i Management Pack di Rilevamento modifiche e inventario seguenti siano installati localmente:

* `Microsoft.IntelligencePacks.ChangeTrackingDirectAgent.*`
* `Microsoft.IntelligencePacks.InventoryChangeTracking.*`
* `Microsoft.IntelligencePacks.SingletonInventoryCollection.*`

##### <a name="vm-from-cloned-machine-that-has-not-been-sysprepped"></a>VM da computer clonato che non è stato preparata con Sysprep

Se si usa un'immagine clonata, eseguire prima l'immagine Sysprep e quindi installare l'agente di Log Analytics per Windows.

## <a name="linux"></a>Linux

### <a name="scenario-no-change-tracking-and-inventory-results-on-linux-machines"></a>Scenario: nessun risultato di Rilevamento modifiche e inventario nei computer Linux

#### <a name="issue"></a>Problema

Non viene visualizzato alcun Rilevamento modifiche e i risultati dell'inventario per i computer Linux caricati per la soluzione. 

#### <a name="cause"></a>Causa
Di seguito sono riportate alcune possibili cause specifiche di questo problema:
* L'agente di Log Analytics per Linux non è in esecuzione.
* L'agente di Log Analytics per Linux non è configurato correttamente.
* Sono presenti conflitti di monitoraggio dell'integrità dei file (FIM).

#### <a name="resolution"></a>Risoluzione 

##### <a name="log-analytics-agent-for-linux-not-running"></a>Agente di Log Analytics per Linux non in esecuzione

Verificare che il daemon per l'agente di Log Analytics per Linux (**omsagent**) sia in esecuzione nel computer. Eseguire la query seguente nell'area di lavoro Log Analytics collegata all'account di automazione.

```loganalytics Copy
Heartbeat
| summarize by Computer, Solutions
```

Se il computer non viene visualizzato nei risultati della query, non è stato archiviato di recente. Probabilmente si è verificato un problema di configurazione locale ed è necessario reinstallare l'agente. Per informazioni sull'installazione e la configurazione, vedere [raccogliere dati di log con l'agente di log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent).

Se il computer viene visualizzato nei risultati della query, verificare la configurazione dell'ambito. Vedere [targeting Solutions Monitoring in monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/insights/solution-targeting).

Per ulteriori informazioni sulla risoluzione di questo problema, vedere [problema: non vengono visualizzati dati Linux](https://docs.microsoft.com/azure/azure-monitor/platform/agent-linux-troubleshoot#issue-you-are-not-seeing-any-linux-data).

##### <a name="log-analytics-agent-for-linux-not-configured-correctly"></a>Log Analytics agente per Linux non è configurato correttamente

L'agente di Log Analytics per Linux potrebbe non essere configurato correttamente per la raccolta di output della riga di comando e di log usando lo strumento di raccolta log di OMS. Vedere [tenere traccia delle modifiche apportate all'ambiente con la soluzione rilevamento modifiche e Inventory](../change-tracking.md).

##### <a name="fim-conflicts"></a>Conflitti FIM

La funzionalità FIM del Centro sicurezza di Azure può convalidare erroneamente l'integrità dei file Linux. Verificare che FIM sia operativo e configurato correttamente per il monitoraggio dei file di Linux. Vedere [tenere traccia delle modifiche apportate all'ambiente con la soluzione rilevamento modifiche e Inventory](../change-tracking.md).

## <a name="next-steps"></a>Passaggi successivi

Se il problema non viene visualizzato qui o non è possibile risolvere il problema, provare a usare uno dei canali seguenti per ottenere supporto aggiuntivo:

* Ottieni risposte dagli esperti di Azure tramite i [Forum di Azure](https://azure.microsoft.com/support/forums/).
* Connettersi con [@AzureSupport](https://twitter.com/azuresupport), l'account ufficiale Microsoft Azure per migliorare l'esperienza del cliente. Il supporto tecnico di Azure connette la community di Azure a risposte, supporto ed esperti.
* Archiviare un incidente del supporto tecnico di Azure. Accedere al [sito del supporto tecnico di Azure](https://azure.microsoft.com/support/options/)e selezionare **ottenere supporto**.
