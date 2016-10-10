<properties
   pageTitle="Disponibilità elevata per le applicazioni Azure | Microsoft Azure"
   description="Panoramiche tecniche e informazioni approfondite sulla progettazione e la creazione di applicazioni per la disponibilità elevata in Microsoft Azure."
   services=""
   documentationCenter="na"
   authors="adamglick"
   manager="saladki"
   editor=""/>

<tags
   ms.service="resiliency"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/18/2016"
   ms.author="aglick"/>

#Disponibilità elevata per le applicazioni basate su Microsoft Azure

Un'applicazione a disponibilità elevata assorbe le fluttuazioni di disponibilità, carico ed errori temporanei nell'hardware e nei servizi dipendenti. L'applicazione continua a funzionare con un livello di risposta utente e di sistema accettabile in base a quanto definito dai requisiti aziendali o dai contratti di servizio dell'applicazione.

##Funzionalità a disponibilità elevata di Azure

Azure offre numerose funzionalità predefinite della piattaforma che supportano applicazioni a disponibilità elevata. Questa sezione descrive alcune di queste funzionalità chiave. Per un'analisi più completa della piattaforma, vedere [Informazioni tecniche sulla resilienza di Azure](./resiliency-technical-guidance.md).

###Controller di infrastruttura

Il controller di infrastruttura di Azure gestisce il provisioning e il monitoraggio delle condizioni delle istanze di calcolo di Azure. Controlla lo stato dell'hardware e del software delle istanze delle macchine host e guest. Quando rileva un errore, applica i contratti di servizio rilocando automaticamente le istanze delle VM. Il contratto di servizio di calcolo è ulteriormente supportato dal concetto dei domini di aggiornamento e di errore.

Quando vengono distribuite più istanze del ruolo del servizio cloud, Azure distribuisce queste istanze in domini di errore diversi. Il limite di un dominio di errore è essenzialmente un diverso rack hardware nella stessa area. I domini di errore riducono le probabilità che un errore hardware localizzato interrompa il servizio di un'applicazione. Non è possibile gestire il numero di errori di dominio allocati ai ruoli Web o di lavoro. Il controller di infrastruttura usa risorse dedicate separate dalle applicazioni ospitate in Azure. Offre un tempo di attività del 100% perché funge da nucleo del sistema Azure e monitora e gestisce le istanze del ruolo nei domini di errore.

La figura seguente illustra le risorse condivise di Azure distribuite e gestite dal controller di infrastruttura nei diversi domini di errore.

