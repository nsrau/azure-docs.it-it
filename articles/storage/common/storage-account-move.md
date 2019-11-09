---
title: Spostare un account di archiviazione di Azure in un'altra area | Microsoft Docs
description: Viene illustrato come spostare un account di archiviazione di Azure in un'altra area.
services: storage
author: normesta
ms.service: storage
ms.subservice: common
ms.topic: article
ms.date: 09/27/2019
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: 8ce949ac997ba7ee38cb057752d89f4b4d22388f
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838714"
---
# <a name="move-an-azure-storage-account-to-another-region"></a>Spostare un account di archiviazione di Azure in un'altra area

Per spostare un account di archiviazione, creare una copia dell'account di archiviazione in un'altra area. Spostare quindi i dati in tale account usando AzCopy o un altro strumento di propria scelta.

L'articolo spiega come:

> [!div class="checklist"]
> 
> * Esportare un modello.
> * Modificare il modello aggiungendo l'area di destinazione e il nome dell'account di archiviazione.
> * Distribuire il modello per creare il nuovo account di archiviazione.
> * Configurare il nuovo account di archiviazione.
> * Spostare i dati nel nuovo account di archiviazione.
> * Eliminare le risorse nell'area di origine.

## <a name="prerequisites"></a>Prerequisiti

- Verificare che i servizi e le funzionalità usati dall'account siano supportati nell'area di destinazione.

- Per le funzionalità di anteprima, assicurarsi che la sottoscrizione sia consentita per l'area di destinazione.

<a id="prepare" />

## <a name="prepare"></a>Preparazione

Per iniziare, esportare e modificare un modello di Gestione risorse. 

### <a name="export-a-template"></a>Esportare un modello

Questo modello contiene le impostazioni che descrivono l'account di archiviazione. 

# <a name="portaltabazure-portal"></a>[Portale](#tab/azure-portal)

Per esportare un modello utilizzando portale di Azure:

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Selezionare **tutte le risorse** e quindi selezionare l'account di archiviazione.

3. Selezionare > **impostazioni** > **Esporta modello**.

4. Scegliere **Scarica** nel pannello **Esporta modello** .

5. Individuare il file zip scaricato dal portale e decomprimere il file in una cartella di propria scelta.

   Questo file zip contiene i file con estensione JSON che comprendono il modello e gli script per distribuire il modello.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Per esportare un modello usando PowerShell:

1. Accedere alla sottoscrizione di Azure con il comando [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) e seguire le istruzioni visualizzate:

   ```azurepowershell-interactive
   Connect-AzAccount
   ```
2. Se l'identità è associata a più di una sottoscrizione, impostare la sottoscrizione attiva sulla sottoscrizione dell'account di archiviazione che si desidera spostare.

   ```azurepowershell-interactive
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

3. Esportare il modello dell'account di archiviazione di origine. Questi comandi salvano un modello JSON nella directory corrente.

   ```azurepowershell-interactive
   $resource = Get-AzResource `
     -ResourceGroupName <resource-group-name> `
     -ResourceName <storage-account-name> `
     -ResourceType Microsoft.Storage/storageAccounts
   Export-AzResourceGroup `
     -ResourceGroupName <resource-group-name> `
     -Resource $resource.ResourceId
   ```

---

### <a name="modify-the-template"></a>Modificare il modello 

Modificare il modello cambiando il nome e l'area dell'account di archiviazione.

# <a name="portaltabazure-portal"></a>[Portale](#tab/azure-portal)

Per distribuire il modello utilizzando portale di Azure:

1. Nel portale di Azure fare clic su **Crea una risorsa**.

2. In **Cerca nel Marketplace** digitare **distribuzione modelli** e quindi premere **INVIO**.

3. Selezionare **Distribuzione modello**.

    ![Raccolta di modelli di Azure Resource Manager](./media/storage-account-move/azure-resource-manager-template-library.png)

4. Selezionare **Crea**.

5. Selezionare **Creare un modello personalizzato nell'editor**.

6. Selezionare **Carica file**e quindi seguire le istruzioni per caricare il file **template. JSON** scaricato nell'ultima sezione.

