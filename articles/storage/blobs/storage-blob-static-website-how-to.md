---
title: Ospitare un sito Web statico in Archiviazione di AzureHost a static website in Azure Storage
description: Informazioni su come gestire contenuto statico (file HTML, CSS, JavaScript e immagini) direttamente da un contenitore in un account GPv2 di Archiviazione di Azure.Learn how to serve static content (HTML, CSS, JavaScript, and image files) directly from a container in an Azure Storage GPv2 account.
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.author: normesta
ms.date: 03/04/2020
ms.openlocfilehash: 056e23f0f0cf1a3a1c70042cef3c92dd41f14f82
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247011"
---
# <a name="host-a-static-website-in-azure-storage"></a>Ospitare un sito Web statico in Archiviazione di AzureHost a static website in Azure Storage

È possibile gestire contenuto statico (file HTML, CSS, JavaScript e immagine) direttamente da un contenitore in un account GPv2 di Archiviazione di Azure.You can serve static content (HTML, CSS, JavaScript, and image files) directly from a container in an Azure Storage GPv2 account. Per altre informazioni, vedere [Hosting di siti Web statico in Archiviazione di Azure.To](storage-blob-static-website.md)learn more, see Static website hosting in Azure Storage .

Questo articolo illustra come abilitare l'hosting statico del sito Web usando il portale di Azure, l'interfaccia della riga di comando di Azure o PowerShell.This article shows you how to enable static website hosting by using the Azure portal, the Azure CLI, or PowerShell.

## <a name="enable-static-website-hosting"></a>Abilitare l'hosting statico di siti Web

L'hosting statico di siti Web è una funzionalità che è necessario abilitare nell'account di archiviazione.

### <a name="portal"></a>[Portale](#tab/azure-portal)

