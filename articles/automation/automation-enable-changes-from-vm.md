---
title: Abilitare Rilevamento modifiche e inventario di Automazione di Azure da una macchina virtuale di Azure
description: Questo articolo descrive come abilitare Rilevamento modifiche e inventario da una macchina virtuale di Azure.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 332ff4f6e63a831c1523c3f959708f9dc48a72a9
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/29/2020
ms.locfileid: "84171056"
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

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni dettagliate sull'uso della funzionalità, vedere [Gestire Rilevamento modifiche e inventario](change-tracking-file-contents.md).
* Per risolvere i problemi generali relativi alla funzionalità, vedere [Risolvere i problemi relativi a Rilevamento modifiche e inventario](troubleshoot/change-tracking.md).
