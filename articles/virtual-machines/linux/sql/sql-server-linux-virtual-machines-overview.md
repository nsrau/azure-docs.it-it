---
title: Panoramica di SQL Server in macchine virtuali Linux di Azure | Microsoft Docs
description: Informazioni su come eseguire edizioni complete di SQL Server in macchine virtuali Linux di Azure, con collegamenti diretti a tutte le immagini di VM Linux di SQL Server e ai contenuti correlati.
services: virtual-machines-linux
documentationcenter: 
author: rothja
manager: jhubbard
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: get-started-article
ms.workload: iaas-sql-server
ms.date: 10/02/2017
ms.author: jroth
ms.openlocfilehash: f044fcba92eb7aa7f4ac44608571fab3db0e03f5
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/18/2017
---
# <a name="overview-of-sql-server-on-azure-virtual-machines-linux"></a>Panoramica di SQL Server in macchine virtuali di Azure (Linux)

> [!div class="op_single_selector"]
> * [Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)
> * [Linux](sql-server-linux-virtual-machines-overview.md)

Questo argomento descrive le opzioni disponibili per eseguire SQL Server in macchine virtuali (VM) Linux di Azure, con collegamenti alle [immagini del portale](#option-1-create-a-sql-vm-with-per-minute-licensing).

> [!NOTE]
> Se si ha già familiarità con SQL Server e si vuole solo sapere come distribuire una VM Linux di SQL Server, vedere [Effettuare il provisioning di una macchina virtuale Linux di SQL Server nel portale di Azure](provision-sql-server-linux-virtual-machine.md). Se invece si vuole creare una VM Windows con SQL Server, vedere [Effettuare il provisioning di una macchina virtuale Windows di SQL Server nel portale di Azure](../../windows/sql/virtual-machines-windows-portal-sql-server-provision.md).

Le VM di Azure consentono agli amministratori di database e agli sviluppatori di spostare le applicazioni e i carichi di lavoro di SQL Server locali nel cloud.

## <a name="scenarios"></a>Scenari

È possibile scegliere l'hosting dei dati in Azure per numerosi motivi. In caso di distribuzione o migrazione di un'applicazione in Azure, inserire in Azure anche i dati back-end migliora le prestazioni. Si ha automaticamente accesso a più data center per una presenza globale e il ripristino di emergenza e i dati sono altamente protetti e durevoli.

L'esecuzione di SQL Server in una VM di Azure costituisce un'opzione per archiviare i dati relazionali in Azure. È anche possibile usare il servizio database SQL di Azure. Per altre informazioni sulla scelta tra SQL Server in macchine virtuali e il database SQL di Azure, vedere [Scegliere un'opzione di SQL Server cloud: database SQL di Azure (PaaS) o SQL Server in VM di Azure (IaaS)](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md).

## <a id="create"></a> Creare una nuova VM di SQL

Per indicazioni dettagliate per la creazione di una nuova VM di SQL, vedere l'esercitazione [Effettuare il provisioning di una macchina virtuale Linux di SQL Server nel portale di Azure](provision-sql-server-linux-virtual-machine.md).

La tabella seguente descrive una matrice di immagini di SQL Server più recenti nella raccolta di macchine virtuali. Fare clic su un collegamento per iniziare a creare una nuova VM di SQL con la versione, l'edizione e il sistema operativo specificati.

> [!TIP]
> Per informazioni sui prezzi delle VM e di SQL per queste immagini, vedere la [pagina dei prezzi per le VM Linux di SQL Server](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

| Versione | Sistema operativo | Edizione |
| --- | --- | --- |
| **SQL Server 2017** | Red Hat Enterprise Linux (RHEL) 7.4 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonRedHatEnterpriseLinux74), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonRedHatEnterpriseLinux74), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonRedHatEnterpriseLinux74), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonRedHatEnterpriseLinux74), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonRedHatEnterpriseLinux74) |
| **SQL Server 2017** | SUSE Linux Enterprise Server (SLES) v12 SP2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonSLES12SP2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonSLES12SP2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonSLES12SP2), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonSLES12SP2), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonSLES12SP2) |
| **SQL Server 2017** | Ubuntu 16.04 LTS |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonUbuntuServer1604LTS), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonUbuntuServer1604LTS), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonUbuntuServer1604LTS), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonUbuntuServer1604LTS), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonUbuntuServer1604LTS) |

> [!NOTE]
> Per visualizzare le immagini di macchine virtuali Linux di SQL Server disponibili, vedere [Panoramica di SQL Server in macchine virtuali di Azure (Windows)](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md).

## <a id="packages"></a> Pacchetti installati

Quando si configura SQL Server in Linux, si installano il pacchetto del motore di database e quindi diversi pacchetti facoltativi, in base ai requisiti specifici. Le immagini di macchine virtuali Linux per SQL Server installano automaticamente la maggior parte dei pacchetti. La tabella seguente illustra i pacchetti installati per ogni distribuzione.

| Distribuzione | [Motore di database](https://docs.microsoft.com/sql/linux/sql-server-linux-setup) | [Strumenti](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools) | [Agente SQL Server](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-sql-agent) | [Ricerca full-text](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-full-text-search) | [SSIS](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-ssis) | [Componente aggiuntivo per disponibilità elevata](https://docs.microsoft.com/sql/linux/sql-server-linux-business-continuity-dr) |
|---|---|---|---|---|---|---|
| RHEL | ![sì](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![sì](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![sì](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![sì](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![sì](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![no](./media/sql-server-linux-virtual-machines-overview/no.png) |
| SLES | ![sì](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![sì](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![sì](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![sì](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![no](./media/sql-server-linux-virtual-machines-overview/no.png) | ![no](./media/sql-server-linux-virtual-machines-overview/no.png) |
| Ubuntu | ![sì](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![sì](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![sì](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![sì](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![sì](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![sì](./media/sql-server-linux-virtual-machines-overview/yes.png) |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla configurazione e l'uso di SQL Server in Linux, vedere la [panoramica di SQL Server in Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-overview).
