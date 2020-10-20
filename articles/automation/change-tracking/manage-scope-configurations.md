---
title: Limitare il Rilevamento modifiche di automazione di Azure e l'ambito di distribuzione dell'inventario
description: Questo articolo descrive come usare le configurazioni dell'ambito per limitare l'ambito di una distribuzione di Rilevamento modifiche e inventario.
services: automation
ms.subservice: change-inventory-management
ms.date: 10/14/2020
ms.topic: conceptual
ms.openlocfilehash: 6eb9a20920f0a340491459f0875fc85b90dfa193
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92209988"
---
# <a name="limit-change-tracking-and-inventory-deployment-scope"></a>Limitare Rilevamento modifiche e l'ambito di distribuzione dell'inventario

Questo articolo descrive come usare le configurazioni dell'ambito quando si usa il [rilevamento modifiche e](overview.md) la funzionalità di inventario per distribuire le modifiche alle macchine virtuali. Per altre informazioni, vedere [targeting Solutions Monitoring in monitoraggio di Azure (anteprima)](../../azure-monitor/insights/solution-targeting.md).

## <a name="about-scope-configurations"></a>Informazioni sulle configurazioni dell'ambito

Una configurazione dell'ambito è un gruppo di una o più ricerche salvate (query) utilizzate per limitare l'ambito di Rilevamento modifiche e inventario a computer specifici. La configurazione dell'ambito viene utilizzata all'interno dell'area di lavoro Log Analytics per consentire ai computer di abilitare. Quando si aggiunge un computer alle modifiche della funzionalità, il computer viene aggiunto anche a una ricerca salvata nell'area di lavoro.

## <a name="set-the-scope-limit"></a>Imposta il limite dell'ambito

Per limitare l'ambito per la distribuzione di Rilevamento modifiche e inventario:

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Nel portale di Azure fare clic su **Tutti i servizi**. Nell'elenco delle risorse digitare **Automazione**. Quando si inizia a digitare, l'elenco Filtra i suggerimenti in base all'input. Selezionare **Account di automazione**.

3. Nell'elenco degli account di automazione selezionare l'account scelto quando è stato abilitato il Rilevamento modifiche e l'inventario.

4. Nell'account di automazione selezionare **area di lavoro collegata** in **risorse correlate**.

5. Fare clic su **Vai all'area di lavoro**.

6. Selezionare **configurazioni ambito (anteprima)** in **origini dati dell'area di lavoro**.

7. Selezionare i puntini di sospensione a destra della  `MicrosoftDefaultScopeConfig-ChangeTracking` configurazione dell'ambito, quindi fare clic su **modifica**.

8. Nel riquadro di modifica scegliere **Selezionare i gruppi di computer**. Il riquadro Gruppi di computer mostra le ricerche salvate usate per creare la configurazione dell'ambito. La ricerca salvata utilizzata da Rilevamento modifiche e dall'inventario è la seguente:

    |Nome     |Category  |Alias  |
    |---------|---------|---------|
    |MicrosoftDefaultComputerGroup     |  ChangeTracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |

9. Selezionare la ricerca salvata per visualizzare e modificare la query utilizzata per popolare il gruppo. L'immagine seguente mostra la query e i relativi risultati:

    ![Ricerche salvate](media/manage-scope-configurations/logsearch.png)

## <a name="next-steps"></a>Passaggi successivi

* Per usare Rilevamento modifiche e inventario, vedere [Gestire Rilevamento modifiche e inventario](manage-change-tracking.md).
* Per risolvere i problemi generali relativi alla funzionalità, vedere [Risolvere i problemi relativi a Rilevamento modifiche e inventario](../troubleshoot/change-tracking.md).
