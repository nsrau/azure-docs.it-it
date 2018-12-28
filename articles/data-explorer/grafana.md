---
title: Visualizzare i dati da Esplora dati di Azure in Grafana
description: In questa procedura viene descritto come configurare Esplora dati di Azure come origine dati per Grafana e quindi come visualizzare i dati da un cluster di esempio.
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/05/2018
ms.openlocfilehash: 5a9684605de9af1cd9006810d595ae846db01661
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2018
ms.locfileid: "52976773"
---
# <a name="visualize-data-from-azure-data-explorer-in-grafana"></a>Visualizzare i dati da Esplora dati di Azure in Grafana

Grafana è una piattaforma di analisi che consente di visualizzare i dati ed eseguirvi query, quindi di creare e condividere dashboard basati sulle visualizzazioni. Grafana fornisce un *plugin* di Esplora dati di Azure che consente di connettersi ai dati e di visualizzarli da Esplora dati di Azure. In questo articolo viene descritto come configurare Esplora dati di Azure come origine dati per Grafana e quindi come visualizzare i dati da un cluster di esempio.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa procedura, sono necessari gli elementi seguenti:

* [Grafana versione 5.3.0 o versioni successive](http://docs.grafana.org/installation/) per il sistema operativo usato

* Il [plug-in di Esplora dati di Azure](https://grafana.com/plugins/grafana-azure-data-explorer-datasource/installation) per Grafana

* Un cluster che include i dati di esempio StormEvents. Per altre informazioni, vedere [Guida introduttiva: Creare un database e un cluster di Esplora dati di Azure](create-cluster-database-portal.md) e [Inserire i dati di esempio in Esplora dati di Azure](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="configure-the-data-source"></a>Configurare l'origine dati

Per configurare Esplora dati di Azure come origine dati per Grafana, eseguire la procedura seguente. In questa sezione verranno illustrati in dettaglio questi passaggi:

1. Creare un'entità servizio di Azure Active Directory (Azure AD). L'entità servizio viene usata da Grafana per accedere al servizio Esplora dati di Azure.

1. Aggiungere l'entità servizio di Azure AD al ruolo di *visualizzatori* nel database di Esplora dati di Azure.

1. Specificare le proprietà di connessione di Grafana in base alle informazioni fornite dall'entità servizio di Azure AD e quindi testare la connessione.

### <a name="create-a-service-principal"></a>Creare un'entità servizio

È possibile creare l'entità servizio nel [portale di Azure](#azure-portal) o tramite l'[interfaccia della riga di comando di Azure](#azure-cli). Indipendentemente dal metodo usato, dopo la creazione si ottengono i valori per quattro proprietà di connessione da usare nei passaggi successivi.

#### <a name="azure-portal"></a>Portale di Azure

1. Per creare l'entità servizio, seguire le istruzioni riportate nella [documentazione del portale di Azure](/azure/active-directory/develop/howto-create-service-principal-portal).

    1. Nella sezione [Assegnare l'applicazione a un ruolo](/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) assegnare il tipo di ruolo di **Lettore** al cluster di Esplora dati di Azure.

    1. Nella sezione [Ottenere i valori per l'accesso](/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) copiare i valori delle tre proprietà illustrati nei passaggi: **ID directory** (ID tenant), **ID applicazione** e **Password**.

1. Nel portale di Azure selezionare **Sottoscrizioni** e quindi copiare l'ID della sottoscrizione in cui è stata creata l'entità servizio.

    ![ID sottoscrizione - Portale](media/grafana/subscription-id-portal.png)

#### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

1. Creare un'entità servizio. Impostare un ambito appropriato e il tipo di ruolo di `reader`.

    ```azurecli
    az ad sp create-for-rbac --name "https://{UrlToYourGrafana}:{PortNumber}" --role "reader" \
                             --scopes /subscriptions/{SubID}/resourceGroups/{ResourceGroupName}
    ```

    Per altre informazioni, vedere [Creare un'entità servizio di Azure con l'interfaccia della riga di comando di Azure](/cli/azure/create-an-azure-service-principal-azure-cli).

1. Il comando restituisce un set di risultati simile al seguente. Copiare i valori delle tre proprietà: **appID**, **password** e **tenant**.

    ```json
    {
      "appId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
      "displayName": "{UrlToYourGrafana}:{PortNumber}",
      "name": "https://{UrlToYourGrafana}:{PortNumber}",
      "password": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
      "tenant": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
    }
    ```

1. Ottenere un elenco delle sottoscrizioni.

    ```azurecli
    az account list --output table
    ```

    Copiare l'ID sottoscrizione appropriato.

    ![ID sottoscrizione - Interfaccia della riga di comando](media/grafana/subscription-id-cli.png)

### <a name="add-the-service-principal-to-the-viewers-role"></a>Aggiungere l'entità servizio al ruolo di visualizzatori

Dopo aver creato l'entità servizio, aggiungerla al ruolo di *visualizzatori* nel database di Esplora dati di Azure. È possibile eseguire questa attività in **Autorizzazioni** nel portale di Azure o in **Query** usando un comando di gestione.

#### <a name="azure-portal---permissions"></a>Portale di Azure - Autorizzazioni

1. Nel portale di Azure passare a cluster di Esplora dati di Azure.

1. Nella sezione **Panoramica** selezionare il database con i dati di esempio StormEvents.

    ![Selezionare il database](media/grafana/select-database.png)

1. Selezionare **Autorizzazioni** quindi**Aggiungi**.

    ![Autorizzazioni per il database](media/grafana/database-permissions.png)

1. In **Aggiungere autorizzazioni database** selezionare il ruolo **Visualizzatore** e quindi **Selezionare entità di sicurezza**.

    ![Aggiungere autorizzazioni database](media/grafana/add-permission.png)

1. Cercare l'entità servizio creata (nell'esempio **mb-grafana**). Selezionare l'entità di sicurezza e quindi **Seleziona**.

    ![Gestire le autorizzazioni nel portale di Azure](media/grafana/new-principals.png)

1. Selezionare **Salva**.

    ![Gestire le autorizzazioni nel portale di Azure](media/grafana/save-permission.png)

#### <a name="management-command---query"></a>Comando di gestione - Query

1. Nel portale di Azure passare a cluster di Esplora dati di Azure e selezionare **Query**.

    ![Query](media/grafana/query.png)

1. Eseguire il comando seguente nella finestra di query. Usare l'ID applicazione e l'ID tenant nel portale di Azure o nell'interfaccia della riga di comando.

    ```kusto
    .add database {TestDatabase} viewers ('aadapp={ApplicationID};{TenantID}')
    ```

    Il comando restituisce un set di risultati simile al seguente. In questo esempio, la prima riga è per un utente esistente nel database e la seconda riga è per l'entità servizio appena aggiunta.

    ![Set di risultati](media/grafana/result-set.png)

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
    | Client Id | ID applicazione | appId |
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

[Scrivere query per Esplora dati di Azure](write-queries.md)

[Esercitazione: Visualizzare i dati da Esplora dati di Azure in Power BI](visualize-power-bi.md)