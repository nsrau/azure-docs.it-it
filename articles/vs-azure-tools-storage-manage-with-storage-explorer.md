---
title: Introduzione a Storage Explorer | Microsoft Docs
description: Gestire le risorse di Archiviazione di Azure con Storage Explorer
services: storage
author: cawaMS
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.date: 04/22/2019
ms.author: cawa
ms.openlocfilehash: 43e76b9331eb8dbe95265810b9191a10d4caee08
ms.sourcegitcommit: a6718e2b0251b50f1228b1e13a42bb65e7bf7ee2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71272016"
---
# <a name="get-started-with-storage-explorer"></a>Introduzione a Storage Explorer

## <a name="overview"></a>Panoramica

Microsoft Azure Storage Explorer è un'app autonoma che semplifica l'uso dei dati di archiviazione di Azure in Windows, macOS e Linux. Questo articolo illustra diversi modi per connettersi e gestire gli account di archiviazione di Azure.

![Microsoft Azure Storage Explorer][0]

## <a name="prerequisites"></a>Prerequisiti

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

Storage Explorer è supportato nelle seguenti versioni di Windows:

* Windows 10 (scelta consigliata)
* Windows 8
* Windows 7

Per tutte le versioni di Windows, è necessario .NET Framework 4.6.2 o versione successiva.

# <a name="macostabmacos"></a>[macOS](#tab/macos)

Storage Explorer è supportato nelle versioni seguenti di macOS:

* macOS 10.12 "Sierra" e versioni successive

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

Storage Explorer è disponibile nell' [Archivio di snap](https://snapcraft.io/storage-explorer) per le distribuzioni più comuni di Linux ed è il metodo consigliato per l'installazione. Lo snap Storage Explorer installa automaticamente tutte le dipendenze e gli aggiornamenti quando le nuove versioni vengono pubblicate nell'archivio di snap.

