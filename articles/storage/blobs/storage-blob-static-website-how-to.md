---
title: Ospitare un sito Web statico in archiviazione di Azure
description: Informazioni su come rendere disponibile contenuto statico (HTML, CSS, JavaScript e i file di immagine) direttamente da un contenitore in un account per utilizzo generico v2 archiviazione di Azure.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.author: normesta
ms.date: 05/28/2019
ms.openlocfilehash: 5ab24a99b22fae172b5308ba7477953f27ecfd44
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67435946"
---
# <a name="host-a-static-website-in-azure-storage"></a>Ospitare un sito Web statico in archiviazione di Azure

È possibile rendere disponibile contenuto statico (HTML, CSS, JavaScript e i file di immagine) direttamente da un contenitore in un account per utilizzo generico v2 archiviazione di Azure. Per altre informazioni, vedere [hosting di siti Web statici in archiviazione di Azure](storage-blob-static-website.md).

Questo articolo illustra come consentire l'hosting del sito Web statico tramite il portale di Azure, il comando di Azure o PowerShell.

<a id="portal" />

## <a name="use-the-azure-portal"></a>Usare il portale di Azure

Per un'esercitazione dettagliata, vedere [esercitazione: Ospitare un sito Web statico nell'archiviazione BLOB](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website-host).

Dopo aver abilitato l'hosting di siti Web statici, è possibile visualizzare le pagine del sito da un browser usando l'URL del sito Web pubblico.

<a id="portal-find-url" />

### <a name="find-the-website-url-by-using-the-azure-portal"></a>Trovare l'URL del sito Web usando il portale di Azure

Nel riquadro visualizzato accanto alla pagina di panoramica account dell'account di archiviazione, selezionare **sito Web statico**. L'URL del sito verrà visualizzato il **endpoint primario** campo.

![Metrica Dati in uscita delle metriche dei siti Web statici di Archiviazione di Azure](./media/storage-blob-static-website/storage-blob-static-website-url.png)

<a id="cli" />

## <a name="use-the-azure-cli"></a>Utilizzare l’interfaccia della riga di comando di Azure

È possibile abilitare l'hosting di siti Web statici tramite le [interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

1. Prima di tutto, aprire il [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest), o se è già [installato](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) la CLI di Azure in locale, aprire un'applicazione console dei comandi, ad esempio Windows PowerShell.

2. Se l'identità è associata a più di una sottoscrizione, quindi impostare attive sottoscrizione a sottoscrizione dell'account di archiviazione che ospiterà il sito Web statico.

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   Sostituire il `<subscription-id>` valore del segnaposto con l'ID della sottoscrizione.

3. Abilitare l'hosting di siti Web statici.

   ```azurecli-interactive
   az storage blob service-properties update --account-name <storage-account-name> --static-website --404-document <error-document-name> --index-document <index-document-name>
   ```

   * Sostituire il valore segnaposto `<storage-account-name>` con il nome del proprio account di archiviazione.

   * Sostituire il `<error-document-name>` segnaposto con il nome del documento di errore che verrà visualizzati agli utenti quando un browser richiede una pagina nel sito che non esiste.

   * Sostituire il `<index-document-name>` segnaposto con il nome del documento di indice. Questo documento è in genere "index. HTML".

