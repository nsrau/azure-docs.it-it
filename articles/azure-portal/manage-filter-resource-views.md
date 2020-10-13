---
title: Visualizzare e filtrare le informazioni sulle risorse di Azure
description: Filtrare le informazioni e usare visualizzazioni diverse per comprendere meglio le risorse di Azure di cui si dispone.
author: mgblythe
ms.service: azure-portal
ms.topic: quickstart
ms.author: mblythe
ms.date: 09/11/2020
ms.openlocfilehash: 3b9783e7f452b38292c784d44ddb60672e150961
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "91650400"
---
# <a name="view-and-filter-azure-resource-information"></a>Visualizzare e filtrare le informazioni sulle risorse di Azure

Il portale di Azure consente di visualizzare informazioni dettagliate sulle risorse nelle varie sottoscrizioni di Azure. Questo articolo illustra come filtrare le informazioni e usare visualizzazioni diverse per comprendere meglio le risorse di cui si dispone.

L'articolo è incentrato sulla schermata **Tutte le risorse** illustrata nello screenshot seguente. Le schermate per i singoli tipi di risorse, ad esempio le macchine virtuali, hanno opzioni diverse, ad esempio l'avvio e l'arresto di una macchina virtuale.

:::image type="content" source="media/manage-filter-resource-views/all-resources.png" alt-text="Visualizzazione portale di Azure di tutte le risorse":::

## <a name="filter-resources"></a>Filtrare le risorse

Iniziare a esplorare **Tutte le risorse** usando i filtri per concentrarsi su un subset delle risorse. Lo screenshot seguente illustra l'applicazione di filtri ai gruppi di risorse, con la selezione di due dei sei gruppi di risorse in una sottoscrizione.

:::image type="content" source="media/manage-filter-resource-views/filter-resource-group.png" alt-text="Visualizzazione portale di Azure di tutte le risorse" in uno dei due gruppi di risorse già selezionati.

:::image type="content" source="media/manage-filter-resource-views/filter-simplewinvm.png" alt-text="Visualizzazione portale di Azure di tutte le risorse":::

Per cambiare le colonne incluse in una visualizzazione, selezionare **Manage view** (Gestisci visualizzazione) e quindi **Modifica colonne**.

:::image type="content" source="media/manage-filter-resource-views/edit-columns.png" alt-text="Visualizzazione portale di Azure di tutte le risorse":::

## <a name="save-use-and-delete-views"></a>Salvare, usare ed eliminare viste

È possibile salvare le visualizzazioni che includono i filtri e le colonne selezionati. Per salvare e usare una visualizzazione:

1. Selezionare **Manage view** (Gestisci visualizzazione) e quindi **Salva visualizzazione**.

1. Immettere un nome per la visualizzazione e quindi selezionare **OK**. La visualizzazione salvata ora viene visualizzata nel menu **Manage view** (Gestisci visualizzazione).

    :::image type="content" source="media/manage-filter-resource-views/simple-view.png" alt-text="Visualizzazione portale di Azure di tutte le risorse":::

1. Per usare una visualizzazione, passare da **Predefinita** a una delle visualizzazioni personalizzate per vedere come cambia l'elenco delle risorse visualizzate.

Per eliminare una visualizzazione:

1. Selezionare **Manage view** (Gestisci visualizzazione) e quindi **Esplora tutte le visualizzazioni**.

1. Nel riquadro **Saved views for "All resources"** (Visualizzazioni salvate per "Tutte le risorse") selezionare la visualizzazione e quindi selezionare l'icona **Elimina** ![icona Elimina visualizzazione](media/manage-filter-resource-views/icon-delete.png).

## <a name="summarize-resources-with-visuals"></a>Riepilogare le risorse con oggetti visivi

Le visualizzazioni esaminate finora sono state _visualizzazioni elenco_, ma sono presenti anche _visualizzazioni di riepilogo_ che includono oggetti visivi. È possibile salvare e usare queste visualizzazioni esattamente come le visualizzazioni elenco. I filtri vengono mantenuti tra i due tipi di visualizzazioni. Sono disponibili visualizzazioni standard, come la visualizzazione **Località** illustrata di seguito, oltre a visualizzazioni pertinenti per specifici servizi, come la visualizzazione **Stato** per le macchine virtuali.

:::image type="content" source="media/manage-filter-resource-views/summary-map.png" alt-text="Visualizzazione portale di Azure di tutte le risorse":::

Per salvare e usare una visualizzazione di riepilogo:

1. Scegliere **Visualizzazione Riepilogo** dal menu Visualizza.

    :::image type="content" source="media/manage-filter-resource-views/menu-summary-view.png" alt-text="Visualizzazione portale di Azure di tutte le risorse":::

1. La visualizzazione di riepilogo consente di riepilogare le informazioni in base a diversi attributi, tra cui **Posizione** e **Tipo**. Selezionare un'opzione **Riepiloga per** e un oggetto visivo appropriato. Lo screenshot seguente illustra il **riepilogo dei tipi** con un oggetto visivo **Grafico a barre**.

    :::image type="content" source="media/manage-filter-resource-views/type-summary-bar-chart.png" alt-text="Visualizzazione portale di Azure di tutte le risorse":::

1. Scegliere **Manage view** (Gestisci visualizzazione) e quindi **Salva** per salvare questa visualizzazione come la visualizzazione elenco.

1. Nella visualizzazione di riepilogo, in **Type summary** (Riepilogo dei tipi), selezionare una barra del grafico. Selezionando la barra, viene visualizzato un elenco filtrato fino a un tipo di risorsa.

    :::image type="content" source="media/manage-filter-resource-views/all-resources-filtered-type.png" alt-text="Visualizzazione portale di Azure di tutte le risorse":::

## <a name="run-queries-in-azure-resource-graph"></a>Eseguire query in Azure Resource Graph

Azure Resource Graph offre un'esplorazione efficace ed efficiente delle risorse con la possibilità di eseguire query su larga scala in un set di sottoscrizioni. La schermata **Tutte le risorse** nel portale di Azure include un collegamento per aprire una query di Resource Graph avente come ambito la visualizzazione filtrata corrente.

Per eseguire una query di Resource Graph:

1. Selezionare **Apri query**.

    :::image type="content" source="media/manage-filter-resource-views/open-query.png" alt-text="Visualizzazione portale di Azure di tutte le risorse":::

1. In **Azure Resource Graph Explorer** selezionare **Esegui query** per visualizzare i risultati.

    :::image type="content" source="media/manage-filter-resource-views/run-query.png" alt-text="Visualizzazione portale di Azure di tutte le risorse":::

    Per altre informazioni, vedere [Eseguire la prima query di Resource Graph con Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md).

## <a name="next-steps"></a>Passaggi successivi

[Panoramica del portale di Azure](azure-portal-overview.md)

[Creare e condividere dashboard nel portale di Azure](azure-portal-dashboards.md)
