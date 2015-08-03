<properties
	pageTitle="Introduzione a Backup di Azure"
	description="Questo articolo offre una panoramica del servizio Backup di Azure che consente il backup dei dati in Azure"
	services="backup"
	documentationCenter=""
	authors="trinadhk"
	manager="shreeshd"
	editor="tysonn"/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/13/2015"
	ms.author="trinadhk"/>

# Introduzione a Backup di Azure
Questo articolo offre una panoramica di alto livello della soluzione di backup integrata nel cloud di Microsoft che consente ai clienti di eseguire il backup dei dati disponibili in locale o in Azure.

## Informazioni su Backup di Azure
Backup di Azure è un servizio di Azure con tenant multipli che consente di eseguire il backup dei dati presenti ovunque: in locale o in Azure. Sostituisce la soluzione di backup locale o esterno esistente con una soluzione basata su cloud affidabile, sicura e conveniente. Offre inoltre la flessibilità di proteggere gli asset in esecuzione nel cloud. Backup di Azure si basa su un'infrastruttura di livello mondiale, scalabile, durevole e a disponibilità elevata. Con questa soluzione è possibile eseguire il backup di dati e applicazioni dai server System Center Data Protection Manager (SCDPM), dai server Windows, dai computer client Windows o da macchine virtuali IaaS di Azure. Backup di Azure e SCDPM sono le tecnologie fondamentali di cui si compone la soluzione di backup integrata nel cloud di Microsoft.

> [AZURE.VIDEO what-is-azure-backup]

## Punto di progettazione cloud
Le soluzioni di backup tradizionali si sono evolute e considerano ora un cloud come un endpoint analogo a un disco o a un nastro. Questo approccio è semplice, facile da distribuire e offre un'esperienza coerente, ma ha un uso limitato e non sfrutta appieno la piattaforma sottostante. Ne risulta una soluzione poco efficiente e costosa per i clienti finali. Le soluzioni di backup che considerano Azure come "un semplice endpoint di archiviazione" non sono in grado di sfruttare i vantaggi e la potenza dalla piattaforma cloud pubblica. Backup di Azure garantisce invece un servizio reale che sfrutta i costrutti del cloud per offrire una soluzione di backup efficace e conveniente. Si integra con la soluzione di backup locale (SCDPM) per offrire una soluzione ibrida end-to-end.

I vantaggi di questo approccio sono i seguenti:

+ Architettura di archiviazione cloud efficiente, che consente un'archiviazione dei dati resiliente e conveniente

+ Scalabilità automatica non invasiva del servizio con garanzia di disponibilità elevata

+ Metodo coerente per il backup di distribuzioni IaaS, ibride e locali

Le funzionalità principali della soluzione sono le seguenti:

1. **Servizio affidabile**: con Backup di Azure è possibile ottenere un servizio di backup estremamente disponibile. Il servizio è multi-tenant e non richiede la gestione dell'archiviazione o del calcolo sottostante da parte dell'utente.

2. **Archiviazione affidabile**: Backup di Azure si basa su un'archiviazione cloud affidabile, supportata da contratti di servizio elevati. Non è necessario preoccuparsi delle spese di capitale o dei costi delle operazioni per la gestione dell'archiviazione. È anche possibile scegliere se eseguire il backup in un'archiviazione con ridondanza locale (LRS) o in un'archiviazione con ridondanza geografica (GRS). LRS consente di eseguire 3 copie dei dati nella stessa area geografica, per proteggere i dati da errori hardware locali. GRS fornisce 3 copie aggiuntive (per un totale di 6 copie) in un data center associato. Questo assicura la disponibilità elevata dei dati di backup. Anche in caso di emergenza nel sito di Azure, i dati di backup sono al sicuro.

3. **Sicurezza**: i dati di Backup di Azure vengono crittografati all'origine, durante la trasmissione e archiviati crittografati in Azure. La chiave di crittografia viene archiviata nell'origine e non viene mai trasmessa o archiviata in Azure. La chiave di crittografia è necessaria per ripristinare i dati e solo il cliente dispone dell'accesso completo ai dati nel servizio.

4. **Protezione esterna**: anziché pagare per soluzioni di backup esterne su nastro, i clienti possono eseguire il backup in Azure, che offre una soluzione interessante con semantica simile a nastro a un costo molto ridotto.

5. **Semplicità**: l'interfaccia familiare di Backup di Azure garantisce la scalabilità necessaria per proteggere una distribuzione di qualsiasi dimensione. Con l'evoluzione del servizio, funzionalità come la gestione centrale consentiranno di gestire l'infrastruttura di backup da un'unica posizione.

6. **Conveniente**: le offerte di Backup di Azure includono una tariffa di gestione del backup per singola istanza e costi dell'archiviazione (prezzo per BLOB in blocchi) utilizzata in Azure. A differenza di altre offerte di backup basate su cloud, Backup di Azure non prevede addebiti per tutte le operazioni di ripristino. Non vengono inoltre addebitati costi per i trasferimenti dei dati in uscita durante le operazioni di ripristino.

7. **Backup nel cloud**: Backup di Azure offre funzionalità di backup coerenti delle applicazioni basate su VSS per le macchine virtuali IaaS di Azure in esecuzione senza la necessità di arrestare la macchina virtuale. È inoltre possibile eseguire il backup delle macchine virtuali Linux in Azure con coerenza del file system.


## Applicazione e carichi di lavoro

| Carico di lavoro | Computer di origine | Soluzione di Backup di Azure |
| --- | --- |---|
| File e cartelle | Windows Server, client Windows | Agente di Backup di Azure |
| File e cartelle | Windows Server, client Windows | System Center DPM |
| Macchina virtuale Hyper-V (Windows) | Windows Server | System Center DPM |
| Macchina virtuale Hyper-V (Linux) | Windows Server | System Center DPM |
| Microsoft SQL Server | Windows Server | System Center DPM |
| Microsoft SharePoint | Windows Server | System Center DPM |
| Microsoft Exchange | Windows Server | System Center DPM |
| Macchine virtuali IaaS di Azure (Windows)| - | Backup di Azure | | Macchine virtuali IaaS di Azure (Linux) | - | Backup di Azure |

## Passaggi successivi
+ Le domande frequenti sul servizio di Backup di Azure sono disponibili [in questa pagina](backup-azure-backup-faq.md).
+ Visitare il [Forum su Backup di Azure](http://go.microsoft.com/fwlink/p/?LinkId=290933).

<!---HONumber=July15_HO4-->