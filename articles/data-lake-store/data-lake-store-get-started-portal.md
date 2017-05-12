---
title: Usare il portale di Azure per iniziare a usare Data Lake Store | Documentazione Microsoft
description: Usare il portale di Azure per creare un account Data Lake Store ed eseguire le relative operazioni di base
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: fea324d0-ad1a-4150-81f0-8682ddb4591c
ms.service: data-lake-store
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/06/2017
ms.author: nitinme
ms.translationtype: Human Translation
ms.sourcegitcommit: 988e7fe2ae9f837b661b0c11cf30a90644085e16
ms.openlocfilehash: efb9e68256765873eb2776637d1a9469af6e46d6
ms.contentlocale: it-it
ms.lasthandoff: 04/06/2017


---
# <a name="get-started-with-azure-data-lake-store-using-the-azure-portal"></a>Introduzione all’Archivio Data Lake di Azure tramite il portale di Azure.
> [!div class="op_single_selector"]
> * [Portale](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [SDK per Java](data-lake-store-get-started-java-sdk.md)
> * [API REST](data-lake-store-get-started-rest-api.md)
> * [Interfaccia della riga di comando di Azure](data-lake-store-get-started-cli.md)
> * [Interfaccia della riga di comando di Azure 2.0](data-lake-store-get-started-cli-2.0.md)
> * [Node.js](data-lake-store-manage-use-nodejs.md)
> * [Python](data-lake-store-get-started-python.md)
>
> 

Informazioni su come usare il portale di Azure per creare un account di Azure Data Lake Store ed eseguire operazioni di base, ad esempio creare cartelle, caricare e scaricare i file di dati, eliminare l'account e così via. Per altre informazioni su Data Lake Store, vedere [Panoramica di Azure Data Lake Store](data-lake-store-overview.md).

## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare questa esercitazione, è necessario disporre di quanto segue:

* **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="do-you-learn-faster-with-videos"></a>Apprendimento rapido con i video
Guardare i video seguenti per iniziare a usare Archivio Data Lake.

* [Creare un account Archivio Data Lake](https://mix.office.com/watch/1k1cycy4l4gen)
* [Gestire i dati in Archivio Data Lake usando Esplora dati](https://mix.office.com/watch/icletrxrh6pc)

## <a name="create-an-azure-data-lake-store-account"></a>Creare un account di Azure Data Lake Store
1. Accedere al nuovo [portale di Azure](https://portal.azure.com).
2. Fare clic su **NUOVO**, selezionare **Data + Storage** (Dati + archiviazione), quindi fare clic su **Azure Data Lake Store**. Leggere le informazioni nel pannello **Azure Data Lake Store**, quindi fare clic su **Crea** nell'angolo inferiore sinistro del pannello.
3. Nel pannello **nuovo archivio Data Lake** , fornire i valori come illustrato nella schermata riportata di seguito:
   
    ![Creare un nuovo account Azure Data Lake Store](./media/data-lake-store-get-started-portal/ADL.Create.New.Account.png "Creare un nuovo account Azure Data Lake")
   
   * **Nome**. Immettere un nome univoco per l'account Data Lake Store.
   * **Sottoscrizione**. Selezionare la sottoscrizione in cui si vuole creare un nuovo account Data Lake Store.
   * **Gruppo di risorse**. Selezionare un gruppo di risorse esistente oppure selezionare l'opzione **Crea nuovo** per crearne uno. Un gruppo di risorse è un contenitore che contiene risorse correlate per un'applicazione. Per altre informazioni, vedere [Gruppi di risorse in Azure](../azure-resource-manager/resource-group-overview.md#resource-groups).
   * **Posizione**: selezionare una posizione in cui si desidera creare l'account Archivio Data Lake
   * **Impostazioni crittografia**. È possibile scegliere se crittografare l'account Data Lake Store. Se si sceglie di crittografarlo, è anche possibile specificare come gestire la chiave di crittografia principale che si vuole usare per crittografare i dati nell'account.
     
     * (Facoltativo) Selezionare **Non abilitare la crittografia** dall'elenco a discesa per rifiutare esplicitamente la crittografia.
     * (Impostazione predefinita) Selezionare **Usare chiavi gestite da Azure Data Lake** per gestire le chiavi di crittografia con Azure Data Lake Store.
       
         ![Crittografia di Data Lake Store](./media/data-lake-store-get-started-portal/adls-encryption-1.png "Crittografia di Data Lake Store")
     * (Facoltativo) Selezionare **Scegliere le chiavi dall'insieme di credenziali delle chiavi di Azure** per usare le proprie chiavi presenti nell'insieme di credenziali delle chiavi di Azure. Con questa opzione, è anche possibile creare le chiavi e un account dell'insieme di credenziali delle chiavi, se necessario.
       
         ![Crittografia di Data Lake Store](./media/data-lake-store-get-started-portal/adls-encryption-2.png "Crittografia di Data Lake Store")
       
       Fare clic su **OK** nel pannello **Impostazioni crittografia**.
       
       > [!NOTE]
       > Se si usano le chiavi di un insieme di credenziali delle chiavi di Azure per configurare la crittografia per l'account Data Lake Store, è necessario assegnare le autorizzazioni per l'accesso dell'account Azure Data Lake Store all'insieme di credenziali delle chiavi di Azure. Per le istruzioni, vedere [Assegnare le autorizzazioni all'insieme di credenziali delle chiavi di Azure](#assign-permissions-to-the-azure-key-vault).
       > 
       > 
4. Fare clic su **Crea**. Se si è scelto di aggiungere l'account al dashboard, si viene indirizzati al dashboard, dove è possibile visualizzare lo stato di avanzamento del provisioning dell'account Data Lake Store. Una volta eseguito il provisioning dell'account di Archivio Data Lake, viene visualizzato il pannello dell’account.

## <a name="assign-permissions-to-the-azure-key-vault"></a>Assegnare le autorizzazioni all'insieme di credenziali delle chiavi di Azure
Se sono state usate le chiavi di un insieme di credenziali delle chiavi di Azure per configurare la crittografia nell'account Data Lake Store, è necessario configurare l'accesso tra l'account Data Lake Store e l'account dell'insieme di credenziali delle chiavi di Azure. Seguire quindi questa procedura.

1. Se sono state usate le chiavi dell'insieme di credenziali delle chiavi di Azure, il pannello dell'account Data Lake Store visualizza un avviso in alto. Fare clic sull'avviso per aprire il pannello **Configurare le autorizzazioni dell'insieme di credenziali delle chiavi**.
   
    ![Crittografia di Data Lake Store](./media/data-lake-store-get-started-portal/adls-encryption-3.png "Crittografia di Data Lake Store")
2. Il pannello mostra due opzioni per configurare l'accesso.
   
   * Nella prima opzione, fare clic su **Concedi autorizzazione** per configurare l'accesso. La prima opzione è abilitata solo quando l'utente che ha creato l'account Data Lake Store è anche un amministratore dell'insieme di credenziali delle chiavi di Azure.
   * L'altra opzione consiste nell'eseguire il cmdlet di PowerShell visualizzato nel pannello. È necessario essere il proprietario dell'insieme di credenziali delle chiavi di Azure o poter concedere le autorizzazioni per l'insieme di credenziali delle chiavi di Azure. Dopo avere eseguito il cmdlet, tornare al pannello e fare clic su **Abilita** per configurare l'accesso.

## <a name="createfolder"></a>Creare delle cartelle in Azure Data Lake Store
È possibile creare delle cartelle con il proprio account di Archivio Data Lake per gestire e archiviare i dati.

1. Aprire l'account archivio Data Lake appena creato. Dal riquadro sinistro fare clic su **Sfoglia** e selezionare **Data Lake Store**, quindi dal pannello di Data Lake Store fare clic sul nome dell’account in cui si desidera creare le cartelle. Se è stato aggiunto l'account alla schermata iniziale, fare clic sul riquadro dell’account.
2. Nel pannello dell'account di Archivio Data Lake, fare clic su **Esplora dati**.
   
    ![Creare cartelle nell'account Data Lake Store](./media/data-lake-store-get-started-portal/ADL.Create.Folder.png "Creare cartelle nell'account Data Lake Store")
3. Nel pannello dell’account di Data Lake Store, fare clic su **Nuova cartella**, rinominarla, quindi selezionare **OK**.
   
    ![Creare cartelle nell'account Data Lake Store](./media/data-lake-store-get-started-portal/ADL.Folder.Name.png "Creare cartelle nell'account Data Lake Store")
   
    La cartella appena creata verrà elencata nel pannello **Esplora dati** . È possibile creare cartelle nidificate fino a qualsiasi livello.
   
    ![Creare cartelle nell'account Data Lake](./media/data-lake-store-get-started-portal/ADL.New.Directory.png "Creare cartelle nell'account Data Lake")

## <a name="uploaddata"></a>Caricare dati in Archivio Data Lake di Azure
È possibile caricare i dati in un archivio Data Lake di Azure direttamente a livello di radice o in una cartella creata all'interno dell'account. Nella schermata di seguito, seguire i passaggi per caricare un file in una sotto-cartella dal pannello **Esplora dati** . In questa schermata, il file viene caricato in una sotto-cartella nei percorsi di navigazione (contrassegnati in una casella rossa).

Se si stanno cercando dati di esempio da caricare, è possibile ottenere la cartella **Ambulance Data** dal [Repository GitHub per Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData).

![Caricare dati](./media/data-lake-store-get-started-portal/ADL.New.Upload.File.png "Caricare dati")

## <a name="properties"></a>Proprietà e azioni disponibili sui dati archiviati
Fare clic sul file appena aggiunto per aprire il pannello **Proprietà** . Le proprietà associate al file e alle azioni che è possibile eseguire nel file sono disponibili in questo pannello. È inoltre possibile copiare il percorso completo al file nell'account dell’Archivio Data Lake di Azure, evidenziato nella casella rossa nella schermata riportata di seguito.

![Proprietà relative ai dati](./media/data-lake-store-get-started-portal/ADL.File.Properties.png "Proprietà relative ai dati")

* Fare clic su **Anteprima** per visualizzare un'anteprima del file direttamente dal browser. È possibile specificare anche il formato dell'anteprima. Fare clic su **Anteprima**, selezionare **Formato** nel pannello **Anteprima file** e nell pannello **File Preview Format** (Formato anteprima file) specificare le opzioni, come ad esempio il numero di righe da visualizzare, la codifica e il delimitatore da usare e così via.
  
  ![Formato di anteprima file](./media/data-lake-store-get-started-portal/ADL.File.Preview.png "Formato di anteprima file")
* Fare clic su **Download** per scaricare il file nel computer.
* Fare clic su **Rinomina file** per rinominare il file.
* Fare clic su **Elimina file** per eliminare il file.

## <a name="secure-your-data"></a>Protezione dei dati
È possibile proteggere i dati archiviati nell'account Archivio Data Lake di Azure mediante Azure Active Directory e il controllo di accesso (ACL). Per istruzioni su come eseguire questa operazione, vedere [Protezione dei dati nell'archivio Data Lake di Azure](data-lake-store-secure-data.md).

## <a name="delete-azure-data-lake-store-account"></a>Eliminare l'account di Azure Data Lake Store
Per eliminare un account di Archivio Data Lake di Azure, dal pannello dell’account dell’Archivio Data Lake, fare clic su **Elimina**. Per confermare l'azione, verrà chiesto di immettere il nome dell'account che si desidera eliminare. Immettere il nome dell’account e quindi fare clic su **Elimina**.

![Eliminare l'account Data Lake](./media/data-lake-store-get-started-portal/ADL.Delete.Account.png "Eliminare l'account Data Lake")

## <a name="next-steps"></a>Passaggi successivi
* [Proteggere i dati in Data Lake Store](data-lake-store-secure-data.md)
* [Usare Azure Data Lake Analytics con Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Usare Azure HDInsight con Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)
* [Accesso ai log di diagnostica per Azure Data Lake Store](data-lake-store-diagnostic-logs.md)


