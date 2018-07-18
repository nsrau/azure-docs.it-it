---
title: Architetture di database multimaster replicate a livello globale con Microsoft Azure Cosmos DB | Microsoft Docs
description: ''
services: cosmos-db
author: rimman
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: rimman
ms.openlocfilehash: cc66b2f506d81a7ba10b26c3b24287472e890682
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2018
ms.locfileid: "34724908"
---
# <a name="multi-master-at-global-scale-with-azure-cosmos-db"></a>Architetture di database multimaster replicate a livello globale con Microsoft Azure Cosmos DB 
 
Lo sviluppo di applicazioni distribuite a livello globale che rispondono con latenze in locale durante la gestione di visualizzazioni coerenti dei dati in tutto il mondo è un problema complesso. I clienti usano i database distribuiti a livello globale perché hanno bisogno di ridurre la latenza dell'accesso ai dati, ottenere la disponibilità elevata dei dati, garantirsi l'eventuale ripristino di emergenza e soddisfare i requisiti aziendali. L'architettura di database multimaster in Microsoft Azure Cosmos DB offre alti livelli di disponibilità (99,999%), una latenza in millisecondi a una cifra per scrivere i dati e una scalabilità con il supporto della risoluzione dei conflitti flessibile, completo e integrato. Queste funzionalità semplificano notevolmente lo sviluppo di applicazioni da distribuire a livello globale. Per le applicazioni distribuite globalmente il supporto multimaster è fondamentale. 

![Architettura multimaster](./media/multi-region-writers/multi-master-architecture.png)

Con il supporto multimaster offerto da Azure Cosmo DB, è possibile eseguire operazioni di scrittura in contenitori di dati, ad esempio raccolte, grafici e tabelle, distribuiti ovunque nel mondo. È possibile aggiornare i dati in qualsiasi area che sia associata all'account di database. Questi aggiornamenti di dati possono essere propagati in modo asincrono. Oltre a offrire accesso rapido e latenza di scrittura breve ai dati, l'architettura multimaster rappresenta inoltre una soluzione pratica ai problemi di bilanciamento del carico e di failover. Microsoft Azure Cosmos DB offre, in altre parole, una latenza di scrittura di < 10 ms al 99° percentile, una disponibilità di lettura e scrittura del 99,999% e la possibilità di scalare la velocità effettiva di lettura e scrittura ovunque nel mondo.   

> [!IMPORTANT]
> Il supporto dell'architettura multimaster è in anteprima privata. Per usare la versione di anteprima, [iscriversi](#sign-up-for-multi-master-support) ora.

## <a name="sign-up-for-multi-master-support"></a>Effettuare l'iscrizione per il supporto multimaster

