---
title: Hosting di un sito Web statico in Archiviazione di Azure
description: Informazioni su come gestire il contenuto statico (file HTML, CSS, JavaScript e di immagine) direttamente da un contenitore in un account GP v2 di Archiviazione di Azure.
author: normesta
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.author: normesta
ms.date: 03/04/2020
ms.custom: devx-track-js
ms.openlocfilehash: 2359c762c9ad653ee2eed294709ad7bf6b2a31c0
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92671115"
---
# <a name="host-a-static-website-in-azure-storage"></a>Hosting di un sito Web statico in Archiviazione di Azure

È possibile gestire contenuto statico (file HTML, CSS, JavaScript e di immagine) direttamente da un contenitore in un account GPv2 di Archiviazione di Azure. Per altre informazioni, vedere [Hosting di siti Web statici in Archiviazione di Azure](storage-blob-static-website.md).

Questo articolo illustra come abilitare l'hosting di siti Web statici usando il portale di Azure, l'interfaccia della riga di comando di Azure o PowerShell.

> [!NOTE]
> Assicurarsi di creare un account di archiviazione standard per utilizzo generico V2. I siti Web statici non sono disponibili in nessun altro tipo di account di archiviazione.

## <a name="enable-static-website-hosting"></a>Abilitare l’hosting di siti Web statici

L'hosting di siti Web statici è una funzionalità che deve essere abilitata nell'account di archiviazione.

### <a name="portal"></a>[Portale](#tab/azure-portal)

