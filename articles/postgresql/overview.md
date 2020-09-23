---
title: Che cos'è Database di Azure per PostgreSQL
description: Offre una panoramica del servizio di database relazionale Database di Azure per PostgreSQL nel contesto del server flessibile.
author: sunilagarwal
ms.author: sunila
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 09/21/2020
ms.openlocfilehash: 5945f50ada9af6a8d117d3d773ebeae48d5f4085
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90903748"
---
# <a name="what-is-azure-database-for-postgresql"></a>Che cos'è Database di Azure per PostgreSQL?

Database di Azure per PostgreSQL è un servizio di database relazionale in Microsoft Cloud basato sul motore di database [PostgreSQL Community Edition](https://www.postgresql.org/) disponibile con licenza GPLv2. Database di Azure per PostgreSQL offre:

- Disponibilità elevata predefinita.
- Protezione dati con backup automatici e ripristino temporizzato per un massimo di 35 giorni.
- Manutenzione automatica per hardware, sistema operativo e motore di database sottostanti per un servizio sempre sicuro e aggiornato.
- Prestazioni prevedibili, con piano tariffario tutto incluso e pagamento in base al consumo.
- Scalabilità elastica in pochi secondi.
- Sicurezza di livello aziendale e conformità leader di settore per proteggere i dati sensibili inattivi e in transito.
- Monitoraggio e automazione per semplificare la gestione e il monitoraggio per distribuzioni su larga scala.
- Esperienza di supporto leader di settore.

 :::image type="content" source="./media/overview/overview-what-is-azure-postgres.png" alt-text="Database di Azure per PostgreSQL":::

Queste funzionalità richiedono pochissima amministrazione e sono disponibili senza costi aggiuntivi. Consentono di concentrarsi sullo sviluppo rapido di applicazioni e di accelerare il time-to-market, piuttosto che allocare tempo e risorse preziosi alla gestione delle macchine virtuali e dell'infrastruttura. È anche possibile continuare a sviluppare l'applicazione con la piattaforma e gli strumenti open source scelti e renderla disponibile con la rapidità e l'efficienza richiesti dall'azienda senza bisogno di acquisire nuove competenze.

## <a name="deployment-models"></a>Modelli di distribuzione

Database di Azure per PostgreSQL basato su PostgreSQL Community Edition è disponibile in tre modalità di distribuzione:

- Server unico
- Server flessibile (anteprima)
- Hyperscale (Citus)

### <a name="azure-database-for-postgresql---single-server"></a>Database di Azure per PostgreSQL - Server singolo

Il server singolo di Database di Azure per PostgreSQL è un servizio di database completamente gestito con requisiti minimi per le personalizzazioni di database. La piattaforma server singolo è progettata per gestire la maggior parte delle funzioni di gestione di database, ad esempio l'applicazione di patch, i backup, la disponibilità elevata e la sicurezza con configurazione e controllo minimi dell'utente. L'architettura è ottimizzata per la disponibilità elevata predefinita con disponibilità del 99,99% in una singola zona di disponibilità. Supporta attualmente la versione community di PostgreSQL 9.5, 9,6, 10 e 11. Il servizio è attualmente disponibile a livello generale in un'ampia gamma di [aree di Azure](https://azure.microsoft.com/global-infrastructure/services/).

L'opzione di distribuzione Server singolo offre tre piani tariffari: piani Basic, Per utilizzo generico e Con ottimizzazione per la memoria. Ognuno offre diverse funzionalità in termini di risorse per il supporto dei carichi di lavoro dei database. È possibile compilare la prima app in un database di piccole dimensioni con un costo mensile minimo, quindi regolare la scalabilità in base alle esigenze della soluzione. La scalabilità dinamica consente al database di rispondere in modo trasparente ai requisiti delle risorse soggetti a rapidi cambiamenti. Si paga solo per le risorse necessarie, quando sono necessarie. Per informazioni dettagliate, vedere [Piani tariffari](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers).

I server singoli sono ideali per applicazioni native per il cloud progettate per gestire l'applicazione automatizzata di patch senza la necessità di un controllo granulare sulla pianificazione dell'applicazione di patch e sulle impostazioni di configurazione di PostgreSQL personalizzate.

Per una panoramica dettagliata della modalità di distribuzione server singolo, vedere la [panoramica del server singolo](./overview-single-server.md).

### <a name="azure-database-for-postgresql---flexible-server-preview"></a>Database di Azure per PostgreSQL - Server flessibile (anteprima)

Il server flessibile di Database di Azure per PostgreSQL è un servizio di database completamente gestito progettato per offrire un controllo più granulare e una maggiore flessibilità rispetto alle funzioni di gestione e alle impostazioni di configurazione del database. In generale, il servizio offre maggiori flessibilità e personalizzazioni in base ai requisiti dell'utente. L'architettura del server flessibile consente agli utenti di scegliere la disponibilità elevata all'interno di una singola zona di disponibilità e tra più zone di disponibilità. Server flessibile fornisce controlli di ottimizzazione dei costi migliori con la possibilità di arrestare/avviare il server e il livello di calcolo della possibilità di burst, una soluzione ideale per i carichi di lavoro che non necessitano di capacità di calcolo completa in modo continuo. Il servizio supporta attualmente la versione community di PostgreSQL 11 e 12 con i piani per aggiungere presto le versioni più recenti. Il servizio è attualmente disponibile in anteprima pubblica in un'ampia gamma di aree di Azure.

I server flessibili sono ideali per

- Sviluppi di applicazioni che richiedono un controllo e personalizzazioni migliori.
- Controlli di ottimizzazione dei costi con la possibilità di arrestare/avviare il server.
- Disponibilità elevata di ridondanza della zona
- Finestre di manutenzione gestite
  
Per una panoramica dettagliata della modalità di distribuzione server flessibile, vedere la [panoramica del server flessibile](./flexible-server/overview.md).

### <a name="azure-database-for-postgresql--hyperscale-citus"></a>Database di Azure per PostgreSQL - Hyperscale (Citus)

L'opzione Hyperscale (Citus) scala orizzontalmente le query tra più computer tramite partizionamento orizzontale. Il motore di query parallelizza le query SQL in ingresso in questi server per risposte più rapide in set di dati di grandi dimensioni. Risponde ai requisiti di scalabilità e prestazioni superiori delle applicazioni, in genere carichi di lavoro che si avvicinano a 100 GB di dati, o li superano già.

L'opzione di distribuzione Hyperscale (Citus) offre:

- Scalabilità orizzontale tra più computer tramite partizionamento orizzontale
- Parallelizzazione delle query tra questi server per risposte più rapide in set di dati di grandi dimensioni
- Supporto ottimale per applicazioni multi-tenant, analisi operative in tempo reale e carichi di lavoro transazionali con velocità effettiva elevata
  
Le applicazioni compilate per PostgreSQL possono eseguire query distribuite in Hyperscale (Citus) con [librerie di connessione](https://docs.microsoft.com/azure/postgresql/concepts-connection-libraries) standard e modifiche minime.

## <a name="next-steps"></a>Passaggi successivi

Ottenere altre informazioni sulle tre modalità di distribuzione per Database di Azure per PostgreSQL e scegliere le opzioni appropriate in base alle proprie esigenze.

- [Server singolo](./overview-single-server.md)
- [Server flessibile](./flexible-server/overview.md)
- Hyperscale (Citus)
