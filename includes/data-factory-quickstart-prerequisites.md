---
title: File di inclusione
description: File di inclusione
services: data-factory
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: include
ms.custom: include file
ms.date: 06/27/2019
ms.openlocfilehash: a979cd0a4c2ee6466edebadf61e8a98b8f17c9f3
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "86544384"
---
## <a name="prerequisites"></a>Prerequisiti

### <a name="azure-subscription"></a>Sottoscrizione di Azure

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

### <a name="azure-roles"></a>Ruoli di Azure

Per creare istanze di Data Factory, l'account utente usato per accedere ad Azure deve essere un membro del ruolo *collaboratore* o *proprietario* oppure un *amministratore* della sottoscrizione di Azure. Per visualizzare le autorizzazioni disponibili nella sottoscrizione, passare al [portale di Azure](https://portal.azure.com), selezionare il nome utente nell'angolo in alto a destra, selezionare l'icona **...** per visualizzare altre opzioni e quindi selezionare **Autorizzazioni personali**. Se si accede a più sottoscrizioni, selezionare quella appropriata.

Per creare e gestire le risorse figlio per Data Factory, inclusi i set di dati, i servizi collegati, le pipeline, i trigger e i runtime di integrazione, sono applicabili i requisiti seguenti:

- Per creare e gestire le risorse figlio nel portale di Azure, è necessario appartenere al ruolo **Collaboratore Data Factory** a livello di gruppo di risorse o superiore.
- Per creare e gestire le risorse figlio con PowerShell o l'SDK, è sufficiente il ruolo di **collaboratore** a livello di risorsa o superiore.

Per istruzioni di esempio su come aggiungere un utente a un ruolo, vedere l'articolo [Aggiungere i ruoli](../articles/cost-management-billing/manage/add-change-subscription-administrator.md).

Per altre informazioni, vedere gli articoli seguenti:

- [Ruolo Collaboratore Data Factory](../articles/role-based-access-control/built-in-roles.md#data-factory-contributor)
- [Roles and permissions for Azure Data Factory](../articles/data-factory/concepts-roles-permissions.md) (Ruoli e autorizzazioni per Azure Data Factory)

### <a name="azure-storage-account"></a>Account di archiviazione di Azure

In questa guida di avvio rapido si usa un account di archiviazione di Azure per utilizzo generico (nello specifico, un account di archiviazione BLOB) come archivio dati sia di *origine* che di *destinazione*. Se non si ha un account di archiviazione di Azure per utilizzo generico, vedere [Creare un account di archiviazione](../articles/storage/common/storage-account-create.md) per informazioni su come crearne uno. 

#### <a name="get-the-storage-account-name"></a>Ottenere il nome dell'account di archiviazione

Il nome dell'account di archiviazione di Azure è necessario per questa guida di avvio rapido. La procedura seguente illustra i passaggi per recuperare il nome dell'account di archiviazione: 

1. In un Web browser passare al [portale di Azure](https://portal.azure.com) e accedere usando il nome utente e la password di Azure.
2. Dal menu del portale di Azure scegliere **Tutti i servizi**, quindi selezionare **Archiviazione** > **Account di archiviazione**. È anche possibile cercare e selezionare *Account di archiviazione* in qualsiasi pagina.
3. Nella pagina **Account di archiviazione** filtrare gli account di archiviazione, se necessario, quindi selezionare il proprio account di archiviazione. 

È anche possibile cercare e selezionare *Account di archiviazione* in qualsiasi pagina.

#### <a name="create-a-blob-container"></a>Creare un contenitore BLOB

In questa sezione viene creato un contenitore BLOB denominato **adftutorial** nell'archivio BLOB di Azure.

1. Dalla pagina dell'account di archiviazione, selezionare **Panoramica** > **Contenitori**.
2. Sulla barra degli strumenti della pagina *\<Account name>*  - **Contenitori** selezionare **Contenitore**.
3. Nella finestra di dialogo **Nuovo contenitore** immettere **adftutorial** come nome e quindi fare clic su **OK**. La pagina *\<Account name>*  - **Contenitori** viene aggiornata per includere **adftutorial** nell'elenco.

   ![Elenco dei contenitori](media/data-factory-quickstart-prerequisites/list-of-containers.png)

#### <a name="add-an-input-folder-and-file-for-the-blob-container"></a>Aggiungere una cartella di input e un file per il contenitore BLOB

In questa sezione viene creata una cartella denominata **input** nel contenitore creato, in cui verrà caricato un file di esempio. Prima di iniziare, aprire un editor di testo come il **Blocco note** e creare un file denominato **emp.txt** con il contenuto seguente:

```emp.txt
John, Doe
Jane, Doe
```

Salvare il file nella cartella **C:\ADFv2QuickStartPSH**. Se la cartella non esiste, crearla. Tornare al portale di Azure e seguire questa procedura:

1. Nella pagina *\<Account name>*  - **Contenitori** selezionare **adftutorial** nell'elenco aggiornato.

   1. Se la finestra è stata chiusa o è passata a un'altra pagina, accedere nuovamente al [portale di Azure](https://portal.azure.com).
   1. Dal menu del portale di Azure scegliere **Tutti i servizi**, quindi selezionare **Archiviazione** > **Account di archiviazione**. È anche possibile cercare e selezionare *Account di archiviazione* in qualsiasi pagina.
   1. Selezionare l'account di archiviazione, quindi **Contenitori** > **adftutorial**.

2. Nella barra degli strumenti della pagina del contenitore **adftutorial** selezionare **Carica**.
3. Nella pagina **Carica BLOB** selezionare la casella **File**, quindi individuare e selezionare il file **emp.txt**.
4. Espandere l'intestazione **Avanzate**. La pagina viene ora visualizzata come illustrato di seguito:

   ![Selezionare il collegamento Avanzate](media/data-factory-quickstart-prerequisites/upload-blob-advanced.png)
5. Nella casella **Carica nella cartella** immettere **input**.
6. Selezionare il pulsante **Carica**. Verranno visualizzati il file **emp.txt** e lo stato del caricamento nell'elenco.
7. Selezionare l'icona **Chiudi** (una **X**) per chiudere la pagina **Carica BLOB**.

Lasciare aperta la pagina del contenitore **adftutorial**. perché verrà usata per verificare l'output alla fine di questa guida introduttiva.