4. Caricare gli oggetti nel contenitore *$web* da una directory di origine.

   > [!NOTE]
   > Se si usa Azure Cloud Shell, assicurarsi di aggiungere un `\` carattere di escape quando si fa riferimento per il `$web` contenitore (ad esempio: `\$web`). Se si usa un'installazione locale della riga di comando di Azure, non sarà necessario usare il carattere di escape.

   Questo esempio si presuppone che si esegue i comandi dalla sessione di Azure Cloud Shell.

   ```azurecli-interactive
   az storage blob upload-batch -s <source-path> -d \$web --account-name <storage-account-name>
   ```

   * Sostituire il valore segnaposto `<storage-account-name>` con il nome del proprio account di archiviazione.

   * Sostituire il `<source-path>` segnaposto con il percorso dei file che si vuole caricare.

   > [!NOTE]
   > Se si usa un'installazione di posizione della riga di comando di Azure, è quindi possibile usare il percorso in un punto qualsiasi nel computer locale (ad esempio: `C:\myFolder`.
   >
   > Se si usa Azure Cloud Shell, è possibile fare riferimento a una condivisione di file che è visibile in Cloud Shell. Questo percorso potrebbe essere la condivisione di file del Cloud condivisione stessa o una condivisione file esistente che si monta da Cloud Shell. Per informazioni su come eseguire questa operazione, vedere [rendere persistenti i file in Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage).

<a id="cli-find-url" />

### <a name="find-the-website-url-by-using-the-azure-cli"></a>Trovare l'URL del sito Web tramite la CLI di Azure

È possibile visualizzare il contenuto da un browser usando l'URL del sito Web pubblico.

Trovare l'URL usando il comando seguente:

```azurecli-interactive
az storage account show -n <storage-account-name> -g <resource-group-name> --query "primaryEndpoints.web" --output tsv
```

* Sostituire il valore segnaposto `<storage-account-name>` con il nome del proprio account di archiviazione.

* Sostituire il `<resource-group-name>` valore del segnaposto con il nome del gruppo di risorse.

<a id="powershell" />

## <a name="use-powershell"></a>Usare PowerShell

È possibile abilitare l'hosting di siti Web statici usando il modulo Azure PowerShell.

1. Aprire una finestra di comando di Windows PowerShell.

2. Verificare di avere Azure PowerShell module Az 0,7 o versioni successive.

   ```powershell
   Get-InstalledModule -Name Az -AllVersions | select Name,Version
   ```

   Se è necessario eseguire l'installazione o l'aggiornamento, vedere come [installare il modulo Azure PowerShell](/powershell/azure/install-Az-ps).

3. Accedere alla sottoscrizione di Azure con il comando `Connect-AzAccount` e seguire le istruzioni visualizzate.

   ```powershell
   Connect-AzAccount
   ```

4. Se l'identità è associata a più di una sottoscrizione, quindi impostare attive sottoscrizione a sottoscrizione dell'account di archiviazione che ospiterà il sito Web statico.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Sostituire il `<subscription-id>` valore del segnaposto con l'ID della sottoscrizione.

5. Ottenere il contesto di account di archiviazione che definisce l'account di archiviazione da usare.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * Sostituire il `<resource-group-name>` valore del segnaposto con il nome del gruppo di risorse.

   * Sostituire il valore segnaposto `<storage-account-name>` con il nome del proprio account di archiviazione.

6. Abilitare l'hosting di siti Web statici.

   ```powershell
   Enable-AzStorageStaticWebsite -Context $ctx -IndexDocument <index-document-name> -ErrorDocument404Path <error-document-name>
   ```

   * Sostituire il `<error-document-name>` segnaposto con il nome del documento di errore che verrà visualizzati agli utenti quando un browser richiede una pagina nel sito che non esiste.

   * Sostituire il `<index-document-name>` segnaposto con il nome del documento di indice. Questo documento è in genere "index. HTML".

7. Caricare gli oggetti nel contenitore *$web* da una directory di origine.

    ```powershell
    # upload a file
    set-AzStorageblobcontent -File "<path-to-file>" `
    -Container `$web `
    -Blob "<blob-name>" `
    -Context $ctx
     ```

   * Sostituire il `<path-to-file>` valore del segnaposto con il percorso completo del file che si vuole caricare (ad esempio: `C:\temp\index.html`).

   * Sostituire il `<blob-name>` valore del segnaposto con il nome che si desidera assegnare al blob risulta (ad esempio: `index.html`).

<a id="powershell-find-url" />

### <a name="find-the-website-url-by-using-powershell"></a>Trovare l'URL del sito Web con PowerShell

È possibile visualizzare il contenuto da un browser usando l'URL del sito Web pubblico.

Trovare l'URL usando il comando seguente:

```powershell
 $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
Write-Output $storageAccount.PrimaryEndpoints.Web
```

* Sostituire il `<resource-group-name>` valore del segnaposto con il nome del gruppo di risorse.

* Sostituire il valore segnaposto `<storage-account-name>` con il nome del proprio account di archiviazione.

<a id="metrics" />

## <a name="enable-metrics-on-static-website-pages"></a>Abilitare le metriche sulle pagine di sito Web statico

Dopo aver abilitato le metriche, il traffico delle statistiche sui file nei **$web** contenitore vengono segnalate nel dashboard di metriche.

1. Fare clic su **le impostazioni** > **monitoraggio** > **metriche**.

   I dati delle metriche vengono generati mediante l'associazione a diverse API di metrica. Il portale visualizza solo i membri delle API usati in un determinato intervallo di tempo per concentrarsi solo sui membri che restituiscono dati. Per assicurare che sia possibile selezionare il membro API necessario, il primo passaggio è necessario espandere l'intervallo di tempo.

2. Fare clic sul pulsante di intervallo di tempo e selezionare **ultime 24 ore** e quindi fare clic su **applica**.

   ![Intervallo di tempo delle metriche dei siti Web statici di Archiviazione di Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-time-range.png)

3. Selezionare **Blob** dalle *Namespace* elenco a discesa.

   ![Spazio dei nomi delle metriche dei siti Web statici di Archiviazione di Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-namespace.png)

4. Selezionare quindi la metrica **Dati in uscita**.

   ![Metrica Dati in uscita delle metriche dei siti Web statici di Archiviazione di Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-metric.png)

5. Selezionare **Somma** dal selettore *Aggregazione*.

   ![Aggregazione delle metriche dei siti Web statici di Archiviazione di Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-aggregation.png)

6. Fare clic sui **Aggiungi filtro** pulsante e scegliere **nome API** dal *proprietà* selettore.

   ![Nome API delle metriche dei siti Web statici di Archiviazione di Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-api-name.png)

7. Controlla la casella accanto a **GetWebContent** nel *valori* selettore per popolare il report sulle metriche.

   ![GetWebContent delle metriche dei siti Web statici di Archiviazione di Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-getwebcontent.png)

## <a name="next-steps"></a>Passaggi successivi

* [Hosting di siti Web statici in Archiviazione di Azure](storage-blob-static-website.md)
* [Usare la rete CDN di Azure per accedere ai BLOB con domini personalizzati tramite HTTPS](storage-https-custom-domain-cdn.md)
* [Configurare un nome di dominio personalizzato per l'endpoint BLOB o Web](storage-custom-domain-name.md)
* [Funzioni di Azure](/azure/azure-functions/functions-overview)
* [Servizio app di Azure](/azure/app-service/overview)
* [Compilare la prima app Web serverless](https://docs.microsoft.com/azure/functions/tutorial-static-website-serverless-api-with-database)
* [Esercitazione: Ospitare un dominio in DNS di Azure](../../dns/dns-delegate-domain-azure-dns.md)
