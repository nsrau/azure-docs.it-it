---
title: Rimuovere macchine virtuali da Rilevamento modifiche e inventario di Automazione di Azure
description: Questo articolo descrive come rimuovere le macchine virtuali da Rilevamento modifiche e inventario.
services: automation
ms.topic: conceptual
ms.date: 05/10/2018
ms.custom: mvc
ms.openlocfilehash: 8e9b80c60c098e4daf247db07fa48baa322aa7f0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84169455"
---
# <a name="remove-vms-from-change-tracking-and-inventory"></a>Rimuovere macchine virtuali da Rilevamento modifiche e inventario

Al termine della distribuzione delle modifiche alle macchine virtuali nell'ambiente, è possibile rimuoverle dalla funzionalità [Rilevamento modifiche e inventario](change-tracking.md).

## <a name="to-remove-your-vms"></a>Per rimuovere le macchine virtuali

1. Nell'account di Automazione selezionare **Rilevamento modifiche** o **Inventario** in **Gestione della configurazione**.

2. Usare il comando seguente per identificare l'UUID di una macchina virtuale che si vuole rimuovere dalla gestione.

    ```azurecli
    az vm show -g MyResourceGroup -n MyVm -d
    ```

3. Nell'area di lavoro Log Analytics in **generale**accedere alle ricerche salvate per la configurazione dell'ambito `MicrosoftDefaultScopeConfig-ChangeTracking` .

4. Per la di ricerca salvata `MicrosoftDefaultComputerGroup`, fare clic sui puntini di sospensione a destra e selezionare **Modifica**. 

5. Rimuovere l'UUID per la macchina virtuale.

6. Ripetere i passaggi per qualsiasi altra VM da rimuovere.

7. Al termine della modifica, salvare la ricerca salvata. 

## <a name="next-steps"></a>Passaggi successivi

* Per continuare a usare Rilevamento modifiche e inventario, vedere [Gestire Rilevamento modifiche e inventario](change-tracking-file-contents.md).
* Per risolvere i problemi generali relativi alla funzionalità, vedere [Risolvere i problemi relativi a Rilevamento modifiche e inventario](troubleshoot/change-tracking.md).