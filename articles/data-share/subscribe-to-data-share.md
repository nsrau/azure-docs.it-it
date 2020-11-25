---
title: 'Esercitazione: Accettare e ricevere dati - Condivisione dati di Azure'
description: 'Esercitazione: Accettare e ricevere dati con Condivisione dati di Azure'
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: tutorial
ms.date: 11/12/2020
ms.openlocfilehash: a225989f0670e9b62b00a35bac719c9357c8a130
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659608"
---
# <a name="tutorial-accept-and-receive-data-using-azure-data-share"></a>Esercitazione: Accettare e ricevere dati con Condivisione dati di Azure  

In questa esercitazione si apprenderà come accettare un invito alla condivisione dei dati usando Condivisione dati di Azure. Si apprenderà come ricevere i dati condivisi con l'utente, nonché come abilitare un intervallo di aggiornamento regolare per garantire di avere sempre lo snapshot più recente dei dati condivisi con l'utente. 

> [!div class="checklist"]
> * Come accettare un invito di Condivisione dati di Azure
> * Creare un account di Condivisione dati di Azure
> * Specificare una destinazione per i dati
> * Creare una sottoscrizione per la condivisione dei dati per l'aggiornamento pianificato

## <a name="prerequisites"></a>Prerequisiti
Prima di accettare un invito alla condivisione dei dati, è necessario effettuare il provisioning di una serie di risorse di Azure, elencate di seguito. 

Assicurarsi che tutti i prerequisiti siano soddisfatti prima di accettare un invito alla condivisione dei dati. 

