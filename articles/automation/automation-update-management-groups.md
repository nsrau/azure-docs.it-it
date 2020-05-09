---
title: Usare i gruppi dinamici con Gestione aggiornamenti di automazione di Azure
description: Questo articolo descrive il funzionamento dei gruppi dinamici con Gestione aggiornamenti di automazione di Azure.
services: automation
ms.subservice: update-management
ms.date: 11/20/2019
ms.topic: conceptual
ms.openlocfilehash: bf4c156d2bf9c205bd7545a96b5314dd43b2d02c
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/01/2020
ms.locfileid: "82690783"
---
# <a name="use-dynamic-groups-with-update-management"></a>Usare i gruppi dinamici con Gestione aggiornamenti

Gestione aggiornamenti consente di specificare come destinazione un gruppo dinamico di macchine virtuali di Azure o non Azure per le distribuzioni di aggiornamenti. L'uso di un gruppo dinamico evita di dover modificare la distribuzione per aggiornare i computer.

> [!NOTE]
> I gruppi dinamici non funzionano con le VM classiche.

È possibile definire gruppi dinamici per computer Azure o non Azure da **Gestione aggiornamenti** nella portale di Azure. Vedere [gestire gli aggiornamenti per più macchine virtuali di Azure](manage-update-multi.md).

Un gruppo dinamico è definito da una query che automazione di Azure valuta in fase di distribuzione. Anche se una query di gruppo dinamica recupera un numero elevato di computer, automazione di Azure può elaborare solo un massimo di 1000 computer alla volta. Vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#update-management). 

> [!NOTE]
> Se si prevede di aggiornare più di 1000 computer, è consigliabile suddividere gli aggiornamenti tra più pianificazioni degli aggiornamenti. 

## <a name="define-dynamic-groups-for-azure-machines"></a>Definire gruppi dinamici per le macchine virtuali di Azure

Quando si definisce una query di gruppo dinamica per i computer Azure, è possibile usare gli elementi seguenti per popolare il gruppo dinamico:

* Subscription
* Gruppi di risorse
* Percorsi
* Tag

![Selezionare i gruppi](./media/automation-update-management/select-groups.png)

Per visualizzare in anteprima i risultati della query di gruppo dinamica, fare clic su **Anteprima**. L'anteprima Mostra l'appartenenza al gruppo nell'ora corrente. Nell'esempio è in corso la ricerca dei computer che hanno il `Role` tag per il gruppo **BackendServer**. Se questo tag è stato aggiunto a più computer, questi vengono aggiunti a tutte le distribuzioni future rispetto a tale gruppo.

![Visualizzare in anteprima i gruppi](./media/automation-update-management/preview-groups.png)

## <a name="define-dynamic-groups-for-non-azure-machines"></a>Definire gruppi dinamici per computer non Azure

Un gruppo dinamico per computer non Azure usa le ricerche salvate, denominate anche gruppi di computer. Per informazioni su come creare una ricerca salvata, vedere [creazione di un gruppo di computer](../azure-monitor/platform/computer-groups.md#creating-a-computer-group). Una volta creata la ricerca salvata, è possibile selezionarla nell'elenco delle ricerche salvate in **Gestione aggiornamenti** nella portale di Azure. Fare clic su **Anteprima** per visualizzare un'anteprima dei computer nella ricerca salvata.

![Selezionare i gruppi](./media/automation-update-management/select-groups-2.png)

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato un gruppo dinamico, è possibile [creare una distribuzione degli aggiornamenti](automation-tutorial-update-management.md).
