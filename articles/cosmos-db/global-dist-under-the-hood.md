---
title: Distribuzione globale con Azure Cosmos DB - informazioni sul funzionamento
description: In questo articolo vengono forniti i dettagli tecnici relativi alla distribuzione globale di Azure Cosmos DB
author: dharmas-cosmos
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: dharmas
ms.reviewer: sngun
ms.openlocfilehash: 849c3a745de08e7cf8ff7f1b8bb237a6d0f54395
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/22/2019
ms.locfileid: "68384156"
---
# <a name="global-data-distribution-with-azure-cosmos-db---under-the-hood"></a>Distribuzione globale dei dati con Azure Cosmos DB - informazioni sul funzionamento

Azure Cosmos DB è un servizio di base in Azure, che viene quindi distribuito in tutte le aree di Azure in tutto il mondo, tra cui Public, Sovereign, Department of Defense (DoD) e cloud governativi. All'interno di un data center, distribuiamo e gestiamo Azure Cosmos DB su moltissimi tipi di computer, ognuno con risorse di archiviazione locali dedicate. All'interno di un data center, Azure Cosmos DB viene distribuito tra vari cluster, ognuno dei quali esegue potenzialmente più generazioni di hardware. I computer all'interno di un cluster vengono in genere distribuiti tra 10-20 domini di errore per la disponibilità elevata all'interno di un'area. L'immagine seguente mostra la topologia del sistema di distribuzione globale di Cosmos DB:

![Topologia del sistema](./media/global-dist-under-the-hood/distributed-system-topology.png)

**La distribuzione globale in Azure Cosmos DB è chiavi in mano:** In qualsiasi momento, con pochi clic o a livello di codice con una singola chiamata API, è possibile aggiungere o rimuovere le aree geografiche associate al database Cosmos. Un database Cosmos, a sua volta, è costituito da un set di contenitori Cosmos. In Cosmos DB, i contenitori fungono da unità logiche di distribuzione e scalabilità. Le raccolte, le tabelle e i grafici che vengono creati sono (internamente) solo contenitori Cosmos. I contenitori sono completamente indipendenti dallo schema e forniscono un ambito per una query. I dati in un contenitore Cosmos vengono indicizzati automaticamente al momento dell'inserimento. L'indicizzazione automatica consente agli utenti di eseguire query sui dati senza problemi di gestione degli schemi o degli indici, soprattutto in una configurazione distribuita a livello globale.  

- In una determinata area, i dati all'interno di un contenitore vengono distribuiti usando una chiave di partizione, fornita e gestita in modo trasparente dalle partizioni fisiche sottostanti (*distribuzione locale*).  

- Ogni partizione fisica viene replicata anche in tutte le aree geografiche (*distribuzione globale*). 

Quando un'app che usa Cosmos DB ridimensiona in modo elastico la velocità effettiva in un contenitore Cosmos o consuma più spazio di archiviazione, Cosmos DB gestisce in modo trasparente le operazioni di gestione delle partizioni (suddivisione, clonazione, eliminazione) in tutte le aree. Indipendente da scalabilità, distribuzione o errori, Cosmos DB continua a fornire una singola immagine del sistema dei dati all'interno dei contenitori, che vengono distribuiti globalmente in un grande numero di aree.  

Come illustrato nell'immagine seguente, i dati all'interno di un contenitore vengono distribuiti lungo due dimensioni, all'interno di un'area e tra aree, in tutto il mondo:  

![partizioni fisiche](./media/global-dist-under-the-hood/distribution-of-resource-partitions.png)

Una partizione fisica viene implementata da un gruppo di repliche, denominato *set*di repliche. Ogni computer ospita centinaia di repliche che corrispondono a diverse partizioni fisiche all'interno di un set fisso di processi, come illustrato nell'immagine precedente. Le repliche corrispondenti alle partizioni fisiche vengono posizionate in modo dinamico e con carico bilanciato nei computer all'interno di un cluster e nei data center all'interno di un'area.  

