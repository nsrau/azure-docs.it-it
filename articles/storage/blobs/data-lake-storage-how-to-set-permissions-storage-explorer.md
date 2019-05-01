---
title: Impostare le autorizzazioni per Data Lake Storage Gen2 con Azure Storage Explorer
description: Questa procedura descrive come impostare le autorizzazioni con Azure Storage Explorer per i file e le directory all'interno di account di archiviazione con supporto di Azure Data Lake Storage Gen2.
services: storage
author: normesta
ms.custom: mvc
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: quickstart
ms.date: 12/11/2018
ms.author: normesta
ms.openlocfilehash: 5f0211765c96cad668abaad7d42da87ec88298c3
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64939318"
---
# <a name="set-file-and-directory-level-permissions-using-azure-storage-explorer-with-azure-data-lake-storage-gen2"></a>Impostare autorizzazioni a livello di file e directory usando Azure Storage Explorer con Azure Data Lake Storage Gen2

I file archiviati in Azure Data Lake Storage Gen2 supportano autorizzazioni specifiche e la gestione di elenchi di controllo di accesso (ACL). Insieme,le autorizzazioni a granularità fine e la gestione degli ACL consentono di gestire l'accesso ai dati a un livello estremamente granulare.

In questo articolo viene illustrato come usare Azure Storage Explorer per:

> [!div class="checklist"]
> * Impostare le autorizzazioni a livello di file
> * Impostare le autorizzazioni a livello di directory
> * Aggiungere utenti o gruppi a un elenco di controllo di accesso

## <a name="prerequisites"></a>Prerequisiti

Per illustrare meglio il processo, è necessario completare la [Guida introduttiva di Azure Storage Explorer](data-lake-storage-Explorer.md). In questo modo l'account di archiviazione sarà nello stato più appropriato (file system creato e dati caricati in esso).

## <a name="managing-access"></a>Gestione dell'accesso

È possibile impostare le autorizzazioni nella radice del file system. A questo scopo, è necessario accedere ad Azure Storage Explorer con l'account personale con diritti per eseguire questa operazione, anziché con una stringa di connessione. Fare clic con il pulsante destro del mouse sul file system e scegliere **Gestisci autorizzazioni**: si aprirà la finestra di dialogo **Gestisci autorizzazioni**.

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
