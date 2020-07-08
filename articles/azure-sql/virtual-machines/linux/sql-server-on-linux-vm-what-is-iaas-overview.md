---
title: Panoramica delle SQL Server in macchine virtuali di Azure per Linux | Microsoft Docs
description: Informazioni su come eseguire edizioni complete di SQL Server in macchine virtuali di Azure per Linux. con collegamenti diretti a tutte le immagini di VM Linux di SQL Server e ai contenuti correlati.
services: virtual-machines-linux
documentationcenter: ''
author: MashaMSFT
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.workload: iaas-sql-server
ms.date: 04/10/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 721f30f5b17b078f3a3905204f6be56db25adead
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84669478"
---
# <a name="overview-of-sql-server-on-azure-virtual-machines-linux"></a>Panoramica di SQL Server in macchine virtuali di Azure (Linux)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](../windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
> * [Linux](sql-server-on-linux-vm-what-is-iaas-overview.md)

SQL Server in macchine virtuali di Azure consente di usare le versioni complete di SQL Server nel cloud senza dover gestire hardware locale. Le VM di SQL Server semplificano anche i costi di licenza quando si sceglie il modello con pagamento in base al consumo.

Le macchine virtuali di Azure vengono eseguite in diverse [aree geografiche](https://azure.microsoft.com/regions/) in tutto il mondo. Offrono anche un'ampia gamma di [dimensioni](../../../virtual-machines/windows/sizes.md). La raccolta di immagini di macchine virtuali consente di creare una VM di SQL Server con la versione, l'edizione e il sistema operativo appropriati. Le macchine virtuali possono così rappresentare una valida opzione per numerosi carichi di lavoro di SQL Server diversi. 

## <a name="get-started-with-sql-server-vms"></a><a id="create"></a>Introduzione alle macchine virtuali SQL Server

Per iniziare, scegliere un'immagine di macchina virtuale di SQL Server con la versione, l'edizione e il sistema operativo appropriati. Le sezioni seguenti forniscono collegamenti diretti al portale di Azure per le immagini della raccolta di macchine virtuali di SQL Server.

> [!TIP]
> Per altre informazioni su come comprendere i prezzi per le immagini SQL Server, vedere [la pagina dei prezzi per le macchine virtuali Linux che eseguono SQL Server](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

| Versione | Sistema operativo | Edizione |
| --- | --- | --- |
| **SQL Server 2017** | Red Hat Enterprise Linux (RHEL) 7.4 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonRedHatEnterpriseLinux74), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonRedHatEnterpriseLinux74), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonRedHatEnterpriseLinux74), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonRedHatEnterpriseLinux74), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonRedHatEnterpriseLinux74) |
| **SQL Server 2017** | SUSE Linux Enterprise Server (SLES) v12 SP2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonSLES12SP2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonSLES12SP2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonSLES12SP2), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonSLES12SP2), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonSLES12SP2) |
| **SQL Server 2017** | Ubuntu 16.04 LTS |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonUbuntuServer1604LTS), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonUbuntuServer1604LTS), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonUbuntuServer1604LTS), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonUbuntuServer1604LTS), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonUbuntuServer1604LTS) |

> [!NOTE]
> Per visualizzare le immagini di macchine virtuali SQL Server disponibili per Windows, vedere [Panoramica di SQL Server in macchine virtuali di Azure (Windows)](../windows/sql-server-on-azure-vm-iaas-what-is-overview.md).

## <a name="installed-packages"></a><a id="packages"></a> Pacchetti installati

Quando si configura SQL Server in Linux, installare il pacchetto di motore di database e quindi diversi pacchetti facoltativi a seconda delle esigenze. Le immagini di macchine virtuali Linux per SQL Server installano automaticamente la maggior parte dei pacchetti. La tabella seguente illustra i pacchetti installati per ogni distribuzione.

| Distribuzione | [Motore di database](https://docs.microsoft.com/sql/linux/sql-server-linux-setup) | [Strumenti](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools) | [Agente di SQL Server](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-sql-agent) | [Ricerca full-text](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-full-text-search) | [SSIS](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-ssis) | [Componente aggiuntivo per disponibilità elevata](https://docs.microsoft.com/sql/linux/sql-server-linux-business-continuity-dr) |
|---|---|---|---|---|---|---|
| RHEL | ![sì](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![sì](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![sì](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![sì](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![sì](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![no](./media/sql-server-on-linux-vm-what-is-iaas-overview/no.png) |
| SLES | ![sì](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![sì](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![sì](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![sì](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![no](./media/sql-server-on-linux-vm-what-is-iaas-overview/no.png) | ![no](./media/sql-server-on-linux-vm-what-is-iaas-overview/no.png) |
| Ubuntu | ![sì](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![sì](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![sì](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![sì](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![sì](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![sì](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) |

## <a name="related-products-and-services"></a>Prodotti e servizi correlati

### <a name="linux-virtual-machines"></a>Macchine virtuali Linux

* [Panoramica di macchine virtuali di Azure](../../../virtual-machines/linux/overview.md)

### <a name="storage"></a>Archiviazione

* [Introduzione ad Archiviazione di Microsoft Azure](../../../storage/common/storage-introduction.md)

### <a name="networking"></a>Rete

* [Panoramica della rete virtuale](../../../virtual-network/virtual-networks-overview.md)
* [Indirizzi IP in Azure](../../../virtual-network/public-ip-addresses.md)
* [Creare un nome di dominio completo nel portale di Azure](../../../virtual-machines/windows/portal-create-fqdn.md)

### <a name="sql"></a>SQL

* [Documentazione di SQL Server in Linux](https://docs.microsoft.com/sql/linux)
* [Confronto con il database SQL di Azure](../../azure-sql-iaas-vs-paas-what-is-overview.md)

## <a name="next-steps"></a>Passaggi successivi

Introduzione a SQL Server in Linux macchine virtuali:

* [Creare una VM di SQL Server nel portale di Azure](sql-vm-create-portal-quickstart.md)

Risposte alle domande frequenti sulle macchine virtuali SQL Server in Linux:

* [Domande frequenti su SQL Server in macchine virtuali di Azure](frequently-asked-questions-faq.md)
