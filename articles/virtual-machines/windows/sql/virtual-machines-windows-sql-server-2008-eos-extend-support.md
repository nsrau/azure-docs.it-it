---
title: Estendi il supporto per SQL Server 2008 e SQL Server 2008 R2 con Azure
description: Informazioni su come estendere il supporto per SQL Server 2008 e SQL Server 2008 R2 eseguendo la migrazione dell'istanza di SQL Server in Azure o acquistando supporto esteso per mantenere le istanze locali.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/08/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 93e0032cd283eda034519ca29a0e1cf501b5cde6
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100462"
---
# <a name="extend-support-for-sql-server-2008-and-sql-server-2008-r2-with-azure"></a>Estendi il supporto per SQL Server 2008 e SQL Server 2008 R2 con Azure

SQL Server 2008 e SQL Server 2008 R2 si avvicinano alla [fine del ciclo di vita del supporto (EOS)](https://www.microsoft.com/sql-server/sql-server-2008). Poiché molti clienti usano ancora entrambe le versioni, sono disponibili diverse opzioni per continuare a ottenere supporto. È possibile eseguire la migrazione delle istanze di SQL Server locali alle macchine virtuali di Azure, eseguire la migrazione al database SQL di Azure o rimanere in locale e acquistare aggiornamenti della sicurezza estesi.

A differenza di un'istanza gestita, per eseguire la migrazione a una macchina virtuale di Azure non è necessario ricertificare le applicazioni. A differenza di quanto avviene in locale, si riceveranno patch di sicurezza estese gratuite eseguendo la migrazione a una macchina virtuale di Azure.

Nella parte restante di questo articolo vengono fornite alcune considerazioni per la migrazione dell'istanza di SQL Server a una macchina virtuale di Azure.

## <a name="provisioning"></a>Provisioning

È disponibile un'immagine con pagamento in base al consumo **SQL Server 2008 R2 in Windows Server 2008 R2** disponibile in Azure Marketplace.

I clienti che si trovano in SQL Server 2008 dovranno eseguire l'installazione automatica o eseguire l'aggiornamento a SQL Server 2008 R2. Analogamente, i clienti di Windows Server 2008 dovranno distribuire la propria macchina virtuale da un disco rigido virtuale personalizzato o eseguire l'aggiornamento a Windows Server 2008 R2.

Le immagini distribuite tramite Azure Marketplace sono disponibili con l'estensione SQL IaaS preinstallata. L'estensione SQL IaaS è un requisito per le licenze flessibili e l'applicazione automatica di patch. I clienti che distribuiscono macchine virtuali autoinstallate dovranno installare manualmente l'estensione SQL IaaS. L'estensione SQL IaaS non è supportata in Windows Server 2008.

> [!NOTE]
> Sebbene il SQL Server **creare** e **gestire** pannelli funzioni con l'immagine SQL Server 2008 R2 nel portale di Azure, le funzionalità seguenti _non sono supportate_: Backup automatici, integrazione Azure Key Vault, R Services e configurazione dell'archiviazione.

## <a name="licensing"></a>Licenze
Le distribuzioni con pagamento in base al consumo SQL Server 2008 R2 possono essere convertite in [vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-benefit/).

Per convertire una licenza basata su Software Assurance (SA) con pagamento in base al consumo, i clienti devono registrarsi con il provider di [risorse](virtual-machines-windows-sql-register-with-resource-provider.md)della macchina virtuale SQL. Al termine della registrazione, il tipo di licenza SQL sarà interscambiabile tra Vantaggio Azure Hybrid e con pagamento in base al consumo.

Le istanze di SQL Server 2008 o SQL Server 2008 R2 autoinstallate in una macchina virtuale di Azure possono eseguire la registrazione con il provider di risorse VM SQL e convertire il tipo di licenza in con pagamento in base al consumo.

## <a name="migration"></a>Migrazione
È possibile eseguire la migrazione di istanze di SQL Server EOS a una macchina virtuale di Azure con metodi di backup/ripristino manuali. Questo è il metodo di migrazione più comune da locale a una macchina virtuale di Azure.

### <a name="azure-site-recovery"></a>Azure Site Recovery

Per le migrazioni bulk, si consiglia il servizio [Azure Site Recovery](/azure/site-recovery/site-recovery-overview) . Con Azure Site Recovery, i clienti possono replicare l'intera VM, incluso SQL Server da locale a macchina virtuale di Azure.

SQL Server richiede snapshot Azure Site Recovery coerenti con l'app per garantire il ripristino. Azure Site Recovery supporta snapshot coerenti con l'app con un intervallo minimo di 1 ora. L'obiettivo del punto di ripristino minimo (RPO) possibile per SQL Server con Azure Site Recovery migrazioni è 1 ora. L'obiettivo del tempo di ripristino (RTO) è di 2 ore più SQL Server tempo di ripristino.

### <a name="database-migration-service"></a>Servizio Migrazione del database

Il [servizio migrazione del database](/azure/dms/dms-overview) è un'opzione per i clienti che eseguono la migrazione da locale a una macchina virtuale di Azure aggiornando SQL Server alla versione 2012 o successiva.

## <a name="disaster-recovery"></a>Ripristino di emergenza

Le soluzioni di ripristino di emergenza per EOS SQL Server in una macchina virtuale di Azure sono le seguenti:

- **SQL Server Backup**: Usa backup di Azure per proteggere la SQL Server EOS da ransomware, eliminazione accidentale e danneggiamento. La soluzione è attualmente disponibile in anteprima per SQL Server EOS e supporta SQL Server 2008 e 2008 R2 in esecuzione su Windows 2008 R2 SP1. Per altri dettagli, vedere [questo articolo](https://docs.microsoft.com/azure/backup/backup-azure-sql-database#support-for-sql-server-2008-and-sql-server-2008-r2).
- **Log shipping**: È possibile creare una replica di log shipping in un'altra area o in un'area di Azure con ripristini continui per ridurre i RTO. È necessario configurare manualmente log shipping.
- **Azure Site Recovery**: È possibile replicare la macchina virtuale tra aree e aree tramite la replica Azure Site Recovery. SQL Server richiede snapshot coerenti con l'app per garantire il ripristino in caso di emergenza. Azure Site Recovery offre un numero minimo di RPO di 1 ora e un RTO di 2 ore (più SQL Server tempo di ripristino) per EOS SQL Server il ripristino di emergenza.

## <a name="security-patching"></a>Patch di sicurezza
Gli aggiornamenti della sicurezza estesi per le macchine virtuali SQL Server vengono distribuiti tramite i canali Microsoft Update dopo che la VM SQL Server è stata registrata con il [provider di risorse](virtual-machines-windows-sql-register-with-resource-provider.md)VM SQL. Le patch possono essere scaricate manualmente o automaticamente.

*Automated patching* è abilitata per impostazione predefinita. Questa opzione consente ad Azure di applicare automaticamente le patch a SQL Server e al sistema operativo. È possibile specificare un giorno della settimana, l'ora e la durata di una finestra di manutenzione se è installata la SQL Server estensione IaaS. Durante la finestra di manutenzione Azure esegue l'applicazione delle patch. La pianificazione dell'ora, la finestra di manutenzione usa le impostazioni locali della macchina virtuale.  Per altre informazioni, vedere applicazione [automatica di patch per SQL Server in macchine virtuali di Azure](virtual-machines-windows-sql-automated-patching.md).


## <a name="next-steps"></a>Passaggi successivi

Eseguire la migrazione della macchina virtuale SQL Server ad Azure:

* [Eseguire la migrazione di un database di SQL Server a SQL Server in una VM di Azure](virtual-machines-windows-migrate-sql.md)

Inizia a usare SQL Server in macchine virtuali di Azure:

* [Creare una VM di SQL Server nel portale di Azure](quickstart-sql-vm-create-portal.md)

Risposte alle domande frequenti sulle macchine virtuali SQL Server:

* [Domande frequenti per SQL Server in macchine virtuali di Azure](virtual-machines-windows-sql-server-iaas-faq.md)
