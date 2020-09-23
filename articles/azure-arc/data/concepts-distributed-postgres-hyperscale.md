---
title: Concetti per la distribuzione dei dati e la scalabilità orizzontale con un gruppo di server con iperscalabilità PostgreSQL abilitato per Arc
titleSuffix: Azure Arc enabled data services
description: Concetti per la distribuzione dei dati con un gruppo di server con iperscalabilità PostgreSQL abilitato per Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: c01da4aed9e27296ea7b570420bb190b16749848
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90939783"
---
# <a name="concepts-for-distributing-data-with-arc-enabled-postgresql-hyperscale-server-group"></a>Concetti per la distribuzione dei dati con un gruppo di server con iperscalabilità PostgreSQL abilitato per Arc

Questo articolo illustra i concetti chiave che sono importanti per sfruttare al meglio la scalabilità di PostgreSQL abilitata per Azure Arc.
Gli articoli collegati di seguito fanno riferimento ai concetti illustrati per la scalabilità di database di Azure per PostgreSQL (CITUS). Si tratta della stessa tecnologia di Azure Arc abilitata per l'iperscalabilità di PostgreSQL, in modo da applicare gli stessi concetti e prospettive.

**Qual è la differenza tra di essi?**
- _Iperscalabilità per database di Azure per PostgreSQL (CITUS)_

Questo è il fattore di forma di iperscalabilità del motore di database Postgres disponibile come database come servizio in Azure (PaaS). È alimentato dall'estensione CITUS che consente l'esperienza di iperscalabilità. In questo fattore di forma il servizio viene eseguito nei Data Center Microsoft ed è gestito da Microsoft.

- _Iperscalabilità PostgreSQL abilitata per Azure Arc_

Questo è il fattore di forma di iperscalabilità del motore di database Postgres offerto disponibile con il servizio dati abilitato per Azure Arc. In questo fattore di forma, i clienti forniscono l'infrastruttura che ospita i sistemi e li gestisce.

I concetti chiave sull'iperscalabilità di PostgreSQL abilitata per Azure Arc sono riepilogati di seguito:

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="nodes-and-tables"></a>Nodi e tabelle
È importante conoscere i concetti seguenti per sfruttare al meglio i vantaggi offerti da Azure Arc con iperscalabilità Postgres abilitata:
- Nodi Postgres specializzati in Azure Arc abilitata per l'iperscalabilità PostgreSQL: coordinatore e ruoli di lavoro
- Tipi di tabelle: tabelle distribuite, tabelle di riferimento e tabelle locali
- Partizioni

Per altre informazioni [, vedere nodi e tabelle in database di Azure per PostgreSQL – iperscalabilità (CITUS)](../../postgresql/concepts-hyperscale-nodes.md). 

## <a name="determine-the-application-type"></a>Determinare il tipo di applicazione
È importante identificare chiaramente il tipo di applicazione che si sta compilando. Perché? Poiché l'esecuzione di query efficienti su un gruppo di server con iperscalabilità PostgreSQL abilitato per Azure Arc richiede che le tabelle vengano distribuite correttamente tra i server. La distribuzione consigliata varia in base al tipo di applicazione e ai relativi modelli di query. Sono disponibili due tipi di applicazioni che funzionano in modo ottimale in Azure Arc con iperscalabilità Postgres abilitata:
- Applicazioni multi-tenant
- Applicazioni in tempo reale

Il primo passaggio nella modellazione dei dati consiste nell'identificare quali sono più simili all'applicazione.

Vedere i dettagli nel [determinare il tipo di applicazione](../../postgresql/concepts-hyperscale-app-type.md).


## <a name="choose-a-distribution-column"></a>Scegliere una colonna di distribuzione
Perché scegliere una colonna distribuita?

Questa è una delle decisioni di modellazione più importanti da prendere. L'iperscalabilità di PostgreSQL abilitata per Azure Arc archivia le righe nelle partizioni in base al valore della colonna di distribuzione Rows. La scelta corretta raggruppa i dati correlati sugli stessi nodi fisici, rendendo rapidamente le query e aggiunge il supporto per tutte le funzionalità SQL. Una scelta non corretta rende l'esecuzione lenta del sistema e non supporta tutte le funzionalità SQL tra i nodi. Questo articolo fornisce suggerimenti sulle colonne di distribuzione per i due scenari più comuni di iperscalabilità.

Per informazioni, vedere [scegliere le colonne di distribuzione](../../postgresql/concepts-hyperscale-choose-distribution-column.md).


## <a name="table-colocation"></a>Condivisione delle tabelle

La condivisione percorso si riferisce all'archiviazione di informazioni correlate negli stessi nodi. Le query possono essere eseguite rapidamente quando tutti i dati necessari sono disponibili senza il traffico di rete. La condivisione dei dati correlati in nodi diversi consente l'esecuzione efficiente delle query in parallelo in ogni nodo.

Vedere i dettagli in condivisione [percorso tabella](../../postgresql/concepts-hyperscale-colocation.md).


## <a name="next-steps"></a>Passaggi successivi
- [Scopri di più sulla creazione dell'iperscalabilità PostgreSQL abilitata per Azure Arc](create-postgresql-hyperscale-server-group.md)
- [Informazioni su come scalare i gruppi di server con scalabilità orizzontale di PostgreSQL abilitati per Azure Arc creati nel controller di dati Arc](scale-out-postgresql-hyperscale-server-group.md)
- [Scopri di più su Azure Arc Enabled Data Services](https://azure.microsoft.com/services/azure-arc/hybrid-data-services)
- [Scopri di più su Azure Arc](https://aka.ms/azurearc)

