---
title: Backup e ripristino per SQL Server in macchine virtuali di Azure | Microsoft Docs
description: Vengono descritte considerazioni sul backup e sul ripristino per i database di SQL Server in esecuzione in macchine virtuali di Azure.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-resource-management
ms.assetid: 95a89072-0edf-49b5-88ed-584891c0e066
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/04/2018
ms.author: mikeray
ms.openlocfilehash: e4c126bbac73accb984f1040a7fea1740d919233
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100543"
---
# <a name="backup-and-restore-for-sql-server-in-azure-virtual-machines"></a>Backup e ripristino per SQL Server in Macchine virtuali di Azure

Questo articolo fornisce indicazioni sulle opzioni di backup e ripristino disponibili per SQL Server in esecuzione in una macchina virtuale Windows in Azure. Archiviazione di Azure mantiene tre copie di ogni disco di macchina virtuale di Azure per garantire la protezione dalla perdita di dati e dal danneggiamento fisico dei dati. Diversamente dalle soluzioni locali, quindi, non è necessario preoccuparsi degli errori hardware. È comunque consigliabile creare copie di backup dei database di SQL Server per la protezione da errori delle applicazioni o degli utenti, come inserimenti o eliminazioni di dati accidentali. In questo caso, è importante essere in grado di eseguire il ripristino in un punto specifico nel tempo.

La prima parte di questo articolo offre una panoramica delle opzioni di ripristino e backup disponibili. Seguono alcune sezioni con ulteriori informazioni su ogni strategia.

## <a name="backup-and-restore-options"></a>Opzioni di backup e ripristino

Nella tabella seguente sono disponibili informazioni sulle varie opzioni di backup e ripristino per SQL Server in esecuzione in macchine virtuali di Azure:

