---
title: Dati da raccogliere quando si apre un caso per Microsoft Azure Automation Documenti Microsoft
description: Questo articolo descrive alcune delle informazioni che è necessario raccogliere prima di aprire un caso per automazione di Azure con il supporto di Microsoft Azure.This article describes some of the information that you should gather before you open a case for Azure Automation with Microsoft Azure Support.
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
ms.openlocfilehash: e1d5d791a58f301991819b41757b9021f6e30fc0
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679410"
---
# <a name="data-to-collect-when-you-open-a-case-for-microsoft-azure-automation"></a>Dati da raccogliere quando si apre un caso per Automazione di Microsoft Azure

Questo articolo descrive alcune delle informazioni che è necessario raccogliere prima di aprire un caso per automazione di Azure con il supporto di Microsoft Azure.This article describes some of the information that you should gather before you open a case for Azure Automation with Microsoft Azure Support. Queste informazioni non sono necessarie per aprire il caso. Tuttavia, può aiutare Microsoft a risolvere il problema più rapidamente. Inoltre, è possibile che vengano richiesti questi dati dal tecnico del supporto dopo aver aperto il caso.

## <a name="basic-data"></a>Dati di base

Raccogliere i dati di base descritti nell'articolo [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)della Knowledge Base - Come acquisire la diagnostica tramite script di Automazione di Azure.

## <a name="data-for-update-management-issues-on-linux"></a>Problemi relativi ai dati per la gestione degli aggiornamenti su LinuxData for Update Management issues on Linux

1. Oltre agli elementi elencati in [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics)KB, eseguire il seguente strumento di raccolta dei registri:

   [Agente operativo OMS Raccoglitore log](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md)
 
2. Comprimere il contenuto della cartella **/var/opt/microsoft/omsagent/run/automationworker/,** quindi inviare il file compresso al supporto di Azure.Comcompress the contents of the /var/opt/microsoft/omsagent/run/automationworker/ folder, then send the compressed file to Azure Support.
 
3. Verificare che l'ID per l'area di lavoro in cui viene pubblicato l'agente log Analytics per Linux report sia lo stesso dell'ID per l'area di lavoro monitorata per gli aggiornamenti.

## <a name="data-for-update-management-issues-on-windows"></a>Problemi relativi ai dati per la gestione degli aggiornamenti in WindowsData for Update Management issues on Windows

1. Raccogliere i dati per gli elementi elencati in [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics).

2. Esportare i seguenti registri eventi nel formato EVTX:

   * Sistema
   * Applicazione
   * Sicurezza
   * Operations Manager
   * Microsoft-SMA/Operativo

3. Verificare che l'ID dell'area di lavoro a cui viene segnalato l'agente sia lo stesso dell'ID dell'area di lavoro monitorata dagli aggiornamenti di Windows.

## <a name="data-for-job-issues"></a>Dati per problemi di lavoro

1. Raccogliere i dati per gli elementi elencati in [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics).

2. Raccogliere l'ID processo per il processo che presenta un problema:Collect the job ID for the job that has an issue:

   1. Nel portale di Azure passare a **Account di automazione.**
   2. Selezionare l'account di automazione che si sta risolvendo e prendere nota del nome.
   3. Selezionare **Processi**.
   4. Scegliere il processo che si sta risolvendo.
   5. Nel riquadro Riepilogo processo cercare il valore GUID in **ID processo.**

   ![ID processo nel riquadro Riepilogo processo](media/collect-data-microsoft-azure-automation-case/job-summary-job-id.png)

3. Raccogliere un esempio dello script in esecuzione.

4. Raccogliere i file di registro:

   1. Nel portale di Azure passare a **Account di automazione.**
   2. Selezionare l'account di automazione che si sta risolvendo.
   3. Selezionare **Processi**.
   4. Scegliere il processo che si sta risolvendo.
   5. Selezionare **Tutti i registri**.
   6. Nel riquadro risultante, raccogliere i dati.

   ![Dati elencati in Tutti i registri](media/collect-data-microsoft-azure-automation-case/all-logs-data.png)

## <a name="data-for-module-issues"></a>Dati per i problemi del modulo

Oltre agli [elementi di dati di base,](#basic-data)raccogliere le seguenti informazioni:

* I passaggi seguiti, in modo che il problema possa essere riprodotto.
* Screenshot di eventuali messaggi di errore.
* Screenshot dei moduli correnti e dei relativi numeri di versione.

## <a name="next-steps"></a>Passaggi successivi

Se hai bisogno di ulteriore aiuto:

* Ottieni risposte dagli esperti di Azure tramite i forum di [Azure.](https://azure.microsoft.com/support/forums/)
* Connettiti [@AzureSupport](https://twitter.com/azuresupport)con , l'account ufficiale di Microsoft Azure per migliorare l'esperienza dei clienti connettendo la community di Azure alle risorse giuste: risposte, supporto ed esperti.
* Archiviare un incidente del supporto tecnico di Azure. Passare al [sito del supporto](https://azure.microsoft.com/support/options/) di Azure e selezionare Ottieni **supporto**.
