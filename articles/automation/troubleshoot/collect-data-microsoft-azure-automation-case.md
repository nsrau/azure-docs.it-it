---
title: Dati da raccogliere quando si apre un case per l'automazione Microsoft Azure | Microsoft Docs
description: Questo articolo descrive alcune delle informazioni che è necessario raccogliere prima di aprire un caso per automazione di Azure con supporto Microsoft Azure.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81679410"
---
# <a name="data-to-collect-when-you-open-a-case-for-microsoft-azure-automation"></a>Dati da raccogliere quando si apre un caso per Automazione di Microsoft Azure

Questo articolo descrive alcune delle informazioni che è necessario raccogliere prima di aprire un caso per automazione di Azure con supporto Microsoft Azure. Queste informazioni non sono necessarie per aprire il caso. Tuttavia, può consentire a Microsoft di risolvere il problema più rapidamente. Inoltre, è possibile che i dati vengano richiesti dal tecnico del supporto dopo l'apertura del caso.

## <a name="basic-data"></a>Dati di base

Raccogliere i dati di base descritti nell'articolo 4034605 della Knowledge base [-come acquisire la diagnostica basata su script di automazione di Azure](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics).

## <a name="data-for-update-management-issues-on-linux"></a>Dati per i problemi di Gestione aggiornamenti in Linux

1. Oltre agli elementi elencati in KB [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics), eseguire lo strumento di raccolta dei log seguente:

   [Agente di raccolta log agente Linux OMS](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md)
 
2. Comprimere il contenuto della cartella **/var/opt/Microsoft/omsagent/Run/automationworker/** , quindi inviare il file compresso al supporto tecnico di Azure.
 
3. Verificare che l'ID per l'area di lavoro a cui l'agente di Log Analytics per Linux riferisca corrisponda all'ID dell'area di lavoro monitorata per gli aggiornamenti.

## <a name="data-for-update-management-issues-on-windows"></a>Dati per i problemi di Gestione aggiornamenti in Windows

1. Raccogliere i dati per gli elementi elencati in [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics).

2. Esportare i registri eventi seguenti nel formato EVTX:

   * Sistema
   * Applicazione
   * Sicurezza
   * Operations Manager
   * Microsoft-SMA/operativo

3. Verificare che l'ID dell'area di lavoro a cui viene segnalato l'agente corrisponda all'ID dell'area di lavoro monitorata dagli aggiornamenti di Windows.

## <a name="data-for-job-issues"></a>Dati per i problemi di processo

1. Raccogliere i dati per gli elementi elencati in [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics).

2. Raccogliere l'ID processo per il processo in cui si è verificato un problema:

   1. Nella portale di Azure passare a account di **automazione**.
   2. Selezionare l'account di automazione per la risoluzione dei problemi e annotare il nome.
   3. Selezionare **processi**.
   4. Scegliere il processo di risoluzione dei problemi.
   5. Nel riquadro Riepilogo processo cercare il valore GUID in **ID processo**.

   ![ID processo nel riquadro Riepilogo processo](media/collect-data-microsoft-azure-automation-case/job-summary-job-id.png)

3. Raccogliere un esempio dello script in esecuzione.

4. Raccogliere i file di log:

   1. Nella portale di Azure passare a account di **automazione**.
   2. Selezionare l'account di automazione per la risoluzione dei problemi.
   3. Selezionare **processi**.
   4. Scegliere il processo di risoluzione dei problemi.
   5. Selezionare **tutti i log**.
   6. Nel riquadro risultante raccogliere i dati.

   ![Dati elencati in tutti i log](media/collect-data-microsoft-azure-automation-case/all-logs-data.png)

## <a name="data-for-module-issues"></a>Dati per i problemi relativi ai moduli

Oltre agli elementi di [dati di base](#basic-data), raccogliere le informazioni seguenti:

* I passaggi seguiti, in modo che il problema possa essere riprodotto.
* Screenshot di eventuali messaggi di errore.
* Schermate dei moduli correnti con i relativi numeri di versione.

## <a name="next-steps"></a>Passaggi successivi

Se è necessaria ulteriore assistenza:

* Ottieni risposte dagli esperti di Azure tramite i [Forum di Azure](https://azure.microsoft.com/support/forums/).
* Connettersi a [@AzureSupport](https://twitter.com/azuresupport), l'account ufficiale Microsoft Azure per migliorare l'esperienza del cliente connettendo la community di Azure alle risorse appropriate: risposte, supporto ed esperti.
* Archiviare un incidente del supporto tecnico di Azure. Accedere al [sito del supporto tecnico di Azure](https://azure.microsoft.com/support/options/) e selezionare **Ottieni supporto**.
