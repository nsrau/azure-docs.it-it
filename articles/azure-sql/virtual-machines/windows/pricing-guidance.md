---
title: Guida ai prezzi e gestione dei costi
description: Fornisce le procedure consigliate per la scelta del modello di prezzo giusto per la macchina virtuale di SQL Server.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/09/2018
ms.author: mathoma
ms.reviewer: jroth
ms.custom: seo-lt-2019
ms.openlocfilehash: 57e9c82e5685171cff994aca7985f6a4211f00e7
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96327288"
---
# <a name="pricing-guidance-for-sql-server-on-azure-vms"></a>Linee guida sui prezzi per SQL Server nelle macchine virtuali di Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Questo articolo fornisce indicazioni sui prezzi per [SQL Server in macchine virtuali di Azure](sql-server-on-azure-vm-iaas-what-is-overview.md). Sono disponibili diverse opzioni che influiscono sul costo ed è importante selezionare l'immagine giusta che consente di bilanciare i costi con i requisiti aziendali.

> [!TIP]
> Se è sufficiente trovare una stima dei costi per una combinazione specifica di SQL Server Edition e delle dimensioni della macchina virtuale (VM), vedere la pagina dei prezzi per [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows) o [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux). Selezionare la piattaforma e l'edizione di SQL Server dall'elenco **Sistema operativo/Software**.
>
> ![Interfaccia utente nella pagina dei prezzi di Macchine virtuali](./media/pricing-guidance/virtual-machines-pricing-ui.png)
>
> In alternativa, usare il [Calcolatore prezzi](https://azure.microsoft.com/pricing/#explore-cost) per aggiungere e configurare una macchina virtuale. 

## <a name="free-licensed-sql-server-editions"></a>Edizioni di SQL Server con licenza gratuita

Se si vuole sviluppare, testare o compilare un modello di verifica, usare **SQL Server Developer Edition** con licenza gratuita. Questa edizione offre tutte le funzionalità di SQL Server Enterprise Edition, consentendo di compilare e testare qualsiasi tipo di applicazione. Tuttavia non è possibile eseguire SQL Server Developer Edition nell'ambiente di produzione. Per una macchina virtuale con SQL Server Developer Edition sarà applicato solo il costo della macchina virtuale e non quello per i costi delle licenze di SQL Server associati.

Se si vuole eseguire un carico di lavoro leggero in fase di produzione (<4 core, <1 GB di memoria, <10 GB di database), usare l'**edizione SQL Server Express** con licenza gratuita. Una macchina virtuale con SQL Server Express Edition comporta addebiti solo per il costo della macchina virtuale.

Per questi carichi di lavoro di sviluppo/test e di produzione leggeri è possibile risparmiare anche scegliendo una VM più piccola ma comunque adeguata a tali carichi di lavoro. DS1v2 potrebbe essere una buona scelta in alcuni scenari.

Per creare una macchina virtuale di Azure che esegue SQL Server 2017 con una di queste immagini, vedere i collegamenti seguenti:

| Piattaforma | Immagini con licenza gratuita |
|---|---|
| Windows Server 2016 | [Macchina virtuale SQL Server 2017 Developer di Azure](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonWindowsServer2016)<br/>[Macchina virtuale SQL Server 2017 Express di Azure](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonWindowsServer2016) |
| Red Hat Enterprise Linux | [Macchina virtuale SQL Server 2017 Developer di Azure](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonRedHatEnterpriseLinux74)<br/>[Macchina virtuale SQL Server 2017 Express di Azure](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonRedHatEnterpriseLinux74) |
| SuSE Linux Enterprise Server | [Macchina virtuale SQL Server 2017 Developer di Azure](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonSLES12SP2)<br/>[Macchina virtuale SQL Server 2017 Express di Azure](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonSLES12SP2) |
| Ubuntu | [Macchina virtuale SQL Server 2017 Developer di Azure](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonUbuntuServer1604LTS)<br/>[Macchina virtuale SQL Server 2017 Express di Azure](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonUbuntuServer1604LTS) |

## <a name="paid-sql-server-editions"></a>Edizioni di SQL Server a pagamento

Se si ha un carico di lavoro di produzione non leggero, usare una delle seguenti edizioni di SQL Server:

| Edizione di SQL Server | Carico di lavoro |
|-----|-----|
| Web | Siti Web di piccole dimensioni |
| Standard | Carichi di lavoro di piccole e medie dimensioni |
| Enterprise | Carichi di lavoro di grandi dimensioni o critici|

Sono disponibili due possibilità per il pagamento delle licenze di SQL Server per queste edizioni: *pagamento in base all'uso* o *Bring Your Own License*.

## <a name="pay-per-usage"></a>Pagamento in base all'utilizzo

**Pagamento della licenza di SQL Server in base all'utilizzo** (anche noto come **pagamento in base al consumo**) indica che il costo al secondo dell'esecuzione della macchina virtuale di Azure include il costo della licenza di SQL Server. È possibile visualizzare i prezzi per le diverse edizioni di SQL Server (Web, standard, Enterprise) nella pagina dei prezzi di macchine virtuali di Azure per [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows) o [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux).

Il costo è lo stesso per tutte le versioni di SQL Server (2012 SP3 a 2019). Il costo della licenza al secondo dipende dal numero di CPU virtuali della macchina virtuale.

Il pagamento della licenze di SQL Server in base all'utilizzo è consigliato per:

- **Carichi di lavoro temporanei o periodici**. Ad esempio un'app che deve supportare un evento per un paio di mesi all'anno o analisi di business il lunedì.

- **Carichi di lavoro con durata o dimensione sconosciuta**. Ad esempio un'app che potrebbe non essere necessaria per alcuni mesi o che potrebbe richiedere una maggiore o minore potenza di calcolo, in base alla richiesta.

Per creare una macchina virtuale di Azure che esegue SQL Server 2017 con una di queste immagini con pagamento in base al consumo, vedere i collegamenti seguenti:

| Piattaforma | Immagini con licenza |
|---|---|
| Windows Server 2016 | [Macchina virtuale SQL Server 2017 Web di Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonWindowsServer2016)<br/>[Macchina virtuale SQL Server 2017 Standard di Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonWindowsServer2016)<br/>[Macchina virtuale SQL Server 2017 Enterprise di Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseWindowsServer2016) |
| Red Hat Enterprise Linux | [Macchina virtuale SQL Server 2017 Web di Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonRedHatEnterpriseLinux74)<br/>[Macchina virtuale SQL Server 2017 Standard di Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonRedHatEnterpriseLinux74)<br/>[Macchina virtuale SQL Server 2017 Enterprise di Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonRedHatEnterpriseLinux74) |
| SuSE Linux Enterprise Server | [Macchina virtuale SQL Server 2017 Web di Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonSLES12SP2)<br/>[Macchina virtuale SQL Server 2017 Standard di Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonSLES12SP2)<br/>[Macchina virtuale SQL Server 2017 Enterprise di Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonSLES12SP2) |
| Ubuntu | [Macchina virtuale SQL Server 2017 Web di Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonUbuntuServer1604LTS)<br/>[Macchina virtuale SQL Server 2017 Standard di Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonUbuntuServer1604LTS)<br/>[Macchina virtuale SQL Server 2017 Enterprise di Azure](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonUbuntuServer1604LTS) |

> [!IMPORTANT]
> Quando si crea una macchina virtuale SQL Server nel portale di Azure, la finestra **Scegli una dimensione** Mostra un costo stimato. È importante notare che questa stima è solo il calcolo dei costi per l'esecuzione della macchina virtuale con altri costi di licenza del sistema operativo (Windows o sistemi operativi Linux di terza parte).
>
> ![Scegliere il pannello Dimensioni macchina virtuale](./media/pricing-guidance/sql-vm-choose-size-pricing-estimate.png)
>
>Non include i costi di licenza aggiuntivi di SQL Server per le versioni Enterprise, Standard e Web. Per ottenere una stima più accurata, selezionare il sistema operativo e la versione di SQL Server nella pagina dei prezzi per [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) o [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

> [!NOTE]
> È ora possibile cambiare il modello di licenza passando dal pagamento in base all'utilizzo a Bring Your Own License (BYOL) e viceversa. Per altre informazioni, vedere [Come cambiare il livello di licenza per una macchina virtuale SQL Server](licensing-model-azure-hybrid-benefit-ahb-change.md). 

## <a name="bring-your-own-license-byol"></a><a id="byol"></a> Bring Your Own License (BYOL)

**Bringing Your Own License per SQL Server attraverso Mobilità delle licenze**, o **BYOL**, indica l'utilizzo di un contratto multilicenza di SQL Server esistente con Software Assurance in una VM di Azure. Per una macchina virtuale di SQL Server che usa l'opzione BYOL sarà addebitato solo il costo di esecuzione della macchina virtuale e non la licenza di SQL Server, dato che le licenze e Software Assurance sono già stati acquisiti tramite un programma di contratto multilicenza.

> [!IMPORTANT]
> Le immagini BYOL richiedono un contratto Enterprise con Software Assurance. Non sono disponibili come parte di Azure Cloud Solution Partner (CSP) in questo momento. I clienti CSP possono usare la propria licenza distribuendo un'immagine con pagamento in base al consumo e quindi abilitando il [Vantaggio Azure Hybrid](licensing-model-azure-hybrid-benefit-ahb-change.md).

> [!NOTE]
> Le immagini BYOL sono attualmente disponibili solo per le macchine virtuali Windows. È tuttavia possibile installare manualmente SQL Server in una macchina virtuale solo Linux. Vedere le linee guida nella [SQL Server domande frequenti su una macchina virtuale Linux](../linux/frequently-asked-questions-faq.md).

È consigliabile usare la gestione delle licenze SQL Server tramite Mobilità delle licenze per:

- **Carichi di lavoro continui**. Ad esempio un'app che deve supportare operazioni aziendali 24/7.

- **Carichi di lavoro con dimensione e durata note**. Ad esempio un'app che sarà necessaria per l'intero anno e la cui domanda è stata prevista.

Per usare l'opzione BYOL con una macchina virtuale SQL Server è necessario avere una licenza di SQL Server Standard o Enterprise e [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default.aspx#tab=1), che è un'opzione obbligatoria per alcuni programmi multilicenza e facoltativa per altri. I livelli di prezzi forniti tramite i programmi multilicenza variano in base al tipo di contratto e alla quantità e/o all'impegno con SQL Server. Ma, come regola generale, l'opzione Bringing Your Own License per i carichi di lavoro di produzione continui presenta i seguenti vantaggi:

| Vantaggio dell'opzione BYOL | Descrizione |
|-----|-----|
| **Risparmi sui costi** | Il [Vantaggio Azure Hybrid](https://azure.microsoft.com/pricing/hybrid-benefit/) offre risparmi fino al 55%. Per altre informazioni, vedere [Cambiare il modello di licenza](licensing-model-azure-hybrid-benefit-ahb-change.md) |
| **Replica secondaria passiva gratuita** | Un altro vantaggio dell'opzione Bring Your Own License è la [licenza gratuita per una replica secondaria passiva](https://azure.microsoft.com/pricing/licensing-faq/) per SQL Server per scopi di disponibilità elevata. Questo dimezza il costo della licenza di una distribuzione di SQL Server a disponibilità elevata, ad esempio con gruppi di disponibilità Always On. I diritti per eseguire la replica secondaria passiva sono forniti attraverso i vantaggi di Software Assurance per i server di failover. |

Per creare una macchina virtuale di Azure che esegue SQL Server 2017 con una di queste immagini Bring your own License, vedere le VM con prefisso "{BYOL}":

- [Macchina virtuale SQL Server 2017 Enterprise di Azure](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017EnterpriseWindowsServer2016)
- [Macchina virtuale SQL Server 2017 Standard di Azure](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017StandardonWindowsServer2016)

> [!IMPORTANT]
> Si prega di comunicare entro 10 giorni il numero di licenze di SQL Server in uso in Azure. Nei collegamenti alle immagini precedenti sono incluse le istruzioni su come eseguire questa operazione.

> [!NOTE]
> È ora possibile cambiare il modello di licenza passando dal pagamento in base all'utilizzo a Bring Your Own License (BYOL) e viceversa. Per altre informazioni, vedere [Come cambiare il livello di licenza per una macchina virtuale SQL Server](licensing-model-azure-hybrid-benefit-ahb-change.md). 



## <a name="reduce-costs"></a>Riduzione dei costi

Per evitare i costi non necessari, scegliere una dimensione di macchina virtuale ottimale e considerare gli arresti intermittenti per carichi di lavoro non continui.

### <a name="correctly-size-your-vm"></a><a id="machinesize"></a> Gestisce correttamente le dimensioni della macchina virtuale

I costi di licenza di SQL Server sono direttamente correlati al numero di CPU virtuali. Scegliere le dimensioni della macchina virtuale che soddisfano le esigenze previste per CPU, memoria, archiviazione e della larghezza di banda di I/O. Per un elenco completo delle opzioni relative alle dimensioni del computer, vedere [Dimensioni per le macchine virtuali Windows](../../../virtual-machines/sizes.md) e [Dimensioni per le macchine virtuali Linux](../../../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Sono disponibili nuove dimensioni per i computer che funzionano bene con determinati tipi di carichi di lavoro di SQL Server. Queste dimensioni di computer mantengono livelli elevati di memoria, archiviazione e larghezza di banda di I/O, ma hanno un basso numero di core virtualizzati. Ad esempio, si consideri l'esempio seguente:

| Dimensioni macchina virtuale | vCPU | Memoria | Numero massimo di dischi | Massima velocità effettiva di I/O | Costi di licenza SQL Server | Costi totali (calcolo + licenze) |
|---|---|---|---|---|---|---|
| **Standard_DS14v2** | 16 | 112 GB | 32 | 51.200 operazioni di I/O al secondo o 768 MB/s | | |
| **Standard_DS14-4v2** | 4 | 112 GB | 32 | 51.200 operazioni di I/O al secondo o 768 MB/s | 75% in meno | 57% in meno |

> [!IMPORTANT]
> Questo è un esempio temporizzato. Per le specifiche tecniche più recenti, vedere gli articoli sulle dimensioni per i computer e la pagina dei prezzi di Azure per [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) e [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

Nell'esempio precedente è possibile vedere che le specifiche per **Standard_DS14v2** e **Standard_DS14-4v2** sono identiche ad eccezione di quelle per la CPU virtuale. Il suffisso **-4v2** alla fine della dimensione del computer **Standard_DS14-4v2** indica il numero di CPU virtuali attive. Poiché i costi di licenza di SQL Server sono collegati al numero di CPU virtuali, questo riduce notevolmente il costo della macchina virtuale in scenari in cui non sono necessarie CPU virtuali aggiuntive. Questo è un esempio, ma ci sono diverse dimensioni per i computer con CPU virtuali vincolate identificate con questo modello di suffisso. Per altre informazioni, vedere il post di blog [Annuncio delle nuove dimensioni per le macchine virtuali di Azure per operazioni sul database più convenienti](https://azure.microsoft.com/blog/announcing-new-azure-vm-sizes-for-more-cost-effective-database-workloads/).

### <a name="shut-down-your-vm-when-possible"></a>Arrestare la macchina virtuale quando possibile

Se si usano carichi di lavoro che non vengono eseguiti in modo continuativo, è consigliabile arrestare la macchina virtuale durante i periodi di inattività. Si paga solo per le risorse utilizzate.

Ad esempio, se si sta semplicemente provando SQL Server in una VM di Azure, evitare che rimanga in esecuzione per settimane per non incorrere in addebiti inutili. Una possibile soluzione è usare la [funzionalità di arresto automatico](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/).

![Arresto automatico della macchina virtuale SQL Server](./media/pricing-guidance/sql-vm-auto-shutdown.png)

L'arresto automatico fa parte di un insieme più ampio di funzionalità simili fornite da [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab).

Per altri flussi di lavoro, prendere in considerazione l'arresto e il riavvio automatico delle macchine virtuali con una soluzione script come [Automazione di Azure](https://azure.microsoft.com/services/automation/).

> [!IMPORTANT]
> Arrestare e deallocare la VM è l'unico modo per evitare addebiti. La semplice interruzione o il semplice uso delle opzioni di risparmio energia per arrestare la VM non sospende l'addebito per l'utilizzo.

## <a name="next-steps"></a>Passaggi successivi

Per informazioni di guida generali sui prezzi di Azure, vedere [Evitare costi imprevisti con la gestione dei costi e alla fatturazione di Azure](../../../cost-management-billing/cost-management-billing-overview.md). Per i prezzi più recenti per le macchine virtuali di Azure, tra cui SQL Server, vedere la pagina dei prezzi di macchine virtuali di Azure per [VM Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) e [VM Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

Per una panoramica delle SQL Server in macchine virtuali di Azure, vedere gli articoli seguenti:

- [Panoramica di SQL Server nelle macchine virtuali Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
- [Panoramica di SQL Server nelle macchine virtuali Linux](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)