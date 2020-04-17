---
title: Spostare un account di archiviazione di Azure in un'altra area. Documenti Microsoft
description: Viene illustrato come spostare un account di Archiviazione di Azure in un'altra area.
services: storage
author: normesta
ms.service: storage
ms.subservice: common
ms.topic: article
ms.date: 09/27/2019
ms.author: normesta
ms.reviewer: dineshm
ms.openlocfilehash: c8578c518ac45bea147790028c2904c7ce36fffb
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459033"
---
# <a name="move-an-azure-storage-account-to-another-region"></a>Spostare un account di archiviazione di Azure in un'altra area

Per spostare un account di archiviazione, creare una copia dell'account di archiviazione in un'altra area. Quindi, spostare i dati in tale account utilizzando AzCopy o un altro strumento di propria scelta.

In questo articolo si apprenderà come:

> [!div class="checklist"]
> 
> * Esportare un modello.
> * Modificare il modello aggiungendo l'area di destinazione e il nome dell'account di archiviazione.
> * Distribuire il modello per creare il nuovo account di archiviazione.
> * Configurare il nuovo account di archiviazione.
> * Spostare i dati nel nuovo account di archiviazione.
> * Eliminare le risorse nell'area di origine.

## <a name="prerequisites"></a>Prerequisiti

- Verificare che i servizi e le funzionalità utilizzati dall'account siano supportati nell'area geografica di destinazione.

- Per le funzionalità di anteprima, verificare che la sottoscrizione sia inserita nella whitelist per l'area di destinazione.

<a id="prepare" />

## <a name="prepare"></a>Preparazione

Per iniziare, esportare e quindi modificare un modello di Resource Manager.To get started, export, and then modify a Resource Manager template. 

### <a name="export-a-template"></a>Esportare un modello

Questo modello contiene le impostazioni che descrivono l'account di archiviazione. 

# <a name="portal"></a>[Portale](#tab/azure-portal)

Per esportare un modello tramite il portale di Azure:To export a template by using Azure portal:

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Selezionare **Tutte le risorse** e quindi selezionare l'account di archiviazione.

3. Selezionare >**modello di esportazione** **impostazioni** > .

4. Scegliere **Scarica** nel pannello **Esporta modello.**

5. Individuare il file .zip scaricato dal portale e decomprimerlo in una cartella di propria scelta.

   Questo file zip contiene i file .json che costituiscono il modello e gli script per distribuire il modello.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Per esportare un modello tramite PowerShell:To export a template by using PowerShell:

1. Accedere alla sottoscrizione di Azure con il comando Connect-AzAccount e seguire le istruzioni visualizzate:Sign in to your Azure subscription with the [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) command and follow the on-screen directions:

   ```azurepowershell-interactive
   Connect-AzAccount
   ```
