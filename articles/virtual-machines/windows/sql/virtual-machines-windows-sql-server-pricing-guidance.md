---
title: Gestire efficacemente i costi per SQL Server nelle macchine virtuali di Azure | Documentazione Microsoft
description: Fornisce le procedure consigliate per la scelta del modello di prezzo giusto per la macchina virtuale di SQL Server.
services: virtual-machines-windows
documentationcenter: na
author: luisherring
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 04/18/2017
ms.author: jroth
translationtype: Human Translation
ms.sourcegitcommit: abdbb9a43f6f01303844677d900d11d984150df0
ms.openlocfilehash: 29a92f0c70bffedeb75c50b7fc3b687ee5ee227d
ms.lasthandoff: 04/21/2017


---
# <a name="pricing-guidance-for-sql-server-azure-vms"></a>Guida ai prezzi per le VM di SQL Server in Azure

Questo argomento riporta indicazioni sui prezzi delle macchine virtuali di SQL Server in Azure. Sono disponibili diverse opzioni che influiscono sul costo ed è importante selezionare l'immagine giusta che consente di bilanciare i costi con i requisiti aziendali.

## <a name="free-licensed-sql-server-editions"></a>Edizioni di SQL Server con licenza gratuita

Se si desidera sviluppare, testare o compilare un modello di verifica, usare **SQL Server Developer Edition** con licenza gratuita. Questa edizione include tutte le funzionalità di SQL Server Enterprise, pertanto è possibile usarla per qualsiasi applicazione desiderata. Non è consentito usarla per l'esecuzione nell'ambiente di produzione. Per una VM di SQL Server Developer sarà applicato solo il costo della VM e non quello per la licenza di SQL Server.

Se si desidera eseguire un carico di lavoro leggero in fase di produzione (meno di 4 memorie centrali, meno di 1 GB di memoria, meno di 10 GB di database), usare l'edizione di **SQL Server Express** con licenza gratuita. Per una VM di SQL Express sarà addebitato solo il costo della VM e non quello della licenza di SQL.

Per questi carichi di lavoro di sviluppo/test o di produzione leggeri è possibile risparmiare anche scegliendo una VM più piccola ma comunque adeguata a tali carichi di lavoro. DS1v2 potrebbe essere una buona scelta per questi carichi di lavoro.

Per creare una VM di Azure per SQL Server 2016 con una di queste immagini, vedere i collegamenti seguenti:

