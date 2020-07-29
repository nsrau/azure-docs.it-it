---
title: Panoramica di SQL Server nelle macchine virtuali Windows di Azure | Microsoft Docs
description: Informazioni su come eseguire le edizioni complete di SQL Server in Macchine virtuali di Azure.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
tags: azure-service-management
ms.assetid: c505089e-6bbf-4d14-af0e-dd39a1872767
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/27/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: f1786123808371601467926c2a114c676e898330
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84668628"
---
# <a name="what-is-sql-server-on-azure-virtual-machines-windows"></a>Informazioni sulle SQL Server in macchine virtuali di Azure (Windows)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
> * [Linux](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)

[SQL Server in macchine virtuali di Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/) consente di usare le versioni complete di SQL Server nel cloud senza dover gestire hardware locale. SQL Server macchine virtuali (VM) semplificano anche i costi di licenza quando si paga in base al consumo.

Le macchine virtuali di Azure vengono eseguite in diverse [aree geografiche](https://azure.microsoft.com/regions/) in tutto il mondo. Offrono anche un'ampia gamma di [dimensioni](../../../virtual-machines/windows/sizes.md). La raccolta di immagini di macchine virtuali consente di creare una VM di SQL Server con la versione, l'edizione e il sistema operativo appropriati. In questo modo le macchine virtuali sono un'opzione ideale per molti carichi di lavoro SQL Server diversi.

## <a name="automated-updates"></a>Aggiornamenti automatici

SQL Server in macchine virtuali di Azure possono usare l'applicazione [automatica delle patch](automated-patching.md) per pianificare una finestra di manutenzione per l'installazione automatica di importanti aggiornamenti di windows e SQL Server.

## <a name="automated-backups"></a>Backup automatizzati

SQL Server in macchine virtuali di Azure può avvalersi del [backup automatico](automated-backup.md), che consente di creare periodicamente backup del database nell'archivio BLOB. È anche possibile usare questa tecnica manualmente. Per altre informazioni, vedere [Usare Archiviazione di Azure per il backup e il ripristino di SQL Server](azure-storage-sql-server-backup-restore-use.md).

Azure offre anche una soluzione di backup di livello aziendale per SQL Server in esecuzione in macchine virtuali di Azure. Si tratta di una soluzione di backup completamente gestita che supporta gruppi di disponibilità Always On, conservazione a lungo termine, recupero temporizzato e gestione e monitoraggio centralizzati. Per altre informazioni, vedere [backup di Azure per SQL Server in macchine virtuali di Azure](https://docs.microsoft.com/azure/backup/backup-azure-sql-database).
  

## <a name="high-availability"></a>Disponibilità elevata

Se è necessaria la disponibilità elevata, è consigliabile configurare i gruppi di disponibilità di SQL Server. Questa operazione riguarda più istanze di SQL Server in macchine virtuali di Azure in una rete virtuale. È possibile configurare la soluzione a disponibilità elevata manualmente oppure è possibile usare i modelli nel portale di Azure per la configurazione automatica. Per una panoramica di tutte le opzioni di disponibilità elevata, vedere [disponibilità elevata e ripristino di emergenza per SQL Server in macchine virtuali di Azure](business-continuity-high-availability-disaster-recovery-hadr-overview.md).

## <a name="performance"></a>Prestazioni

Le macchine virtuali di Azure sono disponibili in più dimensioni per soddisfare le diverse esigenze di carico di lavoro. SQL Server VM forniscono anche la configurazione di archiviazione automatica, ottimizzata in modo da soddisfare i requisiti di prestazioni. Per altre informazioni sulla configurazione dell'archiviazione per le macchine virtuali SQL Server, vedere [configurazione dell'archiviazione per le vm SQL Server](storage-configuration.md). Per ottimizzare le prestazioni, vedere le procedure consigliate [per le prestazioni per SQL Server in macchine virtuali di Azure](performance-guidelines-best-practices.md).

## <a name="get-started-with-sql-server-vms"></a>Introduzione alle macchine virtuali SQL Server

Per iniziare, scegliere un'immagine di macchina virtuale di SQL Server con la versione, l'edizione e il sistema operativo appropriati. Le sezioni seguenti forniscono collegamenti diretti al portale di Azure per le immagini della raccolta di macchine virtuali di SQL Server.

> [!TIP]
> Per altre informazioni su come comprendere i prezzi per le immagini SQL Server, vedere [linee guida sui prezzi per SQL Server in macchine virtuali di Azure](pricing-guidance.md). 

### <a name="pay-as-you-go"></a><a id="payasyougo"></a> Pagamento in base al consumo
La tabella seguente fornisce una matrice delle immagini di SQL Server con pagamento in base al consumo.

| Versione | Sistema operativo | Edizione |
| --- | --- | --- |
| **SQL Server 2019** | Windows Server 2019 | [Enterprise](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019enterprise), [Standard](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019standard), [Web](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019web), [Developer](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019sqldev) | 
| **SQL Server 2017** |Windows Server 2016 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseWindowsServer2016), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonWindowsServer2016), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonWindowsServer2016), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonWindowsServer2016), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonWindowsServer2016) |
| **SQL Server 2016 SP2** |Windows Server 2016 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2016SP2EnterpriseWindowsServer2016), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2016SP2StandardWindowsServer2016), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2016SP2WebWindowsServer2016), [Express](https://portal.azure.com/#create/Microsoft.FreeLicenseSQLServer2016SP2ExpressWindowsServer2016), [Developer](https://portal.azure.com/#create/Microsoft.FreeLicenseSQLServer2016SP2DeveloperWindowsServer2016) |
| **SQL Server 2014 SP2** |R2 per Windows Server 2012 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2ExpressWindowsServer2012R2) |
| **SQL Server 2012 SP4** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4ExpressWindowsServer2012R2) |
| **SQL Server 2008 R2 SP3** |Windows Server 2008 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3EnterpriseWindowsServer2008R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3StandardWindowsServer2008R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3WebWindowsServer2008R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3ExpressWindowsServer2008R2) |

