---
title: "Esercitazione: Accedere all'archiviazione BLOB usando l'insieme di credenziali delle chiavi con Azure Databricks"
description: Questa esercitazione descrive come accedere ad Archiviazione BLOB di Azure da Azure Databricks usando i segreti archiviati in un insieme di credenziali delle chiavi.
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.topic: tutorial
ms.date: 07/19/2019
ms.openlocfilehash: f1472d7043a092f35e10fdedbf4c52740e87c51c
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74706149"
---
# <a name="tutorial-access-azure-blob-storage-from-azure-databricks-using-azure-key-vault"></a>Esercitazione: Accedere ad Archiviazione BLOB di Azure da Azure Databricks usando Azure Key Vault

Questa esercitazione descrive come accedere ad Archiviazione BLOB di Azure da Azure Databricks usando i segreti archiviati in un insieme di credenziali delle chiavi.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un account di archiviazione e un contenitore BLOB
> * Creare un'istanza di Azure Key Vault e aggiungere un segreto
> * Creare un'area di lavoro di Azure Databricks e aggiungere un ambito del segreto
> * Accedere al contenitore BLOB da Azure Databricks

## <a name="prerequisites"></a>Prerequisiti

- Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/)

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/).

> [!Note]
> Questa esercitazione non può essere eseguita usando una **sottoscrizione di valutazione gratuita di Azure**.
> Se l'utente ha un account gratuito, andare al proprio profilo e modificare la sottoscrizione a **con pagamento in base al consumo**. Per altre informazioni, vedere [Account gratuito di Azure](https://azure.microsoft.com/free/). Quindi [rimuovere il limite di spesa](https://docs.microsoft.com/azure/billing/billing-spending-limit#why-you-might-want-to-remove-the-spending-limit) e [richiedere un aumento della quota](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) per le vCPU nell'area dell'utente. Quando si crea l'area di lavoro Azure Databricks, è possibile selezionare il piano tariffario **Versione di valutazione (Premium - Unità Databricks gratuite per 14 giorni)** per concedere l'accesso gratuito Premium per 14 giorni dell'area di lavoro alle Unità Databricks di Azure.

## <a name="create-a-storage-account-and-blob-container"></a>Creare un account di archiviazione e un contenitore BLOB

1. Nel portale di Azure selezionare **Crea una risorsa** > **Archiviazione**. Quindi selezionare **Account di archiviazione**.

   ![Trovare la risorsa account di archiviazione di Azure](./media/store-secrets-azure-key-vault/create-storage-account-resource.png)

