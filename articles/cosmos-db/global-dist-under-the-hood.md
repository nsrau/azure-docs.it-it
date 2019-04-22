---
title: Distribuzione globale con Azure Cosmos DB - informazioni sul funzionamento
description: In questo articolo vengono forniti i dettagli tecnici relativi alla distribuzione globale di Azure Cosmos DB
author: dharmas-cosmos
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/31/2019
ms.author: dharmas
ms.reviewer: sngun
ms.openlocfilehash: 84ce13ae3bb0a4b66b8167e61b720fe6cecbe95c
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "58762413"
---
# <a name="global-data-distribution-with-azure-cosmos-db---under-the-hood"></a>Distribuzione globale dei dati con Azure Cosmos DB - informazioni sul funzionamento

Azure Cosmos DB è un servizio di base in Azure, in modo che viene distribuita in tutte le aree di Azure in tutto il mondo inclusi pubblico, sovereign, Dipartimento della difesa (DoD) e cloud per enti pubblici. All'interno di un data center, distribuiamo e gestiamo Azure Cosmos DB su moltissimi tipi di computer, ognuno con risorse di archiviazione locali dedicate. All'interno di un data center, Azure Cosmos DB viene distribuito tra vari cluster, ognuno dei quali esegue potenzialmente più generazioni di hardware. Le macchine in un cluster sono in genere distribuite su 10 e 20 domini di errore per la disponibilità elevata all'interno di un'area. L'immagine seguente mostra la topologia del sistema di distribuzione globale di Cosmos DB:

![Topologia del sistema](./media/global-dist-under-the-hood/distributed-system-topology.png)

**Distribuzione globale in Azure Cosmos DB è pronta per l'uso:** In qualsiasi momento, con pochi clic oppure a livello di codice con una singola chiamata API, è possibile aggiungere o rimuovere le aree geografiche associate con il database Cosmos. Un database Cosmos, a sua volta, costituito da un set di contenitori di Cosmos. In Cosmos DB, i contenitori fungono da unità logiche di distribuzione e scalabilità. Le raccolte, le tabelle e i grafici che vengono creati sono (internamente) solo contenitori Cosmos. I contenitori sono completamente indipendente dallo schema e forniscono un ambito per una query. I dati in un contenitore Cosmos vengono indicizzati automaticamente al momento dell'inserimento. L'indicizzazione automatica consente agli utenti di eseguire query sui dati senza la complessità di gestire schemi o indici, specialmente in una configurazione distribuita a livello globale.  

- In una determinata area, i dati all'interno di un contenitore viene distribuiti usando una chiave di partizione, che forniscono ed è gestita in modo trasparente per le partizioni fisiche sottostanti (*distribuzione locale*).  

- Ogni partizione fisica viene inoltre replicata tra più aree geografiche (*distribuzione globale*). 

Quando un'app con Cosmos DB in modo elastico offre velocità effettiva in un contenitore Cosmos scalabile o utilizza uno spazio di archiviazione, Cosmos DB gestisce in modo trasparente le operazioni di gestione partizione (split, clonare, delete) in tutte le aree. Indipendente da scalabilità, distribuzione o errori, Cosmos DB continua a fornire una singola immagine del sistema dei dati all'interno dei contenitori, che vengono distribuiti globalmente in un grande numero di aree.  

Come illustrato nell'immagine seguente, i dati all'interno di un contenitore distribuiti lungo due dimensioni: all'interno di un'area e tra diverse aree, in tutto il mondo:  

![partizioni fisiche](./media/global-dist-under-the-hood/distribution-of-resource-partitions.png)

Una partizione fisica viene implementata da un gruppo di repliche, denominate una *set di repliche*. Ogni computer ospita centinaia di repliche che corrispondono a varie partizioni fisiche all'interno di un set fisso di processi, come illustrato nell'immagine precedente. Le repliche corrispondenti alle partizioni fisiche vengono posizionate in modo dinamico e con carico bilanciato nei computer all'interno di un cluster e nei data center all'interno di un'area.  

