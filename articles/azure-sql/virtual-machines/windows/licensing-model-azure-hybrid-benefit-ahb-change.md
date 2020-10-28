---
title: Cambiare il modello di licenza per una macchina virtuale SQL in Azure
description: Informazioni su come cambiare la licenza per una macchina virtuale di SQL Server in Azure da una licenza con pagamento in base al consumo a una licenza BYOL (Bring Your Own License) tramite Vantaggio Azure Hybrid.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 28ab0a158507e3f29ecfdc026203d92d71877633
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/28/2020
ms.locfileid: "92786514"
---
# <a name="change-the-license-model-for-a-sql-virtual-machine-in-azure"></a>Cambiare il modello di licenza per una macchina virtuale SQL in Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]


Questo articolo descrive come cambiare il modello di licenza di una macchina virtuale di SQL Server in Azure usando il nuovo provider di risorse VM di SQL Server, **Microsoft.SqlVirtualMachine** .

Sono disponibili tre modelli di licenza per una VM che ospita SQL Server: con pagamento in base al consumo, Vantaggio Azure Hybrid e ripristino di emergenza. È possibile modificare il modello di licenza delle VM di SQL Server tramite il portale di Azure, l'interfaccia della riga di comando di Azure o PowerShell. 

- Il modello con **pagamento in base al consumo** implica che il costo al secondo associato all'esecuzione della VM di Azure include il costo della licenza di SQL Server.
- Il modello [Vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-benefit/) consente di usare la propria licenza di SQL Server con una VM che esegue SQL Server. 
- Il tipo di licenza **ripristino di emergenza** viene usato per la [replica DR gratuita](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure) in Azure. 

Vantaggio Azure Hybrid consente l'uso di licenze di SQL Server con Software Assurance ("licenza qualificata") in macchine virtuali di Azure. Con Vantaggio Azure Hybrid, i clienti non ricevono addebiti per l'uso di una licenza di SQL Server in una macchina virtuale. Ma devono comunque assumersi il costo della risorsa di calcolo cloud sottostante (ovvero la tariffa di base), dell'archiviazione e dei backup. Devono inoltre pagare per le operazioni di I/O associate all'uso dei servizi (come applicabile).

Secondo le condizioni per l'utilizzo di prodotti Microsoft: "Quando configurano i carichi di lavoro in Azure, i clienti sono tenuti a indicare che usano Database SQL di Azure (Istanza gestita, pool elastico e database singolo), Azure Data Factory, SQL Server Integration Services o macchine virtuali di SQL Server in base alle condizioni di Vantaggio Azure Hybrid per SQL Server".

Per indicare l'uso di Vantaggio Azure Hybrid per SQL Server in una macchina virtuale di Azure e rispettare la conformità, sono disponibili tre opzioni:

- Effettuare il provisioning di una macchina virtuale usando un'immagine di SQL Server con Bring Your Own License da Azure Marketplace. Questa opzione è disponibile solo per i clienti con contratto Enterprise.
- Effettuare il provisioning di una macchina virtuale usando un'immagine di SQL Server con pagamento in base al consumo da Azure Marketplace e attivare Vantaggio Azure Hybrid.
- Installare autonomamente SQL Server in una macchina virtuale di Azure, [eseguire manualmente la registrazione con il provider di risorse VM SQL](sql-vm-resource-provider-register.md) e attivare Vantaggio Azure Hybrid.

Il tipo di licenza di SQL Server può essere configurato quando viene effettuato il provisioning della macchina virtuale o in un secondo momento. Il passaggio tra modelli di licenza non genera tempi di inattività, non provoca il riavvio della macchina virtuale o del servizio SQL Server, non comporta costi aggiuntivi e ha validità immediata. Di fatto, l'attivazione di Vantaggio Azure Hybrid offre una *riduzione dei costi* .

## <a name="prerequisites"></a>Prerequisiti

Per cambiare il modello di licenza delle VM di SQL Server, sono previsti i requisiti seguenti: 

