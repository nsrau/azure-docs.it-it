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
ms.openlocfilehash: 37f6e60aea033dee8adfd66839c82b9fd165c879
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94556273"
---
# <a name="change-the-license-model-for-a-sql-virtual-machine-in-azure"></a>Cambiare il modello di licenza per una macchina virtuale SQL in Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]


Questo articolo descrive come modificare il modello di licenza per un SQL Server macchina virtuale (VM) in Azure usando l' [estensione SQL IaaS Agent](./sql-server-iaas-agent-extension-automate-management.md).

## <a name="overview"></a>Panoramica

Sono disponibili tre modelli di licenza per una macchina virtuale di Azure che ospita SQL Server: con pagamento in base al consumo, Vantaggio Azure Hybrid (vantaggio Azure Hybrid) e disponibilità elevata/ripristino di emergenza (HA/DR). È possibile modificare il modello di licenza delle VM di SQL Server tramite il portale di Azure, l'interfaccia della riga di comando di Azure o PowerShell. 

- Il modello con **pagamento in base al consumo** implica che il costo al secondo associato all'esecuzione della VM di Azure include il costo della licenza di SQL Server.
- Il modello [Vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-benefit/) consente di usare la propria licenza di SQL Server con una VM che esegue SQL Server. 
- Il tipo di licenza a **disponibilità elevata/ripristino** di emergenza viene usato per la [replica a disponibilità elevata/ripristino](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure) di emergenza gratuita in Azure. 

Vantaggio Azure Hybrid consente l'uso di licenze di SQL Server con Software Assurance ("licenza qualificata") in macchine virtuali di Azure. Con Vantaggio Azure Hybrid, i clienti non ricevono addebiti per l'uso di una licenza di SQL Server in una macchina virtuale. Ma devono comunque assumersi il costo della risorsa di calcolo cloud sottostante (ovvero la tariffa di base), dell'archiviazione e dei backup. Devono inoltre pagare per le operazioni di I/O associate all'uso dei servizi (come applicabile).

Secondo le condizioni per l'utilizzo di prodotti Microsoft: "Quando configurano i carichi di lavoro in Azure, i clienti sono tenuti a indicare che usano Database SQL di Azure (Istanza gestita, pool elastico e database singolo), Azure Data Factory, SQL Server Integration Services o macchine virtuali di SQL Server in base alle condizioni di Vantaggio Azure Hybrid per SQL Server".

Per indicare l'uso di Vantaggio Azure Hybrid per SQL Server in una macchina virtuale di Azure e rispettare la conformità, sono disponibili tre opzioni:

