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

Azure offre due diversi modelli di distribuzione per creare e usare le risorse: [Gestione risorse e la distribuzione classica](../resource-manager-deployment-model.md). Questo articolo si applica a entrambi i modelli. Microsoft consiglia di usare il modello di Gestione risorse per le distribuzioni più recenti.

## Panoramica

Una parte importante della strategia BCDR dell'organizzazione è capire come mantenere in esecuzione le app e i carichi di lavoro aziendali quando si verificano interruzioni pianificate e non pianificate.

Site Recovery consente di farlo orchestrando la replica, il failover e il ripristino di carichi di lavoro e app in modo che siano disponibili da una posizione secondaria in caso di inattività di quella primaria.

## Perché usare Site Recovery? 

Ecco cosa può fare Site Recovery per l'azienda:

- **Semplificare la strategia BCDR**: Site Recovery consente di gestire la replica, il failover e il ripristino di più carichi di lavoro e app aziendali da un'unica posizione. Site Recovery orchestra la replica e il failover, ma non intercetta i dati dell'applicazione né raccoglie le relative informazioni.
- **Fornire una modalità di replica flessibile**: con Site Recovery è possibile replicare i carichi di lavoro in esecuzione in macchine virtuali Hyper-V, macchine virtuali VMware e server fisici Windows o Linux. 
- **Eseguire facilmente failover e ripristino**: Site Recovery consente il failover di test per supportare analisi del ripristino di emergenza senza interessare gli ambienti di produzione. È anche possibile eseguire failover pianificati senza perdita di dati per interruzioni previste o il failover non pianificato con perdita di dati minima, in base alla frequenza di replica, per emergenze impreviste. Dopo il failover è possibile eseguire il failback nei siti primari. Site Recovery fornisce i piani di ripristino che possono includere script e cartelle di lavoro di automazione di Azure per personalizzare il failover e il ripristino di applicazioni multilivello. 
- **Eliminare un data center secondario**: è possibile eseguire la replica in un sito secondario in locale o in Azure. Se si usa Azure come destinazione per il ripristino di emergenza, elimina i costi e le complessità di gestione di un sito secondario e i dati replicati vengono archiviati in Archiviazione di Azure, con tutta la resilienza che fornisce.
- **Integrarsi con le tecnologie BCDR esistenti**: Site Recovery interagisce con altre funzionalità BCDR dell'applicazione. A esempio, è possibile usare Site Recovery per proteggere il back-end SQL Server dei carichi di lavoro aziendali, incluso il supporto nativo per SQL Server AlwaysOn per gestire il failover dei gruppi di disponibilità. 

## Ciò che è possibile replicare?

Ecco un riepilogo di ciò che è possibile replicare con Site Recovery.

![Da sito locale a sito locale](./media/site-recovery-overview/asr-overview-graphic.png)

**REPLICATE** | **SORGENTE REPLICA** | **DESTINAZIONE REPLICA** | **ARTICOLO**
---|---|---|---
Carichi di lavoro in esecuzione in VM VMware | Server VMware locale | Archiviazione di Azure | [Distribuire](site-recovery-vmware-to-azure-classic.md)
Carichi di lavoro in esecuzione in VM VMware | Server VMware locale | Sito secondario di VMware | [Distribuire](site-recovery-vmware-to-vmware.md) 
Carichi di lavoro in esecuzione in VM Hyper-V | Server host Hyper-V locale nel cloud VMM | Archiviazione di Azure | [Distribuire](site-recovery-vmm-to-azure.md)
Carichi di lavoro in esecuzione in VM Hyper-V | Server host Hyper-V locale nel cloud VMM | Sito VMM secondario | [Distribuire](site-recovery-vmm-to-vmm.md)
Carichi di lavoro in esecuzione in VM Hyper-V | Server host Hyper-V locale nel cloud VMM con archiviazione SAN| Sito secondario di VMM con archiviazione SAN | [Distribuire](site-recovery-vmm-san.md)
Carichi di lavoro in esecuzione in VM Hyper-V | Sito Hyper-V locale (senza VMM) | Archiviazione di Azure | [Distribuire](site-recovery-hyper-v-site-to-azure.md)
Carichi di lavoro in esecuzione in server fisici Windows/Linux | Server fisico locale | Archiviazione di Azure | [Distribuire](site-recovery-vmware-to-azure-classic.md)
Carichi di lavoro in esecuzione in server fisici Windows/Linux | Server fisico locale | Centro dati secondario | [Distribuire](site-recovery-vmware-to-vmware.md) 


## Quali carichi di lavoro è possibile proteggere?

Site Recovery può essere utile per una strategia BCDR compatibile con le applicazioni, per consentire l'esecuzione ininterrotta e coerente dei carichi di lavoro e delle app quando si verificano interruzioni del servizio. Site Recovery offre:

- **Snapshot coerenti con l'applicazione**: per la replica tramite snapshot coerenti con l'applicazione per applicazioni a uno o più livelli. **Replica quasi sincrona**: per una frequenza di replica anche si soli 30 secondi per Hyper-V e la replica continua per VMware. **Integrazione con SQL Server AlwaysOn**: è possibile gestire il failover dei gruppi di disponibilità nei piani di ripristino di Site Recovery. 
- **Piani di ripristino flessibili**: è possibile creare e personalizzare i piani di ripristino con script esterni, azioni manuali e runbook di automazione di Azure che consentono di ripristinare un intero stack di applicazioni con un solo clic.
- **Libreria di automazione**: una ricca libreria di automazione di Azure fornisce script pronti per la produzione e specifici dell'applicazione che possono essere scaricati e integrate con Site Recovery. -** Semplice gestione della rete**: la gestione di rete avanzata in Site Recovery e Azure semplifica i requisiti di rete dell'applicazione, tra cui la prenotazione di indirizzi IP, la configurazione di servizi di bilanciamento del carico e l'integrazione di Gestione traffico di Azure per commutazioni di rete efficienti.


## Passaggi successivi

- Per altre informazioni, vedere [Quali carichi di lavoro è possibile proteggere con Azure Site Recovery?](site-recovery-workload.md).
- Altre informazioni sull'architettura di Site Recovery sono disponibili in [Funzionamento di Azure Site Recovery](site-recovery-components.md)
 

<!---HONumber=AcomDC_0316_2016-->