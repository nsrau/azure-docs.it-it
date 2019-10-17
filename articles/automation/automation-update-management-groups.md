---
title: Usare i gruppi dinamici con Gestione aggiornamenti di Azure
description: descrive il funzionamento dei gruppi dinamici con Gestione aggiornamenti
services: automation
ms.service: automation
ms.subservice: update-management
author: bobbytreed
ms.author: robreed
ms.date: 10/02/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: edc4384be0f1dc73f2e7e098114080d304d92ce8
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72377746"
---
# <a name="use-dynamic-groups-with-update-management"></a>Usare i gruppi dinamici con Gestione aggiornamenti

Gestione aggiornamenti offre la possibilità di specificare come destinazione un gruppo dinamico di macchine virtuali di Azure o non Azure per le distribuzioni di aggiornamenti. Questi gruppi vengono valutati in fase di distribuzione, pertanto non è necessario modificare la distribuzione per aggiungere computer.

## <a name="azure-machines"></a>Macchine virtuali di Azure

Questi gruppi vengono definiti da una query. Quando inizia una distribuzione di aggiornamenti, i membri di tale gruppo vengono valutati. I gruppi dinamici non funzionano con le VM classiche. Quando si definisce la query, gli elementi seguenti possono essere utilizzati insieme per popolare il gruppo dinamico:

* Sottoscrizione
* Gruppi di risorse
* Località
* Tag

![Selezionare i gruppi](./media/automation-update-management/select-groups.png)

Per visualizzare in anteprima i risultati di un gruppo dinamico, fare clic sul pulsante **Anteprima**. Questa anteprima mostra l'appartenenza al gruppo in quel momento. In questo esempio si esegue una ricerca dei computer con il tag **Ruolo** uguale a **BackendServer**. Se questo tag è stato aggiunto a più computer, tali computer verranno aggiunti alle distribuzioni future in quel gruppo.

![Visualizzare in anteprima i gruppi](./media/automation-update-management/preview-groups.png)

## <a name="non-azure-machines"></a>Computer non Azure

Per i computer non Azure, le ricerche salvate definite anche come gruppi di computer vengono usate per creare il gruppo dinamico. Per informazioni su come creare una ricerca salvata, vedere [creazione di un gruppo di computer](../azure-monitor/platform/computer-groups.md#creating-a-computer-group). Una volta creato il gruppo, è possibile selezionarlo dall'elenco di ricerche salvate. Fare clic su **Anteprima** per visualizzare in anteprima i computer nella ricerca salvata in quel momento.

![Selezionare i gruppi](./media/automation-update-management/select-groups-2.png)

## <a name="next-steps"></a>Passaggi successivi

Dopo aver creato un gruppo dinamico, è possibile [creare una distribuzione degli aggiornamenti](automation-tutorial-update-management.md)
