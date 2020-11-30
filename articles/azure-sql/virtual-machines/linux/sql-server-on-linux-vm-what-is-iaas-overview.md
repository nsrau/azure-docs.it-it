---
title: Panoramica di SQL Server in macchine virtuali di Azure per Linux | Microsoft Docs
description: Informazioni su come eseguire le edizioni complete di SQL Server in macchine virtuali di Azure per Linux. con collegamenti diretti a tutte le immagini di VM Linux di SQL Server e ai contenuti correlati.
services: virtual-machines-linux
documentationcenter: ''
author: MashaMSFT
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: overview
ms.workload: iaas-sql-server
ms.date: 04/10/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: c394fe2aa7639d32e5d79bcb22a01151f7666f5b
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96324619"
---
# <a name="overview-of-sql-server-on-azure-virtual-machines-linux"></a>Panoramica di SQL Server in macchine virtuali di Azure (Linux)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](../windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
> * [Linux](sql-server-on-linux-vm-what-is-iaas-overview.md)

SQL Server in macchine virtuali di Azure consente di usare le versioni complete di SQL Server nel cloud senza la necessità di gestire hardware in locale. Le VM di SQL Server semplificano anche i costi di licenza quando si sceglie il modello con pagamento in base al consumo.

Le macchine virtuali di Azure vengono eseguite in diverse [aree geografiche](https://azure.microsoft.com/regions/) in tutto il mondo. Offrono anche un'ampia gamma di [dimensioni](../../../virtual-machines/sizes.md). La raccolta di immagini di macchine virtuali consente di creare una VM di SQL Server con la versione, l'edizione e il sistema operativo appropriati. Le macchine virtuali possono così rappresentare una valida opzione per numerosi carichi di lavoro di SQL Server diversi. 

Se non si ha familiarità con Azure SQL, vedere il video *Panoramica di SQL Server in macchine virtuali di Azure* della [serie di video approfonditi su Azure SQL](https://channel9.msdn.com/Series/Azure-SQL-for-Beginners?WT.mc_id=azuresql4beg_azuresql-ch9-niner):
> [!VIDEO https://channel9.msdn.com/Series/Azure-SQL-for-Beginners/SQL-Server-on-Azure-VM-Overview-4-of-61/player]

## <a name="get-started-with-sql-server-vms"></a><a id="create"></a> Introduzione alle VM di SQL Server

Per iniziare, scegliere un'immagine di macchina virtuale di SQL Server con la versione, l'edizione e il sistema operativo appropriati. Le sezioni seguenti forniscono collegamenti diretti al portale di Azure per le immagini della raccolta di macchine virtuali di SQL Server.

> [!TIP]
> Per altre informazioni sui prezzi per le immagini di SQL Server, vedere la [pagina dei prezzi per macchine virtuali Linux che eseguono SQL Server](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

| Versione | Sistema operativo | Edizione |
| --- | --- | --- |
| **SQL Server 2019** | Ubuntu 18.04 | [Enterprise](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ubuntu1804enterprise-ARM), [Standard](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ubuntu1804standard-ARM), [Web](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ubuntu1804web-ARM), [Developer](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ubuntu1804sqldev-ARM) | 
| **SQL Server 2019** | Red Hat Enterprise Linux (RHEL) 8 | [Enterprise](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-rhel8enterprise-ARM), [Standard](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-rhel8standard-ARM), [Web](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-rhel8web-ARM), [Developer](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-rhel8sqldev-ARM)|
| **SQL Server 2019** | SUSE Linux Enterprise Server (SLES) v12 SP5 | [Enterprise](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-sles12sp5enterprise-ARM), [Standard](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-sles12sp5standard-ARM), [Web](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-sles12sp5web-ARM), [Developer](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-sles12sp5sqldev-ARM)|
| **SQL Server 2017** | Red Hat Enterprise Linux (RHEL) 7.4 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonRedHatEnterpriseLinux74), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonRedHatEnterpriseLinux74), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonRedHatEnterpriseLinux74), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonRedHatEnterpriseLinux74), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonRedHatEnterpriseLinux74) |
| **SQL Server 2017** | SUSE Linux Enterprise Server (SLES) v12 SP2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonSLES12SP2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonSLES12SP2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonSLES12SP2), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonSLES12SP2), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonSLES12SP2) |
| **SQL Server 2017** | Ubuntu 16.04 LTS |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonUbuntuServer1604LTS), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonUbuntuServer1604LTS), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonUbuntuServer1604LTS), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonUbuntuServer1604LTS), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonUbuntuServer1604LTS) |