- Una [sottoscrizione di Azure](https://azure.microsoft.com/free/).
- Una [VM di SQL Server](./create-sql-vm-portal.md) registrata con il [provider di risorse VM di SQL](sql-vm-resource-provider-register.md).
- [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) è un requisito per utilizzare [Vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-benefit/). 


## <a name="vms-already-registered-with-the-resource-provider"></a>VM già registrate con il provider di risorse 

# <a name="the-azure-portal"></a>[Il portale di Azure](#tab/azure-portal)

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

È possibile modificare il modello di licenza direttamente dal portale: 

1. Aprire il [portale di Azure](https://portal.azure.com) e accedere alle risorse [macchine virtuali di SQL](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource) per la propria VM di SQL Server. 
1. Selezionare **Configura** in **Impostazioni** . 
1. Selezionare l'opzione **Vantaggio Azure Hybrid** , quindi selezionare la casella di controllo per confermare di avere una licenza di SQL Server con Software Assurance. 
1. Selezionare **Applica** nella parte inferiore della pagina **Configura** . 

![Vantaggio Azure Hybrid nel portale](./media/licensing-model-azure-hybrid-benefit-ahb-change/ahb-in-portal.png)


# <a name="the-azure-cli"></a>[L’interfaccia della riga di comando di Azure](#tab/azure-cli)

È possibile usare l'interfaccia della riga di comando di Azure per cambiare il modello di licenza.  


**Vantaggio Azure Hybrid**

```azurecli-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type AHUB
```

**Pagamento in base al consumo** : 

```azurecli-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type PAYG

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type PAYG
```

**Ripristino di emergenza**

```azurecli-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type DR

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type DR
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

È possibile usare PowerShell per cambiare il modello di licenza.

**Vantaggio Azure Hybrid**

```powershell-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType AHUB
```

**Pagamento in base al consumo**

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

## <a name="vms-not-registered-with-the-resource-provider"></a>VM non registrate con il provider di risorse

Se il provisioning di una VM di SQL Server è stato effettuato dalle immagini di Azure Marketplace con pagamento in base al consumo, il tipo di licenza di SQL Server sarà con pagamento in base al consumo. Se il provisioning di una VM di SQL Server è stato effettuato usando un'immagine Bring Your Own License da Azure Marketplace, il tipo di licenza sarà AHUB. Tutte le VM di SQL Server di cui viene effettuato il provisioning dalle immagini predefinite (con pagamento in base al consumo) o Bring Your Own License di Azure Marketplace verranno automaticamente registrate con il provider di risorse VM di SQL in modo che sia possibile cambiare il [tipo di licenza](#vms-already-registered-with-the-resource-provider).

È consentito scegliere l'installazione automatica di SQL Server in una macchina virtuale di Azure solo tramite Vantaggio Azure Hybrid. È necessario [registrare queste VM con il provider di risorse VM di SQL](sql-vm-resource-provider-register.md) impostando la licenza di SQL Server come Vantaggio Azure Hybrid per indicare l'utilizzo di Vantaggio Azure Hybrid in base alle condizioni per l'utilizzo di prodotti Microsoft.

È possibile cambiare il tipo di licenza di una VM di SQL Server con pagamento in base al consumo o come Vantaggio Azure Hybrid solo se la VM di SQL Server è registrata con il provider di risorse VM di SQL.

## <a name="remarks"></a>Osservazioni

- I clienti CSP (Azure Cloud Solution Provider) possono usare Vantaggio Azure Hybrid distribuendo prima di tutto una VM con pagamento in base al consumo e quindi convertendo la licenza in Bring Your Own License, se hanno un contratto Software Assurance attivo.
- Se si elimina la risorsa VM di SQL Server, sarà necessario ripristinare l'impostazione della licenza hardcoded dell'immagine. 
- La possibilità di cambiare il modello di licenza è una funzionalità del provider di risorse VM di SQL. La distribuzione di un'immagine di Azure Marketplace tramite il portale di Azure registra automaticamente una macchina virtuale di SQL Server con il provider di risorse. Tuttavia, i clienti che eseguono l'installazione automatica di SQL Server dovranno [registrare manualmente la loro VM di SQL Server](sql-vm-resource-provider-register.md). 
- Per aggiungere una VM di SQL Server a un set di disponibilità, è necessario ricrearla. Di conseguenza, per tutte le VM aggiunte a un set di disponibilità verrà ripristinato il modello di licenza con pagamento in base al consumo. Sarà necessario abilitare di nuovo Vantaggio Azure Hybrid. 


## <a name="limitations"></a>Limitazioni

La modifica del modello di licenza è:
   - Disponibile solo per i clienti con [Software Assurance](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-overview).
   - Supportata solo per le edizioni Standard ed Enterprise di SQL Server. Non è possibile cambiare licenza per le edizioni Express, Web e Developer. 
   - Supportata solo per le macchine virtuali distribuite tramite il modello di Azure Resource Manager. Le macchine virtuali distribuite tramite il modello classico non sono supportate. 
   - Disponibile solo per il cloud pubblico o per Cloud Azure per enti pubblici. 
   - Supportata solo nelle macchine virtuali con una singola scheda di interfaccia di rete. 


## <a name="known-errors"></a>Errori noti

Esaminare gli errori comunemente noti e le relative soluzioni. 

**La risorsa ' Microsoft. SqlVirtualMachine/SqlVirtualMachines/ \<resource-group> ' nel gruppo di risorse ' \<resource-group> ' non è stata trovata.**

Questo errore si verifica quando si prova a cambiare il modello di licenza in una VM di SQL Server che non è stata registrata con un provider di risorse VM di SQL:

`The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<resource-group>' under resource group '\<resource-group>' was not found. The property 'sqlServerLicenseType' cannot be found on this object. Verify that the property exists and can be set.`

Sarà necessario registrare la sottoscrizione e quindi [la VM di SQL Server con il provider di risorse](sql-vm-resource-provider-register.md). 


**Alla macchina virtuale ' \<vmname\> ' è associata più di una scheda di interfaccia di rete**

Questo errore si verifica nelle macchine virtuali che includono più di una scheda di interfaccia di rete. Rimuovere una delle schede prima di cambiare il modello di licenza. Anche se è possibile aggiungere di nuovo la scheda di interfaccia di rete alla macchina virtuale dopo aver cambiato il modello di licenza, le operazioni nel portale di Azure, come il backup automatico e l'applicazione di patch, non saranno più supportate. 


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere gli articoli seguenti: 

* [Panoramica di SQL Server in una macchina virtuale Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Domande frequenti su SQL Server in una VM di Windows](frequently-asked-questions-faq.md)
* [Informazioni sui prezzi di SQL Server in una VM di Windows](pricing-guidance.md)
* [Note sulla versione di SQL Server in una VM di Windows](../../database/doc-changes-updates-release-notes.md)