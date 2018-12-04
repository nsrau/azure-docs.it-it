---
title: Come cambiare il modello di licenza per una macchina virtuale SQL in Azure | Microsoft Docs
description: Informazioni su come cambiare le licenze per una macchina virtuale SQL in Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/14/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 3bcbfc876ed27d16180ca03801f2054ad804e148
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/28/2018
ms.locfileid: "52577059"
---
# <a name="how-to-change-the-licensing-model-for-a-sql-virtual-machine-in-azure"></a>Come cambiare il modello di licenza per una macchina virtuale SQL in Azure
Questo articolo descrive come cambiare il modello di licenza per una macchina virtuale di SQL Server in Azure usando il nuovo provider di risorse SQL - **Microsoft.SqlVirtualMachine**. Sono disponibili due modelli di licenza per una macchina virtuale (VM) che ospita SQL Server - con pagamento in base all'utilizzo di licenza e bring your own license (BYOL). E a questo punto, utilizzando Powershell o l'interfaccia della riga di comando di Azure, è possibile modificare il modello di licenza utilizzato dalla macchina virtuale SQL. 

Il modello con **pagamento in base all'utilizzo** implica che il costo al secondo per l'esecuzione della macchina virtuale di Azure include il costo della licenza di SQL Server.

Il modello **Bring-your-own-license** è noto anche come il [Vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-benefit/), e consente di usare la propria licenza di SQL Server con una macchina virtuale che esegue SQL Server. Per altre informazioni sui prezzi, vedere [Guida sui prezzi per macchine virtuali di SQL](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance).

