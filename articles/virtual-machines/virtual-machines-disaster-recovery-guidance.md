<properties
	pageTitle="Operazioni da eseguire in caso di un'interruzione del servizio Azure con impatto sulle macchine virtuali di Azure | Microsoft Azure"
	description="Informazioni sulle operazioni da eseguire in caso di un'interruzione del servizio Azure con impatto sulle macchine virtuali di Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="kmouss"
	manager="drewm"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="virtual-machines"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/16/2016"
	ms.author="kmouss;aglick"/>

#Operazioni da eseguire in caso di un'interruzione del servizio Azure con impatto sulle macchine virtuali di Azure

Microsoft si impegna costantemente per garantire agli utenti la disponibilità dei servizi in base alle esigenze. Tuttavia, a volte si verificano eventi al di fuori del suo controllo, che causano interruzioni non pianificate dei servizi.

Microsoft fornisce un Contratto di servizio come impegno per garantire connettività e tempi di attività. Il Contratto di servizio per i singoli servizi di Azure è disponibile in [Contratti di servizio](https://azure.microsoft.com/support/legal/sla/).

Azure offre già numerose funzionalità di piattaforma integrate che supportano applicazioni a disponibilità elevata. Per altre informazioni su questi servizi, vedere [Disaster Recovery and High Availability for Azure Applications](https://aka.ms/drtechguide) (Ripristino di emergenza e disponibilità elevata per le applicazioni di Azure).

Questo documento illustra un caso reale di ripristino di emergenza, quando in un'intera area si verifica un'interruzione a causa di una grave emergenza naturale o di un'interruzione diffusa del servizio. Si tratta di episodi rari, ma è necessario prepararsi alla possibilità che si verifichi tale evento. In caso di un'interruzione del servizio che interessa un'intera area, le copie ridondanti locali dei dati non saranno temporaneamente disponibili. Se è stata abilitata la replica geografica, esistono altre tre copie dei BLOB e delle tabelle di Archiviazione di Azure archiviate in un'area diversa. Nel caso di un'interruzione completa dell'alimentazione a livello locale o di un'emergenza in cui l'area primaria non è recuperabile, Azure esegue un nuovo mapping di tutte le voci DNS all'area con replica geografica.
 
>[AZURE.NOTE]Tenere presente che non è possibile controllare questo processo e che verrà eseguito solo in caso di errori a livello di area. Per questo motivo, è necessario affidarsi anche ad altre strategie di backup specifiche dell'applicazione per ottenere il massimo livello di disponibilità. Per altre informazioni, vedere la sezione sulle [strategie dei dati per il ripristino di emergenza](https://aka.ms/drtechguide#DSDR).

Per facilitare la gestione di tali rari episodi, vengono fornite le seguenti indicazioni per la macchina virtuale di Azure nel caso di un'interruzione del servizio dell'intera area in cui viene distribuita l'applicazione della macchina virtuale di Azure.

##Opzione 1: Attendere il ripristino 
In tal caso, non è necessaria alcuna azione da parte dell'utente. Si sta lavorando per ripristinare la disponibilità dei servizi. È possibile vedere lo stato corrente del servizio nel [dashboard per l'integrità dei servizi di Azure](https://azure.microsoft.com/status/).

>[AZURE.NOTE]Questa è l'opzione migliore se non si è impostato Azure Site Recovery (ASR), il backup delle macchine virtuali (VM), l'archiviazione con ridondanza geografica e accesso in lettura (RA-GRS) o l'archiviazione con ridondanza geografica (GRS) prima dell'interruzione. Se l'archiviazione GRS (o RA-GRS) è stata impostata per l'account di archiviazione in cui sono archiviati i dischi rigidi virtuali della VM, è possibile esaminare il ripristino dell'immagine VHD di base e provare a effettuare il provisioning di una nuova VM da esso. Questa non è un'opzione consigliabile perché non vi è alcuna garanzia di sincronizzazione dei dati a meno che non venga usato il backup delle VM di Azure o ASR e, pertanto, il funzionamento non è garantito.

Per i clienti che desiderano accesso immediato alle macchine virtuali, sono disponibili le due opzioni seguenti.

>[AZURE.NOTE]Tenere presente che entrambe le opzioni presentano il rischio di una perdita dei dati.

##Opzione 2: Ripristinare una macchina virtuale (VM) da un backup 
Per i clienti che dispongono di un backup di VM configurato, è possibile ripristinare la VM dal punto di backup e ripristino.

Seguire questa procedura per ripristinare una nuova VM da un backup di Azure. Vedere Ripristinare macchine virtuali in Azure.

Per facilitare la pianificazione per l'infrastruttura di backup di macchine virtuali di Azure, leggere l'articolo [Pianificare l'infrastruttura di backup delle macchine virtuali in Azure](../backup/backup-azure-vms-introduction.md).

##Opzione 3: Avviare un failover usando Azure Site Recovery (ASR) 
Se è stato configurato Azure Site Recovery per lavorare con le macchine virtuali di Azure interessate, è possibile ripristinare le VM dalle rispettive repliche. Queste repliche possono risiedere in Azure o anche in locale. In questo caso, è possibile creare una nuova VM dalla relativa replica esistente. Seguire questa procedura per ripristinare le VM da una replica di Azure Site Recovery. Vedere [Eseguire la migrazione delle macchine virtuali IaaS di Azure tra aree di Azure con Azure Site Recovery](../site-recovery/site-recovery-migrate-azure-to-azure.md).

>[AZURE.NOTE]Sebbene il sistema operativo e i dischi dati delle macchine virtuali di Azure vengano replicati in un disco rigido virtuale (VHD) secondario, se si trovano in un account di archiviazione GRS o RA-GRS, ogni disco rigido virtuale viene replicato in modo indipendente e questo livello di replica non garantisce coerenza tra i dischi rigidi virtuali replicati. Se l'applicazione e/o i database che usano questi dischi dati presentano dipendenze tra loro, non è garantito che tutti i dischi rigidi virtuali vengano replicati come uno snapshot. Inoltre non è garantito che la replica di dischi rigidi virtuali dall'archiviazione GRS o RA-GRS genererà uno snapshot coerente dell'applicazione per l'avvio della VM.

##Riferimenti 
[Ripristino di emergenza e disponibilità elevata per le applicazioni di Azure](https://aka.ms/drtechguide)

[Informazioni tecniche sulla continuità aziendale di Azure](http://aka.ms/bctechguide)

[Eseguire il backup di macchine virtuali di Azure](../backup/backup-azure-vms.md)

[Azure Site Recovery](https://azure.microsoft.com/documentation/learning-paths/site-recovery/)

Se le istruzioni non sono chiare, o se si desidera che Microsoft esegua le operazioni per proprio conto, contattare il [supporto tecnico](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

<!---HONumber=AcomDC_0525_2016-->