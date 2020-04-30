---
title: Usare i gruppi dinamici con Gestione aggiornamenti di automazione di Azure
description: Questo articolo descrive il funzionamento dei gruppi dinamici con Gestione aggiornamenti di automazione di Azure.
services: automation
ms.subservice: update-management
ms.date: 11/20/2019
ms.topic: conceptual
ms.openlocfilehash: 29a72eb1fe7b8be18cd2160fc63160e408378585
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81617443"
---
# <a name="use-dynamic-groups-with-update-management"></a>Usare i gruppi dinamici con Gestione aggiornamenti

Gestione aggiornamenti consente di specificare come destinazione un gruppo dinamico di macchine virtuali di Azure o non Azure per le distribuzioni di aggiornamenti. Questi gruppi, definiti dalle query, vengono valutati in fase di distribuzione, in modo da non dover modificare la distribuzione per aggiungere computer.

## <a name="azure-machines"></a>Macchine virtuali di Azure

I gruppi dinamici non funzionano con le VM classiche. Quando si definisce la query, gli elementi seguenti possono essere utilizzati insieme per popolare un gruppo dinamico:

* Subscription
* Gruppi di risorse
* Percorsi
* Tag

![Selezionare i gruppi](./media/automation-update-management/select-groups.png)

Per visualizzare in anteprima i risultati di un gruppo dinamico, fare clic su **Anteprima**. L'anteprima Mostra l'appartenenza al gruppo nell'ora corrente. Nell'esempio è in corso la ricerca dei computer che hanno il `Role` tag per il gruppo **BackendServer**. Se questo tag è stato aggiunto a più computer, questi vengono aggiunti a tutte le distribuzioni future rispetto a tale gruppo.

![Visualizzare in anteprima i gruppi](./media/automation-update-management/preview-groups.png)

## <a name="non-azure-machines"></a>Computer non Azure

Per i computer non Azure, le ricerche salvate, denominate anche gruppi di computer, vengono usate per creare il gruppo dinamico. Per informazioni su come creare una ricerca salvata, vedere [creazione di un gruppo di computer](../azure-monitor/platform/computer-groups.md#creating-a-computer-group). Una volta creato il gruppo, è possibile selezionarlo dall'elenco delle ricerche salvate. Fare clic su **Anteprima** per visualizzare in anteprima i computer nella ricerca salvata in quel momento.

![Selezionare i gruppi](./media/automation-update-management/select-groups-2.png)

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato un gruppo dinamico, è possibile [creare una distribuzione degli aggiornamenti](automation-tutorial-update-management.md).