Il passaggio tra i due modelli di licenza comporta **nessun tempo di inattività**, non viene riavviata la macchina virtuale, non comporta **nessun costo aggiuntivo** (in effetti, l'attivazione del Vantaggio Azure Hybrid riduce i costi) e ha **validità immediata**. 


## <a name="register-legacy-sql-vm-with-new-resource-provider"></a>Registrare una macchina virtuale SQL legacy con nuovo provider di risorse
La possibilità di passare tra i modelli di licenza è una funzionalità offerta dal nuovo provider di risorse della macchina virtuale SQL (Microsoft.SqlVirtualMachine). A questo punto, per essere in grado di cambiare il modello di licenza, è necessario innanzitutto registrare il nuovo provider per la sottoscrizione e quindi registrare la macchina virtuale legacy con il nuovo provider di risorse della macchina virtuale SQL. 

  >[!IMPORTANT]
  > Se si rilascia la risorsa della macchina virtuale SQL, si tornerà all'impostazione della licenza hardcoded dell'immagine. 

### <a name="powershell"></a>PowerShell

Il codice seguente registra prima di tutto il nuovo provider di risorse SQL con la sottoscrizione e quindi registra la macchina virtuale di SQL legacy con il nuovo provider di risorse. 

```powershell
# Connect to Azure
Connect-AzureRmAccount
Account: <account_name>

# Verify your subscription ID
Get-AzureRmContext

# Set the correct Azure Subscription ID
Set-AzureRmContext -SubscriptionId <Subscription_ID>

# Register the new SQL resource provider for your subscription
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine

# Register your legacy SQL VM with the new resource provider
# example: $vm=Get-AzureRmVm -ResourceGroupName AHBTest -Name AHBTest
$vm=Get-AzureRmVm -ResourceGroupName <ResourceGroupName> -Name <VMName>
New-AzureRmResource -ResourceName $vm.Name ResourceGroupName $vm.ResourceGroupName -Location $vm.Location -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines -Properties @{virtualMachineResourceId=$vm.Id}
```

### <a name="portal"></a>Portale
È anche possibile registrare il nuovo provider di risorse della macchina virtuale di SQL usando il portale. A tale scopo, seguire questa procedura:
1. Accedere al Portale di Azure e passare a **Tutti i servizi**. 
1. Passare a **Sottoscrizioni** e selezionare la sottoscrizione di interesse.  

  ![Selezionare la propria sottoscrizione](media/virtual-machines-windows-sql-ahb/open-subscriptions.png)

1. Nel pannello **Sottoscrizioni**, passare a **Provider di risorse**. 
1. Digitare `sql` nel filtro per visualizzare i provider di risorse correlati a SQL. 
1. Selezionare uno tra *Registra*, *Ripeti registrazione*, o *Annulla registrazione* per il provider **Microsoft.SqlVirtualMachine** in base all'azione desiderata. 

  ![Modificare il provider](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)


## <a name="use-powershell"></a>Usare PowerShell 
È possibile usare Powershell per modificare il modello di licenza.  Assicurarsi che la macchina virtuale SQL sia già stata registrata con il nuovo provider di risorse SQL prima di cambiare il modello di licenza. 

Questo frammento di codice cambia il modello di licenza da pagamento in base all'utilizzo a BYOL (o usando il Vantaggio Azure Hybrid): 
```powershell
#example: $SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="AHUB"
$SqlVm | Set-AzureRmResource -Force 
``` 

Questo frammento di codice attiva il modello BYOL di pagamento in base all'utilizzo:
```powershell
#example: $SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="PAYG"
$SqlVm | Set-AzureRmResource -Force 
```

  >[!NOTE]
  > Per cambiare le licenze, è necessario usare il nuovo provider di risorse della macchina virtuale SQL. Se si prova a eseguire i comandi seguenti prima di registrare la macchina virtuale SQL con il nuovo provider, è possibile riscontrare questo errore: `Get-AzureRmResource : The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/AHBTest' under resource group 'AHBTest' was not found. The property 'sqlServerLicenseType' cannot be found on this object. Verify that the property exists and can be set. ` Se viene visualizzato questo errore, [registrare la macchina virtuale SQL con il nuovo provider di risorse](#register-legacy-SQL-vm-with-new-resource-provider). 
 

## <a name="use-azure-cli"></a>Utilizzare l'interfaccia della riga di comando di Azure
È possibile usare l'interfaccia della riga di comando di Azure per modificare il modello di licenza.  Assicurarsi che la macchina virtuale SQL sia già stata registrata con il nuovo provider di risorse SQL prima di cambiare il modello di licenza. 

Questo frammento di codice cambia il modello di licenza da pagamento in base all'utilizzo a BYOL (o usando il Vantaggio Azure Hybrid):
```azurecli
az resource update -g <resource_group_name> -n <sql_virtual_machine_name> --resource-type "Microsoft.SqlVirtualMachine/SqlVirtualMachines" --set properties.sqlServerLicenseType=AHUB
# example: az resource update -g AHBTest -n AHBTest --resource-type "Microsoft.SqlVirtualMachine/SqlVirtualMachines" --set properties.sqlServerLicenseType=AHUB
```

Questo frammento di codice cambia il modello da BYOL a pagamento in base all'utilizzo: 
```azurecli
az resource update -g <resource_group_name> -n <sql_virtual_machine_name> --resource-type "Microsoft.SqlVirtualMachine/SqlVirtualMachines" --set properties.sqlServerLicenseType=PAYG
# example: az resource update -g AHBTest -n AHBTest --resource-type "Microsoft.SqlVirtualMachine/SqlVirtualMachines" --set properties.sqlServerLicenseType=PAYG
```

  >[!NOTE]
  >Per cambiare le licenze, è necessario usare il nuovo provider di risorse della macchina virtuale SQL. Se si prova a eseguire i comandi seguenti prima di registrare la macchina virtuale SQL con il nuovo provider, è possibile riscontrare questo errore: `The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/AHBTest' under resource group 'AHBTest' was not found. ` Se viene visualizzato questo errore, [registrare la macchina virtuale SQL con il nuovo provider di risorse](#register-legacy-SQL-vm-with-new-resource-provider). 

## <a name="view-current-licensing"></a>Visualizzare la licenza corrente 

Il frammento di codice seguente consente di visualizzare il modello di licenza corrente per la macchina virtuale SQL. 

```powershell
# View current licensing model for your SQL VM
#example: $SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere gli articoli seguenti: 

* [Panoramica di SQL Server in una macchina virtuale Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server sulle domande frequenti di una macchina virtuale Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [SQL Server in un materiale sussidiario dei prezzi della macchina virtuale di Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [SQL Server sulle note sulla versione della macchina virtuale Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


