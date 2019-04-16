---
title: Come cambiare il modello di licenza per una macchina virtuale di SQL Server in Azure | Microsoft Docs
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
ms.date: 02/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: c68bae87440bddf704d18b575aeb1f4ba4760bbb
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/15/2019
ms.locfileid: "59578244"
---
# <a name="how-to-change-the-licensing-model-for-a-sql-server-virtual-machine-in-azure"></a>Come cambiare il modello di licenza per una macchina virtuale SQL Server in Azure
Questo articolo descrive come cambiare il modello di licenza per una macchina virtuale di SQL Server in Azure usando il nuovo provider di risorse della macchina virtuale di SQL **Microsoft.SqlVirtualMachine**. Sono disponibili due modelli per una macchina virtuale (VM) che ospita SQL Server - con pagamento a consumo, licenze e bring your own license (BYOL) per. E a questo punto, tramite il portale di Azure, della riga di comando di Azure o PowerShell è possibile modificare il modello di licenza Usa VM di SQL Server. 

Il **pagamento a consumo** modello (PAYG) significa che il costo al secondo dell'esecuzione della macchina virtuale di Azure include il costo della licenza di SQL Server.

Il **bring-your-own-license** modello (BYOL) è noto anche come il [vantaggio Hybrid Azure (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/), e consente di usare la propria licenza di SQL Server con una macchina virtuale che esegue SQL Server. Per altre informazioni sui prezzi, vedere la [Guida ai prezzi per le VM di SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance).

Il passaggio tra i due modelli di licenza **non comporta tempi di inattività**, non provoca il riavvio della macchina virtuale, **non comporta costi aggiuntivi** (in realtà, l'attivazione del Vantaggio Azure Hybrid *riduce* i costi) e ha **validità immediata**. 

## <a name="remarks"></a>Osservazioni

 - I clienti CSP possono usufruire del Vantaggio Azure Hybrid distribuendo prima una macchina virtuale con pagamento in base al consumo e convertendola quindi in Bring Your Own License. 
 - Quando si registra un'immagine di VM di SQL Server personalizzata con il provider di risorse, specificare il tipo di licenza come = 'AHUB'. Lasciando la licenza digitare vuoti o specificando 'PAYG' causerà l'esito negativo della registrazione. 
 - Se si elimina la risorsa di macchina virtuale SQL Server, tornerà all'impostazione a livello di codice di licenza dell'immagine. 
 - La possibilità di modificare il modello di licenza è una funzionalità del provider di risorse di VM di SQL. Distribuire automaticamente un'immagine del marketplace tramite il portale di Azure registra una VM SQL Server con il provider di risorse. Tuttavia, i clienti che sono self-installazione di SQL Server saranno necessario manualmente [registrare le VM di SQL Server](#register-sql-server-vm-with-the-sql-vm-resource-provider). 

 
## <a name="limitations"></a>Limitazioni

 - La capacità di convertire il modello di licenza è attualmente disponibile solo se si inizia con un'immagine di macchina virtuale di SQL Server con pagamento in base al consumo. Se si inizia con un'immagine Bring Your Own License dal portale, non sarà possibile convertire tale immagine per applicare il pagamento in base al consumo.
 - Modifica il modello di licenza è supportata solo per le macchine virtuali distribuite tramite il modello di Resource Manager. Non sono supportate le macchine virtuali distribuite tramite il modello di distribuzione classica. 
 - Modifica il modello di licenza è abilitata solo per le installazioni di Cloud pubblico.
 - Modifica il modello di licenza è supportata solo su macchine virtuali con una singola scheda di rete (interfaccia di rete). Nelle macchine virtuali con più NIC, è necessario prima di tutto rimuovere uno di interfaccia di rete (usando il portale di Azure) prima di eseguire la procedura. In caso contrario, si verifica un errore simile al seguente: `The virtual machine '\<vmname\>' has more than one NIC associated.` Anche se è possibile aggiungere la scheda di rete nella macchina virtuale dopo aver modificato la modalità gestione licenze, le operazioni eseguite tramite il pannello di configurazione di SQL, ad esempio l'applicazione automatica delle patch e backup, non verranno considerate supportato.

## <a name="prerequisites"></a>Prerequisiti

Per usare il provider di risorse della macchina virtuale di SQL è necessaria l'estensione IaaS SQL. Per procedere quindi con l'utilizzo del provider di risorse della macchina virtuale di SQL, è necessario quanto segue:
- Una [sottoscrizione di Azure](https://azure.microsoft.com/free/).
- [Assurance software](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default). 
- Oggetto *pagamento a consumo* [VM di SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) con il [estensione SQL IaaS](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension) installato. 

## <a name="with-the-azure-portal"></a>Con il portale di Azure

È possibile modificare il modello di licenza direttamente dal portale. 

1. Passare alla VM di SQL Server all'interno di [portale di Azure](https://portal.azure.com). 
1. Selezionare **configurazione di SQL Server** nel **impostazioni** riquadro. 
1. Selezionare **Edit** nel **licenza di SQL Server** riquadro per modificare la licenza. 

![Vantaggio Azure Hybrid nel portale](media/virtual-machines-windows-sql-ahb/ahb-in-portal.png)

  >[!NOTE]
  > Questa opzione non è disponibile per le immagini bring-your-own-license. 

## <a name="with-azure-cli"></a>Con l'interfaccia della riga di comando di Azure

È possibile usare l'interfaccia della riga di comando di Azure per modificare il modello di licenza.  

Il seguente frammento di codice passa il modello di licenza con pagamento a consumo a BYOL (o tramite il vantaggio Azure Hybrid):

```azurecli
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type AHUB
```

Il seguente frammento di codice passa il modello bring-your-own-license in base al consumo: 

```azurecli
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type PAYG

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type PAYG
```
## <a name="with-powershell"></a>Con PowerShell
È possibile usare PowerShell per modificare il modello di licenza.

Il seguente frammento di codice passa il modello di licenza con pagamento a consumo a BYOL (o tramite il vantaggio Azure Hybrid):

```powershell
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
#example: $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="AHUB"
<# the following code snippet is only necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new() #>
$SqlVm | Set-AzResource -Force 
```

Il seguente frammento di codice attiva il modello BYOL di pagamento a consumo:

```powershell
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
#example: $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="PAYG"
<# the following code snippet is only necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new() #>
$SqlVm | Set-AzResource -Force 
```


## <a name="register-sql-server-vm-with-the-sql-vm-resource-provider"></a>Registrare VM di SQL Server con il provider di risorse di VM di SQL
In alcuni casi, potrebbe essere necessario registrare manualmente le VM di SQL Server con il provider di risorse di VM di SQL. A tale scopo, è necessario anche registrare manualmente il provider di risorse con la sottoscrizione. 


### <a name="register-sql-vm-resource-provider-with-subscription"></a>Registrare il provider di risorse di VM di SQL con la sottoscrizione 

Per registrare la macchina virtuale di SQL Server con il provider di risorse SQL, è necessario registrare il provider di risorse nella sottoscrizione. È possibile farlo con il portale di Azure o Azure CLI. 

#### <a name="with-the-azure-portal"></a>Con il portale di Azure
La procedura seguente verrà registrato il provider di risorse SQL alla sottoscrizione di Azure usando il portale di Azure. 

1. Accedere al Portale di Azure e passare a **Tutti i servizi**. 
1. Passare a **Sottoscrizioni** e selezionare la sottoscrizione di interesse.  
1. Nel pannello **Sottoscrizioni** passare a **Provider di risorse**. 
1. Digitare `sql` nel filtro per visualizzare i provider di risorse correlati a SQL. 
1. Selezionare uno tra *Registra*, *Ripeti registrazione*, o *Annulla registrazione* per il provider **Microsoft.SqlVirtualMachine** in base all'azione desiderata. 

   ![Modificare il provider](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)

#### <a name="with-azure-cli"></a>Con l'interfaccia della riga di comando di Azure
Il frammento di codice seguente verrà registrato il provider di risorse di VM di SQL alla sottoscrizione di Azure. 

```azurecli
# Register the new SQL resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

#### <a name="with-powershell"></a>Con PowerShell

Il frammento di codice seguente verrà registrato il provider di risorse SQL alla sottoscrizione di Azure.

```powershell
# Register the new SQL resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```

### <a name="register-sql-server-vm-with-sql-resource-provider"></a>Registrare la macchina virtuale di SQL Server con il provider di risorse SQL
Una volta registrato il provider di risorse di VM di SQL nella sottoscrizione, è quindi possibile registrare VM di SQL Server con il provider di risorse tramite la CLI di Azure. 

#### <a name="with-azure-cli"></a>Con l'interfaccia della riga di comando di Azure
Registrare VM di SQL Server tramite CLI di Azure con il frammento di codice seguente: 

```azurecli
# Register your existing SQL Server VM with the new resource provider
az sql vm create -n <VMName> -g <ResourceGroupName> -l <VMLocation>
```
#### <a name="with-powershell"></a>Con PowerShell
Registrare VM di SQL Server usando PowerShell con il frammento di codice seguente:

```powershell
# Register your existing SQL Server VM with the new resource provider
# example: $vm=Get-AzVm -ResourceGroupName AHBTest -Name AHBTest
$vm=Get-AzVm -ResourceGroupName <ResourceGroupName> -Name <VMName>
New-AzResource -ResourceName $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines -Proper
```



## <a name="known-errors"></a>Errori noti

### <a name="sql-iaas-extension-is-not-installed-on-virtual-machine"></a>L'estensione IaaS SQL non è installata nella macchina virtuale
L'estensione IaaS SQL è un prerequisito necessario per la registrazione della macchina virtuale di SQL Server con il provider di risorse della macchina virtuale di SQL. Se si prova a registrare la macchina virtuale di SQL Server prima di installare l'estensione IaaS SQL, si verificherà l'errore seguente:

`Sql IaaS Extension is not installed on Virtual Machine: '{0}'. Please make sure it is installed and in running state and try again later.`

Per risolvere questo problema, installare l'estensione IaaS SQL prima di provare a registrare la macchina virtuale di SQL Server. 

  > [!NOTE]
  > L'installazione dell'estensione IaaS SQL determina il riavvio del servizio SQL Server e deve essere eseguita solo durante una finestra di manutenzione. Per altre informazioni, vedere [Installazione dell'estensione IaaS SQL](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension#installation). 


### <a name="the-resource-microsoftsqlvirtualmachinesqlvirtualmachinesresource-group-under-resource-group-resource-group-was-not-found-the-property-sqlserverlicensetype-cannot-be-found-on-this-object-verify-that-the-property-exists-and-can-be-set"></a>La risorsa 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/ < resource-group >' nel gruppo di risorse '< resource-group >' non è stata trovata. Impossibile trovare la proprietà 'sqlServerLicenseType' tohoto objektu. Verificare che la proprietà è presente e può essere impostata.
Questo errore si verifica quando si prova a modificare il modello di licenza in una VM di SQL Server che non è stato registrato con il provider di risorse SQL. È necessario registrare il provider di risorse per il [sottoscrizione](#register-sql-vm-resource-provider-with-subscription)e quindi registrare la macchina virtuale di SQL Server con il codice SQL [provider di risorse](#register-sql-server-vm-with-sql-resource-provider). 

### <a name="cannot-validate-argument-on-parameter-sku"></a>Non è possibile convalidare l'argomento per il parametro 'Sku'
Questo errore può verificarsi quando si prova a cambiare il modello di licenza della macchina virtuale di SQL Server usando Azure PowerShell > 4.0: Set-AzResource: Impossibile convalidare l'argomento per il parametro 'Sku'. L'argomento è null o vuoto. Fornire un argomento che non è null o vuoto e quindi riprovare a eseguire il comando.
Per risolvere questo errore, rimuovere il commento di queste righe nel frammento di codice PowerShell indicato in precedenza quando si cambia il modello di licenza:

```powershell
# the following code snippet is necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new()
```

Usare il codice seguente per verificare la versione di Azure PowerShell:

```powershell
Get-Module -ListAvailable -Name Azure -Refresh
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere gli articoli seguenti: 

* [Panoramica di SQL Server in una macchina virtuale Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [SQL Server sulle domande frequenti di una macchina virtuale Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Pricing guidance for SQL Server Azure VMs](virtual-machines-windows-sql-server-pricing-guidance.md) (Guida ai prezzi per le VM di SQL Server in Azure).
* [SQL Server sulle note sulla versione della macchina virtuale Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