Per visualizzare le immagini di macchine virtuali SQL Server in Linux disponibili, vedere [Panoramica di SQL Server in macchine virtuali di Azure (Linux)](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md).

> [!NOTE]
> Per usare la licenza personale, è ora possibile modificare il modello di licenza di una macchina virtuale di SQL Server con pagamento in base all'utilizzo. Per altre informazioni, vedere [Come cambiare il livello di licenza per una macchina virtuale SQL Server](licensing-model-azure-hybrid-benefit-ahb-change.md). 

### <a name="bring-your-own-license"></a><a id="BYOL"></a> Bring Your Own License
È anche possibile scegliere l'opzione Bring Your Own License (BYOL). In questo scenario si paga solo per la VM senza altre spese per le licenze di SQL Server.  L'opzione Bring Your Own License consente di risparmiare denaro nel tempo per i carichi di lavoro di produzione continui. Per i requisiti per questa opzione, vedere [Guida ai prezzi per le VM di SQL Server in Azure](pricing-guidance.md#byol).

Per usare la propria licenza, è possibile convertire una macchina virtuale SQL Server con pagamento in base all'utilizzo esistente oppure è possibile distribuire un'immagine con il prefisso **{BYOL}**. Per altre informazioni su come cambiare il modello di gestione delle licenze tra i costi di utilizzo e BYOL, vedere [How to change the licensing model for a SQL Server VM](licensing-model-azure-hybrid-benefit-ahb-change.md). 

| Versione | Sistema operativo | Edizione |
| --- | --- | --- |
| **SQL Server 2019** | Windows Server 2019 | [Enterprise BYOL](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019-byolenterprise), [Standard BYOL](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019-byolstandard)| 
| **SQL Server 2017** |Windows Server 2016 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017EnterpriseWindowsServer2016), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017StandardonWindowsServer2016) |
| **SQL Server 2016 SP2** |Windows Server 2016 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP2EnterpriseWindowsServer2016), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP2StandardWindowsServer2016) |
| **SQL Server 2014 SP2** |R2 per Windows Server 2012 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2EnterpriseWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2StandardWindowsServer2012R2) |
| **SQL Server 2012 SP4** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP4EnterpriseWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP4StandardWindowsServer2012R2) |

È possibile distribuire un'immagine meno recente di SQL Server che non è disponibile nel portale di Azure usando PowerShell. Per visualizzare tutte le immagini disponibili tramite PowerShell, utilizzare il comando seguente:

  ```powershell
  Get-AzVMImageOffer -Location $Location -Publisher 'MicrosoftSQLServer'
  ```

Per altre informazioni sulla distribuzione di macchine virtuali di SQL Server usando PowerShell, vedere [Come eseguire il provisioning di macchine virtuali di SQL Server con Azure PowerShell](create-sql-vm-powershell.md).


### <a name="connect-to-the-vm"></a>Connettersi alla VM
Una volta creata la VM di SQL Server, stabilire la connessione con applicazioni o strumenti come SQL Server Management Studio (SSMS). Per istruzioni, vedere [connettersi a una macchina virtuale SQL Server in Azure](ways-to-connect-to-sql.md).

### <a name="migrate-your-data"></a>Migrare i dati
Se si dispone di un database esistente, è opportuno spostarlo nella macchina virtuale di SQL Server appena sottoposta a provisioning. Per le linee guida e un elenco di opzioni per la migrazione, vedere [Eseguire la migrazione di un database di SQL Server a SQL Server in una macchina virtuale di Azure](migrate-to-vm-from-sql-server.md).

## <a name="create-and-manage-azure-sql-resources-with-the-azure-portal"></a>Creare e gestire risorse SQL di Azure con il portale di Azure

