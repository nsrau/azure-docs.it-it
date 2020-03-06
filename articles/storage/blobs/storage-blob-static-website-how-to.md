---
title: Ospitare un sito Web statico in archiviazione di Azure
description: Informazioni su come gestire contenuto statico (file HTML, CSS, JavaScript e immagini) direttamente da un contenitore in un account GPv2 di archiviazione di Azure.
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.author: normesta
ms.date: 03/04/2020
ms.openlocfilehash: e312cc0dc6c58bb33a737e1fc28dd6eb3578b764
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78330270"
---
# <a name="host-a-static-website-in-azure-storage"></a>Ospitare un sito Web statico in archiviazione di Azure

È possibile gestire il contenuto statico (file HTML, CSS, JavaScript e di immagine) direttamente da un contenitore in un account GPv2 di archiviazione di Azure. Per altre informazioni, vedere [hosting di siti web statici in archiviazione di Azure](storage-blob-static-website.md).

Questo articolo illustra come abilitare l'hosting di siti web statici usando il portale di Azure, l'interfaccia della riga di comando di Azure o PowerShell.

## <a name="enable-static-website-hosting"></a>Abilita hosting di siti web statici

L'hosting di siti web statici è una funzionalità che è necessario abilitare nell'account di archiviazione.

### <a name="portal"></a>[Portale](#tab/azure-portal)

