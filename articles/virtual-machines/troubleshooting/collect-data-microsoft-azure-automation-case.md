---
title: Dati da raccogliere quando si apre un case per l'automazione Microsoft Azure | Microsoft Docs
description: Questo articolo descrive alcune delle informazioni di base che è necessario raccogliere prima di aprire un caso per automazione di Azure con supporto Microsoft Azure.
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
ms.openlocfilehash: 83ee78d369af7fe99de8e7236fe1eb0bc63a942f
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/03/2019
ms.locfileid: "71846680"
---
# <a name="data-to-collect-when-you-open-a-case-for-microsoft-azure-automation"></a>Dati da raccogliere quando si apre un case per l'automazione Microsoft Azure

Questo articolo descrive alcune delle informazioni di base che è necessario raccogliere prima di aprire un caso per automazione di Azure con supporto Microsoft Azure. Queste informazioni non sono necessarie per aprire il caso. Tuttavia, può consentire a Microsoft di risolvere il problema più rapidamente. Inoltre, è possibile che i dati vengano richiesti dal tecnico del supporto dopo l'apertura del caso.

## <a name="collect-more-information"></a>Raccogli ulteriori informazioni

Prima di aprire un caso per il supporto di Microsoft Azure Automation, si consiglia di raccogliere le informazioni seguenti.

### <a name="basic-data-collection"></a>Raccolta dati di base

Raccogliere i dati descritti nell'articolo della Knowledge Base seguente:

* [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics) come acquisire la diagnostica basata su script di automazione di Azure

## <a name="collect-data-depending-on-issue"></a>Raccogliere i dati in base al problema
 
### <a name="for-issues-that-affect-update-management-on-linux"></a>Per i problemi che interessano Gestione aggiornamenti in Linux

1. Oltre agli elementi elencati in KB [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics), eseguire lo strumento di raccolta dei log seguente:

   [Agente di raccolta log agente Linux OMS](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/tools/LogCollector/OMS_Linux_Agent_Log_Collector.md)
 
2. Comprimere il contenuto della cartella seguente, quindi inviare il file compresso al supporto tecnico di Azure:

   ``/var/opt/microsoft/omsagent/run/automationworker/``
 
3. Verificare che l'ID dell'area di lavoro a cui l'agente Linux OMS sta segnalando sia uguale all'area di lavoro monitorata per gli aggiornamenti.

### <a name="for-issues-that-affect-update-management-on-windows"></a>Per i problemi che interessano Gestione aggiornamenti in Windows

Oltre agli elementi elencati in [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics), eseguire le operazioni seguenti:

1. Esportare i registri eventi seguenti nel formato EVTX:

   * Sistema
   * Applicazione
   * Security
   * Operations Manager
   * Microsoft-SMA/operativo

2. Verificare che l'ID dell'area di lavoro a cui l'agente sta segnalando sia uguale all'area di lavoro monitorata dagli aggiornamenti di Windows.

### <a name="for-issues-that-affect-a-job"></a>Per i problemi che interessano un processo

Oltre agli elementi elencati in [4034605](https://support.microsoft.com/help/4034605/how-to-capture-azure-automation-scripted-diagnostics), raccogliere le informazioni seguenti:

1. Raccogliere il numero **JobID** (per il processo in cui si è verificato un problema):

   1. Nel portale di Azure passare al pannello account di **automazione** .
   2. Selezionare l' **account di automazione** per la risoluzione dei problemi.
   3. Selezionare **processi**.
   4. Selezionare il processo di cui si desidera risolvere i problemi.
   5. In **Riepilogo processo**cercare un **ID processo** (Guid).

   ![ID processo nel riquadro Riepilogo processo](media/collect-data-microsoft-azure-automation-case/job-summary-job-id.png)

2. Raccogliere il nome dell'account:

   1. Nel portale di Azure passare al pannello account di **automazione** .
   2. Ottenere il nome dell' **account di automazione** di cui si sta eseguendo la risoluzione dei problemi.

3. Raccoglie l'esempio dello script in esecuzione.

4. Raccogliere i file di log:

   1. Nel portale di Azure passare al pannello account di **automazione** .
   2. Selezionare l' **account di automazione** per la risoluzione dei problemi.
   3. Selezionare **processi**.
   4. Selezionare il processo di cui si desidera risolvere i problemi.
   5. Selezionare **tutti i log**.
   6. Nel pannello risultante raccogliere i dati.

   ![Dati elencati in tutti i log](media/collect-data-microsoft-azure-automation-case/all-logs-data.png)

### <a name="for-issues-that-affect-modules"></a>Per i problemi che interessano i moduli

Oltre agli elementi in "raccolta dati di base", raccogliere le informazioni seguenti:

* I passaggi seguiti in modo che il problema possa essere riprodotto.
* Screenshot di eventuali messaggi di errore.
* Screenshot dei moduli correnti con i relativi numeri di versione.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni in qualsiasi punto di questo articolo, contattare gli esperti di Azure nei [Forum MSDN Azure e stack overflow](https://azure.microsoft.com/support/forums/).

In alternativa, archiviare un evento imprevisto del supporto tecnico di Azure. Accedere al [sito del supporto di Azure](https://azure.microsoft.com/support/options/) e selezionare **Ottenere supporto**.