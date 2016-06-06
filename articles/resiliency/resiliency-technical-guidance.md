<properties
   pageTitle="Indice delle indicazioni tecniche sulla resilienza | Microsoft Azure"
   description="Indice degli articoli tecnici per comprendere e progettare applicazioni resilienti a disponibilità elevata e tolleranza di errore e per pianificare il ripristino di emergenza e la continuità aziendale"
   services=""
   documentationCenter="na"
   authors="adamglick"
   manager="hongfeig"
   editor=""/>

<tags
   ms.service="resiliency"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/13/2016"
   ms.author="patw;jroth;aglick"/>

#Indicazioni tecniche sulla resilienza di Azure

##Introduzione
Per soddisfare i requisiti di disponibilità elevata e ripristino di emergenza è necessario: 1) avere una conoscenza tecnica approfondita delle funzionalità della piattaforma cloud ed 2) essere in grado di creare correttamente l'architettura di un servizio distribuito. Questa serie di articoli illustra il primo punto, ovvero le funzionalità e le limitazioni della piattaforma Azure rispetto alla resilienza, a volte definita continuità aziendale. Se è interessati al secondo punto, vedere la serie di articoli incentrata su [Ripristino di emergenza e disponibilità elevata per le applicazioni Azure](https://aka.ms/drtechguide). Mentre questa serie di articoli accenna ai modelli di progettazione e architettura, non è l'obiettivo principale della serie. È necessario consultare il materiale nella sezione [Risorse aggiuntive](#additional-resources) per indicazioni sulla progettazione.

Le informazioni sono organizzate negli articoli seguenti:
##[Ripristino da errori locali in Azure](./resiliency-technical-guidance-recovery-local-failures.md)
Durante i picchi di carico si possono verificare errori ai componenti hardware, ad esempio unità, server e dispositivi di rete, o si possono esaurire le risorse disponibili. Questa sezione descrive le funzionalità fornite da Azure per gestire la disponibilità elevata in queste condizioni.

##[Ripristino dopo un'interruzione di servizio di un'area di Azure](./resiliency-technical-guidance-recovery-loss-azure-region.md)
gli errori generalizzati sono rari, ma teoricamente possibili. Intere aree possono risultare isolate a causa di errori di rete oppure fisicamente danneggiate a causa di calamità naturali. Questa sezione illustra come usare le funzionalità di Azure per creare applicazioni da usare in aree geograficamente diverse.

##[Ripristino dall'ambiente locale ad Azure](./resiliency-technical-guidance-recovery-on-premises-azure.md)
Il cloud modifica in modo significativo l'economia del ripristino di emergenza consentendo alle organizzazioni di usare Azure per creare un secondo sito per il ripristino. Questa operazione può essere eseguita a un costo decisamente ridotto rispetto a quello di compilazione e gestione di un data center secondario. Questa sezione descrive le funzionalità fornite da Azure per estendere un data center locale al cloud.

##[Ripristino dal danneggiamento o dall'eliminazione accidentale dei dati](./resiliency-technical-guidance-recovery-data-corruption.md)
Le applicazioni possono contenere bug che danneggiano i dati e gli operatori possono erroneamente eliminare dati importanti. Questa sezione illustra le funzionalità fornite da Azure per il backup dei dati e il ripristino un punto precedente nel tempo.

##Risorse aggiuntive

###[Informazioni tecniche sulla continuità aziendale di Azure](./resiliency-technical-guidance.md)
Un elenco dettagliato dei concetti e delle procedure consigliate per ottenere la continuità aziendale e la resilienza con Microsoft Azure per le applicazioni locali, ibride e nel cloud pubblico.

###[Ripristino di emergenza e disponibilità elevata per le applicazioni basate su Microsoft Azure](./resiliency-disaster-recovery-high-availability-azure-applications.md)
Una panoramica dettagliata delle disponibilità e del ripristino di emergenza. Illustra la replica manuale per i dati di riferimento e transazionali. Le sezioni finali forniscono un riepilogo dei diversi tipi di topologie di ripristino di emergenza estese alle aree di Azure per il massimo livello di disponibilità.

###[Panoramica: Continuità aziendale del cloud e ripristino di emergenza del database con database SQL](../sql-database/sql-database-business-continuity.md)
Illustra esclusivamente le tecniche del database SQL Azure di Azure per la disponibilità, che consistono in sostanza nelle strategie di backup e ripristino. Se si usa il database SQL Azure nel servizio cloud, è consigliabile esaminare questo documento e le relative risorse correlate.

###[Disponibilità elevata e ripristino di emergenza per SQL Server nelle macchine virtuali di Azure](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md)
Descrive le opzioni di disponibilità aperte all'utente quando si usa l'infrastruttura distribuita come servizio (IaaS) per ospitare i servizi di database. Illustra Gruppi di disponibilità AlwaysOn, mirroring del database, log shipping e backup e ripristino. Si noti che sono disponibili anche diverse esercitazioni nella stessa sezione che illustrano come usare queste tecniche

###[Procedure consigliate per la progettazione di servizi su larga scala nei servizi cloud di Azure](https://azure.microsoft.com//blog/best-practices-for-designing-large-scale-services-on-windows-azure/).
Questa risorsa è incentrata sullo sviluppo di architetture cloud altamente scalabili. Molte delle tecniche che si usano per migliorare la scalabilità migliorano anche la disponibilità. Inoltre, se l'applicazione non può essere ridimensionata nel caso di un aumento del carico, la scalabilità diventa un problema di disponibilità.

###[Backup e ripristino per SQL Server in Macchine virtuali di Azure](../virtual-machines/virtual-machines-windows-sql-backup-recovery.md)
Indicazioni tecniche su come eseguire il backup e ripristino di Microsoft SQL Server eseguito in macchine virtuali di Azure.

###[FailSafe: Informazioni aggiuntive per architetture cloud resilienti](https://channel9.msdn.com/Series/FailSafe)
Indicazioni per la creazione di architetture cloud resilienti, linee guida per implementare queste architetture nelle tecnologie Microsoft e soluzioni per l'implementazione di queste architetture per scenari specifici.

##Passaggi successivi
Questo articolo fa parte di una serie relativa alle [indicazioni tecniche sulla resilienza di Azure](./resiliency-technical-guidance.md). Se si è interessati alla lettura di altri articoli di questa serie, iniziare con il prossimo articolo intitolato [Ripristino da errori locali in Azure](./resiliency-technical-guidance-recovery-local-failures.md).

<!---HONumber=AcomDC_0525_2016-->