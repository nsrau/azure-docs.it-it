---
title: Usare i gruppi dinamici con Gestione aggiornamenti di AzureUse dynamic groups with Azure Update Management
description: Questo articolo descrive il funzionamento dei gruppi dinamici con la gestione degli aggiornamenti di Automazione di Azure.This article describes how dynamic groups work with Azure Automation Update Management.
services: automation
ms.subservice: update-management
ms.date: 11/20/2019
ms.topic: conceptual
ms.openlocfilehash: 678b3f361e4456a2c482896f7d7dc20d530b917b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75420389"
---
# <a name="use-dynamic-groups-with-update-management"></a>Usare i gruppi dinamici con Gestione aggiornamenti

Gestione aggiornamenti offre la possibilità di scegliere come destinazione un gruppo dinamico di macchine virtuali di Azure o non Azure per le distribuzioni di aggiornamenti. Questi gruppi vengono valutati in fase di distribuzione, pertanto non è necessario modificare la distribuzione per aggiungere computer.

## <a name="azure-machines"></a>Macchine di Azure

Questi gruppi vengono definiti da una query. Quando inizia una distribuzione di aggiornamenti, i membri di tale gruppo vengono valutati. I gruppi dinamici non funzionano con le macchine virtuali classiche. Quando si definisce la query, è possibile utilizzare insieme gli elementi seguenti per popolare il gruppo dinamico:

* Subscription
* Gruppi di risorse
* Percorsi
* Tag

![Selezionare i gruppi](./media/automation-update-management/select-groups.png)

Per visualizzare in anteprima i risultati di un gruppo dinamico, fare clic sul pulsante **Anteprima**. Questa anteprima mostra l'appartenenza al gruppo in quel momento. In questo esempio si esegue una ricerca dei computer con il tag **Ruolo** uguale a **BackendServer**. Se questo tag è stato aggiunto a più computer, tali computer verranno aggiunti alle distribuzioni future in quel gruppo.

![Visualizzare in anteprima i gruppi](./media/automation-update-management/preview-groups.png)

## <a name="non-azure-machines"></a>Macchine non Azure

Per i computer non Azure, le ricerche salvate denominate anche gruppi di computer vengono usate per creare il gruppo dinamico. Per informazioni su come creare una ricerca salvata, vedere [Creazione di un gruppo](../azure-monitor/platform/computer-groups.md#creating-a-computer-group)di computer . Una volta creato il gruppo, è possibile selezionarlo dall'elenco delle ricerche salvate. Fare clic su **Anteprima** per visualizzare in anteprima i computer nella ricerca salvata in quel momento.

![Selezionare i gruppi](./media/automation-update-management/select-groups-2.png)

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato un gruppo dinamico, è possibile creare una distribuzione di aggiornamentoAfter creating a dynamic [group,](automation-tutorial-update-management.md) you can Create an Update Deployment
