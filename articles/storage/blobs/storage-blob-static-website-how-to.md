---
title: Ospitare un sito Web statico in archiviazione di Azure
description: Informazioni su come gestire contenuto statico (file HTML, CSS, JavaScript e immagini) direttamente da un contenitore in un account GPv2 di archiviazione di Azure.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.author: normesta
ms.date: 05/28/2019
ms.openlocfilehash: e53f8dd832b7f19692b9095e913330848e5b0330
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385773"
---
# <a name="host-a-static-website-in-azure-storage"></a>Ospitare un sito Web statico in archiviazione di Azure

È possibile gestire il contenuto statico (file HTML, CSS, JavaScript e di immagine) direttamente da un contenitore in un account GPv2 di archiviazione di Azure. Per altre informazioni, vedere [hosting di siti web statici in archiviazione di Azure](storage-blob-static-website.md).

Questo articolo illustra come abilitare l'hosting di siti web statici usando il portale di Azure, l'interfaccia della riga di comando di Azure o PowerShell.

<a id="portal" />

## <a name="use-the-azure-portal"></a>Usare il portale di Azure

Per un'esercitazione dettagliata, vedere [esercitazione: Ospitare un sito Web statico nell'archiviazione BLOB](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website-host).

Dopo aver abilitato l'hosting di siti web statici, è possibile visualizzare le pagine del sito da un browser usando l'URL pubblico del sito Web.

<a id="portal-find-url" />

### <a name="find-the-website-url-by-using-the-azure-portal"></a>Trovare l'URL del sito Web usando il portale di Azure

Nel riquadro visualizzato accanto alla pagina Panoramica account dell'account di archiviazione selezionare **sito Web statico**. L'URL del sito viene visualizzato nel campo **endpoint primario** .

![Metrica Dati in uscita delle metriche dei siti Web statici di Archiviazione di Azure](./media/storage-blob-static-website/storage-blob-static-website-url.png)

<a id="cli" />

## <a name="use-the-azure-cli"></a>Utilizzare l’interfaccia della riga di comando di Azure

