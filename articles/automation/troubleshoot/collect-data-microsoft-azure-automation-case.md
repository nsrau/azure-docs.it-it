---
title: Dati da raccogliere quando si apre un caso per Microsoft Azure Automation Documenti Microsoft
description: Questo articolo descrive alcune delle informazioni di base che è necessario raccogliere prima di aprire un caso per l'automazione di Azure con il supporto di Microsoft Azure.This article describes some of the basic information that you should gather before you open a case for Azure Automation with Microsoft Azure Support.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: top-support-issue, azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/23/2019
ms.author: v-miegge
ms.openlocfilehash: 4839ce7a0188c782656fd3a4c42cbdd116b165e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74849378"
---
# <a name="data-to-collect-when-you-open-a-case-for-microsoft-azure-automation"></a>Dati da raccogliere quando si apre un caso per Automazione di Microsoft Azure

Questo articolo descrive alcune delle informazioni di base che è necessario raccogliere prima di aprire un caso per l'automazione di Azure con il supporto di Microsoft Azure.This article describes some of the basic information that you should gather before you open a case for Azure Automation with Microsoft Azure Support. Queste informazioni non sono necessarie per aprire il caso. Tuttavia, può aiutare Microsoft a risolvere il problema più rapidamente. Inoltre, è possibile che vengano richiesti questi dati dal tecnico del supporto dopo aver aperto il caso.

## <a name="collect-more-information"></a>Raccogliere ulteriori informazioni

Prima di aprire un caso per il supporto di automazione di Microsoft Azure, è consigliabile raccogliere le informazioni seguenti.

### <a name="basic-data-collection"></a>Raccolta dati di base

Raccogliere i dati descritti nel seguente articolo della Knowledge Base:

* [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics) come acquisire la diagnostica con script di automazione di Azure

## <a name="collect-data-depending-on-issue"></a>Raccogliere dati a seconda del problema
 
### <a name="for-issues-that-affect-update-management-on-linux"></a>Per problemi che influiscono sulla gestione degli aggiornamenti su Linux

1. Oltre agli elementi elencati in [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)KB, eseguire il seguente strumento di raccolta dei registri:

   [Agente operativo OMS Raccoglitore log](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md)
 
2. Comprimere il contenuto della cartella seguente, quindi inviare il file compresso al supporto di Azure:

   ``/var/opt/microsoft/omsagent/run/automationworker/``
 
3. Verificare che l'ID dell'area di lavoro a cui l'agente Linux o ms scappa, sia lo stesso dell'area di lavoro monitorata per gli aggiornamenti.

### <a name="for-issues-that-affect-update-management-on-windows"></a>Per problemi che influiscono sulla gestione degli aggiornamenti in Windows

Oltre agli elementi elencati in [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics), eseguire le operazioni seguenti:

1. Esportare i seguenti registri eventi nel formato EVTX:

   * System
   * Applicazione
   * Security
   * Operations Manager
   * Microsoft-SMA/Operativo

2. Verificare che l'ID dell'area di lavoro a cui l'agente sta segnalando sia lo stesso dell'area di lavoro monitorata dagli aggiornamenti di Windows.

### <a name="for-issues-that-affect-a-job"></a>Per problemi che influiscono su un lavoro

Oltre agli elementi elencati in [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics), raccogliere le seguenti informazioni:

1. Raccogliere il numero **JobID** (per il processo che si è verificato un problema):

   1. Nel portale di Azure passare al pannello **Account di automazione.**
   2. Selezionare **l'account di automazione** che si sta risolvendo.
   3. Selezionare **Processi**.
   4. Selezionare il processo che si sta risolvendo.
   5. In **Riepilogo processo**cercare un ID **processo** (GUID).

   ![ID processo nel riquadro Riepilogo processo](media/collect-data-microsoft-azure-automation-case/job-summary-job-id.png)

2. Raccogliere il nome dell'account:

   1. Nel portale di Azure passare al pannello **Account di automazione.**
   2. Ottenere il nome dell'account di **automazione** che si sta risolvendo.

3. Raccogliere l'esempio dello script in esecuzione.

4. Raccogliere i file di registro:

   1. Nel portale di Azure passare al pannello **Account di automazione.**
   2. Selezionare **l'account di automazione** che si sta risolvendo.
   3. Selezionare **Processi**.
   4. Selezionare il processo che si sta risolvendo.
   5. Selezionare **Tutti i registri**.
   6. Nel pannello risultante, raccogliere i dati.

   ![Dati elencati in Tutti i registri](media/collect-data-microsoft-azure-automation-case/all-logs-data.png)

### <a name="for-issues-that-affect-modules"></a>Per i problemi che influiscono sui moduli

Oltre agli elementi in "Raccolta dati di base", raccogliere le seguenti informazioni:

* I passaggi seguiti in modo che il problema possa essere riprodotto.
* Cattura di schermata di eventuali messaggi di errore.
* Uno screenshot dei moduli correnti e dei relativi numeri di versione.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni in qualsiasi momento di questo articolo, contattare gli esperti di Azure nei [forum MSDN Azure e Stack Overflow](https://azure.microsoft.com/support/forums/).

In alternativa, archiviare un incidente di supporto di Azure.Alternatively, file an Azure support incident. Passare al [sito del supporto](https://azure.microsoft.com/support/options/) di Azure e selezionare Ottieni **supporto**.
