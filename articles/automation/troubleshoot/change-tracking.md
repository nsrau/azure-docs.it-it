---
title: Risoluzione dei problemi relativi al Rilevamento modifiche di Azure
description: Informazioni su come risolvere i problemi relativi alla Rilevamento modifiche di automazione di Azure e alla funzionalità di inventario.
services: automation
ms.service: automation
ms.subservice: change-inventory-management
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 6cadaea1a20743071acbe8860df02ca7bbdde954
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198531"
---
# <a name="troubleshoot-change-tracking-and-inventory"></a>Risolvere i problemi di Rilevamento modifiche e Inventario

## <a name="windows"></a>Windows

### <a name="records-not-showing-windows"></a>Scenario: i record di Rilevamento modifiche non vengono visualizzati per i computer Windows

#### <a name="issue"></a>Problema

Non vengono visualizzati i risultati di Rilevamento modifiche o inventario per i computer Windows caricati per Rilevamento modifiche.

#### <a name="cause"></a>Causa

Questo errore può avere le seguenti cause:

* Il Microsoft Monitoring Agent non è in esecuzione.
* Viene bloccata la comunicazione con l'account di automazione.
* I Management Pack per Rilevamento modifiche non vengono scaricati.
* La VM da caricare potrebbe provenire da un computer clonato che non è stato preparata con Sysprep con il Microsoft Monitoring Agent installato.

#### <a name="resolution"></a>Risoluzione

Le soluzioni descritte di seguito possono aiutare a risolvere il problema. Se è ancora necessaria assistenza, è possibile raccogliere informazioni di diagnostica e contattare il supporto tecnico. Nel computer agente passare a C:\Programmi\Microsoft Monitoring Agent\Agent\Tools ed eseguire i comandi seguenti:

```cmd
net stop healthservice
StopTracing.cmd
StartTracing.cmd VER
net start healthservice
```

> [!NOTE]
> Per impostazione predefinita, la traccia degli errori è abilitata. Per abilitare i messaggi di errore dettagliati come nell'esempio precedente, usare il parametro *ver* . Per informazioni sulle tracce, usare *inf* quando si richiama **StartTracing. cmd**.

##### <a name="microsoft-monitoring-agent-not-running"></a>Microsoft Monitoring Agent non in esecuzione

Verificare che il Microsoft Monitoring Agent (HealthService. exe) sia in esecuzione nel computer.

##### <a name="communication-to-automation-account-blocked"></a>Comunicazione con l'account di automazione bloccata

Controllare Visualizzatore eventi nel computer e cercare gli eventi che contengono la parola "rilevamento modifiche".

Vedere [automatizzare le risorse nel Data Center o nel cloud usando il ruolo di lavoro ibrido per Runbook](../automation-hybrid-runbook-worker.md#network-planning) per informazioni sugli indirizzi e le porte che devono essere consentiti per il funzionamento rilevamento modifiche.

##### <a name="management-packs-not-downloaded"></a>Management Pack non scaricati

Verificare che i Management Pack di Rilevamento modifiche e inventario seguenti siano installati localmente:

* Microsoft.IntelligencePacks.ChangeTrackingDirectAgent.*
* Microsoft.IntelligencePacks.InventoryChangeTracking.*
* Microsoft.IntelligencePacks.SingletonInventoryCollection.*

##### <a name="vm-from-cloned-machine-that-has-not-been-sysprepped"></a>VM da computer clonato che non è stato preparata con Sysprep

Se si usa un'immagine clonata, eseguire prima Sysprep l'immagine e quindi installare il Microsoft Monitoring Agent.

## <a name="linux"></a>Linux

### <a name="scenario-no-change-tracking-or-inventory-results-on-linux-machines"></a>Scenario: nessun risultato di Rilevamento modifiche o inventario nei computer Linux

#### <a name="issue"></a>Problema

Non vengono visualizzati i risultati di inventario o di Rilevamento modifiche per i computer Linux caricati per Rilevamento modifiche. 

#### <a name="cause"></a>Causa
Di seguito sono riportate alcune possibili cause specifiche di questo problema:
* L'agente di Log Analytics per Linux non è in esecuzione.
* L'agente di Log Analytics per Linux non è configurato correttamente.
* Sono presenti conflitti di monitoraggio dell'integrità dei file (FIM).

#### <a name="resolution"></a>Risoluzione 

##### <a name="log-analytics-agent-for-linux-not-running"></a>Agente di Log Analytics per Linux non in esecuzione

Verificare che il daemon per l'agente di Log Analytics per Linux (omsagent) sia in esecuzione nel computer. Eseguire la query seguente nell'area di lavoro Log Analytics collegata all'account di automazione.

```loganalytics Copy
Heartbeat
| summarize by Computer, Solutions
```

Se il computer non viene visualizzato nei risultati della query, non è stato archiviato di recente. Probabilmente si è verificato un problema di configurazione locale ed è necessario reinstallare l'agente. Per informazioni sull'installazione e la configurazione, vedere [raccogliere dati di log con l'agente di log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent). 

Se il computer viene visualizzato nei risultati della query, verificare la configurazione dell'ambito. Vedere [targeting Solutions Monitoring in monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/insights/solution-targeting).

Per ulteriori informazioni sulla risoluzione di questo problema, vedere [problema: non vengono visualizzati dati Linux](https://docs.microsoft.com/azure/azure-monitor/platform/agent-linux-troubleshoot#issue-you-are-not-seeing-any-linux-data).

##### <a name="log-analytics-agent-for-linux-not-configured-correctly"></a>Log Analytics agente per Linux non è configurato correttamente

L'agente di Log Analytics per Linux potrebbe non essere configurato correttamente per la raccolta di output della riga di comando e di log usando lo strumento di raccolta log di OMS. Vedere [tenere traccia delle modifiche apportate all'ambiente con la soluzione rilevamento modifiche](../change-tracking.md).

##### <a name="fim-conflicts"></a>Conflitti FIM

La funzionalità FIM del Centro sicurezza di Azure può convalidare erroneamente l'integrità dei file Linux. Verificare che FIM sia operativo e configurato correttamente per il monitoraggio dei file di Linux. Vedere [tenere traccia delle modifiche apportate all'ambiente con la soluzione rilevamento modifiche](../change-tracking.md).

## <a name="next-steps"></a>Passaggi successivi

Se il problema persiste o non è possibile risolvere il problema, usare uno dei canali seguenti per ottenere supporto.

* Ottieni risposte dagli esperti di Azure tramite i [Forum di Azure](https://azure.microsoft.com/support/forums/).
* Collegarsi a [@AzureSupport](https://twitter.com/azuresupport), l'account Microsoft Azure ufficiale per il miglioramento dell'esperienza dei clienti che mette in contatto la community di Azure con le risorse corrette: risposte, supporto ed esperti.
* Se è necessaria un'assistenza maggiore, è possibile inviare una richiesta al supporto tecnico di Azure. Accedere al [sito del supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare **Ottenere supporto**.
