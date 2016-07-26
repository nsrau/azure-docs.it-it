<properties
	pageTitle="Che cos'è Site Recovery? | Microsoft Azure" 
	description="Fornisce una panoramica del servizio Azure Site Recovery e illustra come è possibile distribuire il servizio." 
	services="site-recovery" 
	documentationCenter="" 
	authors="rayne-wiselman" 
	manager="jwhit" 
	editor=""/>

<tags 
	ms.service="site-recovery" 
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery" 
	ms.date="02/22/2016" 
	ms.author="raynew"/>

#  Che cos'è Site Recovery?

Benvenuti in Azure Site Recovery. È possibile iniziare con questo articolo per ottenere una rapida panoramica del servizio Site Recovery e come può favorire l'attuazione della strategia di continuità aziendale e ripristino di emergenza (BCDR).

## Panoramica

Le organizzazioni necessitano di una strategia di continuità aziendale e ripristino di emergenza per determinare come app, carichi di lavoro e dati possano rimanere in esecuzione e disponibili durante i periodi di inattività, pianificati o meno, e come ripristinare le normali condizioni di lavoro il prima possibile. La strategia di continuità aziendale e ripristino di emergenza deve garantire la sicurezza dei dati aziendali e la possibilità di recuperarli, oltre alla disponibilità costante dei carichi di lavoro in caso di emergenza.

Il servizio Azure Site Recovery contribuisce alla strategia BCDR orchestrando la replica dei server fisici locali e delle macchine virtuali sul cloud (Azure) o in un data center secondario. In caso di interruzioni nella località primaria, verrà eseguito il failover alla località secondaria per mantenere disponibili app e carichi di lavoro. Quando la località primaria sarà di nuovo operativa, si tornerà a tale località. Per altre informazioni, vedere [Informazioni su Site Recovery](site-recovery-overview.md).

## Site Recovery nel portale di Azure

Azure offre due [modelli di distribuzione](../resource-manager-deployment-model.md) diversi per la creazione e l'uso delle risorse: il modello Azure Resource Manager e il modello di gestione classica dei servizi. Sono anche disponibili il [portale di Azure classico](https://manage.windowsazure.com/), che supporta il modello di distribuzione classica, e il [portale di Azure](https://portal.azure.com), che supporta entrambi i modelli di distribuzione.

