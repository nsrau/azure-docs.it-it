---
title: Limitare l'ambito di distribuzione Gestione aggiornamenti di automazione di Azure
description: Questo articolo illustra come usare le configurazioni dell'ambito per limitare l'ambito di una distribuzione di Gestione aggiornamenti.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 8770762fa2d2ae6bc0584d75397829298a62e8c0
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86185892"
---
# <a name="limit-update-management-deployment-scope"></a>Limitare Gestione aggiornamenti ambito di distribuzione

Questo articolo descrive come usare le configurazioni dell'ambito quando si usa la funzionalità [Gestione aggiornamenti](automation-update-management.md) per distribuire aggiornamenti e patch nelle VM. Per altre informazioni, vedere [targeting Solutions Monitoring in monitoraggio di Azure (anteprima)](../azure-monitor/insights/solution-targeting.md). 

## <a name="about-scope-configurations"></a>Informazioni sulle configurazioni dell'ambito

Una configurazione dell'ambito è un gruppo di una o più ricerche salvate (query) utilizzate per limitare l'ambito di Gestione aggiornamenti a computer specifici. La configurazione dell'ambito viene utilizzata all'interno dell'area di lavoro Log Analytics per consentire ai computer di abilitare. Quando si aggiunge un computer per la ricezione di aggiornamenti da Gestione aggiornamenti, il computer viene aggiunto anche a una ricerca salvata nell'area di lavoro.

## <a name="set-the-scope-limit"></a>Imposta il limite dell'ambito

Per limitare l'ambito per la distribuzione di Gestione aggiornamenti:

1. Nell'account di automazione selezionare **area di lavoro collegata** in **risorse correlate**.

2. Fare clic su **Vai all'area di lavoro**.

3. Selezionare **configurazioni ambito (anteprima)** in **origini dati dell'area di lavoro**.

4. Selezionare i puntini di sospensione a destra della `MicrosoftDefaultScopeConfig-Updates` configurazione dell'ambito, quindi fare clic su **modifica**. 

5. Nel riquadro di modifica espandere **Seleziona gruppi di computer**. Il riquadro Gruppi di computer mostra le ricerche salvate usate per creare la configurazione dell'ambito. La ricerca salvata usata da Gestione aggiornamenti è la seguente:

    |Nome     |Category  |Alias  |
    |---------|---------|---------|
    |MicrosoftDefaultComputerGroup     | Aggiornamenti        | Updates__MicrosoftDefaultComputerGroup         |

6. Selezionare la ricerca salvata per visualizzare e modificare la query utilizzata per popolare il gruppo. L'immagine seguente mostra la query e i relativi risultati:

    ![Ricerche salvate](media/automation-scope-configurations-update-management/logsearch.png)

## <a name="next-steps"></a>Passaggi successivi

* Per usare la funzionalità, vedere [Gestire gli aggiornamenti e le patch per le macchine virtuali di Azure](automation-tutorial-update-management.md).
* Per risolvere gli errori della funzionalità, vedere [Risolvere i problemi relativi a Gestione aggiornamenti](troubleshoot/update-management.md).
* Per risolvere i problemi relativi all'agente di Windows Update, vedere [Risolvere i problemi dell'agente di Windows Update](troubleshoot/update-agent-issues.md).
* Per risolvere i problemi relativi all'agente di aggiornamento di Linux, vedere[Risolvere i problemi dell'agente di aggiornamento di Linux](troubleshoot/update-agent-issues-linux.md).