Una replica appartiene in modo univoco a un tenant di Azure Cosmos DB. Ogni replica ospita un'istanza del [motore di database](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) di Cosmos DB, che gestisce le risorse, nonché gli indici associati. Il motore di database Cosmos DB funziona su un tipo di sistema basato su una sequenza di record ATOM (ARS,atom-record-sequence). Il motore è indipendente dal concetto di schema, sfocando il limite tra la struttura e i valori dell'istanza dei record. Cosmos DB consente di ottenere la completa indipendenza dallo schema attraverso un'efficiente indicizzazione automatica di tutti gli elementi dopo all'inserimento, consentendo agli utenti di eseguire una query dei dati distribuiti a livello globale senza dover gestire schemi o indici.

Il motore di database di Cosmos è costituito da componenti che includono l'implementazione di diverse primitive di coordinamento, runtime del linguaggio, query processor e i sottosistemi di archiviazione e indicizzazione responsabili dell'archiviazione transazionale e dell'indicizzazione dei dati. rispettivamente. Per garantire durabilità e disponibilità elevata, il motore di database conserva i dati e l'indice nelle unità SSD e ne esegue la replica, rispettivamente tra le istanze del motore di database all'interno dei set di repliche. I tenant più grandi corrispondono a una scala più elevata di velocità effettiva e archiviazione e hanno più repliche o entrambi. Ogni componente del sistema è completamente asincrono: nessun thread si blocca mai e ognuno esegue operazioni di breve durata senza incorrere in eventuali opzioni di thread non necessarie. Limitazione di frequenza e congestione sono sottoposte a plumbing nell'intero stack dal controllo di ammissione a tutti i percorsi I/O. Cosmos Database Engine è progettato per sfruttare la concorrenza con granularità fine e per garantire una velocità effettiva elevata e operare all'interno di quantità di risorse di sistema parsimoniose.

La distribuzione globale di Cosmos DB si basa su due astrazioni chiave, ovvero *set* di repliche e *set di partizioni*. Un set di repliche è un "mattoncino" modulare per il coordinamento e un set di partizioni è una sovrapposizione dinamica di una o più partizioni fisiche distribuite geograficamente. Per comprendere il funzionamento della distribuzione globale, è necessario comprendere queste due chiavi di astrazioni. 

## <a name="replica-sets"></a>Set di repliche

Una partizione fisica viene materializzata come un gruppo di repliche con bilanciamento del carico autogestito e dinamico su più domini di errore, denominato set di repliche. Questo set implementa collettivamente il protocollo replicato dello stato del computer per rendere i dati all'interno della partizione fisica altamente disponibili, durevoli e coerenti. L'appartenenza al set di repliche *N* è dinamica, ma continua a variare tra *NMin* e *NMax* in base agli errori, alle operazioni amministrative e al tempo necessario per la rigenerazione o il ripristino delle repliche non riuscite. In base alle modifiche dell'appartenenza, anche il protocollo della replica riconfigura anche le dimensioni del quorum di lettura e scrittura. Per distribuire in modo uniforme la velocità effettiva assegnata a una determinata partizione fisica, vengono utilizzate due idee: 

- In primo luogo, il costo di elaborazione delle richieste di scrittura sul leader è superiore al costo dell'applicazione degli aggiornamenti nel seguito. Di conseguenza, il leader ha in bilancio più risorse di sistema rispetto ai follower. 

