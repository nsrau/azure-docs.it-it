---
title: Modificare il modello di licenza per una VM SQL in Azure | Microsoft Docs
description: Come modificare il modello di licensing per una virtual machine SQL in Azure. 
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

---
# Come modificare il modello di licenza per una virtual machine SQL in Azure
Questo articolo descrive come modificare il modello di licenza per una virtual machine SQL Server in Azure, usando il nuovo resource provider - **Microsoft.SqlVirtualMachine**. Esistono due modelli di licenza per una virtual machine (VM) che ospita SQL Server - Pay-per usage e Bring-your-own-license (BYOL). Adesso, tramite PowerShell o Azure CLI, è possibile modificare il modello di licenza applicato ad una VM SQL esistente.

Nel modello **Pay-per-usage** il costo al secondo della VM Azure include il costo della licenza SQL Server.

Il modello **Bring-your-own-license** è altresì conosciuto come [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/), e consente di riutilizzare una licenza SQL Server già acquisita su una VM che esegue SQL Server. Per maggiori informazioni circa i costi, consultare la [guida ai prezzi per le VM SQL Server in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance).

Passare da un modello di licenza all'altro **non richiede fermi del sistema**, non riavvia la VM, **non introduce costi addizionali** (infatti, attivare AHB riduce i costi) ed è **immediatamente effettivo**.

## Registrare una VM SQL preesistente con il nuovo resource provider
La possibilità di modificare il modello di licenza è una funzionalità introdotta dal nuovo resource provider per VM SQL (Microsoft.SqlVirtualMachine).
Al momento, per modificare il modello di licenza, è prima di tutto necessario registrare il nuovo resource provider sulla propria sottoscrizione, e successivamente registrare la VM preesistente con il nuovo resource provider SQL VM.

  >[!IMPORTANTE]
  > Se la risorsa SQL VM viene eliminata, torna in essere il modello di licenza inserito nativamente nell'immagine SQL Server distribuita in oridine. 
  
### Powershell

Il seguente codice registra il nuovo resource provider SQL VM sulla sottoscrizione, e successivamente registra una VM preesistente con il nuovo resource provider.

```powershell
# Connessione to Azure
Connect-AzureRmAccount
Account: <account_name>

# Verificare il proprio Azure Subscription ID
Get-AzureRmContext

# Impostare il corretto Azure Subscription ID
Set-AzureRmContext -SubscriptionId <Subscription_ID>

# Registrare il nuovo resource provider SQL VM sulla sottoscrizione
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine

# Registrare la propria VM SQL preesistente con il nuovo resource provider 
# esempio: $vm=Get-AzureRmVm -ResourceGroupName AHBTest -Name AHBTest​
$vm=Get-AzureRmVm -ResourceGroupName <ResourceGroupName> -Name <VMName>​
New-AzureRmResource -ResourceName $vm.Name ResourceGroupName $vm.ResourceGroupName -Location $vm.Location -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines -Properties @{virtualMachineResourceId=$vm.Id}
```

### Portale
E' possibile registrare il nuovo resource provider SQL VM attraverso il portale. Per farlo, applicare i seguenti passaggi:
1. Aprire il portale Azure e posizionarsi su **All Services**
1. Posizionarsi su **Subscriptions** e selezionare la sottoscrizione di interesse.

  ![Select your subscription](media/virtual-machines-windows-sql-ahb/open-subscriptions.png)

1. Nella blade **Subscriptions**, posizionarsi su **Resource Provider**. 
1. Digitare `sql` nella casella di filtro per selezionare i resource providers legati a SQL. 
1. Selezione *Register*, *Re-register*, o *Unregister* pre il resource provider **Microsoft.SqlVirtualMachine**, a seconda dell'azione desiderata. 

  ![Modificare il provider](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)


## Cambiare il modello di licenza via Powershell
E' possibile utilizzare Powershell per modificare il modello di licenza. Verificare che la propria VM SQL sia già registrata presso il resource provider SQL VM prima di modificare il modello di licenza.

Questo esempio di codice modifica la licenza pay-per-usage in BYOL (o Azure Hybrid Benefit):
```powershell
#esempio: $SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="AHUB"
$SqlVm | Set-AzureRmResource -Force 
``` 
Questo esempio di codice converte il modello di licenza BYOL in pay-per-usage:
```powershell
#esempio: $SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="PAYG"
$SqlVm | Set-AzureRmResource -Force 
```

  >[!NOTA]
  > Per modificare il modello di licenza, occorre utilizzare il nuovo resource provider SQL VM. Qualora si provi ad utilizzare questi comandi prima di registrare la VM con il nuovo resource provider, è possibile che si verifichi il seguente errore: `Get-AzureRmResource : The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/AHBTest' under resource group 'AHBTest' was not found. The property 'sqlServerLicenseType' cannot be found on this object. Verify that the property exists and can be set. ` If you see this error, please [register your SQL VM with the new resource provider](#register-legacy-SQL-vm-with-new-resource-provider). 
 

## Cambiare il modello di licenza via Azure CLI
E' possibile utilizzare Azure CLI per modificare il modello di licenza. Verificare che la propria VM SQL sia già registrata presso il resource provider SQL VM prima di modificare il modello di licenza.

Questo esempio di codice modifica la licenza pay-per-usage in BYOL (o Azure Hybrid Benefit):
```azurecli
az resource update -g <resource_group_name> -n <sql_virtual_machine_name> --resource-type "Microsoft.SqlVirtualMachine/SqlVirtualMachines" --set properties.sqlServerLicenseType=AHUB
# example: az resource update -g AHBTest -n AHBTest --resource-type "Microsoft.SqlVirtualMachine/SqlVirtualMachines" --set properties.sqlServerLicenseType=AHUB
```

Questo esempio di codice converte il modello di licenza BYOL in pay-per-usage:
```azurecli
az resource update -g <resource_group_name> -n <sql_virtual_machine_name> --resource-type "Microsoft.SqlVirtualMachine/SqlVirtualMachines" --set properties.sqlServerLicenseType=PAYG
# example: az resource update -g AHBTest -n AHBTest --resource-type "Microsoft.SqlVirtualMachine/SqlVirtualMachines" --set properties.sqlServerLicenseType=PAYG
```

  >[!NOTA]
  >Per modificare il modello di licenza, occorre utilizzare il nuovo resource provider SQL VM. Qualora si provi ad utilizzare questi comandi prima di registrare la VM con il nuovo resource provider, è possibile che si verifichi il seguente errore: `The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/AHBTest' under resource group 'AHBTest' was not found. ` If you see this error, please [register your SQL VM with the new resource provider](#register-legacy-SQL-vm-with-new-resource-provider). 

## Visualizzare il modello di licenza corrente 

Questo esempio di codice consente di verificare il modello di licenza corrente applicato ad una VM SQL.

```powershell
# Visualizzare il modello di licenza di una SQL VM
#example: $SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm = Get-AzureRmResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType
```

## Prossimi passi

Per maggiori informazioni, consultare: 

* [Panoramica di SQL Server su una VM Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [Domande frequenti su SQL Server in esecuzione in macchine virtuali Windows in Azure](virtual-machines-windows-sql-server-iaas-faq.md)
* [Guida ai prezzi per le VM di SQL Server in Azure](virtual-machines-windows-sql-server-pricing-guidance.md)
* [SQL Server on a Windows VM release notes](virtual-machines-windows-sql-server-iaas-release-notes.md)

