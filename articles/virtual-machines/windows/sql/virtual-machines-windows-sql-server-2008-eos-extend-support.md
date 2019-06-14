---
title: Estendere il supporto per SQL Server 2008 e SQL Server 2008 R2 con Azure
description: Informazioni su come estendere il supporto per SQL Server 2008 e SQL Server 2008 R2 per la migrazione di istanza di SQL Server in Azure o l'acquisto di supporto "Extended" per mantenere le istanze locali.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/08/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 62261e46dc4744597acd10c32f0a835f4a597d4d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66243966"
---
# <a name="extend-support-for-sql-server-2008-and-sql-server-2008-r2-with-azure"></a>Estendere il supporto per SQL Server 2008 e SQL Server 2008 R2 con Azure

SQL Server 2008 e SQL Server 2008 R2 sono entrambi per raggiungere la [fine del loro ciclo di vita del supporto (May)](https://www.microsoft.com/sql-server/sql-server-2008). Poiché molti dei nostri clienti sta ancora usando entrambe le versioni, verranno fornite opzioni diverse per continuare a ottenere supporto. È possibile eseguire la migrazione di istanze di SQL Server in locale in macchine virtuali di Azure (VM), eseguire la migrazione al Database SQL di Azure o rimangono in locale e gli aggiornamenti della protezione estesa di acquisto.

A differenza con un'istanza gestita, la migrazione a una VM di Azure non richiede recertifying delle applicazioni. E, a differenza con rimanendo in locale, si riceverà le patch di protezione estesa gratuito eseguendo la migrazione a una VM di Azure. 

Il resto di questo articolo vengono fornite considerazioni per la migrazione di istanza di SQL Server in una VM di Azure. 

## <a name="provisioning"></a>Provisioning 

È presente un pagamento a consumo `SQL Server 2008 R2 on Windows Server 2008 R2` immagine disponibili in Azure marketplace. 

I clienti che usano SQL Server 2008 saranno necessario installare automaticamente, o l'aggiornamento a SQL Server 2008 R2. I clienti su Windows Server 2008 allo stesso modo, saranno necessario distribuire la macchina virtuale da un disco rigido virtuale personalizzato o per eseguire l'aggiornamento a Windows Server 2008 R2. 

Immagini distribuite tramite il Marketplace forniti con l'estensione SQL IaaS pre-installato. L'estensione SQL IaaS è un requisito per la gestione flessibile delle licenze e l'applicazione automatizzata di patch. I clienti che distribuiscono le macchine virtuali installate autonomamente saranno necessario installare manualmente l'estensione SQL IaaS. L'estensione SQL IaaS non è supportato in Windows 2008. 

  > [!NOTE]
  > Mentre il Server SQL `Create` e `Manage` pannelli funzionerà con l'immagine di SQL Server 2008 R2 nel portale di Azure, le funzionalità seguenti sono _non è supportato_: Backup automatici, integrazione di Azure Key Vault, R Services e configurazione dell'archiviazione.

## <a name="licensing"></a>Licenze
Distribuzioni di pagamento a consumo SQL Server 2008 R2 possono convertire [vantaggio Hybrid Azure (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/).

Per convertire una licenza di Software Assurance (SA) in base al consumo, i clienti devono registrarsi con la VM di SQL [provider di risorse](virtual-machines-windows-sql-ahb.md#register-sql-server-vm-with-sql-resource-provider). Una volta registrato con il provider di risorse di VM di SQL, il tipo di licenza SQL saranno interscambiabile tra vantaggio Azure Hybrid e pagamento a consumo. 

Le istanze di SQL Server 2008 o SQL Server 2008 R2 autonomo installate nella macchina virtuale di Azure è possono registrare con il provider di risorse SQL e convertire il tipo di licenza in base al consumo.

## <a name="migration"></a>Migrazione
È possibile eseguire la migrazione di istanze di SQL Server EOS a una VM di Azure con i metodi di backup/ripristino manuale; Questo è il metodo più comune di migrazione da locale a una VM di Azure.

### <a name="azure-site-recovery"></a>Azure Site Recovery

Per le migrazioni delle operazioni bulk, è consigliabile [Azure Site Recovery](/azure/site-recovery/site-recovery-overview) servizio. Con Azure Site Recovery, i clienti possono replicare l'intera macchina virtuale, incluso SQL Server da locale a una VM di Azure.

SQL Server richiede snapshot coerenti con l'app di Azure Site Recovery per garantire il ripristino; e Azure Site Recovery supporta gli snapshot coerenti con l'app con intervallo minimo di 1 ora. Il valore RPO minimo possibile per SQL Server con le migrazioni di Azure Site Recovery è 1 ora e l'obiettivo RTO è 2 ore + tempo di recupero di SQL Server.

### <a name="database-migration-service"></a>Servizio Migrazione del database

Il [servizio migrazione del Database](/azure/dms/dms-overview) è un'opzione per i clienti se eseguire una migrazione da locale alle macchine Virtuali di Azure eseguendo l'aggiornamento di SQL Server a SQL Server 2012 e versioni successive.

## <a name="disaster-recovery"></a>Ripristino di emergenza

Soluzioni di ripristino di emergenza per EOS SQL Server in macchine Virtuali di Azure sono i seguenti:

- **Backup di SQL Server**: Backup di SQL Server è utilizzabile per il ripristino di SQL Server in caso di internazionali o errori della zona. Poiché le funzionalità di backup gestita non sono supportata per EOS SQL Server, i clienti dovranno richiedere manualmente i backup.
- **Il log shipping**: È possibile creare una replica di log shipping in un'altra zona o l'area di Azure con le operazioni di ripristino continua per ridurre l'obiettivo RTO. I clienti dovranno configurare manualmente il log shipping.
- **Azure Site Recovery**: È possibile replicare le VM tra zone e aree tramite la replica di Azure Site Recovery. SQL Server richiede snapshot coerenti con l'App garantire il ripristino in caso di emergenza. Azure Site Recovery offre minimo RPO di 1 ora e 2 ore + tempo di ripristino di SQL Server RTO per EOS SQL Server di ripristino di emergenza.

## <a name="security-patching"></a>L'applicazione di patch di sicurezza
Gli aggiornamenti della protezione estesa per le macchine virtuali di SQL Server verrà recapitati tramite i canali Microsoft Update dopo che la macchina virtuale di SQL Server è stata registrata con il codice SQL [provider di risorse](virtual-machines-windows-sql-ahb.md#register-sql-server-vm-with-sql-resource-provider). Le patch che possono essere scaricate manualmente o automaticamente. 

**Automated patching** è abilitata per impostazione predefinita. Questa opzione consente ad Azure di applicare automaticamente le patch a SQL Server e al sistema operativo. Se è installata l'estensione SQL IaaS, è possibile specificare un giorno della settimana, ora e durata di una finestra di manutenzione. Durante la finestra di manutenzione Azure esegue l'applicazione delle patch. La pianificazione dell'ora, la finestra di manutenzione usa le impostazioni locali della macchina virtuale.  Per altre informazioni, vedere [Applicazione automatica delle patch per SQL Server nelle macchine virtuali di Azure (Resource Manager)](virtual-machines-windows-sql-automated-patching.md).


## <a name="next-steps"></a>Passaggi successivi

Eseguire la migrazione di VM di SQL Server in Azure

* [Eseguire la migrazione di un database di SQL Server a SQL Server in una VM di Azure](virtual-machines-windows-migrate-sql.md)

Introduzione a SQL Server in Macchine virtuali di Azure:

* [Creare una VM di SQL Server nel portale di Azure](quickstart-sql-vm-create-portal.md)

Risposte alle domande comuni sulle VM SQL:

* [Domande frequenti su SQL Server in macchine virtuali di Azure](virtual-machines-windows-sql-server-iaas-faq.md)
