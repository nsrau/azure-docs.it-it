---
title: Esperienza di log semplici in Monitoraggio di Azure (anteprima)Simple Logs experience in Azure Monitor (Preview) Documenti Microsoft
description: L'esperienza di log semplici consente di creare query di base in Monitoraggio di Azure senza interagire direttamente con KQL.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/12/2019
ms.openlocfilehash: 59bcb42edaf7d46498a3514b4f1c919c6e8cc0c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77660258"
---
# <a name="simple-logs-experience-in-azure-monitor-preview"></a>Esperienza di log semplici in Monitoraggio di Azure (anteprima)Simple Logs experience in Azure Monitor (Preview)
Monitoraggio di Azure offre [un'esperienza avanzata](get-started-portal.md) per la creazione di query di [log](log-query-overview.md) usando il linguaggio KQL. Non è possibile richiedere tutta la potenza di KQL e preferire un'esperienza semplificata per i requisiti di query di base. L'esperienza Simple Logs consente di creare query di base senza interagire direttamente con KQL. È inoltre possibile utilizzare Simple Logs come strumento di apprendimento per KQL in quanto sono necessarie query più sofisticate.

> [!NOTE]
> Simple Logs è attualmente implementato come test solo per Cosmos DB e Key Vaults. Condividi la tua esperienza con Microsoft tramite [User Voice](https://feedback.azure.com/forums/913690-azure-monitor) per aiutarci a determinare se espanderemo e rilasceremo questa funzionalità.


## <a name="scope"></a>Scope
L'esperienza di Log semplici recupera i dati dalla tabella *AzureDiagnostics*, *AzureMetrics*e *AzureActivity* per la risorsa selezionata. 

## <a name="using-simple-logs"></a>Utilizzo di registri semplici
Passare a qualsiasi database Cosmos o a Un insieme di credenziali delle chiavi nella sottoscrizione di Azure con [le impostazioni di diagnostica configurate per raccogliere i log in un'area](../platform/resource-logs-collect-storage.md)di lavoro di Log Analytics. Fare clic su **Registri** nel menu **Monitoraggio** per aprire l'esperienza Di registri semplici.

![Menu](media/simple-logs/menu.png)

Selezionare un **campo** e un **operatore** e specificare un **valore** per il confronto. Fare **+** clic su e specificare **And/O** per aggiungere ulteriori criteri.

![Criteri](media/simple-logs/criteria.png)

Fare clic su **Esegui** per visualizzare i risultati della query.

## <a name="view-and-edit-kql"></a>Visualizzare e modificare KQL
Selezionare **Editor di query** per aprire il KQL generato dalla query Log semplici nell'esperienza completa di Log Analytics. 

![Editor di query](media/simple-logs/query-editor.png)

Puoi modificare direttamente il KQL e utilizzare altre funzionalità in Log Analytics, ad esempio i filtri, per perfezionare ulteriormente i risultati.

![Modifica KQL](media/simple-logs/edit-kql.png)


## <a name="next-steps"></a>Passaggi successivi

- Completare un'esercitazione [sull'uso](get-started-portal.md)di Log Analytics nel portale di Azure.Complete a tutorial on using Log Analytics in the Azure portal .
- Completare un'esercitazione sulla [scrittura di query](get-started-portal.md)di log .