- [VM SQL Server 2016 Developer di Azure](https://ms.portal.azure.com/#create/Microsoft.FreeLicenseSQLServer2016SP1DeveloperWindowsServer2016-ARM)
- [VM SQL Server 2016 Express di Azure](https://ms.portal.azure.com/#create/Microsoft.FreeLicenseSQLServer2016SP1ExpressWindowsServer2016-ARM)

## <a name="paid-sql-server-editions"></a>Edizioni di SQL Server a pagamento

Se si ha un carico di lavoro di produzione non leggero, usare una delle seguenti edizioni di SQL Server:

| Edizione di SQL Server | Carico di lavoro |
|-----|-----|
| Web | Siti Web di piccole dimensioni |
| Standard | Carichi di lavoro di piccole e medie dimensioni |
| Enterprise | Carichi di lavoro di grandi dimensioni o critici|

Sono disponibili due possibilità per il pagamento delle licenze di SQL Server per queste edizioni: *pagamento in base all'utilizzo* o *Bring Your Own License*.

### <a name="pay-per-usage"></a>Pagamento in base all'utilizzo

**Pagamento della licenza di SQL Server in base all'utilizzo** significa che il costo al minuto per l'esecuzione della VM di Azure include il costo della licenza di SQL Server. È possibile vedere i prezzi delle diverse edizioni di SQL Server (Web, Standard, Enterprise) nella [pagina dei prezzi delle VM di Azure](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-standard). Il costo è lo stesso per tutte le versioni di SQL Server (dalla 2008 R2 alla 2016). Come per le licenze di SQL Server in generale, il costo della licenza al minuto dipende dal numero di memorie centrali di VM.

Il pagamento della licenze di SQL Server in base all'utilizzo è consigliato per:

- Carichi di lavoro temporanei o periodici. Ad esempio un'app che deve supportare un evento per un paio di mesi all'anno o analisi di business il lunedì.
- Carichi di lavoro con durata o dimensione sconosciuta. Ad esempio un'app che potrebbe non essere necessaria per alcuni mesi o che potrebbe richiedere una maggiore o minore potenza di calcolo, in base alla richiesta.

Per creare una VM di Azure per SQL Server 2016 con una di queste immagini con pagamento in base all'utilizzo, vedere i collegamenti seguenti:

- [VM SQL Server 2016 Web di Azure](https://ms.portal.azure.com/#create/Microsoft.SQLServer2016SP1WebWindowsServer2016)
- [VM SQL Server 2016 Standard di Azure](https://ms.portal.azure.com/#create/Microsoft.SQLServer2016SP1StandardWindowsServer2016)
- [VM SQL Server 2016 Enterprise di Azure](https://ms.portal.azure.com/#create/Microsoft.SQLServer2016SP1EnterpriseWindowsServer2016)

> [!IMPORTANT]
> Quando si crea una macchina virtuale di SQL Server nel portale di Azure, il costo mensile stimato visualizzato nel pannello **Scegli una dimensione** non include i costi di licenza di SQL Server. ma solo il costo della VM.
>
> ![Scegliere il pannello Dimensioni macchina virtuale](./media/virtual-machines-windows-sql-server-pricing-guidance/sql-vm-choose-size-pricing-estimate.png)
>
>Per le edizioni Express e Developer con licenza gratuita di SQL Server, si tratta del costo stimato totale. Per le edizioni Web, Standard ed Enterprise, individuare invece i costi delle licenze SQL aggiuntive nella [pagina relativa ai prezzi delle VM Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). Nella pagina dei prezzi selezionare l'edizione desiderata di SQL Server.

### <a name="bring-your-own-license-byol"></a>Bring Your Own License (BYOL)

**Bringing Your Own License per SQL Server attraverso Mobilità delle licenze**, o **BYOL**, indica l'utilizzo di un contratto multilicenza di SQL Server esistente con Software Assurance in una VM di Azure. Per una VM di SQL Server che usa l'opzione BYOL sarà addebitato solo il costo di esecuzione della VM e non la licenza di SQL Server, dato che le licenze e Software Assurance sono già stati acquisiti tramite un programma di contratto multilicenza.

Bringing Your Own License per SQL Server attraverso Mobilità delle licenze è consigliato per:

- Carichi di lavoro continui. Ad esempio un'app che deve supportare operazioni aziendali 24/7.
- Carichi di lavoro con dimensione e durata note. Ad esempio un'app che sarà necessaria per l'intero anno e la cui domanda è stata prevista.

Per usare l'opzione BYOL con una macchina virtuale di SQL Server è necessario avere una licenza di SQL Server Standard o Enterprise e [Software Assurance](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-default.aspx#tab=1), che è un'opzione obbligatoria per alcuni programmi di [contratti multilicenza](https://www.microsoft.com/en-us/download/details.aspx?id=10585) e facoltativa per altri.  I livelli di prezzi forniti tramite programmi multilicenza variano in base al tipo di contratto e alla quantità e/o all'impegno con SQL Server. Ma, come regola generale, l'opzione Bringing Your Own License per i carichi di lavoro di produzione continui presenta i seguenti vantaggi:

| Vantaggio dell'opzione BYOL | Descrizione |
|-----|-----|
| **Risparmi sui costi** | Bringing Your Own License per SQL Server è più conveniente rispetto al pagamento in base all'utilizzo se un carico di lavoro eseguirà continuativamente SQL Server Standard o Enterprise per *più di 10 mesi*. |
| **Risparmi a lungo termine** | In media, acquistare o rinnovare una licenza di SQL Server per i primi 3 anni comporta un *risparmio del 30% all'anno*. Inoltre, dopo 3 anni, non è più necessario rinnovare la licenza e si paga solo Software Assurance. A quel punto comporta un *risparmio del 200%*. |
| **Replica secondaria passiva gratuita** | Un altro vantaggio dell'opzione Bring Your Own License è la [licenza gratuita per una replica secondaria passiva](https://azure.microsoft.com/pricing/licensing-faq/) per SQL Server per scopi di disponibilità elevata. Questo dimezza il costo della licenza di una distribuzione di SQL Server a disponibilità elevata (ad esempio con gruppi di disponibilità AlwaysOn). I diritti per eseguire la replica secondaria passiva sono forniti attraverso i vantaggi di Software Assurance per i server di failover. |

Per creare una VM di Azure con SQL Server 2016 con una di queste immagini Bring Your Own License, vedere le VM con il prefisso "{BYOL}":

- [VM SQL Server 2016 Enterprise di Azure](https://ms.portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP1EnterpriseWindowsServer2016)
- [VM SQL Server 2016 Standard di Azure](https://ms.portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP1StandardWindowsServer2016)

> [!NOTE]
> Si prega di comunicare entro 10 giorni il numero di licenze di SQL Server che si useranno in Azure. Nei collegamenti alle immagini precedenti sono incluse le istruzioni su come eseguire questa operazione.

## <a name="avoid-unecessary-costs"></a>Evitare i costi non necessari

Se si usano carichi di lavoro che non vengono eseguiti in modo continuativo, è consigliabile arrestare la macchina virtuale durante i periodi di inattività. Si paga solo per le risorse utilizzate.

Ad esempio, se si sta semplicemente provando SQL Server in una VM di Azure, evitare che rimanga in esecuzione per settimane per non incorrere in addebiti inutili. Una possibile soluzione è usare la [funzionalità di arresto automatico](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/).

![Arresto automatico della VM di SQL](./media/virtual-machines-windows-sql-server-pricing-guidance/sql-vm-auto-shutdown.png)

L'arresto automatico fa parte di un insieme più ampio di funzionalità simili fornite da [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab).

Per altri flussi di lavoro, prendere in considerazione l'arresto e il riavvio automatico delle VM con una soluzione script come [Automazione di Azure](https://azure.microsoft.com/services/automation/).

> [!IMPORTANT]
> Arrestare e deallocare la VM è l'unico modo per evitare addebiti. La semplice interruzione o il semplice uso delle opzioni di risparmio energia per arrestare la VM non sospende l'addebito per l'utilizzo.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni di guida generali sui prezzi di Azure, vedere [Evitare costi imprevisti con la gestione dei costi e alla fatturazione di Azure](../../../billing/billing-getting-started.md).

Per le informazioni più recenti sui prezzi delle macchine virtuali, tra cui SQL Server, vedere la [pagina dei prezzi delle VM di Azure](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-standard).

Esaminare altri argomenti relativi alle macchine virtuali di SQL Server in [Panoramica di SQL Server in Macchine virtuali di Azure](virtual-machines-windows-sql-server-iaas-overview.md).

