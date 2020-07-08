---
title: Usare i gruppi dinamici con Gestione aggiornamenti di Automazione di Azure
description: Questo articolo illustra come usare i gruppi dinamici con Gestione aggiornamenti di Automazione di Azure.
services: automation
ms.subservice: update-management
ms.date: 11/20/2019
ms.topic: conceptual
ms.openlocfilehash: c440862f1379983d4644fe99f33207456f747d23
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.contentlocale: it-IT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83830940"
---
# <a name="use-dynamic-groups-with-update-management"></a>Usare i gruppi dinamici con Gestione aggiornamenti

Gestione aggiornamenti consente di specificare come destinazione un gruppo dinamico di macchine virtuali di Azure o non di Azure per le distribuzioni degli aggiornamenti. L'uso di un gruppo dinamico consente di evitare di modificare la distribuzione per aggiornare i computer.

> [!NOTE]
> I gruppi dinamici non funzionano con le macchine virtuali classiche.

È possibile definire gruppi dinamici per macchine virtuali di Azure o non di Azure da **Gestione aggiornamenti** nel portale di Azure. Vedere [Gestire gli aggiornamenti per più macchine virtuali di Azure](manage-update-multi.md).

Un gruppo dinamico è definito da una query che Automazione di Azure valuta in fase di distribuzione. Anche se la query di un gruppo dinamico recupera un numero elevato di macchine virtuali, Automazione di Azure può elaborare solo un massimo di 1000 macchine virtuali alla volta. Vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#update-management). 

> [!NOTE]
> Se si prevede di aggiornare più di 1000 macchine virtuali, è consigliabile suddividere gli aggiornamenti tra più pianificazioni. 

## <a name="define-dynamic-groups-for-azure-machines"></a>Definire gruppi dinamici per macchine virtuali di Azure

Quando si definisce la query di un gruppo dinamico per macchine virtuali di Azure, per popolare il gruppo dinamico è possibile usare gli elementi seguenti:

* Subscription
* Gruppi di risorse
* Percorsi
* Tag

![Selezionare i gruppi](./media/automation-update-management/select-groups.png)

Per visualizzare in anteprima i risultati della query del gruppo dinamico, fare clic su **Anteprima**. L'anteprima visualizza l'appartenenza a gruppi in quel momento. Nell'esempio vengono cercate le macchine virtuali con il tag `Role` per il gruppo **BackendServer**. Se questo tag è stato aggiunto a più macchine virtuali, queste vengono aggiunte alle distribuzioni future in quel gruppo.

![Visualizzare in anteprima i gruppi](./media/automation-update-management/preview-groups.png)

## <a name="define-dynamic-groups-for-non-azure-machines"></a>Definire gruppi dinamici per macchine virtuali non di Azure

Un gruppo dinamico per macchine virtuali non di Azure usa le ricerche salvate, dette anche gruppi di computer. Per informazioni su come creare una ricerca salvata, vedere [Creazione di un gruppo di computer](../azure-monitor/platform/computer-groups.md#creating-a-computer-group). Dopo aver creato la ricerca salvata, è possibile selezionarla dall'elenco delle ricerche salvate in **Gestione aggiornamenti** nel portale di Azure. Fare clic su **Anteprima** per visualizzare in anteprima i computer nella ricerca salvata.

![Selezionare i gruppi](./media/automation-update-management/select-groups-2.png)

## <a name="next-steps"></a>Passaggi successivi

Per usare Gestione aggiornamenti, vedere [Gestire gli aggiornamenti e le patch per le macchine virtuali di Azure](automation-tutorial-update-management.md).
