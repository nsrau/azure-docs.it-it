---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 11/03/2019
ms.author: orspodek
ms.openlocfilehash: f8b3e541dfd55bbd613af2791329a08402cf8670
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73581870"
---
## <a name="configure-the-data-source"></a>Configurare l'origine dati

Per configurare Esplora dati di Azure come origine dati per lo strumento dashboard, attenersi alla procedura riportata di seguito. In questa sezione verranno illustrati in dettaglio questi passaggi:

1. Creare un'entità servizio di Azure Active Directory (Azure AD). L'entità servizio viene usata dallo strumento dashboard per accedere al servizio Esplora dati di Azure.

1. Aggiungere l'entità servizio di Azure AD al ruolo di *visualizzatori* nel database di Esplora dati di Azure.

1. Specificare le proprietà di connessione dello strumento dashboard in base alle informazioni provenienti dall'entità servizio Azure AD, quindi testare la connessione.

### <a name="create-a-service-principal"></a>Creare un'entità servizio

È possibile creare l'entità servizio nel [portale di Azure](#azure-portal) o tramite l'[interfaccia della riga di comando di Azure](#azure-cli). Indipendentemente dal metodo usato, dopo la creazione si ottengono i valori per quattro proprietà di connessione da usare nei passaggi successivi.

#### <a name="azure-portal"></a>Portale di Azure

1. Per creare l'entità servizio, seguire le istruzioni riportate nella [documentazione del portale di Azure](/azure/active-directory/develop/howto-create-service-principal-portal).

    1. Nella sezione [Assegnare l'applicazione a un ruolo](/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) assegnare il tipo di ruolo di **Lettore** al cluster di Esplora dati di Azure.

    1. Nella sezione [ottenere i valori per l'accesso](/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) , copiare i tre valori di proprietà descritti nei passaggi: **ID directory** (ID tenant), **ID applicazione**e **password**.

1. Nel portale di Azure selezionare **Sottoscrizioni** e quindi copiare l'ID della sottoscrizione in cui è stata creata l'entità servizio.

    ![ID sottoscrizione - Portale](media/data-explorer-configure-data-source/subscription-id-portal.png)

#### <a name="azure-cli"></a>Interfaccia della riga di comando di Azure

1. Creare un'entità servizio. Impostare un ambito appropriato e il tipo di ruolo di `reader`.

    ```azurecli
    az ad sp create-for-rbac --name "https://{UrlToYourDashboard}:{PortNumber}" --role "reader" \
                             --scopes /subscriptions/{SubID}/resourceGroups/{ResourceGroupName}
    ```

    Per altre informazioni, vedere [Creare un'entità servizio di Azure con l'interfaccia della riga di comando di Azure](/cli/azure/create-an-azure-service-principal-azure-cli).

1. Il comando restituisce un set di risultati simile al seguente. Copiare i valori delle tre proprietà: **appID**, **password** e **tenant**.


    ```json
    {
      "appId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
      "displayName": "{UrlToYourDashboard}:{PortNumber}",
      "name": "https://{UrlToYourDashboard}:{PortNumber}",
      "password": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
      "tenant": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
    }
    ```

1. Ottenere un elenco delle sottoscrizioni.

    ```azurecli
    az account list --output table
    ```

    Copiare l'ID sottoscrizione appropriato.

    ![ID sottoscrizione - Interfaccia della riga di comando](media/data-explorer-configure-data-source/subscription-id-cli.png)

### <a name="add-the-service-principal-to-the-viewers-role"></a>Aggiungere l'entità servizio al ruolo di visualizzatori

Dopo aver creato l'entità servizio, aggiungerla al ruolo di *visualizzatori* nel database di Esplora dati di Azure. È possibile eseguire questa attività in **Autorizzazioni** nel portale di Azure o in **Query** usando un comando di gestione.

#### <a name="azure-portal---permissions"></a>Portale di Azure - Autorizzazioni

1. Nel portale di Azure passare a cluster di Esplora dati di Azure.

1. Nella sezione **Panoramica** selezionare il database con i dati di esempio StormEvents.

    ![Selezionare il database](media/data-explorer-configure-data-source/select-database.png)

1. Selezionare **Autorizzazioni** quindi**Aggiungi**.

    ![Autorizzazioni per il database](media/data-explorer-configure-data-source/database-permissions.png)

1. In **Aggiungere autorizzazioni database** selezionare il ruolo **Visualizzatore** e quindi **Selezionare entità di sicurezza**.

    ![Aggiungere autorizzazioni database](media/data-explorer-configure-data-source/add-permission.png)

1. Cercare l'entità servizio creata. Selezionare l'entità di sicurezza e quindi **Seleziona**.

    ![Gestire le autorizzazioni nel portale di Azure](media/data-explorer-configure-data-source/new-principals.png)

1. Selezionare **Salva**.

    ![Gestire le autorizzazioni nel portale di Azure](media/data-explorer-configure-data-source/save-permission.png)

#### <a name="management-command---query"></a>Comando di gestione - Query

1. Nel portale di Azure passare a cluster di Esplora dati di Azure e selezionare **Query**.

    ![Query](media/data-explorer-configure-data-source/query.png)

1. Eseguire il comando seguente nella finestra di query. Usare l'ID applicazione e l'ID tenant nel portale di Azure o nell'interfaccia della riga di comando.

    ```kusto
    .add database {TestDatabase} viewers ('aadapp={ApplicationID};{TenantID}')
    ```

    Il comando restituisce un set di risultati simile al seguente. In questo esempio, la prima riga è per un utente esistente nel database e la seconda riga è per l'entità servizio appena aggiunta.

    ![Set di risultati](media/data-explorer-configure-data-source/result-set.png)
