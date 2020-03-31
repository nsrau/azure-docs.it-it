---
title: Risoluzione dei problemi relativi al Rilevamento modifiche di Azure
description: Informazioni su come risolvere i problemi relativi al rilevamento delle modifiche e all'inventario di Automazione di Azure.Learn how to troubleshoot and resolve issues with the Azure Automation Change Tracking and Inventory feature.
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 6cadaea1a20743071acbe8860df02ca7bbdde954
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77198531"
---
# <a name="troubleshoot-change-tracking-and-inventory"></a>Risolvere i problemi di Rilevamento modifiche e Inventario

## <a name="windows"></a>WINDOWS

### <a name="scenario-change-tracking-records-arent-showing-for-windows-machines"></a><a name="records-not-showing-windows"></a>Scenario: i record di rilevamento delle modifiche non vengono visualizzati per i computer WindowsScenario: Change Tracking records aren't showing for Windows machines

#### <a name="issue"></a>Problema

Non viene visualizzato alcun risultato di rilevamento delle modifiche o di inventario per i computer Windows che sono onboarded per il rilevamento delle modifiche.

#### <a name="cause"></a>Causa

Questo errore può avere le seguenti cause:

* Microsoft Monitoring Agent non è in esecuzione.
* È in corso il blocco della comunicazione con l'account di automazione.
* I Management Pack per il rilevamento delle modifiche non vengono scaricati.
* La macchina virtuale in servizio potrebbe provenire da un computer clonato che non è stato syspreed con Microsoft Monitoring Agent installato.

#### <a name="resolution"></a>Risoluzione

Le soluzioni descritte di seguito potrebbero aiutare a risolvere il problema. Se hai ancora bisogno di aiuto, puoi raccogliere informazioni di diagnostica e contattare il supporto. Nel computer dell'agente, passare a C:.

```cmd
net stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

> [!NOTE]
> Per impostazione predefinita, la traccia degli errori è abilitata. Per abilitare i messaggi di errore dettagliati come nell'esempio precedente, utilizzare il parametro *VER.* Per le tracce di informazioni, utilizzare *INF* quando si richiama **StartTracing.cmd**.

##### <a name="microsoft-monitoring-agent-not-running"></a>Microsoft Monitoring Agent non in esecuzione

Verificare che Microsoft Monitoring Agent (HealthService.exe) sia in esecuzione nel computer.

##### <a name="communication-to-automation-account-blocked"></a>Comunicazione con l'account Di automazione bloccata

Controllare il Visualizzatore eventi sul computer e cercare gli eventi che conto della parola "changetracking".

Per informazioni sugli indirizzi e sulle porte che devono essere consentiti per il funzionamento del rilevamento delle modifiche, [vedere Automatizzare le risorse nel data center o nel cloud usando Hybrid Runbook Worker.See Automate resources in your datacenter](../automation-hybrid-runbook-worker.md#network-planning) or cloud by using Hybrid Runbook Worker to learn about addresses and ports that must be allowed for Change Tracking to work.

##### <a name="management-packs-not-downloaded"></a>Management Pack non scaricati

Verificare che i seguenti Service Pack di rilevamento delle modifiche e di inventario siano installati localmente:

* Microsoft.IntelligencePacks.ChangeTrackingDirectAgent.*
* Microsoft.IntelligencePacks.InventoryChangeTracking.*
* Microsoft.IntelligencePacks.SingletonInventoryCollection.*

##### <a name="vm-from-cloned-machine-that-has-not-been-sysprepped"></a>VM da un computer clonato che non è stato sysprepreppedVM from cloned machine that has not been sysprep

Se si utilizza un'immagine clonata, sysprep l'immagine prima e quindi installare Microsoft Monitoring Agent.

## <a name="linux"></a>Linux

### <a name="scenario-no-change-tracking-or-inventory-results-on-linux-machines"></a>Scenario: nessun rilevamento delle modifiche o risultati dell'inventario nei computer LinuxScenario: No Change Tracking or Inventory results on Linux machines

#### <a name="issue"></a>Problema

Non viene visualizzato alcun risultato di Inventario o Rilevamento modifiche per i computer Linux che sono onboarded per il rilevamento delle modifiche. 

#### <a name="cause"></a>Causa
Di seguito sono riportate le possibili cause specifiche di questo problema:
* L'agente di Log Analytics per Linux non è in esecuzione.
* L'agente di Log Analytics per Linux non è configurato correttamente.
* Sono presenti conflitti di monitoraggio dell'integrità dei file (FIM).

#### <a name="resolution"></a>Risoluzione 

##### <a name="log-analytics-agent-for-linux-not-running"></a>Agente di Log Analytics per Linux non in esecuzione

Verificare che il daemon per l'agente log Analytics per Linux (omsagent) sia in esecuzione nel computer. Eseguire la query seguente nell'area di lavoro di Log Analytics collegata all'account di automazione.

```loganalytics Copy
Heartbeat
| summarize by Computer, Solutions
```

Se il computer non viene visualizzato nei risultati delle query, l'utente non è stato archiviato di recente. Probabilmente c'è un problema di configurazione locale ed è necessario reinstallare l'agente. Per informazioni sull'installazione e la configurazione, vedere [Raccogliere i dati del log con l'agente di Log Analytics.](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent) 

Se il computer viene visualizzato nei risultati della query, verificare la configurazione dell'ambito. Vedere [Targeting monitoring solutions in Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/solution-targeting).

Per ulteriori informazioni sulla risoluzione di questo problema, fare riferimento a [Problema: non vengono dati Linux](https://docs.microsoft.com/azure/azure-monitor/platform/agent-linux-troubleshoot#issue-you-are-not-seeing-any-linux-data).

##### <a name="log-analytics-agent-for-linux-not-configured-correctly"></a>Agente di Log Analytics per Linux non configurato correttamenteLog Analytics agent for Linux not configured correctly

L'agente di Log Analytics per Linux potrebbe non essere configurato correttamente per la raccolta di output della riga di log e della riga di comando utilizzando lo strumento OMS Log Collector. Consultate [Tenere traccia delle modifiche nell'ambiente con la soluzione Rilevamento modifiche](../change-tracking.md).

##### <a name="fim-conflicts"></a>Conflitti FIM

La funzionalità FIM del Centro sicurezza di Azure potrebbe convalidare in modo errato l'integrità dei file Linux.Azure Security Center's FIM feature might be uncorrectly validating the integrity of your Linux files. Verificare che FIM sia operativo e configurato correttamente per il monitoraggio dei file Linux. Consultate [Tenere traccia delle modifiche nell'ambiente con la soluzione Rilevamento modifiche](../change-tracking.md).

## <a name="next-steps"></a>Passaggi successivi

Se non vedi il problema o non riesci a risolvere il problema, utilizza uno dei seguenti canali per ulteriore supporto.

* Ottieni risposte dagli esperti di Azure tramite i forum di [Azure.](https://azure.microsoft.com/support/forums/)
* Connettiti [@AzureSupport](https://twitter.com/azuresupport) con l'account ufficiale di Microsoft Azure per migliorare l'esperienza dei clienti connettendo la community di Azure alle risorse giuste: risposte, supporto ed esperti.
* Se è necessaria un'assistenza maggiore, è possibile inviare una richiesta al supporto tecnico di Azure. Passare al [sito del supporto](https://azure.microsoft.com/support/options/) di Azure e selezionare Ottieni **supporto**.
