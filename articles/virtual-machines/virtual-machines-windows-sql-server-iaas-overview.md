---
title: Panoramica di SQL Server in macchine virtuali di Azure | Microsoft Docs
description: Informazioni su come eseguire le versioni complete di SQL Server in macchine virtuali di Azure. Ottenere collegamenti diretti a tutte le immagini di macchine virtuali di SQL Server e al relativo contenuto.
services: virtual-machines-windows
documentationcenter: 
author: rothja
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: c505089e-6bbf-4d14-af0e-dd39a1872767
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 10/19/2016
ms.author: jroth
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: 63ae1f48bcc384eff27a6fa8642f7a214bc7493f


---
# <a name="overview-of-sql-server-on-azure-virtual-machines"></a>Panoramica di SQL Server in macchine virtuali di Azure
Questo argomento descrive le opzioni per eseguire SQL Server in macchine virtuali (VM) di Azure e include [collegamenti alle immagini del portale](#option-1-create-a-sql-vm-with-per-minute-licensing) e una panoramica delle [attività comuni](#manage-your-sql-vm).

> [!NOTE]
> Se si ha già familiarità con SQL Server e si vuole sapere come distribuire una VM di SQL Server, vedere [Effettuare il provisioning di una macchina virtuale di SQL Server nel portale di Azure](virtual-machines-windows-portal-sql-server-provision.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
> 
> 

## <a name="overview"></a>Panoramica
Le VM di Azure consentono agli amministratori di database e agli sviluppatori di spostare le applicazioni e i carichi di lavoro di SQL Server locali nel cloud. Il video seguente offre una panoramica tecnica delle VM di SQL Server Azure.

> [!VIDEO https://channel9.msdn.com/Events/DataDriven/SQLServer2016/Azure-VM-is-the-best-platform-for-SQL-Server-2016/player]
> 
> 

Il video copre le aree seguenti:

| Time | Area |
| --- | --- |
| 00:21 |Informazioni sulle VM di Azure |
| 01:45 |Sicurezza |
| 02:50 |Connettività |
| 03:30 |Affidabilità e prestazioni delle risorse di archiviazione |
| 05:20 |Dimensioni delle macchine virtuali |
| 05:54 |Disponibilità elevata e contratto di servizio |
| 07:30 |Supporto per la configurazione |
| 08:00 |Monitoraggio |
| 08:32 |Demo: creare una VM di SQL Server 2016 |

> [!NOTE]
> Il video è incentrato su SQL Server 2016, ma Azure offre immagini di VM per diverse versioni di SQL Server, tra cui le versioni 2008, 2012, 2014 e 2016. 
> 
> 

## <a name="scenarios"></a>Scenari
È possibile scegliere l'hosting dei dati in Azure per numerosi motivi. Se l'applicazione viene spostata in Azure, spostare anche i dati migliora le prestazioni. Esistono tuttavia altri vantaggi. Si ha automaticamente accesso a più data center per una presenza globale e il ripristino di emergenza e i dati sono altamente protetti e durevoli.

L'esecuzione di SQL Server in una VM di Azure costituisce un'opzione per archiviare i dati relazionali in Azure. È ideale per scenari diversi. Può ad esempio essere opportuno configurare la VM di Azure in modo che sia più simile possibile a un computer SQL Server locale oppure eseguire applicazioni e servizi aggiuntivi nello stesso server di database. Esistono due risorse principali che consentono di valutare altri scenari e considerazioni:

* [SQL Server nelle macchine virtuali](https://azure.microsoft.com/services/virtual-machines/sql-server/) offre una panoramica degli scenari ideali per usare SQL Server in VM di Azure. 
* [Scegliere un'opzione di SQL Server cloud: database SQL di Azure (PaaS) o SQL Server in VM di Azure (IaaS)](../sql-database/sql-database-paas-vs-sql-server-iaas.md) offre un confronto dettagliato tra database SQL ed esecuzione di SQL Server in una VM.

## <a name="create-a-new-sql-vm"></a>Creare una nuova VM di SQL
Le sezioni seguenti forniscono collegamenti diretti al portale di Azure per le immagini della raccolta di macchine virtuali di SQL Server. A seconda dell'immagine selezionata, è possibile pagare i costi della licenza di SQL Server al minuto o scegliere l'opzione Bring Your Own License (BYOL).

Indicazioni dettagliate per questo processo sono disponibili nell'esercitazione [Effettuare il provisioning di una macchina virtuale di SQL Server nel portale di Azure](virtual-machines-windows-portal-sql-server-provision.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Vedere anche [Procedure consigliate per le prestazioni per SQL Server in Macchine virtuali di Azure](virtual-machines-windows-sql-performance.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), che illustra come selezionare le dimensioni appropriate per i computer e altre funzionalità disponibili durante il provisioning.

## <a name="option-1-create-a-sql-vm-with-per-minute-licensing"></a>Opzione 1: Creare una VM di SQL con una licenza al minuto
La tabella seguente descrive una matrice di immagini di SQL Server disponibili nella raccolta di macchine virtuali. Fare clic su un collegamento per iniziare a creare una nuova VM di SQL con la versione, l'edizione e il sistema operativo specificati.

| Versione | Sistema operativo | Edizione |
| --- | --- | --- |
| **SQL 2016** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMEnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMStandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMWebWindowsServer2012R2), [Dev](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMDeveloperWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMExpressWindowsServer2012R2) |
| **SQL 2014 SP1** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1ExpressWindowsServer2012R2) |
| **SQL 2014** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2014WebWindowsServer2012R2) |
| **SQL 2012 SP3** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3ExpressWindowsServer2012R2) |
| **SQL 2012 SP2** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2WebWindowsServer2012R2) |
| **SQL 2012 SP2** |Windows Server 2012 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2EnterpriseWindowsServer2012), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2StandardWindowsServer2012), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2WebWindowsServer2012), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2ExpressWindowsServer2012) |
| **SQL 2008 R2 SP3** |Windows Server 2008 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3EnterpriseWindowsServer2008R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3StandardWindowsServer2008R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3WebWindowsServer2008R2) |
| **SQL 2008 R2 SP3** |Windows Server 2012 |[Express](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3ExpressWindowsServer2012) |

