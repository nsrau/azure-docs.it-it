---
title: Limitare l'ambito di distribuzione Gestione aggiornamenti di automazione di Azure
description: Questo articolo illustra come usare le configurazioni dell'ambito per limitare l'ambito di una distribuzione di Gestione aggiornamenti.
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 76063c479950d12985d5f3f52393f9bb0d5ecd8d
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92222539"
---
# <a name="limit-update-management-deployment-scope"></a>Limitare Gestione aggiornamenti ambito di distribuzione

Questo articolo descrive come usare le configurazioni dell'ambito quando si usa la funzionalità [Gestione aggiornamenti](overview.md) per distribuire aggiornamenti e patch nelle VM. Per altre informazioni, vedere [targeting Solutions Monitoring in monitoraggio di Azure (anteprima)](../../azure-monitor/insights/solution-targeting.md).

## <a name="about-scope-configurations"></a>Informazioni sulle configurazioni dell'ambito

Una configurazione dell'ambito è un gruppo di una o più ricerche salvate (query) utilizzate per limitare l'ambito di Gestione aggiornamenti a computer specifici. La configurazione dell'ambito viene utilizzata all'interno dell'area di lavoro Log Analytics per consentire ai computer di abilitare. Quando si aggiunge un computer per la ricezione di aggiornamenti da Gestione aggiornamenti, il computer viene aggiunto anche a una ricerca salvata nell'area di lavoro.

## <a name="set-the-scope-limit"></a>Imposta il limite dell'ambito

Per limitare l'ambito per la distribuzione di Gestione aggiornamenti:

1. Nell'account di automazione selezionare **area di lavoro collegata** in **risorse correlate**.

2. Selezionare **Vai all'area di lavoro**.

3. Selezionare **configurazioni ambito (anteprima)** in **origini dati dell'area di lavoro**.

4. Selezionare i puntini di sospensione a destra della  `MicrosoftDefaultScopeConfig-Updates` configurazione dell'ambito, quindi scegliere **modifica**.

5. Nel riquadro di modifica espandere **Seleziona gruppi di computer**. Il riquadro Gruppi di computer mostra le ricerche salvate usate per creare la configurazione dell'ambito. La ricerca salvata usata da Gestione aggiornamenti è la seguente:

    |Nome     |Category  |Alias  |
    |---------|---------|---------|
    |MicrosoftDefaultComputerGroup     | Aggiornamenti        | Updates__MicrosoftDefaultComputerGroup         |

6. Selezionare la ricerca salvata per visualizzare e modificare la query utilizzata per popolare il gruppo. L'immagine seguente mostra la query e i relativi risultati:

    [![Ricerche salvate](./media/scope-configuration/logsearch.png)](./media/scope-configuration/logsearch-expanded.png#lightbox)

## <a name="next-steps"></a>Passaggi successivi

È possibile [eseguire query sui log di monitoraggio di Azure](query-logs.md) per analizzare le valutazioni degli aggiornamenti, le distribuzioni e altre attività di gestione correlate. Include query predefinite che consentono di iniziare.
