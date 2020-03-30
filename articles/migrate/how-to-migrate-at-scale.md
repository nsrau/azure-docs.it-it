---
title: Automatizzare la migrazione della macchina di migrazione in Azure MigrateAutomate migration machine migration in Azure Migrate
description: Descrive come usare gli script per eseguire la migrazione di un numero elevato di computer in Azure MigrateDescribes how to use scripts to migrate a large number of machines in Azure Migrate
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: article
ms.date: 04/01/2019
ms.author: snehaa
ms.openlocfilehash: 317b6e8aa799b7982e9897c6a504d6092491c7ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74196353"
---
# <a name="scale-migration-of-vms"></a>Migrazione di scalabilità delle macchine virtualiScale migration of VMs 

Questo articolo illustra come usare gli script per eseguire la migrazione di un numero elevato di macchine virtuali (VM). Per ridimensionare la migrazione, usare [Azure Site Recovery](../site-recovery/site-recovery-overview.md). 

Gli script di Site Recovery sono disponibili per il download in Azure PowerShell Samples repo in GitHub.Site Recovery scripts are available for your download at [Azure PowerShell Samples](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-with-site-recovery) repo on GitHub. Gli script possono essere usati per eseguire la migrazione di VMware, AWS, macchine virtuali GCP e server fisici ai dischi gestiti in Azure. È anche possibile usare questi script per eseguire la migrazione di macchine virtuali Hyper-V se si esegue la migrazione delle macchine virtuali come server fisici. Gli script che sfruttano Azure Site Recovery PowerShell sono documentati [qui](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell).

## <a name="current-limitations"></a>Limitazioni correnti
- È possibile specificare l'indirizzo IP statico solo per la scheda di interfaccia di rete primaria della macchina virtuale di destinazione
- Gli script non accettano gli input correlati al Vantaggio Azure Hybrid ed è necessario aggiornare manualmente le proprietà della macchina virtuale replicata nel portale

## <a name="how-does-it-work"></a>Come funziona?

### <a name="prerequisites"></a>Prerequisiti
Prima di iniziare, è necessario seguire questi passaggi:
- Assicurarsi di creare l'Insieme di credenziali di Site Recovery nella sottoscrizione di Azure
- Assicurarsi che il server di configurazione e il server di elaborazione siano installati nell'ambiente di origine e che l'insieme di credenziali sia in grado di individuare l'ambiente
- Assicurarsi di creare criteri di replica e associarli al server di configurazione
- Verificare di aver aggiunto l'account di amministratore della macchina virtuale al server di configurazione (che verrà usato per replicare le macchine virtuali locali)Ensure that you have added the VM admin account to the config server (that will be used to replicate the ons VMs)
- Verificare che gli elementi di destinazione in Azure vengano creatiEnsure that the target artifacts in Azure are created
    - Gruppo di risorse di destinazione
    - Account di archiviazione di destinazione (e relativo gruppo di risorse): creare un account di archiviazione Premium se si prevede di eseguire la migrazione a dischi gestiti da PremiumTarget Storage Account (and its Resource Group) - Create a premium storage account if you plan to migrate to premium-managed disks
    - Cache Storage Account (e relativo gruppo di risorse) - Creare un account di archiviazione standard nella stessa area dell'insieme di credenziali
    - Rete virtuale di destinazione per il failover (e relativo gruppo di risorse)
    - Subnet di destinazione
    - Rete virtuale di destinazione per il failover di test (e relativo gruppo di risorse)
    - Set di disponibilità (se necessario)
    - Gruppo di sicurezza di rete di destinazione e relativo gruppo di risorse
- Assicurarsi di aver definito le proprietà della macchina virtuale di destinazione
    - Nome della macchina virtuale di destinazione
    - Dimensioni della macchina virtuale di destinazione in Azure (che possono essere stabilite tramite una valutazione di Azure Migrate)
    - Indirizzo IP privato della scheda di interfaccia di rete primaria nella macchina virtuale
- Scaricare gli script dal repository di [esempi di Azure PowerShell](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-with-site-recovery) in GitHub

### <a name="csv-input-file"></a>File di input CSV
Una volta completati tutti i prerequisiti, è necessario creare un file CSV, che contiene i dati per ogni computer di origine di cui si desidera eseguire la migrazione. Il file CSV di input deve avere una riga di intestazione con i dettagli di input e una riga con i dettagli per ogni computer di cui si deve eseguire la migrazione. Tutti gli script sono progettati per funzionare nello stesso file CSV. Un modello CSV di esempio è disponibile nella cartella degli script come riferimento.

### <a name="script-execution"></a>Esecuzione degli script
Una volta pronto il file CSV, è possibile seguire la procedura illustrata di seguito per eseguire la migrazione delle macchine virtuali locali:

**N.ro passaggio** | **Nome script** | **Descrizione**
--- | --- | ---
1 | asr_startmigration.ps1 | Abilitare la replica per tutte le macchine virtuali elencate nel file CSV. Lo script crea un output CSV con i dettagli del processo per ogni macchina virtuale.
2 | asr_replicationstatus.ps1 | Controllare lo stato della replica. Lo script crea un file CSV con lo stato per ogni macchina virtuale.
3 | asr_updateproperties.ps1 | Una volta replicate/protette le macchine virtuali, usare questo script per aggiornare le proprietà di destinazione della macchina virtuale (proprietà di calcolo e rete).
4 | asr_propertiescheck.ps1 | Verificare se le proprietà vengono aggiornate in modo appropriato.
5 | asr_testmigration.ps1 |  Avviare il failover di test delle macchine virtuali elencate nel file CSV. Lo script crea un output CSV con i dettagli del processo per ogni macchina virtuale.
6 | asr_cleanuptestmigration.ps1 | Una volta convalidate manualmente le macchine virtuali che sono state sottoposte al failover di test, è possibile usare questo script per eseguire la pulizia delle macchine virtuali usate per il failover di test.
7 | asr_migration.ps1 | Eseguire un failover non pianificato per le macchine virtuali elencate nel file CSV. Lo script crea un output CSV con i dettagli del processo per ogni macchina virtuale. Lo script non arresta le macchine virtuali locali prima di attivare il failover, per coerenza dell'applicazione, è consigliabile arrestare manualmente le macchine virtuali prima di eseguire lo script.
8 | asr_completemigration.ps1 | Eseguire l'operazione di commit nelle macchine virtuali ed eliminare le entità di Azure Site Recovery
9 | asr_postmigration.ps1 | Se si prevede di assegnare gruppi di sicurezza di rete alle schede di interfaccia di rete dopo il failover, è possibile usare questo script a tale scopo. Assegna un gruppo di sicurezza di rete a qualsiasi scheda di interfaccia di rete nella macchina virtuale di destinazione.

## <a name="how-to-migrate-to-managed-disks"></a>Come eseguire la migrazione ai dischi gestiti?
The script, by default, migrates the VMs to managed disks in Azure. Se l'account di archiviazione di destinazione fornito è un account di archiviazione Premium, i dischi gestiti premium vengono creati dopo la migrazione. L'account di archiviazione della cache può essere ancora un account standard. Se l'account di archiviazione di destinazione è un account di archiviazione standard, i dischi standard vengono creati dopo la migrazione. 

## <a name="next-steps"></a>Passaggi successivi

[Leggere altre informazioni](https://docs.microsoft.com/azure/site-recovery/migrate-tutorial-on-premises-azure) sulla migrazione di server in Azure con Azure Site Recovery