![Vista semplificata dell'isolamento dei domini di errore](./media/resiliency-high-availability-azure-applications/fault-domain-isolation.png)

I domini di aggiornamento hanno una funzione simile ai domini di errore, ma supportano gli aggiornamenti anziché gli errori. Un dominio di aggiornamento è un'unità logica di separazione delle istanze che determina quali istanze in un particolare servizio verranno aggiornate in uno specifico momento. Per impostazione predefinita, per la distribuzione del servizio ospitato vengono definiti cinque domini di aggiornamento. È tuttavia possibile modificare tale valore nel file di definizione del servizio. Si supponga ad esempio di avere otto istanze del ruolo Web. Saranno presenti due istanze in tre domini di aggiornamento e due istanze in un dominio di aggiornamento. Azure definisce la sequenza di aggiornamento, che è tuttavia basata sul numero di domini di aggiornamento. Per altre informazioni sui domini di aggiornamento, vedere [Come aggiornare un servizio cloud](../cloud-services/cloud-services-update-azure-service.md).

###Funzionalità in altri servizi

Oltre a queste funzionalità della piattaforma che supportano la disponibilità elevata a livello di calcolo, Azure incorpora funzionalità di disponibilità elevata negli altri servizi. Il servizio di archiviazione di Azure gestisce ad esempio tre repliche di tutti i dati di BLOB, tabelle e code. Offre anche l'opzione della replica geografica per archiviare i backup di BLOB e tabelle in un'area secondaria. La rete per la distribuzione di contenuti di Azure consente di memorizzare i BLOB nella cache in tutto il mondo ai fini sia della ridondanza sia della scalabilità. Anche il database SQL di Azure gestisce più repliche.

Oltre alla serie di articoli [Informazioni tecniche sulla resilienza](https://aka.ms/bctechguide), vedere il white paper [Procedure consigliate per la progettazione di servizi su larga scala nei servizi cloud di Azure](https://azure.microsoft.com/blog/best-practices-for-designing-large-scale-services-on-windows-azure/). Questi documenti offrono un esame più approfondito delle funzionalità di disponibilità della piattaforma Azure.

Nonostante Azure offra più funzionalità che supportano la disponibilità elevata, è importante conoscerne le limitazioni:

- Per il calcolo, Azure garantisce che i ruoli siano disponibili e in esecuzione, ma non rileva se l'applicazione è in esecuzione o sovraccarica.
- Per il database SQL di Azure, i dati vengono replicati in modo sincrono all'interno dell'area. È possibile scegliere la replica geografica attiva, che consente un massimo di quattro copie aggiuntive del database nella stessa area (o in aree diverse). Le repliche del database non sono backup temporizzati. I database SQL offrono funzionalità di backup point-in-time. Per ulteriori informazioni sulle funzionalità point-in-time del database SQL, leggere [Ripristino temporizzato del database SQL di Azure](https://azure.microsoft.com/blog/azure-sql-database-point-in-time-restore/).
- Per Archiviazione di Azure, i dati di BLOB e tabelle vengono replicati per impostazione predefinita in un'area alternativa. Non è tuttavia possibile accedere alle repliche finché Microsoft non sceglie di eseguire il failover nel sito alternativo. Il failover di un'area viene eseguito solo in caso di interruzione prolungata del servizio a livello di area e non esistono contratti di servizio per il tempo di failover geografico. È anche importante notare che qualsiasi danneggiamento dei dati si diffonde rapidamente alle repliche.

Per questi motivi, è necessario integrare le funzionalità di disponibilità della piattaforma con funzionalità di disponibilità specifiche dell'applicazione. Le funzionalità di disponibilità dell'applicazione includono la funzionalità di snapshot di BLOB per creare backup temporizzati dei dati BLOB.

###Set di disponibilità per le macchine virtuali di Azure

Questo articolo è prevalentemente incentrato sui servizi cloud, che usano un modello di piattaforma distribuita come servizio (PaaS). Esistono tuttavia anche funzionalità di disponibilità specifiche per le macchine virtuali di Azure, che usano un modello di infrastruttura distribuita come servizio (IaaS). Per ottenere la disponibilità elevata con le macchine virtuali, è necessario usare set di disponibilità. Un set di disponibilità svolge una funzione simile ai domini di errore e di aggiornamento. All'interno di un set di disponibilità, Azure posiziona le macchine virtuali in modo da impedire che attività di manutenzione ed errori hardware localizzati arrestino tutte le macchine del gruppo. I set di disponibilità sono necessari per soddisfare i requisiti del contratto di servizio di Azure per la disponibilità delle macchine virtuali.

La figura seguente rappresenta due set di disponibilità che raggruppano rispettivamente macchine virtuali Web e di SQL Server.

![Set di disponibilità per le macchine virtuali di Azure](./media/resiliency-high-availability-azure-applications/availability-set-for-azure-virtual-machines.png)

>[AZURE.NOTE] Nella figura precedente, SQL Server è installato ed eseguito in macchine virtuali. Nella precedente discussione riguardo al database SQL di Azure, invece, il database viene fornito come servizio gestito.

##Strategie delle applicazioni per la disponibilità elevata

La maggior parte delle strategie delle applicazioni per la disponibilità elevata prevedono la ridondanza o la rimozione di dipendenze rigide tra i componenti dell'applicazione. La progettazione dell'applicazione deve supportare la tolleranza di errore durante sporadici tempi di inattività di Azure o di servizi di terze parti. Le sezioni seguenti descrivono diversi modelli delle applicazioni per aumentare la disponibilità dei servizi cloud.

###Comunicazione asincrona e code durevoli

Per aumentare la disponibilità delle applicazioni Azure, prendere in considerazione la comunicazione asincrona tra servizi a regime di controllo libero. In questo modello, i messaggi vengono scritti nelle code di archiviazione o nelle code del bus di servizio di Azure per l'elaborazione successiva. Quando si scrive il messaggio nella coda, il controllo torna immediatamente al mittente del messaggio. L'elaborazione dei messaggi viene gestita da un altro livello dell'applicazione, in genere implementato come ruolo di lavoro. Se il ruolo di lavoro diventa inattivo, i messaggi si accumulano nella coda finché non viene ripristinato il servizio di elaborazione. Finché la coda è disponibile, non esiste alcuna dipendenza diretta tra mittente front-end ed elaboratore dei messaggi. Viene così eliminato il requisito delle chiamate sincrone ai servizi, che nelle applicazioni distribuite possono costituire un collo di bottiglia per la velocità effettiva.

Una variante usa le code di Archiviazione di Azure (BLOB, tabelle, code) o del bus di servizio come posizione di failover per le chiamate al database non riuscite. Se una chiamata sincrona all'interno di un'applicazione verso un altro servizio (come il database SQL di Azure) ha ripetutamente esito negativo, ad esempio, è possibile serializzare tali dati in una risorsa di archiviazione durevole. Successivamente, quando il servizio o il database è di nuovo online, l'applicazione può inviare nuovamente la richiesta dalla risorsa di archiviazione. Questo modello si differenzia perché la posizione intermedia non è una parte costante del flusso di lavoro dell'applicazione. Viene usata solo in scenari di errore.

In entrambi gli scenari, la comunicazione asincrona e l'archiviazione intermedia impediscono a un servizio back-end inattivo di arrestare l'intera applicazione. Le code fungono da intermediario logico. Per altre indicazioni sulla scelta del servizio di accodamento corretto, vedere [Analogie e differenze tra le code di Azure e le code del bus di servizio](../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md).

###Rilevamento degli errori e logica di ripetizione dei tentativi

Un punto chiave nella progettazione di applicazioni a disponibilità elevata è l'uso nel codice di logica di ripetizione dei tentativi per gestire correttamente un servizio temporaneamente inattivo. Il [Blocco di applicazioni per la gestione degli errori temporanei](https://msdn.microsoft.com/library/hh680934.aspx), sviluppato dal team Microsoft Patterns & Practices, assiste gli sviluppatori di applicazioni in questo processo. Il termine "temporaneo" indica una condizione che dura solo per un periodo di tempo relativamente breve. Nel contesto di questo articolo, la gestione degli errori temporanei fa parte dello sviluppo di un'applicazione a disponibilità elevata. Le condizioni temporanee includono, ad esempio, gli errori di rete intermittente e la perdita delle connessioni ai database.

Il blocco applicazione di gestione degli errori temporanei semplifica la gestione corretta degli errori nel codice. È possibile usarlo per migliorare la disponibilità delle applicazioni aggiungendo un'affidabile logica di gestione degli errori temporanei. Nella maggior parte dei casi, la logica di ripetizione dei tentativi gestisce una breve interruzione e riconnette il mittente e il destinatario dopo uno o più tentativi non riusciti. Una ripetizione dei tentativi riuscita non viene in genere rilevata dagli utenti dell'applicazione.

Per gestire la logica di ripetizione dei tentativi, esistono tre opzioni per gli sviluppatori: ripetizione incrementale, a intervalli fissi ed esponenziale. La ripetizione incrementale prevede prima di ogni tentativo un'attesa più lunga che aumenta in modo lineare (ad esempio, 1, 2, 3 e 4 secondi). La ripetizione a intervalli fissi prevede la stessa attesa tra i singoli tentativi (ad esempio, 2 secondi). Un'opzione più casuale è rappresentata dal backoff esponenziale, che prevede un'attesa più lunga tra i tentativi ma usa un comportamento esponenziale (ad esempio, 2, 4, 8 e 16 secondi).

La strategia di alto livello nel codice è

1. Definire una strategia e un criterio di ripetizione dei tentativi.
1. Provare a eseguire l'operazione che potrebbe causare un errore temporaneo.
1. Se si verifica l'errore temporaneo, richiamare il criterio di ripetizione dei tentativi.
1. Se tutti i tentativi hanno esito negativo, rilevare un'eccezione finale.

Testare la logica di ripetizione dei tentativi in errori simulati per verificare che le ripetizioni dei tentativi su operazioni successive non causino un lungo ritardo imprevisto. Eseguire questi test prima di determinare l'esito negativo dell'attività complessiva.

###Modello dei dati di riferimento per la disponibilità elevata

I dati di riferimento sono i dati di sola lettura di un'applicazione. Questi dati forniscono il contesto aziendale all'interno del quale l'applicazione genera dati transazionali nel corso di un'operazione aziendale. I dati transazionali sono una funzione temporizzata dei dati di riferimento. Di conseguenza, la loro integrità dipende dallo snapshot dei dati di riferimento al momento della transazione. Questa definizione, per quanto approssimativa, dovrebbe essere sufficiente per gli scopi di questo articolo.

Nel contesto di un'applicazione, i dati di riferimento sono necessari per il funzionamento dell'applicazione. Le rispettive applicazioni creano e gestiscono i dati di riferimento. Questa funzione è spesso svolta da sistemi di gestione dei dati master (MDM), Questi sistemi gestiscono il ciclo di vita dei dati di riferimento. I dati di riferimento includono, ad esempio, il catalogo prodotti e i master di dipendenti, parti e attrezzature. I dati di riferimento possono anche avere origine all'esterno dell'organizzazione, come nel caso di CAP e aliquote d'imposta. Le strategie per aumentare la disponibilità dei dati di riferimento sono in genere meno difficoltose di quelle per i dati transazionali. I dati di riferimento hanno il vantaggio di essere prevalentemente immutabili.

I ruoli Web e di lavoro di Azure che usano i dati di riferimento possono essere resi autonomi in fase di esecuzione distribuendo i dati di riferimento con l'applicazione. Se le dimensioni della risorsa di archiviazione locale consentono una distribuzione di questo tipo, è uno stato ideale. L'autonomia delle unità di scala di calcolo di Azure è consentita da database incorporati (SQL, NoSQL) o file XML distribuiti in un file system locale. È tuttavia consigliabile usare un meccanismo per aggiornare i dati in ogni ruolo senza che sia necessaria la ridistribuzione. A tale scopo, inserire tutti gli aggiornamenti dei dati di riferimento in un endpoint di archiviazione cloud (ad esempio, il database SQL o l'archivio BLOB di Azure). Aggiungere a ogni ruolo codice che scarica gli aggiornamenti dei dati nei nodi di calcolo all'avvio del ruolo. In alternativa, aggiungere codice che consente a un amministratore di eseguire un download forzato nelle istanze del ruolo.

Per aumentare la disponibilità, i ruoli devono contenere anche un set di dati di riferimento nel caso in cui la risorsa di archiviazione sia inattiva. Ciò garantisce ai ruoli un set di dati di riferimento di base iniziale finché la risorsa di archiviazione non è disponibile per gli aggiornamenti.

![Disponibilità elevata delle applicazioni con nodi di calcolo autonomi](./media/resiliency-high-availability-azure-applications/application-high-availability-through-autonomous-compute-nodes.png)

Una considerazione in merito a questo modello riguarda la distribuzione e la velocità di avvio dei ruoli. La distribuzione o il download di grandi quantità di dati di riferimento all'avvio può aumentare il tempo necessario per avviare nuove distribuzioni o istanze del ruolo. Questo compromesso può essere accettabile per assicurare l'autonomia di avere i dati di riferimento immediatamente disponibili in ogni ruolo anziché dipendere da servizi di archiviazione esterni.

###Modello dei dati transazionali per la disponibilità elevata

I dati transazionali sono i dati generati dall'applicazione in un contesto aziendale. I dati transazionali sono una combinazione dell'insieme dei processi aziendali implementati dall'applicazione e dei dati di riferimento che supportano tali processi. Esempi di dati transazionali possono essere ordini, preavvisi di spedizione, fatture e opportunità di gestione delle relazioni con i clienti (CRM). I dati transazionali così generati verranno inviati a sistemi esterni per il mantenimento dei record o l'ulteriore elaborazione.

Tenere presente che i dati di riferimento possono essere modificati nei sistemi responsabili di questi dati. Per questo motivo, i dati transazionali devono salvare il contesto di dati di riferimento temporizzato in modo da ridurre al minimo le dipendenze esterne per la coerenza semantica. Si consideri ad esempio la rimozione di un prodotto dal catalogo alcuni mesi dopo l'evasione di un ordine. La procedura consigliata è incorporare il massimo contesto di dati di riferimento possibile nella transazione. In questo modo, la semantica associata alla transazione viene mantenuta anche in caso di modifica dei dati di riferimento dopo l'acquisizione della transazione.

Come indicato in precedenza, le architetture che usano un regime di controllo libero e la comunicazione asincrona si prestano a livelli superiori di disponibilità. Lo stesso vale per i dati transazionali, ma l'implementazione è più complessa. I concetti transazionali tradizionali si basano in genere sul database per garantire la transazione. Quando si introducono livelli intermedi, il codice dell'applicazione deve gestire correttamente i dati a vari livelli per assicurare coerenza e durabilità sufficienti.

La sequenza seguente descrive un flusso di lavoro che separa l'acquisizione dei dati transazionali dalla relativa elaborazione.

1. Nodo di calcolo Web: presentazione dei dati di riferimento.
1. Archiviazione esterna: salvataggio dei dati transazionali intermedi.
1. Nodo di calcolo Web: completamento della transazione dell'utente finale.
1. Nodo di calcolo Web: invio dei dati transazionali completati con il contesto di dati di riferimento a una risorsa di archiviazione durevole temporanea che garantisce una risposta prevedibile.
1. Nodo di calcolo Web: segnalazione all'utente finale del completamento della transazione.
1. Nodo di calcolo in background: estrazione dei dati transazionali, relativa post-elaborazione se necessaria e invio alla posizione di archiviazione finale nel sistema corrente.

La figura seguente illustra una possibile implementazione di questa progettazione in un servizio cloud ospitato in Azure.

![Disponibilità elevata a regime di controllo libero](./media/resiliency-disaster-recovery-high-availability-azure-applications/application-high-availability-through-loose-coupling.png)

Le frecce tratteggiate nella figura precedente indicano l'elaborazione asincrona. Il ruolo Web front-end non rileva questa elaborazione asincrona, che conduce all'archiviazione della transazione nella destinazione finale con riferimento al sistema corrente. A causa della latenza introdotta da questo modello asincrono, i dati transazionali non sono immediatamente disponibili per le query. Ogni unità dei dati transazionali deve quindi essere salvata in una cache o una sessione utente per soddisfare le esigenze immediate dell'interfaccia utente.

Il ruolo Web è autonomo dal resto dell'infrastruttura. Il relativo profilo di disponibilità è dato da una combinazione di ruolo Web e coda di Azure e non dall'intera infrastruttura. Oltre alla disponibilità elevata, questo approccio supporta la scalabilità orizzontale del ruolo Web, indipendentemente dall'archiviazione back-end. Questo modello di disponibilità elevata può influire sull'aspetto economico delle operazioni. Componenti aggiuntivi come ruoli di lavoro e code di Azure possono incidere sui costi d'uso mensili.

Si noti che la figura precedente illustra un'implementazione di questo approccio disaccoppiato ai dati transazionali. Esistono molte altre possibili implementazioni. Di seguito sono elencate alcune alternative:

 * È possibile inserire un ruolo di lavoro tra il ruolo Web e la coda di archiviazione.
 * È possibile usare una coda del bus di servizio anziché una coda di Archiviazione di Azure.
 * La destinazione finale potrebbe essere Archiviazione di Azure o un diverso provider di database.
 * È possibile usare Cache di Azure al livello Web per garantire i requisiti di memorizzazione immediata nella cache dopo la transazione.

###Modelli per la scalabilità

È importante notare che la scalabilità del servizio cloud influisce direttamente sulla disponibilità. Se il servizio non risponde a causa di un incremento del carico, l'utente ha l'impressione che l'applicazione sia inattiva. Seguire le procedure consigliate per la scalabilità in base al carico previsto per l'applicazione e alle aspettative future. La scalabilità massima implica molte considerazioni, ad esempio riguardo all'uso di uno o più account di archiviazione, alla condivisione in più database e alle strategie di caching. Per un approfondimento su questi modelli, vedere [Procedure consigliate per la progettazione di servizi su larga scala nei Servizi cloud di Azure](https://azure.microsoft.com/blog/best-practices-for-designing-large-scale-services-on-windows-azure/).

##Passaggi successivi

Questo articolo fa parte di una serie dedicata al [ripristino di emergenza e disponibilità elevata per le applicazioni basate su Microsoft Azure](./resiliency-disaster-recovery-high-availability-azure-applications.md). L'articolo successivo della serie riguarda il [ripristino di emergenza per le applicazioni basate su Microsoft Azure](./resiliency-disaster-recovery-azure-applications.md).

<!---HONumber=AcomDC_0928_2016-->