<properties 
	pageTitle="Informazioni sul database SQL di Azure e su SQL Server in macchine virtuali di Azure | Microsoft Azure" 
	description="Informazioni sul database SQL di Azure e su SQL Server in Macchine virtuali di Azure. Esaminare i vantaggi aziendali comuni per individuare la tecnologia SQL più adatta per l'applicazione." 
	services="sql-database, virtual-machines" 
	documentationCenter="" 
	authors="Selcin" 
	manager="jeffreyg" 
	editor="tysonn"/>

<tags 
	ms.service="sql-database" 
	ms.workload="data-management" 
	ms.tgt_pltfrm="vm-windows-sql-server" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/19/2015" 
	ms.author="selcint"/>

# Informazioni sul database SQL di Azure e su SQL Server in Macchine virtuali di Azure

Microsoft Azure offre due opzioni per l'hosting di SQL Server: [Database SQL di Azure](https://azure.microsoft.com/services/sql-database/) e [SQL Server in macchine virtuali di Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/). In questo articolo vengono esaminate le singole opzioni per vedere come si adattano nel quadro generale della piattaforma dei dati di Microsoft. Quindi, la discussione verrà approfondita prendendo in considerazione i requisiti aziendali alla base della scelta. Indipendentemente dai fattori che determinano la scelta, ad esempio i costi o la gestione ridotta, questo articolo può aiutare a decidere l'approccio ottimale perché descrive le funzionalità offerte da ciascuna soluzione e permette di metterle a confronto con le esigenze aziendali più importanti.

