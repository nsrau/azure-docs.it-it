---
title: Visualizza Esplora dati di Azure con il Ritrattino
description: Questo articolo illustra come visualizzare i dati in Esplora dati di Azure con il connettore nativo Dash.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 8bb8711bc4a6134ec740a55d9f5d5794b2de77ca
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73588590"
---
# <a name="visualize-data-from-azure-data-explorer-in-redash"></a>Visualizza i dati da Esplora dati di Azure in Dash

Il [Ritrattino](https://www.redash.io/) connette ed esegue query sulle origini dati, compila i dashboard per visualizzare i dati e condividerli con i peer. Questo articolo illustra come configurare Esplora dati di Azure come origine dati per il Ritrattino e quindi visualizzare i dati.

## <a name="prerequisites"></a>Prerequisiti

1. [Creare un cluster e un database](create-cluster-database-portal.md).
1. Inserire i dati come illustrato in [inserire i dati di esempio in Esplora dati di Azure](ingest-sample-data.md). Per altre opzioni di inserimento, vedere [Cenni preliminari sull'](ingest-data-overview.md)inserimento.

[!INCLUDE [data-explorer-configure-data-source](../../includes/data-explorer-configure-data-source.md)]

## <a name="create-azure-data-explorer-connector-in-redash"></a>Creare un connettore Azure Esplora dati in Dash 

1. Accedere per [ritrattere](https://www.redash.io/). Selezionare **inizia a creare** un account.
1. In **Introduzione**selezionare **Connetti un'origine dati**.

    ![Connettere un'origine dati](media/redash/connect-data-source.png)

1. Nella finestra **Crea una nuova origine dati** selezionare **Azure Esplora dati (kusto)** , quindi selezionare **Crea**. 

    ![Selezionare l'origine dati Esplora dati di Azure](media/redash/select-adx-data-source.png)

1. Nella finestra di **Esplora dati di Azure (kusto)** completare il modulo seguente e selezionare **Crea**.

    ![Finestra impostazioni di Azure Esplora dati (kusto)](media/redash/adx-settings-window.png)

1. Nella finestra **Impostazioni** selezionare **Salva** e **Test connessione** per testare la connessione all'origine dati di **Azure Esplora dati (kusto)** .

## <a name="create-queries-in-redash"></a>Crea query in Dash

1. Nella parte superiore sinistra di Ritrattino selezionare **crea** > **query**. Fare clic su **nuova query** e rinominare la query.

    ![Crea query](media/redash/create-query.png)

1. Digitare la query nel riquadro di modifica superiore e selezionare **Salva** ed **Esegui**. Selezionare **pubblica** per pubblicare la query per un uso futuro.

    ![Salva ed Esegui query](media/redash/save-and-execute-query.png)

    Nel riquadro sinistro è possibile visualizzare il nome della connessione all'origine dati (**connettore GitHub** nel flusso) nel menu a discesa e le tabelle del database selezionato. 

1. Visualizzare i risultati della query nel riquadro centrale inferiore. Creare una visualizzazione da usare con la query selezionando il pulsante **nuova visualizzazione** .

    ![Nuova visualizzazione](media/redash/new-visualization.png)

1. Nella schermata di visualizzazione selezionare il tipo di **visualizzazione** e i campi pertinenti, ad esempio **colonna X** e **colonna Y**. **Salvare** la visualizzazione.

    ![Configurare e salvare la visualizzazione](media/redash/configure-visualization.png)

### <a name="create-a-query-using-a-parameter"></a>Creare una query usando un parametro

1. **Creare** > **query** per creare una nuova query. Aggiungere un parametro usando le parentesi graffe {{}}. Selezionare **{{}}** per aprire la finestra **Aggiungi parametro** . È anche possibile selezionare l' *icona Impostazioni* per modificare gli attributi di un parametro esistente e aprire la finestra **< parameter_name >** . 

    ![Inserisci parametro](media/redash/insert-parameter.png)

1. Assegnare un nome al parametro. Selezionare **tipo**: **elenco a discesa basato su query** dal menu a discesa. Selezionare **OK**.

    ![elenco a discesa basato su query](media/redash/query-based-dropdown-list.png)

    > [!NOTE]
    > La query utilizza più valori, pertanto è necessario includere la sintassi seguente `| where Type in ((split('{{Type}}', ',')))`. Per ulteriori informazioni, vedere l' [operatore in](/azure/kusto/query/inoperator). Questo comporta [più opzioni per i parametri di query nell'app Dash](https://redash.io/help/user-guide/querying/query-parameters#Serialized-Multi-Select-Query-Parametersredash.io)

## <a name="create-a-dashboard-in-redash"></a>Creare un dashboard in Dash

1. Per creare il dashboard, **creare** > **Dashboard**. In alternativa, selezionare dashboard esistente, **dashboard** > selezionare un dashboard dall'elenco.

    ![Creare un dashboard](media/redash/create-dashboard.png)

1. Nella finestra **nuovo dashboard** assegnare un nome al dashboard e selezionare **Salva**. In **< finestra di > Dashboard_name** selezionare **Aggiungi widget** per creare un nuovo widget. 

1. Nella finestra **Aggiungi widget** selezionare nome query, **scegliere visualizzazione**e **parametri**. Selezionare **Aggiungi al dashboard**

   ![Scegliere visualizzazioni e Aggiungi al dashboard](media/redash/add-widget-window.png)

1. Selezionare **modifica** completata per completare la creazione del dashboard.

1.  Nella modalità di modifica del Dashboard selezionare **Usa filtri a livello di dashboard** per usare il parametro di **tipo** definito in precedenza.

    ![Creazione Dashboard completa](media/redash/complete-dashboard.png)

## <a name="next-steps"></a>Passaggi successivi

* [Scrivere query per Esplora dati di Azure](write-queries.md)


