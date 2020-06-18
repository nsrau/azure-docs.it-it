---
title: Scollegare l'area di lavoro dall'account di Automazione per Rilevamento modifiche e inventario
description: Questo articolo descrive come scollegare un'area di lavoro Log Analytics dall'account di Automazione per Rilevamento modifiche e inventario
services: automation
ms.date: 4/11/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 2be702ec6e820fe71dd8d2da7aa4cf831b52402e
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83828254"
---
# <a name="unlink-workspace-from-automation-account"></a>Scollegare l'area di lavoro dall'account di Automazione

È possibile decidere di non integrare l'account di Automazione con un'area di lavoro Log Analytics quando si abilitano le operazioni di [Rilevamento modifiche e inventario](change-tracking.md). Questo articolo descrive come scollegare l'area di lavoro dall'account.

1. Accedere ad Azure all'indirizzo https://portal.azure.com.

2. Rimuovere Gestione aggiornamenti per le macchine virtuali. Vedere [Rimuovere macchine virtuali da Rilevamento modifiche e inventario](automation-remove-vms-from-change-tracking.md).

3. Se Rilevamento modifiche e inventario include versioni precedenti del monitoraggio SQL di Azure, l'installazione della funzionalità può aver creato risorse di Automazione da rimuovere. Individuare tali asset e rimuoverli.

4. Aprire l'account di Automazione e selezionare **Area di lavoro collegata** in **Risorse correlate** a sinistra.

5. Nella pagina Scollega area di lavoro fare clic su **Scollega area di lavoro** e rispondere alle richieste.

   ![Pagina Unlink workspace (Scollega area di lavoro)](media/automation-unlink-workspace-change-tracking/automation-unlink-workspace-blade.png).

6. Mentre Automazione di Azure tenta di scollegare l'area di lavoro Log Analytics, è possibile tenere traccia dello stato in **Notifiche** dal menu.

In alternativa, è possibile scollegare l'area di lavoro Log Analytics dall'account di Automazione dall'interno dell'area di lavoro:

1. Nell'area di lavoro selezionare **Account di Automazione** in **Risorse correlate**. 
2. Nella pagina Account di Automazione selezionare **Scollega account**.

## <a name="next-steps"></a>Passaggi successivi

* Per usare Rilevamento modifiche e inventario, vedere [Gestire Rilevamento modifiche e inventario](change-tracking-file-contents.md).
* Per risolvere i problemi generali relativi alla funzionalità, vedere [Risolvere i problemi relativi a Rilevamento modifiche e inventario](troubleshoot/change-tracking.md).
