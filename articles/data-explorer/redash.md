---
title: Visualizzare Azure Data Explorer con Redash
description: In questo articolo viene illustrato come visualizzare i dati in Esplora dati di Azure con il connettore nativo Redash.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 0380689ae6ca81e3f31a07f1e205c7773fdea8c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76773947"
---
# <a name="visualize-data-from-azure-data-explorer-in-redash"></a>Visualizzare i dati da Esplora dati di Azure in RedashVisualize data from Azure Data Explorer in Redash

[Redash](https://redash.io/) connette ed esegue query sulle origini dati, crea dashboard per visualizzare i dati e condividerli con i peer. In questo articolo viene illustrato come configurare Azure Data Explorer come origine dati per Redash e quindi visualizzare i dati.

## <a name="prerequisites"></a>Prerequisiti

1. [Creare cluster e database](create-cluster-database-portal.md).
1. Inserire i dati come spiegato in [ingest dati di esempio in Azure Data Explorer](ingest-sample-data.md). Per altre opzioni di inserimento, vedere [Panoramica dell'inserimento.](ingest-data-overview.md)

[!INCLUDE [data-explorer-configure-data-source](../../includes/data-explorer-configure-data-source.md)]

## <a name="create-azure-data-explorer-connector-in-redash"></a>Creare il connettore di Azure Data Explorer in RedashCreate Azure Data Explorer Connector in Redash 

1. Accedere a [Redash](https://www.redash.io/). Seleziona **Inizia** per creare un account.
1. In **Introduzione**selezionare **Connetti origine dati**.

    ![Connettere un'origine dati](media/redash/connect-data-source.png)

1. Nella finestra **Crea una nuova origine dati** selezionare Azure Data Explorer **(Kusto)**, quindi selezionare **Crea**. 

    ![Selezionare l'origine dati di Azure Data ExplorerSelect Azure Data Explorer data source](media/redash/select-adx-data-source.png)

1. Nella finestra Esplora dati di **Azure (Kusto)** completare il form seguente e selezionare **Crea**.

    ![Finestra delle impostazioni di Azure Data Explorer (Kusto)Azure Data Explorer (Kusto) settings window](media/redash/adx-settings-window.png)

1. Nella finestra **Impostazioni** selezionare **Salva** e **verifica connessione** per testare la connessione all'origine dati di Azure Data **Explorer (Kusto).**

## <a name="create-queries-in-redash"></a>Creare query in Redash

1. In alto a sinistra in Redash selezionare **Crea** > **query**. Fare clic su **Nuova query** e rinominare la query.

    ![Crea query](media/redash/create-query.png)

1. Digitare la query nel riquadro di modifica superiore e selezionare **Salva** ed **esegui**. Selezionare **Pubblica** per pubblicare la query per un utilizzo futuro.

    ![Salvare ed eseguire query](media/redash/save-and-execute-query.png)

    Nel riquadro sinistro è possibile visualizzare il nome della connessione all'origine dati (**Connettore Github** nel flusso) nel menu a discesa e le tabelle nel database selezionato. 

1. Visualizzare i risultati della query nel riquadro centrale inferiore. Creare una visualizzazione da utilizzare con la query selezionando il pulsante **Nuova visualizzazione.**

    ![Nuova visualizzazione](media/redash/new-visualization.png)

1. Nella schermata di visualizzazione, selezionare il **tipo di visualizzazione** e i campi pertinenti, ad esempio Colonna **X** e **Colonna Y**. **Salvare** la visualizzazione.

    ![Configurare e salvare la visualizzazione](media/redash/configure-visualization.png)

### <a name="create-a-query-using-a-parameter"></a>Creare una query utilizzando un parametro

1. **Crea** > **query** per creare una nuova query. Aggiungetevi un parametro usando le parentesi graffe di tipo{}.NET. Per aprire la finestra **Aggiungi parametro,** selezionare **il tasto{}.** È inoltre possibile selezionare *l'icona* delle impostazioni per modificare gli attributi di un parametro esistente e aprire la<parameter_name finestra **>.** 

    ![inserire parametro](media/redash/insert-parameter.png)

1. Assegnare un nome al parametro. Selezionare **Tipo**: **Elenco a discesa basato su query** dal menu a discesa. Seleziona **OK**.

    ![elenco a discesa basato su query](media/redash/query-based-dropdown-list.png)

    > [!NOTE]
    > La query utilizza più valori, pertanto `| where Type in ((split('{{Type}}', ',')))`è necessario includere la sintassi seguente. Per ulteriori informazioni, vedere [in operator .](/azure/kusto/query/inoperator) Il risultato è la creazione di più opzioni di parametri di [query nell'app redash](https://redash.io/help/user-guide/querying/query-parameters#Serialized-Multi-Select-Query-Parametersredash.io)

## <a name="create-a-dashboard-in-redash"></a>Creare una dashboard in RedashCreate a dashboard in Redash

1. Per creare il dashboard, **crea** > **dashboard**. In alternativa, selezionare Dashboard esistente, **Dashboard** > selezionare un dashboard dall'elenco.

    ![Creare un dashboard](media/redash/create-dashboard.png)

1. Nella finestra **Nuovo dashboard** assegnare un nome al dashboard e selezionare **Salva**. Nella **finestra>Dashboard_name<,** selezionare Aggiungi **widget** per creare un nuovo widget. 

1. Nella finestra **Aggiungi widget** selezionare nome query, **Scegli visualizzazione**e **Parametri**. Selezionare **Aggiungi al dashboard**

   ![Scegliere le visualizzazioni e aggiungerle al dashboard](media/redash/add-widget-window.png)

1. Selezionare **Fine modifica** per completare la creazione del dashboard.

1.  Nella modalità di modifica del dashboard, selezionare Usa filtri a **livello di dashboard** per utilizzare il parametro **Tipo** definito in precedenza.

    ![Creazione completa del dashboard](media/redash/complete-dashboard.png)

## <a name="next-steps"></a>Passaggi successivi

* [Scrivere query per Esplora dati di Azure](write-queries.md)


