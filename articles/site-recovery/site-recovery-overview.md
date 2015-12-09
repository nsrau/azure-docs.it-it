<properties
	pageTitle="Che cos'è Site Recovery?" 
	description="Azure Site Recovery coordina la replica, il failover e il ripristino delle macchine virtuali e dei server fisici ubicati nei server locali in Azure o in un sito locale secondario." 
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
	ms.date="11/29/2015" 
	ms.author="raynew"/>

#  Che cos'è Site Recovery?

Site Recovery è un servizio di Azure che favorisce la strategia di continuità aziendale e ripristino di emergenza (BCDR) coordinando la replica di macchine virtuali e server locali in un data center locale secondario o in Azure. Site Recovery gestisce il processo di replica e consente di avviare le procedure di failover e ripristino con un semplice clic. Un elenco di domande frequenti è disponibile nella sezione delle [domande frequenti](site-recovery-faq.md)


## Perché usare Site Recovery? 
- **Strategia di continuità aziendale e ripristino di emergenza più semplice**: Site Recovery consente di gestire facilmente i processi di replica, failover e ripristino delle applicazioni e dei carichi di lavoro locali.
- **Flessibilità di replica**: è possibile replicare server locali, macchine virtuali Hyper-V e macchine virtuali VMware. Site Recovery esegue una replica iniziale intelligente utilizzando solo i blocchi di dati, non l’intero disco rigido virtuale. Per la replica continua, vengono replicate solo le modifiche differenziali. Site Recovery supporta il trasferimento di dati offline e funziona con utilità di ottimizzazione WAN. 
- **Eliminazione della necessità di data center secondari**: Site Recovery non solo consente di automatizzare la replica tra data center, ma offre anche la possibilità di evitare una postazione locale secondaria eseguendo la replica in Azure. I dati replicati vengono archiviati in Archiviazione di Azure, caratterizzato da elevati livelli di resilienza.


## Scenari di distribuzione

In questa tabella sono riepilogati gli scenari di replica supportati da Site Recovery.

**OGGETTO REPLICA** | **SORGENTE REPLICA** | **DESTINAZIONE REPLICA** | **ARTICOLO**
---|---|---|---
Macchine virtuali VMware | Server VMware locale | Archiviazione di Azure | [Distribuire](site-recovery-vmware-to-azure.md)
Server fisico Windows/Linux | Server fisico locale | Archiviazione di Azure | [Distribuire](site-recovery-vmware-to-azure.md)
Macchine virtuali Hyper-V | Server host Hyper-V locale nel cloud VMM | Archiviazione di Azure | [Distribuire](site-recovery-vmm-to-azure.md)
Macchine virtuali Hyper-V | Sito Hyper-V locale (uno o più server host Hyper-V) | Archiviazione di Azure | [Distribuire](site-recovery-hyper-v-site-to-azure.md)
Macchine virtuali Hyper-V locali| Server host Hyper-V locale nel cloud VMM | Server host Hyper-V locale nel cloud VMM del data center secondario | [Distribuire](site-recovery-vmm-to-vmm)
Macchine virtuali Hyper-V | Server host Hyper-V locale nel cloud VMM con archiviazione SAN| Server host Hyper-V locale nel cloud VMM con archiviazione SAN del data center secondario | [Distribuire](site-recovery-vmm-san.md)
Macchine virtuali VMware | Server VMware locale | Data center secondario che esegue VMware | [Distribuire](https://microsoft.sharepoint.com/sites/academy/media/AEVD-3-85237) 
Server fisico Windows/Linux | Server fisico locale | Centro dati secondario | [Distribuire](https://microsoft.sharepoint.com/sites/academy/media/AEVD-3-85237) 

Questi scenari sono riepilogati nei diagrammi seguenti.

![Da sito locale a sito locale](./media/site-recovery-overview/asr-overview-graphic.png)

## Quali carichi di lavoro è possibile proteggere?

Site Recovery favorisce la continuità aziendale basata sul riconoscimento delle applicazioni. Consente ad esempio di coordinare il ripristino di emergenza per app Windows e di terze parti. La protezione basata sul riconoscimento delle applicazioni offre i vantaggi seguenti:


- Replica quasi sincrona (30 secondi) con RPO per Hyper-V e replica continua per VMware, per soddisfare le esigenze delle applicazioni più critiche.
- Snapshot coerenti con l'applicazione per le applicazioni a uno o più livelli.
- Integrazione con SQL Server AlwaysOn e interazione con altre tecnologie di replica a livello di applicazione, tra cui la replica di Active Directory, i gruppi di disponibilità dei database di Exchange e Oracle Data Guard.
- Piani di ripristino flessibili, che consentono di ripristinare un intero stack di applicazioni con un singolo clic e possono includere script esterni o azioni manuali. 
- Gestione di rete avanzata in Site Recovery e, in Azure, requisiti di rete più semplici per le app, quali la prenotazione di indirizzi IP, la configurazione di servizi di bilanciamento del carico o l'integrazione di Gestione traffico di Windows Azure per transizioni di rete con RTO ridotto.
- Una libreria di automazione avanzata, in grado di fornire script generici per l'ambiente di produzione e specifici per ogni applicazione, che possono essere scaricati e integrati con Site Recovery.  


Per saperne di più, vedere [Quali carichi di lavoro può proteggere Site Recovery?](site-recovery-workload.md).


## Passaggi successivi

Una volta completata questa panoramica, è possibile [approfondire](site-recovery-components.md) l'architettura di Site Recovery.
 

<!---HONumber=AcomDC_1203_2015-->