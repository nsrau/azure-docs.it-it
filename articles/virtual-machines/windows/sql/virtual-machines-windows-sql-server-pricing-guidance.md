---
title: Gestire efficacemente i costi per SQL Server nelle macchine virtuali di Azure | Documentazione Microsoft
description: Fornisce le procedure consigliate per la scelta del modello di prezzo giusto per la macchina virtuale di SQL Server.
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/17/2017
ms.author: jroth
ms.openlocfilehash: 391f30e0c81aeaf313d58f1f4af877b5be9ed919
ms.sourcegitcommit: b723436807176e17e54f226fe00e7e977aba36d5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/19/2017
---
# <a name="pricing-guidance-for-sql-server-azure-vms"></a>Guida ai prezzi per le VM di SQL Server in Azure

Questo articolo riporta indicazioni sui prezzi delle macchine virtuali di SQL Server in Azure. Sono disponibili diverse opzioni che influiscono sul costo ed è importante selezionare l'immagine giusta che consente di bilanciare i costi con i requisiti aziendali.

## <a name="free-licensed-sql-server-editions"></a>Edizioni di SQL Server con licenza gratuita

Se si desidera sviluppare, testare o compilare un modello di verifica, usare **SQL Server Developer Edition** con licenza gratuita. Questa edizione include tutte le funzionalità di SQL Server Enterprise, pertanto è possibile usarla per qualsiasi applicazione desiderata. Non è consentito usarla per l'esecuzione nell'ambiente di produzione. Per una macchina virtuale di SQL Server Developer sarà applicato solo il costo della macchina virtuale e non quello per la licenza di SQL Server.

Se si desidera eseguire un carico di lavoro leggero in fase di produzione (meno di 4 memorie centrali, meno di 1 GB di memoria, meno di 10 GB di database), usare l'edizione di **SQL Server Express** con licenza gratuita. Per una macchina virtuale di SQL Express sarà addebitato solo il costo della macchina virtuale e non quello della licenza di SQL.

Per questi carichi di lavoro di sviluppo/test o di produzione leggeri è possibile risparmiare anche scegliendo una VM più piccola ma comunque adeguata a tali carichi di lavoro. DS1v2 potrebbe essere una buona scelta per questi carichi di lavoro.

Per creare una macchina virtuale di Azure per SQL Server 2017 con una di queste immagini, vedere i collegamenti seguenti:

| Piattaforma | Immagini con licenza gratuita |
|---|---|
| Windows Server 2016 | [Macchina virtuale SQL Server 2017 Developer di Azure](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonWindowsServer2016)<br/>[Macchina virtuale SQL Server 2017 Express di Azure](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonWindowsServer2016) |
| Red Hat Enterprise Linux. | [Macchina virtuale SQL Server 2017 Developer di Azure](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonRedHatEnterpriseLinux74)<br/>[Macchina virtuale SQL Server 2017 Express di Azure](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonRedHatEnterpriseLinux74) |
| SUSE Linux Enterprise Server | [Macchina virtuale SQL Server 2017 Developer di Azure](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonSLES12SP2)<br/>[Macchina virtuale SQL Server 2017 Express di Azure](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonSLES12SP2) |
| Ubuntu | [Macchina virtuale SQL Server 2017 Developer di Azure](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonUbuntuServer1604LTS)<br/>[Macchina virtuale SQL Server 2017 Express di Azure](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonUbuntuServer1604LTS) |

## <a name="paid-sql-server-editions"></a>Edizioni di SQL Server a pagamento

Se si ha un carico di lavoro di produzione non leggero, usare una delle seguenti edizioni di SQL Server:

| Edizione di SQL Server | Carico di lavoro |
|-----|-----|
| Web | Siti Web di piccole dimensioni |
| Standard | Carichi di lavoro di piccole e medie dimensioni |
| Enterprise | Carichi di lavoro di grandi dimensioni o critici|

Sono disponibili due possibilità per il pagamento delle licenze di SQL Server per queste edizioni: *pagamento in base all'uso* o *Bring Your Own License*.

