---
title: Abilitare Rilevamento modifiche e inventario di Automazione di Azure dal portale di Azure
description: Questo articolo descrive come abilitare la funzionalità Rilevamento modifiche e inventario dal portale di Azure.
services: automation
ms.subservice: change-inventory-management
ms.date: 10/14/2020
ms.topic: conceptual
ms.openlocfilehash: 9d13e19958ce4614db3706845cc6717b77f60c4c
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92209880"
---
# <a name="enable-change-tracking-and-inventory-from-azure-portal"></a>Abilitare Rilevamento modifiche e inventario dal portale di Azure

Questo articolo descrive come abilitare [rilevamento modifiche e l'inventario](overview.md) per una o più macchine virtuali di Azure nel portale di Azure. Per abilitare le macchine virtuali di Azure su larga scala, è necessario abilitare una macchina virtuale esistente usando Rilevamento modifiche e inventario.

Il numero di gruppi di risorse che è possibile usare per la gestione delle macchine virtuali è vincolato ai [limiti di distribuzione di Resource Manager](../../azure-resource-manager/templates/cross-scope-deployment.md). Gestione risorse le distribuzioni sono limitate a cinque gruppi di risorse per ogni distribuzione. Due di questi gruppi di risorse sono riservati alla configurazione dell'area di lavoro Log Analytics, dell'account di Automazione e delle risorse correlate. Rimangono così tre gruppi di risorse da selezionare per la gestione con Rilevamento modifiche e inventario. Questo limite si applica solo alla configurazione simultanea, non al numero di gruppi di risorse che possono essere gestiti da una funzionalità di Automazione.

> [!NOTE]
> Quando si abilita Rilevamento modifiche e inventario, sono supportate solo determinate aree geografiche per il collegamento a un'area di lavoro Log Analytics e un account di Automazione. Per un elenco delle coppie di mapping supportate, vedere [Mapping delle aree per l'account di Automazione e l'area di lavoro Log Analytics](../how-to/region-mappings.md).

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure. Se non si ha ancora una sottoscrizione, è possibile [attivare i vantaggi dell'abbonamento MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Account di Automazione](../index.yml) per gestire i computer.
* Una [macchina virtuale](../../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere ad Azure all'indirizzo https://portal.azure.com.

## <a name="enable-change-tracking-and-inventory"></a>Abilitare il rilevamento delle modifiche e l'inventario

1. Nel portale di Azure passare a **Macchine virtuali**.

2. Usare le caselle di controllo per scegliere le macchine virtuali da aggiungere a Rilevamento modifiche e inventario. È possibile aggiungere macchine per un massimo di tre gruppi di risorse diversi alla volta. Le macchine virtuali possono trovarsi in qualsiasi area, indipendentemente dalla posizione dell'account di Automazione.

    ![Elenco di VM](media/enable-from-portal/vmlist.png)

    > [!TIP]
    > Usare i controlli dei filtri per selezionare macchine virtuali da sottoscrizioni, posizioni e gruppi di risorse diversi. È possibile fare clic sulla casella di controllo superiore per selezionare tutte le macchine virtuali in un elenco.

3. Selezionare **Rilevamento modifiche** o **Inventario** in **Gestione della configurazione**.

4. L'elenco di macchine virtuali viene filtrato per visualizzare solo le macchine virtuali che sono nella stessa sottoscrizione e località. Se le macchine virtuali sono in più di tre gruppi di risorse, vengono selezionati i primi tre gruppi di risorse.

5. Per impostazione predefinita vengono selezionati un'area di lavoro Log Analytics e un account di Automazione esistenti. Per usare un'area di lavoro Log Analytics e un account di Automazione diversi, fare clic su **PERSONALIZZATO** per selezionarli dalla pagina Configurazione personalizzata. Quando si sceglie un'area di lavoro Log Analytics, viene effettuato un controllo per determinare se è collegata a un account di Automazione. Se viene trovato un account di Automazione collegato, verrà visualizzata la schermata seguente. Al termine, fare clic su **OK**.

    ![Selezionare un'area di lavoro e un account](media/enable-from-portal/select-workspace-and-account.png)

6. Se l'area di lavoro selezionata non è collegata a un account di Automazione, verrà visualizzata la schermata seguente. Selezionare un account di Automazione e al termine fare clic su **OK**.

    ![Nessuna area di lavoro](media/enable-from-portal/no-workspace.png)

7. Deselezionare la casella di controllo accanto alle macchine virtuali che non si vuole abilitare. Le macchine virtuali che non è possibile abilitare sono già deselezionate.

8. Fare clic su **Abilita** per abilitare la funzionalità selezionata. L'installazione richiede fino a 15 minuti.

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni dettagliate sull'uso della funzionalità, vedere [gestire rilevamento modifiche](manage-change-tracking.md) e [gestire l'inventario](manage-inventory-vms.md).
* Per risolvere i problemi generali relativi alla funzionalità, vedere [Risolvere i problemi relativi a Rilevamento modifiche e inventario](../troubleshoot/change-tracking.md).
