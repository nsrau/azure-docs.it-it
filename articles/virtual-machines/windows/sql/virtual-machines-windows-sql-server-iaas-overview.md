---
title: Panoramica di SQL Server nelle macchine virtuali Windows di Azure | Microsoft Docs
description: Informazioni su come eseguire le versioni complete di SQL Server in macchine virtuali Windows di Azure. Ottenere collegamenti diretti a tutte le immagini di macchine virtuali di SQL Server e al relativo contenuto.
services: virtual-machines-windows
documentationcenter: 
author: rothja
manager: jhubbard
tags: azure-service-management
ms.assetid: c505089e-6bbf-4d14-af0e-dd39a1872767
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/02/2017
ms.author: jroth
ms.openlocfilehash: b10c995fdd8e241d354c62537a0600b393795c1b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-sql-server-on-azure-virtual-machines-windows"></a>Panoramica di SQL Server in macchine virtuali di Azure (Windows)

> [!div class="op_single_selector"]
> * [Windows](virtual-machines-windows-sql-server-iaas-overview.md)
> * [Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md)

Questo argomento descrive le opzioni per eseguire SQL Server in macchine virtuali Windows di Azure e include [collegamenti alle immagini del portale](#option-1-create-a-sql-vm-with-per-minute-licensing) e una panoramica delle [attività comuni](#manage-your-sql-vm).

> [!NOTE]
> Se si ha già familiarità con SQL Server e si vuole sapere come distribuire una VM Windows di SQL Server, vedere [Effettuare il provisioning di una macchina virtuale SQL Server di Windows nel portale di Azure](virtual-machines-windows-portal-sql-server-provision.md). Se in alternativa si vuole creare una VM Linux con SQL Server, vedere [Effettuare il provisioning di una macchina virtuale SQL Server di Linux nel portale di Azure](../../linux/sql/provision-sql-server-linux-virtual-machine.md)

Le VM di Azure consentono agli amministratori di database e agli sviluppatori di spostare le applicazioni e i carichi di lavoro di SQL Server locali nel cloud.

## <a name="scenarios"></a>Scenari
È possibile scegliere l'hosting dei dati in Azure per numerosi motivi. Se l'applicazione viene spostata in Azure, spostare anche i dati migliora le prestazioni. Esistono tuttavia altri vantaggi. Si ha automaticamente accesso a più data center per una presenza globale e il ripristino di emergenza e i dati sono altamente protetti e durevoli.

L'esecuzione di SQL Server in una VM di Azure costituisce un'opzione per archiviare i dati relazionali in Azure. È ideale per scenari diversi. Può ad esempio essere opportuno configurare la VM di Azure in modo che sia più simile possibile a un computer SQL Server locale oppure eseguire applicazioni e servizi aggiuntivi nello stesso server di database. Esistono due risorse principali che consentono di valutare altri scenari e considerazioni:

* [SQL Server nelle macchine virtuali](https://azure.microsoft.com/services/virtual-machines/sql-server/) offre una panoramica degli scenari ideali per usare SQL Server in VM di Azure. 
* [Scegliere un'opzione di SQL Server cloud: database SQL di Azure (PaaS) o SQL Server in VM di Azure (IaaS)](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md) offre un confronto dettagliato tra database SQL ed esecuzione di SQL Server in una VM.

## <a name="create-a-new-sql-vm"></a>Creare una nuova VM di SQL
Le sezioni seguenti forniscono collegamenti diretti al portale di Azure per le immagini della raccolta di macchine virtuali di SQL Server. A seconda dell'immagine selezionata, è possibile pagare i costi della licenza di SQL Server al minuto o scegliere l'opzione Bring Your Own License (BYOL).

Indicazioni dettagliate per la creazione di una nuova VM di SQL sono disponibili nell'esercitazione [Effettuare il provisioning di una macchina virtuale di SQL Server nel portale di Azure](virtual-machines-windows-portal-sql-server-provision.md). Vedere anche [Procedure consigliate per le prestazioni per SQL Server in Macchine virtuali di Azure](virtual-machines-windows-sql-performance.md), che illustra come selezionare le dimensioni appropriate per i computer e altre funzionalità disponibili durante il provisioning.

## <a name="option-1-create-a-sql-vm-with-per-minute-licensing"></a>Opzione 1: Creare una VM di SQL con una licenza al minuto
La tabella seguente descrive una matrice di immagini di SQL Server più recenti nella raccolta di macchine virtuali. Fare clic su un collegamento per iniziare a creare una nuova VM di SQL con la versione, l'edizione e il sistema operativo specificati. 

> [!TIP]
> Per informazioni sui prezzi delle VM e di SQL per queste immagini, vedere [Pricing guidance for SQL Server Azure VMs](virtual-machines-windows-sql-server-pricing-guidance.md) (Guida ai prezzi per le VM di SQL Server in Azure).

| Versione | Sistema operativo | Edizione |
| --- | --- | --- |
| **SQL Server 2017** |Windows Server 2016 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseWindowsServer2016), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonWindowsServer2016), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonWindowsServer2016), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonWindowsServer2016), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonWindowsServer2016) |
| **SQL Server 2016 SP1** |Windows Server 2016 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2016SP1EnterpriseWindowsServer2016), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2016SP1StandardWindowsServer2016), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2016SP1WebWindowsServer2016), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2016SP1ExpressWindowsServer2016), [Developer](https://portal.azure.com/#create/Microsoft.SQLServer2016SP1DeveloperWindowsServer2016) |
| **SQL Server 2014 SP2** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2ExpressWindowsServer2012R2) |
| **SQL Server 2012 SP3** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3ExpressWindowsServer2012R2) |

