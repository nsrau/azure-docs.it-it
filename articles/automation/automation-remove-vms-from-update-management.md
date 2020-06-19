---
title: Rimuovere VM da Gestione aggiornamenti di Automazione di Azure
description: Questo articolo illustra come rimuovere VM da Gestione aggiornamenti.
services: automation
ms.topic: conceptual
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: 26a38c0851643fbd6446acddd99a05abf8d5b26c
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83836652"
---
# <a name="remove-vms-from-update-management"></a>Rimuovere macchine virtuali da Gestione aggiornamenti

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo https://portal.azure.com.

Dopo aver completato la distribuzione di aggiornamenti nelle VM dell'ambiente, è possibile rimuoverle dalla funzionalità [Gestione aggiornamenti](automation-update-management.md).

1. Nell'account di Automazione selezionare **Gestione aggiornamenti** in **Gestione aggiornamenti**.

2. Usare il comando seguente per identificare l'UUID di una macchina virtuale da rimuovere dalla gestione.

    ```azurecli
    az vm show -g MyResourceGroup -n MyVm -d
    ```

3. Nell'area di lavoro Log Analytics accedere alle ricerche salvate in **Generale**.

4. Per la di ricerca salvata `MicrosoftDefaultComputerGroup`, fare clic sui puntini di sospensione a destra e selezionare **Modifica**. 

5. Rimuovere l'UUID per la macchina virtuale.

6. Ripetere i passaggi per qualsiasi altra VM da rimuovere.

7. Al termine della modifica, salvare la ricerca salvata. 

## <a name="next-steps"></a>Passaggi successivi

* Per continuare a usare Gestione aggiornamenti, vedere [Gestire gli aggiornamenti e le patch per le macchine virtuali di Azure](automation-tutorial-update-management.md).
* Per risolvere i problemi generali relativi alla funzionalità, vedere [Risolvere i problemi di Gestione aggiornamenti](troubleshoot/update-management.md).
* Per informazioni sui problemi dell'agente di Windows Update, vedere [Risolvere i problemi dell'agente di Windows Update](troubleshoot/update-agent-issues.md).
* Per informazioni sui problemi dell'agente di aggiornamento Linux, vedere [Risolvere i problemi dell'agente di aggiornamento Linux](troubleshoot/update-agent-issues-linux.md).