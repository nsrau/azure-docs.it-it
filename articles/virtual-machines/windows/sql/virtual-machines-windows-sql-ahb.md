---
title: Modificare il modello di licenza per una macchina virtuale di SQL Server in AzureChange the license model for a SQL Server VM in Azure
description: Informazioni su come cambiare le licenze per una macchina virtuale di SQL Server in Azure dal conto in base al costo alla licenza Bring-your-own usando il vantaggio Azure Hybrid.Learn how to switch licensing for a SQL Server virtual machine in Azure from pay-as-you-go to bring-your own-license by using the Azure Hybrid Benefit.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 502d1fe599accb29ccc99c9e527f8d1c8e1d52b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77201824"
---
# <a name="change-the-license-model-for-a-sql-server-virtual-machine-in-azure"></a>Modificare il modello di licenza per una macchina virtuale di SQL Server in AzureChange the license model for a SQL Server virtual machine in Azure
In questo articolo viene descritto come modificare il modello di licenza per una macchina virtuale (VM) di SQL Server in Azure utilizzando il nuovo provider di risorse di SQL VM, **Microsoft.SqlVirtualMachine**.

Esistono tre modelli di licenza per una macchina virtuale che ospita SQL Server: con pagamento in base al client, vantaggi ibridi di Azure e ripristino di emergenza. È possibile modificare il modello di licenza della macchina virtuale di SQL Server usando il portale di Azure, l'interfaccia della riga di comando di Azure o PowerShell.You can modify the license model of your SQL Server VM by using the Azure portal, the Azure CLI, or PowerShell. 