### <a name="pay-per-usage"></a>Pagamento in base all'utilizzo

**Pagamento della licenza di SQL Server in base all'utilizzo** significa che il costo al minuto per l'esecuzione della VM di Azure include il costo della licenza di SQL Server. È possibile vedere i prezzi delle diverse edizioni di SQL Server (Web, Standard, Enterprise) nella [pagina dei prezzi delle VM di Azure](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-standard). Il costo è lo stesso per tutte le versioni di SQL Server, dalla 2012 SP3 alla 2017. Come per le licenze di SQL Server in generale, il costo della licenza al minuto dipende dal numero di memorie centrali di VM.

Il pagamento della licenze di SQL Server in base all'utilizzo è consigliato per:

- Carichi di lavoro temporanei o periodici. Ad esempio un'app che deve supportare un evento per un paio di mesi all'anno o analisi di business il lunedì.
- Carichi di lavoro con durata o dimensione sconosciuta. Ad esempio un'app che potrebbe non essere necessaria per alcuni mesi o che potrebbe richiedere una maggiore o minore potenza di calcolo, in base alla richiesta.

Per creare una macchina virtuale di Azure per SQL Server 2017 con una di queste immagini con pagamento in base all'uso, vedere i collegamenti seguenti:

| Piattaforma | Immagini con licenza |
|---|---|
| Windows Server 2016 | [Macchina virtuale SQL Server 2017 Web di Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonWindowsServer2016)<br/>[Macchina virtuale SQL Server 2017 Standard di Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonWindowsServer2016)<br/>[Macchina virtuale SQL Server 2017 Enterprise di Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseWindowsServer2016) |
| Red Hat Enterprise Linux. | [Macchina virtuale SQL Server 2017 Web di Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonRedHatEnterpriseLinux74)<br/>[Macchina virtuale SQL Server 2017 Standard di Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonRedHatEnterpriseLinux74)<br/>[Macchina virtuale SQL Server 2017 Enterprise di Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonRedHatEnterpriseLinux74) |
| SUSE Linux Enterprise Server | [Macchina virtuale SQL Server 2017 Web di Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonSLES12SP2)<br/>[Macchina virtuale SQL Server 2017 Standard di Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonSLES12SP2)<br/>[Macchina virtuale SQL Server 2017 Enterprise di Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonSLES12SP2) |
| Ubuntu | [Macchina virtuale SQL Server 2017 Web di Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonUbuntuServer1604LTS)<br/>[Macchina virtuale SQL Server 2017 Standard di Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonUbuntuServer1604LTS)<br/>[Macchina virtuale SQL Server 2017 Enterprise di Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonUbuntuServer1604LTS) |

> [!IMPORTANT]
> Quando si crea una macchina virtuale di SQL Server nel portale la finestra **Scegli una dimensione** mostra il costo stimato. È importante notare che questa stima è solo il calcolo dei costi per l'esecuzione della macchina virtuale con altri costi di licenza Windows per le macchine virtuali di Windows. Non include i costi di licenza aggiuntivi di SQL Server per le versioni Enterprise, Standard e Web. Non include eventuali costi aggiuntivi di licenza associati ai sistemi operativi Linux di terze parti per le macchine virtuali Linux. Per ottenere una stima di prezzo più accurata, selezionare il sistema operativo e la versione di SQL Server nella pagina dei prezzi per [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) e [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).
>
> ![Scegliere il pannello Dimensioni macchina virtuale](./media/virtual-machines-windows-sql-server-pricing-guidance/sql-vm-choose-size-pricing-estimate.png)

### <a name="bring-your-own-license-byol"></a>Bring Your Own License (BYOL)

**Bringing Your Own License per SQL Server attraverso Mobilità delle licenze**, o **BYOL**, indica l'utilizzo di un contratto multilicenza di SQL Server esistente con Software Assurance in una VM di Azure. Per una macchina virtuale di SQL Server che usa l'opzione BYOL sarà addebitato solo il costo di esecuzione della macchina virtuale e non la licenza di SQL Server, dato che le licenze e Software Assurance sono già stati acquisiti tramite un programma di contratto multilicenza.