## <a name="option-2-create-a-sql-vm-with-an-existing-license"></a>Opzione 2: Creare una VM di SQL con una licenza esistente
È anche possibile scegliere l'opzione Bring Your Own License (BYOL). In questo scenario si paga solo per la VM senza altre spese per le licenze di SQL Server. Per usare la propria licenza, usare la matrice di versioni, edizioni e sistemi operativi di SQL Server più sotto. Nel portale questi nomi di immagine hanno il prefisso **{BYOL}**.

| Versione | Sistema operativo | Edizione |
| --- | --- | --- |
| **SQL Server 2016** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016RTMStandardWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016RTMStandardWindowsServer2012R2) |
| **SQL Server 2014 SP1** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP1EnterpriseWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP1StandardWindowsServer2012R2) |
| **SQL Server 2012 SP2** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3EnterpriseWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3StandardWindowsServer2012R2) |

> [!IMPORTANT]
> Per usare le immagini di VM BYOL, è necessario avere un contratto Enterprise con [mobilità delle licenze tramite Software Assurance in Azure](https://azure.microsoft.com/pricing/license-mobility/). È necessaria anche una licenza valida per la versione/edizione di SQL Server che si vuole usare. Si devono [fornire le informazioni BYOL necessarie a Microsoft](http://d36cz9buwru1tt.cloudfront.net/License_Mobility_Customer_Verification_Guide.pdf) entro **10** giorni dal provisioning della macchina virtuale.
> 
> 

## <a name="manage-your-sql-vm"></a>Gestire la VM di SQL
Dopo il provisioning della VM di SQL Server, è possibile eseguire diverse attività di gestione facoltative. Per molti aspetti, la configurazione e la gestione di SQL Server sono identiche alla gestione di un'istanza di SQL Server locale. Tuttavia alcune attività sono specifiche di Azure. Le sezioni seguenti evidenziano alcuni di questi aspetti e includono i collegamenti ad altre informazioni.

### <a name="connect-to-the-vm"></a>Connettersi alla VM
Una delle operazioni di gestione più basilari è la connessione alla VM di SQL Server tramite strumenti come SQL Server Management Studio (SSMS). Per istruzioni per la connessione a una nuova VM di SQL Server, vedere [Connettersi a una macchina virtuale di SQL Server in Azure](virtual-machines-windows-sql-connect.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="migrate-your-data"></a>Migrare i dati
Se esiste già un database, è possibile spostarlo nella nuova VM di SQL di cui viene effettuato il provisioning. Per le linee guida e un elenco di opzioni per la migrazione, vedere [Eseguire la migrazione di un database di SQL Server a SQL Server in una macchina virtuale di Azure](virtual-machines-windows-migrate-sql.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="configure-high-availability"></a>Configurare la disponibilità elevata
Se è necessaria la disponibilità elevata, è consigliabile configurare i gruppi di disponibilità di SQL Server. Ciò comporta più macchine virtuali di Azure in una rete virtuale. Il portale di Azure dispone di un modello che consente di impostare questa configurazione. Per altre informazioni, vedere [Configurare automaticamente il gruppo di disponibilità AlwaysOn in macchine virtuali di Azure con Resource Manager](virtual-machines-windows-portal-sql-alwayson-availability-groups.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Per configurare manualmente il gruppo di disponibilità e il listener associato, vedere [Configurare manualmente il gruppo di disponibilità AlwaysOn in macchine virtuali di Azure con Resource Manager](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Per ulteriori informazioni sulla disponibilità elevata, vedere [disponibilità elevata e ripristino di emergenza di SQL Server in macchine virtuali Azure](virtual-machines-windows-sql-high-availability-dr.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="back-up-your-data"></a>Eseguire il backup dei dati
Le macchine virtuali di Azure possono sfruttare il [backup automatizzato](virtual-machines-windows-sql-automated-backup.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), che crea periodicamente backup del database nell'archivio BLOB. È anche possibile usare questa tecnica manualmente. Per altre informazioni, vedere [Usare Archiviazione di Azure per il backup e il ripristino di SQL Server](virtual-machines-windows-use-storage-sql-server-backup-restore.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Per una panoramica di tutte le opzioni di backup e ripristino, vedere [Backup e ripristino per SQL Server in Macchine virtuali di Azure](virtual-machines-windows-sql-backup-recovery.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="automate-updates"></a>Automatizzare gli aggiornamenti
Le macchine virtuali di Azure possono usare l' [applicazione automatica delle patch](virtual-machines-windows-sql-automated-patching.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) per pianificare una finestra di manutenzione per l'installazione automatica di importanti aggiornamenti di Windows e SQL Server.

### <a name="customer-experience-improvement-program-ceip"></a>Analisi utilizzo software
Il programma Analisi utilizzo software è abilitato per impostazione predefinita. Invia periodicamente report a Microsoft per contribuire a migliorare SQL Server. Non sono necessarie attività di gestione per il programma Analisi utilizzo software, a meno che non lo si voglia disabilitare dopo il provisioning. È possibile personalizzare o disabilitare Analisi utilizzo software connettendosi alla VM con Desktop remoto. Eseguire quindi l'utilità **Segnalazione errori e utilizzo funzionalità di SQL Server** . Seguire le istruzioni per disabilitare il reporting. 

Per altre informazioni, vedere la sezione relativa ad Analisi utilizzo software nell'argomento [Accettazione delle condizioni di licenza](https://msdn.microsoft.com/library/ms143343.aspx). 

## <a name="next-steps"></a>Passaggi successivi
[Esplorare il percorso di apprendimento](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/) per SQL Server in macchine virtuali di Azure.

Per altre domande, vedere prima di tutto [Domande frequenti su SQL Server in macchine virtuali di Azure](virtual-machines-windows-sql-server-iaas-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). In alternativa, è possibile aggiungere commenti alla fine di uno degli argomenti relativi alle macchine virtuali di SQL per interagire con Microsoft e la community.




<!--HONumber=Nov16_HO2-->


