---
title: Rimuovere macchine virtuali da Rilevamento modifiche e inventario di Automazione di Azure
description: Questo articolo descrive come rimuovere le macchine virtuali da Rilevamento modifiche e inventario.
services: automation
ms.subservice: change-inventory-management
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: 61ced7eda78d3ce56d78dfd6cc54e0e92d376e68
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93100678"
---
# <a name="remove-vms-from-change-tracking-and-inventory"></a>Rimuovere macchine virtuali da Rilevamento modifiche e inventario

Al termine della verifica delle modifiche apportate alle VM nell'ambiente, è possibile interrompere la gestione con la funzionalità [rilevamento modifiche e inventario](overview.md) . Per arrestarne la gestione, modificare la query di ricerca salvata `MicrosoftDefaultComputerGroup` nell'area di lavoro log Analytics collegata all'account di automazione.

## <a name="sign-into-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com).

## <a name="to-remove-your-vms"></a>Per rimuovere le macchine virtuali

1. Nel portale di Azure avviare **cloud Shell** dalla parte superiore della portale di Azure. Se non si ha familiarità con Azure Cloud Shell, vedere [Panoramica di Azure cloud Shell](/azure/cloud-shell/overview).

2. Usare il comando seguente per identificare l'UUID di un computer che si vuole rimuovere dalla gestione.

    ```azurecli
    az vm show -g MyResourceGroup -n MyVm -d
    ```

3. Nella portale di Azure passare a **log Analytics aree di lavoro** . Selezionare l'area di lavoro dall'elenco.

4. Nell'area di lavoro Log Analytics selezionare **log** , quindi scegliere **Esplora query** dal menu azioni principali.

5. Da **Esplora query** nel riquadro di destra, espandere **salvato Queries\Updates** e selezionare la query di ricerca salvata `MicrosoftDefaultComputerGroup` per modificarla.

6. Nell'editor di query esaminare la query e trovare l'UUID per la macchina virtuale. Rimuovere l'UUID per la macchina virtuale e ripetere i passaggi per qualsiasi altra VM che si vuole rimuovere.

7. Salvare la ricerca salvata al termine della modifica selezionando **Salva** nella barra superiore.

>[!NOTE]
>Le macchine virtuali vengono comunque visualizzate dopo aver annullato la registrazione, perché si segnalano tutti i computer valutati nelle ultime 24 ore. Dopo aver rimosso il computer, è necessario attendere 24 ore prima che non vengano più elencate.

## <a name="next-steps"></a>Passaggi successivi

Per abilitare nuovamente questa funzionalità, vedere [abilitare rilevamento modifiche e inventario da un account di automazione](enable-from-automation-account.md), [abilitare rilevamento modifiche e inventario esplorando il portale di Azure](enable-from-portal.md), [abilitare rilevamento modifiche e inventario da un Runbook](enable-from-runbook.md)o [abilitare rilevamento modifiche e inventario da una macchina virtuale di Azure](enable-from-vm.md).