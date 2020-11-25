---
title: "Esercitazione: Condividere all'esterno dell'organizzazione - Condivisione dati di Azure"
description: Esercitazione - Condividere dati con clienti e partner usando Condivisione dati di Azure
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: tutorial
ms.date: 11/12/2020
ms.openlocfilehash: 89c2a725b853b5a2a7578dccc1fd503917e12962
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94659625"
---
# <a name="tutorial-share-data-using-azure-data-share"></a>Esercitazione: Condividere dati con Condivisione dati di Azure  

Questa esercitazione illustra come configurare una nuova condivisione dati di Azure e iniziare a condividere i dati con clienti e partner all'esterno dell'organizzazione di Azure. 

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare una condivisione dati.
> * Aggiungere set di dati alla condivisione dati.
> * Abilitare una pianificazione degli snapshot per la condivisione dati. 
> * Aggiungere i destinatari alla condivisione dati. 

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.
* Indirizzo di posta elettronica per l'accesso ad Azure dei destinatari (gli alias di posta elettronica non funzionano).
* Se l'archivio dati di Azure di origine si trova in una sottoscrizione di Azure diversa da quella che verrà usata per creare la risorsa Condivisione dati, registrare il [provider di risorse Microsoft.DataShare](concepts-roles-permissions.md#resource-provider-registration) nella sottoscrizione in cui risiede l'archivio dati di Azure. 

### <a name="share-from-a-storage-account"></a>Condividere da un account di archiviazione

* Un account di Archiviazione di Azure: se non se ne ha già uno, è possibile creare un [account di archiviazione di Azure](../storage/common/storage-account-create.md).
* Autorizzazione per la scrittura nell'account di archiviazione, disponibile in *Microsoft.Storage/storageAccounts/write*. Questa autorizzazione è presente nel ruolo di **collaboratore**.
* Autorizzazione per aggiungere l'assegnazione di ruolo all'account di archiviazione, disponibile in *Microsoft.Authorization/role assignments/write*. Questa autorizzazione è presente nel ruolo di **proprietario**. 


### <a name="share-from-a-sql-based-source"></a>Condividere da un'origine basata su SQL
Di seguito è riportato l'elenco di prerequisiti per la condivisione da un'origine SQL. 