Il portale di Azure offre un'unica pagina in cui è possibile gestire [tutte le risorse SQL di Azure](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Sql%2Fazuresql), incluse le macchine virtuali SQL.

Per accedere alla pagina delle **risorse SQL di Azure**, selezionare **SQL di Azure** nel menu del portale di Azure oppure cercare e selezionare **SQL di Azure** da qualsiasi pagina.

![Ricerca di SQL di Azure](./media/sql-server-on-azure-vm-iaas-what-is-overview/search-for-azure-sql.png)

> [!NOTE]
> SQL di Azure offre un modo semplice e rapido per accedere a tutti i database SQL, i pool elastici, i server logici, le istanze gestite e le macchine virtuali di Azure. SQL di Azure non è un servizio o una risorsa. 

Per gestire le risorse esistenti, selezionare un elemento nell'elenco. Per creare nuove risorse SQL di Azure, selezionare **+ Aggiungi**. 

![Creare una risorsa SQL di Azure](./media/sql-server-on-azure-vm-iaas-what-is-overview/create-azure-sql-resource.png)

Dopo aver selezionato **+ Aggiungi**, per visualizzare informazioni aggiuntive sulle diverse opzioni, selezionare **Mostra dettagli** in qualsiasi riquadro.

![Dettagli riquadro database](./media/sql-server-on-azure-vm-iaas-what-is-overview/sql-vm-details.png)

Per informazioni dettagliate, vedere:

- [Creare un database singolo](../../database/single-database-create-quickstart.md)
- [Creare un pool elastico](../../database/elastic-pool-overview.md#creating-a-new-sql-database-elastic-pool-using-the-azure-portal)
- [Creare un'istanza gestita](../../managed-instance/instance-create-quickstart.md)
- [Creare una singola macchina virtuale SQL Server](sql-vm-create-portal-quickstart.md)

## <a name="sql-server-vm-image-refresh-policy"></a><a id="lifecycle"></a>SQL Server criteri di aggiornamento dell'immagine di macchina virtuale
Azure gestisce una sola immagine di macchina virtuale per ogni combinazione supportata di sistema operativo, versione ed edizione. Ciò significa che le immagini vengono aggiornate nel tempo e quelle meno recenti vengono rimosse. Per altre informazioni, vedere la sezione **Immagini** delle [domande frequenti sulle macchine virtuali SQL Server](frequently-asked-questions-faq.md#images).

## <a name="customer-experience-improvement-program-ceip"></a>Analisi utilizzo software
Il programma Analisi utilizzo software è abilitato per impostazione predefinita. Invia periodicamente report a Microsoft per contribuire a migliorare SQL Server. Non sono necessarie attività di gestione per il programma Analisi utilizzo software, a meno che non lo si voglia disabilitare dopo il provisioning. È possibile personalizzare o disabilitare Analisi utilizzo software connettendosi alla VM con Desktop remoto. Eseguire quindi l'utilità **Segnalazione errori e utilizzo funzionalità di SQL Server** . Seguire le istruzioni per disabilitare il reporting. Per altre informazioni sulla raccolta di dati, vedere l'[informativa sulla privacy di SQL Server](https://docs.microsoft.com/sql/getting-started/microsoft-sql-server-privacy-statement).

## <a name="related-products-and-services"></a>Prodotti e servizi correlati
### <a name="windows-virtual-machines"></a>Macchine virtuali Windows
* [Panoramica di macchine virtuali di Azure](../../../virtual-machines/windows/overview.md)

### <a name="storage"></a>Archiviazione
* [Introduzione ad Archiviazione di Microsoft Azure](../../../storage/common/storage-introduction.md)

### <a name="networking"></a>Rete
* [Panoramica della rete virtuale](../../../virtual-network/virtual-networks-overview.md)
* [Indirizzi IP in Azure](../../../virtual-network/public-ip-addresses.md)
* [Creare un nome di dominio completo nel portale di Azure](../../../virtual-machines/linux/portal-create-fqdn.md)

### <a name="sql"></a>SQL
* [Documentazione di SQL Server](https://docs.microsoft.com/sql/index)
* [Confronto con il database SQL di Azure](../../azure-sql-iaas-vs-paas-what-is-overview.md)

## <a name="next-steps"></a>Passaggi successivi

Introduzione all'uso di SQL Server nelle macchine virtuali di Azure:

* [Creare una VM di SQL Server nel portale di Azure](sql-vm-create-portal-quickstart.md)

Risposte alle domande comuni sulle VM di SQL Server:

* [Domande frequenti su SQL Server in macchine virtuali di Azure](frequently-asked-questions-faq.md)

Visualizzare le architetture di riferimento per l'esecuzione di applicazioni a più livelli in SQL Server in IaaS

* [Applicazione a più livelli Windows in Azure con SQL Server](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/n-tier-sql-server)
* [Eseguire un'applicazione a più livelli in più aree di Azure per una disponibilità elevata](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/multi-region-sql-server)