- In secondo luogo, per quanto possibile, il quorum di lettura per un livello di coerenza specifico è costituito esclusivamente dalle repliche dei follower. A meno che non necessario, sarà possibile evitare di contattare il leader per la gestione delle operazioni di lettura. Sono state apportate alcune idee dalla ricerca eseguita sulla relazione tra [carico e capacità](https://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf) nei sistemi basati su quorum per i [cinque modelli di coerenza](consistency-levels.md) supportati da Cosmos DB.  

## <a name="partition-sets"></a>Set di partizioni

Un gruppo di partizioni fisiche, una per ogni configurata con le aree del database Cosmos, è costituito per gestire lo stesso set di chiavi replicate in tutte le aree configurate. Questa primitiva di coordinamento più elevata è detta *set* di partizioni, ovvero una sovrapposizione dinamica distribuita geograficamente di partizioni fisiche che gestiscono un determinato set di chiavi. Mentre una determinata partizione fisica (un set di repliche) ha come ambito un cluster, un set di partizioni può estendersi a cluster, Data Center e aree geografiche, come illustrato nell'immagine seguente:  

![Set di partizioni](./media/global-dist-under-the-hood/dynamic-overlay-of-resource-partitions.png)

È possibile considerare un set di partizioni come un "super set di repliche" geograficamente disperso, composto da più set di repliche che dispongono dello stesso numero di chiavi. Analogamente a un set di repliche, l'appartenenza a un set di partizioni è anche dinamica, ma varia in base alle operazioni implicite di gestione delle partizioni fisiche per aggiungere/rimuovere nuove partizioni da e verso un determinato set di partizioni (ad esempio, quando si aumenta la velocità effettiva in un contenitore, aggiungere/rimuovere un'area nel database Cosmos o quando si verificano errori. Poiché ogni partizione (di un set di partizioni) gestisce l'appartenenza al set di partizioni all'interno del proprio set di repliche, l'appartenenza è completamente decentralizzata e a disponibilità elevata. Durante la riconfigurazione di un set di partizioni, viene specificata anche la topologia della sovrapposizione tra le partizioni fisiche. La topologia viene selezionata in modo dinamico in base al livello di coerenza, alla distanza geografica e alla larghezza di banda di rete disponibile tra le partizioni fisiche di origine e di destinazione.  

Il servizio consente di configurare i database Cosmos con una singola area di scrittura o con più aree di scrittura e, a seconda della selezione, i set di partizioni sono configurati per accettare le scritture esattamente in una o tutte le aree. Il sistema usa un protocollo di consenso annidato e a due livelli: un livello opera all'interno delle repliche di un set di repliche di una partizione fisica accettando le scritture; l'altro opera a livello di un set di partizioni per fornire garanzie di ordinamento complete per tutte scritture sulle quali è stato eseguito il commit all'interno del set di partizioni. Questo consenso, annidato e a più livelli, è fondamentale per l'implementazione dei rigorosi contratti di servizio per la disponibilità elevata, nonché per l'implementazione di modelli di coerenza offerti ai client da Cosmos DB.  

## <a name="conflict-resolution"></a>Risoluzione dei conflitti

La progettazione per la propagazione degli aggiornamenti, la risoluzione dei conflitti e il rilevamento della causalità sono ispirate al lavoro preliminare sugli [algoritmi epidemici](https://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf) e il sistema [Bayou](https://zoo.cs.yale.edu/classes/cs422/2013/bib/terry95managing.pdf). Mentre i kernel delle idee rimasti forniscono un pratico riferimento per la comunicazione relativa alla progettazione di sistema di Cosmos DB, sono stati anche sottoposti a una significativa trasformazione una volta applicati al sistema di Cosmos DB. Questa operazione era necessaria perché i sistemi precedenti sono stati progettati né con la governance delle risorse né con la scalabilità a cui Cosmos DB necessario operare, né per fornire le funzionalità (ad esempio, la coerenza con decadimento ristretto) e la rigorosa e Contratti di contratto completi che Cosmos DB consegna ai clienti.  

È importante ricordare che un set di partizioni viene distribuito tra più aree e segue il protocollo di replica Cosmos DB (multimaster) per replicare i dati tra le partizioni fisiche che comprendono uno specifico set di partizioni. Ogni partizione fisica (di un set di partizioni) in genere accetta le scritture e gestisce le letture dei client locali di una determinata area. Le scritture accettate da una partizione fisica all'interno di un'area vengono memorizzate in modo duraturo e rese altamente disponibili all'interno della partizione fisica prima di essere confermate al client. Si tratta di operazioni di scrittura provvisorie e vengono propagate alle altre partizioni fisiche all'interno del set di partizioni usando un canale antientropia. I client possono richiedere operazioni di scrittura provvisori o sulle quali è stato eseguito il commit, passando un'intestazione di richiesta. La propagazione antientropia (inclusa la frequenza della propagazione) è dinamica e basata sulla topologia del set di partizioni, la prossimità dell'area delle partizioni fisiche e il livello di coerenza configurato. All'interno di un set di partizioni, Cosmos DB segue uno schema di commit primario con una partizione arbiter selezionata dinamicamente. La selezione arbiter è dinamica ed è parte integrante della riconfigurazione del set di partizioni basata sulla topologia della sovrimpressione. Le operazioni di scrittura (inclusi multi-row/in batch gli aggiornamenti) in cui è stato eseguito il commit sono garantite per essere ordinate. 

Vengono usati orologi con vettori codificati (contenenti l'ID dell'area e la logica dell'orologio corrispondente a ogni livello di consenso rispettivamente, al set di repliche e al set di partizioni) per il rilevamento della causalità e della versione vettori per identificare e risolvere conflitti di aggiornamento. La topologia e l'algoritmo di selezione peer sono progettati per garantire risorse di archiviazione predefinite e minime e un sovraccarico di rete minimo dei vettori della versione. L'algoritmo garantisce la rigida proprietà di convergenza.  

Per i database Cosmos configurati con più aree di scrittura, il sistema offre agli sviluppatori una serie di criteri di risoluzione dei conflitti automatica e flessibile, che include: 

- **Last-Write-WINS (LWW)** , che, per impostazione predefinita, usa una proprietà timestamp definita dal sistema (basata sul protocollo di clock della sincronizzazione temporale). Cosmos DB consente anche di specificare qualsiasi altra proprietà numerica personalizzata da usare per la risoluzione dei conflitti.  
- **Criteri di risoluzione dei conflitti definiti dall'applicazione (personalizzati)** (espressa tramite le procedure di merge), progettata per la riconciliazione della semantica definita dall'applicazione dei conflitti. Queste procedure vengono richiamate quando viene rilevato un conflitto tra scrittura e scrittura a cura di una transazione di database, sul lato server. Il sistema fornisce una garanzia esatta per l'esecuzione di una procedura di merge come parte del protocollo di impegno. Sono disponibili [diversi esempi di risoluzione dei conflitti](how-to-manage-conflicts.md) per la riproduzione.  

## <a name="consistency-models"></a>Modelli di coerenza

Sia che si configuri il database Cosmos con una o più aree di scrittura, è possibile scegliere tra cinque modelli di coerenza ben definiti. Con più aree di scrittura, di seguito sono riportati alcuni aspetti importanti dei livelli di coerenza:  

La coerenza con decadimento ristretto garantisce che tutte le letture si trovino entro i prefissi *K* o *T* secondi dalla scrittura più recente in una delle aree. Inoltre, le letture con la coerenza con decadimento ristretto sono sicuramente monotone e con garanzie di prefisso coerenti. Il protocollo antientropia è soggetto a limitazioni di frequenza e assicura che i prefissi non vengono accumulati e che non venga applicata la congestione nelle operazioni di scrittura. La coerenza della sessione garantisce la lettura monotona, la scrittura monotona, la lettura delle proprie scritture, la scrittura dopo la lettura e le garanzie di prefisso coerenti, in tutto il mondo Per i database configurati con coerenza assoluta, i vantaggi (latenza di scrittura bassa, elevata disponibilità di scrittura) di più aree di scrittura non si applicano, a causa della replica sincrona tra le aree.

La semantica dei cinque modelli di coerenza in Cosmos DB è descritta in [questo](consistency-levels.md)articolo e viene descritta in maniera matematica usando le specifiche di alto livello di TLA + [qui](https://github.com/Azure/azure-cosmos-tla).

## <a name="next-steps"></a>Passaggi successivi

Scoprire come configurare la distribuzione globale utilizzando i seguenti articoli:

* [Aggiungere o rimuovere aree dall'account di database](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Come configurare i client per il multihoming](how-to-manage-database-account.md#configure-multiple-write-regions)
* [Come creare un criterio di risoluzione dei conflitti personalizzato](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)