| Strategia | Versioni di SQL Server | DESCRIZIONE |
|---|---|---|
| [Backup automatico](#automated) | 2014<br/> 2016<br/> 2017 | La funzionalità Backup automatizzato consente di pianificare backup regolari per tutti i database in una macchina virtuale di SQL Server. I backup vengono archiviati nell'archivio di Azure per un massimo di 30 giorni. A partire da SQL Server 2016, la versione 2 di Backup automatizzato offre opzioni aggiuntive, ad esempio la configurazione di una pianificazione manuale e la frequenza di backup completi e del log. |
| [Backup di Azure per le macchine virtuali di SQL Server](#azbackup) | 2008<br/> 2012<br/> 2014<br/> 2016<br/> 2017 | Backup di Azure offre una funzionalità di backup adatta alle grandi aziende per SQL Server in esecuzione in macchine virtuali di Azure. Con questo servizio, è possibile gestire centralmente i backup per più server e migliaia di database. I database possono essere ripristinati in un punto specifico nel tempo nel portale. Il servizio offre criteri di conservazione personalizzabili che consentono di mantenere i backup per anni. |
| [Backup manuale](#manual) | Tutti | A seconda della versione di SQL Server, esistono diverse tecniche per eseguire manualmente il backup e il ripristino di SQL Server in esecuzione in una macchina virtuale di Azure. In questo scenario, si è responsabili della modalità di backup dei database, nonché della posizione di archiviazione e della gestione di questi backup. |

Le sezioni seguenti descrivono ogni opzione in modo più dettagliato. La sezione finale di questo articolo offre un riepilogo sotto forma di matrice di funzionalità.

## <a id="automated"></a> Backup automatizzato

Il servizio Backup automatizzato consente il backup automatico per le edizioni di SQL Server Standard ed Enterprise in esecuzione in una macchina virtuale Windows in Azure. Il servizio viene reso disponibile dall'[Estensione SQL Server IaaS Agent](virtual-machines-windows-sql-server-agent-extension.md), installata automaticamente nelle immagini di macchine virtuali Windows di SQL Server nel portale di Azure.

Per tutti i database viene creata una copia di backup in un account di archiviazione di Azure configurato. I backup possono essere crittografati e conservati per un massimo di 30 giorni.

Le macchine virtuali SQL Server 2016 e versioni successive offrono ulteriori opzioni di personalizzazione con la versione 2 di Backup automatizzato. I miglioramenti includono:

- Backup dei database di sistema
- Pianificazione del backup e intervallo di tempo manuali
- Frequenza per i backup completi e del file di log

Per ripristinare un database, è necessario individuare i file di backup richiesti nell'account di archiviazione ed eseguire un ripristino nella macchina virtuale SQL tramite SQL Server Management Studio (SSMS) o comandi Transact-SQL.

Per altre informazioni su come configurare Backup automatizzato per le macchine virtuali SQL, vedere uno degli articoli seguenti:

- **SQL Server 2016/2017**: [Backup automatico v2 per macchine virtuali di Azure](virtual-machines-windows-sql-automated-backup-v2.md)
- **SQL Server 2014**: [backup automatico per macchine virtuali SQL Server 2014](virtual-machines-windows-sql-automated-backup.md)

## <a id="azbackup"></a>Backup di Azure per macchine virtuali SQL

[Backup di Azure](/azure/backup/) offre una funzionalità di backup adatta alle grandi aziende per SQL Server in esecuzione in macchine virtuali di Azure. Tutti i backup vengono archiviati e gestiti in un insieme di credenziali di Servizi di ripristino. Esistono diversi vantaggi offerti da questa soluzione, in particolare per le aziende di grandi dimensioni:

- **Backup senza infrastruttura**: non è necessario gestire i server di backup o le posizioni di archiviazione.
- **Scalabilità**: è possibile proteggere molte macchine virtuali SQL e migliaia di database.
- **Pagamento in base al consumo**: questa funzionalità è un servizio separato fornito da Backup di Azure, ma come con tutti i servizi di Azure, si paga solo per ciò che si usa.
- **Gestione e monitoraggio centralizzati**: gestione centralizzata di tutti i backup, inclusi altri carichi di lavoro supportati da Backup di Azure, da un singolo dashboard in Azure.
- **Backup e conservazione basati su criteri**: creare criteri di backup standard per backup a intervalli regolari. Stabilire i criteri di conservazione per mantenere i backup per anni.
- **Supporto per SQL Always On**: rilevare e proteggere una configurazione di SQL Server Always On e rispettare le preferenze di backup dei gruppi di disponibilità.
- **Obiettivo del punto di ripristino (RPO) di 15 minuti**: configurare i backup del log delle transazioni di SQL Server con intervalli fino a ogni 15 minuti.
- **Ripristino temporizzato**: usare il portale per ripristinare i database in un punto specifico nel tempo senza dover ripristinare manualmente più backup completi, differenziali e del log.
- **Consolidamento degli avvisi tramite posta elettronica per gli errori**: configurare notifiche tramite posta elettronica consolidate per eventuali errori.
- **Controllo degli accessi in base al ruolo**: determinare chi può gestire le operazioni di backup e ripristino tramite il portale.

Per una rapida panoramica del funzionamento con una demo, guardare il video seguente:

> [!VIDEO https://www.youtube.com/embed/wmbANpHos_E]

Questa soluzione di backup di Azure per le macchine virtuali SQL è disponibile a livello generale. Per altre informazioni, vedere [Back up SQL Server database to Azure](../../../backup/backup-azure-sql-database.md) (Eseguire il backup del database di SQL Server in Azure).

## <a id="manual"></a> Backup manuale

Se si vogliono gestire manualmente le operazioni di backup e ripristino nelle macchine virtuali SQL, sono disponibili varie opzioni a seconda della versione di SQL Server in uso. Per una panoramica delle operazioni di backup e ripristino, vedere uno degli articoli seguenti in base alla versione di SQL Server:

- [Backup e ripristino per SQL Server 2016 e versioni successive](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases)
- [Backup e ripristino per SQL Server 2014](https://msdn.microsoft.com/library/ms187048%28v=sql.120%29.aspx)
- [Backup e ripristino per SQL Server 2012](https://msdn.microsoft.com/library/ms187048%28v=sql.110%29.aspx)
- [Backup e ripristino per SQL Server 2008 R2](https://msdn.microsoft.com/library/ms187048%28v=sql.105%29.aspx)
- [Backup e ripristino per SQL Server 2008](https://msdn.microsoft.com/library/ms187048%28v=sql.100%29.aspx)

Le sezioni seguenti descrivono varie opzioni per il backup e il ripristino manuali in modo più dettagliato.

### <a name="backup-to-attached-disks"></a>Backup su dischi collegati

Per SQL Server in esecuzione nelle macchine virtuali di Azure, è possibile usare le tecniche di backup e ripristino native con dischi collegati alla macchina virtuale come destinazione dei file di backup. Vi è tuttavia un limite al numero di dischi che è possibile collegare a una macchina virtuale di Azure, a seconda della [dimensione della macchina virtuale](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). È inoltre da prendere in considerazione l'overhead della gestione disco.

Per un esempio di come creare manualmente un backup completo del database usando SQL Server Management Studio (SSMS) o Transact-SQL, vedere [Creare un backup completo del database](https://docs.microsoft.com/sql/relational-databases/backup-restore/create-a-full-database-backup-sql-server).

### <a name="backup-to-url"></a>Backup su URL

A partire da SQL Server 2012 SP1 CU2, è possibile eseguire backup e ripristino direttamente in un archivio BLOB di Microsoft Azure, operazione nota anche come backup su URL. In SQL Server 2016 sono stati introdotti anche i miglioramenti seguenti per questa funzionalità:

| Miglioramento nella versione 2016 | Dettagli |
| --- | --- |
| **Striping** |Quando si esegue un backup nell'archivio BLOB di Microsoft Azure, SQL Server 2016 supporta il backup di più BLOB per abilitare il backup di database di grandi dimensioni, fino a un massimo di 12,8 TB. |
| **Backup di snapshot** |Grazie all'usp di snapshot di Azure, la funzionalità di backup di snapshot dei file di SQL Server fornisce backup quasi istantanei e ripristini rapidi per i file di database archiviati con il servizio di archiviazione BLOB di Azure. Questa funzionalità consente di semplificare i criteri di backup e ripristino. Backup di snapshot di file supporta anche il ripristino temporizzato. Per altre informazioni, vedere [Backup di snapshot di file di database in Azure](https://docs.microsoft.com/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure). |

Per altre informazioni, vedere uno degli articoli seguenti in base alla versione di SQL Server:

- **SQL Server 2016/2017**: [Backup di SQL Server in un URL](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service)
- **SQL Server 2014**: [Backup di SQL Server 2014 in un URL](https://msdn.microsoft.com/library/jj919148%28v=sql.120%29.aspx)
- **SQL Server 2012**: [Backup di SQL Server 2012 in un URL](https://msdn.microsoft.com/library/jj919148%28v=sql.110%29.aspx)

### <a name="managed-backup"></a>Backup gestito

A partire da SQL Server 2014, Backup gestito consente di automatizzare la creazione dei backup nell'archivio di Azure. Dietro le quinte, Backup gestito usa la funzionalità di backup su URL descritta nella sezione precedente di questo articolo. Backup gestito è anche la funzionalità sottostante che supporta il servizio Backup automatizzato per le macchine virtuali di SQL Server.

A partire da SQL Server 2016, Backup gestito include ulteriori opzioni per la pianificazione, il backup dei database di sistema e la frequenza per il backup completo e del log.

Per altre informazioni, vedere uno degli articoli seguenti in base alla versione di SQL Server:

- [Backup gestito di SQL Server in Microsoft Azure per SQL Server 2016 e versioni successive](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure)
- [Backup gestito di SQL Server in Microsoft Azure per SQL Server 2014](https://msdn.microsoft.com/library/dn449496%28v=sql.120%29.aspx)

## <a name="decision-matrix"></a>Matrice decisionale

La tabella seguente riepiloga le funzionalità di ogni opzione di backup e ripristino per le macchine virtuali SQL Server in Azure.

|| **Backup automatico** | **Backup di Azure per SQL** | **Backup manuale** |
|---|---|---|---|
| Richiede altri servizi di Azure |   | ![Sì](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Configurare i criteri di backup nel portale di Azure | ![Sì](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Sì](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Ripristinare i database nel portale di Azure |   | ![Sì](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Gestire più server in un unico dashboard |   | ![Sì](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Ripristino temporizzato | ![Sì](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Sì](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Sì](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| Obiettivo del punto di ripristino (RPO) di 15 minuti | ![Sì](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Sì](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Sì](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| Criteri di conservazione dei backup a breve termine (giorni) | ![Sì](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Sì](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Criteri di conservazione dei backup a lungo termine (mesi, anni) |   | ![Yes](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Supporto predefinito per SQL Server Always On |   | ![Sì](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Backup in account di archiviazione di Azure | ![Yes](./media/virtual-machines-windows-sql-backup-recovery/yes.png)(automatico) | ![Yes](./media/virtual-machines-windows-sql-backup-recovery/yes.png)(automatico) | ![Sì](./media/virtual-machines-windows-sql-backup-recovery/yes.png)(gestito dal cliente) |
| Gestione dei file di backup e archiviazione | | ![Sì](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |  |
| Backup su dischi collegati nella macchina virtuale |   |   | ![Sì](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| Report sui backup personalizzabili centralizzati |   | ![Sì](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Avvisi tramite posta elettronica consolidati per gli errori |   | ![Sì](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Personalizzare il monitoraggio in base ai log di monitoraggio di Azure |   | ![Sì](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Monitoraggio dei processi di backup con SSMS o script Transact-SQL | ![Yes](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Sì](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![Yes](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| Ripristino dei database con SSMS o script Transact-SQL | ![Sì](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   | ![Yes](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |

## <a name="next-steps"></a>Passaggi successivi

Se si pianifica la distribuzione di SQL Server in una macchina virtuale di Azure, è possibile trovare indicazioni per il provisioning nella guida seguente: [Come effettuare il provisioning di una macchina virtuale Windows di SQL Server nel portale di Azure](virtual-machines-windows-portal-sql-server-provision.md).

Sebbene il backup e il ripristino possano essere utilizzati per la migrazione dei dati, esistono percorsi di SQL Server potenzialmente più semplici per la migrazione dati in una macchina virtuale di Azure. Per una descrizione completa delle opzioni di migrazione e i suggerimenti, vedere [Migrazione di un database a SQL Server in una macchina virtuale di Azure](virtual-machines-windows-migrate-sql.md).
