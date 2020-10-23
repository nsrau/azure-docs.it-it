---
title: Rimuovere Rilevamento modifiche di automazione di Azure e la funzionalità di inventario
description: Questo articolo illustra come interrompere l'uso di Rilevamento modifiche e l'inventario e scollegare un account di automazione dall'area di lavoro Log Analytics.
services: automation
ms.subservice: change-inventory-management
ms.date: 10/14/2020
ms.topic: conceptual
ms.openlocfilehash: 2e3e5abdfbb2bf2e9d7a12a677422adc67336775
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92210264"
---
# <a name="remove-change-tracking-and-inventory-from-automation-account"></a>Rimuovere Rilevamento modifiche e l'inventario dall'account di automazione

Dopo aver abilitato la gestione delle macchine virtuali tramite automazione di Azure Rilevamento modifiche e l'inventario, è possibile decidere di interrompere l'uso e rimuovere la configurazione dall'account e dall'area di lavoro Log Analytics collegata. Questo articolo descrive come rimuovere completamente Rilevamento modifiche e inventario dalle macchine virtuali gestite, dall'account di automazione e da Log Analytics area di lavoro.

## <a name="sign-into-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com).

## <a name="remove-management-of-vms"></a>Rimuovere la gestione delle macchine virtuali

Prima di rimuovere Rilevamento modifiche e inventario, è necessario prima di tutto arrestare la gestione delle macchine virtuali. Vedere [rimuovere VM da rilevamento modifiche per annullare](remove-vms-from-change-tracking.md) la registrazione dalla funzionalità.

## <a name="remove-changetracking-solution"></a>Rimuovere la soluzione rilevamento modifiche

Prima di poter scollegare l'account di automazione dall'area di lavoro, è necessario seguire questa procedura per rimuovere completamente Rilevamento modifiche e l'inventario. La soluzione **rilevamento modifiche** verrà rimossa dall'area di lavoro.

1. Nel portale di Azure fare clic su **Tutti i servizi**. Nell'elenco delle risorse digitare **Log Analytics**. Quando si inizia a digitare, l'elenco Filtra i suggerimenti in base all'input. Selezionare **Log Analytics**.

2. Nell'elenco delle aree di lavoro Log Analytics selezionare l'area di lavoro scelta quando è stata abilitata Rilevamento modifiche e l'inventario.

3. A sinistra selezionare **soluzioni**.  

4. Nell'elenco delle soluzioni selezionare **rilevamento modifiche (nome dell'area di lavoro)**. Nella pagina **Overview (panoramica** ) per la soluzione selezionare **Delete (Elimina**). Quando viene richiesto di confermare, selezionare **Sì**.

## <a name="unlink-workspace-from-automation-account"></a>Scollegare l'area di lavoro dall'account di Automazione

1. Nel portale di Azure selezionare **Account di Automazione**.

2. Aprire l'account di Automazione e selezionare **Area di lavoro collegata** in **Risorse correlate** a sinistra.

3. Nella pagina **Scollega area di lavoro** selezionare **Scollega area di lavoro** e rispondere alle richieste.

   ![Pagina Unlink workspace (Scollega area di lavoro)](media/remove-feature/automation-unlink-workspace-blade.png)

Mentre tenta di scollegare l'area di lavoro Log Analytics, è possibile tenere traccia dello stato di avanzamento in **notifiche** dal menu.

In alternativa, è possibile scollegare l'area di lavoro Log Analytics dall'account di Automazione dall'interno dell'area di lavoro:

1. Nel portale di Azure selezionare **Log Analytics**.

2. Dall'area di lavoro, selezionare **account di automazione** in **risorse correlate**.

3. Nella pagina Account di Automazione selezionare **Scollega account**.

Mentre tenta di scollegare l'account di automazione, è possibile tenere traccia dello stato di avanzamento in **notifiche** dal menu.

## <a name="next-steps"></a>Passaggi successivi

Per abilitare nuovamente questa funzionalità, vedere [abilitare rilevamento modifiche e inventario da un account di automazione](enable-from-automation-account.md), [abilitare rilevamento modifiche e inventario esplorando il portale di Azure](enable-from-portal.md), [abilitare rilevamento modifiche e inventario da un Runbook](enable-from-runbook.md)o [abilitare rilevamento modifiche e inventario da una macchina virtuale di Azure](enable-from-vm.md).