* Sottoscrizione di Azure: Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* Un invito alla Condivisione dati: un invito da Microsoft Azure con un oggetto denominato "Azure Data Share invitation from **<yourdataprovider@domain.com>** " (Invito alla Condivisione dati di Azure da...).
* Registrare il [provider di risorse Microsoft.DataShare](concepts-roles-permissions.md#resource-provider-registration) nella sottoscrizione di Azure verrà creata una risorsa Condivisione dati e nella sottoscrizione di Azure in cui si trovano gli archivi dati di Azure di destinazione.

### <a name="receive-data-into-a-storage-account"></a>Ricevere dati in un account di archiviazione

* Un account di Archiviazione di Azure: se non se ne ha già uno, è possibile creare un [account di Archiviazione di Azure](../storage/common/storage-account-create.md). 
* Autorizzazione per la scrittura nell'account di archiviazione, disponibile in *Microsoft.Storage/storageAccounts/write*. Questa autorizzazione è presente nel ruolo Collaboratore. 
* Autorizzazione per aggiungere l'assegnazione di ruolo all'account di archiviazione, disponibile in *Microsoft.Authorization/role assignments/write*. Questa autorizzazione è presente nel ruolo Proprietario.  

### <a name="receive-data-into-a-sql-based-target"></a>Ricevere dati in una destinazione basata su SQL
Se si sceglie di ricevere dati in Database SQL di Azure o Azure Synapse Analytics, ecco l'elenco di prerequisiti. 

#### <a name="prerequisites-for-receiving-data-into-azure-sql-database-or-azure-synapse-analytics-formerly-azure-sql-dw"></a>Prerequisiti per la ricezione di dati in Database SQL di Azure o Azure Synapse Analytics (in precedenza Azure SQL Data Warehouse)
Per configurare i prerequisiti, è possibile seguire la [demo dettagliata](https://youtu.be/aeGISgK1xro).

* Un'istanza di Database SQL di Azure o di Azure Synapse Analytics (in precedenza SQL Data Warehouse).
* Autorizzazione per la scrittura nei database del server SQL, disponibile in *Microsoft.Sql/servers/databases/write*. Questa autorizzazione è presente nel ruolo di **collaboratore**. 
* Autorizzazione per l'identità gestita della risorsa Condivisione dati per l'accesso a Database SQL di Azure o ad Azure Synapse Analytics. A tale scopo, seguire questa procedura: 
    1. Nel portale di Azure passare al server SQL e impostare se stessi come **amministratore di Azure Active Directory**.
    1. Connettersi all'istanza di Database SQL di Azure/Data Warehouse usando l'[editor di query](../azure-sql/database/connect-query-portal.md#connect-using-azure-active-directory) o SQL Server Management Studio con l'autenticazione di Azure Active Directory. 
    1. Eseguire lo script seguente per aggiungere l'identità gestita di Condivisione dati come 'db_datareader, db_datawriter, db_ddladmin'. È necessario connettersi usando Active Directory e non l'autenticazione di SQL Server. 

        ```sql
        create user "<share_acc_name>" from external provider; 
        exec sp_addrolemember db_datareader, "<share_acc_name>"; 
        exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
        exec sp_addrolemember db_ddladmin, "<share_acc_name>";
        ```      
        Si noti che *<share_acc_name>* è il nome della risorsa Condivisione dati. Se ancora non si è provveduto a creare una risorsa Condivisione dati, è possibile tornare a questo prerequisito in un secondo momento.         

* Accesso al firewall di SQL Server. A tale scopo, seguire questa procedura: 
    1. Nel portale di Azure in SQL Server passare a *Firewall e reti virtuali*
    1. Fare clic su **Sì** per *Consenti alle risorse e ai servizi di Azure di accedere a questo server*.
    1. Fare clic su **+ Aggiungi IP client**. L'indirizzo IP client è soggetto a modifiche. Potrebbe essere necessario ripetere questo processo la volta successiva che si condividono i dati SQL dal portale di Azure. È anche possibile aggiungere un intervallo di indirizzi IP.
    1. Fare clic su **Salva**. 
 
#### <a name="prerequisites-for-receiving-data-into-azure-synapse-analytics-workspace-sql-pool"></a>Prerequisiti per la ricezione di dati nel pool SQL di Azure Synapse Analytics (area di lavoro)

* Un pool SQL dedicato di Azure Synapse Analytics (area di lavoro). La ricezione di dati nel pool SQL serverless non è attualmente supportata.
* Autorizzazione per scrivere nel pool SQL nell'area di lavoro di Synapse, presente in *Microsoft.Synapse/workspaces/sqlPools/write*. Questa autorizzazione è presente nel ruolo di **collaboratore**.
* Autorizzazione per l'identità gestita della risorsa Condivisione dati per l'accesso al pool SQL dell'area di lavoro di Synapse. A tale scopo, seguire questa procedura: 
    1. Nel portale di Azure passare all'area di lavoro di Synapse. Selezionare Amministratore SQL Active Directory nel riquadro di spostamento sinistro e impostare se stessi come **amministratore di Azure Active Directory**.
    1. Aprire Synapse Studio e selezionare *Gestisci* nel riquadro di spostamento sinistro. Selezionare *Controllo di accesso* in Sicurezza. Assegnare a se stessi il ruolo di **amministratore SQL** o **amministratore dell'area di lavoro**.
    1. In Synapse Studio selezionare *Sviluppo* nel riquadro di spostamento sinistro. Eseguire lo script seguente nel pool SQL per aggiungere l'identità gestita della risorsa Condivisione dati come 'db_datareader, db_datawriter, db_ddladmin'. 
    
        ```sql
        create user "<share_acc_name>" from external provider; 
        exec sp_addrolemember db_datareader, "<share_acc_name>"; 
        exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
        exec sp_addrolemember db_ddladmin, "<share_acc_name>";
        ```                   
       Si noti che *<share_acc_name>* è il nome della risorsa Condivisione dati. Se ancora non si è provveduto a creare una risorsa Condivisione dati, è possibile tornare a questo prerequisito in un secondo momento.  

* Accesso al firewall dell'area di lavoro di Synapse. A tale scopo, seguire questa procedura: 
    1. Nel portale di Azure passare all'area di lavoro di Synapse. Selezionare *Firewall* nel riquadro di spostamento sinistro.
    1. Fare clic su **Sì** per *Consenti alle risorse e ai servizi di Azure di accedere a questa area di lavoro*.
    1. Fare clic su **+ Aggiungi IP client**. L'indirizzo IP client è soggetto a modifiche. Potrebbe essere necessario ripetere questo processo la volta successiva che si condividono i dati SQL dal portale di Azure. È anche possibile aggiungere un intervallo di indirizzi IP.
    1. Fare clic su **Salva**. 

### <a name="receive-data-into-an-azure-data-explorer-cluster"></a>Ricevere i dati in un cluster di Esplora dati di Azure: 

* Un cluster di Esplora dati di Azure nello stesso data center di Azure del cluster di Esplora dati del provider di dati: Se non si è già provveduto, è possibile creare un [cluster di Esplora dati di Azure](/azure/data-explorer/create-cluster-database-portal). Se non si conosce il data center di Azure del cluster del provider di dati, è possibile creare il cluster in un secondo momento del processo.
* Autorizzazione per la scrittura nel cluster di Esplora dati di Azure, disponibile in *Microsoft.Kusto/clusters/write*. Questa autorizzazione è presente nel ruolo Collaboratore. 
* Autorizzazione per aggiungere l'assegnazione di ruolo al cluster di Esplora dati di Azure, disponibile in *Microsoft.Authorization/role assignments/write*. Questa autorizzazione è presente nel ruolo Proprietario. 

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/).

## <a name="open-invitation"></a>Aprire l'invito

### <a name="portal"></a>[Portale](#tab/azure-portal)

1. È possibile aprire l'invito da un messaggio di posta elettronica o direttamente dal portale di Azure. 

   Per aprire l'invito da un messaggio di posta elettronica, controllare la posta in arrivo per verificare di aver ricevuto un invito dal provider di dati. L'invito proviene da Microsoft Azure con l'oggetto **Azure Data Share invitation from<yourdataprovider@domain.com>** (Invito alla Condivisione dati di Azure da...). Fare clic su **Visualizza invito** per visualizzare l'invito in Azure. 

   Per aprire direttamente l'invito dal portale di Azure, cercare **Inviti alla condivisione dati** nel portale di Azure. Con questa azione viene visualizzato l'elenco di inviti di Condivisione dati.

   ![Elenco di inviti](./media/invitations.png "Elenco di inviti") 

1. Selezionare la condivisione che si vuole visualizzare. 

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Preparare l'ambiente per l'interfaccia della riga di comando di Azure e quindi visualizzare gli inviti.

Per iniziare, preparare l'ambiente per l'interfaccia della riga di comando di Azure:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Eseguire il comando [az datashare consumer invitation list](/cli/azure/ext/datashare/datashare/consumer/invitation#ext_datashare_az_datashare_consumer_invitation_list) per visualizzare gli inviti correnti:

```azurecli
az datashare consumer invitation list --subscription 11111111-1111-1111-1111-111111111111
```

Copiare l'ID invito per usarlo nella sezione successiva.

---

## <a name="accept-invitation"></a>Accettare l'invito

### <a name="portal"></a>[Portale](#tab/azure-portal)

1. Assicurarsi di rivedere tutti i campi, tra cui le **condizioni per l'utilizzo**. Se si accettano le condizioni per l'utilizzo, verrà richiesto di selezionare la casella per confermare l'accettazione. 

   ![Condizioni d'uso](./media/terms-of-use.png "Condizioni per l'utilizzo") 

1. In *Target Data Share Account* (Account di condivisione dati di destinazione), selezionare la sottoscrizione e il gruppo di risorse in cui verrà distribuita la condivisione dati. 

   Nel campo **Data Share Account** (Account di condivisione dati), selezionare **Create new** (Crea nuovo) se non si ha un account di Condivisione dati esistente. In caso contrario, selezionare un account esistente di Condivisione dati in cui si vuole accettare la condivisione dei dati. 

   Nel campo **Received Share Name** (Nome condivisione ricevuta) si può lasciare il valore predefinito specificato dal provider di dati o specificare un nuovo nome per la condivisione ricevuta. 

   Dopo aver accettato le condizioni per l'utilizzo e specificato un account Condivisione dati per gestire la condivisione ricevuta, selezionare **Accept and Configure** (Accetta e configura). Verrà creata una nuova sottoscrizione di condivisione. 

   ![Opzioni di accettazione](./media/accept-options.png "Opzioni di accettazione") 

   Con questa azione si viene reindirizzati nella condivisione ricevuta nell'account di Condivisione dati. 

   Se non si vuole accettare l'invito, selezionare *Reject* (Rifiuta). 

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Usare il comando [az datashare consumer share-subscription create](/cli/azure/ext/datashare/datashare/consumer/share-subscription#ext_datashare_az_datashare_consumer_share_subscription_create) per creare la condivisione dati.

```azurecli
az datashare consumer share-subscription create --resource-group share-rg \
  --name "Fabrikam Solutions" --account-name FabrikamDataShareAccount \
  --invitation-id 89abcdef-0123-4567-89ab-cdef01234567 \
  --source-share-location "East US 2" --subscription 11111111-1111-1111-1111-111111111111
```

---

## <a name="configure-received-share"></a>Configurare la condivisione ricevuta

### <a name="portal"></a>[Portale](#tab/azure-portal)

Seguire questa procedura per configurare la posizione in cui si desidera ricevere i dati.

1. Selezionare la scheda **Set di dati**. Selezionare la casella accanto al set di dati a cui si vuole assegnare una destinazione. Selezionare **+ Esegui mapping alla destinazione** per scegliere un archivio dati di destinazione. 

   ![Esegui mapping alla destinazione](./media/dataset-map-target.png "Esegui mapping alla destinazione") 

1. Selezionare un tipo di archivio dati di destinazione in cui verranno ospitati i dati. Eventuali tabelle o file di dati nell'archivio dati di destinazione con lo stesso percorso e nome verranno sovrascritti. 

   Per la condivisione sul posto selezionare un archivio dati nella località specificata. La località è il data center di Azure in cui si trova l'archivio dati di origine del provider di dati. Una volta eseguito il mapping del set di dati, è possibile seguire il collegamento nel percorso di destinazione per accedere ai dati.

   ![Account di archiviazione di destinazione](./media/dataset-map-target-sql.png "Archiviazione di destinazione") 

1. Per la condivisione basata su snapshot, se il provider di dati ha creato una pianificazione degli snapshot per fornire aggiornamenti regolari ai dati, è anche possibile abilitare la pianificazione degli snapshot selezionando la scheda **Pianificazione degli snapshot**. Selezionare la casella accanto alla pianificazione degli snapshot e selezionare **+ Abilita**.

   ![Abilitare la pianificazione degli snapshot](./media/enable-snapshot-schedule.png "Abilitare la pianificazione degli snapshot")

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Usare questi comandi per configurare la posizione in cui ricevere i dati.

1. Eseguire il comando [az datashare consumer share-subscription list-source-dataset](/cli/azure/ext/datashare/datashare/consumer/share-subscription#ext_datashare_az_datashare_consumer_share_subscription_list_source_dataset) per ottenere l'ID set di dati:

   ```azurecli
   az datashare consumer share-subscription list-source-dataset \
     --resource-group "share-rg" --account-name "FabrikamDataShareAccount" \
     --share-subscription-name "Fabrikam Solutions" \
     --subscription 11111111-1111-1111-1111-111111111111 --query "[0].dataSetId"
   ```

1. Eseguire il comando [az storage account create](/cli/azure/storage/account#az_storage_account_create) per creare un account di archiviazione per questa condivisione dati:

   ```azurecli
   az storage account create --resource-group "share-rg" --name "FabrikamDataShareAccount" \
     --subscription 11111111-1111-1111-1111-111111111111
   ```

1. Usare il comando [az storage account show](/cli/azure/storage/account#az_storage_account_show) per ottenere l'ID account di archiviazione:

   ```azurecli
   az storage account show --resource-group "share-rg" --name "FabrikamDataShareAccount" \
     --subscription 11111111-1111-1111-1111-111111111111 --query "id"
   ```

1. Usare il comando seguente per ottenere l'ID entità account:

   ```azurecli
   az datashare account show --resource-group "share-rg" --name "cli_test_consumer_account" \
     --subscription 11111111-1111-1111-1111-111111111111 --query "identity.principalId"
   ```

1. Usare il comando [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) per creare un'assegnazione di ruolo per l'entità account:

   ```azurecli
   az role assignment create --role "01234567-89ab-cdef-0123-456789abcdef" \
     --assignee-object-id 6789abcd-ef01-2345-6789-abcdef012345 
     --assignee-principal-type ServicePrincipal --scope 456789ab-cdef-0123-4567-89abcdef0123 \
     --subscription 11111111-1111-1111-1111-111111111111
   ```

1. Creare una variabile per il mapping in base all'ID set di dati:

   ```azurecli
   $mapping='{\"data_set_id\":\"' + $dataset_id + '\",\"container_name\":\"newcontainer\",
     \"storage_account_name\":\"datashareconsumersa\",\"kind\":\"BlobFolder\",\"prefix\":\"consumer\"}'
   ```

1. Usare il comando [az datashare consumer dataset-mapping create](/cli/azure/ext/datashare/datashare/consumer/dataset-mapping#ext_datashare_az_datashare_consumer_dataset_mapping_create) per creare il mapping del set di dati:

   ```azurecli
   az datashare consumer dataset-mapping create --resource-group "share-rg" \
     --name "consumer-data-set-mapping" --account-name "FabrikamDataShareAccount" \
     --share-subscription-name "Fabrikam Solutions" --mapping $mapping \
     --subscription 11111111-1111-1111-1111-111111111111
   ```

1. Eseguire il comando [az datashare consumer share-subscription synchronization start](/cli/azure/ext/datashare/datashare/consumer/share-subscription/synchronization#ext_datashare_az_datashare_consumer_share_subscription_synchronization_start) per avviare la sincronizzazione del set di dati.

   ```azurecli
   az datashare consumer share-subscription synchronization start \
     --resource-group "share-rg" --account-name "FabrikamDataShareAccount"  \
     --share-subscription-name "Fabrikam Solutions" --synchronization-mode "Incremental" \
     --subscription 11111111-1111-1111-1111-111111111111
   ```

   Eseguire il comando [az datashare consumer share-subscription synchronization list](/cli/azure/ext/datashare/datashare/consumer/share-subscription/synchronization#ext_datashare_az_datashare_consumer_share_subscription_synchronization_list) per visualizzare un elenco di sincronizzazioni:

   ```azurecli
   az datashare consumer share-subscription synchronization list \
     --resource-group "share-rg" --account-name "FabrikamDataShareAccount" \
     --share-subscription-name "Fabrikam Solutions" \
     --subscription 11111111-1111-1111-1111-111111111111
   ```

   Usare il comando [az datashare consumer share-subscription list-source-share-synchronization-setting](/cli/azure/ext/datashare/datashare/consumer/share-subscription#ext_datashare_az_datashare_consumer_share_subscription_list_source_share_synchronization_setting) per visualizzare le impostazioni di sincronizzazione impostate nella condivisione.

   ```azurecli
   az datashare consumer share-subscription list-source-share-synchronization-setting \
     --resource-group "share-rg" --account-name "FabrikamDataShareAccount" \
     --share-subscription-name "Fabrikam Solutions" --subscription 11111111-1111-1111-1111-111111111111
   ```

---

## <a name="trigger-a-snapshot"></a>Attivazione di uno snapshot

### <a name="portal"></a>[Portale](#tab/azure-portal)

Questa procedura si applica solo alla condivisione basata su snapshot.

1. È possibile attivare uno snapshot selezionando la scheda **Dettagli** e quindi **Attiva snapshot**. Qui è possibile attivare uno snapshot completo o incrementale dei dati. Se è la prima volta che si ricevono i dati dal provider, selezionare Full copy (Copia completa). 

   ![Attivazione dello snapshot](./media/trigger-snapshot.png "Attivazione dello snapshot") 

1. Quando l'ultimo stato di esecuzione è *riuscito*, passare all'archivio dati di destinazione per visualizzare i dati ricevuti. Selezionare **Set di dati** e fare clic sul collegamento nel percorso di destinazione. 

   ![Set di dati consumer](./media/consumer-datasets.png "Mapping dei set di dati consumer") 

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Eseguire il comando [az datashare consumer trigger create](/cli/azure/ext/datashare/datashare/consumer/trigger#ext_datashare_az_datashare_consumer_trigger_create) per attivare uno snapshot:

```azurecli
az datashare consumer trigger create --resource-group "share-rg" \
  --name "share_test_trigger" --account-name "FabrikamDataShareAccount" \
  --share-subscription-name "Fabrikam Solutions" --recurrence-interval "Day" \
  --synchronization-time "2020-04-23 18:00:00 +00:00" --kind ScheduleBased \
  --subscription 11111111-1111-1111-1111-111111111111
```

> [!NOTE]
> Usare questo comando solo per la condivisione basata su snapshot.

---

## <a name="view-history"></a>Visualizzare la cronologia
Questo passaggio si applica solo alla condivisione basata su snapshot. Per visualizzare la cronologia degli snapshot, selezionare la scheda **Cronologia**. Qui è presente una cronologia di tutti gli snapshot generati negli ultimi 30 giorni.

## <a name="clean-up-resources"></a>Pulizia delle risorse

Quando la risorsa non è più necessaria, passare alla pagina **Panoramica di Condivisione dati** e selezionare **Elimina** per rimuoverla.

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione si è appreso come accettare e ricevere una Condivisione dati di Azure. Per altre informazioni sui concetti di Condivisione dati di Azure, passare alla terminologia di Condivisione dati di Azure.

> [!div class="nextstepaction"]
> [Concetti relativi a Condivisione dati di Azure](terminology.md)