2. Selezionare la sottoscrizione e il gruppo di risorse esistente oppure crearne uno nuovo. Quindi immettere un nome di account di archiviazione e scegliere una località. Selezionare **Rivedi e crea**.

   ![Impostare le proprietà dell'account di archiviazione](./media/store-secrets-azure-key-vault/create-storage-account.png)

3. Se la convalida ha esito negativo, risolvere i problemi e riprovare. Se la convalida ha esito positivo, selezionare **Crea** e attendere che venga creato l'account di archiviazione.

4. Passare all'account di archiviazione appena creato e selezionare **BLOB** in **Servizi** nella pagina **Panoramica**. Quindi selezionare **+ Contenitore** e immettere un nome per il contenitore. Selezionare **OK**.

   ![Creare un nuovo contenitore](./media/store-secrets-azure-key-vault/create-blob-storage-container.png)

5. Individuare un file da caricare nel contenitore di archiviazione BLOB. Se non si hanno file, usare un editor di testo per creare un nuovo file di testo con alcune informazioni. In questo esempio, un file denominato **hw.txt** contiene il testo "hello world". Salvare il file di testo in locale e caricarlo nel contenitore di archiviazione BLOB.

   ![Caricare il file nel contenitore](./media/store-secrets-azure-key-vault/upload-txt-file.png)

6. Tornare nell'account di archiviazione e selezionare **Chiavi di accesso** in **Impostazioni**. Copiare il **nome dell'account di archiviazione** e la **chiave 1** in un editor di testo per usarli in seguito in questa esercitazione.

   ![Trovare le chiavi di accesso dell'account di archiviazione](./media/store-secrets-azure-key-vault/storage-access-keys.png)

## <a name="create-an-azure-key-vault-and-add-a-secret"></a>Creare un'istanza di Azure Key Vault e aggiungere un segreto

1. Nel portale di Azure selezionare **Crea una risorsa** e immettere **Key Vault** nella casella di ricerca.

   ![Casella di ricerca per creare una risorsa di Azure](./media/store-secrets-azure-key-vault/find-key-vault-resource.png)

2. La risorsa Key Vault viene selezionata automaticamente. Selezionare **Create** (Crea).

   ![Creare una risorsa Key Vault](./media/store-secrets-azure-key-vault/create-key-vault-resource.png)

3. Nella pagina **Crea insieme di credenziali delle chiavi** immettere le informazioni seguenti e mantenere i valori predefiniti per i campi rimanenti:

   |Proprietà|DESCRIZIONE|
   |--------|-----------|
   |NOME|Un nome univoco per l'insieme di credenziali delle chiavi.|
   |Subscription|Scegliere una sottoscrizione.|
   |Resource group|Scegliere un gruppo di risorse oppure crearne uno nuovo.|
   |Location|Scegliere un paese.|

   ![Proprietà di Azure Key Vault](./media/store-secrets-azure-key-vault/create-key-vault-properties.png)

3. Dopo avere specificato le informazioni, selezionare **Crea**. 

4. Passare all'insieme di credenziali delle chiavi appena creato nel portale di Azure e selezionare **Segreti**. Quindi selezionare **+ Genera/Importa**. 

   ![Generare un nuovo segreto dell'insieme di credenziali delle chiavi](./media/store-secrets-azure-key-vault/generate-import-secrets.png)

5. Nella pagina **Crea un segreto** immettere le informazioni seguenti e mantenere i valori predefiniti per i campi rimanenti:

   |Proprietà|Valore|
   |--------|-----------|
   |Opzioni di caricamento|Manuale|
   |NOME|Nome descrittivo della chiave dell'account di archiviazione.|
   |Valore|key1 dell'account di archiviazione.|

   ![Proprietà del nuovo segreto dell'insieme di credenziali delle chiavi](./media/store-secrets-azure-key-vault/create-storage-secret.png)

6. Salvare il nome della chiave in un editor di testo per usarlo più avanti in questa esercitazione, quindi selezionare **Crea**. Passare al menu **Proprietà**. Copiare i valori di **Nome DNS** e **ID risorsa** in un editor di testo per usarli più avanti nell'esercitazione.

   ![Copiare il nome DNS e l'ID risorsa di Azure Key Vault](./media/store-secrets-azure-key-vault/copy-dns-resource.png)

## <a name="create-an-azure-databricks-workspace-and-add-a-secret-scope"></a>Creare un'area di lavoro di Azure Databricks e aggiungere un ambito del segreto

1. Nel portale di Azure selezionare **Crea una risorsa** > **Analisi** > **Azure Databricks**.

    ![Databricks nel portale di Azure](./media/store-secrets-azure-key-vault/azure-databricks-on-portal.png)

2. In **Servizio Azure Databricks** specificare i valori seguenti per creare un'area di lavoro di Databricks.

   |Proprietà  |DESCRIZIONE  |
   |---------|---------|
   |Nome dell'area di lavoro     | Specificare un nome per l'area di lavoro di Databricks        |
   |Subscription     | Selezionare la sottoscrizione di Azure nell'elenco a discesa.        |
   |Resource group     | Selezionare lo stesso gruppo di risorse che contiene l'insieme di credenziali delle chiavi. |
   |Location     | Selezionare la stessa località scelta per Azure Key Vault. Per informazioni su tutte le aree disponibili, vedere [Servizi di Azure disponibili per area](https://azure.microsoft.com/regions/services/).        |
   |Piano tariffario     |  Scegliere tra **Standard** e **Premium**. Per altre informazioni su questi piani tariffari, vedere la [pagina dei prezzi di Databricks](https://azure.microsoft.com/pricing/details/databricks/).       |

   ![Proprietà dell'area di lavoro di Databricks](./media/store-secrets-azure-key-vault/create-databricks-service.png)

   Selezionare **Create** (Crea).

3. Nel portale di Azure passare alla risorsa di Azure Databricks appena creata e selezionare **Avvia l'area di lavoro**.

   ![Avviare l'area di lavoro di Azure Databricks](./media/store-secrets-azure-key-vault/launch-databricks-workspace.png)

4. Dopo aver aperto l'area di lavoro di Azure Databricks in una finestra distinta, aggiungere **#secrets/CreateScope** all'URL. L'URL dovrà avere il formato seguente: 

   **https://<\location>.azuredatabricks.net/#secrets/createScope**.

5. Immettere un nome di ambito, quindi il nome DNS e l'ID risorsa di Azure Key Vault salvati in precedenza. Salvare il nome dell'ambito in un editor di testo per usarlo più avanti in questa esercitazione. Scegliere quindi **Create** (Crea).

   ![Creare un ambito del segreto nell'area di lavoro di Azure Databricks](./media/store-secrets-azure-key-vault/create-secret-scope.png)

## <a name="access-your-blob-container-from-azure-databricks"></a>Accedere al contenitore BLOB da Azure Databricks

1. Nella home page dell'area di lavoro di Azure Databricks selezionare **Nuovo cluster** in **Attività comuni**.

   ![Creare un nuovo notebook di Azure Databricks](./media/store-secrets-azure-key-vault/create-new-cluster.png)

2. Immettere un nome di cluster e selezionare **Crea cluster**. La creazione del cluster richiede alcuni minuti.

3. Una volta creato il cluster, passare alla home page dell'area di lavoro di Azure Databricks e selezionare **Nuovo notebook** in **Attività comuni**.

   ![Creare un nuovo notebook di Azure Databricks](./media/store-secrets-azure-key-vault/create-new-notebook.png)

4. Immettere un nome per il notebook e impostare il linguaggio su Python. Impostare il cluster sul nome del cluster creato nel passaggio precedente.

5. Eseguire il comando seguente per montare il contenitore di archiviazione BLOB. Ricordarsi di cambiare i valori per le proprietà seguenti:

   * your-container-name
   * your-storage-account-name
   * mount-name
   * config-key
   * scope-name
   * key-name

   ```python
   dbutils.fs.mount(
   source = "wasbs://<your-container-name>@<your-storage-account-name>.blob.core.windows.net",
   mount_point = "/mnt/<mount-name>",
   extra_configs = {"<conf-key>":dbutils.secrets.get(scope = "<scope-name>", key = "<key-name>")})
   ```

   * **mount-name** è un percorso DBFS che rappresenta il punto in cui verrà montato il contenitore di archiviazione BLOB o una cartella all'interno del contenitore (specificata nell'origine).
   * **conf-key** può essere `fs.azure.account.key.<\your-storage-account-name>.blob.core.windows.net` o `fs.azure.sas.<\your-container-name>.<\your-storage-account-name>.blob.core.windows.net`
   * **scope-name** è il nome dell'ambito del segreto creato nella sezione precedente. 
   * **key-name** è il nome del segreto creato per la chiave dell'account di archiviazione nell'insieme di credenziali delle chiavi.

   ![Creare il montaggio dell'archiviazione BLOB nel notebook](./media/store-secrets-azure-key-vault/command1.png)

6. Eseguire il comando seguente per leggere il file di testo nel contenitore di archiviazione BLOB in un dataframe. Cambiare i valori nel comando in modo che corrispondano al nome del montaggio e al nome del file.

   ```python
   df = spark.read.text("mnt/<mount-name>/<file-name>")
   ```

   ![Leggere il file nel dataframe](./media/store-secrets-azure-key-vault/command2.png)

7. Usare il comando seguente per visualizzare il contenuto del file.

   ```python
   df.show()
   ```
   ![Visualizzare il dataframe](./media/store-secrets-azure-key-vault/command3.png)

8. Per smontare l'archiviazione BLOB, eseguire il comando seguente:

   ```python
   dbutils.fs.unmount("/mnt/<mount-name>")
   ```

   ![Smontare l'account di archiviazione](./media/store-secrets-azure-key-vault/command4.png)

9. Si noti che una volta smontato il montaggio, non sarà più possibile leggerlo dall'account di archiviazione BLOB.

   ![Errore relativo allo smontaggio dell'account di archiviazione](./media/store-secrets-azure-key-vault/command5.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si intende continuare a usare questa applicazione, eliminare l'intero gruppo di risorse seguendo questa procedura:

1. Dal menu a sinistra nel portale di Azure scegliere **Gruppi di risorse** e passare al gruppo di risorse.

2. Selezionare **Elimina gruppo di risorse** e digitare il nome del gruppo di risorse. Selezionare **Elimina**. 

## <a name="next-steps"></a>Passaggi successivi

Passare all'articolo successivo per informazioni su come implementare un ambiente Databricks con rete virtuale inserita con un endpoint di servizio abilitato per Cosmos DB.
> [!div class="nextstepaction"]
> [Esercitazione: Implementare Azure Databricks con un endpoint di Cosmos DB](service-endpoint-cosmosdb.md)