> [!NOTE]
> Per le immagini di macchine virtuali SQL Server disponibili per Linux, vedere [Panoramica di SQL Server in macchine virtuali di Azure (Linux)](../../linux/sql/sql-server-linux-virtual-machines-overview.md).

## <a id="BYOL"></a>Opzione 2: Creare una VM di SQL con una licenza esistente
È anche possibile scegliere l'opzione Bring Your Own License (BYOL). In questo scenario si paga solo per la VM senza altre spese per le licenze di SQL Server. Per usare la propria licenza, usare la matrice di versioni, edizioni e sistemi operativi di SQL Server più sotto. Nel portale questi nomi di immagine hanno il prefisso **{BYOL}**.

> [!TIP]
> L'opzione Bring Your Own License consente di risparmiare denaro nel tempo per i carichi di lavoro di produzione continui. Per altre informazioni, vedere [Pricing guidance for SQL Server Azure VMs](virtual-machines-windows-sql-server-pricing-guidance.md) (Guida ai prezzi per le VM di SQL Server in Azure).

| Versione | Sistema operativo | Edizione |
| --- | --- | --- |
| **SQL Server 2016 SP1** |Windows Server 2016 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP1StandardWindowsServer2016), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP1StandardWindowsServer2016) |
| **SQL Server 2014 SP2** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2EnterpriseWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2StandardWindowsServer2012R2) |
| **SQL Server 2012 SP2** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3EnterpriseWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3StandardWindowsServer2012R2) |

Oltre alle versioni elencate, sono disponibili altre combinazioni di versioni di SQL Server e dei sistemi operativi. Trovare altre immagini tramite una ricerca nel Marketplace nel portale di Azure (cercare "{BYOL} SQL Server").