Se si dispone già di una sottoscrizione di Azure, è possibile iscriversi per partecipare al programma di anteprima multimaster nel portale di Azure. Se non si ha familiarità con Azure, registrarsi per una [versione di prova gratuita](https://azure.microsoft.com/free) che prevede 12 mesi di accesso libero ad Azure Cosmos DB. Per richiedere l'accesso al programma di anteprima multimaster, completare i passaggi seguenti.

1. Nel [portale di Azure](https://portal.azure.com) fare clic su **Crea una risorsa** > **Database** > **Azure Cosmos DB**.  

2. Nella pagina Nuovo account, specificare un nome per l'account di Azure Cosmos DB e quindi scegliere API, sottoscrizione, gruppo di risorse e località.  

3. Selezionare poi **Iscrizione immediata all'anteprima** nel campo Multi Master Preview.  

   ![Effettuare l'iscrizione per l'anteprima multimaster](./media/multi-region-writers/sign-up-for-multi-master-preview.png)

4. Nel riquadro **Iscrizione immediata all'anteprima** fare clic su **OK**. Dopo aver inviato la richiesta, nel riquadro di creazione dell'account lo stato diventa **In attesa di approvazione**.  

Dopo l'invio della richiesta, si riceverà una notifica di approvazione della richiesta tramite posta elettronica. A causa dell'elevato volume di richieste, si riceverà una notifica entro una settimana. Non è necessario creare un ticket di supporto per completare la richiesta. Le richieste verranno esaminate nell'ordine in cui sono state ricevute.

## <a name="a-simple-multi-master-example--content-publishing"></a>Esempio semplice di architettura multimaster: pubblicazione di contenuto  

Esaminiamo ora uno scenario reale per illustrare il modo in cui si può usare il modello multimaster con Microsoft Azure Cosmos DB. Esaminare una piattaforma di pubblicazione di contenuti creata in Cosmos DB. Ecco alcuni requisiti che devono essere rispettati da questa piattaforma per offrire un'esperienza utente ottimale per server di pubblicazione e consumer. 

* Sia gli autori che i sottoscrittori sono distribuiti in tutto il mondo.  

* Gli autori devono pubblicare (scrivere) articoli nella rispettiva area locale (più vicina).  

* Gli autori hanno lettori/sottoscrittori degli articoli distribuiti in tutto il mondo.  

* I sottoscrittori ricevono una notifica quando vengono pubblicati nuovi articoli.  

* I sottoscrittori devono essere in grado di leggere articoli dalla rispettiva area locale. Devono anche potere aggiungere revisioni per questi articoli.  

* Qualunque utente, incluso l'autore degli articoli, deve potere visualizzare tutte le revisioni allegate agli articoli da un'area locale.  

Supponendo che siano presenti milioni di consumer e server di pubblicazione con miliardi di articoli, sarà presto necessario affrontare i problemi relativi alla scalabilità, oltre a garantire l'accesso locale. Un caso d'uso di questo tipo è un candidato ideale per il modello multimaster di Microsoft Azure Cosmos DB. 

## <a name="benefits-of-having-multi-master-support"></a>Vantaggi offerti dal supporto multimaster 

Per le applicazioni distribuite a livello globale il supporto multimaster è fondamentale. Il modello multimaster è costituito da [molteplici aree master](distribute-data-globally.md) che partecipano in ugual misura a un modello di scrittura ovunque (modello attivo-attivo) e viene usato per assicurare la disponibilità dei dati sempre e ovunque. Eventuali aggiornamenti che vengono apportati a una singola area vengono propagati in modo asincrono a tutte le altre aree, che a loro volta fungono da aree master. Le aree di Microsoft Azure Cosmos DB che fungono da aree master in una configurazione multimaster operano automaticamente in modo da convergere i dati di tutte le repliche e assicurare la [coerenza globale e l'integrità dei dati](consistency-levels.md). La figura seguente mostra la replica in lettura/scrittura in uno scenario a master singolo e in uno multimaster.

![Master singolo e multimaster](./media/multi-region-writers/single-vs-multi-master.png)

I clienti che tentano di implementare il modello multimaster autonomamente aggiungono carico di lavoro agli sviluppatori. Questi clienti possono impiegare centinaia di ore a configurare e testare una configurazione multimaster a livello globale e molti dispongono di un team di ingegneri dedicato il cui unico compito consiste nel monitorare e gestire la replica multimaster. Quando si crea e si gestisce in modo autonomo una configurazione multimaster, si tolgono tempo e risorse all'innovazione dell'applicazione stessa e i costi aumentano. Microsoft Azure Cosmos DB offre supporto multimaster "pronto all'uso" ed evita il sovraccarico di lavoro per gli sviluppatori.  

Il supporto multimaster offre in pratica i vantaggi seguenti:

* **Miglioramenti nel ripristino di emergenza, nella disponibilità in scrittura e nel failover** - È possibile usare il supporto multimaster per conservare in misura maggiore la disponibilità elevata di un database di importanza strategica. Un database multimaster può, ad esempio, replicare i dati di un'area verso un'area di failover quando l'area primaria diventa non disponibile a causa di un'interruzione del servizio o una situazione di emergenza. L'area di failover funge a questo punto da area master pienamente funzionale per supportare l'applicazione. Il modello multimaster offre una "possibilità di sopravvivenza" e una protezione maggiori al verificarsi di disastri naturali, interruzioni di elettricità o sabotaggi, o di tutte queste situazioni insieme in quanto le aree rimanenti possono trovarsi in multimaster posti in luoghi geografici diversi con una disponibilità di scrittura garantita maggiore del 99,999%. 

* **Miglioramento della latenza in scrittura per gli utenti finali** - Più sono vicini i dati (offerti dal servizio) all'utente finale, migliore è l'esperienza dell'utente. Se, ad esempio, gli utenti si trovano in Europa ma il database risiede negli Stati Uniti o in Australia, la latenza aggiunta è di circa 140 ms e 300 ms per le rispettive aree. Per molti giochi popolari, servizi bancari o applicazioni interattive (Web o per dispositivi mobili) i ritardi non sono un buon inizio. La latenza ha un'enorme importanza nella percezione che un cliente ha di un'esperienza di alta qualità. È stato inoltre dimostrato che la latenza ha un notevole impatto sul comportamento degli utenti. Con l'evoluzione della tecnologia e soprattutto con l'avvento di AR, VR e MR, che richiedono esperienze ancora più immersive e realistiche, gli sviluppatori devono ora creare sistemi software con requisiti di latenza rigorosi. Diventa quindi ancora più importante avere applicazioni e dati (contenuto per le applicazioni) disponibili localmente. Con il supporto multimaster di Microsoft Azure Cosmos DB, le prestazioni sono veloci quanto le normali operazioni di lettura e scrittura in locale e sono avanzate a livello globale grazie alla distribuzione geografica.  

* **Miglioramento della scalabilità in scrittura e della velocità effettiva di scrittura** - Il supporto multimaster consente di ottenere una velocità effettiva più elevata e un utilizzo maggiore offrendo al contempo più modelli di coerenza con garanzia di correttezza e supportati da contratti di servizio. 

  ![Scalabilità della velocità effettiva di scrittura con il supporto multimaster](./media/multi-region-writers/scale-write-throughput.png)

* **Miglioramento del supporto per ambienti disconnessi (ad esempio, i dispositivi periferici)** - Il supporto multimaster consente agli utenti di replicare tutti i dati o un subset di questi da un dispositivo periferico all'area più vicina in un ambiente disconnesso. Questo scenario è tipico dei sistemi di automazione della forza vendita, dove il portatile di un individuo (un dispositivo disconnesso) memorizza un subset di dati correlati al singolo venditore. Le aree master nel cloud che si trovano in un posto qualsiasi nel mondo possono fungere da destinazione della copia dei dispositivi periferici remoti.  

* **Bilanciamento del carico** - Con il supporto multimaster, il carico nell'applicazione può essere ribilanciato spostando utenti/carichi di lavoro da un'area sovraccarica ad aree in cui il carico viene distribuito in modo uniforme. È possibile estendere la capacità di scrittura facilmente aggiungendo una nuova area e successivamente spostandovi alcune scritture. 

* **Migliore uso della capacità predisposta** - Con il supporto multimaster, per carichi di lavoro misti e con intensa attività di scrittura, si può saturare la capacità predisposta in più regioni.  In alcuni casi si può ridistribuire le attività di lettura e scrittura in modo più equo. In questo modo è richiesta una minore velocità effettiva e i clienti hanno un maggiore risparmio sui costi.  

* **Applicazioni con architetture più semplici e più resilienti** - Le applicazioni che vengono spostate in una configurazione multimaster ottengono la garanzia della resilienza dei dati.  Poiché Microsoft Azure Cosmos DB nasconde tutta la complessità, la progettazione e l'architettura delle applicazioni diventano notevolmente più semplici. 

* **Test di failover privo di rischi** - Il test di failover non subisce alcuna riduzione delle prestazioni nella velocità effettiva di scrittura. Nel modello multimaster, tutte le altre aree sono master completi, pertanto il failover non ha molto impatto sulla velocità effettiva di scrittura.  

* **Costo totale di proprietà (TCO) e DevOps più bassi** - Il raggiungimento degli obiettivi di scalabilità, prestazioni, distribuzione globale e tempi di ripristino è spesso costoso a causa dell'elevato costo dei componenti aggiuntivi o al mantenimento di un'infrastruttura di backup che opera solo in casi di emergenza. Con il modello multimaster di Microsoft Azure Cosmos DB supportato da ottimi contratti di servizio, non sono più gli sviluppatori a dover creare e mantenere "la logica collante del back-end" e quindi possono gestire con più tranquillità i carichi di lavoro di importanza strategica. 

## <a name="use-cases-where-multi-master-support-is-needed"></a>Casi d'uso che richiedono il supporto multimaster

Esistono molti casi d'uso che richiedono il supporto multimaster in Microsoft Azure Cosmos DB: 

* **IoT** - Il supporto multimaster di Microsoft Azure Cosmos DB Azure consente l'implementazione distribuita semplificata dell'elaborazione dei dati IoT. Le distribuzioni periferiche geograficamente distribuite che usano tipi di dati replicati privi di conflitti con CRDT spesso devono tenere traccia dei dati delle serie temporali provenienti da più posizioni. Ogni dispositivo può essere disponibile in una delle aree più vicine e un dispositivo può viaggiare (ad esempio un'auto) ed essere dinamicamente reso disponibile per la scrittura in un'altra area.  

* **E-commerce** - Per garantire un'esperienza utente ottimale negli scenari di e-commerce, sono necessarie la disponibilità elevata e la resilienza agli errori. Se in un'area si verifica un errore, le sessioni degli utenti, i carrelli acquisti e gli elenchi delle preferenze attivi devono essere rilevati senza problemi da un'altra area senza che si verifichi la perdita dello stato. Gli aggiornamenti eseguiti nel frattempo dall'utente devono essere gestiti in modo adeguato (ad esempio, le aggiunte e le rimozioni dal carrello acquisti devono essere trasferite). Con il supporto multimaster, Microsoft Azure Cosmos DB è in grado di gestire questi scenari senza problemi, con una facile transizione tra aree attive mantenendo al contempo una visualizzazione coerente dal punto di vista dell'utente. 

* **Rilevamento di frodi/anomalie** - Le applicazioni che monitorano le attività degli utenti o degli account spesso sono distribuite a livello globale e devono tenere traccia di numerosi eventi contemporaneamente. Mentre si creano e si gestiscono i punteggi per un utente, azioni da aree geografiche differenti devono aggiornare simultaneamente i punteggi per mantenere le metriche di rischio in linea. Microsoft Azure Cosmos DB fa in modo che gli sviluppatori non debbano gestire scenari di conflitto a livello di applicazione. 

* **Collaborazione** - Per le applicazioni che vengono valutate in base alla popolarità degli articoli, ad esempio prodotti in vendita o supporti da usare e così via. Il monitoraggio della popolarità in tutte le aree geografiche può diventare complicato, in particolare quando è necessario pagare le royalty o prendere decisioni sulla pubblicità in tempo reale. La classificazione, l'ordinamento e la creazione di report in molte aree distribuite globalmente in tempo reale con Microsoft Azure Cosmos DB consentono agli sviluppatori di offrire funzionalità con poco sforzo e senza compromessi in termini di latenze. 

* **Misurazione** - Il conteggio e la regolazione dell'uso (ad esempio chiamate API, transazioni/secondo, minuti usati) possono essere implementati a livello globale con semplicità usando il supporto multimaster di Microsoft Azure Cosmos DB. La risoluzione dei conflitti integrata assicura sia l'accuratezza dei conteggi che la regolazione in tempo reale. 

* **Personalizzazione** - Che si stia gestendo contatori geograficamente distribuiti che attivano azioni, ad esempio punti fedeltà, o che si stiano implementando visualizzazioni personalizzate delle sessioni degli utenti, il conteggio a disponibilità elevata e geograficamente distribuito e semplificato offerto da Microsoft Azure Cosmos DB consente di ottenere dalle applicazioni prestazioni elevate con semplicità. 

## <a name="conflict-resolution-with-multi-master"></a>Risoluzione dei conflitti con il supporto multimaster 

La sfida che spesso il modello multimaster deve affrontare è che due o più repliche dello stesso record potrebbero venire aggiornate simultaneamente da utenti diversi in due o più aree diverse. Le scritture simultanee possono generare due versioni diverse dello stesso record e, senza risoluzione dei conflitti integrata, l'applicazione deve eseguire da sola la risoluzione dei conflitti per risolvere questa incoerenza.  

**Esempio** - Si supponga di usare Microsoft Azure Cosmos DB come archivio di salvataggi permanenti per un'applicazione di carrelli acquisti e che questa applicazione sia distribuita in due aree, Stati Uniti orientali e Stati Uniti occidentali.  Approssimativamente nello stesso momento, un utente a San Francisco aggiunge un articolo al proprio carrello acquisti, ad esempio un libro, mentre un processo di gestione magazzino negli Stati Uniti orientali invalida un altro articolo del carrello acquisti (ad esempio un nuovo smartphone) dello stesso utente in risposta a una notifica da parte del fornitore relativa a un ritardo nella data di uscita del prodotto. All'ora T1, i record del carrello acquisti nelle due aree sono diversi. Il database usa il proprio meccanismo di replica e di risoluzione dei conflitti per risolvere questa incoerenza e, alla fine, viene selezionata una delle due versioni del carrello acquisti. Usando l'euristica della risoluzione dei conflitti applicata nella maggior parte dei casi dai database multimaster (ad esempio, la precedenza all'ultima scrittura), è impossibile per l'utente o l'applicazione prevedere quale versione viene selezionata. In entrambi i casi si verifica una perdita dei dati o un comportamento imprevisto. Se viene selezionata la versione dell'area orientale, la selezione di un nuovo articolo (il libro) da parte dell'utente viene persa e se viene selezionata la versione dell'area occidentale, l'articolo precedentemente scelto (lo smartphone) rimane nel carrello. In ogni caso, alcune informazioni vengono perse. Qualsiasi altro processo che analizza il carrello acquisti nel periodo compreso tra T1 e T2 si trova di fronte a un comportamento non deterministico. Un processo in background che seleziona il magazzino per l'evasione e aggiorna i costi del carrello acquisti produce risultati che sono in conflitto con l'eventuale contenuto del carrello. Se il processo è in esecuzione nell'area occidentale e l'alternativa 1 diventa realtà, il processo calcola i costi di spedizione per due articoli, anche se il carrello presto potrebbe avere un solo articolo, vale a dire il libro. 

Microsoft Azure Cosmos DB implementa la logica per la gestione delle scritture in conflitto all'interno del motore del database stesso. Azure Cosmos DB offre **supporto per la risoluzione dei conflitti completo e flessibile** offrendo vari modelli di risoluzione dei conflitti, tra cui Automatico (tipi di dati replicati senza conflitti), Priorità dell'ultima scrittura (LWW) e Personalizzato (stored procedure) per la risoluzione dei conflitti automatica. I modelli di risoluzione dei conflitti offrono garanzie di correttezza e coerenza e alleviano il carico di lavoro degli sviluppatori, i quali non devono più preoccuparsi della coerenza, la disponibilità, le prestazioni, la latenza di replica e le complesse combinazioni di eventi in caso di failover geografici e conflitti di scrittura tra più aree.  

  ![Risoluzione dei conflitti nel modello multimaster](./media/multi-region-writers/multi-master-conflict-resolution-blade.png)

Microsoft Azure Cosmos DB offre 3 tipi di modelli di risoluzione dei conflitti. Le semantiche dei modelli di risoluzione dei conflitti sono le seguenti: 

**Automatico** - Criteri di risoluzione dei conflitti predefiniti. Se si seleziona questo tipo di criteri, Microsoft Azure Cosmos DB risolve automaticamente gli aggiornamenti in conflitto sul lato server e garantisce una coerenza assoluta. Microsoft Azure Cosmos DB implementa internamente la risoluzione dei conflitti automatica sfruttando i tipi di dati replicati privi di conflitti (CRDT) nel motore di database.  

**Priorità dell'ultima scrittura (LWW)** - Questi criteri consentono di risolvere i conflitti in base alla proprietà di timestamp sincronizzata definita dal sistema o a una proprietà personalizzata definita nella versione dei record in conflitto. La risoluzione dei conflitti avviene sul lato server e viene selezionata come prioritaria la versione con il timestamp più recente.  

**Personalizzato** - È possibile registrare una logica di risoluzione dei conflitti definita dall'applicazione tramite la registrazione di una stored procedure. La stored procedure viene richiamata quando viene rilevato un conflitto tra aggiornamenti a cura di una transazione di database, sul lato server. Se si seleziona l'opzione ma non si registra una stored procedure (o se la stored procedure genera un'eccezione in fase di esecuzione), è possibile accedere a tutte le versioni in conflitto tramite il feed dei conflitti e risolverle singolarmente.  

## <a name="next-steps"></a>Passaggi successivi  

In questo articolo è stato spiegato come usare il supporto multimaster distribuito a livello globale con Microsoft Azure Cosmos DB. Esaminare quindi le risorse seguenti: 

* [Altre informazioni sul modo in cui Microsoft Azure Cosmos DB supporta la distribuzione globale](distribute-data-globally.md)  

* [Altre informazioni sui failover automatici in Azure Cosmos DB](regional-failover.md)  

* [Informazioni sulla consistenza globale con Microsoft Azure Cosmos DB](consistency-levels.md)  

* Sviluppare in più aree usando Microsoft Azure Cosmos DB: [API SQL](tutorial-global-distribution-sql-api.md), [API MongoDB](tutorial-global-distribution-mongodb.md) o [API ella tabella](tutorial-global-distribution-table.md)  
