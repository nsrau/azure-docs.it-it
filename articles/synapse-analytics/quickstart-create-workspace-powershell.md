---
title: "Avvio rapido: Creare un'area di lavoro di Synapse con Azure PowerShell"
description: Creare un'area di lavoro di Azure Synapse con Azure PowerShell seguendo la procedura decritta in questa guida.
services: synapse-analytics
author: alehall
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: overview
ms.date: 10/19/2020
ms.author: alehall
ms.reviewer: jrasnick
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 005e3a3b717d4b1b8e5eb02b77a1d228908f8707
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92210578"
---
# <a name="quickstart-create-an-azure-synapse-workspace-with-azure-powershell"></a>Avvio rapido: Creare un'area di lavoro di Azure Synapse con Azure PowerShell

Azure PowerShell è costituito da un set di cmdlet per la gestione delle risorse di Azure direttamente da PowerShell. È possibile usarla nel browser con Azure Cloud Shell. È anche possibile installarlo in macOS, Linux o Windows.

Questa guida di avvio rapido illustra come creare un'area di lavoro di Synapse con Azure PowerShell.

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

- [Account di archiviazione di Azure Data Lake Storage Gen2 ](../storage/common/storage-account-create.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

    > [!IMPORTANT]
    > L'area di lavoro di Azure Synapse deve essere in grado di leggere e scrivere nell'account di ADLS Gen2 selezionato. Per qualsiasi account di archiviazione collegato come primario, è necessario abilitare lo **spazio dei nomi gerarchico** al momento della creazione, come descritto nella pagina [Creare un account di archiviazione](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-powershell#create-a-storage-account).

Se si sceglie di usare Cloud Shell, vedere [Panoramica di Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) per altre informazioni.

### <a name="install-the-azure-powershell-module-locally"></a>Installare il modulo di Azure PowerShell in locale

Se si sceglie di usare PowerShell in locale, per questo articolo è necessario installare il modulo Az PowerShell e connettersi all'account di Azure usando il cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount). Per altre informazioni sull'installazione del modulo Az PowerShell, vedere [Installare Azure PowerShell](/powershell/azure/install-az-ps).

Per altre informazioni sull'autenticazione con Azure PowerShell, vedere [Accedere con Azure PowerShell](/powershell/azure/authenticate-azureps).

### <a name="install-the-azure-synapse-powershell-module"></a>Installare il modulo Azure Synapse di PowerShell

> [!IMPORTANT]
> Durante la fase di anteprima del modulo **Az.Synapse** di PowerShell, è necessario installarlo separatamente usando il cmdlet `Install-Module`. Quando il modulo di PowerShell diventerà disponibile a livello generale, entrerà a far parte delle future versioni del modulo Az di PowerShell e sarà disponibile per impostazione predefinita all'interno di Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.Synapse
```

## <a name="create-an-azure-synapse-workspace-using-azure-powershell"></a>Creare un'area di lavoro di Azure Synapse con Azure PowerShell

1. Definire le variabili di ambiente necessarie per creare le risorse per l'area di lavoro di Azure Synapse.

   |        Nome variabile        |                                                 Descrizione                                                 |
   | --------------------------- | ----------------------------------------------------------------------------------------------------------- |
   | StorageAccountName          | Nome dell'account di archiviazione di ADLS Gen2 esistente.                                                           |
   | StorageAccountResourceGroup | Nome del gruppo di risorse dell'account di archiviazione di ADLS Gen2 esistente.                                             |
   | FileShareName               | Nome del file system di archiviazione esistente.                                                                  |
   | SynapseResourceGroup        | Scegliere un nuovo nome per il gruppo di risorse di Azure Synapse.                                                    |
   | Area                      | Scegliere una delle [aree di Azure](https://azure.microsoft.com/global-infrastructure/geographies/#overview). |
   | SynapseWorkspaceName        | Scegliere un nome univoco per la nuova area di lavoro di Azure Synapse.                                                  |
   | SqlUser                     | Scegliere un valore per un nuovo nome utente.                                                                          |
   | SqlPassword                 | Scegliere una password sicura.                                                                                   |
   | ClientIP                    | L'indirizzo IP pubblico del sistema in cui si esegue PowerShell.                                             |
   |                             |                                                                                                             |

1. Creare un gruppo di risorse come contenitore per l'area di lavoro di Azure Synapse:

   ```azurepowershell-interactive
   New-AzResourceGroup -Name $SynapseResourceGroup -Location $Region
   ```

1. Recuperare la chiave dell'account di archiviazione di ADLS Gen2:

   ```azurepowershell-interactive
   $StorageAccountKey = Get-AzStorageAccountKey -ResourceGroupName $StorageAccountResourceGroup -Name $StorageAccountName |
     Select-Object -First 1 -ExpandProperty Value
    ```

1. Recuperare l'URL dell'endpoint di archiviazione di ADLS Gen2:

   ```azurepowershell-interactive
   $StorageEndpointUrl = (Get-AzStorageAccount -ResourceGroupName $StorageAccountResourceGroup -Name $StorageAccountName).PrimaryEndpoints.Dfs
   ```

1. (Facoltativo) È sempre possibile verificare quali sono l'endpoint e la chiave dell'account di archiviazione di ADLS Gen2:

   ```azurepowershell-interactive
   Write-Output "Storage Account Key: $StorageAccountKey"
   Write-Output "Storage Endpoint URL: $StorageEndpointUrl"
   ```

1. Creare un'area di lavoro di Azure Synapse:

   ```azurepowershell-interactive
   $Cred = New-Object -TypeName System.Management.Automation.PSCredential ($SqlUser, (ConvertTo-SecureString $SqlPassword -AsPlainText -Force))

   $WorkspaceParams = @{
     Name = $SynapseWorkspaceName
     ResourceGroupName = $SynapseResourceGroup
     DefaultDataLakeStorageAccountName = $StorageAccountName
     DefaultDataLakeStorageFilesystem = $FileShareName
     SqlAdministratorLoginCredential = $Cred
     Location = $Region
   }
   New-AzSynapseWorkspace @WorkspaceParams
   ```

1. Ottenere l'URL di sviluppo e Web per l'area di lavoro di Azure Synapse:

   ```azurepowershell-interactive
   $WorkspaceWeb = (Get-AzSynapseWorkspace -Name $SynapseWorkspaceName -ResourceGroupName $StorageAccountResourceGroup).ConnectivityEndpoints.Web
   $WorkspaceDev = (Get-AzSynapseWorkspace -Name $SynapseWorkspaceName -ResourceGroupName $StorageAccountResourceGroup).ConnectivityEndpoints.Dev
   ```

1. Creare una regola del firewall per consentire l'accesso all'area di lavoro di Azure Synapse dal computer in uso:

   ```azurepowershell-interactive
   $FirewallParams = @{
     WorkspaceName = $SynapseWorkspaceName
     Name = 'Allow Client IP'
     ResourceGroupName = $StorageAccountResourceGroup
     StartIpAddress = $ClientIP
     EndIpAddress = $ClientIP
   }
   New-AzSynapseFirewallRule @FirewallParams
   ```

1. Aprire l'indirizzo URL Web dell'area di lavoro di Azure Synapse archiviato nella variabile di ambiente `WorkspaceWeb` per accedere all'area di lavoro:

   ```azurepowershell-interactive
   Start-Process $WorkspaceWeb
   ```

   ![Area di lavoro di Azure Synapse sul Web](media/quickstart-create-synapse-workspace-powershell/create-workspace-powershell-1.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Per eliminare l'area di lavoro di Azure Synapse, seguire questa procedura.

> [!WARNING]
> Con l'eliminazione di un'area di lavoro di Azure Synapse verranno rimossi anche i motori di analisi e i dati archiviati nel database dei pool SQL contenuti e dei metadati dell'area di lavoro. Non sarà più possibile connettersi agli endpoint SQL o Apache Spark. Tutti gli artefatti del codice verranno eliminati (query, notebook, definizioni di processi e pipeline). L'eliminazione dell'area di lavoro **non** influisce sui dati dell'istanza di Data Lake Store Gen2 collegata all'area di lavoro.

Se l'area di lavoro di Azure Synapse creata in questo articolo non è necessaria, è possibile eliminarla eseguendo l'esempio seguente.

```azurepowershell-interactive
Remove-AzSynapseWorkspace -Name $SynapseWorkspaceNam -ResourceGroupName $SynapseResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

Successivamente, è possibile [creare pool SQL](quickstart-create-sql-pool-studio.md) o [pool di Apache Spark](quickstart-create-apache-spark-pool-studio.md) per iniziare ad analizzare ed esplorare i dati.
