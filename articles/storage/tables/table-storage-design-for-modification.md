---
title: Progettare tabelle di archiviazione di Azure per la modifica dei dati | Microsoft Docs
description: Progettare tabelle per la modifica dei dati nell'archiviazione tabelle di Azure.
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: article
ms.date: 04/23/2018
ms.author: sngun
ms.component: tables
ms.openlocfilehash: 5f67a8ffde24d3c3e39065806b07bdd5cba2857a
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2018
ms.locfileid: "39522044"
---
# <a name="design-for-data-modification"></a>Progettazione per la modifica dei dati
Questo articolo esamina le considerazioni relative alla progettazione per ottimizzare inserimenti, aggiornamenti ed eliminazioni. In alcuni casi, sarà necessario valutare il compromesso tra progettazioni che ottimizzano le query e progettazioni che ottimizzano la modifica dei dati, come avviene per le progettazioni per i database relazionali (anche se le tecniche per gestire i compromessi tra progettazioni sono diverse in un database relazionale). La sezione [Modelli di progettazione tabelle](#table-design-patterns) descrive in dettaglio alcuni modelli di progettazione per il servizio tabelle ed evidenzia alcuni di questi compromessi. In pratica si vedrà che molte progettazioni ottimizzate per le query delle entità vanno bene anche per la modifica delle entità.  

## <a name="optimize-the-performance-of-insert-update-and-delete-operations"></a>Ottimizzare le prestazioni delle operazioni di inserimento, aggiornamento ed eliminazione
Per aggiornare o eliminare un'entità, è necessario poterla identificare usando i valori **PartitionKey** e **RowKey**. In questo senso la scelta di **PartitionKey** e **RowKey** per modificare le entità deve seguire criteri simili a quelli usati per la scelta di supportare le query di tipo punto, perché l'obiettivo è identificare le entità nel modo più efficiente possibile. Si vuole evitare di usare una scansione di tabella o di partizione inefficiente per trovare un'entità e poter individuare i valori **PartitionKey** e **RowKey** necessari per aggiornarla o eliminarla.  

I modelli seguenti nella sezione [Modelli di progettazione tabelle](#table-design-patterns) consentono di ottimizzare le prestazioni delle operazioni di inserimento, aggiornamento ed eliminazione:  

* [Modello di eliminazione volume elevato](table-storage-design-patterns.md#high-volume-delete-pattern) - Abilita l'eliminazione di un volume elevato di entità mediante l'archiviazione di tutte le entità per l'eliminazione simultanea nella relativa tabella separata. Per eliminare le entità, eliminare la tabella.  
* [Modello di serie di dati](table-storage-design-patterns.md#data-series-pattern) - Archivia serie di dati complete in un'unica entità per ridurre al minimo il numero di richieste effettuate.  
* [Modello di entità di grandi dimensioni](table-storage-design-patterns.md#wide-entities-pattern) - Usa più entità fisiche per archiviare entità logiche con più di 252 proprietà.  
* [Modello di entità di grandi dimensioni](table-storage-design-patterns.md#large-entities-pattern) - Usa l'archiviazione BLOB per archiviare i valori di proprietà di grandi dimensioni.  

## <a name="ensure-consistency-in-your-stored-entities"></a>Assicurare la coerenza nelle entità archiviate
L'altro aspetto importante che influisce sulla scelta delle chiavi per ottimizzare la modifica dei dati è come assicurare la coerenza usando le transazioni atomiche. È possibile usare una transazione EGT solo per agire sulle entità archiviate nella stessa partizione.  

I modelli seguenti nell'articolo [Modelli di progettazione tabella](table-storage-design-patterns.md) consentono la gestione della coerenza:  

* [Modello per indice secondario intrapartizione](table-storage-design-patterns.md#intra-partition-secondary-index-pattern) - Archivia più copie di ogni entità usando valori **RowKey** diversi (nella stessa partizione) per consentire ricerche rapide ed efficienti e ordinamenti alternativi usando valori **RowKey** diversi.  
* [Modello per indice secondario intrapartizione](table-storage-design-patterns.md#inter-partition-secondary-index-pattern) - Archivia più copie di ogni entità usando valori RowKey diversi in partizioni separate o in tabelle separate per consentire ricerche rapide ed efficienti e ordinamenti alternativi usando valori **RowKey** diversi.  
* [Modello per transazioni con coerenza finale](table-storage-design-patterns.md#eventually-consistent-transactions-pattern) - Abilita un comportamento di coerenza finale tra i limiti della partizione o i limiti del sistema di archiviazione usando le code di Azure.
* [Modello per entità di indice](table-storage-design-patterns.md#index-entities-pattern) - Mantiene le entità di indice per consentire ricerche efficienti che restituiscano elenchi di entità.  
* [Modello di denormalizzazione](table-storage-design-patterns.md#denormalization-pattern) - Combina i dati correlati in una singola entità per consentire di recuperare tutti i dati necessari con un sola query di tipo punto.  
* [Modello di serie di dati](table-storage-design-patterns.md#data-series-pattern) - Archivia serie di dati complete in un'unica entità per ridurre al minimo il numero di richieste effettuate.  

Per informazioni sulle transazioni di gruppi di entità, vedere la sezione [Transazioni di gruppi di entità](table-storage-design.md#entity-group-transactions).  

## <a name="ensure-your-design-for-efficient-modifications-facilitates-efficient-queries"></a>Verificare che la capacità della progettazione per modifiche efficienti consenta query efficienti
In molti casi, una progettazione per query efficienti consente modifiche efficienti, ma è consigliabile valutare sempre se questa condizione si applica a uno specifico scenario. Alcuni dei modelli nell'articolo [Modelli di progettazione tabelle](table-storage-design-patterns.md) valutano in modo esplicito i compromessi tra la query e la modifica delle entità. Inoltre è sempre consigliabile tenere in considerazione il numero di ogni tipo di operazione.  

I seguenti modelli nell'articolo [Modelli di progettazione tabelle](table-storage-design-patterns.md) consentono di gestire i compromessi tra la progettazione per query efficienti e la progettazione per una modifica efficiente dei dati:  

* [Modello per chiave composta](table-storage-design-patterns.md#compound-key-pattern) - Usa valori **RowKey** composti per consentire a un client di cercare dati correlati con una sola query di tipo punto.  
* [Modello della parte finale del log](table-storage-design-patterns.md#log-tail-pattern) - Recupera le entità *n* aggiunte più di recente a una partizione in base a un valore **RowKey** che usa un ordinamento inverso di data e ora.  
