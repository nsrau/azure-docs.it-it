---
title: Panoramica di SQL Server in macchine virtuali Linux di Azure | Microsoft Docs
description: Informazioni su come eseguire edizioni complete di SQL Server in macchine virtuali Linux di Azure, con collegamenti diretti a tutte le immagini di VM Linux di SQL Server e ai contenuti correlati.
services: virtual-machines-linux
documentationcenter: ''
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.workload: iaas-sql-server
ms.date: 04/10/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: c97793dc28b4ef097d8baa34678aeb92bf123809
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57778094"
---
# <a name="overview-of-sql-server-on-azure-virtual-machines-linux"></a>Panoramica di SQL Server in macchine virtuali di Azure (Linux)

> [!div class="op_single_selector"]
> * [Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)
> * [Linux](sql-server-linux-virtual-machines-overview.md)

SQL Server su macchine virtuali di Azure consente di usare versioni complete di SQL Server sul cloud senza dovere gestire eventuale hardware locale. Le VM di SQL Server semplificano anche i costi di licenza quando si sceglie il modello con pagamento in base al consumo.

Le macchine virtuali di Azure vengono eseguite in diverse [aree geografiche](https://azure.microsoft.com/regions/) in tutto il mondo. Offrono anche un'ampia gamma di [dimensioni](../sizes.md). La raccolta di immagini di macchine virtuali consente di creare una VM di SQL Server con la versione, l'edizione e il sistema operativo appropriati. Le macchine virtuali possono così rappresentare una valida opzione per numerosi carichi di lavoro di SQL Server diversi.

## <a id="create"></a> Introduzione alle macchine virtuali di SQL

Per iniziare, scegliere un'immagine di macchina virtuale di SQL Server con la versione, l'edizione e il sistema operativo appropriati. Le sezioni seguenti forniscono collegamenti diretti al portale di Azure per le immagini della raccolta di macchine virtuali di SQL Server.

> [!TIP]
> Per altre informazioni sui prezzi per le immagini di SQL, vedere la [pagina dei prezzi per macchine virtuali SQL Server Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

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
| RHEL | ![Sì](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Sì](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Sì](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Sì](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Sì](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![no](./media/sql-server-linux-virtual-machines-overview/no.png) |
| SLES | ![Sì](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Sì](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Sì](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Sì](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![no](./media/sql-server-linux-virtual-machines-overview/no.png) | ![no](./media/sql-server-linux-virtual-machines-overview/no.png) |
| Ubuntu | ![Sì](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Sì](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Sì](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Sì](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Sì](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![Sì](./media/sql-server-linux-virtual-machines-overview/yes.png) |

## <a name="related-products-and-services"></a>Prodotti e servizi correlati

### <a name="linux-virtual-machines"></a>Macchine virtuali Linux

* [Panoramica di Macchine virtuali](../overview.md)

### <a name="storage"></a>Archiviazione

* [Introduzione ad Archiviazione di Microsoft Azure](../../../storage/common/storage-introduction.md)

### <a name="networking"></a>Rete

* [Panoramica della rete virtuale](../../../virtual-network/virtual-networks-overview.md)
* [Indirizzi IP in Azure](../../../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* [Creare un nome di dominio completo nel portale di Azure](../portal-create-fqdn.md)

### <a name="sql"></a>SQL

* [Documentazione di SQL Server in Linux](https://docs.microsoft.com/sql/linux)
* [Confronto con il database SQL di Azure](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md)

## <a name="next-steps"></a>Passaggi successivi

Introduzione a SQL Server nelle macchine virtuali Linux di Azure:

* [Creare una VM di SQL Server nel portale di Azure](provision-sql-server-linux-virtual-machine.md)

Ottenere risposte alle domande frequenti sulle macchine virtuali di SQL su Linux:

* [Domande frequenti su SQL Server nelle macchine virtuali Linux di Azure](sql-server-linux-faq.md)
