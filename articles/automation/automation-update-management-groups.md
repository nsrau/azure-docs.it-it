---
title: Usare i gruppi dinamici con La gestione degli aggiornamenti di Automazione di AzureUse dynamic groups with Azure Automation Update Management
description: Questo articolo descrive il funzionamento dei gruppi dinamici con la gestione degli aggiornamenti di Automazione di Azure.This article describes how dynamic groups work with Azure Automation Update Management.
services: automation
ms.subservice: update-management
ms.date: 11/20/2019
ms.topic: conceptual
ms.openlocfilehash: 29a72eb1fe7b8be18cd2160fc63160e408378585
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617443"
---
# <a name="use-dynamic-groups-with-update-management"></a>Usare i gruppi dinamici con Gestione aggiornamenti

La gestione degli aggiornamenti consente di scegliere come destinazione un gruppo dinamico di macchine virtuali di Azure o non Azure per le distribuzioni di aggiornamenti. Questi gruppi, definiti dalle query, vengono valutati in fase di distribuzione in modo che non sia necessario modificare la distribuzione per aggiungere computer.

## <a name="azure-machines"></a>Macchine di Azure

I gruppi dinamici non funzionano con le macchine virtuali classiche. Quando si definisce la query, è possibile utilizzare insieme gli elementi seguenti per popolare un gruppo dinamico:

* Subscription
* Gruppi di risorse
* Percorsi
* Tag

![Selezionare i gruppi](./media/automation-update-management/select-groups.png)

Per visualizzare in anteprima i risultati di un gruppo dinamico, fare clic su **Anteprima**. L'anteprima mostra l'appartenenza al gruppo all'ora corrente. Nell'esempio vengono cercati i computer `Role` con il tag per il gruppo **BackendServer**. Se questo tag è stato aggiunto a più computer, questi vengono aggiunti a tutte le distribuzioni future in tale gruppo.

![Visualizzare in anteprima i gruppi](./media/automation-update-management/preview-groups.png)

## <a name="non-azure-machines"></a>Macchine non Azure

Per i computer non Azure, le ricerche salvate, denominate anche gruppi di computer, vengono usate per creare il gruppo dinamico. Per informazioni su come creare una ricerca salvata, vedere [Creazione di un gruppo](../azure-monitor/platform/computer-groups.md#creating-a-computer-group)di computer . Una volta creato il gruppo, è possibile selezionarlo dall'elenco delle ricerche salvate. Fare clic su **Anteprima** per visualizzare in anteprima i computer nella ricerca salvata in quel momento.

![Selezionare i gruppi](./media/automation-update-management/select-groups-2.png)

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato un gruppo dinamico, è possibile creare una distribuzione di [aggiornamento](automation-tutorial-update-management.md).
