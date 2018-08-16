---
title: Introduzione a Storage Explorer | Microsoft Docs
description: Gestire le risorse di Archiviazione di Azure con Storage Explorer
services: storage
documentationcenter: na
author: cawa
manager: paulyuk
editor: ''
ms.assetid: 1ed0f096-494d-49c4-ab71-f4164ee19ec8
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2017
ms.author: cawa
ms.openlocfilehash: 329653e7494d2f993acb462d7d989db07a18f790
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/07/2018
ms.locfileid: "39600864"
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

[Scaricare e installare Storage Explorer](http://www.storageexplorer.com)

# <a name="macostabmacos"></a>[macOS](#tab/macos)

Azure Storage Explorer è supportato nelle versioni seguenti di macOS:

* macOS 10.12 "Sierra" e versioni successive

[Scaricare e installare Storage Explorer](http://www.storageexplorer.com)

# <a name="linuxtablinux"></a>[Linux](#tab/linux)

Azure Storage Explorer è supportato nelle distribuzioni di Linux seguenti:

* Ubuntu 16.04 x64 (scelta consigliata)
* Ubuntu 17.10 x64
* Ubuntu 14.04 x64

Azure Storage Explorer può essere eseguito in altre distribuzioni, ma solo quelle sopra elencate sono ufficialmente supportate.

Per eseguire Azure Storage Explorer in Linux, è inoltre necessario disporre delle dipendenze/librerie seguenti installate:

* [.NET Core 2.x](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x)
* libsecret (Nota: libsecret 1.so.0 deve essere disponibile nel computer. Se è disponibile una diversa versione di libsecret installata, è possibile provare un collegamento flessibile del file con estensione so a libsecret-1.so.0)
* libgconf-2-4
* GCC aggiornato

Le [note sulla versione](https://go.microsoft.com/fwlink/?LinkId=838275&clcid=0x409) di Azure Storage Explorer contengono passaggi specifici per alcune distribuzioni.

[Scaricare e installare Storage Explorer](http://www.storageexplorer.com)

---

## <a name="connect-to-a-storage-account-or-service"></a>Connettersi a un account o a un servizio di archiviazione

Storage Explorer offre numerosi modi per connettersi agli account di archiviazione. Ad esempio, è possibile:

* Connettersi agli account di archiviazione associati alle sottoscrizioni di Azure.
* Connettersi agli account e ai servizi di archiviazione condivisi da altre sottoscrizioni di Azure.
* Connettersi alla risorsa di archiviazione locale e gestirla usando l'emulatore di archiviazione di Azure.

Inoltre, è possibile usare gli account di archiviazione in Azure globale e nazionale:

* [Connettersi a una sottoscrizione di Azure](#connect-to-an-azure-subscription): gestire le risorse di archiviazione appartenenti alla sottoscrizione di Azure.
* [Usare la risorsa di archiviazione locale](#work-with-local-development-storage): gestire la risorsa di archiviazione locale usando l'emulatore di archiviazione di Azure.
* [Collegarsi a una risorsa di archiviazione esterna](#attach-or-detach-an-external-storage-account): gestire le risorse di archiviazione appartenenti a un'altra sottoscrizione di Azure o ai cloud di Azure nazionale usando il nome, la chiave e gli endpoint dell'account di archiviazione.
* [Collegare un account di archiviazione usando la firma di accesso condiviso](#attach-storage-account-using-sas): gestire le risorse di archiviazione appartenenti a un'altra sottoscrizione di Azure usando una firma di accesso condiviso.
* [Collegare un servizio usando la firma di accesso condiviso](#attach-service-using-sas): gestire un servizio di archiviazione specifico (contenitore BLOB, coda o tabella) appartenente a un'altra sottoscrizione di Azure usando una firma di accesso condiviso.
* [Connettersi a un account Azure Cosmos DB usando una stringa di connessione](#connect-to-an-azure-cosmos-db-account-by-using-a-connection-string): gestire un account Cosmos DB usando una stringa di connessione.

## <a name="connect-to-an-azure-subscription"></a>Connettersi a una sottoscrizione di Azure

> [!NOTE]
> Se non si ha un account Azure, è possibile [iscriversi per ottenere una versione di valutazione gratuita](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F) oppure [attivare i vantaggi della sottoscrizione di Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).
>
>

1. In Storage Explorer selezionare **Manage Accounts** (Gestisci account) per andare al **pannello Account Management** (Gestione account).

    ![Gestire account][1]

2. Il riquadro di sinistra mostra ora tutti gli account Azure a cui si è connessi. Per connettersi a un altro account, selezionare **Add an account** (Aggiungi un account).

3. Se si vuole accedere a un cloud nazionale o ad Azure Stack, fare clic sull'elenco a discesa **Azure environment** (Ambiente Azure) per selezionare il cloud di Azure da usare. Dopo avere scelto l'ambiente, fare clic sul pulsante **Sign in...** (Accedi). Se si effettua l'accesso ad Azure Stack, vedere [Connect Storage Explorer to an Azure Stack subscription](azure-stack/user/azure-stack-storage-connect-se.md) (Connettere Storage Explorer a una sottoscrizione di Azure Stack) per altre informazioni.

    ![Opzione Sign In (Accedi)][2]

4. Dopo avere effettuato l'accesso con un account di Azure, l'account e le sottoscrizioni di Azure associate a quell'account vengono aggiunti nel pannello di sinistra. Selezionare le sottoscrizioni di Azure da usare e quindi selezionare **Apply** (Applica). Selezionando **All subscriptions:** (Tutte le sottoscrizioni) si alterna la selezione di tutte o di nessuna delle sottoscrizioni di Azure elencate.

    ![Selezionare le sottoscrizioni di Azure][3]

    Il riquadro sinistro mostra gli account di archiviazione associati alle sottoscrizioni di Azure selezionate.

    ![Sottoscrizioni di Azure selezionate][4]

## <a name="work-with-local-development-storage"></a>Utilizzare la risorsa di archiviazione locale

Storage Explorer consente di lavorare con la risorsa di archiviazione locale usando un emulatore. Questo approccio consente di simulare l'uso di Archiviazione di Azure senza necessariamente avere un account di archiviazione distribuito in Azure.

A partire dalla versione 1.1.0, l'emulatore di archiviazione locale è supportato in tutte le piattaforme. Storage Explorer può connettersi a qualsiasi servizio emulato in ascolto degli endpoint di archiviazione locali predefiniti.

> [!NOTE]
> Il supporto per funzionalità e servizi di archiviazione può variare notevolmente a seconda dell'emulatore che si sceglie. Assicurarsi che l'emulatore supporti i servizi e le funzionalità che si intende usare.

1. Configurare i servizi dell'emulatore preferito per l'ascolto su una porta inutilizzata.

   Servizio emulato | Endpoint predefinito
   -----------------|-------------------------
   Blobs            | `http://127.0.0.1:10000`
   Code           | `http://127.0.0.1:10001`
   Tabelle           | `http://127.0.0.1:10002`

2. Avviare l'emulatore.
   > [!IMPORTANT]
   > Storage Explorer non avvia automaticamente l'emulatore. È necessario avviarlo manualmente.

3. In Storage Explorer fare clic sul pulsante **Add Account** (Aggiungi account). Selezionare **Attach to a local emulator** (Collega a un emulatore locale) e fare clic su **Avanti**.

4. Immettere i numeri di porta per i servizi configurati in precedenza (lasciare vuoto se non si prevede di usare un servizio). Fare clic su **Avanti**, quindi **Connetti** per creare la connessione.

5. Espandere **Local & Attached** (Locale e collegato)  > **Account di archiviazione** > Nodi, quindi espandere i nodi del servizio sotto il nodo corrispondente alla connessione dell'emulatore.

   È possibile usare questo nodo per creare e usare tabelle, code e BLOB locali. Per informazioni su come usare i vari tipi di account di archiviazione, fare riferimento a una delle guide seguenti:

   * [Manage Azure blob storage resources (Gestire risorse di archiviazione BLOB di Azure)](vs-azure-tools-storage-explorer-blobs.md)
   * [Manage Azure File storage resources (Gestire risorse di archiviazione file di Azure)](vs-azure-tools-storage-explorer-files.md)

## <a name="attach-or-detach-an-external-storage-account"></a>Collegare o scollegare un account di archiviazione esterno

Storage Explorer consente di collegarsi agli account di archiviazione esterni per poter condividere facilmente gli account di archiviazione. Questa sezione illustra come collegarsi (e scollegarsi) agli account di archiviazione esterni.

### <a name="get-the-storage-account-credentials"></a>Ottenere le credenziali dell'account di archiviazione

Per condividere un account di archiviazione esterno, il proprietario di tale account deve prima ottenere le credenziali, ovvero il nome e la chiave dell'account, e quindi condividere tali informazioni con la persona che vuole collegarsi a tale account. Per ottenere le credenziali dell'account di archiviazione nel portale di Azure, è possibile seguire questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Selezionare **Esplora**.

3. Selezionare **Account di archiviazione**.

4. Nell'elenco **Account di archiviazione** selezionare l'account di archiviazione desiderato.

5. In **Impostazioni** selezionare **Chiavi di accesso**.

    ![Opzione Chiavi di accesso][7]

6. Copiare i valori di **Nome account di archiviazione** e **key1**.

    ![Chiavi di accesso][8]

### <a name="attach-to-an-external-storage-account"></a>Collegarsi a un account di archiviazione esterno

Per il collegamento a un account di archiviazione esterno è necessario avere la chiave e il nome dell'account. La sezione "Ottenere le credenziali dell'account di archiviazione" spiega come ottenere questi valori dal portale di Azure. Nel portale, la chiave dell'account è tuttavia denominata **key1**. Quindi, quando Storage Explorer chiede la chiave dell'account, immettere il valore di **key1**.

1. In Storage Explorer aprire la **finestra di dialogo di connessione**.

    ![Opzione Connect to Azure storage (Connetti ad Archiviazione di Azure)][9]

2. Nella **finestra di dialogo di connessione** scegliere **Use a storage account name and key** (Usare il nome e la chiave di un account di archiviazione).

    ![Opzione per l'aggiunta con un nome e una chiave][10]

3. Incollare il nome dell'account nella casella di testo **Account name** (Nome account), incollare la chiave dell'account (il valore **key1** del portale di Azure) nella casella di testo **Account key** (Chiave account) e quindi selezionare **Next** (Avanti).

    ![Pagina per il nome e la chiave][11]

    > [!NOTE]
    > Per usare un nome e una chiave di un cloud nazionale, usare l'elenco a discesa **Storage endpoints domain:** (Dominio endpoint archiviazione) per selezionare il dominio degli endpoint appropriato:
    >
    >

4. Nella finestra di dialogo **Riepilogo connessione** verificare le informazioni. Per apportare modifiche, selezionare **Indietro** e immettere nuovamente le impostazioni.

5. Selezionare **Connessione**.

6. Dopo essere stato collegato correttamente, l'account di archiviazione viene visualizzato con il testo **(External)** (Esterno) aggiunto al nome.

    ![Risultato della connessione a un account di archiviazione esterno][12]

### <a name="detach-from-an-external-storage-account"></a>Scollegarsi da un account di archiviazione esterno

1. Fare clic con il pulsante destro del mouse sull'account di archiviazione esterno che si vuole scollegare e quindi scegliere **Scollega**.

    ![Opzione per scollegarsi da un account di archiviazione][13]

2. Nella finestra di dialogo del messaggio di conferma, selezionare **Sì** per confermare che si vuole scollegare l'account di archiviazione esterno.

## <a name="attach-a-storage-account-by-using-a-shared-access-signature-sas"></a>Collegare un account di archiviazione tramite una firma di accesso condiviso

Una [firma di accesso condiviso](storage/common/storage-dotnet-shared-access-signature-part-1.md) consente all'amministratore di una sottoscrizione di Azure di concedere temporaneamente l'accesso a un account di archiviazione senza dover fornire le credenziali della sottoscrizione di Azure.

Per illustrare questo scenario, si supponga che l'utente A sia l'amministratore di una sottoscrizione di Azure e che voglia consentire all'utente B di accedere a un account di archiviazione per un periodo limitato con determinate autorizzazioni:

1. L'utente A genera una stringa di connessione della firma di accesso condiviso per un periodo di tempo specifico e con le autorizzazioni desiderate.

2. L'utente A condivide la firma di accesso condiviso con la persona che vuole accedere all'account di archiviazione, ovvero l'utente B.

3. L'utente B usa Storage Explorer per collegarsi all'account appartenente all'utente A usando la firma di accesso condiviso fornita.

### <a name="generate-a-sas-connection-string-for-the-account-you-want-to-share"></a>Generare una stringa di connessione della firma di accesso condiviso per l'account da condividere

1. In Storage Explorer fare clic con il pulsante destro del mouse sull'account di archiviazione che si vuole condividere e quindi scegliere **Get Shared Access Signature** (Ottieni firma di accesso condiviso).

    ![Menu di scelta rapida Get SAS (Ottieni firma di accesso condiviso)][14]

2. Nella finestra di dialogo **Generate Shared Access Signature** (Genera firma di accesso condiviso) specificare l'intervallo di tempo e le autorizzazioni per l'account e quindi fare clic sul pulsante **Create** (Crea).

    ![Finestra di dialogo Get SAS (Ottieni firma di accesso condiviso)][15]

3. Accanto alla casella di testo **Connection String** (Stringa di connessione) selezionare **Copy** (Copia) per copiare la stringa negli Appunti e quindi fare clic su **Close** (Chiudi).

### <a name="attach-to-a-storage-account-by-using-a-sas-connection-string"></a>Collegarsi a un account di archiviazione tramite una stringa di connessione della firma di accesso condiviso

1. In Storage Explorer aprire la **finestra di dialogo di connessione**.

    ![Opzione Connect to Azure storage (Connetti ad Archiviazione di Azure)][9]

2. Nella **finestra di dialogo di connessione** scegliere **Use a connection string or shared access signature URI** (Usare una stringa di connessione o un URI di firma di accesso condiviso) e quindi fare clic su **Next** (Avanti).

    ![Finestra di dialogo Connetti ad Archiviazione di Azure][16]

3. Scegliere **Use a connection string** (Usare una stringa di connessione) e incollare la stringa di connessione nel campo **Connection string:** (Stringa di connessione). Fare clic sul pulsante **Next** (Avanti).

    ![Finestra di dialogo Connetti ad Archiviazione di Azure][17]

4. Nella finestra di dialogo **Riepilogo connessione** verificare le informazioni. Per apportare modifiche, selezionare **Indietro** e immettere le impostazioni desiderate.

5. Selezionare **Connessione**.

6. Dopo essere stato collegato correttamente, l'account di archiviazione viene visualizzato con il testo **(SAS)** aggiunto al nome.

    ![Risultato del collegamento a un account con firma di accesso condiviso][18]

## <a name="attach-a-service-by-using-a-shared-access-signature-sas"></a>Collegare un servizio tramite una firma di accesso condiviso

La sezione "Collegare un account di archiviazione tramite una firma di accesso condiviso" illustra come l'amministratore di una sottoscrizione di Azure possa concedere l'accesso temporaneo a un account di archiviazione generando e condividendo una firma di accesso condiviso per l'account di archiviazione. Analogamente, una firma di accesso condiviso può essere generata per un servizio specifico (contenitore BLOB, coda, tabella o condivisione file) in un account di archiviazione.

### <a name="generate-an-sas-for-the-service-that-you-want-to-share"></a>Generare una firma di accesso condiviso per il servizio che si vuole condividere

In questo contesto un servizio può essere un contenitore BLOB, una coda, una tabella o una condivisione file. Per generare la firma di accesso condiviso per un servizio elencato, vedere:

* [Ottenere la firma di accesso condiviso per un contenitore BLOB](vs-azure-tools-storage-explorer-blobs.md#get-the-sas-for-a-blob-container)

### <a name="attach-to-the-shared-account-service-by-using-a-sas-uri"></a>Collegarsi al servizio account condiviso tramite l'URI di firma di accesso condiviso

1. In Storage Explorer aprire la **finestra di dialogo di connessione**.

    ![Opzione Connect to Azure storage (Connetti ad Archiviazione di Azure)][9]

2. Nella **finestra di dialogo di connessione** scegliere **Use a connection string or shared access signature URI** (Usare una stringa di connessione o un URI di firma di accesso condiviso) e quindi fare clic su **Next** (Avanti).

    ![Finestra di dialogo Connetti ad Archiviazione di Azure][16]

3. Scegliere **Use a SAS URI** (Usare un URI di firma di accesso condiviso) e incollare l'URI nel campo **URI:**. Fare clic sul pulsante **Next** (Avanti).

    ![Finestra di dialogo Connetti ad Archiviazione di Azure][19]

4. Nella finestra di dialogo **Riepilogo connessione** verificare le informazioni. Per apportare modifiche, selezionare **Indietro** e immettere le impostazioni desiderate.

5. Selezionare **Connessione**.

6. Dopo essere stato collegato correttamente, il servizio verrà visualizzato nel nodo **(SAS-Attached Services)** (Servizi associati alla firma di accesso condiviso).

    ![Risultato della connessione a un servizio condiviso usando una firma di accesso condiviso][20]

## <a name="connect-to-an-azure-cosmos-db-account-by-using-a-connection-string"></a>Connettersi a un account Azure Cosmos DB usando una stringa di connessione

Oltre a gestire gli account Azure Cosmos DB tramite la sottoscrizione di Azure, un metodo alternativo per connettersi a un account Azure Cosmos DB consiste nell'usare una stringa di connessione. Usare la procedura seguente per connettersi mediante una stringa di connessione.

1. Individuare **Local and Attached** (Locali e connesse) nella struttura ad albero a sinistra, fare doppio clic su **Account Azure Cosmos DB**, quindi scegliere **Connect to Azure Cosmos DB...** (Connetti ad Azure Cosmos DB...)

    ![Connettersi ad Azure Cosmos DB usando una stringa di connessione][21]

2. Scegliere l'API Azure Cosmos DB, incollare la **stringa di connessione** e quindi fare clic su **OK** per connettere l'account Azure Cosmos DB. Per informazioni su come recuperare la stringa di connessione, vedere [Ottenere la stringa di connessione](https://docs.microsoft.com/azure/cosmos-db/manage-account#get-the--connection-string).

    ![connection-string][22]

## <a name="connect-to-azure-data-lake-store-by-uri"></a>Connettersi a Azure Data Lake Store mediante URI

Se si vuole accedere a risorse che non sono incluse nella propria sottoscrizione ma per le quali altri utenti concedono di ottenere l'URI, è possibile connettersi a Data Lake Store usando l'URI dopo aver effettuato l'accesso. Vedere la procedura seguente.

1. Aprire Esplora archivi.
2. Nel riquadro sinistro espandere **Local and Attached** (Locale e collegato).
3. Fare clic con il pulsante destro del mouse su **Data Lake Store** e scegliere **Connect to Data Lake Store** (Connetti a Data Lake Store) dal menu di scelta rapida.

    ![Menu di scelta rapida per la connessione a Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach.png)

4. Immettere l'URI. Lo strumento passerà quindi alla posizione dell'URL appena immesso.

    ![Finestra di dialogo contestuale per la connessione a Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-uri-attach-dialog.png)

    ![Risultato della connessione a Data Lake Store](./media/vs-azure-tools-storage-manage-with-storage-explorer/storageexplorer-adls-attach-finish.png)

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