2. Se l'identità è associata a più di una sottoscrizione, impostare la sottoscrizione attiva sulla sottoscrizione dell'account di archiviazione che si vuole spostare.

   ```azurepowershell-interactive
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

3. Esportare il modello dell'account di archiviazione di origine. Questi comandi salvano un modello json nella directory corrente.

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

Modificare il modello modificando il nome e l'area dell'account di archiviazione.

# <a name="portal"></a>[Portale](#tab/azure-portal)

Per distribuire il modello tramite il portale di Azure:To deploy the template by using Azure portal:

1. Nel portale di Azure selezionare **Crea una risorsa**.

2. In **Cerca nel Marketplace** digitare **distribuzione modelli** e quindi premere **INVIO**.

3. Selezionare **Distribuzione modello**.

    ![Raccolta di modelli di Azure Resource Manager](./media/storage-account-move/azure-resource-manager-template-library.png)

4. Selezionare **Create** (Crea).

5. Selezionare **Creare un modello personalizzato nell'editor**.

6. Selezionare **Carica file**e quindi seguire le istruzioni per caricare il file **template.json** scaricato nell'ultima sezione.

7. Nel file **template.json** assegnare un nome all'account di archiviazione di destinazione impostando il valore predefinito del nome dell'account di archiviazione. In questo esempio il valore predefinito `mytargetaccount`del nome dell'account di archiviazione viene impostato su .
    
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
    Per ottenere i codici di ubicazione, vedere [Azure Locations](https://azure.microsoft.com/global-infrastructure/locations/).  Il codice per una regione è il nome dell'area senza spazi, **Central US** = **centralus**.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Per distribuire il modello tramite PowerShell:To deploy the template by using PowerShell:

1. Nel file **template.json** assegnare un nome all'account di archiviazione di destinazione impostando il valore predefinito del nome dell'account di archiviazione. In questo esempio il valore predefinito `mytargetaccount`del nome dell'account di archiviazione viene impostato su .
    
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

2. Modificare la proprietà **location** nel file **template.json** nell'area di destinazione. In questo esempio l'area di destinazione viene impostata su `eastus`.

    ```json
    "resources": [{
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2019-04-01",
         "name": "[parameters('storageAccounts_mysourceaccount_name')]",
         "location": "eastus"
         }]          
    ```

    È possibile ottenere i codici di area eseguendo il comando [Get-AzLocation.You](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) can obtain region codes by running the Get-AzLocation command.

    ```azurepowershell-interactive
    Get-AzLocation | format-table 
    ```
---

<a id="move" />

## <a name="move"></a>Spostamento

Distribuire il modello per creare un nuovo account di archiviazione nell'area di destinazione. 

# <a name="portal"></a>[Portale](#tab/azure-portal)

1. Salvare il file **template.json.**

2. Immettere o selezionare i valori delle proprietà:

- **Sottoscrizione:** selezionare una sottoscrizione di Azure.Subscription : Select an Azure subscription.

- **Gruppo di risorse**: selezionare **Crea nuovo** e assegnare un nome al gruppo di risorse.

- **Posizione**: selezionare un percorso di Azure.Location : Select an Azure location.

3. Fare clic sulla casella di controllo **Accetto i termini e le condizioni sopra indicati,** quindi fare clic sul pulsante **Seleziona acquisto.**

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Ottenere l'ID sottoscrizione in cui si vuole distribuire l'indirizzo IP pubblico di destinazione con [Get-AzSubscription](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-2.5.0):

   ```azurepowershell-interactive
   Get-AzSubscription
   ```

2. Usare questi comandi per distribuire il modello:Use these commands to deploy your template:

   ```azurepowershell-interactive
   $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
   $location = Read-Host -Prompt "Enter the location (i.e. centralus)"

   New-AzResourceGroup -Name $resourceGroupName -Location "$location"
   New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "<name of your local template file>"  
   ```
---

### <a name="configure-the-new-storage-account"></a>Configurare il nuovo account di archiviazioneConfigure the new storage account

Alcune funzionalità non vengono esportate in un modello, pertanto sarà necessario aggiungerle al nuovo account di archiviazione. 

Nella tabella seguente sono elencate queste funzionalità insieme alle indicazioni per aggiungerle al nuovo account di archiviazione.

| Funzionalità    | Materiale sussidiario    |
|--------|-----------|
| **Criteri di gestione del ciclo di vita** | [Gestire il ciclo di vita di Archiviazione BLOB di Azure](../blobs/storage-lifecycle-management-concepts.md) |
| **Siti Web statici** | [Ospitare un sito Web statico in Archiviazione di AzureHost a static website in Azure Storage](../blobs/storage-blob-static-website-how-to.md) |
| **Abbonamenti agli eventi** | [Reazione agli eventi di archiviazione BLOBReacting to Blob storage events](../blobs/storage-blob-event-overview.md) |
| **Avvisi** | [Creare, visualizzare e gestire gli avvisi del log attività tramite Monitoraggio di AzureCreate, view, and manage activity log alerts by using Azure Monitor](../../azure-monitor/platform/alerts-activity-log.md) |
| **Rete CDN (Content Delivery Network, rete per la distribuzione di contenuti)** | [Usare Rete CDN di Azure per accedere ai BLOB con domini personalizzati tramite HTTPS](../blobs/storage-https-custom-domain-cdn.md) |

> [!NOTE] 
> Se si configura una rete CDN per l'account di archiviazione di origine, è sufficiente modificare l'origine della rete CDN esistente nell'endpoint del servizio BLOB primario (o nell'endpoint del sito Web statico primario) del nuovo account. 

### <a name="move-data-to-the-new-storage-account"></a>Spostare i dati nel nuovo account di archiviazioneMove data to the new storage account

Ecco alcuni modi per spostare i dati.

:heavy_check_mark: **Azure Storage Explorer**

  È facile da usare e adatto per set di dati di piccole dimensioni. È possibile copiare contenitori e condivisioni file e quindi incollarli nell'account di destinazione.

  Vedere [Esplora archivi di Azure;](https://azure.microsoft.com/features/storage-explorer/)

:heavy_check_mark: **AzCopy**

  Questo è l'approccio preferito. È ottimizzato per le prestazioni.  Un modo per essere più veloci, è che i dati vengono copiati direttamente tra i server di archiviazione, in modo che AzCopy non usi la larghezza di banda di rete del computer. Utilizzare AzCopy nella riga di comando o come parte di uno script personalizzato.

  Vedere [Introduzione a AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

:heavy_check_mark: **Azure Data Factory** 

  Utilizzare questo strumento solo se sono necessarie funzionalità non supportate nella versione corrente di AzCopy. Ad esempio, nella versione corrente di AzCopy, non è possibile copiare BLOB tra account con uno spazio dei nomi gerarchico. Anche AzCopy non mantiene gli elenchi di controllo di accesso ai file o timestamp dei file (ad esempio: creare e modificare timestamp). 

  Vedere questi collegamenti:See these links:
  - [Copiare dati in o dall'archiviazione BLOB di Azure usando Azure Data FactoryCopy data to or from Azure Blob storage by using Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)
  - [Copiare dati da e in Azure Data Lake Storage Gen2 tramite Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)
  - [Copiare dati da o ad Archiviazione file di Azure tramite Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-file-storage)
  - [Copiare dati in e da Archiviazione tabelle di Azure usando Azure Data Factory](https://docs.microsoft.com/azure/data-factory/connector-azure-table-storage)

---

## <a name="discard-or-clean-up"></a>Scartare o pulire

Dopo la distribuzione, se si vuole ricominciare da capo, è possibile eliminare l'account di archiviazione di destinazione e ripetere i passaggi descritti nelle sezioni [Preparazione](#prepare) e [spostamento](#move) di questo articolo.

Per eseguire il commit delle modifiche e completare lo spostamento di un account di archiviazione, eliminare l'account di archiviazione di origine.

# <a name="portal"></a>[Portale](#tab/azure-portal)

Per rimuovere un account di archiviazione tramite il portale di Azure:To remove a storage account by using the Azure portal:

1. Nel portale di Azure espandere il menu a sinistra per aprire il menu dei servizi e scegliere **Account di archiviazione** per visualizzare l'elenco degli account di archiviazione.

2. Individuare l'account di archiviazione di destinazione da eliminare e fare clic con il pulsante destro del mouse sul pulsante **Altro** (**...**) sul lato destro dell'elenco.

3. Selezionare **Elimina**e confermare.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Per rimuovere il gruppo di risorse e le risorse associate, incluso il nuovo account di archiviazione, usare il comando [Remove-AzStorageAccount:To](/powershell/module/az.storage/remove-azstorageaccount) remove the resource group and its associated resources, including the new storage account, use the Remove-AzStorageAccount command:

```powershell
Remove-AzStorageAccount -ResourceGroupName  $resourceGroup -AccountName $storageAccount
```
---

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato spostato un account di archiviazione di Azure da un'area a un'altra e sono stati puliti le risorse di origine.  Per altre informazioni sullo spostamento delle risorse tra aree e il ripristino di emergenza in Azure, vedere:To learn more about moving resources between regions and disaster recovery in Azure, refer to:


- [Spostare le risorse in un altro gruppo di risorse o un'altra sottoscrizione](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Spostare macchine virtuali di Azure in un'altra area](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
