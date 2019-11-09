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
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73891369"
---
# <a name="get-started-with-storage-explorer"></a>Introduzione a Storage Explorer

## <a name="overview"></a>Panoramica

Microsoft Azure Storage Explorer è un'app autonoma che semplifica l'uso dei dati di archiviazione di Azure in Windows, macOS e Linux. Questo articolo illustra diversi modi per connettersi e gestire gli account di archiviazione di Azure.

![Microsoft Azure Storage Explorer][0]

## <a name="prerequisites"></a>Prerequisiti

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

Le seguenti versioni di Windows supportano Storage Explorer:

* Windows 10 (scelta consigliata)
* Windows 8
* Windows 7

Per tutte le versioni di Windows, Storage Explorer richiede .NET Framework 4.6.2 o versione successiva.

# <a name="macostabmacos"></a>[macOS](#tab/macos)

Le versioni seguenti del supporto macOS Storage Explorer:

* macOS 10,12 Sierra e versioni successive

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

Storage Explorer è disponibile nell' [Archivio di snap](https://snapcraft.io/storage-explorer) per le distribuzioni più comuni di Linux. Si consiglia di usare l'archivio di snap per questa installazione. Lo snap Storage Explorer installa tutte le relative dipendenze e gli aggiornamenti quando le nuove versioni vengono pubblicate nell'archivio di snap.

Per le distribuzioni supportate, vedere la pagina relativa all' [Installazione snap](https://snapcraft.io/docs/installing-snapd)-in.

Storage Explorer richiede l'uso di un gestore delle password. Potrebbe essere necessario connettersi manualmente a un gestore delle password. È possibile connettere Storage Explorer al gestore delle password del sistema eseguendo il comando seguente:

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

Storage Explorer è disponibile anche come download di *. tar. gz* . È necessario installare le dipendenze manualmente. Di seguito sono riportate le distribuzioni di Linux supportate dall'installazione di *. tar. gz* :

* Ubuntu 18,04 x64
* Ubuntu 16,04 x64
* Ubuntu 14.04 x64

L'installazione di *. tar. gz* potrebbe funzionare in altre distribuzioni, ma solo queste sono ufficialmente supportate.

Per ulteriori informazioni sull'installazione di Storage Explorer in Linux, vedere [dipendenze Linux](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting#linux-dependencies) nella Guida alla risoluzione dei problemi Azure Storage Explorer.

---

## <a name="download-and-install"></a>Download e installazione

