---
title: Scollegare l'area di lavoro dall'account di Automazione per Gestione aggiornamenti
description: Questo articolo descrive come scollegare un'area di lavoro Log Analytics dall'account di Automazione per Gestione aggiornamenti
services: automation
ms.date: 4/11/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 9129d10071a4c8da0376cbad3d64c10cbaceb8b9
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.contentlocale: it-IT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83835785"
---
# <a name="unlink-workspace-from-automation-account-for-update-management"></a>Scollegare l'area di lavoro dall'account di Automazione per Gestione aggiornamenti

È possibile decidere di non integrare l'account di Automazione con un'area di lavoro Log Analytics durante le operazioni di [Gestione aggiornamenti](automation-update-management.md). Questo articolo descrive come scollegare l'area di lavoro dall'account.

1. Accedere ad Azure all'indirizzo https://portal.azure.com.

2. Rimuovere Gestione aggiornamenti per le macchine virtuali. Vedere [Rimuovere macchine virtuali da Gestione aggiornamenti](automation-remove-vms-from-update-management.md).

3. Se Gestione aggiornamenti include versioni precedenti del monitoraggio SQL di Azure, l'installazione della funzionalità può aver creato risorse di automazione da rimuovere. Per Gestione aggiornamenti, è necessario rimuovere gli elementi seguenti non più necessari:

   * Pianificazioni degli aggiornamenti - Ognuna ha un nome corrispondente alla distribuzione dell'aggiornamento creata.
   * Gruppi di ruoli di lavoro ibridi creati Gestione aggiornamenti - Ognuno ha un nome simile a machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8.

4. Aprire l'account di Automazione e selezionare **Linked workspace** (Area di lavoro collegata) in **Risorse correlate** a sinistra.

5. Nella pagina Scollega area di lavoro fare clic su **Scollega area di lavoro** e rispondere alle richieste.

   ![Pagina Unlink workspace (Scollega area di lavoro)](media/automation-unlink-workspace-update-management/automation-unlink-workspace-blade.png).

6. Mentre Automazione di Azure tenta di scollegare l'area di lavoro Log Analytics, è possibile tenere traccia dello stato in **Notifiche** dal menu.

In alternativa, è possibile scollegare l'area di lavoro Log Analytics dall'account di Automazione dall'interno dell'area di lavoro:

1. Nell'area di lavoro selezionare **Account di Automazione** in **Risorse correlate**. 
2. Nella pagina Account di Automazione selezionare **Scollega l'account**.

## <a name="next-steps"></a>Passaggi successivi

* Per usare la funzionalità, vedere [Gestire gli aggiornamenti e le patch per le macchine virtuali di Azure](automation-tutorial-update-management.md).
* Per risolvere gli errori della funzionalità, vedere [Risolvere i problemi relativi a Gestione aggiornamenti](troubleshoot/update-management.md).
* Per risolvere i problemi relativi all'agente di Windows Update, vedere [Risolvere i problemi dell'agente di Windows Update](troubleshoot/update-agent-issues.md).
* Per risolvere i problemi relativi all'agente di aggiornamento di Linux, vedere[Risolvere i problemi dell'agente di aggiornamento di Linux](troubleshoot/update-agent-issues-linux.md).
