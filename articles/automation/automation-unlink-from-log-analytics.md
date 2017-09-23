---
title: Scollegare l'account di Automazione di Azure da Log Analytics | Documentazione Microsoft
description: Questo articolo offre una panoramica su come scollegare l'account di Automazione di Azure da un'area di lavoro di OMS.
services: automation
documentationcenter: 
author: mgoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to-article
ms.date: 02/07/2017
ms.author: magoedte
ms.translationtype: Human Translation
ms.sourcegitcommit: 7cd65cd34846122ff14f6d5df61e4f61a7c1ac4f
ms.openlocfilehash: 56b09c2cfc14813b5efcb364c580787fec1bf639
ms.contentlocale: it-it
ms.lasthandoff: 02/08/2017

---

# <a name="how-to-unlink-your-automation-account-from-a-log-analytics-workspace"></a>Come scollegare l'account di automazione da un'area di lavoro di Log Analytics

Automazione di Azure si integra con Log Analytics non solo per supportare il monitoraggio proattivo dei processi di runbook in tutti gli account di Automazione, ma l'integrazione è necessaria anche quando si importano le soluzioni seguenti che dipendono da Log Analytics:

* [Gestione degli aggiornamenti](../operations-management-suite/oms-solution-update-management.md)
* [Rilevamento delle modifiche](../log-analytics/log-analytics-change-tracking.md)
* [Avviare/arrestare le VM durante gli orari di minore attività](automation-solution-vm-management.md)
 
Se si decide che non si vuole più integrare l'account di Automazione con Log Analytics, è possibile scollegare l'account direttamente dal portale di Azure.  Prima di procedere, è necessario rimuovere le soluzioni menzionate in precedenza; in caso contrario non sarà possibile continuare con il processo.  Esaminare l'argomento relativo alla soluzione specifica importata per comprendere i passaggi necessari per la rimozione.  

Dopo la rimozione di queste soluzioni è possibile eseguire i passaggi seguenti per scollegare l'account di automazione.

## <a name="unlink-workspace"></a>Unlink workspace (Scollega area di lavoro)

1. Nel portale di Azure aprire l'account di Automazione e nel pannello Account di automazione selezionare **Unlink workspace** (Scollega area di lavoro) nel pannello dell'account.<br><br> ![Opzione Unlink workspace (Scollega area di lavoro)](media/automation-unlink-from-log-analytics/automation-unlink-workspace-option.png)<br><br>  
2. Nel pannello Unlink workspace (Scollega area di lavoro) fare clic su **Unlink workspace (Scollega area di lavoro)**.<br><br> ![Pannello Unlink workspace (Scollega area di lavoro)](media/automation-unlink-from-log-analytics/automation-unlink-workspace-blade.png).<br><br>  Verrà richiesto di confermare l'operazione.<br><br>
3. Mentre Automazione di Azure tenta di scollegare l'account dall'area di lavoro di Log Analytics, è possibile tenere traccia dello stato di avanzamento in **Notifiche** dal menu.

Se è stata usata la soluzione di gestione degli aggiornamenti, facoltativamente è consigliabile rimuovere gli elementi seguenti che non sono più necessari dopo la rimozione della soluzione.

* Aggiornare le pianificazioni.  Ogni elemento avrà un nome corrispondente alle distribuzioni di aggiornamenti create.

* Gruppi di ruoli di lavoro ibridi creati per la soluzione.  Ogni elemento verrà denominato in modo analogo a machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8.

Se è stata usata la soluzione per avviare/arrestare VM durante gli orari di minore attività, facoltativamente è consigliabile rimuovere gli elementi seguenti che non sono più necessari dopo la rimozione della soluzione.

* Avviare e arrestare le pianificazioni di runbook delle VM 
* Avviare e arrestare i runbook delle VM
* Variabili   

## <a name="next-steps"></a>Passaggi successivi

Per riconfigurare l'account di Automazione per l'integrazione con Log Analytics di OMS, vedere [Inoltrare lo stato e i flussi del processo da Automazione a Log Analytics (OMS)](automation-manage-send-joblogs-log-analytics.md). 
