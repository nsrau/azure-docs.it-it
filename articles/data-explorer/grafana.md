---
title: Visualizzare i dati da Esplora dati di Azure con Grafana
description: In questa procedura viene descritto come configurare Esplora dati di Azure come origine dati per Grafana e quindi come visualizzare i dati da un cluster di esempio.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 6/30/2019
ms.openlocfilehash: f1eb9fb0d81d1e9cdf3dd8628a6d7ad1f0ccce92
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73581927"
---
# <a name="visualize-data-from-azure-data-explorer-in-grafana"></a>Visualizzare i dati da Esplora dati di Azure in Grafana

Grafana è una piattaforma di analisi che consente di visualizzare i dati ed eseguirvi query, quindi di creare e condividere dashboard basati sulle visualizzazioni. Grafana fornisce un *plugin* di Esplora dati di Azure che consente di connettersi ai dati e di visualizzarli da Esplora dati di Azure. In questo articolo viene descritto come configurare Esplora dati di Azure come origine dati per Grafana e quindi come visualizzare i dati da un cluster di esempio.

Usando il video seguente, è possibile imparare a usare il plug-in Azure Esplora dati di Grafana, configurare Esplora dati di Azure come origine dati per Grafana e quindi visualizzare i dati. 

> [!VIDEO https://www.youtube.com/embed/fSR_qCIFZSA]

In alternativa, è possibile [configurare l'origine dati](#configure-the-data-source) e [visualizzare i dati](#visualize-data) come descritto in dettaglio nell'articolo seguente.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa procedura, sono necessari gli elementi seguenti:

* [Grafana versione 5.3.0 o versioni successive](https://docs.grafana.org/installation/) per il sistema operativo usato

* Il [plug-in di Esplora dati di Azure](https://grafana.com/plugins/grafana-azure-data-explorer-datasource/installation) per Grafana

* Un cluster che include i dati di esempio StormEvents. Per altre informazioni, vedere [Quickstart: Create an Azure Data Explorer cluster and database](create-cluster-database-portal.md) (Guida introduttiva: creare un cluster e un database di Esplora dati di Azure) e [Inserire i dati di esempio in Esplora dati di Azure](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

[!INCLUDE [data-explorer-configure-data-source](../../includes/data-explorer-configure-data-source.md)]

### <a name="specify-properties-and-test-the-connection"></a>Specificare le proprietà e testare la connessione

Con l'entità servizio assegnata al ruolo di *visualizzatori* è ora possibile specificare le proprietà nell'istanza di Grafana e testare la connessione a Esplora dati di Azure.

1. In Grafana selezionare l'icona a forma di ingranaggio nel menu a sinistra e quindi **Origini dati**.

    ![Origini dati](media/grafana/data-sources.png)

1. Selezionare **Aggiungi origine dati**.

1. Nella pagina **Origini dati / Nuova** immettere un nome per l'origine dati e quindi selezionare il tipo **Origine dati di Esplora dati di Azure**.

    ![Nome e tipo della connessione](media/grafana/connection-name-type.png)

1. Immettere il nome del cluster nel formato https://{ClusterName}.{Region}.kusto.windows.net. Immettere gli altri valori nel portale di Azure o nell'interfaccia della riga di comando. Vedere la tabella sotto l'immagine seguente per un mapping.

    ![Connection properties (Proprietà connessione)](media/grafana/connection-properties.png)

    | Interfaccia utente Grafana | Portale di Azure | Interfaccia della riga di comando di Azure |
    | --- | --- | --- |
    | ID sottoscrizione | ID SOTTOSCRIZIONE | SubscriptionId |
    | ID tenant | ID directory | tenant |
    | ID client | ID applicazione | appId |
    | Segreto client | Password | password |
    | | | |

1. Selezionare **Salva e verifica**.

    Se il test ha esito positivo, passare alla sezione successiva. Se si verificano problemi, controllare i valori specificati in Grafana e rivedere i passaggi precedenti.

## <a name="visualize-data"></a>Visualizzare i dati

Dopo aver completato la configurazione di Esplora dati di Azure come origine dati per Grafana, è possibile visualizzare i dati. In questo articolo verrà mostrato un esempio di base, ma è possibile fare molto di più. È consigliabile vedere [Scrivere query per Esplora dati di Azure](write-queries.md) per alcuni esempi di altre query da eseguire sul set di dati di esempio.

1. In Grafana selezionare l'icona con il segno più nel menu a sinistra e quindi **Dashboard**.

    ![Creare un dashboard](media/grafana/create-dashboard.png)

1. Nella scheda **Aggiungi** selezionare **Grafico**.

    ![Aggiungere un grafico](media/grafana/add-graph.png)

1. Nel pannello del grafico selezionare **Titolo pannello** e quindi **Modifica**.

    ![Modificare il pannello](media/grafana/edit-panel.png)

1. Nella parte inferiore del pannello selezionare **Origine dati** e quindi selezionare l'origine dati configurata.

    ![Selezionare l'origine dati](media/grafana/select-data-source.png)

1. Nel riquadro di query copiare la query seguente e quindi selezionare **Esegui**. La query indica il numero di eventi al giorno per il set di dati di esempio.

    ```kusto
    StormEvents
    | summarize event_count=count() by bin(StartTime, 1d)
    ```

    ![Esegui query](media/grafana/run-query.png)

1. Il grafico non mostra alcun risultato perché per impostazione predefinita vengono visualizzati i dati delle ultime sei ore. Nel menu in alto selezionare **Ultime 6 ore**.

    ![Ultime sei ore](media/grafana/last-six-hours.png)

1. Specificare un intervallo personalizzato che copre il 2007, l'anno incluso nel set di dati di esempio StormEvents. Selezionare **Applica**.

    ![Intervallo di date personalizzato](media/grafana/custom-date-range.png)

    Ora il grafico mostra i dati del 2007, suddivisi per giorno.

    ![Grafico completato](media/grafana/finished-graph.png)

1. Nel menu in alto selezionare l'icona di salvataggio: ![Icona Salva](media/grafana/save-icon.png).

## <a name="next-steps"></a>Passaggi successivi

* [Scrivere query per Esplora dati di Azure](write-queries.md)

* [Esercitazione: visualizzare i dati da Esplora dati di Azure in Power BI](visualize-power-bi.md)