Per un elenco delle distribuzioni supportate, passare alla [pagina di installazione dello snap](https://snapcraft.io/docs/installing-snapd)-in.

Storage Explorer richiede l'uso di un gestore delle password, che potrebbe essere necessario connettersi manualmente prima che Storage Explorer funzioni correttamente. È possibile connettere Storage Explorer al gestore delle password del sistema eseguendo il comando seguente:

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

Storage Explorer è disponibile anche come download. tar. gz, ma è necessario installare le dipendenze manualmente. L'installazione di. tar. gz è supportata nelle distribuzioni di Linux seguenti:

* Ubuntu 18.04 x64
* Ubuntu 16.04 x64
* Ubuntu 14.04 x64

L'installazione di. tar. gz potrebbe funzionare in altre distribuzioni, ma solo queste sono ufficialmente supportate.

Per ulteriori informazioni sull'installazione di Storage Explorer in Linux, vedere la [Guida alla risoluzione dei problemi](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting#linux-dependencies).

---

## <a name="download-and-install"></a>Scarica e installa

[Scaricare e installare Storage Explorer](https://www.storageexplorer.com)

## <a name="connect-to-a-storage-account-or-service"></a>Connettersi a un account o a un servizio di archiviazione

Storage Explorer offre numerosi modi per connettersi agli account di archiviazione. In generale, è possibile eseguire una delle operazioni seguenti:

* [Accedere ad Azure per accedere alle sottoscrizioni e alle relative risorse](#sign-in-to-azure)
* [Alleghi una risorsa di archiviazione o CosmosDB specifica](#attach-a-specific-resource)

### <a name="sign-in-to-azure"></a>Accedi ad Azure

> [!NOTE]
> Per accedere completamente alle risorse dopo l'accesso, Storage Explorer richiede le autorizzazioni sia per la gestione (Azure Resource Manager) che per il livello dati. Ciò significa che sono necessarie autorizzazioni di Azure Active Directory (Azure AD), che consentono di accedere all'account di archiviazione, ai contenitori nell'account e ai dati nei contenitori. Se si dispone di autorizzazioni solo a livello di dati, è consigliabile [aggiungere una risorsa tramite Azure ad](#add-a-resource-via-azure-ad). Per ulteriori informazioni sulle autorizzazioni specifiche Storage Explorer richieste, vedere la [Guida alla risoluzione dei problemi](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting#rbac-permissions-issues).

1. In Storage Explorer selezionare **Gestisci account** per passare al pannello di gestione account.

    ![Gestisci account][1]

2. Il riquadro di sinistra mostra ora tutti gli account Azure a cui si è connessi. Per connettersi a un altro account, selezionare **Aggiungi un account**.

3. Per accedere a un cloud nazionale o a una Azure Stack, selezionare l'elenco a discesa **ambiente Azure** per scegliere il cloud di Azure che si vuole usare. Dopo aver scelto l'ambiente, selezionare il pulsante **di accesso** . Per ulteriori informazioni, vedere [la pagina relativa alla connessione Storage Explorer a una sottoscrizione di Azure stack](/azure-stack/user/azure-stack-storage-connect-se).

    ![Opzione di accesso][2]

4. Dopo avere effettuato l'accesso con un account di Azure, l'account e le sottoscrizioni di Azure associate a quell'account vengono aggiunti nel pannello di sinistra. Selezionare le sottoscrizioni di Azure da usare e quindi selezionare **Applica**. Selezionando **tutte le sottoscrizioni** , la selezione viene alternata tra tutte o nessuna delle sottoscrizioni di Azure elencate.

    ![Selezionare le sottoscrizioni di Azure][3]

    Il riquadro sinistro mostra gli account di archiviazione associati alle sottoscrizioni di Azure selezionate.

    ![Sottoscrizioni di Azure selezionate][4]

### <a name="attach-a-specific-resource"></a>Alleghi una risorsa specifica

Esistono diversi modi per connettersi a una risorsa in Storage Explorer:

* [Aggiungere una risorsa tramite Azure ad](#add-a-resource-via-azure-ad). Se si hanno le autorizzazioni solo a livello di dati, usare questa opzione per aggiungere un contenitore BLOB o un contenitore di archiviazione BLOB Azure Data Lake Storage Gen2.
* [Usare una stringa di connessione](#use-a-connection-string). Usare questa opzione se si dispone di una stringa di connessione a un account di archiviazione. Storage Explorer supporta [sia le stringhe](storage/common/storage-dotnet-shared-access-signature-part-1.md) di connessione chiave che la firma di accesso condiviso.
* [Usare un URI di firma di](#use-a-sas-uri)accesso condiviso. Se si dispone di un [URI](storage/common/storage-dotnet-shared-access-signature-part-1.md) di firma di accesso condiviso per un contenitore BLOB, una condivisione file, una coda o una tabella, usarlo per connettersi alla risorsa. Per ottenere un URI di firma di accesso condiviso, è possibile usare [Storage Explorer](#generate-a-sas-in-storage-explorer) o l' [portale di Azure](https://portal.azure.com).
* [Usare un nome e una chiave](#use-a-name-and-key). Se si conosce una delle chiavi dell'account per l'account di archiviazione, è possibile usare questa opzione per connettersi rapidamente. Le chiavi per l'account di archiviazione si trovano nel pannello **chiavi di accesso** dell'account di archiviazione nel [portale di Azure](https://portal.azure.com).
* [Connettersi a un emulatore locale](#attach-to-a-local-emulator). Se si usa uno degli emulatori di archiviazione di Azure disponibili, usare questa opzione per connettersi facilmente all'emulatore.
* [Connettersi a un account Azure Cosmos DB utilizzando una stringa di connessione](#connect-to-an-azure-cosmos-db-account-by-using-a-connection-string). Utilizzare questa opzione se si dispone di una stringa di connessione a un'istanza di CosmosDB.
* [Connettersi a Azure Data Lake Store in base all'URI](#connect-to-azure-data-lake-store-by-uri). Utilizzare questa opzione se si dispone di un URI per Azure Data Lake Store.

#### <a name="add-a-resource-via-azure-ad"></a>Aggiungere una risorsa tramite Azure AD

1. Aprire la finestra di dialogo **Connetti** selezionando il pulsante **Connetti** sulla barra degli strumenti verticale a sinistra:

    ![Opzione Connect to Azure storage (Connetti ad Archiviazione di Azure)][9]

2. Se non è già stato fatto, usare l'opzione **Aggiungi un account Azure** per accedere all'account di Azure che ha accesso alla risorsa. Dopo aver eseguito l'accesso, tornare alla finestra di dialogo **Connetti** .

3. Selezionare **Aggiungi una risorsa tramite Azure Active Directory (Azure ad)** e quindi fare clic su **Avanti**.

4. Selezionare l'account di Azure e il tenant che hanno accesso alla risorsa di archiviazione a cui si vuole connettersi. Selezionare **Avanti**.

5. Scegliere il tipo di risorsa che si desidera collegare, quindi immettere le informazioni necessarie per la connessione. Le informazioni immesse in questa pagina dipendono dal tipo di risorsa che si sta aggiungendo. Assicurarsi di scegliere il tipo di risorsa corretto. Dopo aver immesso le informazioni necessarie, fare clic su **Avanti**.

6. Esaminare il riepilogo della connessione e assicurarsi che tutte le informazioni siano corrette. In caso contrario, selezionare **Connetti**. In caso contrario, utilizzare il pulsante **indietro** per tornare alle pagine precedenti per correggere eventuali informazioni non corrette.

Dopo che la connessione è stata aggiunta correttamente, l'albero delle risorse passa automaticamente al nodo che rappresenta la connessione. Se non passa a quel nodo, Cerca in**contenitori BLOB**di**account** > di archiviazione >  **associati & locali** **(contenitori collegati)**  > . Se Storage Explorer non è stato in grado di aggiungere la connessione o se non è possibile accedere ai dati dopo aver aggiunto correttamente la connessione, vedere la [Guida alla risoluzione dei problemi](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting).

#### <a name="use-a-connection-string"></a>Usa stringa di connessione

1. Aprire la finestra di dialogo **Connetti** selezionando il pulsante **Connetti** sulla barra degli strumenti verticale a sinistra:

    ![Opzione Connect to Azure storage (Connetti ad Archiviazione di Azure)][9]

2. Selezionare **Usa una stringa di connessione**e quindi fare clic su **Avanti**.

3. Scegliere un nome visualizzato per la connessione e immettere la stringa di connessione. Quindi selezionare **Avanti**.

4. Esaminare il riepilogo della connessione e assicurarsi che tutte le informazioni siano corrette. In caso contrario, selezionare **Connetti**. In caso contrario, utilizzare il pulsante **indietro** per tornare alle pagine precedenti per correggere eventuali informazioni non corrette.

Dopo che la connessione è stata aggiunta correttamente, l'albero delle risorse passa automaticamente al nodo che rappresenta la connessione. Se non passa a quel nodo, Cerca in **locale &**  > **account di archiviazione**collegati. Se Storage Explorer non è stato in grado di aggiungere la connessione o se non è possibile accedere ai dati dopo aver aggiunto correttamente la connessione, vedere la [Guida alla risoluzione dei problemi](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting).

#### <a name="use-a-sas-uri"></a>Usa un URI di firma di accesso condiviso

1. Aprire la finestra di dialogo **Connetti** selezionando il pulsante **Connetti** sulla barra degli strumenti verticale a sinistra:

    ![Opzione Connect to Azure storage (Connetti ad Archiviazione di Azure)][9]

2. Selezionare **Usa un URI di firma di accesso condiviso (SAS)** , quindi fare clic su **Avanti**.

3. Scegliere un nome visualizzato per la connessione e immettere l'URI di firma di accesso condiviso. L'endpoint del servizio per il tipo di risorsa a cui si sta eseguendo il connessione deve essere automatico. Se si usa un endpoint personalizzato, potrebbe non essere possibile. Selezionare **Avanti**.

4. Esaminare il riepilogo della connessione e assicurarsi che tutte le informazioni siano corrette. In caso contrario, selezionare **Connetti**. In caso contrario, utilizzare il pulsante **indietro** per tornare alle pagine precedenti per correggere eventuali informazioni non corrette.

Dopo che la connessione è stata aggiunta correttamente, l'albero delle risorse passa automaticamente al nodo che rappresenta la connessione. Se non passa a quel nodo, Cerca in **Local & Attached** > **Storage Accounts** >  **(contenitori collegati)**  > *il nodo del servizio per il tipo di contenitore collegato*. Se Storage Explorer non è stato in grado di aggiungere la connessione o se non è possibile accedere ai dati dopo aver aggiunto correttamente la connessione, vedere la [Guida alla risoluzione dei problemi](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting).

#### <a name="use-a-name-and-key"></a>Usare un nome e una chiave

1. Aprire la finestra di dialogo **Connetti** selezionando il pulsante **Connetti** sulla barra degli strumenti verticale a sinistra:

    ![Opzione Connect to Azure storage (Connetti ad Archiviazione di Azure)][9]

2. Selezionare **Usa nome e chiave dell'account di archiviazione**e quindi fare clic su **Avanti**.

3. Scegliere un nome visualizzato per la connessione.

4. Immettere il nome dell'account di archiviazione e una delle relative chiavi di accesso.

5. Scegliere il **dominio di archiviazione** da usare e quindi fare clic su **Avanti**.

6. Esaminare il riepilogo della connessione e assicurarsi che tutte le informazioni siano corrette. In caso contrario, selezionare **Connetti**. In caso contrario, utilizzare il pulsante **indietro** per tornare alle pagine precedenti per correggere eventuali informazioni non corrette.

Dopo che la connessione è stata aggiunta correttamente, l'albero delle risorse passa automaticamente al nodo che rappresenta la connessione. Se non passa a quel nodo, Cerca in **locale &**  > **account di archiviazione**collegati. Se Storage Explorer non è stato in grado di aggiungere la connessione o se non è possibile accedere ai dati dopo aver aggiunto correttamente la connessione, vedere la [Guida alla risoluzione dei problemi](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting).

#### <a name="attach-to-a-local-emulator"></a>Connettersi a un emulatore locale

Storage Explorer supporta attualmente due emulatori di archiviazione ufficiali:
* [Emulatore di archiviazione di Azure](storage/common/storage-use-emulator.md) (Solo Windows)
* [Azzurrite](https://github.com/azure/azurite) (Windows, macOS o Linux)

Se l'emulatore è in ascolto sulle porte predefinite, è possibile usare il nodo delle **porte predefinite dell'emulatore** (disponibile in**account di archiviazione** **locali & collegati** > ) per accedere rapidamente all'emulatore.

Se si vuole usare un nome diverso per la connessione o se l'emulatore non è in esecuzione sulle porte predefinite, seguire questa procedura:

1. Avviare l'emulatore. Quando si esegue questa operazione, prendere nota delle porte su cui è in ascolto l'emulatore per ogni tipo di servizio.

   > [!IMPORTANT]
   > Storage Explorer non avvia automaticamente l'emulatore. È necessario avviarlo manualmente.

2. Aprire la finestra di dialogo **Connetti** selezionando il pulsante **Connetti** sulla barra degli strumenti verticale a sinistra:

    ![Opzione Connect to Azure storage (Connetti ad Archiviazione di Azure)][9]

3. Selezionare **Connetti a un emulatore locale**e quindi fare clic su **Avanti**.

4. Scegliere un nome visualizzato per la connessione e immettere le porte su cui è in ascolto l'emulatore per ogni tipo di servizio. Le caselle di testo inizieranno con i valori di porta predefiniti per la maggior parte degli emulatori. La casella **file porta** viene lasciata vuota, perché nessuno degli emulatori ufficiali supporta attualmente il servizio file. Se l'emulatore che si sta usando supporta i file, è possibile immettere la porta usata. Quindi selezionare **Avanti**.

5. Esaminare il riepilogo della connessione e assicurarsi che tutte le informazioni siano corrette. In caso contrario, selezionare **Connetti**. In caso contrario, utilizzare il pulsante **indietro** per tornare alle pagine precedenti per correggere eventuali informazioni non corrette.

Dopo che la connessione è stata aggiunta correttamente, l'albero delle risorse passa automaticamente al nodo che rappresenta la connessione. Se non passa a quel nodo, Cerca in **locale &**  > **account di archiviazione**collegati. Se Storage Explorer non è stato in grado di aggiungere la connessione o se non è possibile accedere ai dati dopo aver aggiunto correttamente la connessione, vedere la [Guida alla risoluzione dei problemi](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting).

#### <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>Connettersi a un account Azure Cosmos DB usando una stringa di connessione

In alternativa alla gestione di account Azure Cosmos DB tramite una sottoscrizione di Azure, è anche possibile connettersi a Azure Cosmos DB utilizzando una stringa di connessione. A tale scopo, effettuare le operazioni seguenti:

1. Sul lato sinistro dell'albero delle risorse espandere **locale e collegato**, fare clic con il pulsante destro del mouse su **account Azure Cosmos DB**e selezionare **Connetti a Azure Cosmos DB**.

    ![Connettersi ad Azure Cosmos DB usando una stringa di connessione][21]

2. Selezionare l'API Azure Cosmos DB, immettere i dati della **stringa di connessione** e quindi fare clic su **OK** per connettere l'account Azure Cosmos DB. Per informazioni su come recuperare la stringa di connessione, vedere [ottenere la stringa di connessione](https://docs.microsoft.com/azure/cosmos-db/manage-account).

    ![Stringa di connessione][22]

#### <a name="connect-to-azure-data-lake-store-by-uri"></a>Connettersi a Azure Data Lake Store mediante URI

Se si vuole accedere a una risorsa che non si trova nella sottoscrizione, sarà necessario qualcuno che possa accedere a tale risorsa per fornire l'URI della risorsa. Dopo aver eseguito l'accesso, è possibile connettersi a Data Lake Store tramite l'URI. A tale scopo, effettuare le operazioni seguenti:

1. Aprire Esplora archivi.
2. Nel riquadro sinistro espandere **Local and Attached** (Locale e collegato).
3. Fare clic con il pulsante destro del mouse su **Data Lake Store**. Scegliere **Connetti a data Lake Store**dal menu di scelta rapida.

    ![menu di scelta rapida Connetti a Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach.png)

4. Immettere l'URI. Lo strumento passa alla posizione dell'URL appena immesso.

    ![Finestra di dialogo Connetti a contesto Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach-dialog.png)

    ![Connetti a risultato Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-attach-finish.png)


## <a name="generate-a-sas-in-storage-explorer"></a>Generare una firma di accesso condiviso in Storage Explorer

### <a name="account-level-sas"></a>Firma di accesso condiviso a livello di account

1. Fare clic con il pulsante destro del mouse sull'account di archiviazione che si vuole condividere e quindi scegliere **Ottieni firma di accesso condiviso**.

    ![Menu di scelta rapida Get SAS (Ottieni firma di accesso condiviso)][14]

2. Nella finestra di dialogo **genera firma di accesso condiviso** specificare l'intervallo di tempo e le autorizzazioni desiderate per l'account, quindi selezionare **Crea**.

    ![Finestra di dialogo Get SAS (Ottieni firma di accesso condiviso)][15]

3. È ora possibile copiare la **stringa di connessione** o la **stringa di query** non elaborata negli Appunti.

### <a name="service-level-sas"></a>Firma di accesso condiviso a livello di servizio

[Come ottenere una firma di accesso condiviso per un contenitore BLOB in Storage Explorer](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)

## <a name="search-for-storage-accounts"></a>Cercare gli account di archiviazione

Se è necessario trovare una risorsa di archiviazione e non si sa dove si trova, è possibile usare la casella di ricerca nella parte superiore del riquadro sinistro per cercare la risorsa.

Mentre si digita nella casella di ricerca, il riquadro di sinistra mostra tutte le risorse che corrispondono al valore di ricerca immesso fino a quel momento. Ad esempio, nella schermata seguente è visualizzata la ricerca di **endpoints**:

![Ricerca dell'account di archiviazione][23]

> [!NOTE]
> Per velocizzare la ricerca, usare il pannello Gestione account per deselezionare le sottoscrizioni che non contengono l'elemento che si sta cercando. È anche possibile fare clic con il pulsante destro del mouse su un nodo e selezionare **Cerca da qui** per avviare la ricerca da un nodo specifico.
>
>

## <a name="next-steps"></a>Passaggi successivi

* [Gestire le risorse di archiviazione BLOB di Azure con Storage Explorer](vs-azure-tools-storage-explorer-blobs.md)
* [Gestisci Azure Cosmos DB in Storage Explorer (anteprima)](./cosmos-db/storage-explorer.md)
* [Gestire le risorse di Azure Data Lake Store con Storage Explorer](./data-lake-store/data-lake-store-in-storage-explorer.md)

[0]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Overview.png
[1]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ManageAccounts.png
[2]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-SignInSelected.png
[3]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AccountPanel.png
[4]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SubscriptionNode.png
[5]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog.png
[7]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/PortalAccessKeys.png
[8]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AccessKeys.png
[9]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog.png
[10]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-AddWithKeySelected.png
[11]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-NameAndKeyPage.png
[12]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithKeyAccount.png
[13]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithKeyAccount-Detach.png
[14]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/GetSharedAccessSignature.png
[15]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/SharedAccessSignatureDialog.png
[16]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-WithConnStringOrSASSelected.png
[17]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-ConnStringOrSASPage-1.png
[18]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/AttachedWithSASAccount.png
[19]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ConnectDialog-ConnStringOrSASPage-2.png
[20]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/ServiceAttachedWithSAS.png
[21]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connect-to-db-by-connection-string.png
[22]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/connection-string.png
[23]: ./media/vs-azure-tools-storage-manage-with-storage-explorer/Search.png