> [!IMPORTANT]
> Per usare le immagini di VM BYOL, è necessario avere un contratto Enterprise con [mobilità delle licenze tramite Software Assurance in Azure](https://azure.microsoft.com/pricing/license-mobility/). È necessaria anche una licenza valida per la versione/edizione di SQL Server che si vuole usare. Si devono [fornire le informazioni BYOL necessarie a Microsoft](http://d36cz9buwru1tt.cloudfront.net/License_Mobility_Customer_Verification_Guide.pdf) entro **10** giorni dal provisioning della macchina virtuale. 

> [!NOTE]
> Non è possibile modificare il modello di licenza di una VM di SQL Server con costo al minuto per utilizzare la propria licenza. In questo caso, è necessario creare una nuova VM BYOL ed eseguire la migrazione dei database alla nuova VM. 

## <a name="manage-your-sql-vm"></a>Gestire la VM di SQL
Dopo il provisioning della VM di SQL Server, è possibile eseguire diverse attività di gestione facoltative. Per molti aspetti, la configurazione e la gestione di SQL Server sono identiche alla gestione di un'istanza di SQL Server locale. Tuttavia alcune attività sono specifiche di Azure. Le sezioni seguenti evidenziano alcuni di questi aspetti e includono i collegamenti ad altre informazioni.

### <a name="connect-to-the-vm"></a>Connettersi alla VM
Una delle operazioni di gestione più basilari è la connessione alla VM di SQL Server tramite strumenti come SQL Server Management Studio (SSMS). Per istruzioni per la connessione a una nuova VM di SQL Server, vedere [Connettersi a una macchina virtuale di SQL Server in Azure](virtual-machines-windows-sql-connect.md).

### <a name="migrate-your-data"></a>Migrare i dati
Se esiste già un database, è possibile spostarlo nella nuova VM di SQL di cui viene effettuato il provisioning. Per le linee guida e un elenco di opzioni per la migrazione, vedere [Eseguire la migrazione di un database di SQL Server a SQL Server in una macchina virtuale di Azure](virtual-machines-windows-migrate-sql.md).

### <a name="configure-high-availability"></a>Configurare la disponibilità elevata
Se è necessaria la disponibilità elevata, è consigliabile configurare i gruppi di disponibilità di SQL Server. Ciò comporta più macchine virtuali di Azure in una rete virtuale. Il portale di Azure dispone di un modello che consente di impostare questa configurazione. Per altre informazioni, vedere [Configurare automaticamente il gruppo di disponibilità AlwaysOn in macchine virtuali di Azure con Resource Manager](virtual-machines-windows-portal-sql-alwayson-availability-groups.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Per configurare manualmente il gruppo di disponibilità e il listener associato, vedere [Configurare manualmente il gruppo di disponibilità AlwaysOn in macchine virtuali di Azure con Resource Manager](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md).

Per ulteriori informazioni sulla disponibilità elevata, vedere [disponibilità elevata e ripristino di emergenza di SQL Server in macchine virtuali Azure](virtual-machines-windows-sql-high-availability-dr.md).

### <a name="back-up-your-data"></a>Eseguire il backup dei dati
Le macchine virtuali di Azure possono sfruttare il [backup automatizzato](virtual-machines-windows-sql-automated-backup.md), che crea periodicamente backup del database nell'archivio BLOB. È anche possibile usare questa tecnica manualmente. Per altre informazioni, vedere [Usare Archiviazione di Azure per il backup e il ripristino di SQL Server](virtual-machines-windows-use-storage-sql-server-backup-restore.md). Per una panoramica di tutte le opzioni di backup e ripristino, vedere [Backup e ripristino per SQL Server in Macchine virtuali di Azure](virtual-machines-windows-sql-backup-recovery.md).

### <a name="automate-updates"></a>Automatizzare gli aggiornamenti
Le macchine virtuali di Azure possono usare l' [applicazione automatica delle patch](virtual-machines-windows-sql-automated-patching.md) per pianificare una finestra di manutenzione per l'installazione automatica di importanti aggiornamenti di Windows e SQL Server.

### <a name="customer-experience-improvement-program-ceip"></a>Analisi utilizzo software
Il programma Analisi utilizzo software è abilitato per impostazione predefinita. Invia periodicamente report a Microsoft per contribuire a migliorare SQL Server. Non sono necessarie attività di gestione per il programma Analisi utilizzo software, a meno che non lo si voglia disabilitare dopo il provisioning. È possibile personalizzare o disabilitare Analisi utilizzo software connettendosi alla VM con Desktop remoto. Eseguire quindi l'utilità **Segnalazione errori e utilizzo funzionalità di SQL Server** . Seguire le istruzioni per disabilitare il reporting. 

Per altre informazioni sulla raccolta di dati, vedere l'[informativa sulla privacy di SQL Server](https://www.microsoft.com/EN-US/privacystatement/SQLServer/Default.aspx). 

## <a name="next-steps"></a>Passaggi successivi

Per domande sui prezzi, vedere [Pricing guidance for SQL Server Azure VMs](virtual-machines-windows-sql-server-pricing-guidance.md) (Guida ai prezzi per le VM di SQL Server in Azure) e la [pagina Prezzi di Azure](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). Selezionare l'edizione di destinazione di SQL Server nell'elenco **Sistema operativo/Software**. Visualizzare quindi i prezzi per macchine virtuali di dimensioni diverse.

Per altre domande, vedere prima di tutto [Domande frequenti su SQL Server in macchine virtuali di Azure](virtual-machines-windows-sql-server-iaas-faq.md). In alternativa, è possibile aggiungere commenti alla fine di uno degli argomenti relativi alle macchine virtuali di SQL per interagire con Microsoft e la community.