Una replica appartiene in modo univoco a un tenant di Azure Cosmos DB. Ogni replica ospita un'istanza del [motore di database](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) di Cosmos DB, che gestisce le risorse, nonché gli indici associati. Il motore di database Cosmos DB funziona su un tipo di sistema basato su una sequenza di record ATOM (ARS,atom-record-sequence). Il motore è agnostico al concetto di uno schema, il limite tra i valori di struttura e istanza del record di sfocatura. Cosmos DB consente di ottenere la completa indipendenza dallo schema attraverso un'efficiente indicizzazione automatica di tutti gli elementi dopo all'inserimento, consentendo agli utenti di eseguire una query dei dati distribuiti a livello globale senza dover gestire schemi o indici.

Il motore di database Cosmos è costituito da componenti, tra cui implementazione diverse primitive di coordinamento, runtime di linguaggio, query processor, archiviazione e l'indicizzazione sottosistemi responsabili dell'archiviazione transazionale e di indicizzazione dei dati, rispettivamente. Per garantire durabilità e disponibilità elevata, il motore di database conserva i dati e l'indice nelle unità SSD e ne esegue la replica, rispettivamente tra le istanze del motore di database all'interno dei set di repliche. Tenant di dimensioni maggiori corrispondono a una scalabilità superiore di velocità effettiva e archiviazione e installato uno più grande o più repliche o entrambi. Ogni componente del sistema è completamente asincrono: nessun thread si blocca mai e ognuno esegue operazioni di breve durata senza incorrere in eventuali opzioni di thread non necessarie. Limitazione di frequenza e congestione sono sottoposte a plumbing nell'intero stack dal controllo di ammissione a tutti i percorsi I/O. Motore di database COSMOS è progettato per sfruttare la concorrenza con granularità fine, per offrire velocità effettiva elevata mentre lavora all'interno di quantità frugale nel consumo di risorse di sistema.

Distribuzione globale di COSMOS DB si basa su due chiavi astrazioni – *set di repliche* e *set di partizioni*. Un set di repliche è un "mattoncino" modulare per il coordinamento e un set di partizioni è una sovrapposizione dinamica di una o più partizioni fisiche distribuite geograficamente. Per comprendere il funzionamento della distribuzione globale, è necessario comprendere queste due chiavi di astrazioni. 

## <a name="replica-sets"></a>Set di repliche

Una partizione fisica viene materializzata come un gruppo di repliche con bilanciamento del carico autogestito e dinamico su più domini di errore, denominato set di repliche. Questo set implementa collettivamente il protocollo replicato dello stato del computer per rendere i dati all'interno della partizione fisica altamente disponibili, durevoli e coerenti. L'appartenenza al set di repliche *N* è dinamico, mantiene ripristinarne tra *NMin* e *NMax* base gli errori, le operazioni amministrative e l'ora per non è riuscita repliche per rigenerare/ripristino. In base alle modifiche dell'appartenenza, anche il protocollo della replica riconfigura anche le dimensioni del quorum di lettura e scrittura. Per distribuire in modo uniforme della velocità effettiva che viene assegnata a una determinata partizione fisica, vengono usati due concetti: 

- In primo luogo, il costo di elaborare le richieste di scrittura sulla massima è maggiore del costo dell'applicazione degli aggiornamenti sui follower. Di conseguenza, il leader ha in bilancio più risorse di sistema rispetto ai follower. 

