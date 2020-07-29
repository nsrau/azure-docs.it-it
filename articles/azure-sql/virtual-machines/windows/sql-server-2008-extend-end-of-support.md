---
title: Estendere il supporto per SQL Server 2008 e 2008 R2
description: Per estendere il supporto per SQL Server 2008 e SQL Server 2008 R2, eseguire la migrazione dell'istanza di SQL Server ad Azure o acquistare supporto esteso per mantenere le istanze in locale.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/08/2019
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 48288ed3765fa939fc56a4469f64070315c4c6aa
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84668747"
---
# <a name="extend-support-for-sql-server-2008-and-sql-server-2008-r2-with-azure"></a>Estendere il supporto per SQL Server 2008 e SQL Server 2008 R2 con Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

SQL Server 2008 e SQL Server 2008 R2 hanno raggiunto la [fine del ciclo di vita del supporto](https://www.microsoft.com/sql-server/sql-server-2008). Poiché molti clienti usano ancora entrambe le versioni, sono disponibili diverse opzioni per continuare a ricevere assistenza. È possibile eseguire la migrazione delle istanze di SQL Server locali a macchine virtuali di Azure, eseguire la migrazione al database SQL di Azure o rimanere in locale e acquistare aggiornamenti della sicurezza estesi.

A differenza di un'istanza gestita, per eseguire la migrazione a una macchina virtuale di Azure non è necessario certificare di nuovo le applicazioni. E a differenza di quanto avviene rimanendo in locale, si riceveranno patch di sicurezza estese gratuite eseguendo la migrazione a una macchina virtuale di Azure.

Nella parte rimanente di questo articolo sono riportate alcune considerazioni sulla migrazione dell'istanza di SQL Server a una macchina virtuale di Azure.

Per altre informazioni sulle opzioni di fine del supporto, vedere [Fine del supporto](/sql/sql-server/end-of-support/sql-server-end-of-life-overview).

## <a name="provisioning"></a>Provisioning

In Azure Marketplace è disponibile un'immagine di **SQL Server 2008 R2 in Windows Server 2008 R2** con pagamento in base al consumo.

I clienti con SQL Server 2008 dovranno eseguire l'installazione automatica o l'aggiornamento a SQL Server 2008 R2. Analogamente, i clienti di Windows Server 2008 dovranno distribuire la propria macchina virtuale da un disco rigido virtuale personalizzato o eseguire l'aggiornamento a Windows Server 2008 R2.

Nelle immagini distribuite con Azure Marketplace è preinstallata l'estensione SQL IaaS. L'estensione SQL IaaS è un requisito per le licenze flessibili e l'applicazione automatica delle patch. I clienti che distribuiscono macchine virtuali installate automaticamente dovranno installare manualmente l'estensione SQL IaaS. L'estensione SQL IaaS non è supportata in Windows Server 2008.

> [!NOTE]
> Sebbene i pannelli **Crea** e **Gestisci** di SQL Server funzionino con l'immagine di SQL Server 2008 R2 nel portale di Azure, le funzionalità seguenti _non sono supportate_: backup automatici, integrazione di Azure Key Vault, R Services e configurazione dell'archiviazione.

## <a name="licensing"></a>Gestione delle licenze
Le distribuzioni con pagamento in base al consumo di SQL Server 2008 R2 possono essere convertite in [Vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-benefit/).

Per convertire una licenza basata su Software Assurance in licenza con pagamento in base al consumo, i clienti devono registrarsi con il [provider di risorse](sql-vm-resource-provider-register.md) per VM SQL. Al termine della registrazione, il tipo di licenza SQL sarà interscambiabile tra Vantaggio Azure Hybrid e pagamento in base al consumo.

Le istanze di SQL Server 2008 o SQL Server 2008 R2 installate automaticamente in una macchina virtuale di Azure possono eseguire la registrazione con il provider di risorse per VM SQL e convertire il tipo di licenza in pagamento in base al consumo.

## <a name="migration"></a>Migrazione
È possibile eseguire la migrazione di istanze di SQL Server EOS a una macchina virtuale di Azure con metodi di backup o ripristino manuali. Questo è il metodo di migrazione più comune da locale a una macchina virtuale di Azure.

### <a name="azure-site-recovery"></a>Azure Site Recovery

Per le migrazioni in blocco, è consigliabile il servizio [Azure Site Recovery](/azure/site-recovery/site-recovery-overview). Con Azure Site Recovery, i clienti possono replicare l'intera macchina virtuale, incluso SQL Server da locale a macchina virtuale di Azure.

SQL Server richiede snapshot di Azure Site Recovery coerenti con l'app per garantire il ripristino. Azure Site Recovery supporta snapshot coerenti con l'app con un intervallo minimo di 1 ora. L'obiettivo del punto di ripristino (RPO) minimo possibile per le migrazioni di SQL Server con Azure Site Recovery è 1 ora. L'obiettivo del tempo di ripristino (RTO) è 2 ore più il tempo di ripristino di SQL Server.

### <a name="database-migration-service"></a>Servizio Migrazione del database

Il [servizio migrazione del database di Azure](/azure/dms/dms-overview) è un'opzione per i clienti che eseguono la migrazione dall'ambiente locale a una macchina virtuale di Azure aggiornando SQL Server alla versione 2012 o successiva.

## <a name="disaster-recovery"></a>Ripristino di emergenza

Le soluzioni di ripristino di emergenza per la versione EOS di SQL Server in una macchina virtuale di Azure sono le seguenti:

- **Backup di SQL Server**: usare Backup di Azure per proteggere la versione EOS di SQL Server 2008 e 2008 R2 da al ransomware, eliminazione accidentale e danneggiamento con RPO di 15 minuti e recupero temporizzato. Per altri dettagli, vedere [questo articolo](https://docs.microsoft.com/azure/backup/sql-support-matrix#scenario-support).
- **Log shipping**: è possibile creare una replica di log shipping in un'altra zona o un'area di Azure con ripristini continui per ridurre il tempo di ripristino. È necessario configurare manualmente il log shipping.
- **Azure Site Recovery**: è possibile replicare la macchina virtuale tra zone e aree usando la replica di Azure Site Recovery. SQL Server richiede snapshot coerenti con l'app per garantire il ripristino in caso di emergenza. Azure Site Recovery offre un RPO minimo di 1 ora e un RTO di 2 ore (più il tempo di ripristino di SQL Server) per il ripristino di emergenza di SQL Server EOS.

## <a name="security-patching"></a>Patch di sicurezza
Gli aggiornamenti della sicurezza estesi per le macchine virtuali di SQL Server vengono distribuiti attraverso i canali Microsoft Update dopo che la VM di SQL Server viene registrata con il [provider di risorse](sql-vm-resource-provider-register.md) per VM SQL. Le patch possono essere scaricate manualmente o automaticamente.

*Automated patching* è abilitata per impostazione predefinita. Questa opzione consente ad Azure di applicare automaticamente le patch a SQL Server e al sistema operativo. È possibile specificare un giorno della settimana, l'ora e la durata di una finestra di manutenzione se è installata l'estensione SQL Server IaaS. Durante la finestra di manutenzione Azure esegue l'applicazione delle patch. La pianificazione dell'ora, la finestra di manutenzione usa le impostazioni locali della macchina virtuale. Per altre informazioni, vedere [Applicazione automatica delle patch per SQL Server in macchine virtuali di Azure (Resource Manager)](automated-patching.md).


## <a name="next-steps"></a>Passaggi successivi

Eseguire la migrazione della VM di SQL Server ad Azure:

* [Eseguire la migrazione di un database di SQL Server a SQL Server in una VM di Azure](migrate-to-vm-from-sql-server.md)

Introduzione all'uso di SQL Server nelle macchine virtuali di Azure:

* [Creare una VM di SQL Server nel portale di Azure](sql-vm-create-portal-quickstart.md)

Risposte alle domande comuni sulle VM di SQL Server:

* [Domande frequenti su SQL Server nelle macchine virtuali di Azure](frequently-asked-questions-faq.md)

Informazioni sulle opzioni di fine supporto e sugli aggiornamenti della sicurezza estesi:

* [Fine del supporto](/sql/sql-server/end-of-support/sql-server-end-of-life-overview) & [Aggiornamenti della sicurezza estesi](/sql/sql-server/end-of-support/sql-server-extended-security-updates)