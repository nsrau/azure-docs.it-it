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
ms.openlocfilehash: f7dd6d3d30f34ba2c69b40111bb28d484ce572e7
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508747"
---
# <a name="get-started-with-storage-explorer"></a>Introduzione a Storage Explorer

## <a name="overview"></a>Panoramica

Azure Storage Explorer è un'app autonoma che consente di usare facilmente dati di Archiviazione di Azure in Windows, macOS e Linux. Questo articolo illustra diversi modi per connettersi agli account di archiviazione di Azure e per gestirli.

![Microsoft Azure Storage Explorer][0]

## <a name="prerequisites"></a>Prerequisiti

# <a name="windowstabwindows"></a>[Windows](#tab/windows)

Azure Storage Explorer è supportato nelle versioni seguenti di Windows:

* Windows 10 (scelta consigliata)
* Windows 8
* Windows 7

Per tutte le versioni di Windows è richiesto .NET Framework 4.6.2 o versione successiva.

[Scaricare e installare Storage Explorer](https://www.storageexplorer.com)

# <a name="macostabmacos"></a>[macOS](#tab/macos)

Azure Storage Explorer è supportato nelle versioni seguenti di macOS:

* macOS 10.12 "Sierra" e versioni successive

[Scaricare e installare Storage Explorer](https://www.storageexplorer.com)

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

Azure Storage Explorer è supportato nelle distribuzioni di Linux seguenti:

* Ubuntu 18.04 x64
* Ubuntu 16.04 x64
* Ubuntu 14.04 x64

Azure Storage Explorer può essere eseguito in altre distribuzioni, ma solo quelle sopra elencate sono ufficialmente supportate.

Per altre informazioni sull'installazione di Storage Explorer in Linux, vedere la [Troubleshooting Guide](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting#linux-dependencies).

Le [note sulla versione](https://go.microsoft.com/fwlink/?LinkId=838275&clcid=0x409) di Azure Storage Explorer contengono passaggi specifici per alcune distribuzioni.

[Scaricare e installare Storage Explorer](https://www.storageexplorer.com)

---

## <a name="connect-to-a-storage-account-or-service"></a>Connettersi a un account o a un servizio di archiviazione

Storage Explorer offre numerosi modi per connettersi agli account di archiviazione. In generale è possibile:

* [Accedere ad Azure per visualizzare le sottoscrizioni e le relative risorse](#sign-in-to-azure)
* [Collegare una risorsa di archiviazione o di COSMOS DB specifica](#attach-a-specific-resource)

### <a name="sign-in-to-azure"></a>Accedi ad Azure

> [!NOTE]
> Per un accesso completo alle risorse dopo l'accesso, Storage Explorer richiede la gestione (ARM) e le autorizzazioni di livello dati. Ciò significa che sono necessarie autorizzazioni di Azure AD che consentono di accedere al proprio account di archiviazione, i contenitori nell'account di e i dati nei contenitori. Se si dispone solo delle autorizzazioni a livello di dati, è consigliabile usare [Attach con Azure AD](#add-a-resource-via-azure-ad). Per altre informazioni su Storage Explorer richiede le autorizzazioni, vedere la [Guida alla risoluzione dei](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting?tabs=1804#role-based-access-control-permission-issues).
>
>

1. In Storage Explorer selezionare **Manage Accounts** (Gestisci account) per andare al **pannello Account Management** (Gestione account).

    ![Gestire account][1]

2. Il riquadro di sinistra mostra ora tutti gli account Azure a cui si è connessi. Per connettersi a un altro account, selezionare **Add an account** (Aggiungi un account).

3. Se si vuole accedere a un cloud nazionale o ad Azure Stack, fare clic sull'elenco a discesa **Azure environment** (Ambiente Azure) per selezionare il cloud di Azure da usare. Dopo avere scelto l'ambiente, fare clic sul pulsante **Sign in...** (Accedi). Se si effettua l'accesso ad Azure Stack, vedere [Connect Storage Explorer to an Azure Stack subscription](/azure-stack/user/azure-stack-storage-connect-se) (Connettere Storage Explorer a una sottoscrizione di Azure Stack) per altre informazioni.

    ![Opzione Sign In (Accedi)][2]

4. Dopo avere effettuato l'accesso con un account di Azure, l'account e le sottoscrizioni di Azure associate a quell'account vengono aggiunti nel pannello di sinistra. Selezionare le sottoscrizioni di Azure da usare e quindi selezionare **Apply** (Applica). Selezionando **All subscriptions:** (Tutte le sottoscrizioni) si alterna la selezione di tutte o di nessuna delle sottoscrizioni di Azure elencate.

    ![Selezionare le sottoscrizioni di Azure][3]

    Il riquadro sinistro mostra gli account di archiviazione associati alle sottoscrizioni di Azure selezionate.

    ![Sottoscrizioni di Azure selezionate][4]

### <a name="attach-a-specific-resource"></a>Collegare una risorsa specifica
    
Esistono diverse opzioni per associare una risorsa a Storage Explorer. È possibile:

* [Aggiungere una risorsa tramite Azure AD](#add-a-resource-via-azure-ad): Se si dispone solo delle autorizzazioni a livello di dati, è possibile utilizzare questa opzione per aggiungere un contenitore Blob o un contenitore Blob di Azure Data Lake Store Gen2.
* [Usare una stringa di connessione](#use-a-connection-string): Se si dispone di una stringa di connessione a un Account di archiviazione. Storage Explorer supporta entrambe le chiavi e [SAS](storage/common/storage-dotnet-shared-access-signature-part-1.md) le stringhe di connessione.
* [Usare un URI di firma di accesso condiviso](#use-a-sas-uri): Se si dispone di un [SAS](storage/common/storage-dotnet-shared-access-signature-part-1.md) URI a una condivisione File, coda, tabella o il contenitore Blob. Per ottenere un URI SAS, è possibile usare [Storage Explorer](#generate-a-sas-in-storage-explorer) o nella [portale di Azure](https://portal.azure.com).
* [Usare un nome e una chiave](#use-a-name-and-key): Se si conosce sia le chiavi dell'account all'Account di archiviazione, è possibile utilizzare questa opzione per connettersi rapidamente. Le chiavi dell'Account di archiviazione si trovano nell'Account di archiviazione **chiavi di accesso** Pannello di [portale di Azure](https://portal.azure.com).
* [Collegare a un emulatore locale](#attach-to-a-local-emulator): Se si usa uno degli emulatori di archiviazione di Azure disponibili, usare questa opzione per connettersi facilmente all'emulatore.
* [Connettersi a un account Azure Cosmos DB usando una stringa di connessione](#connect-to-an-azure-cosmos-db-account-by-using-a-connection-string): Se si dispone di una stringa di connessione a un'istanza di COSMOS DB.
* [Connettersi a Azure Data Lake Store tramite URI](#connect-to-azure-data-lake-store-by-uri): Se si dispone di un URI a un Store di Azure Data Lake.

#### <a name="add-a-resource-via-azure-ad"></a>Aggiungere una risorsa tramite Azure AD

1. Aprire il **finestra di dialogo Connect** facendo clic sul **pulsante Connetti** sulla sinistra, sulla barra degli strumenti verticale.

    ![Opzione Connect to Azure storage (Connetti ad Archiviazione di Azure)][9]

2. Se non è già stato fatto, usare il **aggiungere un Account Azure** possibilità di accedere all'Account di Azure che ha accesso alla risorsa. Dopo l'accesso in cambio per il **finestra di dialogo connessione**.

3. Selezionare il **aggiungere una risorsa tramite Azure Active Directory (Azure AD)** opzione e fare clic su **successivo**.

4. Selezionare l'Account di Azure che può accedere a cui si desidera collegare la risorsa di archiviazione e la sottoscrizione in cui la risorsa e quindi fare clic su **successivo**.

5. Scegliere il tipo di risorsa che si desidera collegare e quindi immettere le informazioni necessarie per la connessione. Gli input in questa pagina verranno modificato a seconda del tipo di risorsa che si sta aggiungendo. Assicurarsi di scegliere il tipo di risorsa corretto. Dopo aver inserito scegliere informazioni obbligatorie **successivo**.

6. Esaminare il riepilogo di connessione e assicurarsi che tutte le informazioni siano corrette. Se tutte le informazioni sono corretti fare clic su **Connect**, in caso contrario, tornare alle pagine precedenti con la **nuovamente** pulsante per correggere eventuali informazioni errate.

Dopo la connessione è stata aggiunta, l'albero delle risorse passerà automaticamente al nodo che rappresenta la connessione. Se per qualche motivo, non sembrano sotto **Attached & locale** → **account di archiviazione** → **(contenitori collegati)** → **contenitori Blob** . Se Storage Explorer è riuscito ad aggiungere la connessione o se è possibile accedere ai dati dopo aver aggiunto la connessione, consultare il [Guida alla risoluzione dei](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) per assistenza.

#### <a name="use-a-connection-string"></a>Usare una stringa di connessione

1. Aprire il **finestra di dialogo Connect** facendo clic sul **pulsante Connetti** sulla sinistra, sulla barra degli strumenti verticale.

    ![Opzione Connect to Azure storage (Connetti ad Archiviazione di Azure)][9]

2. Selezionare il **usare una stringa di connessione** opzione e fare clic su **successivo**.

3. Scegliere un nome visualizzato per la connessione e immettere la stringa di connessione. Quindi fare clic su **Next**.

4. Esaminare il riepilogo di connessione e assicurarsi che tutte le informazioni siano corrette. Se tutte le informazioni sono corretti fare clic su **Connect**, in caso contrario, tornare alle pagine precedenti con la **nuovamente** pulsante per correggere eventuali informazioni errate.

Dopo la connessione è stata aggiunta, l'albero delle risorse passerà automaticamente al nodo che rappresenta la connessione. Se per qualche motivo, non sembrano sotto **Attached & locale** → **account di archiviazione**. Se Storage Explorer è riuscito ad aggiungere la connessione o se è possibile accedere ai dati dopo aver aggiunto la connessione, consultare il [Guida alla risoluzione dei](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) per assistenza.

#### <a name="use-a-sas-uri"></a>Usare un URI di firma di accesso condiviso

1. Aprire il **finestra di dialogo Connect** facendo clic sul **pulsante Connetti** sulla sinistra, sulla barra degli strumenti verticale.

    ![Opzione Connect to Azure storage (Connetti ad Archiviazione di Azure)][9]

2. Selezionare il **usare una firma di accesso condiviso (SAS) URI** opzione e fare clic su **successivo**.

3. Scegliere un nome visualizzato per la connessione e immettere l'URI SAS. L'endpoint del servizio per il tipo di risorsa che si sta associando deve riempimento automatico. Se si usa un endpoint personalizzato è possibile che non possono. Fare clic su **Avanti**.

4. Esaminare il riepilogo di connessione e assicurarsi che tutte le informazioni siano corrette. Se tutte le informazioni sono corretti fare clic su **Connect**, in caso contrario, tornare alle pagine precedenti con la **nuovamente** pulsante per correggere eventuali informazioni errate.

Dopo la connessione è stata aggiunta, l'albero delle risorse passerà automaticamente al nodo che rappresenta la connessione. Se per qualche motivo, non sembrano sotto **Attached & locale** → **account di archiviazione** → **(contenitori collegati)** → **nodo del servizio per il tipo di contenitore sono stati collegati**. Se Storage Explorer è riuscito ad aggiungere la connessione o se è possibile accedere ai dati dopo aver aggiunto la connessione, consultare il [Guida alla risoluzione dei](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) per assistenza.

#### <a name="use-a-name-and-key"></a>Usare un nome e una chiave

1. Aprire il **finestra di dialogo Connect** facendo clic sul **pulsante Connetti** sulla sinistra, sulla barra degli strumenti verticale.

    ![Opzione Connect to Azure storage (Connetti ad Archiviazione di Azure)][9]

2. Selezionare il **usare un nome account di archiviazione e la chiave** opzione e fare clic su **successivo**.

3. Scegliere un nome visualizzato per la connessione.

4. Immettere il nome di account di archiviazione e una delle relative chiavi di accesso.

5. Scegliere il **dominio di archiviazione** da usare e quindi fare clic su **successivo**.

4. Esaminare il riepilogo di connessione e assicurarsi che tutte le informazioni siano corrette. Se tutte le informazioni sono corretti fare clic su **Connect**, in caso contrario, tornare alle pagine precedenti con la **nuovamente** pulsante per correggere eventuali informazioni errate.

Dopo la connessione è stata aggiunta, l'albero delle risorse passerà automaticamente al nodo che rappresenta la connessione. Se per qualche motivo, non sembrano sotto **Attached & locale** → **account di archiviazione**. Se Storage Explorer è riuscito ad aggiungere la connessione o se è possibile accedere ai dati dopo aver aggiunto la connessione, consultare il [Guida alla risoluzione dei](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) per assistenza.

#### <a name="attach-to-a-local-emulator"></a>Collegare a un emulatore locale

Storage Explorer supporta gli emulatori in tutte le piattaforme. I due attualmente ufficiale degli emulatori disponibili sono:
* [Emulatore di archiviazione di Azure](storage/common/storage-use-emulator.md) (solo Windows)
* [Azurite](https://github.com/azure/azurite) (Windows, macOS o Linux)

Se l'emulatore è in esecuzione sulle porte predefinite è possibile usare la **emulatore - porte predefinite** nodo, che è sempre disponibile in **Attached & locale** → **account di archiviazione** , per accedere rapidamente all'emulatore. Se si vuole usare un nome diverso per la connessione o se l'emulatore non è in esecuzione sulle porte predefinite, quindi seguire i passaggi riportati di seguito.

1. Avviare l'emulatore. Quando si, prendere nota di quali porte l'emulatore è in ascolto per ogni tipo di servizio. È necessario conoscere queste informazioni in un secondo momento.

   > [!IMPORTANT]
   > Storage Explorer non avvia automaticamente l'emulatore. È necessario avviarlo manualmente.

2. Aprire il **finestra di dialogo Connect** facendo clic sul **pulsante Connetti** sulla sinistra, sulla barra degli strumenti verticale.

    ![Opzione Connect to Azure storage (Connetti ad Archiviazione di Azure)][9]

3. Selezionare il **Collega a un emulatore locale** opzione e fare clic su **successivo**.

4. Scegliere un nome visualizzato per la connessione e immettere le porte che all'emulatore è in ascolto per ogni tipo di servizio. Per impostazione predefinita, le caselle di testo conterrà i valori di porta predefiniti per la maggior parte degli emulatori. Il **file porta** anche viene lasciato vuoto per impostazione predefinita perché nessuno degli emulatori ufficiali supporta attualmente il servizio file. Se l'emulatore in che uso lo supporta tuttavia, è possibile immettere la porta usata. Fare clic su **Avanti**.

5. Esaminare il riepilogo di connessione e assicurarsi che tutte le informazioni siano corrette. Se tutte le informazioni sono corretti fare clic su **Connect**, in caso contrario, tornare alle pagine precedenti con la **nuovamente** pulsante per correggere eventuali informazioni errate.

Dopo la connessione è stata aggiunta, l'albero delle risorse passerà automaticamente al nodo che rappresenta la connessione. Se per qualche motivo, non sembrano sotto **Attached & locale** → **account di archiviazione**. Se Storage Explorer è riuscito ad aggiungere la connessione o se è possibile accedere ai dati dopo aver aggiunto la connessione, consultare il [Guida alla risoluzione dei](https://docs.microsoft.com/azure/storage/common/storage-explorer-troubleshooting) per assistenza.

#### <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>Connettersi a un account Azure Cosmos DB usando una stringa di connessione

Oltre a gestire gli account Azure Cosmos DB tramite la sottoscrizione di Azure, un metodo alternativo per connettersi a un account Azure Cosmos DB consiste nell'usare una stringa di connessione. Usare la procedura seguente per connettersi mediante una stringa di connessione.

1. Individuare **Local and Attached** (Locali e connesse) nella struttura ad albero a sinistra, fare doppio clic su **Account Azure Cosmos DB**, quindi scegliere **Connect to Azure Cosmos DB...** (Connetti ad Azure Cosmos DB...)

    ![Connettersi ad Azure Cosmos DB usando una stringa di connessione][21]

2. Scegliere l'API Azure Cosmos DB, incollare la **stringa di connessione** e quindi fare clic su **OK** per connettere l'account Azure Cosmos DB. Per informazioni su come recuperare la stringa di connessione, vedere [Ottenere la stringa di connessione](https://docs.microsoft.com/azure/cosmos-db/manage-account).

    ![connection-string][22]

#### <a name="connect-to-azure-data-lake-store-by-uri"></a>Connettersi a Azure Data Lake Store mediante URI

Se si vuole accedere a risorse che non sono incluse nella propria sottoscrizione ma per le quali altri utenti concedono di ottenere l'URI, è possibile connettersi a Data Lake Store usando l'URI dopo aver effettuato l'accesso. Vedere la procedura seguente.

1. Aprire Esplora archivi.
2. Nel riquadro sinistro espandere **Local and Attached** (Locale e collegato).
3. Fare clic con il pulsante destro del mouse su **Data Lake Store** e scegliere **Connect to Data Lake Store** (Connetti a Data Lake Store) dal menu di scelta rapida.

    ![Menu di scelta rapida per la connessione a Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach.png)

4. Immettere l'URI. Lo strumento passerà quindi alla posizione dell'URL appena immesso.

    ![Finestra di dialogo contestuale per la connessione a Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach-dialog.png)

    ![Risultato della connessione a Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-attach-finish.png)


## <a name="generate-a-sas-in-storage-explorer"></a>Generare una firma di accesso condiviso in Storage Explorer

### <a name="account-level-sas"></a>A livello di firma di accesso condiviso dell'account

1. Fare doppio clic su account di archiviazione a cui si vuole condividere e quindi selezionare **Ottieni firma di accesso condiviso...** .

    ![Menu di scelta rapida Get SAS (Ottieni firma di accesso condiviso)][14]

2. Nella finestra di dialogo **Generate Shared Access Signature** (Genera firma di accesso condiviso) specificare l'intervallo di tempo e le autorizzazioni per l'account e quindi fare clic sul pulsante **Create** (Crea).

    ![Finestra di dialogo Get SAS (Ottieni firma di accesso condiviso)][15]

3. È ora possibile copiare il **stringa di connessione** o non elaborato **stringa di Query** negli Appunti.

### <a name="service-level-sas"></a>Firma di accesso condiviso a livello di servizio

[Come ottenere una firma di accesso condiviso per un contenitore blob in Storage Explorer](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)

## <a name="search-for-storage-accounts"></a>Cercare gli account di archiviazione

Se è necessario trovare una risorsa di archiviazione ma non se ne conosce la posizione, è possibile usare la casella di ricerca nella parte superiore del riquadro di sinistra per cercare la risorsa.

Mentre si digita nella casella di ricerca, il riquadro di sinistra mostra tutte le risorse che corrispondono al valore di ricerca immesso fino a quel momento. Ad esempio, nella schermata seguente è visualizzata la ricerca di **endpoints**:

![Ricerca dell'account di archiviazione][23]

> [!NOTE]
> Usare il **pannello Account Management** (Gestione account) per deselezionare le sottoscrizioni che non contengono l'elemento che si sta cercando in modo da migliorare il tempo di esecuzione della ricerca. È anche possibile fare clic con il pulsante destro del mouse su un nodo e scegliere **Search From Here** (Esegui ricerca da qui) per avviare la ricerca da un nodo specifico.
>
>

## <a name="next-steps"></a>Passaggi successivi

* [Gestire le risorse di archiviazione BLOB di Azure con Storage Explorer](vs-azure-tools-storage-explorer-blobs.md)
* [Gestire Azure Cosmos DB in Azure Storage Explorer (anteprima)](./cosmos-db/storage-explorer.md)
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
