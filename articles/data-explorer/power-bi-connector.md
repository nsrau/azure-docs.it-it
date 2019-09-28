---
title: Visualizzare i dati con il connettore Esplora dati di Azure per Power BI
description: 'Questo articolo illustra come usare una delle tre opzioni disponibili per la visualizzazione dei dati in Power BI: connettore Power BI per Esplora dati di Azure.'
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: a2ec179321c5d9cb6e9627e397fcb6ae09dc82ed
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2019
ms.locfileid: "71349152"
---
# <a name="visualize-data-using-the-azure-data-explorer-connector-for-power-bi"></a>Visualizzare i dati con il connettore Esplora dati di Azure per Power BI

Esplora dati di Azure è un servizio di esplorazione dati rapido e a scalabilità elevata per dati di log e di telemetria. Power BI è una soluzione di analisi aziendale che consente di visualizzare i dati e condividere i risultati all'interno dell'organizzazione. Esplora dati di Azure fornisce tre opzioni per la connessione ai dati in Power BI: usare il connettore predefinito, importare una query da Esplora dati di Azure o usare una query SQL. Questo articolo illustra come usare il connettore incorporato per ottenere i dati e visualizzarli in un report Power BI. L'uso di Azure Esplora dati connettore nativo per la creazione di Power BI Dashboard è semplice. Il connettore Power BI supporta le [modalità di connettività di query e importazione diretta](https://docs.microsoft.com/power-bi/desktop-directquery-about). È possibile compilare dashboard usando la modalità **importazione** o **DirectQuery** , a seconda dei requisiti di scenario, scalabilità e prestazioni. 

## <a name="prerequisites"></a>Prerequisiti

Per completare questo articolo, è necessario quanto segue:

* Se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* Un account di posta elettronica dell'organizzazione membro di Azure Active Directory, in modo da potersi connettere al [cluster della guida di Esplora dati di Azure](https://dataexplorer.azure.com/clusters/help/databases/samples).
* [Power BI Desktop](https://powerbi.microsoft.com/get-started/) (selezionare **SCARICA GRATUITAMENTE**)

## <a name="get-data-from-azure-data-explorer"></a>Ottenere dati da Esplora dati di Azure

In primo luogo si effettua la connessione al cluster della guida di Esplora dati di Azure e quindi si importa un subset dei dati dalla tabella *StormEvents*. [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

1. Nella scheda **Home** di Power BI Desktop selezionare **Recupera dati** e quindi **Altro**.

    ![Ottenere i dati](media/power-bi-connector/get-data-more.png)

1. Cercare *azure Esplora dati*, selezionare **Azure Esplora dati** quindi **connettersi**.

    ![Cercare e ottenere i dati](media/power-bi-connector/search-get-data.png)

1. Nella schermata **Esplora dati di Azure (kusto)** compilare il modulo con le informazioni seguenti.

    ![Opzioni di cluster, database e tabelle](media/power-bi-connector/cluster-database-table.png)

    **Impostazione** | **Valore** | **Descrizione campo**
    |---|---|---|
    | Cluster | *https://help.kusto.windows.net* | L'URL del cluster della guida. Per altri cluster, l'URL è nel formato *https://\<ClusterName\>.\<Area\>.kusto.windows.net*. |
    | Database | Lasciare vuoto | Un database ospitato nel cluster con cui si effettua la connessione. Questo verrà selezionato in un passaggio successivo. |
    | Nome tabella | Lasciare vuoto | Una delle tabelle nel database o una query come <code>StormEvents \| take 1000</code>. Questo verrà selezionato in un passaggio successivo. |
    | Opzioni avanzate | Lasciare vuoto | Opzioni per le query, ad esempio dimensioni del set di risultati. |
    | Modalità connettività dati | *DirectQuery* | Determina se Power BI Importa i dati o si connette direttamente all'origine dati. È possibile usare l'una o l'altra opzione con questo connettore. |
    | | | |
    
    > [!NOTE]
    > In modalità di **importazione** i dati vengono spostati in Power bi. Nella modalità **DirectQuery** i dati vengono sottoposti a query direttamente dal cluster di Azure Esplora dati.
    >
    > Usare la modalità di **importazione** nei casi seguenti:
    > * Il set di dati è ridotto.
    > * Non sono necessari dati quasi in tempo reale. 
    > * I dati sono già aggregati o si esegue l' [aggregazione in kusto](/azure/kusto/query/summarizeoperator#list-of-aggregation-functions)    
    >
    > Usare la modalità **DirectQuery** nei casi seguenti:
    > * Il set di dati è di dimensioni molto elevate. 
    > * Sono necessari dati quasi in tempo reale.   

1. Se non si ha già una connessione al cluster della guida, accedere. Accedere con un account aziendale e quindi selezionare **Connetti**.

    ![Accesso](media/power-bi-connector/sign-in.png)

1. Nella schermata **Strumento di navigazione** espandere il database **Samples**, selezionare **StormEvents** e quindi **Modifica**.

    ![Selezionare la tabella](media/power-bi-connector/select-table.png)

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

Se il report creato per questo articolo non è più necessario, eliminare il file Power BI Desktop (pbix).

## <a name="next-steps"></a>Passaggi successivi

[Suggerimenti per l'uso di Azure Esplora dati Connector per Power BI per eseguire query sui dati](power-bi-best-practices.md#tips-for-using-the-azure-data-explorer-connector-for-power-bi-to-query-data)