> [!NOTE]
> Le immagini BYOL sono attualmente disponibili solo per le macchine virtuali Windows. È tuttavia possibile installare manualmente SQL Server in una macchina virtuale solo Linux. Vedere le linee guida nelle [domande frequenti sulla macchina virtuale SQL di Linux](../../linux/sql/sql-server-linux-faq.md).

Bringing Your Own License per SQL Server attraverso Mobilità delle licenze è consigliato per:

- Carichi di lavoro continui. Ad esempio un'app che deve supportare operazioni aziendali 24/7.
- Carichi di lavoro con dimensione e durata note. Ad esempio un'app che sarà necessaria per l'intero anno e la cui domanda è stata prevista.

Per usare l'opzione BYOL con una macchina virtuale di SQL Server è necessario avere una licenza di SQL Server Standard o Enterprise e [Software Assurance](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-default.aspx#tab=1), che è un'opzione obbligatoria per alcuni programmi di [contratti multilicenza](https://www.microsoft.com/en-us/download/details.aspx?id=10585) e facoltativa per altri.  I livelli di prezzi forniti tramite i programmi multilicenza variano in base al tipo di contratto e alla quantità e/o all'impegno con SQL Server. Ma, come regola generale, l'opzione Bringing Your Own License per i carichi di lavoro di produzione continui presenta i seguenti vantaggi:

| Vantaggio dell'opzione BYOL | Descrizione |
|-----|-----|
| **Risparmi sui costi** | Bringing Your Own License per SQL Server è più conveniente rispetto al pagamento in base all'uso se un carico di lavoro eseguirà continuativamente SQL Server Standard o Enterprise per *più di 10 mesi*. |
| **Risparmi a lungo termine** | In media, acquistare o rinnovare una licenza di SQL Server per i primi 3 anni comporta un *risparmio del 30% all'anno*. Inoltre, dopo 3 anni, non è più necessario rinnovare la licenza e si paga solo Software Assurance. A quel punto comporta un *risparmio del 200%*. |
| **Replica secondaria passiva gratuita** | Un altro vantaggio dell'opzione Bring Your Own License è la [licenza gratuita per una replica secondaria passiva](https://azure.microsoft.com/pricing/licensing-faq/) per SQL Server per scopi di disponibilità elevata. Questo dimezza il costo della licenza di una distribuzione di SQL Server a disponibilità elevata, ad esempio con gruppi di disponibilità Always On. I diritti per eseguire la replica secondaria passiva sono forniti attraverso i vantaggi di Software Assurance per i server di failover. |

Per creare una VM di Azure con SQL Server 2016 con una di queste immagini Bring Your Own License, vedere le VM con il prefisso "{BYOL}":

- [VM SQL Server 2016 Enterprise di Azure](https://ms.portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP1EnterpriseWindowsServer2016)
- [VM SQL Server 2016 Standard di Azure](https://ms.portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP1StandardWindowsServer2016)

> [!NOTE]
> Si prega di comunicare entro 10 giorni il numero di licenze di SQL Server che si useranno in Azure. Nei collegamenti alle immagini precedenti sono incluse le istruzioni su come eseguire questa operazione.

## <a name="avoid-unnecessary-costs"></a>Evitare i costi non necessari

Per evitare i costi non necessari, scegliere una dimensione di macchina virtuale ottimale e considerare gli arresti intermittenti per carichi di lavoro non continui.

### <a id="machinesize"></a> Gestisce correttamente le dimensioni della macchina virtuale

I costi di licenza di SQL Server sono direttamente correlati al numero di core. Scegliere le dimensioni della macchina virtuale che soddisfano le esigenze previste per CPU, memoria, archiviazione e della larghezza di banda di I/O. Per un elenco completo delle opzioni relative alle dimensioni del computer, vedere [Dimensioni per le macchine virtuali Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) e [Dimensioni per le macchine virtuali Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Sono disponibili nuove dimensioni per i computer che funzionano bene con determinati tipi di carichi di lavoro di SQL Server. Queste dimensioni di computer mantengono livelli elevati di memoria, archiviazione e larghezza di banda di I/O, ma hanno un basso numero di core virtualizzati. Ad esempio, si consideri l'esempio seguente:

| Dimensioni macchina virtuale | vCPU | Memoria | Numero massimo di dischi | Massima velocità effettiva di I/O | Costi di licenza di SQL | Costi totali (calcolo + licenze) |
|---|---|---|---|---|---|---|
| **Standard_DS14v2** | 16 | 112 GB | 32 | 51.200 operazioni di I/O al secondo o 768 MB/s | | |
| **Standard_DS14-4v2** | 4 | 112 GB | 32 | 51.200 operazioni di I/O al secondo o 768 MB/s | 75% in meno | 57% in meno |

> [!IMPORTANT]
> Questo è un esempio temporizzato. Per le specifiche tecniche più recenti, vedere gli articoli sulle dimensioni per i computer e la pagina dei prezzi di Azure per [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) e [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

Nell'esempio precedente è possibile vedere che le specifiche per **Standard_DS14v2** e **Standard_DS14-4v2** sono identiche ad eccezione di quelle per la CPU virtuale. Il suffisso **-4v2** alla fine della dimensione del computer **Standard_DS14-4v2** indica il numero di CPU virtuali attive. Poiché i costi di licenza di SQL Server sono collegati al numero di core, questo riduce notevolmente il costo della macchina virtuale in scenari in cui non sono necessari. CPU virtuali aggiuntive. Questo è un esempio, ma ci sono diverse dimensioni per i computer con CPU virtuali vincolate identificate con questo modello di suffisso. Per altre informazioni, vedere il post di blog [Annuncio delle nuove dimensioni per le macchine virtuali di Azure per operazioni sul database più convenienti](https://azure.microsoft.com/blog/announcing-new-azure-vm-sizes-for-more-cost-effective-database-workloads/).

### <a name="shut-down-your-vm-when-possible"></a>Arrestare la macchina virtuale quando possibile

Se si usano carichi di lavoro che non vengono eseguiti in modo continuativo, è consigliabile arrestare la macchina virtuale durante i periodi di inattività. Si paga solo per le risorse utilizzate.

Ad esempio, se si sta semplicemente provando SQL Server in una VM di Azure, evitare che rimanga in esecuzione per settimane per non incorrere in addebiti inutili. Una possibile soluzione è usare la [funzionalità di arresto automatico](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/).

![Arresto automatico della VM di SQL](./media/virtual-machines-windows-sql-server-pricing-guidance/sql-vm-auto-shutdown.png)

L'arresto automatico fa parte di un insieme più ampio di funzionalità simili fornite da [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab).

Per altri flussi di lavoro, prendere in considerazione l'arresto e il riavvio automatico delle macchine virtuali con una soluzione script come [Automazione di Azure](https://azure.microsoft.com/services/automation/).

> [!IMPORTANT]
> Arrestare e deallocare la VM è l'unico modo per evitare addebiti. La semplice interruzione o il semplice uso delle opzioni di risparmio energia per arrestare la VM non sospende l'addebito per l'utilizzo.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni di guida generali sui prezzi di Azure, vedere [Evitare costi imprevisti con la gestione dei costi e alla fatturazione di Azure](../../../billing/billing-getting-started.md).

Per le informazioni più recenti sui prezzi delle macchine virtuali, tra cui SQL Server, vedere la [pagina dei prezzi delle VM di Azure](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-standard).

Altre informazioni sulle macchine virtuali di SQL Server sia per [macchine virtuali Windows di SQL Server](virtual-machines-windows-sql-server-iaas-overview.md) che per [macchine virtuali Linux di SQL Server](../../linux/sql/sql-server-linux-virtual-machines-overview.md).
