---
title: 'Esercitazione: Accettare e ricevere dati - Condivisione dati di Azure'
description: 'Esercitazione: Accettare e ricevere dati con Condivisione dati di Azure'
author: jifems
ms.author: jife
ms.service: data-share
ms.topic: tutorial
ms.date: 08/14/2020
ms.openlocfilehash: 409f143ce67e301e3b2a973d8d2db80380fbd50e
ms.sourcegitcommit: ef055468d1cb0de4433e1403d6617fede7f5d00e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2020
ms.locfileid: "88258665"
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

### <a name="receive-data-into-a-storage-account"></a>Ricevere dati in un account di archiviazione: 

* Un account di Archiviazione di Azure: se non se ne ha già uno, è possibile creare un [account di Archiviazione di Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account). 
* Autorizzazione per la scrittura nell'account di archiviazione, disponibile in *Microsoft.Storage/storageAccounts/write*. Questa autorizzazione è presente nel ruolo Collaboratore. 
* Autorizzazione per aggiungere l'assegnazione di ruolo all'account di archiviazione, disponibile in *Microsoft.Authorization/role assignments/write*. Questa autorizzazione è presente nel ruolo Proprietario.  

### <a name="receive-data-into-a-sql-based-source"></a>Ricevere dati in un'origine basata su SQL:

* Autorizzazione per la scrittura nei database del server SQL, disponibile in *Microsoft.Sql/servers/databases/write*. Questa autorizzazione è presente nel ruolo Collaboratore. 
* Autorizzazione per l'identità gestita della risorsa di condivisione dati per l'accesso al database SQL di Azure o ad Azure SQL Data Warehouse. A tale scopo, seguire questa procedura: 
    1. Impostare se stessi come amministratore di Azure Active Directory per il server SQL.
    1. Connettersi al database SQL di Azure o ad Azure SQL Data Warehouse con Azure Active Directory.
    1. Usare l'Editor di query (anteprima) per eseguire lo script seguente in modo da aggiungere l'identità gestita di Condivisione dati come 'db_datareader, db_datawriter, db_ddladmin'. È necessario connettersi usando Active Directory e non l'autenticazione di SQL Server. 

        ```sql
        create user "<share_acc_name>" from external provider; 
        exec sp_addrolemember db_datareader, "<share_acc_name>"; 
        exec sp_addrolemember db_datawriter, "<share_acc_name>"; 
        exec sp_addrolemember db_ddladmin, "<share_acc_name>";
        ```      
        Si noti che *<share_acc_name>* è il nome della risorsa Condivisione dati. Se ancora non si è provveduto a creare una risorsa Condivisione dati, è possibile tornare a questo prerequisito in un secondo momento.         

* Accesso del firewall di SQL Server all'indirizzo IP client. A tale scopo, seguire questa procedura: 
    1. Nel portale di Azure in SQL Server passare a *Firewall e reti virtuali*
    1. Fare clic sull'interruttore di **attivazione** per consentire l'accesso ai servizi di Azure.
    1. Fare clic su **+ Aggiungi IP client** e fare clic su **Salva**. L'indirizzo IP client è soggetto a modifiche. Potrebbe essere necessario ripetere questo processo la volta successiva che vengono ricevuti i dati in una destinazione SQL dal portale di Azure. È anche possibile aggiungere un intervallo di indirizzi IP. 


### <a name="receive-data-into-an-azure-data-explorer-cluster"></a>Ricevere i dati in un cluster di Esplora dati di Azure: 

* Un cluster di Esplora dati di Azure nello stesso data center di Azure del cluster di Esplora dati del provider di dati: Se non si è già provveduto, è possibile creare un [cluster di Esplora dati di Azure](https://docs.microsoft.com/azure/data-explorer/create-cluster-database-portal). Se non si conosce il data center di Azure del cluster del provider di dati, è possibile creare il cluster in un secondo momento del processo.
* Autorizzazione per la scrittura nel cluster di Esplora dati di Azure, disponibile in *Microsoft.Kusto/clusters/write*. Questa autorizzazione è presente nel ruolo Collaboratore. 
* Autorizzazione per aggiungere l'assegnazione di ruolo al cluster di Esplora dati di Azure, disponibile in *Microsoft.Authorization/role assignments/write*. Questa autorizzazione è presente nel ruolo Proprietario. 

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/).

## <a name="open-invitation"></a>Aprire l'invito

1. È possibile aprire l'invito da un messaggio di posta elettronica o direttamente dal portale di Azure. 

   Per aprire l'invito da un messaggio di posta elettronica, controllare la posta in arrivo per verificare di aver ricevuto un invito dal provider di dati. L'invito proviene da Microsoft Azure con l'oggetto **Azure Data Share invitation from<yourdataprovider@domain.com>** (Invito alla Condivisione dati di Azure da...). Fare clic su **Visualizza invito** per visualizzare l'invito in Azure. 

   Per aprire direttamente l'invito dal portale di Azure, cercare **Inviti alla condivisione dati** nel portale di Azure. In questo modo viene visualizzato l'elenco di inviti alla condivisione dati.

   ![Inviti](./media/invitations.png "Elenco di inviti") 