- Il modello con pagamento in base al costo indica che il costo al secondo per l'esecuzione della macchina virtuale di Azure include il costo della licenza di SQL Server.The **pay-as-you-go** model means that the per-second cost of running the Azure VM includes the cost of the SQL Server license.
- [Il vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-benefit/) consente di usare la propria licenza di SQL Server con una macchina virtuale che esegue SQL Server.Azure Hybrid Benefit allows you to use your own SQL Server license with a VM that's running SQL Server. 
- Il tipo di licenza di ripristino di emergenza viene usato per la [replica di ripristino di](virtual-machines-windows-sql-high-availability-dr.md#free-dr-replica-in-azure) **emergenza** gratuita in Azure.The disaster recovery license type is used for the free DR replica in Azure. 

Il vantaggio Azure Hybrid consente l'uso di licenze di SQL Server con Software Assurance ("Licenza qualificata") nelle macchine virtuali di Azure.Azure Hybrid Benefit allows the use of SQL Server licenses with Software Assurance ("Qualified License") on Azure virtual machines. Con il vantaggio Azure Hybrid, ai clienti non viene addebitato l'uso di una licenza di SQL Server in una macchina virtuale. Ma devono comunque pagare il costo del cloud compute sottostante (ovvero la tariffa di base), l'archiviazione e i backup. Devono anche pagare per l'I/O associato al loro utilizzo dei servizi (se applicabile).

In base alle condizioni del prodotto Microsoft: "I clienti devono indicare che utilizzano il database SQL di Azure (istanza gestita, pool elastico e database singolo), Azure Data Factory, SQL Server Integration Services o macchine virtuali di SQL Server in Azure Vantaggio ibrido per SQL Server durante la configurazione dei carichi di lavoro in Azure."

Per indicare l'uso del vantaggio Azure Hybrid per SQL Server in una macchina virtuale di Azure ed essere conformi, sono disponibili tre opzioni:To indicate the use of Azure Hybrid Benefit for SQL Server on an Azure VM and be compliant, you have three options:

- Effettuare il provisioning di una macchina virtuale usando un'immagine di SQL Server bring-your-own-license da Azure Marketplace.Provision a virtual machine by using a bring your own-own-license SQL Server image from Azure Marketplace. Questa opzione è disponibile solo per i clienti che dispongono di un contratto Enterprise Agreement.
- Effettuare il provisioning di una macchina virtuale usando un'immagine di SQL Server con pagamento in base al client da Azure Marketplace e attivare il vantaggio Azure Hybrid.Provision a virtual machine by using a pay-as-you-go SQL Server image from Azure Marketplace and activate the Azure Hybrid Benefit.
- Installare sql Server automaticamente in una macchina virtuale di Azure, eseguire manualmente la registrazione con il provider di risorse della macchina virtuale SQL e attivare il vantaggio Azure Hybrid.Self-install SQL Server on an Azure VM, [manually register with the SQL VM resource provider](virtual-machines-windows-sql-register-with-resource-provider.md), and activate Azure Hybrid Benefit.

Il tipo di licenza di SQL Server viene impostato quando viene eseguito il provisioning della macchina virtuale. Puoi cambiarlo in qualsiasi momento dopo. Il passaggio da un modello di licenza all'altro non comporta tempi di inattività, non riavvia la macchina virtuale o il servizio SQL Server, non aggiunge costi aggiuntivi ed è immediatamente effettivo. Infatti, l'attivazione del vantaggio Azure Hybrid *riduce i* costi.

## <a name="prerequisites"></a>Prerequisiti

La modifica del modello di licenza della macchina virtuale di SQL Server ha i requisiti seguenti: 

- Una [sottoscrizione di Azure.](https://azure.microsoft.com/free/)
- Una [macchina virtuale di SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) registrata con il provider di risorse della macchina virtuale [SQL.](virtual-machines-windows-sql-register-with-resource-provider.md)
- [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) è un requisito per utilizzare il [vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-benefit/). 


## <a name="vms-already-registered-with-the-resource-provider"></a>Macchine virtuali già registrate con il provider di risorse 

# <a name="portal"></a>[Portale](#tab/azure-portal)

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

È possibile modificare il modello di licenza direttamente dal portale: 

1. Aprire il portale di Azure e aprire la risorsa macchine virtuali SQL per la macchina virtuale di SQL Server.Open the [Azure portal](https://portal.azure.com) and open the [SQL virtual machines resource](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) for your SQL Server VM. 
1. Selezionare **Configura** in **Impostazioni**. 
1. Selezionare l'opzione **Vantaggi ibridi** di Azure e selezionare la casella di controllo per confermare di disporre di una licenza di SQL Server con Software Assurance. 
1. Selezionare **Applica** nella parte inferiore della pagina **Configura.** 

![Vantaggio Azure Hybrid nel portaleAzure Hybrid Benefit in the portal](media/virtual-machines-windows-sql-ahb/ahb-in-portal.png)


# <a name="azure-cli"></a>[Interfaccia della riga di comando di AzureAzure](#tab/azure-cli)

È possibile usare l'interfaccia della riga di comando di Azure per modificare il modello di licenza.  


**Vantaggio dell'ambiente ibrido di AzureAzure hybrid benefit**

```azurecli-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type AHUB
```

**Paga come si va**: 

```azurecli-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type PAYG

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type PAYG
```

**Ripristino di emergenza (DR)**

```azurecli-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type DR

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type DR
```

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

È possibile usare PowerShell per modificare il modello di licenza.

**Vantaggio Azure Hybrid**

```powershell-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType AHUB
```

**Paga come si va**

```powershell-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType PAYG
```

**Ripristino di emergenza** 

```powershell-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType DR
```

---

## <a name="vms-not-registered-with-the-resource-provider"></a>Macchine virtuali non registrate con il provider di risorse

Se è stato eseguito il provisioning di una macchina virtuale di SQL Server da immagini di Azure Marketplace con pagamento in base al rispetto, il tipo di licenza di SQL Server verrà con pagamento in base al client. Se è stato eseguito il provisioning di una macchina virtuale di SQL Server usando un'immagine bring-your-own-license da Azure Marketplace, il tipo di licenza sarà AHUB. Tutte le macchine virtuali di SQL Server di cui è stato eseguito il provisioning dalle immagini predefinite (con pagamento in base al consumo) o bring your own License di Azure Marketplace verranno registrate automaticamente con il provider di risorse della macchina virtuale SQL, in modo da poter modificare il [tipo di licenza.](#vms-already-registered-with-the-resource-provider)

Si è idonei solo per l'installazione automatica di SQL Server in una macchina virtuale di Azure tramite il vantaggio Azure Hybrid.You are only eligible to self-install SQL Server on an Azure VM via Azure Hybrid Benefit. È necessario [registrare queste macchine virtuali con il provider](virtual-machines-windows-sql-register-with-resource-provider.md) di risorse della macchina virtuale SQL impostando la licenza di SQL Server come vantaggio Azure Hybrid per indicare l'utilizzo del vantaggio Azure Hybrid in base alle Condizioni del prodotto Microsoft.You should register these VMs with the SQL VM resource provider by setting the SQL Server license as Azure Hybrid Benefit, to indicate the Azure Hybrid Benefit usage according to Microsoft Product Terms.

È possibile modificare il tipo di licenza di una macchina virtuale di SQL Server come pay-as-you-go o Azure Hybrid Benefit solo se la macchina virtuale di SQL Server è registrata con il provider di risorse della macchina virtuale SQL.

## <a name="remarks"></a>Osservazioni

- I clienti di Azure Cloud Solution Provider (CSP) possono usare il vantaggio Azure Hybrid distribuendo prima una macchina virtuale con pagamento in base al consumo e quindi convertendola in una licenza personale, se dispongono di Software Assurance attivo.
- Se si elimina la risorsa VM di SQL Server, si tornerà all'impostazione di licenza hardcoded dell'immagine. 
- La possibilità di modificare il modello di licenza è una funzionalità del provider di risorse della macchina virtuale SQL. La distribuzione di un'immagine di Azure Marketplace tramite il portale di Azure registra automaticamente una macchina virtuale di SQL Server con il provider di risorse. Tuttavia, i clienti che si installano automaticamente SQL Server sql Server dovranno registrare manualmente la macchina virtuale di [SQL Server.](virtual-machines-windows-sql-register-with-resource-provider.md) 
- L'aggiunta di una macchina virtuale di SQL Server a un set di disponibilità richiede la ricreazione della macchina virtuale. Di conseguenza, tutte le macchine virtuali aggiunte a un set di disponibilità torneranno al tipo di licenza con pagamento in base al costo. Il vantaggio Azure Hybrid dovrà essere abilitato di nuovo. 


## <a name="limitations"></a>Limitazioni

La modifica del modello di licenza è:
   - Disponibile solo per i clienti con [Software Assurance](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-overview).
   - Supportato solo per le edizioni Standard ed Enterprise di SQL Server. Le modifiche alle licenze per Express, Web e Developer non sono supportate. 
   - Supportato solo per le macchine virtuali distribuite tramite il modello di Azure Resource Manager.Only supported for virtual machines deployed through the Azure Resource Manager model. Le macchine virtuali distribuite tramite il modello classico non sono supportate. 
   - Disponibile solo per i cloud pubblici o di Azure per enti pubblici. 
   - Supportato solo su macchine virtuali che dispongono di una singola interfaccia di rete (NIC). 


## <a name="known-errors"></a>Errori noti

### <a name="the-resource-microsoftsqlvirtualmachinesqlvirtualmachinesresource-group-under-resource-group-resource-group-was-not-found"></a>Impossibile trovare la risorsa 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<>' nel gruppo di risorse 'gruppo\<di risorse>'.

Questo errore si verifica quando si tenta di modificare il modello di licenza in una macchina virtuale di SQL Server che non è stata registrata con il provider di risorse VM SQL:

`The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<resource-group>' under resource group '\<resource-group>' was not found. The property 'sqlServerLicenseType' cannot be found on this object. Verify that the property exists and can be set.`

È necessario registrare la sottoscrizione con il provider di risorse e quindi [registrare la macchina virtuale di SQL Server con il provider di risorse.](virtual-machines-windows-sql-register-with-resource-provider.md) 


### <a name="the-virtual-machine-vmname-has-more-than-one-nic-associated"></a>Alla macchina\<virtuale\>' vmname ' sono associate più di una scheda di interfaccia di rete

Questo errore si verifica nelle macchine virtuali con più di una scheda di interfaccia di rete. Rimuovere una delle schede di interfaccia di rete prima di modificare il modello di licenza. Sebbene sia possibile aggiungere nuovamente la scheda di interfaccia di rete alla macchina virtuale dopo aver modificato il modello di licenza, le operazioni nel portale di Azure, ad esempio il backup automatico e l'applicazione di patch, non saranno più supportate. 


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere gli articoli seguenti: 

* [Panoramica di SQL Server in una macchina virtuale WindowsOverview of SQL Server on a Windows VM](virtual-machines-windows-sql-server-iaas-overview.md)
* [Domande frequenti su SQL Server in una macchina virtuale WindowsFAQ for SQL Server on a Windows VM](virtual-machines-windows-sql-server-iaas-faq.md)
* [Indicazioni sui prezzi per SQL Server in una macchina virtuale WindowsPricing guidance for SQL Server on a Windows VM](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Note sulla versione per SQL Server in una macchina virtuale WindowsRelease notes for SQL Server on a Windows VM](virtual-machines-windows-sql-server-iaas-release-notes.md)


