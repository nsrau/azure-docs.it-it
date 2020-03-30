---
title: Introduzione a Storage Explorer | Microsoft Docs
description: Gestire le risorse di Archiviazione di Azure con Storage Explorer
services: storage
author: cawaMS
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.date: 11/08/2019
ms.author: cawa
ms.openlocfilehash: 7886d5a1ad0745550767b7d6f19592ca3c84b00a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279793"
---
# <a name="get-started-with-storage-explorer"></a>Introduzione a Storage Explorer

## <a name="overview"></a>Panoramica

Microsoft Azure Storage Explorer is a standalone app that makes it easy to work with Azure Storage data on Windows, macOS, and Linux. In questo articolo verranno appeccheranno diversi modi per connettersi e gestire gli account di archiviazione di Azure.In this article, you'll learn several ways of connecting to and managing your Azure storage accounts.

![Microsoft Azure Storage Explorer][0]

## <a name="prerequisites"></a>Prerequisiti

# <a name="windows"></a>[Windows](#tab/windows)

Le seguenti versioni di Windows supportano Storage Explorer:

* Windows 10 (scelta consigliata)
* Windows 8
* Windows 7

Per tutte le versioni di Windows, Storage Explorer richiede .NET Framework 4.6.2 o versione successiva.

# <a name="macos"></a>[Macos](#tab/macos)

Le seguenti versioni di macOS supportano Storage Explorer:

* macOS 10.12 Sierra e versioni successive

# <a name="linux"></a>[Linux](#tab/linux)