> [!NOTE]
> Per informazioni sulle immagini di macchine virtuali di SQL Server disponibili per Windows, vedere [Panoramica di SQL Server in macchine virtuali di Azure (Windows)](../windows/sql-server-on-azure-vm-iaas-what-is-overview.md).

## <a name="installed-packages"></a><a id="packages"></a> Pacchetti installati

Quando si configura SQL Server in Linux, si installano il pacchetto del motore di database e quindi diversi pacchetti facoltativi in base ai requisiti specifici. Le immagini di macchine virtuali Linux per SQL Server installano automaticamente la maggior parte dei pacchetti. La tabella seguente illustra i pacchetti installati per ogni distribuzione.

| Distribuzione | [Motore di database](/sql/linux/sql-server-linux-setup) | [Strumenti](/sql/linux/sql-server-linux-setup-tools) | [SQL Server Agent](/sql/linux/sql-server-linux-setup-sql-agent) | [Ricerca full-text](/sql/linux/sql-server-linux-setup-full-text-search) | [SSIS](/sql/linux/sql-server-linux-setup-ssis) | [Componente aggiuntivo per disponibilità elevata](/sql/linux/sql-server-linux-business-continuity-dr) |
|---|---|---|---|---|---|---|
| RHEL | ![RHEL e motore di database](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![RHEL e strumenti](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![RHEL e SQL Server Agent](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![RHEL e ricerca full-text](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![RHEL e SSIS](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![RHEL e componente aggiuntivo per la disponibilità elevata](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) |
| SLES | ![SLES e motore di database](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![SLES e strumenti](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![SLES e SQL Server Agent](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![SLES e ricerca full-text](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![SLES e SSIS](./media/sql-server-on-linux-vm-what-is-iaas-overview/no.png) | ![SLES e componente aggiuntivo per la disponibilità elevata](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png)|
| Ubuntu | ![Ubuntu e motore di database](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![Ubuntu e strumenti](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![Ubuntu e SQL Server Agent](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![Ubuntu e ricerca full-text](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![Ubuntu e SSIS](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![Ubuntu e componente aggiuntivo per la disponibilità elevata](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) |

## <a name="related-products-and-services"></a>Prodotti e servizi correlati

### <a name="linux-virtual-machines"></a>Macchine virtuali Linux

* [Panoramica delle macchine virtuali di Azure](../../../virtual-machines/linux/overview.md)

### <a name="storage"></a>Archiviazione

* [Introduzione ad Archiviazione di Microsoft Azure](../../../storage/common/storage-introduction.md)

### <a name="networking"></a>Rete

* [Panoramica della rete virtuale](../../../virtual-network/virtual-networks-overview.md)
* [Indirizzi IP in Azure](../../../virtual-network/public-ip-addresses.md)
* [Creare un nome di dominio completo nel portale di Azure](../../../virtual-machines/create-fqdn.md)

### <a name="sql"></a>SQL

* [Documentazione di SQL Server in Linux](/sql/linux)
* [Confronto con il database SQL di Azure](../../azure-sql-iaas-vs-paas-what-is-overview.md)

## <a name="next-steps"></a>Passaggi successivi

Introduzione alle macchine virtuali di SQL Server in Linux:

* [Creare una VM di SQL Server nel portale di Azure](sql-vm-create-portal-quickstart.md)

Risposte alle domande comuni sulle VM di SQL Server in Linux:

* [Domande frequenti su SQL Server in macchine virtuali di Azure](frequently-asked-questions-faq.md)