Site Recovery è disponibile sia nel portale classico che nel portale di Azure. Nel portale di Azure classico è possibile supportare Site Recovery con il modello di gestione classica dei servizi. Nel portale di Azure è possibile supportare i modelli di distribuzione classica e Resource Manager. [Altre informazioni](site-recovery-overview.md#site-recovery-in-the-azure-portal) sulla distribuzione con il portale di Azure.

Le informazioni in questo articolo sono applicabili al modello di distribuzione classica e al modello di distribuzione tramite il portale di Azure. Le differenze sono indicate ove applicabili.


## Perché usare Site Recovery? 

Ecco cosa può fare Site Recovery per l'azienda:

- **Semplificare la strategia BCDR**: Site Recovery consente di gestire facilmente la replica, il failover e il ripristino di più carichi di lavoro e app aziendali da un'unica posizione. Site Recovery orchestra la replica e il failover, ma non intercetta i dati dell'applicazione né raccoglie le relative informazioni.
- **Offrire una modalità di replica flessibile**: con Site Recovery è possibile replicare i carichi di lavoro in esecuzione in macchine virtuali Hyper-V, macchine virtuali VMware e server fisici Windows o Linux.
- **Eseguire facilmente failover e ripristino**: Site Recovery offre failover di test per supportare analisi del ripristino di emergenza senza interessare gli ambienti di produzione. È anche possibile eseguire failover pianificati senza perdita di dati per interruzioni previste o il failover non pianificato con perdita di dati minima, in base alla frequenza di replica, per emergenze impreviste. Dopo il failover è possibile eseguire il failback nei siti primari. Site Recovery fornisce i piani di ripristino che possono includere script e cartelle di lavoro di automazione di Azure per personalizzare il failover e il ripristino di applicazioni multilivello.
- **Eliminare il data center secondario**: è possibile eseguire la replica in un sito secondario locale o in Azure. Se si usa Azure come destinazione per il ripristino di emergenza, elimina i costi e le complessità di gestione di un sito secondario e i dati replicati vengono archiviati in Archiviazione di Azure, con tutta la resilienza che fornisce.
- **Integrarsi con le tecnologie BCDR esistenti**: Site Recovery interagisce con altre funzionalità BCDR delle applicazioni. A esempio, è possibile usare Site Recovery per proteggere il back-end SQL Server dei carichi di lavoro aziendali, incluso il supporto nativo per SQL Server AlwaysOn per gestire il failover dei gruppi di disponibilità.

## Ciò che è possibile replicare?

Ecco un riepilogo di ciò che è possibile replicare con Site Recovery.

![Da sito locale a sito locale](./media/site-recovery-overview/asr-overview-graphic.png)

**REPLICA** | **ORIGINE REPLICA (LOCALE)** | **DESTINAZIONE REPLICA** | **ARTICOLO**
---|---|---|---
VM VMware | Server VMware | Azure | [Altre informazioni](site-recovery-vmware-to-azure-classic.md)
VM VMware | Server VMware | Sito secondario di VMware | [Altre informazioni](site-recovery-vmware-to-vmware.md) 
VM Hyper-V | Host Hyper-V in cloud VMM | Azure | [Altre informazioni](site-recovery-vmm-to-azure.md) 
VM Hyper-V | Host Hyper-V in cloud VMM | Sito VMM secondario | [Altre informazioni](site-recovery-vmm-to-vmm.md)
VM Hyper-V | Host Hyper-V in cloud VMM con archiviazione SAN| Sito secondario di VMM con archiviazione SAN | [Altre informazioni](site-recovery-vmm-san.md)
VM Hyper-V | Host Hyper-V (senza VMM) | Azure | [Altre informazioni](site-recovery-hyper-v-site-to-azure.md)
Server fisici Windows/Linux | Server fisico | Azure | [Altre informazioni](site-recovery-vmware-to-azure-classic.md)
Carichi di lavoro in esecuzione in server fisici Windows/Linux | Server fisico | Centro dati secondario | [Altre informazioni](site-recovery-vmware-to-vmware.md) 


## Quali carichi di lavoro è possibile proteggere?

Site Recovery può essere utile per una strategia BCDR compatibile con le applicazioni, per consentire l'esecuzione ininterrotta e coerente dei carichi di lavoro e delle app quando si verificano interruzioni del servizio. Site Recovery offre:

- **Snapshot coerenti con l'applicazione**. Replica con snapshot coerenti con l'applicazione per app a uno o più livelli.
- **Replica quasi sincrona**. Frequenza di replica di soli 30 secondi per Hyper-V e replica continua per VMware.
- **Integrazione con SQL Server AlwaysOn**. È possibile gestire il failover dei gruppi di disponibilità nei piani di ripristino di Site Recovery.
- **Piani di ripristino flessibili**. È possibile creare e personalizzare i piani di ripristino con script esterni, azioni manuali e runbook di automazione di Azure che consentono di ripristinare un intero stack di applicazioni con un solo clic.
- **Libreria di automazione**. Un'avanzata libreria di automazione di Azure offre script pronti per la produzione e specifici dell'applicazione che possono essere scaricati e integrati con Site Recovery.
- **Gestione semplice della rete**. Gestione di rete avanzata in Site Recovery e, in Azure, requisiti di rete più semplici per le applicazioni, come l'impostazione di indirizzi IP riservati, la configurazione di servizi di bilanciamento del carico e l'integrazione di Gestione traffico di Azure per cambi di rete efficienti.


## Passaggi successivi

- Per altre informazioni, vedere [Quali carichi di lavoro è possibile proteggere con Azure Site Recovery?](site-recovery-workload.md)
- Per altre informazioni sull'architettura di Site Recovery, vedere [Funzionamento di Azure Site Recovery](site-recovery-components.md)
 

<!---HONumber=AcomDC_0720_2016-->