---
title: Usare configurazioni dell'ambito per Rilevamento modifiche e inventario di Automazione di Azure
description: Questo articolo descrive come usare configurazioni dell'ambito con Rilevamento modifiche e inventario.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 4fac94cc2f8f378b7e9d8e9485baed6a0ffa838b
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83832164"
---
# <a name="work-with-scope-configurations-for-change-tracking-and-inventory"></a>Usare configurazioni dell'ambito per Rilevamento modifiche e inventario

Questo articolo descrive come usare configurazioni dell'ambito quando si abilita la funzionalità [Gestione aggiornamenti](automation-update-management.md) nelle macchine virtuali. 

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo https://portal.azure.com.

## <a name="check-the-scope-configuration"></a><a name="scope-configuration"></a>Controllare la configurazione dell'ambito

Gestione aggiornamenti usa una configurazione dell'ambito all'interno dell'area di lavoro Log Analytics per individuare i computer nei quali abilitare Gestione aggiornamenti. La configurazione dell'ambito è un gruppo di una o più ricerche salvate usate per limitare l'ambito della funzionalità a computer specifici. Per accedere alle configurazioni dell'ambito:

1. Nell'account di Automazione selezionare **Area di lavoro** in **Risorse correlate**. 

2. Scegliere l'area di lavoro in **Origini dati dell'area di lavoro** e selezionare **Configurazioni ambito**.

3. Se per l'area di lavoro selezionata non è ancora abilitata la funzionalità Gestione aggiornamenti, viene creata la configurazione dell'ambito `MicrosoftDefaultScopeConfig-ChangeTracking`. 

4. Se per l'area di lavoro selezionata è già abilitata la funzionalità, non verrà ridistribuita e la configurazione dell'ambito non verrà aggiunta. 

5. Selezionare i puntini di sospensione in una delle configurazioni dell'ambito, quindi fare clic su **Modifica**. 

6. Nel riquadro di modifica scegliere **Selezionare i gruppi di computer**. Il riquadro Gruppi di computer mostra le ricerche salvate usate per creare la configurazione dell'ambito.

## <a name="view-a-saved-search"></a>Visualizzare una ricerca salvata

Quando un computer viene aggiunto a Rilevamento modifiche e inventario, viene aggiunto anche a una ricerca salvata nell'area di lavoro. La ricerca salvata è una query che contiene i computer di destinazione.

1. Passare all'area di lavoro Log Analytics e selezionare **Ricerche salvate** in **Generale**. La ricerca salvata usata da Gestione aggiornamenti è la seguente:

    |Nome     |Category  |Alias  |
    |---------|---------|---------|
    |MicrosoftDefaultComputerGroup     |  ChangeTracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |

2. Selezionare la ricerca salvata per visualizzare la query usata per popolare il gruppo. L'immagine seguente mostra la query e i relativi risultati:

    ![Ricerche salvate](media/automation-scope-configurations-change-tracking/logsearch.png)

## <a name="next-steps"></a>Passaggi successivi

* Per usare Rilevamento modifiche e inventario, vedere [Gestire Rilevamento modifiche e inventario](change-tracking-file-contents.md).
* Per risolvere i problemi generali relativi alla funzionalità, vedere [Risolvere i problemi relativi a Rilevamento modifiche e inventario](troubleshoot/change-tracking.md).