7. Nel file **template. JSON** assegnare un nome all'account di archiviazione di destinazione impostando il valore predefinito del nome dell'account di archiviazione. Questo esempio Mostra come impostare il valore predefinito del nome dell'account di archiviazione su `mytargetaccount`.
    
    ```json
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccounts_mysourceaccount_name": {
            "defaultValue": "mytargetaccount",
            "type": "String"
        }
    },
 
8. Edit the **location** property in the **template.json** file to the target region. This example sets the target region to `centralus`.

    ```json
    "resources": [{
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2019-04-01",
         "name": "[parameters('storageAccounts_mysourceaccount_name')]",
         "location": "centralus"
         }]          
    ```
    Per ottenere i codici di posizione dell'area, vedere [località di Azure](https://azure.microsoft.com/global-infrastructure/locations/).  Il codice per un'area è il nome dell'area senza spazi, **Stati Uniti centrali** = **centralus**.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Per distribuire il modello usando PowerShell:

1. Nel file **template. JSON** assegnare un nome all'account di archiviazione di destinazione impostando il valore predefinito del nome dell'account di archiviazione. Questo esempio Mostra come impostare il valore predefinito del nome dell'account di archiviazione su `mytargetaccount`.
    
    ```json
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccounts_mysourceaccount_name": {
            "defaultValue": "mytargetaccount",
            "type": "String"
        }
    },
    ``` 

2. Modificare la proprietà **location** nel file **template. JSON** nell'area di destinazione. In questo esempio l'area di destinazione viene impostata su `eastus`.

    ```json
    "resources": [{
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2019-04-01",
         "name": "[parameters('storageAccounts_mysourceaccount_name')]",
         "location": "eastus"
         }]          
    ```

    È possibile ottenere i codici di area eseguendo il comando [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) .

    ```azurepowershell-interactive
    Get-AzLocation | format-table 
    ```
---

<a id="move" />

## <a name="move"></a>Spostamento

Distribuire il modello per creare un nuovo account di archiviazione nell'area di destinazione. 

# <a name="portaltabazure-portal"></a>[Portale](#tab/azure-portal)

1. Salvare il file **template. JSON** .

2. Immettere o selezionare i valori delle proprietà:

- **Sottoscrizione**: selezionare una sottoscrizione di Azure.

- **Gruppo di risorse**: selezionare **Crea nuovo** e assegnare un nome al gruppo di risorse.

- **Località**: selezionare una località di Azure.

3. Fare clic sulla casella di controllo Accetto **i termini e le condizioni indicati in precedenza** , quindi fare clic sul pulsante **Seleziona acquisto** .

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

1. Ottenere l'ID sottoscrizione in cui si vuole distribuire l'IP pubblico di destinazione con [Get-AzSubscription](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-2.5.0):

   ```azurepowershell-interactive
   Get-AzSubscription
   ```

2. Usare questi comandi per distribuire il modello:

   ```azurepowershell-interactive
   $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
   $location = Read-Host -Prompt "Enter the location (i.e. centralus)"

   New-AzResourceGroup -Name $resourceGroupName -Location "$location"
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "<name of your local template file>"  
   ```
---

### <a name="configure-the-new-storage-account"></a>Configurare il nuovo account di archiviazione

Alcune funzionalità non verranno esportate in un modello, pertanto sarà necessario aggiungerle al nuovo account di archiviazione. 

La tabella seguente elenca queste funzionalità insieme alle indicazioni per aggiungerle al nuovo account di archiviazione.

| Funzionalità    | Indicazioni    |
|--------|-----------|
| **Criteri di gestione del ciclo di vita** | [Gestire il ciclo di vita di Archiviazione BLOB di Azure](../blobs/storage-lifecycle-management-concepts.md) |
| **Siti web statici** | [Ospitare un sito Web statico in archiviazione di Azure](../blobs/storage-blob-static-website-how-to.md) |
| **Sottoscrizioni di eventi** | [Reazione agli eventi di archiviazione BLOB](../blobs/storage-blob-event-overview.md) |
| **Avvisi** | [Creare, visualizzare e gestire gli avvisi del log attività tramite monitoraggio di Azure](../../azure-monitor/platform/alerts-activity-log.md) |
| **Rete per la distribuzione di contenuti (rete CDN)** | [Usare la rete CDN di Azure per accedere ai BLOB con domini personalizzati tramite HTTPS](../blobs/storage-https-custom-domain-cdn.md) |

> [!NOTE] 
> Se si configura una rete CDN per l'account di archiviazione di origine, è sufficiente modificare l'origine della rete CDN esistente con l'endpoint del servizio BLOB primario (o l'endpoint del sito Web statico primario) del nuovo account. 

### <a name="move-data-to-the-new-storage-account"></a>Spostare i dati nel nuovo account di archiviazione

Ecco alcuni modi per spostare i dati.

: heavy_check_mark: **Azure Storage Explorer**

  È facile da usare e adatto per piccoli set di dati. È possibile copiare contenitori e condivisioni file e quindi incollarli nell'account di destinazione.

  Vedere [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/);

: heavy_check_mark: **AzCopy**

  Si tratta dell'approccio preferito. È ottimizzato per le prestazioni.  Un modo più rapido è che i dati vengono copiati direttamente tra i server di archiviazione, quindi AzCopy non usa la larghezza di banda di rete del computer. Usare AzCopy nella riga di comando o come parte di uno script personalizzato.

  Vedere [Introduzione a AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

: heavy_check_mark: **Azure Data Factory** 

  Usare questo strumento solo se sono necessarie funzionalità che non sono supportate nella versione corrente di AzCopy. Nella versione corrente di AzCopy, ad esempio, non è possibile copiare i BLOB tra gli account che hanno uno spazio dei nomi gerarchico. Inoltre, AzCopy non mantiene gli elenchi di controllo di accesso ai file o i timestamp dei file (ad esempio, creare e modificare i timestamp). 

  Vedere i collegamenti seguenti:
  - [Copiare dati da e in Archiviazione BLOB di Azure usando Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
  - [Copiare dati da o verso Azure Data Lake Storage Gen2 usando Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
  - [Copiare dati da o ad archiviazione file di Azure usando Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-file-storage)
  - [Copiare dati da e verso l'archiviazione tabelle di Azure usando Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-table-storage)

---

## <a name="discard-or-clean-up"></a>Elimina o Pulisci

Dopo la distribuzione, se si desidera ricominciare, è possibile eliminare l'account di archiviazione di destinazione e ripetere i passaggi descritti nelle sezioni [preparare](#prepare) e [spostare](#move) di questo articolo.

Per eseguire il commit delle modifiche e completare lo spostamento di un account di archiviazione, eliminare l'account di archiviazione di origine.

# <a name="portaltabazure-portal"></a>[Portale](#tab/azure-portal)

Per rimuovere un account di archiviazione usando il portale di Azure:

1. Nel portale di Azure espandere il menu a sinistra per aprire il menu dei servizi e scegliere **account di archiviazione** per visualizzare l'elenco degli account di archiviazione.

2. Individuare l'account di archiviazione di destinazione da eliminare e fare clic con il pulsante destro del mouse sul pulsante **altro** ( **...** ) sul lato destro dell'inserzione.

3. Selezionare **Elimina**e confermare.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Per rimuovere il gruppo di risorse e le risorse associate, incluso il nuovo account di archiviazione, usare il comando [Remove-AzStorageAccount](/powershell/module/az.resources/remove-azstorageaccount) :

```powershell
Remove-AzStorageAccount -ResourceGroupName  $resourceGroup -AccountName $storageAccount
```
---

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato spostato un account di archiviazione di Azure da un'area a un'altra ed è stata eseguita la pulizia delle risorse di origine.  Per altre informazioni sullo trasferimento di risorse tra aree e ripristino di emergenza in Azure, vedere:


- [Spostare le risorse in un altro gruppo di risorse o un'altra sottoscrizione](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Spostare macchine virtuali di Azure in un'altra area](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
