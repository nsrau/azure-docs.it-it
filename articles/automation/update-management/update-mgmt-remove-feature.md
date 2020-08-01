---
title: Rimuovere la funzionalità Gestione aggiornamenti di automazione di Azure
description: Questo articolo descrive come interrompere l'uso di Gestione aggiornamenti e scollegare un account di automazione dall'area di lavoro Log Analytics.
services: automation
ms.date: 07/28/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 4b4946da9f63299c7ba2b383d6c153673595a1ab
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2020
ms.locfileid: "87450113"
---
# <a name="remove-update-management-from-automation-account"></a>Rimuovere Gestione aggiornamenti dall'account di automazione

Dopo aver abilitato la gestione degli aggiornamenti nelle macchine virtuali usando Gestione aggiornamenti di automazione di Azure, è possibile decidere di interrompere l'uso e rimuovere la configurazione dall'account e dall'area di lavoro Log Analytics collegata.  Questo articolo descrive come rimuovere completamente Gestione aggiornamenti dalle macchine virtuali gestite, dall'account di automazione e da Log Analytics area di lavoro.

## <a name="sign-into-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com).

## <a name="remove-management-of-vms"></a>Rimuovere la gestione delle macchine virtuali

Prima di rimuovere Gestione aggiornamenti, è necessario prima arrestare la gestione delle macchine virtuali. Vedere [rimuovere VM da Gestione aggiornamenti per annullare](update-mgmt-remove-vms.md) la registrazione dalla funzionalità.

## <a name="remove-updatemanagement-solution"></a>Rimuovere la soluzione UpdateManagement

Prima di poter scollegare l'account di automazione dall'area di lavoro, è necessario seguire questa procedura per rimuovere completamente Gestione aggiornamenti. La soluzione **aggiornamenti** verrà rimossa dall'area di lavoro.

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Nel portale di Azure fare clic su **Tutti i servizi**. Nell'elenco delle risorse digitare **Log Analytics**. Quando si inizia a digitare, l'elenco Filtra i suggerimenti in base all'input. Selezionare **log Analytics**.

3. Nell'elenco delle aree di lavoro Log Analytics selezionare l'area di lavoro scelta quando è stata abilitata Gestione aggiornamenti.

4. A sinistra selezionare **soluzioni**.  

5. Nell'elenco delle soluzioni selezionare **aggiornamenti (nome dell'area di lavoro)**. Nella pagina **Overview (panoramica** ) per la soluzione selezionare **Delete (Elimina**). Quando viene richiesto di confermare, selezionare **Sì**.

## <a name="unlink-workspace-from-automation-account"></a>Scollegare l'area di lavoro dall'account di Automazione

1. Nel portale di Azure selezionare **Account di Automazione**.

2. Aprire l'account di Automazione e selezionare **Area di lavoro collegata** in **Risorse correlate** a sinistra.

3. Nella pagina **Scollega area di lavoro** selezionare **Scollega area di lavoro** e rispondere alle richieste.

   ![Pagina Unlink workspace (Scollega area di lavoro)](media/update-mgmt-remove-feature/automation-unlink-workspace-blade.png)

Mentre tenta di scollegare l'area di lavoro Log Analytics, è possibile tenere traccia dello stato di avanzamento in **notifiche** dal menu.

In alternativa, è possibile scollegare l'area di lavoro Log Analytics dall'account di Automazione dall'interno dell'area di lavoro:

1. Nel portale di Azure selezionare **Log Analytics**.

2. Dall'area di lavoro, selezionare **account di automazione** in **risorse correlate**.

3. Nella pagina Account di Automazione selezionare **Scollega account**.

Mentre tenta di scollegare l'account di automazione, è possibile tenere traccia dello stato di avanzamento in **notifiche** dal menu.

## <a name="cleanup-automation-account"></a>Pulisci account di automazione

Se Gestione aggiornamenti è stato configurato per supportare le versioni precedenti di monitoraggio di Azure SQL, il programma di installazione della funzionalità potrebbe avere creato asset di automazione da rimuovere. Per Gestione aggiornamenti, è necessario rimuovere gli elementi seguenti non più necessari:

   * Pianificazioni degli aggiornamenti - Ognuna ha un nome corrispondente alla distribuzione dell'aggiornamento creata.
   * Gruppi di lavoro ibridi creati per Gestione aggiornamenti. ognuno è denominato in modo analogo a *machine1. contoso. com_9ceb8108-26C9-4051-B6B3-227600d715c8)*.

## <a name="next-steps"></a>Passaggi successivi

Per abilitare nuovamente questa funzionalità, vedere [abilitare Gestione aggiornamenti da un account di automazione](update-mgmt-enable-automation-account.md), [abilitare Gestione aggiornamenti esplorando il portale di Azure](update-mgmt-enable-portal.md), [abilitare Gestione aggiornamenti da un Runbook](update-mgmt-enable-runbook.md)o [abilitare Gestione aggiornamenti da una macchina virtuale di Azure](update-mgmt-enable-vm.md).
