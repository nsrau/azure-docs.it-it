---
title: Rimuovere VM da Gestione aggiornamenti di Automazione di Azure
description: Questo articolo descrive come rimuovere i computer gestiti con Gestione aggiornamenti.
services: automation
ms.topic: conceptual
ms.date: 07/28/2020
ms.custom: mvc
ms.openlocfilehash: d7f7e4aa8b2c192688020b4449c8750f94af29f6
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2020
ms.locfileid: "87450444"
---
# <a name="remove-vms-from-update-management"></a>Rimuovere macchine virtuali da Gestione aggiornamenti

Al termine della gestione degli aggiornamenti nelle VM nell'ambiente in uso, è possibile arrestare la gestione delle VM con la funzionalità [Gestione aggiornamenti](update-mgmt-overview.md) .

## <a name="sign-into-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com).

## <a name="to-remove-your-vms"></a>Per rimuovere le macchine virtuali

1. Nell'account di Automazione selezionare **Gestione aggiornamenti** in **Gestione aggiornamenti**.

2. Usare il comando seguente per identificare l'UUID di un computer che si vuole rimuovere dalla gestione.

    ```azurecli
    az vm show -g MyResourceGroup -n MyVm -d
    ```

3. Nell'area di lavoro Log Analytics in **generale**accedere alle ricerche salvate per la configurazione dell'ambito `MicrosoftDefaultScopeConfig-Updates` .

4. Per la di ricerca salvata `MicrosoftDefaultComputerGroup`, fare clic sui puntini di sospensione a destra e selezionare **Modifica**.

5. Rimuovere l'UUID per la macchina virtuale.

6. Ripetere i passaggi per qualsiasi altra VM da rimuovere.

7. Al termine della modifica, salvare la ricerca salvata.

>[!NOTE]
>Le macchine virtuali vengono comunque visualizzate dopo aver annullato la registrazione, perché si segnalano tutti i computer valutati nelle ultime 24 ore. Dopo la disconnessione del computer, è necessario attendere 24 ore prima che non vengano più elencate.

## <a name="next-steps"></a>Passaggi successivi

Per abilitare di nuovo la gestione della macchina virtuale, vedere [abilitare Gestione aggiornamenti esplorando il portale di Azure](update-mgmt-enable-portal.md) o [abilitare Gestione aggiornamenti da una macchina virtuale di Azure](update-mgmt-enable-vm.md).