Storage Explorer è disponibile nello [Snap Store](https://snapcraft.io/storage-explorer) per le distribuzioni più comuni di Linux. Consigliamo Snap Store per questa installazione. Lo snap di Storage Explorer installa tutte le relative dipendenze e gli aggiornamenti quando nuove versioni vengono pubblicate nello Snap Store.

Per le distribuzioni supportate, vedere la [pagina di installazione ancorata](https://snapcraft.io/docs/installing-snapd).

Storage Explorer richiede l'uso di un gestore di password. Potrebbe essere necessario connettersi manualmente a un gestore di password. È possibile connettere Storage Explorer al gestore password del sistema eseguendo il comando seguente:

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

Storage Explorer è disponibile anche come download *.tar.gz.* È necessario installare le dipendenze manualmente. Le seguenti distribuzioni di Linux supportano *l'installazione .tar.gz:*

* Ubuntu 18.04 x64
* Ubuntu 16,04 x64
* Ubuntu 14.04 x64

L'installazione *di .tar.gz* potrebbe funzionare su altre distribuzioni, ma solo queste sono ufficialmente supportate.

Per altre informazioni sull'installazione di Storage Explorer in Linux, vedere Dipendenze Linux nella guida alla risoluzione dei problemi di Azure Storage Explorer.For more help installing Storage Explorer on Linux, see [Linux dependencies](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting#linux-dependencies) in the Azure Storage Explorer troubleshooting guide.

---

## <a name="download-and-install"></a>Download e installazione

Per scaricare e installare Storage Explorer, vedere Esplora archivi di [Azure.](https://www.storageexplorer.com)

## <a name="connect-to-a-storage-account-or-service"></a>Connettersi a un account o a un servizio di archiviazione

Storage Explorer offre numerosi modi per connettersi agli account di archiviazione. In generale è possibile:

* [Accedere ad Azure per accedere alle sottoscrizioni e alle relative risorse](#sign-in-to-azure)
* [Collegare una risorsa Di archiviazione o CosmosDB specifica](#attach-a-specific-resource)

### <a name="sign-in-to-azure"></a>Accedere ad Azure

> [!NOTE]
> Per accedere completamente alle risorse dopo l'accesso, Storage Explorer richiede sia la gestione (Azure Resource Manager) che le autorizzazioni del livello dati. Ciò significa che sono necessarie autorizzazioni di Azure Active Directory (Azure AD), che consentono di accedere all'account di archiviazione, ai contenitori nell'account e ai dati nei contenitori. Se si dispone di autorizzazioni solo al livello dati, è consigliabile [aggiungere una risorsa tramite Azure AD.](#add-a-resource-via-azure-ad) Per altre informazioni sulle autorizzazioni specifiche necessarie per Storage Explorer, vedere la [guida alla risoluzione dei problemi di Azure Storage Explorer.](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting#rbac-permissions-issues)

1. In Esplora archivi selezionare **Visualizza** > **gestione account** o il pulsante Gestisci **account.**

    ![Gestire account][1]

1. **ACCOUNT MANAGEMENT** ora visualizza tutti gli account di Azure a cui è stato effettuato l'accesso. Per connettersi a un altro account, selezionare **Aggiungi un account**.

1. In **Connetti ad Archiviazione di Azure**selezionare un cloud di Azure dall'ambiente Azure per accedere a un cloud nazionale o a uno stack di Azure.In Connect to Azure Storage , select an Azure cloud from Azure **environment** to sign in to a national cloud or an Azure Stack. Dopo aver scelto l'ambiente, selezionare **Avanti**.

    ![Opzione per l'accesso][2]

    Storage Explorer apre una pagina in cui accedere. Per altre informazioni, vedere [Connettere Esplora archivi a](/azure-stack/user/azure-stack-storage-connect-se)una sottoscrizione di Azure Stack o a un account di archiviazione.

1. Dopo aver eseguito correttamente l'accesso con un account Azure, l'account e le sottoscrizioni di Azure associate a tale account vengono visualizzati in **GESTIONE ACCOUNT**. Selezionare **Tutte le sottoscrizioni** per alternare la selezione tra tutte o nessuna delle sottoscrizioni di Azure elencate. Selezionare le sottoscrizioni di Azure da usare e quindi selezionare **Applica**.

    ![Selezionare le sottoscrizioni di Azure][3]

    **EXPLORER** visualizza gli account di archiviazione associati alle sottoscrizioni di Azure selezionate.

    ![Sottoscrizioni di Azure selezionate][4]

### <a name="attach-a-specific-resource"></a>Allegare una risorsa specifica

Esistono diversi modi per connettersi a una risorsa in Esplora archivi:There are several ways to attach to a resource in Storage Explorer:

* [Aggiungere una risorsa tramite Azure AD](#add-a-resource-via-azure-ad). Se si dispone di autorizzazioni solo al livello dati, usare questa opzione per aggiungere un contenitore BLOB o un contenitore di archiviazione BLOB Gen2 di Archiviazione dati di Azure Data Lake.If you have permissions only at the data layer, use this option to add a blob container or an Azure Data Lake Storage Gen2 BLOB storage container.
* [Utilizzare una stringa](#use-a-connection-string)di connessione . Usare questa opzione se si dispone di una stringa di connessione a un account di archiviazione. Storage Explorer supporta le stringhe di connessione della firma di [accesso condiviso](storage/common/storage-dotnet-shared-access-signature-part-1.md) e chiave.
* [Utilizzare un URI della firma](#use-a-shared-access-signature-uri)di accesso condiviso . Se si dispone di un URI della firma di [accesso condiviso](storage/common/storage-dotnet-shared-access-signature-part-1.md) a un contenitore BLOB, una condivisione file, una coda o una tabella, usarla per connettersi alla risorsa. Per ottenere un URI della firma di accesso condiviso, è possibile usare [Storage Explorer](#generate-a-sas-in-storage-explorer) o il portale di [Azure.](https://portal.azure.com)
* [Utilizzare un nome e una chiave](#use-a-name-and-key). Se si conoscono le chiavi dell'account di archiviazione, è possibile usare questa opzione per connettersi rapidamente. Trovare le chiavi nella pagina dell'account di archiviazione selezionando **Impostazioni** > **di accesso alle chiavi** nel portale di [Azure.](https://portal.azure.com)
* [Connettersi a un emulatore locale.](#attach-to-a-local-emulator) Se si usa uno degli emulatori di archiviazione di Azure disponibili, usare questa opzione per connettersi facilmente all'emulatore.
* [Connettersi a un account di database Cosmos](#connect-to-an-azure-cosmos-db-account-by-using-a-connection-string)di Azure usando una stringa di connessione. Utilizzare questa opzione se si dispone di una stringa di connessione a un'istanza CosmosDB.
* [Connettersi all'archivio dati di Azure in base all'URI](#connect-to-azure-data-lake-store-by-uri). Usare questa opzione se si ha un URI per Azure Data Lake Store.Use this option if you have a URI to Azure Data Lake Store.

#### <a name="add-a-resource-via-azure-ad"></a>Aggiungere una risorsa tramite Azure ADAdd a resource via Azure AD

1. Selezionare il simbolo **Connetti** per aprire **Connetti ad Archiviazione di Azure**.

    ![Opzione Connect to Azure storage (Connetti ad Archiviazione di Azure)][9]

1. Se non è già stato fatto, usare l'opzione **Aggiungi un account Azure** per accedere all'account Azure che ha accesso alla risorsa. Dopo aver eseguito l'accesso, tornare a **Connetti ad Archiviazione di Azure**.

1. Selezionare **Aggiungi una risorsa tramite Azure Active Directory (Azure AD)** e quindi selezionare **Avanti**.

1. Selezionare un account e un tenant di Azure.Select an Azure account and tenant. Questi valori devono avere accesso alla risorsa di archiviazione a cui si desidera connettersi. Fare clic su **Avanti**.

1. Scegliere il tipo di risorsa da collegare. Immettere le informazioni necessarie per la connessione. 

   Le informazioni immesse in questa pagina dipendono dal tipo di risorsa che si sta aggiungendo. Assicurarsi di scegliere il tipo di risorsa corretto. Dopo aver immesso le informazioni richieste, selezionare **Avanti**.

1. Esaminare il **Riepilogo connessioni** per assicurarsi che tutte le informazioni siano corrette. In caso affermativo, selezionare **Connetti**. In caso contrario, selezionare **Indietro** per tornare alle pagine precedenti per correggere eventuali informazioni errate.

Dopo aver aggiunto correttamente la connessione, l'albero delle risorse passa al nodo che rappresenta la connessione. La risorsa viene visualizzata in**Contenitori BLOB** **Local & Attached** > **Storage Accounts** > **(Attached Containers).** >  Se Storage Explorer non è riuscito ad aggiungere la connessione o se non è possibile accedere ai dati dopo aver aggiunto la connessione, vedere la guida alla [risoluzione dei problemi di Azure Storage Explorer](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting).

#### <a name="use-a-connection-string"></a>Usare una stringa di connessioneUse a connection string

1. Selezionare il simbolo **Connetti** per aprire **Connetti ad Archiviazione di Azure**.

    ![Opzione Connect to Azure storage (Connetti ad Archiviazione di Azure)][9]

1. Selezionare **Usa una stringa**di connessione , quindi scegliere **Avanti**.

1. Scegliere un nome visualizzato per la connessione e immettere la stringa di connessione. Selezionare quindi **Avanti**.

1. Esaminare il **Riepilogo connessioni** per assicurarsi che tutte le informazioni siano corrette. In caso affermativo, selezionare **Connetti**. In caso contrario, selezionare **Indietro** per tornare alle pagine precedenti per correggere eventuali informazioni errate.

Dopo aver aggiunto correttamente la connessione, l'albero delle risorse passa al nodo che rappresenta la connessione. La risorsa viene visualizzata in Account**di archiviazione** **collegati** > & locale . Se Storage Explorer non è riuscito ad aggiungere la connessione o se non è possibile accedere ai dati dopo aver aggiunto la connessione, vedere la guida alla [risoluzione dei problemi di Azure Storage Explorer](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting).

#### <a name="use-a-shared-access-signature-uri"></a>Usare un URI della firma di accesso condivisoUse a shared access signature URI

1. Selezionare il simbolo **Connetti** per aprire **Connetti ad Archiviazione di Azure**.

    ![Opzione Connect to Azure storage (Connetti ad Archiviazione di Azure)][9]

1. Selezionare **Usa URI firma di accesso condiviso**e quindi selezionare **Avanti**.

1. Scegliere un nome visualizzato per la connessione e immettere l'URI della firma di accesso condiviso. L'endpoint del servizio per il tipo di risorsa che si sta allegando deve essere riempito automaticamente. Se si usa un endpoint personalizzato, è possibile che non sia possibile. Fare clic su **Avanti**.

1. Esaminare il **Riepilogo connessioni** per assicurarsi che tutte le informazioni siano corrette. In caso affermativo, selezionare **Connetti**. In caso contrario, selezionare **Indietro** per tornare alle pagine precedenti per correggere eventuali informazioni errate.

Dopo aver aggiunto correttamente la connessione, l'albero delle risorse passa al nodo che rappresenta la connessione. La risorsa viene visualizzata in Account**di archiviazione** >  **collegati local &mente** > **(contenitori collegati)** > il nodo del*servizio per il tipo di contenitore collegato.* Se Storage Explorer non è riuscito ad aggiungere la connessione, vedere la guida alla [risoluzione dei problemi di Azure Storage Explorer](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting). Vedere la guida alla risoluzione dei problemi se non è possibile accedere ai dati dopo aver aggiunto correttamente la connessione.

#### <a name="use-a-name-and-key"></a>Usare un nome e una chiave

1. Selezionare il simbolo **Connetti** per aprire **Connetti ad Archiviazione di Azure**.

    ![Opzione Connect to Azure storage (Connetti ad Archiviazione di Azure)][9]

1. Selezionare **Usa un nome e una chiave dell'account di archiviazione**e quindi selezionare **Avanti**.

1. Scegliere un nome visualizzato per la connessione.

1. Immettere il nome dell'account di archiviazione e una delle relative chiavi di accesso.

1. Scegliere il **dominio di archiviazione** da utilizzare e quindi selezionare **Avanti**.

1. Esaminare il **Riepilogo connessioni** per assicurarsi che tutte le informazioni siano corrette. In caso affermativo, selezionare **Connetti**. In caso contrario, selezionare **Indietro** per tornare alle pagine precedenti per correggere eventuali informazioni errate.

Dopo aver aggiunto correttamente la connessione, l'albero delle risorse passa al nodo che rappresenta la connessione. La risorsa viene visualizzata in Account**di archiviazione** **collegati** > & locale . Se Storage Explorer non è riuscito ad aggiungere la connessione o se non è possibile accedere ai dati dopo aver aggiunto la connessione, vedere la guida alla [risoluzione dei problemi di Azure Storage Explorer](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting).

#### <a name="attach-to-a-local-emulator"></a>Connettersi a un emulatore locale

Storage Explorer supporta attualmente due emulatori di archiviazione ufficiali:

* [Emulatore di archiviazione di Azure](storage/common/storage-use-emulator.md) (solo Windows)Azure Storage emulator (Windows only)
* [Azurite](https://github.com/azure/azurite) (Windows, macOS o Linux)

Se l'emulatore è in ascolto sulle porte predefinite, è possibile usare il nodo **Emulatore - Porte predefinite** per accedere all'emulatore. Cercare **Emulatore - Porte predefinite** in Account**di archiviazione** **collegati** > & locale .

Se si desidera utilizzare un nome diverso per la connessione o se l'emulatore non è in esecuzione sulle porte predefinite, attenersi alla seguente procedura:

1. Avviare l'emulatore. Immettere `AzureStorageEmulator.exe status` il comando per visualizzare le porte per ogni tipo di servizio.

   > [!IMPORTANT]
   > Storage Explorer non avvia automaticamente l'emulatore. È necessario avviarlo manualmente.

1. Selezionare il simbolo **Connetti** per aprire **Connetti ad Archiviazione di Azure**.

    ![Opzione Connect to Azure storage (Connetti ad Archiviazione di Azure)][9]

1. Selezionare **Connetti a un emulatore locale**e quindi **Avanti**.

1. Scegliere un nome visualizzato per la connessione e immettere le porte su cui l'emulatore è in ascolto per ogni tipo di servizio. **Connetti a un emulatore locale** suggerisce i valori di porta predefiniti per la maggior parte degli emulatori. **La porta file** è vuota, perché nessuno degli emulatori ufficiali attualmente supporta il servizio File. Se l'emulatore in uso supporta File, è possibile immettere la porta da utilizzare. Selezionare quindi **Avanti**.

1. Esaminare il **Riepilogo connessioni** e assicurarsi che tutte le informazioni siano corrette. In caso affermativo, selezionare **Connetti**. In caso contrario, selezionare **Indietro** per tornare alle pagine precedenti per correggere eventuali informazioni errate.

Dopo aver aggiunto correttamente la connessione, l'albero delle risorse passa al nodo che rappresenta la connessione. Il nodo dovrebbe essere visualizzato in Account**di archiviazione** **collegati** > & locali . Se Storage Explorer non è riuscito ad aggiungere la connessione o se non è possibile accedere ai dati dopo aver aggiunto la connessione, vedere la guida alla [risoluzione dei problemi di Azure Storage Explorer](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting).

#### <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>Connettersi a un account Azure Cosmos DB usando una stringa di connessione

Invece di gestire gli account di Azure Cosmos DB tramite una sottoscrizione di Azure, è possibile connettersi a Azure Cosmos DB usando una stringa di connessione. Per eseguire la connessione, seguire questa procedura:

1. In **EXPLORER**, espandere **Local & allegati**, fare clic con il pulsante destro del mouse su Account database **cosmoe**quindi selezionare **Connetti a Database Cosmos di Azure**.

    ![Connettersi ad Azure Cosmos DB usando una stringa di connessione][21]

1. Selezionare l'API database Cosmos di Azure, immettere i dati della stringa di connessione e quindi selezionare OK per connettere l'account database Cosmos di Azure.Select the Azure Cosmos DB API, enter your **Connection String** data, and then select **OK** to connect the Azure Cosmos DB account. Per informazioni su come recuperare la stringa di connessione, vedere [Gestire un account Azure Cosmos.](https://docs.microsoft.com/azure/cosmos-db/manage-account)

    ![Stringa di connessione][22]

#### <a name="connect-to-azure-data-lake-store-by-uri"></a>Connettersi a Azure Data Lake Store mediante URI

È possibile accedere a una risorsa non presente nella sottoscrizione. È necessario un utente che abbia accesso alla risorsa per fornire l'URI della risorsa. Dopo aver eseguito l'accesso, connettersi a Data Lake Store utilizzando l'URI. Per eseguire la connessione, seguire questa procedura:

1. In **EXPLORER**espandere **Locale & Associato**.

1. Fare clic con il pulsante destro del mouse su **Data Lake Storage Gen1**e scegliere **Connetti a Data Lake Storage Gen1**.

    ![Connettersi al menu di scelta rapida di Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-connect-data-lake-storage.png)

1. Immettere l'URI e quindi selezionare **OK**. Il Data Lake Store viene visualizzato in **Data Lake Storage**.

    ![Connettersi al risultato di Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-attach-data-lake-finished.png)

In questo esempio viene utilizzata data Lake Storage Gen1. Azure Data Lake Storage Gen2 è ora disponibile. Per altre informazioni, vedere [Che cos'è Azure Data Lake Storage Gen1.](./data-lake-store/data-lake-store-overview.md)

## <a name="generate-a-shared-access-signature-in-storage-explorer"></a>Generare una firma di accesso condiviso in Esplora archiviGenerate a shared access signature in Storage Explorer<a name="generate-a-sas-in-storage-explorer"></a>

### <a name="account-level-shared-access-signature"></a>Firma di accesso condiviso a livello di account

1. Fare clic con il pulsante destro del mouse sull'account di archiviazione di cui si vuole condividere e quindi **scegliere Ottieni firma**di accesso condiviso .

    ![Opzione di menu contestuale Ottieni firma di accesso condiviso][14]

1. In **Firma di accesso condiviso**specificare l'intervallo di tempo e le autorizzazioni desiderate per l'account, quindi selezionare **Crea**.

    ![Ottenere una firma di accesso condivisoGet a shared access signature][15]

1. Copiare la **stringa di connessione** o la stringa **Query** non elaborata negli Appunti.

### <a name="service-level-shared-access-signature"></a>Firma di accesso condiviso a livello di servizioService level shared access signature

È possibile ottenere una firma di accesso condiviso a livello di servizio. Per altre informazioni, vedere [Ottenere la chiave di accesso utente per un contenitore BLOB.](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)

## <a name="search-for-storage-accounts"></a>Cercare gli account di archiviazione

Per trovare una risorsa di archiviazione, è possibile eseguire ricerche nel riquadro **EXPLORER.**

Quando si immette del testo nella casella di ricerca, Storage Explorer visualizza tutte le risorse che corrispondono al valore di ricerca immesso fino a quel punto. In questo esempio viene illustrata una ricerca di endpoint:This example shows a search for **endpoints**:

![Ricerca dell'account di archiviazione][23]

> [!NOTE]
> Per velocizzare la ricerca, utilizzare **Gestione account** per deselezionare tutte le sottoscrizioni che non contengono l'elemento che si sta cercando. È anche possibile fare clic con il pulsante destro del mouse su un nodo e selezionare **Cerca da qui** per avviare la ricerca da un nodo specifico.
>
>

## <a name="next-steps"></a>Passaggi successivi

* [Gestire le risorse di archiviazione BLOB di Azure con Esplora archiviManage Azure Blob storage resources with Storage Explorer](vs-azure-tools-storage-explorer-blobs.md)
* [Gestire i dati con Azure Storage Explorer](./cosmos-db/storage-explorer.md)
* [Gestire le risorse di Azure Data Lake Store con Storage Explorer](./data-lake-store/data-lake-store-in-storage-explorer.md)

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Overview.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ManageAccounts.png
[2]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-azure-storage-azure-environment.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/account-panel-subscriptions-apply.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SubscriptionNode.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog.png
[7]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/PortalAccessKeys.png
[8]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AccessKeys.png
[9]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog.png
[10]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-AddWithKeySelected.png
[11]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-NameAndKeyPage.png
[12]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithKeyAccount.png
[13]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithKeyAccount-Detach.png
[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/get-shared-access-signature-for-storage-explorer.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/create-shared-access-signature-for-storage-explorer.png
[16]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-WithConnStringOrSASSelected.png
[17]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-ConnStringOrSASPage-1.png
[18]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithSASAccount.png
[19]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-ConnStringOrSASPage-2.png
[20]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ServiceAttachedWithSAS.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-cosmos-db-by-connection-string.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connection-string-for-cosmos-db.png
[23]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-search-for-resource.png
