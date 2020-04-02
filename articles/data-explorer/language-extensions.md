---
title: Gestire le estensioni del linguaggio nel cluster di Azure Data Explorer.Manage language extensions in your Azure Data Explorer cluster.
description: Usare l'estensione del linguaggio per integrare altri linguaggi all'interno delle query KQL di Azure Data Explorer.Use language extension to integrate other languages within your Azure Data Explorer KQL queries.
author: orspod
ms.author: orspodek
ms.reviewer: orhasban
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 88c3047afebf3d31b50145c6df47776fdc1ddcd4
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80522472"
---
# <a name="manage-language-extensions-in-your-azure-data-explorer-cluster-preview"></a>Gestire le estensioni del linguaggio nel cluster di Azure Data Explorer (anteprima)Manage language extensions in your Azure Data Explorer cluster (Preview)

La funzionalità delle estensioni del linguaggio consente di usare i plug-in di estensione del linguaggio per integrare altri linguaggi nelle query KQL di Azure Data Explorer.The language extensions feature allows you to use language extension plugins to integrate other languages into your Azure Data Explorer KQL queries. Quando si esegue una funzione definita dall'utente (UDF) utilizzando uno script pertinente, lo script ottiene i dati tabulari come input e si prevede di produrre output tabulare. Il runtime del plug-in è ospitato in una [sandbox,](/azure/kusto/concepts/sandboxes)un ambiente isolato e sicuro, in esecuzione sui nodi del cluster. In questo articolo si gestisce il plug-in delle estensioni del linguaggio nel cluster Azure Data Explorer all'interno del portale di Azure.In this article, you manage the language extensions plugin in your Azure Data Explorer cluster within the Azure portal.

> [!NOTE]
> Azure Data Explorer language extensions that are currently supported are Python and R.

## <a name="prerequisites"></a>Prerequisiti

* Se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* Creare [un database e un cluster di Azure Data Explorer](create-cluster-database-portal.md).

## <a name="enable-language-extensions-on-your-cluster"></a>Abilitare le estensioni del linguaggio nel clusterEnable language extensions on your cluster

> [!WARNING]
> Rivedere le [limitazioni](#limitations) prima di abilitare un'estensione del linguaggio.

Eseguire la procedura seguente per abilitare le estensioni del linguaggio nel cluster:

1. Nel portale di Azure passare a cluster di Esplora dati di Azure. 
1. In **Impostazioni**selezionare **Configurazioni**. 
1. Nel riquadro **Configurazioni** selezionare **Attivato** per abilitare un'estensione del linguaggio.
1. Selezionare **Save** (Salva).
 
    ![estensione del linguaggio su](media/language-extensions/configurations-enable-extension.png)

> [!NOTE]
> L'abilitazione del processo di estensione del linguaggio può richiedere alcuni minuti. Durante questo periodo, il cluster verrà sospeso.
 
## <a name="run-language-extension-integrated-queries"></a>Eseguire query integrate nell'estensione del linguaggioRun language extension-integrated queries

* Scopri come [eseguire query KQL integrate in Python.](/azure/kusto/query/pythonplugin)
* Scopri come [eseguire query KQL integrate R.](/azure/kusto/query/rplugin) 

## <a name="disable-language-extensions-on-your-cluster"></a>Disabilitare le estensioni del linguaggio nel clusterDisable language extensions on your cluster

> [!NOTE]
> La disattivazione delle estensioni del linguaggio potrebbe richiedere alcuni minuti.

Eseguire la procedura seguente per disabilitare le estensioni del linguaggio nel cluster:

1. Nel portale di Azure passare a cluster di Esplora dati di Azure. 
1. In **Impostazioni**selezionare **Configurazioni**. 
1. Nel riquadro **Configurazioni** selezionare **Disattivato** per disabilitare un'estensione per il linguaggio.
1. Selezionare **Save** (Salva).

    ![Estensione del linguaggio disattivata](media/language-extensions/configurations-disable-extension.png)

## <a name="limitations"></a>Limitazioni

* La funzionalità delle estensioni del linguaggio non supporta la [crittografia del disco.](manage-cluster-security.md) 
* La sandbox di runtime delle estensioni del linguaggio alloca spazio su disco anche se non viene eseguita alcuna query nell'ambito della lingua pertinente.