1. Selezionare la condivisione che si vuole visualizzare. 

## <a name="accept-invitation"></a>Accettare l'invito
1. Assicurarsi di rivedere tutti i campi, tra cui le **condizioni per l'utilizzo**. Se si accettano le condizioni per l'utilizzo, verrà richiesto di selezionare la casella per confermare l'accettazione. 

   ![Condizioni d'uso](./media/terms-of-use.png "Condizioni per l'utilizzo") 

1. In *Target Data Share Account* (Account di condivisione dati di destinazione), selezionare la sottoscrizione e il gruppo di risorse in cui verrà distribuita la condivisione dati. 

   Nel campo **Data Share Account** (Account di condivisione dati), selezionare **Create new** (Crea nuovo) se non si ha un account di Condivisione dati esistente. In caso contrario, selezionare un account esistente di Condivisione dati in cui si vuole accettare la condivisione dei dati. 

   Nel campo **Received Share Name** (Nome condivisione ricevuta) si può lasciare il valore predefinito specificato dal provider di dati o specificare un nuovo nome per la condivisione ricevuta. 

   Dopo aver accettato le condizioni per l'utilizzo e specificato un account Condivisione dati per gestire la condivisione ricevuta, selezionare **Accept and Configure** (Accetta e configura). Verrà creata una nuova sottoscrizione di condivisione. 

   ![Opzioni di accettazione](./media/accept-options.png "Opzioni di accettazione") 

   Si verrà reindirizzati alla condivisione ricevuta nell'account Condivisione dati. 

   Se non si vuole accettare l'invito, selezionare *Reject* (Rifiuta). 

## <a name="configure-received-share"></a>Configurare la condivisione ricevuta
Seguire questa procedura per configurare la posizione in cui si desidera ricevere i dati.

1. Selezionare la scheda **Set di dati**. Selezionare la casella accanto al set di dati a cui si vuole assegnare una destinazione. Selezionare **+ Esegui mapping alla destinazione** per scegliere un archivio dati di destinazione. 

   ![Esegui mapping alla destinazione](./media/dataset-map-target.png "Esegui mapping alla destinazione") 

1. Selezionare un tipo di archivio dati di destinazione in cui verranno ospitati i dati. Eventuali tabelle o file di dati nell'archivio dati di destinazione con lo stesso percorso e nome verranno sovrascritti. 

   Per la condivisione sul posto selezionare un archivio dati nella località specificata. La località è il data center di Azure in cui si trova l'archivio dati di origine del provider di dati. Una volta eseguito il mapping del set di dati, è possibile seguire il collegamento nel percorso di destinazione per accedere ai dati.

   ![Account di archiviazione di destinazione](./media/dataset-map-target-sql.png "Archiviazione di destinazione") 

1. Per la condivisione basata su snapshot, se il provider di dati ha creato una pianificazione degli snapshot per fornire aggiornamenti regolari ai dati, è anche possibile abilitare la pianificazione degli snapshot selezionando la scheda **Pianificazione degli snapshot**. Selezionare la casella accanto alla pianificazione degli snapshot e selezionare **+ Abilita**.

   ![Abilitare la pianificazione degli snapshot](./media/enable-snapshot-schedule.png "Abilitare la pianificazione degli snapshot")

## <a name="trigger-a-snapshot"></a>Attivazione di uno snapshot
Questa procedura si applica solo alla condivisione basata su snapshot.

1. È possibile attivare uno snapshot selezionando la scheda **Dettagli** e quindi **Attiva snapshot**. Qui è possibile attivare uno snapshot completo o incrementale dei dati. Se è la prima volta che si ricevono i dati dal provider, selezionare Full copy (Copia completa). 

   ![Attivazione dello snapshot](./media/trigger-snapshot.png "Attivazione dello snapshot") 

1. Quando l'ultimo stato di esecuzione è *riuscito*, passare all'archivio dati di destinazione per visualizzare i dati ricevuti. Selezionare **Set di dati** e fare clic sul collegamento nel percorso di destinazione. 

   ![Set di dati consumer](./media/consumer-datasets.png "Mapping dei set di dati consumer") 

## <a name="view-history"></a>Visualizzare la cronologia
Questo passaggio si applica solo alla condivisione basata su snapshot. Per visualizzare la cronologia degli snapshot, selezionare la scheda **Cronologia**. Qui è presente una cronologia di tutti gli snapshot generati negli ultimi 30 giorni. 

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione si è appreso come accettare e ricevere una Condivisione dati di Azure. Per altre informazioni sui concetti di Condivisione dati di Azure, passare a [Concetti: Terminologia di Condivisione dati di Azure](terminology.md).