È possibile abilitare l'hosting di siti web statici usando l' [interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

1. Aprire prima di tutto la [Azure cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)o, se l'interfaccia della riga di comando di Azure è stata [installata](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) localmente, aprire un'applicazione console comando come Windows PowerShell.

2. Se l'identità è associata a più di una sottoscrizione, impostare la sottoscrizione attiva sulla sottoscrizione dell'account di archiviazione che ospiterà il sito Web statico.

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   Sostituire il `<subscription-id>` valore del segnaposto con l'ID della sottoscrizione.

3. Abilita l'hosting di siti web statici.

   ```azurecli-interactive
   az storage blob service-properties update --account-name <storage-account-name> --static-website --404-document <error-document-name> --index-document <index-document-name>
   ```

   * Sostituire il valore segnaposto `<storage-account-name>` con il nome del proprio account di archiviazione.

   * Sostituire il `<error-document-name>` segnaposto con il nome del documento di errore che verrà visualizzato agli utenti quando un browser richiede una pagina nel sito che non esiste.

   * Sostituire il `<index-document-name>` segnaposto con il nome del documento di indice. Questo documento è comunemente "index. html".

4. Caricare gli oggetti nel contenitore *$web* da una directory di origine.

   > [!NOTE]
   > Se si usa Azure cloud Shell, assicurarsi di aggiungere un `\` carattere di escape quando si fa riferimento `$web` al contenitore (ad esempio: `\$web`). Se si usa un'installazione locale dell'interfaccia della riga di comando di Azure, non sarà necessario usare il carattere di escape.

   In questo esempio si presuppone che siano in esecuzione comandi da Azure Cloud Shell sessione.

   ```azurecli-interactive
   az storage blob upload-batch -s <source-path> -d \$web --account-name <storage-account-name>
   ```

   * Sostituire il valore segnaposto `<storage-account-name>` con il nome del proprio account di archiviazione.

   * Sostituire il `<source-path>` segnaposto con un percorso per il percorso dei file che si desidera caricare.

   > [!NOTE]
   > Se si usa un percorso di installazione dell'interfaccia della riga di comando di Azure, è possibile usare il percorso di qualsiasi percorso nel computer locale ( `C:\myFolder`ad esempio:.
   >
   > Se si usa Azure Cloud Shell, sarà necessario fare riferimento a una condivisione file visibile al Cloud Shell. Questo percorso potrebbe corrispondere alla condivisione file della condivisione cloud o a una condivisione file esistente montata dal Cloud Shell. Per informazioni su come eseguire questa operazione, vedere [salvare i file in Azure cloud Shell](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage).

<a id="cli-find-url" />

### <a name="find-the-website-url-by-using-the-azure-cli"></a>Trovare l'URL del sito Web usando l'interfaccia della riga di comando di Azure

È possibile visualizzare il contenuto da un browser utilizzando l'URL pubblico del sito Web.

Trovare l'URL usando il comando seguente:

```azurecli-interactive
az storage account show -n <storage-account-name> -g <resource-group-name> --query "primaryEndpoints.web" --output tsv
```

* Sostituire il valore segnaposto `<storage-account-name>` con il nome del proprio account di archiviazione.

* Sostituire il `<resource-group-name>` valore del segnaposto con il nome del gruppo di risorse.

<a id="powershell" />

## <a name="use-powershell"></a>Usare PowerShell

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

   Sostituire il `<subscription-id>` valore del segnaposto con l'ID della sottoscrizione.

5. Ottenere il contesto dell'account di archiviazione che definisce l'account di archiviazione che si vuole usare.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * Sostituire il `<resource-group-name>` valore del segnaposto con il nome del gruppo di risorse.

   * Sostituire il valore segnaposto `<storage-account-name>` con il nome del proprio account di archiviazione.

6. Abilita l'hosting di siti web statici.

   ```powershell
   Enable-AzStorageStaticWebsite -Context $ctx -IndexDocument <index-document-name> -ErrorDocument404Path <error-document-name>
   ```

   * Sostituire il `<error-document-name>` segnaposto con il nome del documento di errore che verrà visualizzato agli utenti quando un browser richiede una pagina nel sito che non esiste.

   * Sostituire il `<index-document-name>` segnaposto con il nome del documento di indice. Questo documento è comunemente "index. html".

7. Caricare gli oggetti nel contenitore *$web* da una directory di origine.

    ```powershell
    # upload a file
    set-AzStorageblobcontent -File "<path-to-file>" `
    -Container `$web `
    -Blob "<blob-name>" `
    -Context $ctx
     ```

   * Sostituire il `<path-to-file>` valore del segnaposto con il percorso completo del file che si desidera caricare, ad esempio:. `C:\temp\index.html`

   * Sostituire il `<blob-name>` valore del segnaposto con il nome che si desidera assegnare al BLOB risultante, ad esempio `index.html`:.

<a id="powershell-find-url" />

### <a name="find-the-website-url-by-using-powershell"></a>Trovare l'URL del sito Web usando PowerShell

È possibile visualizzare il contenuto da un browser utilizzando l'URL pubblico del sito Web.

Trovare l'URL usando il comando seguente:

```powershell
 $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
Write-Output $storageAccount.PrimaryEndpoints.Web
```

* Sostituire il `<resource-group-name>` valore del segnaposto con il nome del gruppo di risorse.

* Sostituire il valore segnaposto `<storage-account-name>` con il nome del proprio account di archiviazione.

<a id="metrics" />

## <a name="enable-metrics-on-static-website-pages"></a>Abilitare le metriche nelle pagine del sito Web statico

Dopo aver abilitato le metriche, le statistiche sul traffico sui file nel contenitore **$Web** vengono segnalate nel dashboard delle metriche.

1. Fare clic su **Impostazioni** > **metriche**di**monitoraggio** > .

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

* [Hosting di siti Web statici in Archiviazione di Azure](storage-blob-static-website.md)
* [Usare la rete CDN di Azure per accedere ai BLOB con domini personalizzati tramite HTTPS](storage-https-custom-domain-cdn.md)
* [Configurare un nome di dominio personalizzato per l'endpoint BLOB o Web](storage-custom-domain-name.md)
* [Funzioni di Azure](/azure/azure-functions/functions-overview)
* [Servizio app di Azure](/azure/app-service/overview)
* [Compilare la prima app Web serverless](https://docs.microsoft.com/azure/functions/tutorial-static-website-serverless-api-with-database)
* [Esercitazione: Ospitare un dominio in DNS di Azure](../../dns/dns-delegate-domain-azure-dns.md)
