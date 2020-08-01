---
title: Abilitare Gestione aggiornamenti di Automazione di Azure da una macchina virtuale di Azure
description: Questo articolo illustra come abilitare Gestione aggiornamenti da una macchina virtuale di Azure.
services: automation
ms.date: 07/28/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 27832190125840e367edbfb2db8e4134f98b192d
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2020
ms.locfileid: "87450562"
---
# <a name="enable-update-management-from-an-azure-vm"></a>Abilitare Gestione aggiornamenti da una macchina virtuale di Azure

Questo articolo descrive come usare una macchina virtuale di Azure per abilitare la funzionalità [Gestione aggiornamenti](update-mgmt-overview.md) in altri computer. Per abilitare le macchine virtuali di Azure su larga scala, è necessario abilitare una macchina virtuale esistente usando Gestione aggiornamenti.

> [!NOTE]
> Quando si abilita Gestione aggiornamenti, sono supportate solo determinate aree per il collegamento di un'area di lavoro Log Analytics e un account di Automazione. Per un elenco delle coppie di mapping supportate, vedere [Mapping delle aree per l'account di Automazione e l'area di lavoro Log Analytics](../how-to/region-mappings.md).

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure. Se non si ha ancora una sottoscrizione, è possibile [attivare i vantaggi dell'abbonamento MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Account di Automazione](../index.yml) per gestire i computer.
* Una [macchina virtuale](../../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al [portale di Azure](https://portal.azure.com).

## <a name="enable-the-feature-for-deployment"></a>Abilitare la funzionalità per la distribuzione

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Macchine virtuali** oppure cercare e selezionare **Macchine virtuali** nella pagina Home.

2. Selezionare la macchina virtuale per la quale si vuole abilitare Gestione aggiornamenti. Le macchine virtuali possono esistere in qualsiasi area, indipendentemente dalla posizione dell'account di Automazione. Queste informazioni verranno usate

3. Nella pagina della macchina virtuale, in **Operazioni** selezionare **Gestione aggiornamenti**.

4. È necessario avere l'autorizzazione `Microsoft.OperationalInsights/workspaces/read` per determinare se la macchina virtuale è abilitata per un'area di lavoro. Per informazioni sulle autorizzazioni aggiuntive necessarie, vedere [Autorizzazioni necessarie per abilitare i computer](../automation-role-based-access-control.md#feature-setup-permissions). Per informazioni su come abilitare più computer contemporaneamente, vedere [Abilitare Gestione aggiornamenti da un account di Automazione](update-mgmt-enable-automation-account.md).

5. Scegliere l'area di lavoro Log Analytics e l'account di Automazione da usare e fare clic su **Abilita** per abilitare Gestione aggiornamenti. Dopo aver abilitato Gestione aggiornamenti, prima di poter visualizzare la valutazione dell'aggiornamento dalla macchina virtuale potrebbero essere necessari circa 15 minuti.

    ![Abilitare la gestione degli aggiornamenti](media/update-mgmt-enable-vm/manageupdates-update-enable.png)

## <a name="next-steps"></a>Passaggi successivi

* Per usare Gestione aggiornamenti per le macchine virtuali, vedere [Gestire gli aggiornamenti e le patch per le macchine virtuali di Azure](update-mgmt-manage-updates-for-vm.md).

* Per risolvere gli errori generali di Gestione aggiornamenti, vedere [Risolvere i problemi relativi a Gestione aggiornamenti](../troubleshoot/update-management.md).
