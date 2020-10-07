---
title: Panoramica di Database di Azure per PostgreSQL - Hyperscale (Citus)
description: Panoramica dell'opzione di distribuzione Hyperscale (Citus)
author: jonels-msft
ms.author: jonels
ms.custom: mvc
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: overview
ms.date: 09/01/2020
ms.openlocfilehash: 90d3cb106fa93649f7d6dda5ab5755061d118f66
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91268384"
---
# <a name="what-is-azure-database-for-postgresql---hyperscale-citus"></a>Che cos'è Database di Azure per PostgreSQL - Hyperscale (Citus)?

Il database di Azure per PostgreSQL è un servizio di database relazionale in Microsoft Cloud creato per gli sviluppatori. È basato sulla versione community del motore di database [PostgreSQL](https://www.postgresql.org/) open source.

L'opzione di distribuzione Hyperscale (Citus) divide orizzontalmente le query tra più computer tramite partizionamento orizzontale. Il motore di query parallelizza le query SQL in ingresso in questi server per risposte più rapide in set di dati di grandi dimensioni. Risponde ai requisiti delle applicazioni di scalabilità e prestazioni maggiori rispetto a quelle offerte da altre opzioni di distribuzione: in genere carichi di lavoro che si avvicinano o superano i 100 GB di dati.

Hyperscale (Citus) offre:

- Scalabilità orizzontale tra più computer tramite partizionamento orizzontale
- Parallelizzazione delle query tra questi server per risposte più rapide in set di dati di grandi dimensioni
- Supporto ottimale per applicazioni multi-tenant, analisi operative in tempo reale e carichi di lavoro transazionali con velocità effettiva elevata

Le applicazioni compilate per PostgreSQL possono eseguire query distribuite in Hyperscale (Citus) con [librerie di connessione](./concepts-connection-libraries.md) standard e modifiche minime.

## <a name="next-steps"></a>Passaggi successivi

- Per iniziare, [creare il primo gruppo di server ](./quickstart-create-hyperscale-portal.md) di Database di Azure per PostgreSQL - Hyperscale (Citus).
- Vedere la [pagina relativa ai prezzi](https://azure.microsoft.com/pricing/details/postgresql/) per confrontare i costi e usare i calcolatori. Hyperscale (Citus) offre anche sconti per le istanze riservate prepagate. Per informazioni dettagliate, vedere la pagina dei [prezzi di istanze riservate di Hyperscale (Citus)](concepts-hyperscale-reserved-pricing.md).
- Determinare le [dimensioni iniziali](howto-hyperscale-scaling.md#picking-initial-size) ottimali per il gruppo di server
