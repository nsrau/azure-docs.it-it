---
title: Visualizzare i dati con il connettore di Azure Data Explorer per Power BI
description: 'In questo articolo viene illustrato come usare una delle tre opzioni per la visualizzazione dei dati in Power BI: il connettore di Power BI per Azure Data Explorer.'
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: a95d45481bed17e46429e3a22dff4b8cc62354a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77560491"
---
# <a name="visualize-data-using-the-azure-data-explorer-connector-for-power-bi"></a>Visualizzare i dati con il connettore Esplora dati di Azure per Power BI

Esplora dati di Azure è un servizio di esplorazione dati rapido e a scalabilità elevata per dati di log e di telemetria. Power BI è una soluzione di analisi aziendale che consente di visualizzare i dati e condividere i risultati all'interno dell'organizzazione. Esplora dati di Azure fornisce tre opzioni per la connessione ai dati in Power BI: usare il connettore predefinito, importare una query da Esplora dati di Azure o usare una query SQL. Questo articolo illustra come usare il connettore incorporato per ottenere dati e visualizzarli in un report di Power BI. L'uso del connettore nativo di Azure Data Explorer per la creazione di dashboard di Power BI è semplice. Il connettore Power BI supporta le modalità di [connettività di importazione e query diretta.](https://docs.microsoft.com/power-bi/desktop-directquery-about) È possibile creare dashboard usando la modalità **Import** o **DirectQuery** a seconda dello scenario, della scalabilità e dei requisiti di prestazioni. 

## <a name="prerequisites"></a>Prerequisiti

Per completare questo articolo, è necessario quanto segue:

* Se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* Un account di posta elettronica dell'organizzazione membro di Azure Active Directory, in modo da potersi connettere al [cluster della guida di Esplora dati di Azure](https://dataexplorer.azure.com/clusters/help/databases/samples).
* [Power BI Desktop](https://powerbi.microsoft.com/get-started/) (selezionare **SCARICA GRATUITAMENTE**)

## <a name="get-data-from-azure-data-explorer"></a>Ottenere dati da Esplora dati di Azure

In primo luogo si effettua la connessione al cluster della guida di Esplora dati di Azure e quindi si importa un subset dei dati dalla tabella *StormEvents*. [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

1. Nella scheda **Home** di Power BI Desktop selezionare **Recupera dati** e quindi **Altro**.

    ![Recuperare i dati](media/power-bi-connector/get-data-more.png)

1. Cercare *Azure Data Explorer*, selezionare **Azure Data Explorer,** quindi **Connetti**.

    ![Cercare e ottenere i dati](media/power-bi-connector/search-get-data.png)

1. Nella schermata **Azure Data Explorer (Kusto)** compilare il modulo con le informazioni seguenti.

    ![Opzioni di cluster, database e tabelle](media/power-bi-connector/cluster-database-table.png)

    **Impostazione** | **Valore** | **Descrizione campo**
    |---|---|---|
    | Cluster | *https://help.kusto.windows.net* | L'URL del cluster della guida. Per altri cluster, l'URL è nel formato *https://\<NomeCluster\>.\< Regione\>.kusto.windows.net .* |
    | Database | Lasciare vuoto | Un database ospitato nel cluster con cui si effettua la connessione. Questo verrà selezionato in un passaggio successivo. |
    | Nome tabella | Lasciare vuoto | Una delle tabelle nel database o una query come <code>StormEvents \| take 1000</code>. Questo verrà selezionato in un passaggio successivo. |
    | Advanced Options | Lasciare vuoto | Opzioni per le query, ad esempio dimensioni del set di risultati. |
    | Modalità connettività dati | *DirectQuery* | Determina se Power BI Importa i dati o si connette direttamente all'origine dati. È possibile usare l'una o l'altra opzione con questo connettore. |
    | | | |
    
    > [!NOTE]
    > In modalità **di importazione** i dati vengono spostati in Power BI. In modalità **DirectQuery,** i dati vengono sottoposti a query direttamente dal cluster Azure Data Explorer.In DirectQuery mode, data is queryed directly from your Azure Data Explorer cluster.
    >
    > Utilizzare la modalità **di importazione** quando:
    > * Il set di dati è piccolo.
    > * Non sono necessari dati quasi in tempo reale. 
    > * I dati sono già aggregati o si esegue [l'aggregazione in Kusto](/azure/kusto/query/summarizeoperator#list-of-aggregation-functions)    
    >
    > Usare la modalità DirectQuery quando:Use **DirectQuery** mode when:
    > * Il set di dati è molto grande. 
    > * Hai bisogno di dati quasi in tempo reale.   

1. Se non si ha già una connessione al cluster della guida, accedere. Accedere con un account aziendale e quindi selezionare **Connetti**.

    ![Accesso](media/power-bi-connector/sign-in.png)

1. Nella schermata **Strumento di navigazione** espandere il database **Samples**, selezionare **StormEvents** e quindi **Modifica**.

    ![Selezionare una tabella](media/power-bi-connector/select-table.png)

    La tabella verrà aperta nell'Editor di Power Query, in cui è possibile modificare righe e colonne prima di importare i dati.

1. Nell'Editor di Power Query selezionare la freccia accanto alla colonna **DamageCrops** e quindi selezionare **Ordinamento decrescente**.

    ![Ordinare DamageCrops in senso decrescente](media/power-bi-connector/sort-descending.png)

1. Nella scheda **Home** selezionare **Mantieni righe** e quindi **Mantieni prime righe**. Immettere il valore *1000* per recuperare le prime 1000 righe della tabella ordinata.

    ![Mantieni prime righe](media/power-bi-connector/keep-top-rows.png)

1. Nella scheda **Home** selezionare **Chiudi e applica**.

    ![Chiudere e applicare](media/power-bi-connector/close-apply.png)

## <a name="visualize-data-in-a-report"></a>Visualizzare i dati in un report

[!INCLUDE [data-explorer-power-bi-visualize-basic](../../includes/data-explorer-power-bi-visualize-basic.md)]

## <a name="clean-up-resources"></a>Pulire le risorse

Se il report creato per questo articolo non è più necessario, eliminare il file di Power BI Desktop (con estensione pbix).

## <a name="next-steps"></a>Passaggi successivi

[Suggerimenti per l'uso del connettore di Azure Data Explorer per Power BI per eseguire query sui datiTips for using the Azure Data Explorer connector for Power BI to query data](power-bi-best-practices.md#tips-for-using-the-azure-data-explorer-connector-for-power-bi-to-query-data)