- Effettuare il provisioning di una macchina virtuale usando un'immagine di SQL Server con Bring Your Own License da Azure Marketplace. Questa opzione è disponibile solo per i clienti con contratto Enterprise.
- Effettuare il provisioning di una macchina virtuale usando un'immagine di SQL Server con pagamento in base al consumo da Azure Marketplace e attivare Vantaggio Azure Hybrid.
- Installare autonomamente SQL Server nella macchina virtuale di Azure, [registrarsi manualmente con l'estensione SQL IaaS Agent](sql-agent-extension-manually-register-single-vm.md)e attivare vantaggio Azure Hybrid.

Il tipo di licenza di SQL Server può essere configurato quando viene effettuato il provisioning della macchina virtuale o in un secondo momento. Il passaggio tra modelli di licenza non genera tempi di inattività, non provoca il riavvio della macchina virtuale o del servizio SQL Server, non comporta costi aggiuntivi e ha validità immediata. Di fatto, l'attivazione di Vantaggio Azure Hybrid offre una *riduzione dei costi*.

## <a name="prerequisites"></a>Prerequisiti

Per cambiare il modello di licenza delle VM di SQL Server, sono previsti i requisiti seguenti: 

- Una [sottoscrizione di Azure](https://azure.microsoft.com/free/).
- Una [macchina virtuale SQL Server](./create-sql-vm-portal.md) registrata con l' [estensione SQL IaaS Agent](./sql-server-iaas-agent-extension-automate-management.md).
- [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) è un requisito per utilizzare [Vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-benefit/). 


## <a name="change-license-model"></a>Cambiare modello di licenza

# <a name="azure-portal"></a>[Azure portal](#tab/azure-portal)

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

È possibile modificare il modello di licenza direttamente dal portale: 

1. Aprire il [portale di Azure](https://portal.azure.com) e accedere alle risorse [macchine virtuali di SQL](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource) per la propria VM di SQL Server. 
1. Selezionare **Configura** in **Impostazioni**. 
1. Selezionare l'opzione **Vantaggio Azure Hybrid** , quindi selezionare la casella di controllo per confermare di avere una licenza di SQL Server con Software Assurance. 
1. Selezionare **Applica** nella parte inferiore della pagina **Configura**. 

![Vantaggio Azure Hybrid nel portale](./media/licensing-model-azure-hybrid-benefit-ahb-change/ahb-in-portal.png)


# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

È possibile usare l'interfaccia della riga di comando di Azure per cambiare il modello di licenza.  

Specificare i valori seguenti per il **tipo di licenza** :
- `AHUB` per la Vantaggio Azure Hybrid
- `PAYG` per il pagamento in base al consumo
- `DR` per attivare la replica a disponibilità elevata/ripristino di emergenza


```azurecli-interactive
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type <license-type>
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

È possibile usare PowerShell per cambiare il modello di licenza.

Specificare i valori seguenti per il **tipo di licenza** :
- `AHUB` per la Vantaggio Azure Hybrid
- `PAYG` per il pagamento in base al consumo
- `DR` per attivare la replica a disponibilità elevata/ripristino di emergenza


```powershell-interactive
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType <license-type>
```

---

## <a name="remarks"></a>Osservazioni

- I clienti CSP (Azure Cloud Solution Provider) possono usare Vantaggio Azure Hybrid distribuendo prima di tutto una VM con pagamento in base al consumo e quindi convertendo la licenza in Bring Your Own License, se hanno un contratto Software Assurance attivo.
- Se si elimina la risorsa della macchina virtuale SQL, si tornerà all'impostazione di licenza hardcoded dell'immagine. 
- La possibilità di modificare il modello di licenza è una funzionalità dell'estensione SQL IaaS Agent. La distribuzione di un'immagine di Azure Marketplace tramite il portale di Azure registra automaticamente una macchina virtuale di SQL Server con l'estensione. Tuttavia, i clienti che eseguono l'installazione automatica di SQL Server dovranno [registrare manualmente la loro VM di SQL Server](sql-agent-extension-manually-register-single-vm.md). 
- Per aggiungere una VM di SQL Server a un set di disponibilità, è necessario ricrearla. Di conseguenza, per tutte le VM aggiunte a un set di disponibilità verrà ripristinato il modello di licenza con pagamento in base al consumo. Sarà necessario abilitare di nuovo Vantaggio Azure Hybrid. 


## <a name="limitations"></a>Limitazioni

La modifica del modello di licenza è:
   - Disponibile solo per i clienti con [Software Assurance](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-overview).
   - Supportata solo per le edizioni Standard ed Enterprise di SQL Server. Non è possibile cambiare licenza per le edizioni Express, Web e Developer. 
   - Supportata solo per le macchine virtuali distribuite tramite il modello di Azure Resource Manager. Le macchine virtuali distribuite tramite il modello classico non sono supportate. 
   - Disponibile solo per il cloud pubblico o per Cloud Azure per enti pubblici. 
   - Supportata solo nelle macchine virtuali con una singola scheda di interfaccia di rete. 

> [!Note]
> Solo SQL Server le licenze basate su core con Software Assurance o licenze di sottoscrizione sono idonee per Vantaggio Azure Hybrid. Se si usano server e licenze CAL per SQL Server e si dispone di Software Assurance, è possibile usare la funzionalità Bring your own License per un'immagine di macchina virtuale di Azure SQL Server per sfruttare la mobilità delle licenze per questi server, ma non è possibile sfruttare le altre funzionalità di Vantaggio Azure Hybrid. 

## <a name="known-errors"></a>Errori noti

Esaminare gli errori comunemente noti e le relative soluzioni. 

**La risorsa ' Microsoft. SqlVirtualMachine/SqlVirtualMachines/ \<resource-group> ' nel gruppo di risorse ' \<resource-group> ' non è stata trovata.**

Questo errore si verifica quando si tenta di modificare il modello di licenza in una macchina virtuale SQL Server che non è stata registrata con l'estensione dell'agente IaaS SQL Server:

`The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<resource-group>' under resource group '\<resource-group>' was not found. The property 'sqlServerLicenseType' cannot be found on this object. Verify that the property exists and can be set.`

È necessario registrare la sottoscrizione con il provider di risorse e quindi [registrare la macchina virtuale SQL Server con l'estensione SQL IaaS Agent](sql-agent-extension-manually-register-single-vm.md). 


**Alla macchina virtuale ' \<vmname\> ' è associata più di una scheda di interfaccia di rete**

Questo errore si verifica nelle macchine virtuali che includono più di una scheda di interfaccia di rete. Rimuovere una delle schede prima di cambiare il modello di licenza. Anche se è possibile aggiungere di nuovo la scheda di interfaccia di rete alla macchina virtuale dopo aver cambiato il modello di licenza, le operazioni nel portale di Azure, come il backup automatico e l'applicazione di patch, non saranno più supportate. 


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni, vedere gli articoli seguenti: 

* [Panoramica di SQL Server in una macchina virtuale Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Domande frequenti su SQL Server in una VM di Windows](frequently-asked-questions-faq.md)
* [Informazioni sui prezzi di SQL Server in una VM di Windows](pricing-guidance.md)
* [Note sulla versione di SQL Server in una VM di Windows](../../database/doc-changes-updates-release-notes.md)
* [Panoramica dell'estensione SQL IaaS Agent](./sql-server-iaas-agent-extension-automate-management.md)
