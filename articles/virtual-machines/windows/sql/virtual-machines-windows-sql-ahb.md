---
title: Modificare il modello di licenza per una macchina virtuale SQL Server in Azure
description: Informazioni su come cambiare le licenze per una macchina virtuale SQL Server in Azure con pagamento in base al consumo per bring your own License usando il Vantaggio Azure Hybrid.
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
ms.openlocfilehash: 0aa2cbad75319de93c34128a09f94971e5c70216
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74790620"
---
# <a name="change-the-license-model-for-a-sql-server-virtual-machine-in-azure"></a>Modificare il modello di licenza per una macchina virtuale SQL Server in Azure
Questo articolo descrive come modificare il modello di licenza per un SQL Server macchina virtuale (VM) in Azure usando il nuovo provider di risorse VM SQL, **Microsoft. SqlVirtualMachine**.

Esistono due modelli di licenza per una macchina virtuale che ospita SQL Server: con pagamento in base al consumo e Vantaggio Azure Hybrid. È possibile modificare il modello di licenza della macchina virtuale SQL Server usando il portale di Azure, l'interfaccia della riga di comando di Azure o PowerShell. 

Il modello con pagamento in base al consumo indica che il costo al secondo dell'esecuzione della macchina virtuale di Azure include il costo della licenza di SQL Server.
[Vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-benefit/) consente di usare la propria licenza SQL Server con una macchina virtuale che esegue SQL Server. 

Vantaggio Azure Hybrid consente l'uso di licenze SQL Server con Software Assurance ("licenza qualificata") in macchine virtuali di Azure. Con Vantaggio Azure Hybrid, ai clienti non viene addebitato l'uso di una licenza di SQL Server in una macchina virtuale. Ma devono comunque pagare il costo del calcolo cloud sottostante, ovvero la tariffa di base, l'archiviazione e i backup. Devono anche pagare l'I/O associato all'uso dei servizi (come applicabile).

Secondo le condizioni del prodotto Microsoft: "i clienti devono indicare che usano il database SQL di Azure (Istanza gestita, Pool elastico e Database singolo), Azure Data Factory, SQL Server Integration Services o SQL Server macchine virtuali in Azure Vantaggio ibrido per SQL Server durante la configurazione dei carichi di lavoro in Azure. "

Per indicare l'uso di Vantaggio Azure Hybrid per SQL Server in una macchina virtuale di Azure ed essere conformi, sono disponibili tre opzioni:

- Effettuare il provisioning di una macchina virtuale usando un'immagine di SQL Server Bring your own License da Azure Marketplace. Questa opzione è disponibile solo per i clienti che dispongono di un Enterprise Agreement.
- Eseguire il provisioning di una macchina virtuale usando un'immagine di SQL Server con pagamento in base al consumo da Azure Marketplace e attivare l'Vantaggio Azure Hybrid.
- Installare autonomamente SQL Server in una macchina virtuale di Azure, [registrarsi manualmente con il provider di risorse VM SQL](virtual-machines-windows-sql-register-with-resource-provider.md)e attivare vantaggio Azure Hybrid.

Il tipo di licenza di SQL Server viene impostato quando viene effettuato il provisioning della macchina virtuale. È possibile modificarlo in qualsiasi momento in seguito. Il cambio tra modelli di licenza non comporta tempi di inattività, non riavvia la macchina virtuale o il servizio SQL Server, non aggiunge costi aggiuntivi ed è immediatamente efficace. Di fatto, l'attivazione di Vantaggio Azure Hybrid *riduce* i costi.

## <a name="prerequisites"></a>Prerequisiti

La modifica del modello di gestione licenze della macchina virtuale SQL Server presenta i requisiti seguenti: 