- [Piattaforma dei dati Microsoft](#platform)
- [Informazioni dettagliate sul database SQL di Azure e su SQL Server in una macchina virtuale di Azure](#close)	
- [Motivi aziendali alla base della scelta del database SQL di Azure o di SQL Server in una macchina virtuale di Azure](#business)	
	- [Costi](#cost)
		- [Nozioni di base su fatturazione e licenze](#billing)	
		- [Calcolo del costo totale dell'applicazione](#appcost)	
	- [Amministrazione](#admin)	
	- [Contratto di servizio](#sla)	
	- [Time-to-market](#market)	
- [Riepilogo](#summary)	
- [Riconoscimenti](#ack)	
- [Risorse aggiuntive](#resources)	


##<a name="platform"></a>Piattaforma dei dati Microsoft

Una delle prime cose da comprendere in una discussione in cui Azure viene confrontato con i database SQL Server locali è che è possibile usare tutte le soluzioni. La piattaforma dei dati Microsoft si basa sulla tecnologia SQL Server e la rende disponibile nei computer fisici locali, negli ambienti cloud privati, negli ambienti cloud privati ospitati da terze parti e nel cloud pubblico. Ciò consente di soddisfare numerose esigenze aziendali specifiche grazie a una combinazione di distribuzioni locali e ospitate su cloud usando lo stesso set di prodotti server, strumenti di sviluppo ed esperienza in tutti gli ambienti.

   ![][1]

Come mostrato nel diagramma, ogni offerta può essere caratterizzata dal livello di amministrazione che si ha sull'infrastruttura (asse X) e dal grado di efficienza nei costi raggiunto dal consolidamento e automazione a livello di database (asse Y).

Quando si progetta un'applicazione sono disponibili quattro opzioni di base per l'hosting della parte SQL Server dell'applicazione:

- SQL Server in computer fisici non virtualizzati 
- SQL Server in macchine virtualizzate locali (cloud privato)
- SQL Server in una macchina virtuale di Azure (cloud pubblico)
- Database SQL di Azure (cloud pubblico)

Nelle sezioni seguenti verranno trattati gli ultimi due: il database SQL di Azure e SQL Server in Macchine virtuali di Azure. Inoltre, vengono trattati i vantaggi aziendali più diffusi che consentono di determinare l'opzione ottimale per la propria applicazione.

##<a name="close"></a>Informazioni dettagliate sul database SQL di Azure e su SQL Server in una macchina virtuale di Azure

**Il database SQL di Microsoft Azure (Database SQL di Azure)** è un database relazionale distribuito come servizio che rientra nella categoria di settore *PaaS (Platform as a Service)*. Il database SQL di Azure si basa su hardware e software standardizzati appartenenti, ospitati e mantenuti da Microsoft. Con il database SQL è possibile eseguire operazioni di sviluppo direttamente nel servizio con le funzioni e le funzionalità predefinite. Il database SQL prevede un pagamento in base al consumo con la possibilità di aumentare o diminuire il numero di istanze per una maggiore efficienza.

**SQL Server in una macchina virtuale di Azure (VM)** rientra nella categoria di settore *IaaS (Infrastructure as a Service)* e consente di eseguire SQL Server all'interno di una macchina virtuale nel cloud. Analogamente al database SQL di Azure, si basa su hardware standardizzato appartenente, ospitato e mantenuto da Microsoft. Quando si usa SQL Server in una macchina virtuale, è possibile usare la propria licenza SQL Server in Azure oppure usare una delle immagini preconfigurate di SQL Server nel portale di Azure.

In generale, queste due opzioni SQL sono ottimizzate per scopi diversi:

- Il **database SQL di Azure** è ottimizzato per ridurre al minimo i costi complessivi per il provisioning e la gestione di numerosi database. I costi amministrativi correnti possono essere ridotti perché non è necessario gestire alcuna macchina virtuale, sistema operativo o software del database, incluse le operazioni di aggiornamento, disponibilità elevata e backup. In generale, il database SQL può aumentare significativamente il numero di database gestiti da una singola risorsa IT o di sviluppo.
- **SQL Server in esecuzione in una macchina virtuale di Azure** è ottimizzato per estendere applicazioni SQL Server locali esistenti ad Azure in uno scenario ibrido o per distribuire un'applicazione esistente in Azure in uno scenario di migrazione o di sviluppo/test. Un esempio di scenario ibrido è rappresentato dalla conservazione di repliche del database secondario in Azure tramite la [rete virtuale di Azure](../virtual-network/virtual-networks-overview.md). SQL Server in Macchine virtuali di Azure offre diritti amministrativi completi su un'istanza di SQL Server dedicata e su una macchina virtuale basata sul cloud. È la scelta ideale quando un'organizzazione ha già delle risorse IT disponibili per mantenere le macchine virtuali. Con SQL Server in una macchina virtuale è possibile creare un sistema altamente personalizzato per soddisfare gli specifici requisiti dell'applicazione in termini di prestazioni e disponibilità.

La seguente tabella riepiloga le caratteristiche principali del database SQL di Azure e di SQL Server in una macchina virtuale di Azure:

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle"></th>
   <th align="left" valign="middle">Database SQL di Azure</th>
   <th align="left" valign="middle">SQL Server in una macchina virtuale Azure</th>
   
</tr>
<tr>
   <td valign="middle"><p><b>Ideale per</b></p></td>
   <td valign="middle">
          <ul>
          <li type=round>Nuove applicazioni progettate per il cloud con vincoli di tempo per lo sviluppo e il marketing. 
          <li type=round>Applicazioni che richiedono meccanismi automatici predefiniti per l'elevata disponibilità, il ripristino di emergenza e l'aggiornamento.
          <li type=round>Se si hanno centinaia o migliaia di database ma non si vuole gestire il sistema operativo sottostante, l'hardware e le impostazioni di configurazione. 
         <li type=round>Applicazioni che usano modelli per ridurre il numero di istanze.
         <li type=round>Database con dimensioni fino a 500 GB.
         <li type=round>Compilazione di applicazioni Software-as-a-Service.
         
  </ul>
</td>
   <td valign="middle">
      <ul>
      <li type=round>Applicazioni esistenti che richiedono una migrazione rapida al cloud con poche modifiche.
      <li type=round>Applicazioni SQL Server che richiedono l'accesso da Azure alle risorse locali, ad esempio Active Directory, tramite un tunnel sicuro. 
      <li type=round>Se è necessario un ambiente IT personalizzato con diritti amministrativi completi.
      <li type=round>Scenari di sviluppo e test rapidi quando non si vuole acquistare hardware SQL Server locale non di produzione.
      <li type=round>Ripristino di emergenza per le applicazioni di SQL Server locale usando <a href="http://msdn.microsoft.com/library/jj919148.aspx">backup in Archiviazione di Azure</a> o <a href="https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions">repliche AlwaysOn in Macchine virtuali di Azure</a>.
      <li type=round>Grandi database con dimensioni maggiori di 1 TB.
      </ul></td>
   
</tr>
<tr>
   <td valign="middle"><p><b>Risorse</b></p></td>
   <td valign="middle">
       <ul>
       <li type=round>Non si vogliono usare risorse IT per il supporto e la manutenzione dell'infrastruttura sottostante.
       <li type=round>Ci si vuole concentrare sul livello dell'applicazione.
       </ul></td>
   <td valign="middle"><ul><li type=round>Si hanno risorse IT per il supporto e la manutenzione.</ul></td>
   
</tr>
<tr>
   <td valign="middle"><p><b>Costo totale di proprietà</b></p></td>
   <td valign="middle"><ul><li type=round>Elimina i costi associati all'hardware. Riduce i costi amministrativi.</ul></td>
   <td valign="middle"><ul><li type=round>Elimina i costi associati all'hardware. </ul></td>
   
</tr>
<tr>
   <td valign="middle"><p><b>Continuità aziendale</b></p></td>
   <td valign="middle"><ul><li type=round>Oltre alle capacità di tolleranza di errore predefinite dell'infrastruttura, il database SQL di Azure fornisce funzionalità come Ripristino temporizzato, Ripristino a livello geografico e Replica geografica per migliorare la continuità aziendale. Per altre informazioni, vedere <a href="http://msdn.microsoft.com/library/azure/hh852669.aspx">Continuità aziendale del database SQL di Azure</a>.</ul></td>
   <td valign="middle"><ul><li type=round>SQL Server in una macchina virtuale di Azure consente di configurare una soluzione di disponibilità elevata e di ripristino di emergenza per le esigenze specifiche del database. È quindi possibile avere un sistema altamente ottimizzato per la propria applicazione. È possibile testare ed eseguire i failover autonomamente quando necessario. Per altre informazioni, vedere <a href="https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions">Disponibilità elevata e ripristino di emergenza di SQL Server in Macchine virtuali di Azure</a>.</ul></td>
   
</tr>
<tr>
   <td valign="middle"><p><b>Cloud ibrido</b></p></td>
   <td valign="middle"><ul><li type=round>L'applicazione locale può accedere ai dati nel database SQL di Azure.</ul></td>
   <td valign="middle"><ul>
      <li type=round>Con SQL Server in Macchine virtuali di Azure, le applicazioni possono essere eseguite in parte nel cloud e in parte localmente. Ad esempio, è possibile estendere la rete locale e Active Domain Directory nel cloud tramite i <a href="https://azure.microsoft.com/documentation/articles/virtual-networks-overview/">servizi di rete di Azure</a>. Inoltre è possibile archiviare i file di dati locali nell'archiviazione di Azure usando la funzionalità <a href="http://msdn.microsoft.com/library/dn385720.aspx">File di dati di SQL Server in Azure</a>. Per altre informazioni, vedere <a href="http://msdn.microsoft.com/library/dn606154.aspx">Introduzione al cloud ibrido di SQL Server 2014</a>.
      <li type=round>Supporta il ripristino di emergenza per le applicazioni di SQL Server locale usando <a href="http://msdn.microsoft.com/library/jj919148.aspx">backup in Archiviazione di Azure</a> o <a href="https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions">repliche AlwaysOn in Macchine virtuali di Azure</a>.
      </ul></td>
   
</tr>
</table>

##<a name="business"></a>Motivi aziendali alla base della scelta del database SQL di Azure o di SQL Server in una macchina virtuale di Azure

###<a name="cost"></a>Costi

Una startup con pochi fondi o un team di un'azienda consolidata con vincoli di budget: due esempi in cui la limitata disponibilità economica rappresenta un fattore primario nella scelta dell'hosting dei database. In questa sezione verranno fornite le nozioni di base sulla fatturazione e le licenze in Azure relative alle due opzioni di database relazionali: database SQL di Azure e SQL Server in una macchina virtuale di Azure. Quindi verrà spiegato come calcolare il costo totale dell'applicazione.

####<a name="billing"></a>Nozioni di base su fatturazione e licenze

Il **database SQL di Azure** viene venduto ai clienti come servizio e non con una licenza, mentre SQL Server in una macchina virtuale di Azure richiede la tradizionale licenza di SQL Server.

Al momento il **database SQL di Azure** è disponibile in diversi livelli di servizio. Per i livelli di servizio Basic, Standard e Premium la fatturazione è oraria e basata su una tariffa fissa associata ai livelli di servizio e di prestazioni prescelti. I livelli di servizio Basic, Standard e Premium sono progettati per offrire prestazioni prevedibili con più livelli di prestazioni in modo da soddisfare i requisiti massimi dell'applicazione. È possibile modificare i livelli di servizio e i livelli di prestazioni per soddisfare le diverse esigenze di velocità effettiva dell'applicazione. Per le informazioni più recenti sui livelli di servizio attualmente supportati, vedere [Livelli di servizio (edizioni) del database SQL di Azure](sql-database-service-tiers.md).

Con il **database SQL di Azure**, Microsoft Azure configura, applica le patch ed esegue l'aggiornamento del software del database automaticamente nei data center di tutto il mondo. Quindi i costi amministrativi risultano ridotti. Inoltre, le capacità di [backup predefinite](http://msdn.microsoft.com/library/azure/jj650016.aspx) consentono di ottenere una significativa riduzione dei costi, specialmente se il numero di database è elevato. Quando si usa il database SQL di Azure, le singole query nel database SQL di Azure o il traffico Internet in entrata o in uscita non vengono addebitati. Viene fatturato[il traffico Internet in uscita](http://azure.microsoft.com/pricing/details/data-transfers/). Se il database ha un volume transazionale elevato e deve supportare più utenti simultanei, si consiglia di usare il livello di servizio Premium invece che Basic o Standard.

Con **SQL Server in una macchina virtuale di Azure** si usano le tradizionali licenze di SQL Server. È possibile usare l'immagine SQL Server fornita dalla piattaforma o la propria licenza SQL Server in Azure. Quando si usano le immagini fornite dalla piattaforma SQL Server, il costo dipende dalle dimensioni della macchina virtuale e dalla versione scelta di SQL Server. In sostanza, si paga un costo di licenza al minuto per SQL Server, un costo di licenza al minuto per Windows Server e il costo per l'archiviazione di Azure. L'opzione di fatturazione al minuto consente di usare SQL Server per tutto il tempo necessario senza dover acquistare la licenza di SQL Server completa. Se si dispone già di una licenza di SQL Server vengono addebitati solo i costi di calcolo e di archiviazione di Azure. Per altre informazioni, vedere [Mobilità delle licenze tramite Software Assurance in Azure](http://azure.microsoft.com/pricing/license-mobility/).

####<a name="appcost"></a>Calcolo del costo totale dell'applicazione

Quando si inizia a usare una piattaforma cloud, il costo per l'esecuzione dell'applicazione include generalmente lo sviluppo e i costi amministrativi, oltre ai costi del servizio richiesti dalla piattaforma cloud pubblica.

Di seguito è illustrato il calcolo dei costi per l'applicazione eseguita nel database SQL di Azure e in SQL Server in una macchina virtuale di Azure:

**Quando si usa il database SQL di Azure:**

*Costo totale dell'applicazione = Costi amministrativi molto ridotti + costi per lo sviluppo software + costi per il servizio del database SQL di Azure*

**Quando si usa SQL Server in una macchina virtuale di Azure:**

*Costo totale dell'applicazione = Costi ridotti per lo sviluppo/modifica del software + costi amministrativi + costi di licenza per SQL Server e Windows Server + costi per l'archiviazione di Azure*

> [AZURE.IMPORTANT]Al momento il database SQL di Azure non supporta tutte le funzionalità di SQL Server. Per un confronto dettagliato, vedere [Linee guida e limitazioni per il database SQL di Azure](http://msdn.microsoft.com/library/azure/ff394102.aspx). Tenerlo presente quando si sposta un database esistente nel database SQL di Azure perché potrebbe essere necessario un budget aggiuntivo per la riprogettazione del database. Il database SQL di Azure è l'offerta PaaS (Platform as a Service) di Microsoft. Quando si esegue la migrazione di un'applicazione SQL Server locale esistente nel database SQL di Azure, si consiglia di aggiornare l'applicazione per sfruttare tutti i vantaggi dell'offerta PaaS. Ad esempio, iniziare a usare [Siti Web di Azure](http://azure.microsoft.com/documentation/services/websites/) o [Servizi cloud di Azure](http://azure.microsoft.com/services/cloud-services/) al livello dell'applicazione per aumentare i vantaggi in termini di costo. Inoltre, convalidare l'applicazione sulla base di diversi livelli di servizio del database SQL di Azure e verificare quale soddisfa al meglio le esigenze dell'applicazione. Questo processo consente di ottenere risultati migliori in termini di prestazioni e costi ridotti. Per ulteriori informazioni, vedere [Livelli di servizio e livelli di prestazioni del database SQL di Azure](sql-database-service-tiers.md).

Per una stima dei costi dettagliata, usare il [Calcolatore dei costi di Azure](http://azure.microsoft.com/pricing/calculator/).

Per altre informazioni sui prezzi, vedere le seguenti risorse:

- [Dettagli prezzi per il database SQL di Azure](http://azure.microsoft.com/pricing/details/sql-database/) 
- [Dettagli prezzi per le macchine virtuali](http://azure.microsoft.com/pricing/details/virtual-machines/)
- [SQL Server in Macchine virtuali di Azure - Dettagli prezzi](http://azure.microsoft.com/pricing/details/virtual-machines/#sql-server)
- [Windows Server in Macchine virtuali di Azure - Dettagli prezzi](http://azure.microsoft.com/pricing/details/virtual-machines/#windows) 

###<a name="admin"></a>Amministrazione

Se si seguono già molte attività, aggiungere anche l'amministrazione di server e database non è una soluzione auspicabile. Per molte aziende, la decisione di passare a un servizio cloud riguarda esclusivamente la possibilità di ridurre la complessità dell'amministrazione. Con il **database SQL di Azure**, Microsoft amministra l'hardware fisico (ad esempio i dischi rigidi, i server e l'archiviazione), replica automaticamente tutti i dati per fornire la disponibilità elevata, configura e aggiorna il software del database, gestisce il bilanciamento del carico ed esegue un failover trasparente in caso di problemi del server. È possibile continuare ad amministrare le istanze del database SQL di Azure, ma senza controllare le risorse fisiche nell'istanza sottostante di SQL Server o della piattaforma Azure. Ad esempio, è possibile amministrare i database e gli accessi e ottimizzare l'indice e le query, ma non è possibile amministrare la gestione delle tabelle di sistema e dei filegroup. Per altre informazioni, vedere [Linee guida e limitazioni per il database SQL di Azure](http://msdn.microsoft.com/library/ff394102.aspx).

D'altra parte, se si hanno risorse interne esperte, si potrebbe voler mantenere il controllo su tutto il percorso del database fino allo stesso computer. Con **SQL Server in esecuzione in una macchina virtuale di Azure**, si ha il pieno controllo del sistema operativo e della configurazione dell'istanza di SQL Server. Con una macchina virtuale, l'utente può decidere quando aggiornare il sistema operativo e il software del database e quando installare software aggiuntivi come gli strumenti anti-virus e di backup. Inoltre, è possibile controllare le dimensioni della macchina virtuale, il numero di dischi e le relative configurazioni di archiviazione. Ad esempio, Azure consente di modificare le dimensioni di una macchina virtuale in esecuzione in base alle esigenze. Per informazioni, vedere [Dimensioni delle macchine virtuali e del servizio cloud per Azure](../virtual-machines/virtual-machines-size-specs.md).

###<a name="sla"></a>Contratto di servizio (SLA)

Per alcuni di noi, rispettare gli obblighi relativi al tempo di attività di un contratto di servizio è della massima priorità. Questa sezione spiega le condizioni del contratto di servizio per ciascuna opzione di hosting del database.

Per il **database SQL di Azure**, considerati i livelli di servizio Basic, Standard e Premium, Microsoft fornisce un contratto di servizio con disponibilità al 99,99%. Questo contratto si riferisce alla capacità di connettersi al database. In altre parole, è un contratto di servizio a livello del database. Per le informazioni più recenti sui contratti di servizio, vedere [Contratto di servizio](http://azure.microsoft.com/support/legal/sla/). Per le informazioni più recenti sui livelli di servizio (edizioni) del database SQL di Azure e sui piani di continuità aziendale supportati, vedere [Livelli di servizio (edizioni) del database SQL di Azure](sql-database-service-tiers.md).

Per le **macchine virtuali ospitate in Azure**, Microsoft fornisce un contratto di servizio con disponibilità al 99,95%; tale disponibilità riguarda la macchina virtuale, non i processi in esecuzione al suo interno, ad esempio SQL Server. Il [contratto di servizio della macchina virtuale](http://www.microsoft.com/download/details.aspx?id=38427) richiede che siano ospitate almeno due macchine virtuali in un set di disponibilità. Con questa configurazione, Azure garantisce una disponibilità del 99,95% per almeno una delle macchine virtuali. Per la disponibilità elevata (HA) del database all'interno delle macchine virtuali, è necessario configurare una delle opzioni di disponibilità elevata supportate in SQL Server, ad esempio Gruppi di disponibilità AlwaysOn. La configurazione di AlwaysOn in Azure richiede alcune operazioni manuali di configurazione e gestione ed è previsto un costo aggiuntivo per ogni macchina secondaria.


###<a name="market"></a>Time-to-market

Il **database SQL di Azure** è la soluzione ideale per le applicazioni progettate per il cloud quando i fattori critici sono la produttività degli sviluppatori e un time-to-market rapido. Con la funzionalità di tipo DBA programmatica, è lo strumento ideale per gli architetti e gli sviluppatori cloud perché riduce la gestione del sistema operativo e del database sottostanti. Aiuta gli sviluppatori a comprendere e configurare le attività relative al database. Ad esempio, è possibile usare le cmdlets [API REST](http://msdn.microsoft.com/library/azure/dn505719.aspx) e [PowerShell](http://msdn.microsoft.com/library/azure/dn546726.aspx) per automatizzare e gestire le operazioni amministrative di migliaia di database. Con la [scalabilità elastica](sql-database-elastic-pool.md) nel cloud, è possibile concentrarsi sul livello dell'applicazione e rilasciare più rapidamente l'applicazione sul mercato.

Un **Server SQL in esecuzione in una macchina virtuale di Azure** rappresenta la soluzione ottimale se le nuove applicazioni o quelle esistenti richiedono l'accesso e il controllo di tutte le funzionalità di un'istanza di SQL Server e se si desidera eseguire la migrazione nel cloud delle applicazioni e dei database locali esistenti, senza modificarli. Poiché non è necessario modificare la presentazione, l'applicazione e i livelli dati, si risparmia il tempo e i costi legati a una nuova progettazione dell'architettura della soluzione esistente. In questo modo, è possibile concentrarsi sulla migrazione di tutti i pacchetti della soluzione alle macchine virtuali e sull'ottimizzazione delle prestazioni richiesta dalla piattaforma Azure. Per altre informazioni, vedere [Procedure consigliate per le prestazioni per SQL Server in Macchine virtuali di Azure](../virtual-machines/virtual-machines-sql-server-performance-best-practices.md).

##<a name="summary"></a>Riepilogo

In questo articolo sono stati descritti: database SQL di Azure e SQL Server in una macchina virtuale di Azure. Inoltre, si è discusso dei vantaggi aziendali più diffusi che possono influire sulla decisione della soluzione da adottare.

Di seguito è presentato un riepilogo dei suggerimenti da tenere presenti quando si sceglie quale soluzione usare:

Scegliere il **database SQL di Azure** se:

- Si stanno compilando nuove applicazioni basate sul cloud o si vuole eseguire la migrazione del database SQL Server esistente in Azure e il database non usa una delle funzionalità non supportate nel database SQL di Azure. Per altre informazioni, vedere [Guida di riferimento per Transact-SQL del database SQL di Azure](http://msdn.microsoft.com/library/azure/ee336281.aspx). Questo approccio offre tutti i vantaggi di un servizio cloud completamente gestito e assicura un rapido time-to-market.

- Si vuole che Microsoft si occupi di operazioni di gestione comuni sui database e si richiedono contratti di servizio con disponibilità più elevata per i database. Questo approccio consente di ridurre i costi amministrativi e, contemporaneamente, fornisce una disponibilità garantita per il database.

    [Creare il primo database SQL di Azure](sql-database-get-started.md)


Scegliere **SQL Server in una macchina virtuale di Azure** se:

- Si hanno applicazioni locali esistenti e non si vuole più mantenere il proprio hardware oppure si stanno prendendo in considerazione delle soluzioni ibride. Questo approccio consente di accedere più rapidamente a capacità del database elevate e di connettere le applicazioni locali al cloud tramite un tunnel sicuro.

- Si hanno risorse IT esistenti, sono necessari diritti amministrativi completi per SQL Server ed è richiesta una compatibilità totale con SQL Server locale (ad esempio, se alcune funzionalità non esistono nel database SQL di Azure). Questo approccio consente di ridurre i costi per lo sviluppo o la modifica delle applicazioni esistenti e offre la flessibilità necessaria ad eseguire la maggior parte delle applicazioni. Inoltre, fornisce il controllo totale sulla macchina virtuale, sul sistema operativo e sulla configurazione del database.

    [Provisioning di una macchina virtuale di SQL Server in Azure](virtual-machines-provision-sql-server.md)

> [AZURE.NOTE]Se si desidera provare SQL Server 2016 CTP2, iscriversi a Microsoft Azure e fare clic [qui](http://aka.ms/sql2016vm "qui") per creare una macchina virtuale con SQL Server 2016 CTP2 già installato.


##<a name="ack"></a>Riconoscimenti

Questo articolo del gruppo Microsoft Cloud and Enterprise Content Services è stato elaborato grazie all'aiuto di molte persone della community Microsoft.

**Autore:** Selcin Turkarslan

**Collaboratori tecnici:** Conor Cunningham

**Revisori tecnici:** Joanne Marone (Hodgins), Karthika Raman, Lindsey Allen, Lori Clark, Luis Carlos Vargas Herring, Nosheen Syed Wajahatulla Hussain, Pravin Mittal, Shawn Bice, Silvano Coriani, Tony Petrossian, Tracy Daugherty.

**Revisori editoriali:** Heidi Steen, Maggie Sparkman.

Grazie a tutti per la collaborazione!

##<a name="resources"></a>Risorse aggiuntive 

<table cellspacing="0" border="1">
<tr>
   <th align="left" valign="middle">Risorsa</th>
   <th align="left" valign="middle">Descrizione</th>
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/library/azure/ee336279.aspx">MSDN: database SQL di Azure</a></p>
<p><a href="https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-infrastructure-services/">Panoramica di SQL Server in macchine virtuali di Azure</a></p>

<p><a href="http://azure.microsoft.com/services/sql-database/">Azure.com: database SQL di Azure</a></p></td>
   <td valign="middle">Collegamenti alla documentazione della libreria.</td>   
</tr>
<tr>
   <td valign="middle"><p><a href="https://azure.microsoft.com/documentation/articles/virtual-machines-sql-server-application-patterns-and-development-strategies/">Modelli di applicazione e strategie di sviluppo per SQL Server in Macchine virtuali di Azure</p></td>
   <td valign="middle">Questo articolo descrive i modelli dell'applicazione più diffusi applicabili a SQL Server in Macchine virtuali di Azure e gli scenari ibridi incluso il database SQL di Azure. </td>   
</tr>
<tr>
   <td valign="middle"><p><a href="http://msdn.microsoft.com/library/hh680934(v=PandP.50).aspx">Blocco applicazione per la gestione degli errori temporanei di Microsoft Enterprise Library</p></td>
   <td valign="middle">Questa libreria consente agli sviluppatori di rendere più resilienti le applicazioni in esecuzione nel database SQL di Azure aggiungendo un'affidabile logica di gestione degli errori temporanei. Gli errori temporanei sono dovuti a eventi temporanei, ad esempio problemi di connettività di rete o non disponibilità del servizio. Poiché il database SQL di Azure è un servizio multi-tenant, è importante gestire questi errori per ridurre gli eventuali tempi di inattività dell'applicazione. </td>   
</tr>
</table>

<!--Image references-->
[1]: ./media/data-management-azure-sql-database-and-sql-server-iaas/SQLIAAS_SQL_Server_Cloud_Continuum.png

<!---HONumber=Oct15_HO4-->