---
title: Impostare le autorizzazioni per Data Lake Storage Gen2 con Azure Storage Explorer
description: Questa procedura, descrive come impostare le autorizzazioni con Azure Storage Explorer per file e directory all'interno di account di archiviazione in grado di supportare Azure Data Lake Storage Gen2 (anteprima).
services: storage
author: roygara
ms.custom: mvc
ms.component: data-lake-storage-gen2
ms.service: storage
ms.topic: quickstart
ms.date: 12/11/2018
ms.author: rogarana
ms.openlocfilehash: 1b89553816b6ff8a8076d954274d8404f49154a6
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/14/2018
ms.locfileid: "53384854"
---
# <a name="set-file-and-directory-level-permissions-using-azure-storage-explorer-with-azure-data-lake-storage-gen2-preview"></a>Impostare autorizzazioni a livello di file e directory usando Azure Storage Explorer con Azure Data Lake Storage Gen2 (anteprima)

I file archiviati in Azure Data Lake Storage Gen2 (anteprima) supportano le autorizzazioni granulari e la gestione degli elenchi di controllo di accesso (ACL). Insieme,le autorizzazioni a granularità fine e la gestione degli ACL consentono di gestire l'accesso ai dati a un livello estremamente granulare.

In questo articolo viene illustrato come usare Azure Storage Explorer per:

> [!div class="checklist"]
> * Impostare le autorizzazioni a livello di file
> * Impostare le autorizzazioni a livello di directory
> * Aggiungere utenti o gruppi a un elenco di controllo di accesso

## <a name="prerequisites"></a>Prerequisiti

Per illustrare meglio il processo, è necessario completare la [Guida introduttiva di Azure Storage Explorer](data-lake-storage-Explorer.md). In questo modo l'account di archiviazione sarà nello stato più appropriato (file system creati e dati caricati sul file system).

## <a name="managing-access"></a>Gestione dell'accesso

È possibile impostare le autorizzazioni nella radice del file system. A tale scopo, fare clic con il tasto destro sul file system e scegliere **Gestisci autorizzazioni**, si aprirà la finestra di dialogo **Gestisci autorizzazioni**.

![Microsoft Azure Storage Explorer - Gestire l’accesso alle directory](media/storage-quickstart-blobs-storage-Explorer/manageperms.png)

La finestra di dialogo **Gestisci autorizzazioni** consente di gestire le autorizzazioni per il proprietario e il gruppo di proprietari. Consente inoltre di aggiungere nuovi utenti e gruppi all'elenco di controllo di accesso per il quale è possibile quindi gestire le autorizzazioni.

Per aggiungere un nuovo utente o gruppo all'elenco di controllo di accesso, selezionare il campo **Aggiungi utente o gruppo**.

Immettere la voce corrispondente di Azure Active Directory (AAD) che si desidera aggiungere all'elenco e quindi selezionare **Aggiungi**.

L'utente o il gruppo verranno visualizzati nel campo **Utenti e gruppi:**, che consente di iniziare a gestire le relative autorizzazioni.

> [!NOTE]
> È una procedura consigliata per creare un gruppo di sicurezza in Azure Active Directory e gestire le autorizzazioni per il gruppo anziché per i singoli utenti. Per informazioni dettagliate su questa procedura consigliata, nonché per altre procedure consigliate, consultare [procedure consigliate per Data Lake Storage Gen2](data-lake-storage-best-practices.md).

Esistono due categorie di autorizzazioni che è possibile assegnare: elenchi ACL di acceso e ACL predefiniti.

* **Accesso**: Gli elenchi ACL di accesso controllano l'accesso a un oggetto. Sia i file che le directory hanno ACL di accesso.

* **Predefiniti**: modello di ACL associato a una directory che determina gli ACL di accesso per tutti gli elementi figlio creati in tale directory. I file non hanno ACL predefiniti.

In entrambi i casi, sono disponibili tre autorizzazioni che è possibile assegnare ai file o alle directory: **Lettura**, **Scrittura** ed **Esecuzione**.

>[!NOTE]
> Le selezioni effettuate qui non imposteranno le autorizzazioni per tutti gli elementi attualmente esistenti all'interno della directory. Se il file esiste già, è necessario passare a ogni singolo elemento e impostare le autorizzazioni manualmente.

È possibile gestire le autorizzazioni per directory singole, nonché per file singoli, ovvero ciò che consente il controllo di accesso granulare. Il processo di gestione delle autorizzazioni per file e directory è identico a quello descritto sopra. Fare clic con il tasto destro sul file o sulla directory per cui si desidera gestire le autorizzazioni e seguire la stessa procedura.

## <a name="next-steps"></a>Passaggi successivi

In questa procedura, è stato descritto come impostare le autorizzazioni per file e directory usando **Azure Storage Explorer**. Per altre informazioni sugli ACL, tra cui ACL predefiniti, ACL di accesso e il loro comportamento e le relative autorizzazioni, consultare il nostro articolo concettuale sull'argomento.

> [!div class="nextstepaction"]
> [Controllo di accesso in Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)