- In secondo luogo, per quanto possibile, il quorum di lettura per un livello di coerenza specifico è costituito esclusivamente dalle repliche dei follower. A meno che non necessario, sarà possibile evitare di contattare il leader per la gestione delle operazioni di lettura. Viene eseguito un numero di ispira alla ricerca effettuata della relazione tra [carico e la capacità](https://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf) nei sistemi basato su quorum per il [cinque modelli di coerenza](consistency-levels.md) che Cosmos DB supporta.  

## <a name="partition-sets"></a>Set di partizioni

Comprende un gruppo di partizioni fisiche, una per ognuna delle configurato con le aree di database Cosmos, per gestire lo stesso set di chiavi replicato in tutte le aree configurate. Questa primitiva di coordinamento superiore viene chiamata un *set di partizioni* -una sovrapposizione dinamica distribuita geograficamente di gestione di un determinato set di chiavi di partizioni fisiche. Mentre l'ambito di una determinata partizione fisica (un set di repliche) è all'interno di un cluster, un set di partizioni può estendersi su cluster, i Data Center e aree geografiche, come illustrato nell'immagine seguente:  

![Set di partizioni](./media/global-dist-under-the-hood/dynamic-overlay-of-resource-partitions.png)

È possibile considerare un set di partizioni come un "super set di repliche" geograficamente disperso, composto da più set di repliche che dispongono dello stesso numero di chiavi. Analogamente a un set di repliche, dell'appartenenza partizione-del set è dinamica: varia in base alle operazioni di gestione partizione fisica implicita per aggiungere o rimuovere nuove partizioni da e verso un determinato set di partizioni (ad esempio, quando scalabilità orizzontale della velocità effettiva in un contenitore, aggiungere o rimuovere un'area per il database Cosmos, o quando si verificano errori). Grazie alla presenza ogni partizione (di un set di partizioni) di gestire l'appartenenza al set di partizioni all'interno di un proprio set di repliche, l'appartenenza è completamente decentralizzati e a disponibilità elevata. Durante la riconfigurazione di un set di partizioni, viene specificata anche la topologia della sovrapposizione tra le partizioni fisiche. La topologia viene selezionata in modo dinamico basato sul livello di coerenza, distanza geografica e larghezza di banda disponibile tra l'origine e le partizioni fisiche di destinazione.  

Il servizio consente di configurare i database Cosmos con una singola area di scrittura o con più aree di scrittura e, a seconda della selezione, i set di partizioni sono configurati per accettare le scritture esattamente in una o tutte le aree. Il sistema usa un protocollo di consenso annidato e a due livelli: un livello opera all'interno delle repliche di un set di repliche di una partizione fisica accettando le scritture; l'altro opera a livello di un set di partizioni per fornire garanzie di ordinamento complete per tutte scritture sulle quali è stato eseguito il commit all'interno del set di partizioni. Questo consenso, annidato e a più livelli, è fondamentale per l'implementazione dei rigorosi contratti di servizio per la disponibilità elevata, nonché per l'implementazione di modelli di coerenza offerti ai client da Cosmos DB.  

## <a name="conflict-resolution"></a>Risoluzione dei conflitti

