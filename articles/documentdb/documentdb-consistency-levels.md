---
title: Livelli di coerenza in Azure DocumentDB | Microsoft Docs
description: "DocumentDB ha quattro livelli di coerenza per consentire agli sviluppatori di applicazioni di compensare in modo prevedibile coerenza, disponibilità e latenza."
keywords: coerenza finale, documentdb, azure, Microsoft azure
services: documentdb
author: syamkmsft
manager: jhubbard
editor: cgronlun
documentationcenter: 
ms.assetid: 3fe51cfa-a889-4a4a-b320-16bf871fe74c
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/16/2016
ms.author: syamk
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 094729399070a64abc1aa05a9f585a0782142cbf
ms.openlocfilehash: 839f8c8669c06e22635b68018d816a669aec895b
ms.lasthandoff: 03/07/2017


---
# <a name="tunable-data-consistency-levels-in-documentdb"></a>Livelli di coerenza dei dati ottimizzabili in DocumentDB
Azure DocumentDB è stato progettato da zero pensando alla distribuzione globale. È pensato per offrire garanzie di bassa latenza stimabile, Contratto di servizio con disponibilità al 99,99% e più modelli di coerenza ben definiti meno severi. Attualmente DocumentDB offre quattro livelli di coerenza: assoluta, con obsolescenza associata, sessione e finale. Oltre ai modelli di **coerenza assoluta** e **finale** offerti dai database NoSQL, DocumentDB offre anche due modelli di coerenza attentamente codificati e operativi: **con obsolescenza associata** e **sessione** e ha convalidato la loro utilità in casi reali. Questi quattro livelli di coerenza, collettivamente, consentono di bilanciare in modo informato coerenza, disponibilità e latenza. 

## <a name="scope-of-consistency"></a>Ambito di coerenza
L'ambito di granularità della coerenza è limitato alla richiesta del singolo utente. Una richiesta di scrittura può corrispondere a una transazione di inserimento, sostituzione, upsert o eliminazione (con o senza l'esecuzione di un trigger associato di tipo pre o post). Oppure una richiesta di scrittura può corrispondere all'esecuzione transazionale di una stored procedure JavaScript che opera su più documenti all'interno di una partizione. Come per le operazioni di scrittura, anche per una transazione di lettura/query l'ambito equivale alla richiesta del singolo utente. Potrebbe essere chiesto all'utente di scorrere le pagine di un ampio set di risultati, che occupa varie partizioni, ma ciascuna transazione di lettura ha come ambito una singola pagina ed è servita dall'interno di una singola partizione.

