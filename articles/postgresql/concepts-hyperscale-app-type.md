---
title: Determinare il tipo di applicazione-iperscala (CITUS)-database di Azure per PostgreSQL
description: Identificare l'applicazione per la modellazione dei dati distribuita efficace
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 07/17/2020
ms.openlocfilehash: 92333857177d33307d6997bfcbdf79787d3ab127
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90895957"
---
# <a name="determining-application-type"></a>Determinazione del tipo di applicazione

L'esecuzione di query efficienti su un gruppo di server con iperscalabilità (CITUS) richiede che le tabelle vengano distribuite correttamente tra i server. La distribuzione consigliata varia in base al tipo di applicazione e ai relativi modelli di query.

Ci sono molto due tipi di applicazioni che funzionano bene in iperscalabilità (CITUS). Il primo passaggio nella modellazione dei dati consiste nell'identificare quali sono più simili all'applicazione.

## <a name="at-a-glance"></a>A colpo d'occhio

| Applicazioni multi-tenant                                 | Applicazioni in tempo reale                                |
|-----------------------------------------------------------|-------------------------------------------------------|
| A volte dozzine o centinaia di tabelle nello schema          | Numero ridotto di tabelle                                |
| Query relative a un tenant (società/Archivio) alla volta | Query di analisi relativamente semplici con le aggregazioni |
| Carichi di lavoro OLTP per servire i client Web                    | Volume di inserimento elevato di dati quasi non modificabili           |
| Carichi di lavoro OLAP che servono query analitiche per ogni tenant   | Spesso centrato su una tabella di grandi dimensioni di eventi            |

## <a name="examples-and-characteristics"></a>Esempi e caratteristiche

**Applicazione multi-tenant**

> Si tratta in genere di applicazioni SaaS che servono altre società, account o organizzazioni. La maggior parte delle applicazioni SaaS è intrinsecamente relazionale. Hanno una dimensione naturale su cui distribuire i dati tra i nodi: solo la partizione in base all' \_ ID tenant.
>
> Iperscalabilità (CITUS) consente di scalare il database a milioni di tenant senza dover riprogettare l'applicazione. È possibile gestire la semantica relazionale necessaria, ad esempio join, vincoli di chiave esterna, transazioni, ACID e coerenza.
>
> -   **Esempi**: siti Web in cui vengono ospitati i negozi per altre aziende, ad esempio una soluzione di marketing digitale o uno strumento di automazione delle vendite.
> -   **Caratteristiche**: query relative a un singolo tenant anziché Unione di informazioni tra i tenant. Sono inclusi i carichi di lavoro OLTP per servire i client Web e i carichi di lavoro OLAP che consentono di eseguire query analitiche per ogni tenant. Avere dozzine o centinaia di tabelle nello schema del database è anche un indicatore per il modello di dati multi-tenant.
>
> Il ridimensionamento di un'app multi-tenant con iperscalabilità (CITUS) richiede anche modifiche minime al codice dell'applicazione. Sono supportati i Framework più diffusi, come Ruby on Rails e Django.

**Analisi in tempo reale**

> Le applicazioni richiedono un parallelismo massiccio, coordinando centinaia di core per ottenere risultati rapidi in query numeriche, statistiche o di conteggio.  Tramite il partizionamento orizzontale e parallelizzazione query SQL su più nodi, l'iperscalabilità (CITUS) rende possibile l'esecuzione di query in tempo reale su miliardi di record in meno di un secondo.
>
> Le tabelle nei modelli di dati di analisi in tempo reale vengono in genere distribuite in base a colonne quali \_ ID utente, \_ ID host o \_ ID dispositivo.
>
> -   **Esempi**: dashboard di analisi rivolte ai clienti che richiedono tempi di risposta in frazioni di secondo.
> -   **Caratteristiche**: poche tabelle, spesso incentrate su una tabella di grandi dimensioni di eventi di dispositivi, siti o utenti e che richiedono un volume di inserimento elevato di dati quasi non modificabili. Query di analisi relativamente semplici (ma a elevato utilizzo di calcolo) che coinvolgono diverse aggregazioni e gruppi di sosta.

Se la situazione è analoga a quella del caso precedente, il passaggio successivo consiste nel decidere come partizionare i dati nel gruppo di server. \'È necessario che la scelta delle colonne di distribuzione dell'amministratore del database corrisponda ai modelli di accesso delle query tipiche per garantire prestazioni ottimali.

## <a name="next-steps"></a>Passaggi successivi

* [Scegliere una colonna di distribuzione](concepts-hyperscale-choose-distribution-column.md) per le tabelle nell'applicazione per distribuire i dati in modo efficiente
