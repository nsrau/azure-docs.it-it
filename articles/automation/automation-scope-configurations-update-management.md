---
title: Usare le configurazioni dell'ambito per Gestione aggiornamenti di Automazione di Azure
description: Questo articolo descrive come usare le configurazioni dell'ambito con Gestione aggiornamenti.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 23ec49f2d68cf376ef0beb118d8bf69ada7bc0de
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83832028"
---
# <a name="work-with-scope-configurations-for-update-management"></a>Usare le configurazioni dell'ambito per Gestione aggiornamenti

Questo articolo descrive come usare le configurazioni dell'ambito quando si usa la funzionalità [Gestione aggiornamenti](automation-update-management.md) nelle macchine virtuali. 

## <a name="sign-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo https://portal.azure.com.

## <a name="check-the-scope-configuration"></a><a name="scope-configuration"></a>Controllare la configurazione dell'ambito

Gestione aggiornamenti usa una configurazione dell'ambito all'interno dell'area di lavoro Log Analytics per individuare i computer nei quali abilitare la funzionalità. La configurazione dell'ambito è un gruppo di una o più ricerche salvate usate per limitare l'ambito della funzionalità a computer specifici. Per accedere alle configurazioni dell'ambito:

1. Nell'account di Automazione selezionare **Area di lavoro** in **Risorse correlate**. 

2. Scegliere l'area di lavoro in **Origini dati dell'area di lavoro** e selezionare **Configurazioni ambito**.

3. Se per l'area di lavoro selezionata non è ancora abilitata la funzionalità Gestione aggiornamenti, viene creata la configurazione dell'ambito `MicrosoftDefaultScopeConfig-Updates`. 

4. Se per l'area di lavoro selezionata è già abilitata la funzionalità, non verrà ridistribuita e la configurazione dell'ambito non verrà aggiunta. 

5. Selezionare i puntini di sospensione in una delle configurazioni dell'ambito, quindi fare clic su **Modifica**. 

6. Nel riquadro di modifica scegliere **Selezionare i gruppi di computer**. Il riquadro Gruppi di computer mostra le ricerche salvate usate per creare la configurazione dell'ambito.

## <a name="view-a-saved-search"></a>Visualizzare una ricerca salvata

Quando un computer viene aggiunto a Gestione aggiornamenti, viene aggiunto anche a una ricerca salvata nell'area di lavoro. La ricerca salvata è una query che contiene i computer di destinazione.

1. Passare all'area di lavoro Log Analytics e selezionare **Ricerche salvate** in **Generale**. La ricerca salvata usata da Gestione aggiornamenti è la seguente:

|Nome     |Category  |Alias  |
|---------|---------|---------|
|MicrosoftDefaultComputerGroup     | Aggiornamenti        | Updates__MicrosoftDefaultComputerGroup         |

2. Selezionare la ricerca salvata per visualizzare la query usata per popolare il gruppo. L'immagine seguente mostra la query e i relativi risultati:

    ![Ricerche salvate](media/automation-scope-configurations-update-management/logsearch.png)

## <a name="next-steps"></a>Passaggi successivi

* Per usare la funzionalità, vedere [Gestire gli aggiornamenti e le patch per le macchine virtuali di Azure](automation-tutorial-update-management.md).
* Per risolvere gli errori della funzionalità, vedere [Risolvere i problemi relativi a Gestione aggiornamenti](troubleshoot/update-management.md).
* Per risolvere i problemi relativi all'agente di Windows Update, vedere [Risolvere i problemi dell'agente di Windows Update](troubleshoot/update-agent-issues.md).
* Per risolvere i problemi relativi all'agente di aggiornamento di Linux, vedere[Risolvere i problemi dell'agente di aggiornamento di Linux](troubleshoot/update-agent-issues-linux.md).