---
title: Dati da raccogliere quando si apre un caso per Automazione di Microsoft Azure | Microsoft Docs
description: Questo articolo descrive le informazioni da raccogliere prima di aprire un caso per Automazione di Azure con il supporto di Microsoft Azure.
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
ms.openlocfilehash: d2f97d0b889186324aef9613847e3eddbfe1eb02
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "83684855"
---
# <a name="data-to-collect-when-opening-a-case-for-microsoft-azure-automation"></a>Dati da raccogliere quando si apre un caso per Automazione di Microsoft Azure

Questo articolo descrive alcune delle informazioni che è necessario raccogliere prima di aprire un caso per Automazione di Azure con il supporto di Microsoft Azure. Queste informazioni non sono necessarie per aprire il caso. Tuttavia, possono consentire a Microsoft di risolvere il problema più rapidamente. Inoltre, è possibile che tali dati vengano richiesti dal tecnico del supporto dopo l'apertura del caso.

## <a name="basic-data"></a>Dati di base

Raccogliere i dati di base descritti nell'articolo della Knowledge Base [4034605 - Come acquisire la diagnostica basata su script di Automazione di Azure](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics).

## <a name="data-for-update-management-issues-on-linux"></a>Dati per i problemi di Gestione aggiornamenti in Linux

1. Oltre agli elementi elencati nell'articolo della Knowledge Base [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics), eseguire lo strumento di raccolta dei log seguente:

   [Agente di raccolta log dell'agente di OMS per Linux](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md)
 
2. Comprimere il contenuto della cartella **/var/opt/microsoft/omsagent/run/automationworker/** , quindi inviare il file compresso al supporto di Azure.
 
3. Verificare che l'ID per l'area di lavoro a cui l'agente di Log Analytics per Linux fa riferimento corrisponda all'ID dell'area di lavoro monitorata per gli aggiornamenti.

## <a name="data-for-update-management-issues-on-windows"></a>Dati per i problemi di Gestione aggiornamenti in Windows

1. Raccogliere i dati per gli elementi elencati in [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics).

2. Esportare i registri eventi seguenti nel formato EVTX:

   * Sistema
   * Applicazione
   * Sicurezza
   * Operations Manager
   * Microsoft-SMA/Operational

3. Verificare che l'ID dell'area di lavoro a cui l'agente fa riferimento corrisponda all'ID dell'area di lavoro monitorata da Windows Updates.

## <a name="data-for-job-issues"></a>Dati per i problemi del processo

1. Raccogliere i dati per gli elementi elencati in [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics).

2. Raccogliere l'ID del processo in cui si è verificato un problema:

   1. Nel portale di Azure passare ad **Account di automazione**.
   2. Selezionare l'account di Automazione di cui si intende risolvere i problemi e annotare il nome.
   3. Selezionare **Processi**.
   4. Scegliere il processo di cui si intende risolvere i problemi.
   5. Nel riquadro Riepilogo processi cercare il valore GUID in **ID processo**.

   ![ID processo nel riquadro Riepilogo processi](media/collect-data-microsoft-azure-automation-case/job-summary-job-id.png)

3. Raccogliere un esempio dello script in esecuzione.

4. Raccogliere i file di log:

   1. Nel portale di Azure passare ad **Account di automazione**.
   2. Selezionare l'account di Automazione di cui si intende risolvere i problemi.
   3. Selezionare **Processi**.
   4. Scegliere il processo di cui si intende risolvere i problemi.
   5. Selezionare **Tutti i log**.
   6. Nel riquadro risultante raccogliere i dati.

   ![Dati elencati in Tutti i log](media/collect-data-microsoft-azure-automation-case/all-logs-data.png)

## <a name="data-for-module-issues"></a>Dati per i problemi relativi ai moduli

Oltre agli [elementi dei dati di base](#basic-data), raccogliere le informazioni seguenti:

* I passaggi seguiti, in modo che il problema possa essere riprodotto.
* Screenshot di eventuali messaggi di errore.
* Screenshot dei moduli correnti con i relativi numeri di versione.

## <a name="next-steps"></a>Passaggi successivi

Se è necessaria ulteriore assistenza:

* Ottenere risposte dagli esperti di Azure tramite i [forum di Azure](https://azure.microsoft.com/support/forums/).
* Collegarsi a [@AzureSupport](https://twitter.com/azuresupport), l'account Microsoft Azure ufficiale per migliorare l'esperienza dei clienti che mette in contatto la community di Azure con le risorse corrette: risposte, supporto ed esperti.
* Archiviare un incidente del supporto tecnico di Azure. Accedere al [sito del supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare **Ottenere supporto**.
