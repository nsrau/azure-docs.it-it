---
title: Abilitare Rilevamento modifiche e inventario di Automazione di Azure da una macchina virtuale di Azure
description: Questo articolo descrive come abilitare Rilevamento modifiche e inventario da una macchina virtuale di Azure.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 5379f2c46bbeaba4ee8509603b7b739b75d08f04
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83836788"
---
# <a name="enable-change-tracking-and-inventory-from-an-azure-vm"></a>Abilitare Rilevamento modifiche e inventario da una macchina virtuale di Azure

Questo articolo descrive come usare una macchina virtuale di Azure per abilitare la funzionalità [Rilevamento modifiche e inventario](change-tracking.md) in altri computer. Per abilitare le macchine virtuali di Azure su larga scala, è necessario abilitare una macchina virtuale esistente usando Rilevamento modifiche e inventario. 

> [!NOTE]
> Quando si abilita Rilevamento modifiche e inventario, sono supportate solo determinate aree geografiche per il collegamento a un'area di lavoro Log Analytics e un account di Automazione. Per un elenco delle coppie di mapping supportate, vedere il [ mapping delle aree per l'account di Automazione e l'area di lavoro Log Analytics](how-to/region-mappings.md).

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure. Se non si ha ancora una sottoscrizione, è possibile [attivare i vantaggi dell'abbonamento MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) oppure iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Account di Automazione](automation-offering-get-started.md) per gestire i computer.
* Una [macchina virtuale](../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo https://portal.azure.com.

## <a name="enable-change-tracking-and-inventory"></a>Abilitare il rilevamento delle modifiche e l'inventario

1. Nel [portale di Azure](https://portal.azure.com) selezionare **Macchine virtuali** oppure cercare e selezionare **Macchine virtuali** nella pagina Home.

2. Selezionare la macchina virtuale per cui si vuole abilitare Rilevamento modifiche e inventario. Le macchine virtuali possono esistere in qualsiasi area, indipendentemente dalla posizione dell'account di Automazione.

3. Nella pagina della VM selezionare **Inventario** o **Rilevamento modifiche** in **Gestione della configurazione**.

4. È necessario avere l'autorizzazione `Microsoft.OperationalInsights/workspaces/read` per determinare se la macchina virtuale è abilitata per un'area di lavoro. Per informazioni sulle autorizzazioni aggiuntive necessarie, vedere le [autorizzazioni di installazione delle funzionalità](automation-role-based-access-control.md#feature-setup-permissions). Per informazioni su come abilitare più macchine contemporaneamente, vedere [Abilitare Rilevamento modifiche e inventario da un account di Automazione](automation-enable-changes-from-auto-acct.md).

5. Scegliere l'area di lavoro Log Analytics e l'account di Automazione e fare clic su **Abilita** per abilitare Rilevamento modifiche e inventario per la macchina virtuale. L'installazione richiede fino a 15 minuti. 

## <a name="check-the-scope-configuration"></a><a name="scope-configuration"></a>Controllare la configurazione dell'ambito

Rilevamento modifiche e inventario usa una configurazione dell'ambito all'interno dell'area di lavoro per individuare i computer nei quali abilitare la funzionalità. La configurazione dell'ambito è un gruppo di una o più ricerche salvate usate per limitare l'ambito della funzionalità a computer specifici. Per altre informazioni, vedere [Usare configurazioni dell'ambito per Rilevamento modifiche e inventario](automation-scope-configurations-change-tracking.md).

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni dettagliate sull'uso della funzionalità, vedere [Gestire Rilevamento modifiche e inventario](change-tracking-file-contents.md).
* Per informazioni sulle configurazioni dell'ambito, vedere [Usare configurazioni dell'ambito per Rilevamento modifiche e inventario](automation-scope-configurations-change-tracking.md).
* Per informazioni su come usare la funzionalità per identificare il software installato nell'ambiente, vedere [Individuare il software installato nelle VM](automation-tutorial-installed-software.md).
* Se non si vuole integrare l'account di automazione con un'area di lavoro Log Analytics quando si abilita la funzionalità, vedere [Scollegare l'area di lavoro dall'account di Automazione](automation-unlink-workspace-change-tracking.md).
* Al termine della distribuzione delle modifiche nelle macchine virtuali, è possibile rimuoverle come descritto in [Rimuovere macchine virtuali da Rilevamento modifiche e inventario](automation-remove-vms-from-change-tracking.md).
* Per risolvere i problemi generali relativi alla funzionalità, vedere [Risolvere i problemi relativi a Rilevamento modifiche e inventario](troubleshoot/change-tracking.md).