1. Accedere al [portale di Azure](https://portal.azure.com/) per iniziare.

2. Individuare l'account di archiviazione e visualizzare la sezione della panoramica dell'account.

3. Selezionare **Sito Web statico** per visualizzare la pagina di configurazione per i siti Web statici.

4. Selezionare **Abilitato** per abilitare l'hosting di siti Web statici per l'account di archiviazione.

5. Nel campo **nome documento indice** specificare una pagina di indice predefinita, ad esempio *index. html*. 

   La pagina di indice predefinita viene visualizzata quando un utente passa alla radice del sito Web statico.  

6. Nel campo **percorso del documento di errore** specificare una pagina di errore predefinita (ad esempio: *404. html*). 

   La pagina di errore predefinita viene visualizzata quando un utente tenta di passare a una pagina inesistente nel sito Web statico.

7. Fare clic su **Save**. Il portale di Azure visualizza ora l'endpoint del sito Web statico. 

    ![Abilitare l'hosting di siti Web statici per un account di archiviazione](media/storage-blob-static-website-host/enable-static-website-hosting.png)

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

<a id="cli" />

È possibile abilitare l'hosting di siti web statici usando l' [interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

1. Aprire prima di tutto la [Azure cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)o, se l'interfaccia della riga di comando di Azure è stata [installata](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) localmente, aprire un'applicazione console comando come Windows PowerShell.

2. Se l'identità è associata a più di una sottoscrizione, impostare la sottoscrizione attiva sulla sottoscrizione dell'account di archiviazione che ospiterà il sito Web statico.

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   Sostituire il valore del segnaposto `<subscription-id>` con l'ID della sottoscrizione.

3. Abilita l'hosting di siti web statici.

   ```azurecli-interactive
   az storage blob service-properties update --account-name <storage-account-name> --static-website --404-document <error-document-name> --index-document <index-document-name>
   ```

   * Sostituire il valore segnaposto `<storage-account-name>` con il nome del proprio account di archiviazione.

   * Sostituire il segnaposto `<error-document-name>` con il nome del documento di errore che verrà visualizzato agli utenti quando un browser richiede una pagina nel sito che non esiste.

   * Sostituire il segnaposto `<index-document-name>` con il nome del documento di indice. Questo documento è comunemente "index. html".

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

<a id="powershell" />

È possibile abilitare l'hosting di siti web statici usando il modulo Azure PowerShell.

1. Aprire una finestra di comando di Windows PowerShell.

2. Verificare di disporre di Azure PowerShell modulo AZ versione 0,7 o successiva.

   ```powershell
   Get-InstalledModule -Name Az -AllVersions | select Name,Version
   ```

   Se è necessario eseguire l'installazione o l'aggiornamento, vedere come [installare il modulo Azure PowerShell](/powershell/azure/install-Az-ps).

3. Accedere alla sottoscrizione di Azure con il comando `Connect-AzAccount` e seguire le istruzioni visualizzate.

   ```powershell
   Connect-AzAccount
   ```

4. Se l'identità è associata a più di una sottoscrizione, impostare la sottoscrizione attiva sulla sottoscrizione dell'account di archiviazione che ospiterà il sito Web statico.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Sostituire il valore del segnaposto `<subscription-id>` con l'ID della sottoscrizione.

5. Ottenere il contesto dell'account di archiviazione che definisce l'account di archiviazione che si vuole usare.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * Sostituire il valore del segnaposto `<resource-group-name>` con il nome del gruppo di risorse.

   * Sostituire il valore segnaposto `<storage-account-name>` con il nome del proprio account di archiviazione.

6. Abilita l'hosting di siti web statici.

   ```powershell
   Enable-AzStorageStaticWebsite -Context $ctx -IndexDocument <index-document-name> -ErrorDocument404Path <error-document-name>
   ```

   * Sostituire il segnaposto `<error-document-name>` con il nome del documento di errore che verrà visualizzato agli utenti quando un browser richiede una pagina nel sito che non esiste.

   * Sostituire il segnaposto `<index-document-name>` con il nome del documento di indice. Questo documento è comunemente "index. html".

---

## <a name="upload-files"></a>Caricare file 

### <a name="portal"></a>[Portale](#tab/azure-portal)

Queste istruzioni illustrano come caricare i file usando la versione di Storage Explorer visualizzata nella portale di Azure. Tuttavia, è anche possibile usare la versione di [Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) eseguita all'esterno del portale di Azure. È possibile usare [AzCopy](../common/storage-use-azcopy-v10.md), PowerShell, l'interfaccia della riga di comando o qualsiasi applicazione personalizzata in grado di caricare i file nel contenitore **$Web** dell'account. Per un'esercitazione dettagliata che consente di caricare file con Visual Studio Code, vedere [esercitazione: ospitare un sito Web statico nell'archivio BLOB](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website-host).

1. Selezionare **Storage Explorer (anteprima)** .

2. Espandere il nodo **contenitori BLOB** , quindi selezionare il contenitore **$Web** .

3. Scegliere il pulsante **carica** per caricare i file.

   ![Caricare file](media/storage-blob-static-website/storage-blob-static-website-upload.png)

4. Se si desidera che il browser visualizzi il contenuto del file, verificare che il tipo di contenuto del file sia impostato su `text/html`. 

   ![Controllare i tipi di contenuto](media/storage-blob-static-website/storage-blob-static-website-content-type.png)

   >[!NOTE]
   > Storage Explorer imposta automaticamente questa proprietà su `text/html` per le estensioni comunemente riconosciute, ad esempio `.html`. Tuttavia, in alcuni casi, è necessario impostare questa impostazione. Se non si imposta questa proprietà su `text/html`, il browser richiederà agli utenti di scaricare il file anziché eseguire il rendering del contenuto. Per impostare questa proprietà, fare clic con il pulsante destro del mouse sul file, quindi scegliere **Proprietà**.

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Caricare gli oggetti nel contenitore *$web* da una directory di origine.

> [!NOTE]
> Se si usa Azure Cloud Shell, assicurarsi di aggiungere un carattere di escape `\` quando si fa riferimento al contenitore `$web` (ad esempio: `\$web`). Se si usa un'installazione locale dell'interfaccia della riga di comando di Azure, non sarà necessario usare il carattere di escape.

In questo esempio si presuppone che siano in esecuzione comandi da Azure Cloud Shell sessione.

```azurecli-interactive
az storage blob upload-batch -s <source-path> -d \$web --account-name <storage-account-name> --content-type 'text/html; charset=utf-8'
```

* Sostituire il valore segnaposto `<storage-account-name>` con il nome del proprio account di archiviazione.

* Sostituire il segnaposto `<source-path>` con un percorso del percorso dei file che si desidera caricare.

> [!NOTE]
> Se si usa un percorso di installazione dell'interfaccia della riga di comando di Azure, è possibile usare il percorso di qualsiasi percorso nel computer locale, ad esempio: `C:\myFolder`.
>
> Se si usa Azure Cloud Shell, sarà necessario fare riferimento a una condivisione file visibile al Cloud Shell. Questo percorso potrebbe corrispondere alla condivisione file della condivisione cloud o a una condivisione file esistente montata dal Cloud Shell. Per informazioni su come eseguire questa operazione, vedere [salvare i file in Azure cloud Shell](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage).

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Caricare gli oggetti nel contenitore *$web* da una directory di origine.

```powershell
# upload a file
set-AzStorageblobcontent -File "<path-to-file>" `
-Properties @{ ContentType = "text/html; charset=utf-8";} `
-Container `$web `
-Blob "<blob-name>" `
-Context $ctx
```

* Sostituire il valore del segnaposto `<path-to-file>` con il percorso completo del file che si desidera caricare, ad esempio: `C:\temp\index.html`.

* Sostituire il valore del segnaposto `<blob-name>` con il nome che si desidera assegnare al BLOB risultante, ad esempio `index.html`.

---

## <a name="find-the-website-url-by-using-the-azure-portal"></a>Trovare l'URL del sito Web usando il portale di Azure

È possibile visualizzare le pagine del sito da un browser usando l'URL pubblico del sito Web.

### <a name="portal"></a>[Portale](#tab/azure-portal)

<a id="portal-find-url" />

Nel riquadro visualizzato accanto alla pagina Panoramica account dell'account di archiviazione selezionare **sito Web statico**. L'URL del sito viene visualizzato nel campo **endpoint primario** .

![Metrica Dati in uscita delle metriche dei siti Web statici di Archiviazione di Azure](./media/storage-blob-static-website/storage-blob-static-website-url.png)

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

<a id="cli-find-url" />

Trovare l'URL pubblico del sito Web statico usando il comando seguente:

```azurecli-interactive
az storage account show -n <storage-account-name> -g <resource-group-name> --query "primaryEndpoints.web" --output tsv
```

* Sostituire il valore segnaposto `<storage-account-name>` con il nome del proprio account di archiviazione.

* Sostituire il valore del segnaposto `<resource-group-name>` con il nome del gruppo di risorse.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

<a id="powershell-find-url" />

Trovare l'URL pubblico del sito Web statico usando usando il comando seguente:

```powershell
 $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -Name "<storage-account-name>"
Write-Output $storageAccount.PrimaryEndpoints.Web
```

* Sostituire il valore del segnaposto `<resource-group-name>` con il nome del gruppo di risorse.

* Sostituire il valore segnaposto `<storage-account-name>` con il nome del proprio account di archiviazione.

---

<a id="metrics" />

## <a name="enable-metrics-on-static-website-pages"></a>Abilitare le metriche nelle pagine del sito Web statico

Dopo aver abilitato le metriche, le statistiche sul traffico sui file nel contenitore **$Web** vengono segnalate nel dashboard delle metriche.

1. Fare clic su **impostazioni** > **monitoraggio** > **metrica**.

   I dati delle metriche vengono generati mediante l'associazione a diverse API di metrica. Il portale visualizza solo i membri delle API usati in un determinato intervallo di tempo per concentrarsi solo sui membri che restituiscono dati. Per essere certi di poter selezionare il membro API necessario, il primo passaggio consiste nell'espandere l'intervallo di tempo.

2. Fare clic sul pulsante relativa all'intervallo di tempo e selezionare le **ultime 24 ore** e quindi fare clic su **applica**.

   ![Intervallo di tempo delle metriche dei siti Web statici di Archiviazione di Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-time-range.png)

3. Selezionare **BLOB** dall'elenco a discesa *spazio dei nomi* .

   ![Spazio dei nomi delle metriche dei siti Web statici di Archiviazione di Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-namespace.png)

4. Selezionare quindi la metrica **Dati in uscita**.

   ![Metrica Dati in uscita delle metriche dei siti Web statici di Archiviazione di Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-metric.png)

5. Selezionare **Somma** dal selettore *Aggregazione*.

   ![Aggregazione delle metriche dei siti Web statici di Archiviazione di Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-aggregation.png)

6. Fare clic sul pulsante **Aggiungi filtro** e scegliere **nome API** dal selettore della *proprietà* .

   ![Nome API delle metriche dei siti Web statici di Archiviazione di Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-api-name.png)

7. Selezionare la casella accanto a **GetWebContent** nel selettore *valori* per popolare il rapporto delle metriche.

   ![GetWebContent delle metriche dei siti Web statici di Archiviazione di Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-getwebcontent.png)

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come configurare un dominio personalizzato con il sito Web statico. Vedere [eseguire il mapping di un dominio personalizzato a un endpoint di archiviazione BLOB di Azure](storage-custom-domain-name.md).