## <a name="consistency-levels"></a>Livelli di coerenza
È possibile configurare un livello di coerenza predefinito per l'account di database che si applica a tutte le raccolte (in tutti i database) nel proprio account di database. Per impostazione predefinita, tutte le letture e le query eseguite sulle risorse definite dall'utente useranno il livello di coerenza predefinito che è stato specificato nell'account di database. Tuttavia, è possibile abbassare il livello di coerenza di una determinata richiesta di lettura/query specificando l'intestazione di richiesta [[x-ms-consistency-level]](https://msdn.microsoft.com/library/azure/mt632096.aspx). Esistono quattro tipi di livelli di coerenza supportati dal protocollo di replica di DocumentDB che forniscono un chiaro compromesso tra garanzie di coerenza specifiche e prestazioni, come descritto di seguito.

![DocumentDB offre numerosi modelli di coerenza, ampi e ben definiti, tra cui scegliere][1]

**Assoluta**: 

* la coerenza assoluta offre una garanzia di [linearità](https://aphyr.com/posts/313-strong-consistency-models) ovvero la garanzia che le letture restituiscano la versione più recente di un documento. 
* la coerenza assoluta garantisce che una scrittura sia visibile solo dopo che ne è stato eseguito il commit in modo permanente dal quorum di maggioranza delle repliche. Una scrittura può ottenere o il commit sincrono e permanente da parte della replica primaria e della maggioranza delle repliche secondarie o l'interruzione. Una lettura viene sempre confermata dalla quorum di maggioranza per le letture: un client non potrà mai vedere una scrittura parziale o di cui non sia stato eseguito il commit e leggerà sempre la più recente scrittura confermata. 
* Gli account DocumentDB configurati per usare la coerenza assoluta non possono associare più di un'area di Azure con il loro account DocumentDB. 
* Il costo di un'operazione di lettura (in termini di [unità richiesta](documentdb-request-units.md) consumate) con il livello di coerenza assoluta è più alto rispetto ai livelli sessione e finale, ma uguale a quello del livello con obsolescenza associata.

**Obsolescenza associata**: 

* La coerenza con obsolescenza associata garantisce che il ritardo delle letture sulle scritture sia al massimo pari a *K* versioni o prefissi di un documento o all'intervallo di tempo *t*. 
* Perciò, quando si sceglie l'obsolescenza associata, tale “obsolescenza” può essere configurata in due modi: 
  * Numero di versioni *K* del documento di cui le letture possono essere in ritardo rispetto alle scritture
  * Intervallo di tempo *t* 
* L'obsolescenza associata offre un ordine globale totale tranne all'interno della "finestra di obsolescenza". Si noti che la garanzia di lettura monotona esiste in un'area sia all'interno che all'esterno della "finestra di obsolescenza". 
* L'obsolescenza associata offre una maggiore garanzia di coerenza rispetto alla coerenza di sessione o finale. Per le applicazioni distribuite a livello globale, è consigliabile usare l'obsolescenza associata per gli scenari in cui si desidera una coerenza assoluta ma si desidera anche il 99,99% di disponibilità e bassa latenza. 
* Gli account DocumentDB configurati con la coerenza con obsolescenza associata possono associare qualsiasi numero di aree di Azure con il proprio account DocumentDB. 
* Il costo di un'operazione di lettura (in termini di unità richiesta consumate) con l'obsolescenza associata è più alto rispetto ai livelli sessione e finale, ma uguale a quello del livello assoluto.

**Sessione**: 

* a differenza dei modelli di coerenza globale offerti dai livelli di coerenza assoluta e con obsolescenza associata, la coerenza di "sessione" ha come ambito una sessione del client. 
* La coerenza di sessione è ideale per tutti gli scenari in cui è coinvolto un dispositivo o una sessione utente poiché garantisce letture monotone, scritture monotone e garanzie di lettura di ciò che si scrive (RYW). 
* La coerenza di sessione offre una coerenza prevedibile per una sessione e la massima velocità di scrittura, con latenza minima per scrittura e lettura. 
* Gli account DocumentDB configurati con la coerenza di sessione possono associare qualsiasi numero di aree di Azure con il proprio account DocumentDB. 
* Il costo di un'operazione di lettura (in termini di unità richiesta consumate) con il livello di coerenza di sessione è minore rispetto ai livelli assoluto e con obsolescenza associata, ma maggiore rispetto al livello finale

**Finale**: 

* la coerenza finale garantisce che, in assenza di ulteriori scritture, alla fine le repliche all'interno del gruppo convergeranno. 
* La coerenza finale è la forma più debole di coerenza, in cui un client può ottenere nel tempo valori obsoleti rispetto a quelli già visualizzati in passato.
* La coerenza finale rappresenta il livello più debole, ma offre la latenza più bassa sia per le letture sia le per scritture.
* Gli account DocumentDB che sono configurati con coerenza finale possono associare un numero qualsiasi di aree di Azure con il proprio account DocumentDB. 
* Il costo di un'operazione di lettura (in termini di unità richiesta consumate) con il livello di coerenza finale è il più basso fra tutti i livelli di coerenza di DocumentDB.

## <a name="consistency-guarantees"></a>Garanzie di coerenza
La tabella seguente descrive varie garanzie di coerenza che corrispondono ai quattro livelli di coerenza.

| Garanzia | Assoluta | Obsolescenza associata | sessione | Finale |
| --- | --- | --- | --- | --- |
| **Ordine globale totale** |Sì |Sì, all'esterno della "finestra di obsolescenza" |No, ordine di “sessione” parziale |No |
| **Garanzia di coerenza prefisso** |Sì |Sì |Sì |Sì |
| **Letture monotone** |Sì |Sì, tra aree all'esterno della finestra di obsolescenza e all'interno di un'area per tutto il tempo. |Sì, per la sessione data |No |
| **Scritture monotone** |Sì |Sì |Sì |Sì |
| **Lettura delle proprie scritture** |Sì |Sì |Sì (nell'area della scrittura) |No |

## <a name="configuring-the-default-consistency-level"></a>Configurazione del livello di coerenza predefinito
1. Nell'indice del [portale di Azure](https://portal.azure.com/)fare clic su **DocumentDB (NoSQL)**.
2. Nel pannello **DocumentDB (NoSQL)** selezionare l'account di database da modificare.
3. Nel pannello dell'account fare clic su **Coerenza predefinita**.
4. Nel pannello **Default Consistency** (Uniformità predefinita) selezionare il nuovo livello di coerenza e fare clic su **Salva**.
   
    ![Schermata con icona Impostazioni e opzione Uniformità predefinita](./media/documentdb-consistency-levels/database-consistency-level-1.png)

> [!NOTE]
> La configurazione del livello di coerenza predefinito non è supportata all'interno dell’[emulatore di Azure DocumentDB](documentdb-nosql-local-emulator.md). 

## <a name="consistency-levels-for-queries"></a>Livelli di coerenza per le query
Per impostazione predefinita, per le risorse definite dall'utente, il livello di coerenza per le query è identico a quello delle letture. Per impostazione predefinita, l'indice viene aggiornato in modo sincrono a ogni inserimento, sostituzione o eliminazione di un documento nella raccolta. Ciò permette alle query di rispettare lo stesso livello di coerenza delle letture di documenti. Benché DocumentDB sia ottimizzato per la scrittura e supporti volumi elevati di scritture di documenti, la manutenzione sincrona dell'indice e la gestione di query coerenti, è possibile configurare determinate raccolte per l'aggiornamento differito dell'indice. L'indicizzazione differita migliora ulteriormente le prestazioni di scrittura ed è ideale per scenari di inserimento in blocco quando un carico di lavoro presenta principalmente un'intensa attività di lettura.  

| Modalità di indicizzazione | Letture | Query |
| --- | --- | --- |
| Coerente (impostazione predefinita) |Selezionare tra assoluta, con obsolescenza associata, sessione o finale |Selezionare tra assoluta, con obsolescenza associata, sessione o finale |
| Differita |Selezionare tra assoluta, con obsolescenza associata, sessione o finale |Finale |
| Nessuna |Selezionare tra assoluta, con obsolescenza associata, sessione o finale |Non applicabile |

Come per le richieste di lettura, è possibile abbassare il livello di coerenza per una particolare richiesta di query specificando l'intestazione di richiesta [x-ms-consistency-level](https://msdn.microsoft.com/library/azure/mt632096.aspx) .

## <a name="next-steps"></a>Passaggi successivi
Se si desidera eseguire ulteriori informazioni sui livelli di coerenza e i compromessi, è consigliabile che le risorse seguenti:

* Doug Terry. Video: Replicated Data Consistency explained through baseball (La coerenza dei dati replicati illustrata attraverso il baseball).   
  [https://www.youtube.com/watch?v=gluIh8zd26I](https://www.youtube.com/watch?v=gluIh8zd26I)
* Doug Terry. Replicated Data Consistency explained through baseball.   
  [http://research.microsoft.com/pubs/157411/ConsistencyAndBaseballReport.pdf](http://research.microsoft.com/pubs/157411/ConsistencyAndBaseballReport.pdf)
* Doug Terry. Session Guarantees for Weakly Consistent Replicated Data.   
  [http://dl.acm.org/citation.cfm?id=383631](http://dl.acm.org/citation.cfm?id=383631)
* Daniel Abadi. Svantaggi della coerenza nella Progettazione moderna distribuita di sistemi di Database: CAP è solo una parte del brano".   
  [http://computer.org/csdl/mags/co/2012/02/mco2012020037-abs.html](http://computer.org/csdl/mags/co/2012/02/mco2012020037-abs.html)
* Peter Bailis, Shivaram Venkataraman, Michael J. Franklin, Joseph M. Hellerstein, Ion Stoica. Probabilistic Bounded Staleness (PBS) for Practical Partial Quorums.   
  [http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf](http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
* Werner Vogels. Coerente Finale - Revisited.    
  [http://allthingsdistributed.com/2008/12/eventually_consistent.html](http://allthingsdistributed.com/2008/12/eventually_consistent.html)

[1]: ./media/documentdb-consistency-levels/consistency-tradeoffs.png

