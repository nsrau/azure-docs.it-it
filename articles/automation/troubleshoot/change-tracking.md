---
title: Risoluzione dei problemi relativi al rilevamento delle modifiche e all'inventario
description: Informazioni su come risolvere i problemi con la soluzione Monitoraggio modifiche e inventario di Automazione di Azure.Learn how to troubleshoot and resolve issues with the Azure Automation Change Tracking and Inventory solution.
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 11c1fd05055922b07801c20d525d852d5360b069
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679344"
---
# <a name="troubleshoot-change-tracking-and-inventory-issues"></a>Risolvere i problemi relativi al rilevamento delle modifiche e all'inventario

In questo articolo viene descritto come risolvere i problemi di rilevamento delle modifiche e di inventario.

>[!NOTE]
>Questo articolo è stato aggiornato per usare il nuovo modulo Az di Azure PowerShell. È comunque possibile usare il modulo AzureRM, che continuerà a ricevere correzioni di bug almeno fino a dicembre 2020. Per altre informazioni sul nuovo modulo Az e sulla compatibilità di AzureRM, vedere [Introduzione del nuovo modulo Az di Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Per istruzioni sull'installazione del modulo Az nel ruolo di lavoro ibrido per runbook, vedere [Installare il modulo di Azure PowerShell.For](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)Az module installation instructions on your Hybrid Runbook Worker, see Install the Azure PowerShell Module . Per l'account di automazione, è possibile aggiornare i moduli alla versione più recente usando Come aggiornare i moduli di [Azure PowerShell in Automazione di Azure.](../automation-update-azure-modules.md)

## <a name="windows"></a>WINDOWS

### <a name="scenario-change-tracking-and-inventory-records-arent-showing-for-windows-machines"></a><a name="records-not-showing-windows"></a>Scenario: i record di rilevamento delle modifiche e inventario non vengono visualizzati per i computer Windows

#### <a name="issue"></a>Problema

Non viene visualizzato alcun risultato di rilevamento delle modifiche e dell'inventario per i computer Windows che sono onboardati.

#### <a name="cause"></a>Causa

Questo errore può avere le seguenti cause:

* L'agente di Log Analytics per Windows non è in esecuzione.
* È in corso il blocco della comunicazione con l'account di automazione.
* I Management Pack Rilevamento modifiche e Inventario non vengono scaricati.
* La macchina virtuale in servizio potrebbe provenire da un computer clonato che non è stato syspreed con l'agente di Log Analytics per Windows installato.

#### <a name="resolution"></a>Risoluzione

Nel computer dell'agente di Log Analytics, passare a **C:** , Programmi , Microsoft Monitoring Agent , Agent , Strumenti ed eseguire i comandi seguenti:

```cmd
net stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

Se hai ancora bisogno di aiuto, puoi raccogliere informazioni di diagnostica e contattare il supporto. 

> [!NOTE]
> L'agente Log Analyticss abilita la traccia degli errori per impostazione predefinita. Per abilitare i messaggi di errore dettagliati `VER` come nell'esempio precedente, utilizzare il parametro . Per la traccia delle informazioni, usare `INF` quando si richiama `StartTracing.cmd`.

##### <a name="log-analytics-agent-for-windows-not-running"></a>Agente di Log Analytics per Windows non in esecuzione

Verificare che l'agente di Log Analytics per Windows (**HealthService.exe**) sia in esecuzione nel computer.

##### <a name="communication-to-automation-account-blocked"></a>Comunicazione con l'account Di automazione bloccata

Controllare Visualizzatore eventi nel computer e cercare gli eventi che contengono la parola `changetracking`.

Per informazioni sugli indirizzi e sulle porte che devono essere consentiti per il rilevamento delle modifiche e l'inventario, vedere Automatizzare le risorse nel data center o nel [cloud usando Hybrid Runbook Worker.](../automation-hybrid-runbook-worker.md#network-planning)

##### <a name="management-packs-not-downloaded"></a>Management Pack non scaricati

Verificare che i seguenti Service Pack di rilevamento delle modifiche e di inventario siano installati localmente:

* `Microsoft.IntelligencePacks.ChangeTrackingDirectAgent.*`
* `Microsoft.IntelligencePacks.InventoryChangeTracking.*`
* `Microsoft.IntelligencePacks.SingletonInventoryCollection.*`

##### <a name="vm-from-cloned-machine-that-has-not-been-sysprepped"></a>VM da un computer clonato che non è stato sysprepreppedVM from cloned machine that has not been sysprep

Se si usa un'immagine clonata, sysprep l'immagine prima e quindi installare l'agente di Log Analytics per Windows.

## <a name="linux"></a>Linux

### <a name="scenario-no-change-tracking-and-inventory-results-on-linux-machines"></a>Scenario: nessun rilevamento delle modifiche e risultati dell'inventario nei computer LinuxScenario: No Change Tracking and Inventory results on Linux machines

#### <a name="issue"></a>Problema

Non vengono visualizzati risultati di inventario e rilevamento delle modifiche per le macchine Linux che sono in servizio per la soluzione. 

#### <a name="cause"></a>Causa
Di seguito sono riportate le possibili cause specifiche di questo problema:
* L'agente di Log Analytics per Linux non è in esecuzione.
* L'agente di Log Analytics per Linux non è configurato correttamente.
* Sono presenti conflitti di monitoraggio dell'integrità dei file (FIM).

#### <a name="resolution"></a>Risoluzione 

##### <a name="log-analytics-agent-for-linux-not-running"></a>Agente di Log Analytics per Linux non in esecuzione

Verificare che il daemon per l'agente Log Analytics per Linux (**omsagent**) sia in esecuzione nel computer. Eseguire la query seguente nell'area di lavoro di Log Analytics collegata all'account di automazione.

```loganalytics Copy
Heartbeat
| summarize by Computer, Solutions
```

Se il computer non viene visualizzato nei risultati delle query, l'utente non è stato archiviato di recente. Probabilmente c'è un problema di configurazione locale ed è necessario reinstallare l'agente. Per informazioni sull'installazione e la configurazione, vedere [Raccogliere i dati del log con l'agente di Log Analytics.](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent) 

Se il computer viene visualizzato nei risultati della query, verificare la configurazione dell'ambito. Vedere [Targeting monitoring solutions in Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/solution-targeting).

Per ulteriori informazioni sulla risoluzione di questo problema, vedere [Problema: non vengono visualizzati dati Linux](https://docs.microsoft.com/azure/azure-monitor/platform/agent-linux-troubleshoot#issue-you-are-not-seeing-any-linux-data).

##### <a name="log-analytics-agent-for-linux-not-configured-correctly"></a>Agente di Log Analytics per Linux non configurato correttamenteLog Analytics agent for Linux not configured correctly

L'agente di Log Analytics per Linux potrebbe non essere configurato correttamente per la raccolta di output della riga di log e della riga di comando utilizzando lo strumento OMS Log Collector. Vedere [Tenere traccia delle modifiche nell'ambiente con la soluzione Rilevamento modifiche e inventario](../change-tracking.md).

##### <a name="fim-conflicts"></a>Conflitti FIM

La funzionalità FIM del Centro sicurezza di Azure potrebbe convalidare in modo errato l'integrità dei file Linux.Azure Security Center's FIM feature might be uncorrectly validating the integrity of your Linux files. Verificare che FIM sia operativo e configurato correttamente per il monitoraggio dei file Linux. Vedere [Tenere traccia delle modifiche nell'ambiente con la soluzione Rilevamento modifiche e inventario](../change-tracking.md).

## <a name="next-steps"></a>Passaggi successivi

Se non vedi il problema sopra o non riesci a risolvere il problema, prova uno dei seguenti canali per ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite i forum di [Azure.](https://azure.microsoft.com/support/forums/)
* Connettiti [@AzureSupport](https://twitter.com/azuresupport)con , l'account ufficiale di Microsoft Azure per migliorare l'esperienza dei clienti connettendo la community di Azure alle risorse giuste: risposte, supporto ed esperti.
* Archiviare un incidente del supporto tecnico di Azure. Passare al [sito del supporto](https://azure.microsoft.com/support/options/) di Azure e selezionare Ottieni **supporto**.
