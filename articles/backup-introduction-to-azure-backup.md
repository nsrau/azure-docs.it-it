<properties
	pageTitle="Introduzione al Backup di Azure"
	description="In questo articolo viene fornita una panoramica del servizio di Backup di Azure che consente ai clienti di dati di backup in Azure"
	services="backup"
	documentationCenter=""
	authors="prvijay"
	manager="shreeshd"
	editor="tysonn"/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/08/2015"
	ms.author="prvijay"/>

# Introduzione al Backup di Azure
In questo articolo viene fornita una panoramica di alto livello della soluzione di Microsoft cloud integrato backup che consente ai clienti di eseguire il backup i propri dati locali in Azure.

## Che cos'è Azure Backup?
Backup di Azure è un servizio di Azure con tenant multipli che consente di eseguire il backup dei dati locali in Azure. Sostituisce la soluzione di backup fuori sede o locale esistente con un cloud competitivo affidabile, sicuro e costo base offerta. Backup di Azure è basato su un'infrastruttura di classe mondiale è scalabile, durevole e a disponibilità elevata. Utilizzo di questa soluzione, è possibile eseguire backup dati e le applicazioni dai server di System Center Data Protection Manager (SCDPM), Windows Server o computer client Windows. Backup di Azure e SCDPM sono tecnologie fondamentali che costituiscono la soluzione di backup integrata e cloud di Microsoft.

## Area di progettazione
Soluzioni di backup tradizionali si sono evolute per considerare cloud come un endpoint simile su disco o nastro. Sebbene questo approccio è semplice e facile da distribuire e offre un'esperienza coerenza, ha un utilizzo limitato e non sfruttare la piattaforma sottostante. Ciò si traduce in una soluzione poco efficiente e costosa per i clienti finali. Trattando Azure come "solo come un endpoint di archiviazione" soluzioni di backup sono in grado di sfruttare la ricchezza e la potenza della piattaforma di cloud pubblico. D'altra parte, Backup di Azure offre un vero e proprio servizio che utilizza costrutti di cloud per fornire una soluzione di backup efficace e conveniente. Si integra con la soluzione di backup locale (SCDPM) per offrire una soluzione ibrida end-to-end.

I vantaggi di questo approccio sono:

+ Architettura di archiviazione cloud efficiente che consente di memorizzare dati a basso costo e resilienti

+ Non intrusivo, garantisce la scalabilità automatica del servizio con disponibilità elevata

+ Metodo coerente per il backup in locale, ibridi e le distribuzioni IaaS

Le funzionalità principali di questa soluzione sono:

1. **Servizio affidabile**: adottando Backup di Azure, è possibile ottenere un servizio di backup che è a disponibilità elevata. Il servizio è con tenant più e non è necessario preoccuparsi di gestire il calcolo sottostante o archiviazione.

2. **Archiviazione affidabile**: Backup di Azure si basa sull'archiviazione affidabile cloud supportata da elevata contratti di servizio. Non è necessario preoccuparsi delle spese di capitale o operazione mantenere lo spazio di archiviazione. Inoltre, è possibile scegliere di backup di archiviazione con ridondanza locale (archiviazione con ridondanza locale) o di archiviazione (archiviazione replica geografica) GRS. Mentre LRS consente di 3 copie dei dati nella stessa area geografica che protegge da errori hardware locale. GRS fornisce copie aggiuntive 3 (un totale di 6 copie) in un centro dati associati. Questo assicura che i dati di backup siano a disponibilità elevata. Anche se è presente un'emergenza a livello di sito Azure, i dati di backup sono sicuri con noi.

3. **Secure**: dati di Azure backup viene crittografati a origine, durante la trasmissione e stored crittografati in Azure. La chiave di crittografia archiviata nell'origine e mai trasmessi o archiviata in Azure. La chiave di crittografia è necessaria per ripristinare i dati e solo il cliente ha accesso completo ai dati nel servizio.

4. **Protezione fuori sede**: invece di pagare per soluzioni di backup su nastro fuori sede, i clienti possono eseguire il backup in Azure che fornisce una soluzione interessante con semantica di tipo nastro a un costo molto ridotto.

5. **Semplicità**: Backup di Azure fornisce un'interfaccia familiare scalabili in grado di proteggere una distribuzione di qualsiasi dimensione. Come si evolve il servizio, funzionalità quali gestione centrale consente di gestire l'infrastruttura di backup da un'unica posizione.

6. **Conveniente**: determinazione dei prezzi di Azure Backup include una Commissione di gestione di backup per ogni istanza e i costi di archiviazione (prezzo blob blocco) utilizzato in Azure. A differenza di altri offerta backup basato sul cloud, Azure Backup non viene addebitata i clienti per qualsiasi operazione di ripristino. Inoltre, i clienti non vengono addebitati per i trasferimenti di dati (in uscita) in uscita dei costi durante un'operazione di ripristino.


## Applicazione e i carichi di lavoro che è possibile eseguire il backup in Azure
Combinata con Backup di Azure SCDPM possibile eseguire il backup:

+ File e cartelle dal client aziendali, computer server

+ Backup della macchina virtuale a livello di host di macchine virtuali di Microsoft Hyper-V

+ Microsoft SQL Server

+ Microsoft SharePoint

+ Microsoft Exchange

## Passaggi successivi
+ Domande frequenti domanda sul servizio Azure Backup è elencato [qui](backup-azure-backup-faq.md).
+ Visitare il [Forum su Backup di Azure](http://go.microsoft.com/fwlink/p/?LinkId=290933).

<!---HONumber=GIT-SubDir-->