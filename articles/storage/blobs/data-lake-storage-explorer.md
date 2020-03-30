---
title: Usare Azure Storage Explorer con Azure Data Lake Storage Gen2Use Azure Storage Explorer with Azure Data Lake Storage Gen2
description: Usare Azure Storage Explorer per gestire le directory e gli elenchi di controllo di accesso (ACL) di file e directory negli account di archiviazione con spazio dei nomi gerarchico abilitato.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: fca9fa8a964c6c9d69ffbb3036bd4774e0d1bd34
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255548"
---
# <a name="use-azure-storage-explorer-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Usare Azure Storage Explorer per gestire directory, file e ACL in Azure Data Lake Storage Gen2

Questo articolo illustra come usare [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) per creare e gestire directory, file e autorizzazioni negli account di archiviazione con spazio dei nomi gerarchico abilitato.

## <a name="prerequisites"></a>Prerequisiti

> [!div class="checklist"]
> * Una sottoscrizione di Azure. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Un account di archiviazione con spazio dei nomi gerarchico abilitato. Seguire [queste](data-lake-storage-quickstart-create-account.md) istruzioni per crearne una.
> * Esplora archivi di Azure installato nel computer locale. Per installare Azure Storage Explorer per Windows, Macintosh o Linux, vedere [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

## <a name="sign-in-to-storage-explorer"></a>Accedere a Storage Explorer

Al primo avvio di Storage Explorer, viene visualizzata la finestra **Microsoft Azure Storage Explorer - Connetti**. Sebbene Storage Explorer offra numerosi modi per connettersi agli account di archiviazione, per la gestione degli elenchi di controllo di accesso è attualmente supportata una sola modalità.

|Attività|Scopo|
|---|---|
|Aggiungere un account Azure | Consente il reindirizzamento alla pagina di accesso dell'organizzazione per l'autenticazione in Azure. Attualmente questo è l'unico metodo di autenticazione supportato per la gestione e l'impostazione degli elenchi di controllo di accesso.|
|Usare una stringa di connessione o un URI di firma di accesso condiviso | Consente di accedere direttamente a un contenitore o a un account di archiviazione con un token di firma di accesso condiviso o una stringa di connessione condivisa. |
|Usare un nome e una chiave dell'account di archiviazione| Consente di usare un nome e una chiave dell'account di archiviazione per la connessione ad Archiviazione di Azure.|

Selezionare **Aggiungi un account Azure** e fare clic su **Accedi..**. Seguire le istruzioni visualizzate per accedere all'account Azure.Follow the on-screen prompts to sign into your Azure account.

![Microsoft Azure Storage Explorer - Finestra Connetti](media/storage-quickstart-blobs-storage-explorer/connect.png)

Al termine della connessione, Azure Storage Explorer viene caricato con la scheda **Explorer** visualizzata. Questa visualizzazione include informazioni dettagliate per tutti gli account di archiviazione di Azure e per le risorse di archiviazione locali configurate tramite gli account di [Azure Storage Emulator](../common/storage-use-emulator.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json), [Cosmos DB](../../cosmos-db/storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) o gli ambienti di [Azure Stack](/azure-stack/user/azure-stack-storage-connect-se?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

![Microsoft Azure Storage Explorer - Finestra Connetti](media/storage-quickstart-blobs-storage-explorer/mainpage.png)

## <a name="create-a-container"></a>Creare un contenitore

Un contenitore contiene directory e file. Per crearne uno, espandere l'account di archiviazione creato nel passaggio successivo. Selezionare **Contenitori BLOB**, fare clic con il pulsante destro del mouse e scegliere **Crea contenitore BLOB**. Immettere il nome per il contenitore. Vedere la sezione [Creare un contenitore](storage-quickstart-blobs-dotnet.md#create-a-container) per un elenco di regole e restrizioni sulla denominazione dei contenitori. Al termine, premere **INVIO** per creare il contenitore. Una volta creato correttamente, il contenitore viene visualizzato nella cartella **Contenitori BLOB** per l'account di archiviazione selezionato.

![Esplora archivi di Microsoft Azure - Creazione di un contenitore](media/data-lake-storage-explorer/creating-a-filesystem.png)

## <a name="create-a-directory"></a>Creare una directory

Per creare una directory, selezionare il contenitore creato nel passaggio successivo. Nella barra multifunzione del contenitore scegliere il pulsante **Nuova cartella.** Immettere il nome della directory. Al termine, premere **INVIO** per creare la directory. Una volta creata, la directory viene visualizzata nella finestra dell'editor.

![Esplora archivi di Microsoft Azure - Creazione di una directory](media/data-lake-storage-explorer/creating-a-directory.png)

## <a name="upload-blobs-to-the-directory"></a>Caricare i BLOB nella directory

Sulla barra multifunzione della directory scegliere il pulsante **Carica.** Questa operazione consente di caricare una cartella o un file.

Scegliere i file o le cartelle da caricare.

![Microsoft Azure Storage Explorer - Caricare un BLOB](media/data-lake-storage-explorer/upload-file.png)

Quando si seleziona **OK**, i file selezionati vengono accodati per il caricamento e viene caricato ogni file. Al termine del caricamento, i risultati vengono visualizzati nella finestra **Attività**.

## <a name="view-blobs-in-a-directory"></a>Visualizzare BLOB in una directory

Nell'applicazione **Azure Storage Explorer**, selezionare una directory in un account di archiviazione. Il riquadro principale mostra un elenco dei BLOB disponibili nella directory selezionata.

![Microsoft Azure Storage Explorer - Elencare i BLOB in una directory](media/data-lake-storage-explorer/list-files.png)

## <a name="download-blobs"></a>Scaricare BLOB

Per scaricare i file tramite **Azure Storage Explorer,** con un file selezionato, selezionare **Scarica** dalla barra multifunzione. Viene visualizzata una finestra di dialogo File, che consente di immettere un nome di file. Selezionare **Salva** per avviare il download di un file nel percorso locale.

## <a name="managing-access"></a>Gestione dell'accesso

È possibile impostare le autorizzazioni nella radice del contenitore. A questo scopo, è necessario accedere ad Azure Storage Explorer con l'account personale con diritti per eseguire questa operazione, anziché con una stringa di connessione. Fare clic con il tasto destro sul contenitore e scegliere **Gestisci autorizzazioni**, si aprirà la finestra di dialogo **Gestisci autorizzazioni**.

![Microsoft Azure Storage Explorer - Gestire l’accesso alle directory](media/storage-quickstart-blobs-storage-Explorer/manageperms.png)

La finestra di dialogo **Gestisci autorizzazioni** consente di gestire le autorizzazioni per il proprietario e il gruppo di proprietari. Consente inoltre di aggiungere nuovi utenti e gruppi all'elenco di controllo di accesso per il quale è possibile quindi gestire le autorizzazioni.

Per aggiungere un nuovo utente o gruppo all'elenco di controllo di accesso, selezionare il campo **Aggiungi utente o gruppo**.

Immettere la voce corrispondente di Azure Active Directory (AAD) che si desidera aggiungere all'elenco e quindi selezionare **Aggiungi**.

L'utente o il gruppo verranno visualizzati nel campo **Utenti e gruppi:**, che consente di iniziare a gestire le relative autorizzazioni.

> [!NOTE]
> È una procedura consigliata per creare un gruppo di sicurezza in Azure Active Directory e gestire le autorizzazioni per il gruppo anziché per i singoli utenti. Per informazioni dettagliate su questa procedura consigliata, nonché per altre procedure consigliate, consultare [procedure consigliate per Data Lake Storage Gen2](data-lake-storage-best-practices.md).

Esistono due categorie di autorizzazioni che è possibile assegnare: elenchi ACL di acceso e ACL predefiniti.

* **Access**: gli ACL di accesso controllano l'accesso a un oggetto. Sia i file che le directory hanno ACL di accesso.

* **Predefinito**: un modello di ACL associato a una directory che determina gli ACL di accesso per tutti gli elementi figlio creati in tale directory. I file non hanno ACL predefiniti.

All'interno di entrambe queste categorie, è possibile assegnare tre autorizzazioni a file o directory: **Lettura**, **Scrittura**ed **Esecuzione**.

>[!NOTE]
> Le selezioni effettuate qui non imposteranno le autorizzazioni per tutti gli elementi attualmente esistenti all'interno della directory. Se il file esiste già, è necessario passare a ogni singolo elemento e impostare le autorizzazioni manualmente.

È possibile gestire le autorizzazioni per directory singole, nonché per file singoli, ovvero ciò che consente il controllo di accesso granulare. Il processo di gestione delle autorizzazioni per file e directory è identico a quello descritto sopra. Fare clic con il tasto destro sul file o sulla directory per cui si desidera gestire le autorizzazioni e seguire la stessa procedura.

## <a name="next-steps"></a>Passaggi successivi

Informazioni gli elenchi di controllo di accesso in Data Lake Storage Gen2.Learn access control lists in Data Lake Storage Gen2.

> [!div class="nextstepaction"]
> [Controllo dell'accesso in Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)
