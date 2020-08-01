---
title: Abilitare Gestione aggiornamenti di automazione di Azure dalla portale di Azure
description: Questo articolo illustra come abilitare Gestione aggiornamenti dal portale di Azure.
services: automation
ms.date: 04/11/2019
ms.topic: article
ms.custom: mvc
ms.openlocfilehash: 1ee1b118ed655f29125f0a24de850bbe4ce89939
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2020
ms.locfileid: "87450622"
---
# <a name="enable-update-management-from-the-azure-portal"></a>Abilitare Gestione aggiornamenti dal portale di Azure

Questo articolo descrive come abilitare la funzionalità [Gestione aggiornamenti](update-mgmt-overview.md) per le macchine virtuali esplorando il portale di Azure. Per abilitare le macchine virtuali di Azure su larga scala, è necessario abilitare una macchina virtuale esistente usando Gestione aggiornamenti.

Il numero di gruppi di risorse che è possibile usare per la gestione delle macchine virtuali è vincolato ai [limiti delle distribuzioni di Resource Manager](../../azure-resource-manager/templates/cross-resource-group-deployment.md). Le distribuzioni di Resource Manager, da non confondere con le distribuzioni degli aggiornamenti, sono limitate a cinque gruppi di risorse per distribuzione. Due di questi gruppi di risorse sono riservati alla configurazione dell'area di lavoro Log Analytics, dell'account di Automazione e delle risorse correlate. Rimangono così tre gruppi di risorse da selezionare per la gestione con Gestione aggiornamenti. Questo limite si applica solo alla configurazione simultanea, non al numero di gruppi di risorse che possono essere gestiti da una funzionalità di Automazione.

> [!NOTE]
> Quando si abilita Gestione aggiornamenti, sono supportate solo determinate aree per il collegamento di un'area di lavoro Log Analytics e un account di Automazione. Per un elenco delle coppie di mapping supportate, vedere [Mapping delle aree per l'account di Automazione e l'area di lavoro Log Analytics](../how-to/region-mappings.md).

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure. Se non si ha ancora una sottoscrizione, è possibile [attivare i vantaggi dell'abbonamento MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Account di Automazione](../index.yml) per gestire i computer.
* Una [macchina virtuale](../../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere ad Azure all'indirizzo https://portal.azure.com.

## <a name="enable-update-management"></a>Abilitare la gestione degli aggiornamenti

1. Nel portale di Azure passare a **Macchine virtuali**.

2. Usare le caselle di controllo per scegliere le macchine virtuali da aggiungere a Gestione aggiornamenti. È possibile aggiungere macchine per un massimo di tre gruppi di risorse diversi alla volta. Le macchine virtuali possono trovarsi in qualsiasi area, indipendentemente dalla posizione dell'account di Automazione.

    ![Elenco di VM](media/update-mgmt-enable-portal/vmlist.png)

    > [!TIP]
    > Usare i controlli dei filtri per selezionare macchine virtuali da sottoscrizioni, posizioni e gruppi di risorse diversi. È possibile fare clic sulla casella di controllo superiore per selezionare tutte le macchine virtuali in un elenco.

    [![Abilita gestione aggiornamenti](./media/update-mgmt-enable-portal/onboard-feature.png)](./media/update-mgmt-enable-portal/onboard-feature-expanded.png#lightbox)

3. Selezionare **Servizi** e selezionare **Gestione aggiornamenti** per la funzionalità Gestione aggiornamenti.

4. L'elenco di macchine virtuali viene filtrato per visualizzare solo le macchine virtuali che sono nella stessa sottoscrizione e località. Se le macchine virtuali sono in più di tre gruppi di risorse, vengono selezionati i primi tre gruppi di risorse.

5. Per impostazione predefinita vengono selezionati un'area di lavoro Log Analytics e un account di Automazione esistenti. Se si vuole usare un'area di lavoro Log Analytics diversa e un account di automazione, selezionare **personalizzato** per selezionarli nella pagina Configurazione personalizzata. Quando si sceglie un'area di lavoro Log Analytics, viene effettuato un controllo per determinare se è collegata a un account di Automazione. Se viene trovato un account di Automazione collegato, verrà visualizzata la schermata seguente. Al termine selezionare **OK**.

    [![Selezionare l'area di lavoro e l'account](./media/update-mgmt-enable-portal/select-workspace-and-account.png)](./media/update-mgmt-enable-portal/select-workspace-and-account-expanded.png#lightbox)

6. Se l'area di lavoro selezionata non è collegata a un account di Automazione, verrà visualizzata la schermata seguente. Selezionare un account di automazione e fare clic su **OK** al termine.

    ![Nessuna area di lavoro](media/update-mgmt-enable-portal/no-workspace.png)

7. Deselezionare le macchine virtuali che non si desidera abilitare. Le macchine virtuali che non è possibile abilitare sono già deselezionate.

8. Selezionare **Abilita** per abilitare la funzionalità. Dopo aver abilitato Gestione aggiornamenti, potrebbero essere necessari circa 15 minuti prima di poter visualizzare la valutazione degli aggiornamenti.

## <a name="next-steps"></a>Passaggi successivi

* Per usare Gestione aggiornamenti per le macchine virtuali, vedere [gestire gli aggiornamenti e le patch per le macchine virtuali](update-mgmt-manage-updates-for-vm.md).
* Per risolvere gli errori generali di Gestione aggiornamenti, vedere [Risolvere i problemi relativi a Gestione aggiornamenti](../troubleshoot/update-management.md).
* Per risolvere i problemi relativi all'agente di Windows Update, vedere [Risolvere i problemi relativi all'agente di Windows Update](../troubleshoot/update-agent-issues.md).
* Per risolvere problemi relativi all'agente di aggiornamento Linux, vedere [Risolvere i problemi dell'agente di aggiornamento Linux](../troubleshoot/update-agent-issues-linux.md).