Per scaricare e installare Storage Explorer, vedere [Azure Storage Explorer](https://www.storageexplorer.com).

## <a name="connect-to-a-storage-account-or-service"></a>Connettersi a un account o a un servizio di archiviazione

Storage Explorer offre numerosi modi per connettersi agli account di archiviazione. In generale, è possibile eseguire una delle operazioni seguenti:

* [Accedere ad Azure per accedere alle sottoscrizioni e alle relative risorse](#sign-in-to-azure)
* [Alleghi una risorsa di archiviazione o CosmosDB specifica](#attach-a-specific-resource)

### <a name="sign-in-to-azure"></a>Accedere ad Azure

> [!NOTE]
> Per accedere completamente alle risorse dopo l'accesso, Storage Explorer richiede le autorizzazioni sia per la gestione (Azure Resource Manager) che per il livello dati. Ciò significa che sono necessarie autorizzazioni di Azure Active Directory (Azure AD), che consentono di accedere all'account di archiviazione, ai contenitori nell'account e ai dati nei contenitori. Se si dispone di autorizzazioni solo a livello di dati, è consigliabile [aggiungere una risorsa tramite Azure ad](#add-a-resource-via-azure-ad). Per ulteriori informazioni sulle autorizzazioni specifiche Storage Explorer richieste, vedere la [Guida alla risoluzione dei problemi Azure Storage Explorer](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting#rbac-permissions-issues).

1. In Storage Explorer selezionare **visualizza** > **Gestione account** oppure selezionare il pulsante **Gestisci account** .

    ![Gestire account][1]

1. La **gestione degli account** ora Visualizza tutti gli account Azure a cui è stato effettuato l'accesso. Per connettersi a un altro account, selezionare **Aggiungi un account**.

1. In **Connetti ad archiviazione di Azure**selezionare un cloud di Azure dall' **ambiente Azure** per accedere a un cloud nazionale o a un Azure stack. Dopo aver scelto l'ambiente, fare clic su **Avanti**.

    ![Opzione per l'accesso][2]

    Storage Explorer apre una pagina per l'accesso. Per altre informazioni, vedere [connettere Storage Explorer a una sottoscrizione Azure stack o a un account di archiviazione](/azure-stack/user/azure-stack-storage-connect-se).

1. Dopo aver eseguito l'accesso con un account Azure, l'account e le sottoscrizioni di Azure associate a tale account vengono visualizzati in **Gestione account**. Selezionare **tutte le sottoscrizioni** per alternare la selezione tra tutte o nessuna delle sottoscrizioni di Azure elencate. Selezionare le sottoscrizioni di Azure da usare e quindi selezionare **Applica**.

    ![Selezionare le sottoscrizioni di Azure][3]

    **Explorer** Visualizza gli account di archiviazione associati alle sottoscrizioni di Azure selezionate.

    ![Sottoscrizioni di Azure selezionate][4]

### <a name="attach-a-specific-resource"></a>Alleghi una risorsa specifica

Esistono diversi modi per connettersi a una risorsa in Storage Explorer:

* [Aggiungere una risorsa tramite Azure ad](#add-a-resource-via-azure-ad). Se si hanno le autorizzazioni solo a livello di dati, usare questa opzione per aggiungere un contenitore BLOB o un contenitore di archiviazione BLOB Azure Data Lake Storage Gen2.
* [Usare una stringa di connessione](#use-a-connection-string). Usare questa opzione se si dispone di una stringa di connessione a un account di archiviazione. Storage Explorer supporta le stringhe di connessione per la [firma di accesso condiviso](storage/common/storage-dotnet-shared-access-signature-part-1.md) e la chiave.
* [Usare un URI di firma di accesso condiviso](#use-a-shared-access-signature-uri). Se si dispone di un [URI di firma di accesso condiviso](storage/common/storage-dotnet-shared-access-signature-part-1.md) per un contenitore BLOB, una condivisione file, una coda o una tabella, usarlo per connettersi alla risorsa. Per ottenere un URI di firma di accesso condiviso, è possibile usare [Storage Explorer](#generate-a-sas-in-storage-explorer) o l' [portale di Azure](https://portal.azure.com).
* [Usare un nome e una chiave](#use-a-name-and-key). Se si conosce una delle chiavi dell'account per l'account di archiviazione, è possibile usare questa opzione per connettersi rapidamente. Trovare le chiavi nella pagina dell'account di archiviazione selezionando **impostazioni** > **chiavi di accesso** nel [portale di Azure](https://portal.azure.com).
* [Connettersi a un emulatore locale](#attach-to-a-local-emulator). Se si usa uno degli emulatori di archiviazione di Azure disponibili, usare questa opzione per connettersi facilmente all'emulatore.
* [Connettersi a un account Azure Cosmos DB utilizzando una stringa di connessione](#connect-to-an-azure-cosmos-db-account-by-using-a-connection-string). Utilizzare questa opzione se si dispone di una stringa di connessione a un'istanza di CosmosDB.
* [Connettersi a Azure Data Lake Store in base all'URI](#connect-to-azure-data-lake-store-by-uri). Utilizzare questa opzione se si dispone di un URI per Azure Data Lake Store.

#### <a name="add-a-resource-via-azure-ad"></a>Aggiungere una risorsa tramite Azure AD

1. Selezionare il simbolo **Connetti** per aprire **Connetti ad archiviazione di Azure**.

    ![Opzione Connect to Azure storage (Connetti ad Archiviazione di Azure)][9]

1. Se non è già stato fatto, usare l'opzione **Aggiungi un account Azure** per accedere all'account di Azure che ha accesso alla risorsa. Dopo aver eseguito l'accesso, tornare a **connettersi ad archiviazione di Azure**.

1. Selezionare **Aggiungi una risorsa tramite Azure Active Directory (Azure ad)** e quindi fare clic su **Avanti**.

1. Selezionare un account e un tenant di Azure. Questi valori devono avere accesso alla risorsa di archiviazione a cui si vuole connettersi. Selezionare **Avanti**.

1. Scegliere il tipo di risorsa che si vuole alleghi. Immettere le informazioni necessarie per la connessione. 

   Le informazioni immesse in questa pagina dipendono dal tipo di risorsa che si sta aggiungendo. Assicurarsi di scegliere il tipo di risorsa corretto. Dopo aver immesso le informazioni necessarie, fare clic su **Avanti**.

1. Esaminare il **Riepilogo della connessione** per assicurarsi che tutte le informazioni siano corrette. In caso contrario, selezionare **Connetti**. In caso contrario, selezionare **indietro** per tornare alle pagine precedenti per correggere eventuali informazioni non corrette.

Una volta aggiunta la connessione, l'albero delle risorse viene indirizzato al nodo che rappresenta la connessione. La risorsa viene visualizzata in **Local & attached** > **account di archiviazione** >  **(contenitori collegati)**  > **contenitori BLOB**. Se Storage Explorer non è riuscito ad aggiungere la connessione o se non è possibile accedere ai dati dopo aver aggiunto correttamente la connessione, vedere la [Guida alla risoluzione dei problemi Azure Storage Explorer](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting).

#### <a name="use-a-connection-string"></a>Usa una stringa di connessione

1. Selezionare il simbolo **Connetti** per aprire **Connetti ad archiviazione di Azure**.

    ![Opzione Connect to Azure storage (Connetti ad Archiviazione di Azure)][9]

1. Selezionare **Usa una stringa di connessione**e quindi fare clic su **Avanti**.

1. Scegliere un nome visualizzato per la connessione e immettere la stringa di connessione. Quindi selezionare **Avanti**.

1. Esaminare il **Riepilogo della connessione** per assicurarsi che tutte le informazioni siano corrette. In caso contrario, selezionare **Connetti**. In caso contrario, selezionare **indietro** per tornare alle pagine precedenti per correggere eventuali informazioni non corrette.

Una volta aggiunta la connessione, l'albero delle risorse viene indirizzato al nodo che rappresenta la connessione. La risorsa viene visualizzata in **Local & attached** > **account di archiviazione**. Se Storage Explorer non è riuscito ad aggiungere la connessione o se non è possibile accedere ai dati dopo aver aggiunto correttamente la connessione, vedere la [Guida alla risoluzione dei problemi Azure Storage Explorer](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting).

#### <a name="use-a-shared-access-signature-uri"></a>Usa un URI di firma di accesso condiviso

1. Selezionare il simbolo **Connetti** per aprire **Connetti ad archiviazione di Azure**.

    ![Opzione Connect to Azure storage (Connetti ad Archiviazione di Azure)][9]

1. Selezionare **Usa un URI di firma di accesso condiviso (SAS)** , quindi fare clic su **Avanti**.

1. Scegliere un nome visualizzato per la connessione e immettere l'URI della firma di accesso condiviso. L'endpoint del servizio per il tipo di risorsa a cui si sta eseguendo il connessione deve essere automatico. Se si usa un endpoint personalizzato, potrebbe non essere possibile. Selezionare **Avanti**.

1. Esaminare il **Riepilogo della connessione** per assicurarsi che tutte le informazioni siano corrette. In caso contrario, selezionare **Connetti**. In caso contrario, selezionare **indietro** per tornare alle pagine precedenti per correggere eventuali informazioni non corrette.

Una volta aggiunta la connessione, l'albero delle risorse viene indirizzato al nodo che rappresenta la connessione. La risorsa viene visualizzata in **Local & attached** > **account di archiviazione** >  **(contenitori collegati)**  > *il nodo del servizio per il tipo di contenitore collegato*. Se Storage Explorer non è stato in grado di aggiungere la connessione, vedere la [Guida alla risoluzione dei problemi Azure Storage Explorer](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting). Vedere la guida alla risoluzione dei problemi se non è possibile accedere ai dati dopo aver aggiunto correttamente la connessione.

#### <a name="use-a-name-and-key"></a>Usare un nome e una chiave

1. Selezionare il simbolo **Connetti** per aprire **Connetti ad archiviazione di Azure**.

    ![Opzione Connect to Azure storage (Connetti ad Archiviazione di Azure)][9]

1. Selezionare **Usa nome e chiave dell'account di archiviazione**e quindi fare clic su **Avanti**.

1. Scegliere un nome visualizzato per la connessione.

1. Immettere il nome dell'account di archiviazione e una delle relative chiavi di accesso.

1. Scegliere il **dominio di archiviazione** da usare e quindi fare clic su **Avanti**.

1. Esaminare il **Riepilogo della connessione** per assicurarsi che tutte le informazioni siano corrette. In caso contrario, selezionare **Connetti**. In caso contrario, selezionare **indietro** per tornare alle pagine precedenti per correggere eventuali informazioni non corrette.

Una volta aggiunta la connessione, l'albero delle risorse viene indirizzato al nodo che rappresenta la connessione. La risorsa viene visualizzata in **Local & attached** > **account di archiviazione**. Se Storage Explorer non è riuscito ad aggiungere la connessione o se non è possibile accedere ai dati dopo aver aggiunto correttamente la connessione, vedere la [Guida alla risoluzione dei problemi Azure Storage Explorer](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting).

#### <a name="attach-to-a-local-emulator"></a>Connettersi a un emulatore locale

Storage Explorer supporta attualmente due emulatori di archiviazione ufficiali:

* [Emulatore di archiviazione di Azure](storage/common/storage-use-emulator.md) (solo Windows)
* [Azzurrite](https://github.com/azure/azurite) (Windows, MacOS o Linux)

Se l'emulatore è in ascolto sulle porte predefinite, è possibile usare il nodo **porte predefinite dell'emulatore** per accedere all'emulatore. Cercare le **porte predefinite dell'emulatore** in **& locale collegati** > **account di archiviazione**.

Se si vuole usare un nome diverso per la connessione o se l'emulatore non è in esecuzione sulle porte predefinite, seguire questa procedura:

1. Avviare l'emulatore. Immettere il comando `AzureStorageEmulator.exe status` per visualizzare le porte per ogni tipo di servizio.

   > [!IMPORTANT]
   > Storage Explorer non avvia automaticamente l'emulatore. È necessario avviarlo manualmente.

1. Selezionare il simbolo **Connetti** per aprire **Connetti ad archiviazione di Azure**.

    ![Opzione Connect to Azure storage (Connetti ad Archiviazione di Azure)][9]

1. Selezionare **Connetti a un emulatore locale**e quindi fare clic su **Avanti**.

1. Scegliere un nome visualizzato per la connessione e immettere le porte su cui è in ascolto l'emulatore per ogni tipo di servizio. **Connetti a un emulatore locale** suggerisce i valori di porta predefiniti per la maggior parte degli emulatori. La **porta file** è vuota, perché nessuno degli emulatori ufficiali supporta attualmente il servizio file. Se l'emulatore che si sta usando supporta i file, è possibile immettere la porta da usare. Quindi selezionare **Avanti**.

1. Esaminare il **Riepilogo della connessione** e assicurarsi che tutte le informazioni siano corrette. In caso contrario, selezionare **Connetti**. In caso contrario, selezionare **indietro** per tornare alle pagine precedenti per correggere eventuali informazioni non corrette.

Una volta aggiunta la connessione, l'albero delle risorse viene indirizzato al nodo che rappresenta la connessione. Il nodo verrà visualizzato in **Local & attached** > **account di archiviazione**. Se Storage Explorer non è riuscito ad aggiungere la connessione o se non è possibile accedere ai dati dopo aver aggiunto correttamente la connessione, vedere la [Guida alla risoluzione dei problemi Azure Storage Explorer](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting).

#### <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>Connettersi a un account Azure Cosmos DB usando una stringa di connessione

Anziché gestire gli account Azure Cosmos DB tramite una sottoscrizione di Azure, è possibile connettersi a Azure Cosmos DB usando una stringa di connessione. Per connettersi, attenersi alla seguente procedura:

1. In **Esplora**, espandere **& locali collegati**, fare clic con il pulsante destro del mouse su **account Cosmos DB**e selezionare **Connetti a Azure Cosmos DB**.

    ![Connettersi ad Azure Cosmos DB usando una stringa di connessione][21]

1. Selezionare l'API Azure Cosmos DB, immettere i dati della **stringa di connessione** e quindi fare clic su **OK** per connettere l'account Azure Cosmos DB. Per informazioni su come recuperare la stringa di connessione, vedere [gestire un account Azure Cosmos](https://docs.microsoft.com/azure/cosmos-db/manage-account).

    ![Stringa di connessione][22]

#### <a name="connect-to-azure-data-lake-store-by-uri"></a>Connettersi a Azure Data Lake Store mediante URI

È possibile accedere a una risorsa che non si trova nella sottoscrizione. È necessario un utente che abbia accesso a tale risorsa per fornire l'URI della risorsa. Dopo aver eseguito l'accesso, connettersi a Data Lake Store usando l'URI. Per connettersi, attenersi alla seguente procedura:

1. In **Esplora risorse**espandere **& locali collegati**.

1. Fare clic con il pulsante destro del mouse su **Data Lake storage Gen1**e scegliere **Connetti a data Lake storage Gen1**.

    ![Menu di scelta rapida Connetti a Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-connect-data-lake-storage.png)

1. Immettere l'URI, quindi fare clic su **OK**. Il Data Lake Store viene visualizzato sotto **Data Lake storage**.

    ![Connetti a risultato Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storage-explorer-attach-data-lake-finished.png)

In questo esempio viene usato Data Lake Storage Gen1. Azure Data Lake Storage Gen2 è ora disponibile. Per ulteriori informazioni, vedere [che cos'è Azure Data Lake storage Gen1](./data-lake-store/data-lake-store-overview.md).

## Generare una firma di accesso condiviso in Storage Explorer<a name="generate-a-sas-in-storage-explorer"></a>

### <a name="account-level-shared-access-signature"></a>Firma di accesso condiviso a livello di account

1. Fare clic con il pulsante destro del mouse sull'account di archiviazione che si vuole condividere e quindi scegliere **Ottieni firma di accesso condiviso**.

    ![Ottenere l'opzione del menu di scelta rapida firma di accesso condiviso][14]

1. In **firma di accesso condiviso**specificare l'intervallo di tempo e le autorizzazioni desiderate per l'account, quindi selezionare **Crea**.

    ![Ottenere una firma di accesso condiviso][15]

1. Copiare la **stringa di connessione** o la **stringa di query** non elaborata negli Appunti.

### <a name="service-level-shared-access-signature"></a>Firma di accesso condiviso a livello di servizio

È possibile ottenere una firma di accesso condiviso a livello di servizio. Per altre informazioni, vedere [ottenere la firma di accesso condiviso per un contenitore BLOB](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container).

## <a name="search-for-storage-accounts"></a>Cercare gli account di archiviazione

Per trovare una risorsa di archiviazione, è possibile cercare nel riquadro di **esplorazione** .

Quando si immette testo nella casella di ricerca, Storage Explorer Visualizza tutte le risorse che corrispondono al valore di ricerca immesso fino a quel momento. Questo esempio mostra una ricerca di **endpoint**:

![Ricerca dell'account di archiviazione][23]

> [!NOTE]
> Per velocizzare la ricerca, usare **Gestione account** per deselezionare le sottoscrizioni che non contengono l'elemento che si sta cercando. È anche possibile fare clic con il pulsante destro del mouse su un nodo e selezionare **Cerca da qui** per avviare la ricerca da un nodo specifico.
>
>

## <a name="next-steps"></a>Passaggi successivi

* [Gestire le risorse di archiviazione BLOB di Azure con Storage Explorer](vs-azure-tools-storage-explorer-blobs.md)
* [Utilizzare i dati utilizzando Azure Storage Explorer](./cosmos-db/storage-explorer.md)
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
