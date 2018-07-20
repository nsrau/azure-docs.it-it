---
title: Livelli di coerenza in Azure Cosmos DB | Microsoft Docs
description: Cosmos DB ha cinque livelli di coerenza per consentire di compensare scompensi di coerenza, disponibilità e latenza finale.
keywords: coerenza finale, azure cosmos db, azure, Microsoft azure
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 03/27/2018
ms.author: sngun
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 20edcd5e8e3ec3a9d3d294f7a81a2e97b4958f50
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2018
ms.locfileid: "37857185"
---
# <a name="tunable-data-consistency-levels-in-azure-cosmos-db"></a>Livelli di coerenza dei dati ottimizzabili in Azure Cosmos DB
Azure Cosmos DB è stato progettato da zero pensando alla distribuzione globale di tutti i modelli di dati. È pensato per offrire garanzie di bassa latenza stimabile e più modelli di coerenza meno rigidi e ben definiti. Azure Cosmos DB offre attualmente cinque livelli di coerenza: assoluta, decadimento ristretto, sessione, prefisso coerente e finale. I modelli di coerenza con obsolescenza associata, sessione, finale e con prefisso coerente vengono definiti "modelli di coerenza meno rigidi" in quanto forniscono un livello di coerenza inferiore alla coerenza assoluta, ovvero il modello di coerenza massima disponibile. 

Oltre ai modelli di **coerenza assoluta** e **finale** offerti in genere dai database distribuiti, Azure Cosmos DB offre altri tre modelli di coerenza attentamente codificati e operativi, ovvero i modelli **con obsolescenza associata**, **sessione** e **con prefisso coerente**. L'utilità di ognuno di questi livelli di coerenza è stata convalidata per casi d'uso reali. Questi cinque livelli di coerenza, collettivamente, consentono di bilanciare in modo informato coerenza, disponibilità e latenza. 

Nel video seguente Andrew Liu, Program Manager di Azure Cosmos DB, illustra le funzionalità di distribuzione globale chiavi in mano.

>[!VIDEO https://www.youtube.com/embed/-4FsGysVD14]

## <a name="distributed-databases-and-consistency"></a>Coerenza e database distribuiti
I database distribuiti a livello commerciale rientrano in due categorie: database che non offrono opzioni di coerenza comprovabili e ben definite e database che offrono due opzioni di programmabilità estreme (coerenza assoluta e coerenza finale). 

Nel primo caso gli sviluppatori di applicazioni devono preoccuparsi della gestione di aspetti secondari come i protocolli di replica e si trovano a scendere a difficili compromessi tra coerenza, disponibilità, latenza e velocità effettiva. Il secondo tipo di database, invece, implica una pressione per la scelta di uno dei due estremi. Nonostante il grande numero di ricerche e proposte per oltre 50 modelli di coerenza, la community dei database distribuiti non è riuscita a commercializzare livelli di coerenza che vadano oltre la coerenza assoluta e quella finale. Cosmos DB consente agli sviluppatori di scegliere tra cinque modelli di coerenza ben definiti nell'ambito della coerenza: assoluta, decadimento ristretto, [sessione](http://dl.acm.org/citation.cfm?id=383631), prefisso coerente e finale. 

![Azure Cosmos DB offre numerosi modelli di coerenza, ampi e ben definiti, tra cui scegliere](./media/consistency-levels/five-consistency-levels.png)

La tabella seguente illustra le garanzie specifiche fornite da ciascun livello di coerenza.
 
**Livelli di coerenza e garanzie**

| Livello di coerenza | Garanzie |
| --- | --- |
| Assoluta | Linearità. Le letture restituiscono sempre la versione più recente di un elemento.|
| Decadimento ristretto | Prefisso coerente. Ritardo delle letture rispetto alle scritture almeno di prefissi k o intervallo t |
| sessione   | Prefisso coerente. Letture costanti, scritture costanti, lettura delle proprie scritture, scrittura basata sulle letture |
| Prefisso coerente | Gli aggiornamenti restituiti sono un prefisso di tutti gli aggiornamenti, senza interruzioni |
| Finale  | Letture non in ordine |

È possibile configurare il livello di coerenza predefinito per l'account Cosmos DB (e successivamente ignorare l'impostazione del livello di coerenza per una specifica richiesta di lettura). Internamente, il livello di coerenza predefinito si applica ai dati all'interno dei set di partizioni che possono estendersi a più aree. Circa il 73% dei tenant di Azure Cosmos DB usa il livello di coerenza sessione e il 20% il livello decadimento ristretto. Circa il 3% dei clienti di Azure Cosmos DB sperimenta i vari livelli di coerenza inizialmente prima di decidere quale sia il livello di coerenza specifico più adatto alla propria applicazione. Solo il 2% dei tenant di Azure Cosmos DB eseguono l'override dei livelli di coerenza per ogni richiesta. 