1. Accedere al [portale di Azure](https://portal.azure.com/) per iniziare.

2. Individuare l'account di archiviazione e visualizzare la sezione della panoramica dell'account.

3. Selezionare **Sito Web statico** per visualizzare la pagina di configurazione per i siti Web statici.

4. Selezionare **Abilitato** per abilitare l'hosting di siti Web statici per l'account di archiviazione.

5. Nel campo **Nome del documento di indice** specificare una pagina di indice predefinita, ad esempio *index.html* . 

   La pagina di indice predefinita viene visualizzata quando un utente passa alla radice del sito Web statico.  

6. Nel campo **Percorso del documento di errore** specificare una pagina di errore predefinita, ad esempio *404.html* . 

   La pagina di errore predefinita viene visualizzata quando un utente tenta di passare a una pagina inesistente nel sito Web statico.

7. Fare clic su **Salva** . Il portale di Azure visualizza ora l'endpoint del sito Web statico. 

    ![Abilitare l'hosting di siti Web statici per un account di archiviazione](media/storage-blob-static-website-host/enable-static-website-hosting.png)

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

<a id="cli"></a>

È possibile abilitare l’hosting dei siti Web statici usando l’[interfaccia della riga di comando di Azure (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

1. Prima di tutto aprire [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest) oppure un'applicazione console dei comandi come Windows PowerShell, se si dispone dell’interfaccia della riga di comando di Azure [installata](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) in locale.

2. Se la propria identità è associata a più sottoscrizioni, impostare come sottoscrizione attiva quella dell’account di archiviazione destinato a ospitare il sito Web statico.

   ```azurecli-interactive
   az account set --subscription <subscription-id>
   ```

   Sostituire il valore segnaposto `<subscription-id>` con l'ID della sottoscrizione.

3. Abilitare l’hosting del sito Web statico.

   ```azurecli-interactive
   az storage blob service-properties update --account-name <storage-account-name> --static-website --404-document <error-document-name> --index-document <index-document-name>
   ```

   * Sostituire il valore segnaposto `<storage-account-name>` con il nome del proprio account di archiviazione.

   * Sostituire il segnaposto `<error-document-name>` con il nome del documento di errore che verrà visualizzato agli utenti quando il browser tenterà di visualizzare una pagina del sito che non esiste.

   * Sostituire il segnaposto `<index-document-name>` con il nome del documento di indice. Questo documento è di solito denominato "index.html".

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

<a id="powershell"></a>

È possibile abilitare l’hosting dei siti Web statici usando il modulo Azure PowerShell.

1. Aprire una finestra dei comandi di Windows PowerShell.

2. Verificare di disporre del modulo Azure PowerShell 0.7 o versioni successive.

   ```powershell
   Get-InstalledModule -Name Az -AllVersions | select Name,Version
   ```

   Se è necessario eseguire l'installazione o l'aggiornamento, vedere come [installare il modulo Azure PowerShell](/powershell/azure/install-Az-ps).

3. Accedere alla sottoscrizione di Azure con il comando `Connect-AzAccount` e seguire le istruzioni visualizzate.

   ```powershell
   Connect-AzAccount
   ```

4. Se la propria identità è associata a più sottoscrizioni, impostare come sottoscrizione attiva quella dell’account di archiviazione destinato a ospitare il sito Web statico.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Sostituire il valore segnaposto `<subscription-id>` con l'ID della sottoscrizione.

5. Ottenere il contesto dell’account di archiviazione che definisce l'account di archiviazione che si intende usare.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * Sostituire il valore segnaposto `<resource-group-name>` con il nome del proprio gruppo di risorse.

   * Sostituire il valore segnaposto `<storage-account-name>` con il nome del proprio account di archiviazione.

6. Abilitare l’hosting del sito Web statico.

   ```powershell
   Enable-AzStorageStaticWebsite -Context $ctx -IndexDocument <index-document-name> -ErrorDocument404Path <error-document-name>
   ```

   * Sostituire il segnaposto `<error-document-name>` con il nome del documento di errore che verrà visualizzato agli utenti quando il browser tenterà di visualizzare una pagina del sito che non esiste.

   * Sostituire il segnaposto `<index-document-name>` con il nome del documento di indice. Questo documento è di solito denominato "index.html".

---

## <a name="upload-files"></a>Caricare file 

### <a name="portal"></a>[Portale](#tab/azure-portal)

Queste istruzioni illustrano come caricare i file usando la versione di Storage Explorer presente nel portale di Azure. Si può tuttavia usare anche la versione di [Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) che viene eseguita esternamente al portale di Azure. È possibile usare [AzCopy](../common/storage-use-azcopy-v10.md), PowerShell, l’interfaccia della riga di comando o una qualsiasi applicazione personalizzata per caricare i file nel contenitore **$web** dell’account. Per un'esercitazione dettagliata sul caricamento dei file con Visual Studio Code, vedere [Esercitazione: Ospitare un sito Web statico nell'archiviazione BLOB](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website-host).

1. Selezionare **Storage Explorer (anteprima)** .

2. Espandere il nodo **CONTENITORI BLOB** e quindi selezionare il contenitore **$web** .

3. Scegliere il pulsante **Carica** per caricare i file.

   ![Caricare file](media/storage-blob-static-website/storage-blob-static-website-upload.png)

4. Se si desidera che il browser visualizzi il contenuto di un file, assicurarsi che il tipo di contenuto del file sia impostato su `text/html`. 

   ![Verificare i tipi di contenuto](media/storage-blob-static-website/storage-blob-static-website-content-type.png)

   >[!NOTE]
   > Storage Explorer imposta automaticamente questa proprietà su `text/html` per le estensioni comunemente riconosciute, ad esempio `.html`. In alcuni casi, tuttavia, è necessario impostare la proprietà manualmente. Se non si imposta questa proprietà su `text/html`, il browser richiederà agli utenti di scaricare il file anziché eseguire il rendering del contenuto. Per impostare questa proprietà, fare clic con il pulsante destro del mouse sul file e quindi fare clic su **Proprietà** .

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Caricare gli oggetti nel contenitore *$web* da una directory di origine.

In questo esempio si presuppone che si eseguano i comandi da una sessione di Azure Cloud Shell.

```azurecli-interactive
az storage blob upload-batch -s <source-path> -d '$web' --account-name <storage-account-name>
```

> [!NOTE] 
> Se il browser richiede agli utenti di scaricare il file invece di eseguire il rendering del contenuto, si può accodare `--content-type 'text/html; charset=utf-8'` al comando. 

* Sostituire il valore segnaposto `<storage-account-name>` con il nome del proprio account di archiviazione.

* Sostituire il segnaposto `<source-path>` con il percorso ai file che si intende caricare.

> [!NOTE]
> Se si usa un’installazione locale dell'interfaccia della riga di comando di Azure, è possibile usare il percorso a qualsiasi posizione nel computer locale, ad esempio `C:\myFolder`.
>
> Se si usa Azure Cloud Shell, sarà necessario fare riferimento a una condivisione file visibile a Cloud Shell. Questo percorso può essere la condivisione file della condivisione Cloud stessa o una condivisione file esistente montata da Cloud Shell. Per altre informazioni in merito, vedere [Rendere persistenti i file in Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage).

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Caricare gli oggetti nel contenitore *$web* da una directory di origine.

```powershell
# upload a file
set-AzStorageblobcontent -File "<path-to-file>" `
-Container `$web `
-Blob "<blob-name>" `
-Context $ctx
```

> [!NOTE] 
> Se il browser richiede agli utenti di scaricare il file invece di eseguire il rendering del contenuto, si può accodare `-Properties @{ ContentType = "text/html; charset=utf-8";}` al comando.

* Sostituire il valore segnaposto `<path-to-file>` con il percorso completo del file che si vuole caricare, ad esempio: `C:\temp\index.html`.

* Sostituire il valore segnaposto `<blob-name>` con il nome che si vuole assegnare al BLOB risultante, ad esempio `index.html`.

---

<a id="portal-find-url"></a>

## <a name="find-the-website-url"></a>Specificare l'URL del sito Web

È possibile visualizzare le pagine del sito da un browser usando l'URL pubblico del sito Web.

### <a name="portal"></a>[Portale](#tab/azure-portal)

Nel riquadro che appare accanto alla pagina della panoramica dell’account di archiviazione selezionare **Sito Web statico** . L'URL del sito viene visualizzato nel campo **Endpoint primario** .

![Metrica Dati in uscita delle metriche dei siti Web statici di Archiviazione di Azure](./media/storage-blob-static-website/storage-blob-static-website-url.png)

### <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Trovare l'URL pubblico del sito Web statico usando il comando seguente:

```azurecli-interactive
az storage account show -n <storage-account-name> -g <resource-group-name> --query "primaryEndpoints.web" --output tsv
```

* Sostituire il valore segnaposto `<storage-account-name>` con il nome del proprio account di archiviazione.

* Sostituire il valore segnaposto `<resource-group-name>` con il nome del proprio gruppo di risorse.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Trovare l'URL pubblico del sito Web statico usando il comando seguente:

```powershell
 $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -Name "<storage-account-name>"
Write-Output $storageAccount.PrimaryEndpoints.Web
```

* Sostituire il valore segnaposto `<resource-group-name>` con il nome del proprio gruppo di risorse.

* Sostituire il valore segnaposto `<storage-account-name>` con il nome del proprio account di archiviazione.

---

<a id="metrics"></a>

## <a name="enable-metrics-on-static-website-pages"></a>Abilitare le metriche nelle pagine del sito Web statico

Dopo che le metriche sono state abilitate, le statistiche sul traffico per i file nel contenitore **$web** vengono riportate nel dashboard delle metriche.

1. Fare clic su **Metriche** nella sezione **Monitoraggio** del menu dell'account di archiviazione.

   > [!div class="mx-imgBorder"]
   > ![Collegamento per le metriche](./media/storage-blob-static-website/metrics-link.png)

   > [!NOTE]
   > I dati delle metriche vengono generati mediante l'associazione a diverse API di metrica. Il portale visualizza solo i membri delle API usati in un determinato intervallo di tempo per concentrarsi solo sui membri che restituiscono dati. Per assicurarsi di poter selezionare il membro dell'API necessario, occorre per prima cosa espandere l'intervallo di tempo.

2. Fare clic sul pulsante dell'intervallo di tempo, scegliere un intervallo di tempo e quindi fare clic su **Applica** .

   ![Intervallo di tempo delle metriche dei siti Web statici di Archiviazione di Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-time-range.png)

3. Selezionare **BLOB** dall'elenco a discesa *Spazio dei nomi* .

   ![Spazio dei nomi delle metriche dei siti Web statici di Archiviazione di Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-namespace.png)

4. Selezionare quindi la metrica **Dati in uscita** .

   ![Screenshot che mostra la metrica di uscita dei siti web statici di archiviazione di Azure.](./media/storage-blob-static-website/storage-blob-static-website-metrics-metric.png)

5. Selezionare **Somma** dal selettore *Aggregazione* .

   ![Aggregazione delle metriche dei siti Web statici di Archiviazione di Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-aggregation.png)

6. Fare clic sul pulsante **Aggiungi filtro** e scegliere **Nome API** dal selettore *Proprietà* .

   ![Nome API delle metriche dei siti Web statici di Archiviazione di Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-api-name.png)

7. Selezionare la casella accanto a **GetWebContent** nel selettore *Valori* per popolare il report sulle metriche.

   ![GetWebContent delle metriche dei siti Web statici di Archiviazione di Azure](./media/storage-blob-static-website/storage-blob-static-website-metrics-getwebcontent.png)

   >[!NOTE]
   > La casella di controllo **GetWebContent** viene visualizzata solo se il membro API è stato usato in un determinato intervallo di tempo. Il portale visualizza solo i membri delle API usati in un determinato intervallo di tempo per concentrarsi solo sui membri che restituiscono dati. Se non è possibile trovare un membro API specifico in questo elenco, ampliare l'intervallo di tempo.

## <a name="next-steps"></a>Passaggi successivi

* Nella prossima esercitazione si vedrà come configurare un dominio personalizzato con il sito Web statico. Vedere [Eseguire il mapping di un dominio personalizzato a un endpoint di Archiviazione BLOB di Azure](storage-custom-domain-name.md).

