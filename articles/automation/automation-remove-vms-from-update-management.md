---
title: Rimuovere VM da Gestione aggiornamenti di Automazione di Azure
description: Questo articolo descrive come rimuovere i computer gestiti con Gestione aggiornamenti.
services: automation
ms.topic: conceptual
ms.date: 06/30/2020
ms.custom: mvc
ms.openlocfilehash: 9745ddea1035f239a9ca65a073fb698a8f42c01f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85610048"
---
# <a name="remove-vms-from-update-management"></a>Rimuovere macchine virtuali da Gestione aggiornamenti

Dopo aver completato la distribuzione di aggiornamenti nelle VM dell'ambiente, è possibile rimuoverle dalla funzionalità [Gestione aggiornamenti](automation-update-management.md).

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

* Per continuare a usare Gestione aggiornamenti, vedere [Gestire gli aggiornamenti e le patch per le macchine virtuali di Azure](automation-tutorial-update-management.md).
* Per risolvere i problemi generali relativi alla funzionalità, vedere [Risolvere i problemi di Gestione aggiornamenti](troubleshoot/update-management.md).
* Per informazioni sui problemi dell'agente di Windows Update, vedere [Risolvere i problemi dell'agente di Windows Update](troubleshoot/update-agent-issues.md).
* Per informazioni sui problemi dell'agente di aggiornamento Linux, vedere [Risolvere i problemi dell'agente di aggiornamento Linux](troubleshoot/update-agent-issues-linux.md).