In Cosmos DB le operazioni di lettura servite con coerenza di sessione, prefisso coerente e finale sono due volte più economici delle operazioni di lettura con coerenza assoluta o decadimento ristretto. I contratti di servizio di Cosmos DB riguardano complessivamente aziende leader nel settore. Tali contratti includono garanzie di coerenza per quanto riguarda disponibilità, velocità effettiva e latenza. Azure Cosmos DB esegue un [controllo della linearità](http://dl.acm.org/citation.cfm?id=1806634), che usa continuamente i dati di telemetria del servizio e segnala esplicitamente eventuali violazioni della coerenza. Per il decadimento ristretto, vengono controllate e segnalate le eventuali violazioni dei limiti k e t. Per tutti i cinque livelli di coerenza flessibili, viene segnalata anche la [metrica probabilistica di decadimento ristretto](http://dl.acm.org/citation.cfm?id=2212359) direttamente all'utente.  

## <a name="service-level-agreements"></a>Contratti di servizio

Il 99,99% dei [contratti di servizio](https://azure.microsoft.com/support/legal/sla/cosmos-db/) offerto da Azure Cosmos DB garantisce coerenza, disponibilità e latenza per gli account di database Azure Cosmos DB database con ambito in una singola area di Azure configurata in base a uno qualsiasi dei cinque livelli di coerenza oppure per gli account di database che interessano più aree di Azure configurate in base a uno dei quattro livelli di coerenza meno rigidi. Inoltre, indipendentemente dalla scelta del livello di coerenza, Azure Cosmos DB offre un contratto di servizio che garantisce al 99,999% la disponibilità in lettura per gli account di database che si estendono su due o più aree di Azure.

## <a name="scope-of-consistency"></a>Ambito di coerenza
L'ambito di granularità della coerenza è limitato alla richiesta del singolo utente. Una richiesta di scrittura può corrispondere a un'operazione di inserimento, sostituzione, upsert o eliminazione. Come per le operazioni di scrittura, anche per una transazione di lettura/query l'ambito equivale alla richiesta del singolo utente. Potrebbe essere chiesto all'utente di scorrere le pagine di un ampio set di risultati, che occupa varie partizioni, ma ciascuna transazione di lettura ha come ambito una singola pagina ed è servita dall'interno di una singola partizione.

## <a name="consistency-levels"></a>Livelli di coerenza
È possibile configurare un livello di coerenza predefinito per l'account del database che si applica a tutti i contenitori e in tutti i database nel proprio account di Cosmos DB. Per impostazione predefinita, tutte le operazioni di lettura e le query eseguite sulle risorse definite dall'utente useranno il livello di coerenza predefinito che è stato specificato nell'account del database. È possibile ridurre il livello di coerenza di una richiesta di lettura/query specifica usato in ognuna delle API supportate. Esistono cinque tipi di livelli di coerenza supportati dal protocollo di replica di Azure Cosmos DB che offrono un chiaro compromesso tra garanzie di coerenza specifiche e prestazioni, come descritto in questa sezione.

<a id="strong"></a>
**Assoluta**: 

* la coerenza assoluta offre una garanzia di [linearità](https://aphyr.com/posts/313-strong-consistency-models) ovvero la garanzia che le letture restituiscano la versione più recente di un elemento. 
* la coerenza assoluta garantisce che una scrittura sia visibile solo dopo che ne è stato eseguito il commit in modo permanente dal quorum di maggioranza delle repliche. Una scrittura può ottenere o il commit sincrono e permanente da parte della replica primaria e della maggioranza delle repliche secondarie o l'interruzione. Una lettura viene sempre confermata dalla quorum di maggioranza per le letture: un client non potrà mai vedere una scrittura parziale o di cui non sia stato eseguito il commit e leggerà sempre la più recente scrittura confermata. 
* Gli account Azure Cosmos DB configurati per usare la coerenza assoluta non possono associare più di un'area di Azure con il loro account Azure Cosmos DB.  
* Il costo di un'operazione di lettura (in termini di [unità richiesta](request-units.md) consumate) con il livello di coerenza assoluta è più alto rispetto ai livelli sessione e finale, ma uguale a quello del livello con decadimento ristretto.

<a id="bounded-staleness"></a>
**Decadimento ristretto**: 

* La coerenza con decadimento ristretto garantisce che il ritardo delle letture sulle scritture sia al massimo pari a *K* versioni o prefissi di un elemento o all'intervallo di tempo *t*. 
* Pertanto, quando si sceglie il decadimento ristretto, il "decadimento" può essere configurato in due modi: numero di versioni *K* dell'elemento del ritardo delle operazioni di lettura sulle operazioni di scrittura e l'intervallo di tempo *t* 
* Il decadimento ristretto offre un ordine globale totale tranne all'interno della "finestra di decadimento". La garanzia di lettura monotona esiste in un'area sia all'interno che all'esterno della "finestra di decadimento". 
* Il decadimento ristretto offre una maggiore garanzia di coerenza rispetto alla coerenza di sessione, con prefisso coerente o finale. Per le applicazioni distribuite a livello globale, è consigliabile usare il decadimento ristretto per gli scenari in cui si desidera una coerenza assoluta ma si desidera anche il 99,99% di disponibilità e bassa latenza.   
* Gli account Azure Cosmos DB configurati con la coerenza con decadimento ristretto possono associare qualsiasi numero di aree di Azure con il proprio account Azure Cosmos DB. 
* Il costo di un'operazione di lettura (in termini di unità richiesta consumate) con il decadimento ristretto è più alto rispetto ai livelli sessione e finale, ma uguale a quello del livello assoluto.

<a id="session"></a>
**Sessione**: 

* a differenza dei modelli di coerenza globale offerti dai livelli di coerenza assoluta e con decadimento ristretto, la coerenza di "sessione" ha come ambito una sessione del client. 
* La coerenza di sessione è ideale per tutti gli scenari in cui è coinvolto un dispositivo o una sessione utente poiché garantisce letture monotone, scritture monotone e garanzie di lettura di ciò che si scrive (RYW). 
* La coerenza di sessione offre una coerenza prevedibile per una sessione e la massima velocità di scrittura, con latenza minima per scrittura e lettura. 
* Gli account Azure Cosmos DB configurati con la coerenza sessione possono associare qualsiasi numero di aree di Azure con il proprio account Azure Cosmos DB. 
* Il costo di un'operazione di lettura (in termini di unità richiesta consumate) con il livello di coerenza di sessione è minore rispetto ai livelli assoluto e con decadimento ristretto, ma maggiore rispetto al livello finale.

<a id="consistent-prefix"></a>
**Prefisso coerente**: 

* il prefisso coerente garantisce che, in assenza di altre operazioni di scrittura, alla fine le repliche convergeranno all'interno del gruppo. 
* Il prefisso coerente garantisce che le operazioni di lettura non vedano mai il fuori sequenza delle operazioni di scrittura. Se queste sono state eseguite nell'ordine `A, B, C`, il client vede `A`, `A,B` o `A,B,C`, ma mai il fuori sequenza ad esempio `A,C` o `B,A,C`.
* Gli account Azure Cosmos DB configurati con la coerenza con prefisso coerente possono associare qualsiasi numero di aree di Azure con il proprio account Azure Cosmos DB. 

<a id="eventual"></a>
**Finale**: 

* la coerenza finale garantisce che, in assenza di altre scritture, alla fine le repliche all'interno del gruppo convergeranno. 
* La coerenza finale è la forma più debole di coerenza, in cui un client può ottenere nel tempo valori obsoleti rispetto a quelli già visualizzati in passato.
* La coerenza finale rappresenta il livello più debole, ma offre la latenza più bassa sia per le letture sia le per scritture.
* Gli account Azure Cosmos DB configurati con la coerenza finale possono associare qualsiasi numero di aree di Azure con il proprio account Azure Cosmos DB. 
* Il costo di un'operazione di lettura, in termini di unità richieste consumate, con il livello di coerenza finale è il più basso fra tutti i livelli di coerenza di Azure Cosmos DB.

## <a name="configuring-the-default-consistency-level"></a>Configurazione del livello di coerenza predefinito
1. Nell'indice del [portale di Azure](https://portal.azure.com/) fare clic su **Azure Cosmos DB**.
2. Nella pagina **Azure Cosmos DB** selezionare l'account di database da modificare.
3. Nella pagina dell'account fare clic su **Coerenza predefinita**.
4. Nella pagina **Coerenza predefinita** selezionare il nuovo livello di coerenza e fare clic su **Salva**.
   
    ![Schermata con icona Impostazioni e opzione Uniformità predefinita](./media/consistency-levels/database-consistency-level-1.png)

## <a name="consistency-levels-for-queries"></a>Livelli di coerenza per le query
Per impostazione predefinita, per le risorse definite dall'utente, il livello di coerenza per le query è identico a quello delle letture. Per impostazione predefinita, l'indice è aggiornato in modo sincrono a ogni inserimento, sostituzione o eliminazione di un elemento nel contenitore di Cosmos DB. Ciò permette alle query di rispettare lo stesso livello di coerenza delle letture di punti. Benché Azure Cosmos DB sia ottimizzato per la scrittura e supporti volumi elevati di scritture, la manutenzione sincrona dell'indice e la gestione di query coerenti, è possibile configurare determinati contenitori per l'aggiornamento differito dell'indice. L'indicizzazione differita migliora ulteriormente le prestazioni di scrittura ed è ideale per scenari di inserimento in blocco quando un carico di lavoro presenta principalmente un'intensa attività di lettura.  

| Modalità di indicizzazione | Letture | Query |
| --- | --- | --- |
| Coerente (impostazione predefinita) |Selezionare tra assoluta, con decadimento ristretto, sessione, prefisso coerente o finale |Selezionare tra assoluta, con decadimento ristretto, sessione o finale |
| Differita |Selezionare tra assoluta, con decadimento ristretto, sessione, prefisso coerente o finale |Finale |
| Nessuna |Selezionare tra assoluta, con decadimento ristretto, sessione, prefisso coerente o finale |Non applicabile |

Come per le richieste di lettura, è possibile abbassare il livello di coerenza per una particolare richiesta di query specifica in ogni API.

## <a name="consistency-levels-for-the-mongodb-api"></a>Livelli di coerenza per l'API di MongoDB

Azure Cosmos DB implementa attualmente la versione 3.4 di MongoDB, che include due impostazioni di coerenza: assoluta e finale. Azure Cosmos DB è un'applicazione multi-API e, pertanto, le impostazioni di coerenza sono applicabili a livello di account e l'applicazione della coerenza è controllata da ogni API.  Fino a MongoDB 3.6 non esisteva il concetto di coerenza della sessione e, se si imposta un account di API MongoDB per l'uso della coerenza di sessione, quando si usano le API di MongoDB viene eseguito il downgrade della coerenza a "finale". Se è necessaria una garanzia di tipo Read Your Own Write (RYOW) per un account di API MongoDB, il livello di coerenza predefinito per l'account deve essere impostato su "assoluta" o "decadimento ristretto".

## <a name="next-steps"></a>Passaggi successivi
Se si desidera eseguire ulteriori informazioni sui livelli di coerenza e i compromessi, è consigliabile che le risorse seguenti:

* [Video: Replicated Data Consistency explained through baseball di Doug Terry](https://www.youtube.com/watch?v=gluIh8zd26I) (La coerenza dei dati replicati illustrata attraverso il baseball)
* [White paper: Replicated Data Consistency explained through baseball di Doug Terry](http://research.microsoft.com/pubs/157411/ConsistencyAndBaseballReport.pdf) (La coerenza dei dati replicati illustrata attraverso il baseball)
* [Session Guarantees for Weakly Consistent Replicated Data](http://dl.acm.org/citation.cfm?id=383631) (Garanzie di sessione per i dati replicati con coerenza debole)
* [Consistency Tradeoffs in Modern Distributed Database Systems Design: CAP is only part of the story](http://computer.org/csdl/mags/co/2012/02/mco2012020037-abs.html) (Compromessi sulla coerenza nella progettazione di sistemi di database distribuiti moderni: CAP è solo una parte della storia)
* [Probabilistic Bounded Staleness (PBS) for Practical Partial Quorums](http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf) (Decadimento ristretto probabilistico per quorum parziali pratici)
* [Eventual Consistent - Revisited](http://allthingsdistributed.com/2008/12/eventually_consistent.html) (Coerenza finale - Rivisitato)
* [The Load, Capacity, and Availability of Quorum Systems, SIAM Journal on Computing](http://epubs.siam.org/doi/abs/10.1137/S0097539795281232) (Carico, capacità e disponibilità nei sistemi di quorum)
* [Line-up: a complete and automatic linearizability checker (Un controllo completo e automatico della linearità), Procedimenti della conferenza ACM SIGPLAN del 2010 sulla progettazione e l'implementazione dei linguaggi di programmazione](http://dl.acm.org/citation.cfm?id=1806634)
* [Probabilistic bounded staleness for practical partial quorums](http://dl.acm.org/citation.cfm?id=2212359) (Decadimento ristretto probabilistico per quorum parziali pratici)