- Una [sottoscrizione di Azure](https://azure.microsoft.com/free/).
- Una [macchina virtuale SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) registrata con il [provider di risorse VM SQL](virtual-machines-windows-sql-register-with-resource-provider.md).
- [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) è un requisito per usare la [vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-benefit/). 


## <a name="change-the-license-for-vms-already-registered-with-the-resource-provider"></a>Modificare la licenza per le macchine virtuali già registrate con il provider di risorse 

# <a name="portaltabazure-portal"></a>[di Microsoft Azure](#tab/azure-portal)

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

È possibile modificare il modello di licenza direttamente dal portale: 

1. Aprire il [portale di Azure](https://portal.azure.com) e aprire la [risorsa macchine virtuali SQL](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) per la SQL Server VM. 
1. Selezionare **Configura** in **Impostazioni**. 
1. Selezionare l'opzione **vantaggio Azure Hybrid** e selezionare la casella di controllo per confermare di disporre di una licenza SQL Server con Software Assurance. 
1. Selezionare **applica** nella parte inferiore della pagina **Configura** . 

![Vantaggio Azure Hybrid nel portale](media/virtual-machines-windows-sql-ahb/ahb-in-portal.png)


# <a name="azure-clitabazure-cli"></a>[interfaccia della riga di comando di Azure](#tab/azure-cli)

Per modificare il modello di licenza, è possibile usare l'interfaccia della riga di comando di Azure.  

Il frammento di codice seguente passa il modello di licenza con pagamento in base al consumo a bring your own License (o usando Vantaggio Azure Hybrid):

```azurecli-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type AHUB
```

Il frammento di codice seguente passa il modello Bring-your-own-License al modello con pagamento in base al consumo: 

```azurecli-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type PAYG

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type PAYG
```

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
È possibile usare PowerShell per modificare il modello di licenza.

Il frammento di codice seguente passa il modello di licenza con pagamento in base al consumo a bring your own License (o usando Vantaggio Azure Hybrid):

```powershell-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType AHUB
```

Il frammento di codice seguente passa il modello Bring-your-own-License al modello con pagamento in base al consumo:

```powershell-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType PAYG
```

---

## <a name="change-the-license-for-vms-not-registered-with-the-resource-provider"></a>Modificare la licenza per le macchine virtuali non registrate con il provider di risorse

Se è stato effettuato il provisioning di una macchina virtuale SQL Server dalle immagini di Azure Marketplace con pagamento in base al consumo, il tipo di licenza SQL Server sarà con pagamento in base al consumo. Se è stato effettuato il provisioning di una macchina virtuale SQL Server usando un'immagine Bring your own License da Azure Marketplace, il tipo di licenza sarà vantaggio Ahu. Tutte le macchine virtuali SQL Server sottoposte a provisioning dalle immagini predefinite (con pagamento in base al consumo) o bring your own License di Azure Marketplace verranno automaticamente registrate con il provider di risorse VM SQL, in modo che possano modificare il [tipo di licenza](#change-the-license-for-vms-already-registered-with-the-resource-provider).

L'utente è idoneo per l'installazione automatica di SQL Server in una macchina virtuale di Azure tramite Vantaggio Azure Hybrid. È necessario [registrare queste VM con il provider di risorse VM SQL](virtual-machines-windows-sql-register-with-resource-provider.md) impostando la licenza di SQL Server come vantaggio Azure Hybrid per indicare l'utilizzo vantaggio Azure Hybrid in base alle condizioni del prodotto Microsoft.

È possibile modificare il tipo di licenza di una macchina virtuale SQL Server come con pagamento in base al consumo o Vantaggio Azure Hybrid solo se la VM SQL Server è registrata con il provider di risorse della macchina virtuale SQL.

## <a name="remarks"></a>Osservazioni

- I clienti di Azure Cloud Solution Provider (CSP) possono usare il Vantaggio Azure Hybrid distribuendo prima di tutto una macchina virtuale con pagamento in base al consumo e quindi convertirla in Bring your own License se hanno Software Assurance attivo.
- Se si elimina la risorsa SQL Server VM, si tornerà all'impostazione di licenza hardcoded dell'immagine. 
- La possibilità di modificare il modello di licenza è una funzionalità del provider di risorse della macchina virtuale SQL. La distribuzione di un'immagine di Azure Marketplace tramite il portale di Azure registra automaticamente una macchina virtuale SQL Server con il provider di risorse. Tuttavia, i clienti che eseguono l'installazione automatica di SQL Server dovranno [registrare manualmente la macchina virtuale SQL Server](virtual-machines-windows-sql-register-with-resource-provider.md). 
- Per aggiungere una macchina virtuale SQL Server a un set di disponibilità è necessario ricreare la macchina virtuale. Di conseguenza, tutte le macchine virtuali aggiunte a un set di disponibilità tornano al tipo di licenza con pagamento in base al consumo predefinito. Vantaggio Azure Hybrid sarà necessario abilitarla di nuovo. 


## <a name="limitations"></a>Limitazioni

Modifica del modello di licenza:
   - Disponibile solo per i clienti con [Software Assurance](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-overview).
   - Supportato solo per le edizioni standard ed Enterprise di SQL Server. Le modifiche alle licenze per Express, Web e Developer non sono supportate. 
   - Supportato solo per le macchine virtuali distribuite tramite il modello di Azure Resource Manager. Le macchine virtuali distribuite con il modello classico non sono supportate. 
   - Disponibile solo per le installazioni di cloud pubblico. 
   - Supportato solo in macchine virtuali con una singola interfaccia di rete (NIC). 


## <a name="known-errors"></a>Errori noti

### <a name="the-resource-microsoftsqlvirtualmachinesqlvirtualmachinesresource-group-under-resource-group-resource-group-was-not-found"></a>La risorsa ' Microsoft. SqlVirtualMachine/SqlVirtualMachines/\<Resource-Group >' nel gruppo di risorse '\<Resource-Group >' non è stata trovata.

Questo errore si verifica quando si tenta di modificare il modello di licenza in una macchina virtuale SQL Server che non è stata registrata con il provider di risorse VM SQL:

`The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<resource-group>' under resource group '\<resource-group>' was not found. The property 'sqlServerLicenseType' cannot be found on this object. Verify that the property exists and can be set.`

È necessario registrare la sottoscrizione con il provider di risorse e quindi [registrare la macchina virtuale SQL Server con il provider di risorse](virtual-machines-windows-sql-register-with-resource-provider.md). 


## <a name="the-virtual-machine-vmname-has-more-than-one-nic-associated"></a>Alla macchina virtuale '\<VMName\>' è associata più di una scheda di interfaccia di rete

Questo errore si verifica nelle macchine virtuali con più di una scheda di interfaccia di rete. Rimuovere una delle schede di rete prima di modificare il modello di licenza. Sebbene sia possibile aggiungere di nuovo la scheda di interfaccia di rete alla macchina virtuale dopo aver modificato il modello di licenza, le operazioni nel portale di Azure come il backup automatico e l'applicazione di patch non saranno più supportate. 


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere gli articoli seguenti: 

* [Panoramica di SQL Server in una macchina virtuale Windows](virtual-machines-windows-sql-server-iaas-overview.md)
* [Domande frequenti per SQL Server in una macchina virtuale Windows](virtual-machines-windows-sql-server-iaas-faq.md)
* [Linee guida sui prezzi per SQL Server in una macchina virtuale Windows](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Note sulla versione per SQL Server in una macchina virtuale Windows](virtual-machines-windows-sql-server-iaas-release-notes.md)