1. Accedere al [portale di Azure](https://portal.azure.com/) per iniziare.

2. Individuare l'account di archiviazione e visualizzare la sezione della panoramica dell'account.

3. Selezionare **Sito Web statico** per visualizzare la pagina di configurazione per i siti Web statici.

4. Selezionare **Abilitato** per abilitare l'hosting di siti Web statici per l'account di archiviazione.

5. Nel campo **Nome documento indice,** specificate una pagina di indice predefinita (ad esempio: *index.html*). 

   La pagina di indice predefinita viene visualizzata quando un utente passa alla radice del sito Web statico.  

6. Nel campo **Percorso documento di** errore specificare una pagina di errore predefinita (ad esempio: *404.html*). 

   La pagina di errore predefinita viene visualizzata quando un utente tenta di passare a una pagina inesistente nel sito Web statico.

7. Fare clic su **Salva**. Il portale di Azure visualizza ora l'endpoint del sito Web statico. 

    ![Abilitare l'hosting di siti Web statici per un account di archiviazione](media/storage-blob-static-website-host/enable-static-website-hosting.png)

### <a name="azure-cli"></a>[Interfaccia della riga di comando di AzureAzure](#tab/azure-cli)

<a id="cli" />

È possibile abilitare l'hosting statico del sito Web usando l'interfaccia della [riga di comando (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)di Azure.

1. Aprire innanzitutto Azure Cloud Shell oppure, se l'interfaccia della riga di comando di Azure è stata [installata](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) localmente, aprire un'applicazione della console dei comandi, ad esempio Windows PowerShell.First, open the [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest), or if you've installed the Azure CLI locally, open a command console application such as Windows PowerShell.

2. Se l'identità è associata a più di una sottoscrizione, impostare la sottoscrizione attiva sulla sottoscrizione dell'account di archiviazione che ospiterà il sito Web statico.

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   Sostituire `<subscription-id>` il valore segnaposto con l'ID della sottoscrizione.

3. Abilitare l'hosting statico di siti Web.

   ```azurecli-interactive
   az storage blob service-properties update --account-name <storage-account-name> --static-website --404-document <error-document-name> --index-document <index-document-name>
   ```

   * Sostituire il valore segnaposto `<storage-account-name>` con il nome del proprio account di archiviazione.

   * Sostituire `<error-document-name>` il segnaposto con il nome del documento di errore che verrà visualizzato agli utenti quando un browser richiede una pagina del sito che non esiste.

   * Sostituire `<index-document-name>` il segnaposto con il nome del documento di indice. Questo documento è comunemente "index.html".

### <a name="powershell"></a>[Powershell](#tab/azure-powershell)

<a id="powershell" />

È possibile abilitare l'hosting statico del sito Web usando il modulo Azure PowerShell.You can enable static website hosting by using the Azure PowerShell module.

1. Aprire una finestra di comando di Windows PowerShell.Open a Windows PowerShell command window.

2. Verificare di disporre del modulo di Azure PowerShell az versione 0.7 o successiva.

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

   Sostituire `<subscription-id>` il valore segnaposto con l'ID della sottoscrizione.

5. Ottenere il contesto dell'account di archiviazione che definisce l'account di archiviazione da usare.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * Sostituire `<resource-group-name>` il valore segnaposto con il nome del gruppo di risorse.

   * Sostituire il valore segnaposto `<storage-account-name>` con il nome del proprio account di archiviazione.

6. Abilitare l'hosting statico di siti Web.

   ```powershell
   Enable-AzStorageStaticWebsite -Context $ctx -IndexDocument <index-document-name> -ErrorDocument404Path <error-document-name>
   ```

   * Sostituire `<error-document-name>` il segnaposto con il nome del documento di errore che verrà visualizzato agli utenti quando un browser richiede una pagina del sito che non esiste.

   * Sostituire `<index-document-name>` il segnaposto con il nome del documento di indice. Questo documento è comunemente "index.html".

---

## <a name="upload-files"></a>Caricare file 

### <a name="portal"></a>[Portale](#tab/azure-portal)

Queste istruzioni illustrano come caricare i file usando la versione di Storage Explorer visualizzata nel portale di Azure.These instructions show you how to upload files by using the version of Storage Explorer that appears in the Azure portal. Tuttavia, è anche possibile usare la versione di Storage Explorer che viene eseguita all'esterno del portale di Azure.However, you can also use the version of [Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) that runs outside of the Azure portal. È possibile usare [AzCopy](../common/storage-use-azcopy-v10.md), PowerShell, CLI o qualsiasi applicazione personalizzata in grado di caricare file nel **contenitore $web** dell'account. Per un'esercitazione dettagliata che consente di caricare file tramite codice di Visual Studio, vedere [Esercitazione: Ospitare un sito Web statico nell'archiviazione BLOB.](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website-host)

1. Selezionare **Storage Explorer (anteprima)**.

2. Espandere il nodo **BLOB CONTAINERS** e quindi selezionare il contenitore **$web.**

3. Scegliere il pulsante **Carica** per caricare i file.

   ![Caricare file](media/storage-blob-static-website/storage-blob-static-website-upload.png)

4. Se si desidera che il browser visualizzi il contenuto del file, assicurarsi che il tipo di contenuto di tale file sia impostato su `text/html`. 

   ![Controllare i tipi di contenuto](media/storage-blob-static-website/storage-blob-static-website-content-type.png)

   >[!NOTE]
   > Storage Explorer imposta automaticamente `text/html` questa proprietà su `.html`per le estensioni riconosciute comunemente, ad esempio . Tuttavia, in alcuni casi, dovrai impostarlo da solo. Se non si imposta questa `text/html`proprietà su , il browser richiederà agli utenti di scaricare il file anziché eseguirne il rendering del contenuto. Per impostare questa proprietà, fare clic con il pulsante destro del mouse sul file, quindi **scegliere Proprietà**.

### <a name="azure-cli"></a>[Interfaccia della riga di comando di AzureAzure](#tab/azure-cli)

Caricare oggetti nel contenitore *$web* da una directory di origine.

> [!NOTE]
> Se si usa Azure Cloud Shell, assicurarsi di aggiungere `\` `$web` un carattere `\$web`di escape quando si fa riferimento al contenitore (ad esempio: ). Se si usa un'installazione locale dell'interfaccia della riga di comando di Azure, non sarà necessario usare il carattere di escape.

Questo esempio presuppone che si stiano eseguendo comandi dalla sessione di Azure Cloud Shell.This example assumes that you're running commands from Azure Cloud Shell session.

```azurecli-interactive
az storage blob upload-batch -s <source-path> -d \$web --account-name <storage-account-name> --content-type 'text/html; charset=utf-8'
```

* Sostituire il valore segnaposto `<storage-account-name>` con il nome del proprio account di archiviazione.

* Sostituire `<source-path>` il segnaposto con un percorso nel percorso dei file che si desidera caricare.

> [!NOTE]
> Se si usa un'installazione del percorso dell'interfaccia della riga di comando di Azure, è possibile usare il percorso di qualsiasi percorso nel computer locale (ad esempio: `C:\myFolder`.
>
> Se si usa Azure Cloud Shell, è necessario fare riferimento a una condivisione file visibile a Cloud Shell. Questo percorso può essere la condivisione file della condivisione Cloud stessa o una condivisione file esistente montata da Cloud Shell. Per informazioni su come eseguire questa operazione, vedere Rendere persistenti i [file in Azure Cloud Shell.](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage)

### <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Caricare oggetti nel contenitore *$web* da una directory di origine.

```powershell
# upload a file
set-AzStorageblobcontent -File "<path-to-file>" `
-Properties @{ ContentType = "text/html; charset=utf-8";} `
-Container `$web `
-Blob "<blob-name>" `
-Context $ctx
```

* Sostituire `<path-to-file>` il valore segnaposto con il percorso completo del file che `C:\temp\index.html`si desidera caricare (ad esempio: ).

* Sostituire `<blob-name>` il valore segnaposto con il nome che si vuole `index.html`assegnare al BLOB risultante (ad esempio: ).

---

## <a name="find-the-website-url-by-using-the-azure-portal"></a>Trovare l'URL del sito Web tramite il portale di AzureFind the website url by using the Azure portal

È possibile visualizzare le pagine del sito da un browser utilizzando l'URL pubblico del sito Web.

### <a name="portal"></a>[Portale](#tab/azure-portal)

<a id="portal-find-url" />

Nel riquadro visualizzato accanto alla pagina di panoramica dell'account di archiviazione selezionare **Sito Web statico**. L'URL del sito viene visualizzato nel campo **Endpoint primario.**

![Metrica Dati in uscita delle metriche dei siti Web statici di Archiviazione di Azure](./media/storage-blob-static-website/storage-blob-static-website-url.png)

### <a name="azure-cli"></a>[Interfaccia della riga di comando di AzureAzure](#tab/azure-cli)

<a id="cli-find-url" />

Trovare l'URL pubblico del sito Web statico utilizzando il comando seguente:

```azurecli-interactive
az storage account show -n <storage-account-name> -g <resource-group-name> --query "primaryEndpoints.web" --output tsv
```

* Sostituire il valore segnaposto `<storage-account-name>` con il nome del proprio account di archiviazione.

* Sostituire `<resource-group-name>` il valore segnaposto con il nome del gruppo di risorse.

### <a name="powershell"></a>[Powershell](#tab/azure-powershell)

<a id="powershell-find-url" />

Trovare l'URL pubblico del sito Web statico utilizzando il comando seguente:

```powershell
 $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -Name "<storage-account-name>"
Write-Output $storageAccount.PrimaryEndpoints.Web
```

* Sostituire `<resource-group-name>` il valore segnaposto con il nome del gruppo di risorse.

* Sostituire il valore segnaposto `<storage-account-name>` con il nome del proprio account di archiviazione.

---

<a id="metrics" />

## <a name="enable-metrics-on-static-website-pages"></a>Abilitare le metriche nelle pagine statiche del sito Web

Dopo aver abilitato le metriche, le statistiche sul traffico relative ai file nel contenitore **$web** vengono segnalate nel dashboard delle metriche.

1. Fare clic su **Metriche** nella sezione **Monitoraggio** del menu dell'account di archiviazione.

   > [!div class="mx-imgBorder"]
   > ![Collegamento Metriche](./media/storage-blob-static-website/metrics-link.png)

   > [!NOTE]
   > I dati delle metriche vengono generati mediante l'associazione a diverse API di metrica. Il portale visualizza solo i membri delle API usati in un determinato intervallo di tempo per concentrarsi solo sui membri che restituiscono dati. Per garantire che tu sia in grado di selezionare il membro API necessario, il primo passaggio consiste nell'espandere l'intervallo di tempo.

2. Fare clic sul pulsante dell'intervallo di tempo, scegliere un intervallo di tempo e quindi fare clic su **Applica**.

   ![Intervallo di tempo delle metriche dei siti Web statici di Archiviazione di Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-time-range.png)

3. Selezionare **BLOB** dall'elenco a discesa *Spazio dei nomi.*

   ![Spazio dei nomi delle metriche dei siti Web statici di Archiviazione di Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-namespace.png)

4. Selezionare quindi la metrica **Dati in uscita**.

   ![Metrica Dati in uscita delle metriche dei siti Web statici di Archiviazione di Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-metric.png)

5. Selezionare **Somma** dal selettore *Aggregazione*.

   ![Aggregazione delle metriche dei siti Web statici di Archiviazione di Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-aggregation.png)

6. Fare clic sul pulsante **Aggiungi filtro** e scegliere il **nome dell'API** dal selettore *Proprietà.*

   ![Nome API delle metriche dei siti Web statici di Archiviazione di Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-api-name.png)

7. Seleziona la casella accanto a **GetWebContent** nel selettore *Valori* per popolare il rapporto sulle metriche.

   ![GetWebContent delle metriche dei siti Web statici di Archiviazione di Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-getwebcontent.png)

   >[!NOTE]
   > La casella di controllo **GetWebContent** viene visualizzata solo se tale membro API è stato utilizzato entro un determinato intervallo di tempo. Il portale visualizza solo i membri delle API usati in un determinato intervallo di tempo per concentrarsi solo sui membri che restituiscono dati. Se non riesci a trovare un membro API specifico in questo elenco, espandi l'intervallo di tempo.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come configurare un dominio personalizzato con il sito Web statico. Vedere Eseguire il mapping di un dominio personalizzato a un endpoint di Archiviazione BLOB di [Azure.See Map a custom](storage-custom-domain-name.md)domain to an Azure Blob Storage endpoint.