La progettazione per la propagazione degli aggiornamenti, la risoluzione dei conflitti e il rilevamento della causalità sono ispirate al lavoro preliminare sugli [algoritmi epidemici](https://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf) e il sistema [Bayou](https://zoo.cs.yale.edu/classes/cs422/2013/bib/terry95managing.pdf). Mentre i kernel delle idee rimasti forniscono un pratico riferimento per la comunicazione relativa alla progettazione di sistema di Cosmos DB, sono stati anche sottoposti a una significativa trasformazione una volta applicati al sistema di Cosmos DB. Ciò è stato necessario, perché i sistemi precedenti sono stati progettati con la governance delle risorse né con la scalabilità in corrispondenza del quale deve Cosmos DB per il funzionamento, né per fornire le funzionalità (ad esempio, la coerenza decadimento ristretto) e i rigorosi e contratti di servizio completi che Cosmos DB offre ai clienti.  

È importante ricordare che un set di partizioni viene distribuito tra più aree e segue il protocollo di replica Cosmos DB (multimaster) per replicare i dati tra le partizioni fisiche che comprendono uno specifico set di partizioni. Ogni partizione fisica (di un set di partizioni) in genere accetta le scritture e gestisce le letture dei client locali di una determinata area. Le scritture accettate da una partizione fisica all'interno di un'area vengono memorizzate in modo duraturo e rese altamente disponibili all'interno della partizione fisica prima di essere confermate al client. Si tratta di operazioni di scrittura provvisorie e vengono propagate alle altre partizioni fisiche all'interno del set di partizioni usando un canale antientropia. I client possono richiedere operazioni di scrittura provvisori o sulle quali è stato eseguito il commit, passando un'intestazione di richiesta. La propagazione antientropia (inclusa la frequenza della propagazione) è dinamica e basata sulla topologia del set di partizioni, la prossimità dell'area delle partizioni fisiche e il livello di coerenza configurato. All'interno di un set di partizioni, Cosmos DB segue uno schema di commit primario con una partizione arbiter selezionata dinamicamente. La selezione arbiter è dinamica ed è parte integrante della riconfigurazione del set di partizioni basata sulla topologia della sovrimpressione. Le operazioni di scrittura (inclusi multi-row/in batch gli aggiornamenti) in cui è stato eseguito il commit sono garantite per essere ordinate. 

Vengono usati orologi con vettori codificati (contenenti l'ID dell'area e la logica dell'orologio corrispondente a ogni livello di consenso rispettivamente, al set di repliche e al set di partizioni) per il rilevamento della causalità e della versione vettori per identificare e risolvere conflitti di aggiornamento. La topologia e l'algoritmo di selezione peer sono progettati per garantire risorse di archiviazione predefinite e minime e un sovraccarico di rete minimo dei vettori della versione. L'algoritmo garantisce la rigida proprietà di convergenza.  

Per i database Cosmos configurati con più aree di scrittura, il sistema offre agli sviluppatori una serie di criteri di risoluzione dei conflitti automatica e flessibile, che include: 

- **Last-Write-Wins (LWW)**, che, per impostazione predefinita, Usa una proprietà definita dal sistema timestamp (che è basata sul protocollo-sincronizzazione ora di orologio). Cosmos DB consente anche di specificare qualsiasi altra proprietà numerica personalizzata da usare per la risoluzione dei conflitti.  
- **Definito dall'applicazione (personalizzata) in conflitto dei criteri di risoluzione** (espressi tramite le procedure di tipo merge), che è progettato per la riconciliazione di semantica definita dall'applicazione di conflitti. Queste procedure vengono richiamate quando viene rilevato un conflitto tra scrittura e scrittura a cura di una transazione di database, sul lato server. Il sistema fornisce esattamente una volta garantisce che per l'esecuzione di una routine di tipo merge come parte del protocollo dell'impegno. Esistono [diversi esempi di risoluzione in conflitto](how-to-manage-conflicts.md) è possibile provare a usare.  

## <a name="consistency-models"></a>Modelli di coerenza

Se si configura il database Cosmos con uno o più aree di scrittura, è possibile scegliere tra cinque modelli di coerenza ben definiti. Con più aree di scrittura, ecco alcuni aspetti più interessanti di livelli di coerenza:  

La coerenza a decadimento ristretto garantisce che tutte le letture siano all'interno *K* prefissi o *T* pochi secondi dalla scrittura più recente in una qualsiasi delle aree. Inoltre, con la coerenza a decadimento ristretto le letture monotone e con le garanzie di coerenza del prefisso. Il protocollo antientropia è soggetto a limitazioni di frequenza e assicura che i prefissi non vengono accumulati e che non venga applicata la congestione nelle operazioni di scrittura. Garanzie di coerenza di sessione monotonica lettura, scrittura monotona, lettura delle proprie scritture, scrittura segue garanzie di lettura e coerenti con il prefisso, tutto il mondo. Per i database configurati con coerenza assoluta, i vantaggi di più aree di scrittura (scrittura bassa latenza, disponibilità elevata di scrittura) non è applicabile, a causa di replica sincrona tra aree.

Venga descritte la semantica cinque modelli di coerenza in Cosmos DB [Ecco](consistency-levels.md)e matematicamente descritto usando un alto livello TLA + specifiche [qui](https://github.com/Azure/azure-cosmos-tla).

## <a name="next-steps"></a>Passaggi successivi

Scoprire come configurare la distribuzione globale utilizzando i seguenti articoli:

* [Aggiungere o rimuovere aree dall'account di database](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Come configurare i client per il multihoming](how-to-manage-database-account.md#configure-clients-for-multi-homing)
* [Come creare un criterio di risoluzione dei conflitti personalizzato](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)
