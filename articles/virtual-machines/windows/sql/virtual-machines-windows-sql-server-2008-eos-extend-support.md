---
title: Estendere il supporto per SQL Server 2008 & 2008 R2
description: Estendere il supporto per SQL Server 2008 e SQL Server 2008 R2 eseguendo la migrazione dell'istanza di SQL Server in Azure o acquistando il supporto esteso per mantenere le istanze in locale.
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
ms.custom: seo-lt-2019
ms.openlocfilehash: 6c27fa8f80140e6db92ea0f998852507260cc278
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159541"
---
# <a name="extend-support-for-sql-server-2008-and-sql-server-2008-r2-with-azure"></a>Estendere il supporto per SQL Server 2008 e SQL Server 2008 R2 con AzureExtend support for SQL Server 2008 and SQL Server 2008 R2 with Azure

SQL Server 2008 e SQL Server 2008 R2 hanno entrambi raggiunto la [fine del ciclo di vita del supporto (EOS).](https://www.microsoft.com/sql-server/sql-server-2008) Poiché molti clienti utilizzano ancora entrambe le versioni, forniamo diverse opzioni per continuare a ricevere supporto. È possibile eseguire la migrazione delle istanze di SQL Server locale alle macchine virtuali di Azure, eseguire la migrazione al database SQL di Azure o rimanere in locale e acquistare aggiornamenti della sicurezza estesi.

A differenza di un'istanza gestita, la migrazione a una macchina virtuale di Azure non richiede una ricertificazione delle applicazioni. E a differenza di rimanere in locale, riceverai patch di sicurezza estese gratuite eseguendo la migrazione a una macchina virtuale di Azure.And unlike with staying on-premises, you'll receive free extended security patches by migrating to an Azure VM.

Il resto di questo articolo fornisce considerazioni per la migrazione dell'istanza di SQL Server a una macchina virtuale di Azure.The rest of this article provides considerations for migrating your SQL Server instance to an Azure VM.

Per ulteriori informazioni sulle opzioni di fine supporto, vedere [Fine del supporto](/sql/sql-server/end-of-support/sql-server-end-of-life-overview).

## <a name="provisioning"></a>Provisioning

In Azure Marketplace è disponibile un'immagine di SQL Server 2008 R2 con pagamento in **Windows Server 2008 R2.**

I clienti che utilizzano SQL Server 2008 dovranno eseguire l'installazione autonoma o l'aggiornamento a SQL Server 2008 R2. Analogamente, i clienti in Windows Server 2008 dovranno distribuire la propria macchina virtuale da un disco rigido virtuale personalizzato o eseguire l'aggiornamento a Windows Server 2008 R2.

Le immagini distribuite tramite Azure Marketplace vengono associate con l'estensione SQL IaaS preinstallata. L'estensione SQL IaaS è un requisito per licenze flessibili e patch automatiche. I clienti che distribuiscono macchine virtuali autoinstallate dovranno installare manualmente l'estensione SQL IaaS. L'estensione SQL IaaS non è supportata in Windows Server 2008.

> [!NOTE]
> Anche se i pannelli **Crea** e **gestisci** di SQL Server funzioneranno con l'immagine di SQL Server 2008 R2 nel portale di Azure, le funzionalità seguenti non sono _supportate:_ backup automatici, integrazione dell'insieme di credenziali delle chiavi di Azure, R Services e configurazione dell'archiviazione.

## <a name="licensing"></a>Gestione delle licenze
Le distribuzioni di SQL Server 2008 R2 con pagamento in base al pagamento possono essere convertite in vantaggi per Azure [Hybrid.](https://azure.microsoft.com/pricing/hybrid-benefit/)

Per convertire una licenza basata su Software Assurance (SA) in conpagamento in base al consumo, i clienti devono registrarsi presso il provider di [risorse](virtual-machines-windows-sql-register-with-resource-provider.md)della macchina virtuale SQL. Dopo tale registrazione, il tipo di licenza SQL sarà intercambiabile tra il vantaggio Azure Hybrid e il pagamento in base al consumo.

Le istanze di SQL Server 2008 o SQL Server 2008 R2 autoinstallate in una macchina virtuale di Azure possono registrarsi con il provider di risorse della macchina virtuale SQL e convertirne il tipo di licenza in con pagamento in base al consumo.

## <a name="migration"></a>Migrazione
È possibile eseguire la migrazione delle istanze di EOS SQL Server a una macchina virtuale di Azure con metodi di backup/ripristino manuali. Questo è il metodo di migrazione più comune da una macchina virtuale di Azure locale.

### <a name="azure-site-recovery"></a>Azure Site Recovery

Per le migrazioni in blocco, è consigliabile il servizio [Azure Site Recovery.For](/azure/site-recovery/site-recovery-overview) bulk migrations, we recommend the Azure Site Recovery service. Con Azure Site Recovery, i clienti possono replicare l'intera macchina virtuale, incluso SQL Server da locale a macchina virtuale di Azure.With Azure Site Recovery, customers can replicate the whole VM, including SQL Server from on-premises to Azure VM.

SQL Server richiede snapshot di Azure Site Recovery coerenti con le app per garantire il ripristino. Azure Site Recovery supporta snapshot coerenti con le app con un intervallo minimo di 1 ora. L'obiettivo del punto di ripristino minimo (RPO) possibile per SQL Server con migrazioni di Azure Site Recovery è 1 ora. L'obiettivo del tempo di ripristino (RTO) è di 2 ore più il tempo di ripristino di SQL Server.The recovery time objective (RTO) is 2 hours plus SQL Server recovery time.

### <a name="database-migration-service"></a>Servizio Migrazione del database

Il [servizio di migrazione del database](/azure/dms/dms-overview) è un'opzione per i clienti se eseguono la migrazione da una macchina virtuale di Azure locale aggiornando SQL Server alla versione 2012 o successiva.

## <a name="disaster-recovery"></a>Ripristino di emergenza

Le soluzioni di ripristino di emergenza per EOS SQL Server in una macchina virtuale di Azure sono le seguenti:Disaster recovery solutions for EOS SQL Server on an Azure VM are as follows:

- **Backup**di SQL Server : Usare Backup di Azure per proteggere l'eOS SQL Server 2008 e 2008 R2 da ransomware, eliminazione accidentale e danneggiamento con 15-min RPO e ripristino point-in-time. Per ulteriori informazioni, vedere [questo articolo](https://docs.microsoft.com/azure/backup/backup-azure-sql-database#scenario-support).
- **Distribuzione dei log:** è possibile creare una replica di distribuzione dei log in un'altra zona o in un'area di Azure con ripristini continui per ridurre l'oggetto RTO. È necessario configurare manualmente la distribuzione dei log.
- **Azure Site Recovery:** è possibile replicare la macchina virtuale tra aree e aree tramite la replica di Azure Site Recovery.Azure Site Recovery : You can replicate your VM between zones and regions through Azure Site Recovery replication. SQL ServerSQL Server richiede snapshot coerenti con l'app per garantire il ripristino in caso di emergenza. Azure Site Recovery offre un RPO minimo di 1 ora e un RTO di 2 ore (più il tempo di ripristino di SQL Server) per il ripristino di emergenza di SQL Server EOS.

## <a name="security-patching"></a>Applicazione di patch di sicurezza
Gli aggiornamenti della sicurezza estesi per le macchine virtuali di SQL Server vengono recapitati tramite i canali di Microsoft Update dopo la registrazione della macchina virtuale di SQL Server con il provider di [risorse](virtual-machines-windows-sql-register-with-resource-provider.md)DELLA macchina virtuale SQL. Le patch possono essere scaricate manualmente o automaticamente.

*Automated patching* è abilitata per impostazione predefinita. Questa opzione consente ad Azure di applicare automaticamente le patch a SQL Server e al sistema operativo. È possibile specificare un giorno della settimana, l'ora e la durata di una finestra di manutenzione se è installata l'estensione IaaS di SQL Server. Durante la finestra di manutenzione Azure esegue l'applicazione delle patch. La pianificazione dell'ora, la finestra di manutenzione usa le impostazioni locali della macchina virtuale.  Per altre informazioni, vedere Applicazione automatica di patch per SQL Server in Macchine virtuali di Azure.For more information, see [Automated patching for SQL Server on Azure Virtual Machines](virtual-machines-windows-sql-automated-patching.md).


## <a name="next-steps"></a>Passaggi successivi

Eseguire la migrazione della macchina virtuale di SQL Server in Azure:Migrate your SQL Server VM to Azure:

* [Eseguire la migrazione di un database di SQL Server a SQL Server in una macchina virtuale di Azure](virtual-machines-windows-migrate-sql.md)

Introduzione a SQL Server in Macchine virtuali di Azure:Get started with SQL Server on Azure Virtual Machines:

* [Creare una VM di SQL Server nel portale di Azure](quickstart-sql-vm-create-portal.md)

Risposte alle domande frequenti sulle macchine virtuali di SQL Server:Get answers to commonly asked questions about SQL Server VMs:

* [Domande frequenti su SQL Server in Macchine virtuali di AzureFAQ for SQL Server on Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-faq.md)

Ulteriori informazioni sulle opzioni di fine del supporto e sugli aggiornamenti della sicurezza estesi:

* [Fine del supporto](/sql/sql-server/end-of-support/sql-server-end-of-life-overview) & Aggiornamenti di[sicurezza estesi](/sql/sql-server/end-of-support/sql-server-extended-security-updates)