#### <a name="prerequisites-for-sharing-from-azure-sql-database-or-azure-synapse-analytics-formerly-azure-sql-dw"></a>Prerequisiti per la condivisione da Database SQL di Azure o Azure Synapse Analytics (in precedenza Azure SQL Data Warehouse)
Per configurare i prerequisiti, è possibile seguire la [demo dettagliata](https://youtu.be/hIE-TjJD8Dc).

* Un'istanza di Database SQL di Azure o di Azure Synapse Analytics (in precedenza Azure SQL Data Warehouse) con tabelle e viste da condividere.
* Autorizzazione per la scrittura nei database del server SQL, disponibile in *Microsoft.Sql/servers/databases/write*. Questa autorizzazione è presente nel ruolo di **collaboratore**.
* Autorizzazione per l'identità gestita della risorsa Condivisione dati per l'accesso al database. A tale scopo, seguire questa procedura: 
    1. Nel portale di Azure passare al server SQL e impostare se stessi come **amministratore di Azure Active Directory**.
    1. Connettersi all'istanza di Database SQL di Azure/Data Warehouse usando l'[editor di query](../azure-sql/database/connect-query-portal.md#connect-using-azure-active-directory) o SQL Server Management Studio con l'autenticazione di Azure Active Directory. 
    1. Eseguire lo script seguente per aggiungere l'identità gestita della risorsa Condivisione dati come db_datareader. È necessario connettersi usando Active Directory e non l'autenticazione di SQL Server. 
    
        ```sql
        create user "<share_acct_name>" from external provider;     
        exec sp_addrolemember db_datareader, "<share_acct_name>"; 
        ```                   
       Si noti che *<share_acc_name>* è il nome della risorsa Condivisione dati. Se ancora non si è provveduto a creare una risorsa Condivisione dati, è possibile tornare a questo prerequisito in un secondo momento.  

* Un utente di Database SQL di Azure con accesso **'db_datareader'** per individuare e selezionare le tabelle e/o le viste da condividere. 

* Accesso al firewall di SQL Server. A tale scopo, seguire questa procedura: 
    1. Nel portale di Azure passare al server SQL. Selezionare *Firewall e reti virtuali* nel riquadro di spostamento sinistro.
    1. Fare clic su **Sì** per *Consenti alle risorse e ai servizi di Azure di accedere a questo server*.
    1. Fare clic su **+ Aggiungi IP client**. L'indirizzo IP client è soggetto a modifiche. Potrebbe essere necessario ripetere questo processo la volta successiva che si condividono i dati SQL dal portale di Azure. È anche possibile aggiungere un intervallo di indirizzi IP.
    1. Fare clic su **Salva**. 

#### <a name="prerequisites-for-sharing-from-azure-synapse-analytics-workspace-sql-pool"></a>Prerequisiti per la condivisione dal pool SQL di Azure Synapse Analytics (area di lavoro)

* * Un pool SQL dedicato di Azure Synapse Analytics (area di lavoro) con le tabelle da condividere. La condivisione di viste non è attualmente supportata. La condivisione dal pool SQL serverless non è attualmente supportata.
* Autorizzazione per scrivere nel pool SQL nell'area di lavoro di Synapse, presente in *Microsoft.Synapse/workspaces/sqlPools/write*. Questa autorizzazione è presente nel ruolo di **collaboratore**.
* Autorizzazione per l'identità gestita della risorsa Condivisione dati per l'accesso al pool SQL dell'area di lavoro di Synapse. A tale scopo, seguire questa procedura: 
    1. Nel portale di Azure passare all'area di lavoro di Synapse. Selezionare Amministratore SQL Active Directory nel riquadro di spostamento sinistro e impostare se stessi come **amministratore di Azure Active Directory**.
    1. Aprire Synapse Studio e selezionare *Gestisci* nel riquadro di spostamento sinistro. Selezionare *Controllo di accesso* in Sicurezza. Assegnare a se stessi il ruolo di **amministratore SQL** o **amministratore dell'area di lavoro**.
    1. In Synapse Studio selezionare *Sviluppo* nel riquadro di spostamento sinistro. Eseguire lo script seguente nel pool SQL per aggiungere l'identità gestita della risorsa Condivisione dati come db_datareader. 
    
        ```sql
        create user "<share_acct_name>" from external provider;     
        exec sp_addrolemember db_datareader, "<share_acct_name>"; 
        ```                   
       Si noti che *<share_acc_name>* è il nome della risorsa Condivisione dati. Se ancora non si è provveduto a creare una risorsa Condivisione dati, è possibile tornare a questo prerequisito in un secondo momento.  

* Accesso al firewall dell'area di lavoro di Synapse. A tale scopo, seguire questa procedura: 
    1. Nel portale di Azure passare all'area di lavoro di Synapse. Selezionare *Firewall* nel riquadro di spostamento sinistro.
    1. Fare clic su **Sì** per *Consenti alle risorse e ai servizi di Azure di accedere a questa area di lavoro*.
    1. Fare clic su **+ Aggiungi IP client**. L'indirizzo IP client è soggetto a modifiche. Potrebbe essere necessario ripetere questo processo la volta successiva che si condividono i dati SQL dal portale di Azure. È anche possibile aggiungere un intervallo di indirizzi IP.
    1. Fare clic su **Salva**. 


### <a name="share-from-azure-data-explorer"></a>Condividere da Esplora dati di Azure
* Cluster di Esplora dati di Azure con i database da condividere.
* Autorizzazione per la scrittura nel cluster di Esplora dati di Azure, disponibile in *Microsoft.Kusto/clusters/write*. Questa autorizzazione è presente nel ruolo di **collaboratore**.
* Autorizzazione per aggiungere l'assegnazione di ruolo al cluster di Esplora dati di Azure, disponibile in *Microsoft.Authorization/role assignments/write*. Questa autorizzazione è presente nel ruolo di **proprietario**.

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/).

## <a name="create-a-data-share-account"></a>Creare un account di condivisione dati

### <a name="portal"></a>[Portale](#tab/azure-portal)

Creare una risorsa di condivisione dati di Azure in un gruppo di risorse di Azure.

1. Selezionare il pulsante del menu nell'angolo superiore sinistro del portale e quindi selezionare **+ Crea una risorsa**.

1. Cercare *Condivisione dati*.

1. Selezionare Condivisione dati e quindi **Crea**.

1. Compilare i dettagli di base della risorsa di condivisione dati di Azure con le informazioni seguenti. 

     **Impostazione** | **Valore consigliato** | **Descrizione campo**
    |---|---|---|
    | Subscription | Sottoscrizione in uso | Selezionare la sottoscrizione di Azure da usare per l'account di condivisione dati.|
    | Gruppo di risorse | *testresourcegroup* | Usare un gruppo di risorse esistente oppure crearne uno nuovo. |
    | Posizione | *Stati Uniti orientali 2* | Selezionare un'area per l'account di condivisione dati.
    | Nome | *datashareaccount* | Specificare un nome per l'account di condivisione dati. |
    | | |

1. Selezionare **Rivedi e crea**, quindi **Crea** per effettuare il provisioning dell'account di condivisione dati. Il completamento del provisioning di un nuovo account di condivisione dati in genere impiega al massimo 2 minuti. 

1. Una volta completata la distribuzione, selezionare **Vai alla risorsa**.

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Creare una risorsa di condivisione dati di Azure in un gruppo di risorse di Azure.

Per iniziare, preparare l'ambiente per l'interfaccia della riga di comando di Azure:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Usare questi comandi per creare la risorsa:

1. Usare il comando [az account set](/cli/azure/account#az_account_set) per impostare la sottoscrizione come sottoscrizione predefinita corrente:

   ```azurecli
   az account set --subscription 00000000-0000-0000-0000-000000000000
   ```

1. Eseguire il comando [az provider register](/cli/azure/provider#az_provider_register) per registrare il provider di risorse:

   ```azurecli
   az provider register --name "Microsoft.DataShare"
   ```

1. Eseguire il comando [az group create](/cli/azure/group#az_group_create) per creare un gruppo di risorse o usarne uno esistente:

   ```azurecli
   az group create --name testresourcegroup --location "East US 2"
   ```

1. Eseguire il comando [az datashare account create](/cli/azure/ext/datashare/datashare/account#ext_datashare_az_datashare_account_create) per creare un account di Condivisione dati:

   ```azurecli
   az datashare account create --resource-group testresourcegroup --name datashareaccount --location "East US 2" 
   ```

   Eseguire il comando [az datashare account list](/cli/azure/ext/datashare/datashare/account#ext_datashare_az_datashare_account_list) per visualizzare gli account di Condivisione dati:

   ```azurecli
   az datashare account list --resource-group testresourcegroup
   ```

---

## <a name="create-a-share"></a>Creare una condivisione

### <a name="portal"></a>[Portale](#tab/azure-portal)

1. Passare alla pagina di panoramica della condivisione dati.

    ![Condividere i dati](./media/share-receive-data.png "Condividere i dati") 

1. Selezionare **Start sharing your data** (Inizia a condividere i dati).

1. Selezionare **Create** (Crea).   

1. Immettere i dettagli della condivisione. Specificare un nome, un tipo di condivisione, una descrizione del contenuto della condivisione e le condizioni per l'utilizzo (facoltativo). 

    ![Immettere i dettagli della condivisione](./media/enter-share-details.png "Immettere i dettagli della condivisione") 

1. Selezionare **Continua**.

1. Per aggiungere set di dati alla condivisione, selezionare **Aggiungi set di dati**. 

    ![Aggiungere set di dati alla condivisione](./media/datasets.png "Set di dati")

1. Selezionare il tipo di set di dati da aggiungere. Verrà visualizzato un elenco di tipi di set di dati diverso a seconda del tipo di condivisione (snapshot o sul posto) selezionato nel passaggio precedente. Se la condivisione viene eseguita da un'istanza di Database SQL di Azure o di Azure Synapse Analytics (in precedenza Azure SQL Data Warehouse), verrà chiesto di immettere le credenziali di SQL per elencare le tabelle.

    ![Aggiungere i set di dati](./media/add-datasets.png "Aggiungere i set di dati")    

1. Passare all'oggetto che si vuole condividere e selezionare "Add Datasets" (Aggiungi set di dati). 

    ![Selezionare i set di dati](./media/select-datasets.png "Selezionare i set di dati")    

1. Nella scheda Destinatari immettere l'indirizzo di posta elettronica del consumer di dati selezionando "+ Aggiungi il destinatario". 

    ![Aggiungere i destinatari](./media/add-recipient.png "Aggiungere destinatari") 

1. Selezionare **Continua**.

1. Se è stato selezionato tipo di condivisione snapshot, è possibile configurare la pianificazione degli snapshot per fornire aggiornamenti dei dati al consumer di dati. 

    ![Abilitare gli snapshot](./media/enable-snapshots.png "Abilitare gli snapshot") 

1. Selezionare un'ora di inizio e un intervallo di ricorrenza. 

1. Selezionare **Continua**.

1. Nella scheda Rivedi e crea verificare il contenuto del pacchetto, le impostazioni, i destinatari e le impostazioni di sincronizzazione. Selezionare **Crea**.

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

1. Eseguire il comando [az storage account create](/cli/azure/storage/account#az_storage_account_create) per creare una condivisione dati:

   ```azurecli
   az storage account create --resource-group testresourcegroup --name ContosoMarketplaceAccount
   ```

1. Usare il comando [az storage container create](/cli/azure/storage/container#az_storage_container_create) per creare un contenitore per la condivisione del comando precedente:

   ```azurecli
   az storage container create --name ContosoMarketplaceContainer --account-name ContosoMarketplaceAccount
   ```

1. Eseguire il comando [az datashare create](/cli/azure/ext/datashare/datashare#ext_datashare_az_datashare_create) per creare la condivisione dati:

   ```azurecli
   az datashare create --resource-group testresourcegroup \
     --name ContosoMarketplaceDataShare --account-name ContosoMarketplaceAccount \
     --description "Data Share" --share-kind "CopyBased" --terms "Confidential"
   ```

1. Usare il comando [az datashare invitation create](/cli/azure/ext/datashare/datashare/invitation#ext_datashare_az_datashare_invitation_create) per creare l'invito per l'indirizzo specificato:

   ```azurecli
   az datashare invitation create --resource-group testresourcegroup \
     --name DataShareInvite --share-name ContosoMarketplaceDataShare \
     --account-name ContosoMarketplaceAccount --target-email "jacob@fabrikam"
   ```

---

La condivisione dati di Azure è stata creata e il suo destinatario è ora pronto ad accettare l'invito.

## <a name="clean-up-resources"></a>Pulizia delle risorse

Quando la risorsa non è più necessaria, passare alla pagina **Panoramica di Condivisione dati** e selezionare **Elimina** per rimuoverla.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come creare una condivisione dati di Azure e invitare destinatari. Per informazioni su come un consumer di dati può accettare e ricevere una condivisione dati, continuare con l'esercitazione Accettare e ricevere dati.

> [!div class="nextstepaction"]
> [Esercitazione: Accettare e ricevere dati con Condivisione dati di Azure](subscribe-to-data-share.md)
