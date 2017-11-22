---
title: Presentazione dell'app Wingtips - Database SQL di Azure | Microsoft Docs
description: Informazioni sui modelli di tenancy database e sull'applicazione SaaS Wingtips di esempio per il database SQL di Azure nell'ambiente cloud.
keywords: esercitazione database SQL
services: sql-database
documentationcenter: 
author: billgib
manager: craigg
editor: MightyPen
ms.service: sql-database
ms.custom: scale out apps
ms.workload: Active
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: billgib;genemi
ms.openlocfilehash: 96e031835905057a9ab2b3ee4023b08de092dd8e
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2017
---
# <a name="welcome-to-the-wingtip-tickets-sample-saas-azure-sql-database-tenancy-app"></a>Introduzione all'app SaaS Wingtip Tickets di esempio del tenancy del database SQL di Azure

Introduzione all'applicazione SaaS Wingtip Tickets di esempio del tenancy del database SQL di Azure e alle relative esercitazioni. Per tenancy del database si intende la modalità di isolamento dei dati offerta dall'app ai client che pagano per essere ospitati nell'applicazione. In parole molto semplici, ogni client ha a disposizione un database tutto per sé o ne condivide uno con altri client.

## <a name="wingtip-tickets-app"></a>App Wingtip Tickets

L'applicazione di esempio Wingtip Tickets illustra gli effetti dei diversi modelli di tenancy del database sulla progettazione e sulla gestione di applicazioni SaaS multi-tenant. Le esercitazioni fornite a corredo descrivono direttamente questi stessi effetti. L'app Wingtip Tickets è basata sul database SQL di Azure.

L'app Wingtip Tickets è concepita in modo da gestire diversi scenari di progettazione e gestione usati da client SaaS effettivi. I modelli d'uso emersi vengono presi in considerazione in Wingtip Tickets.

Bastano cinque minuti per installare l'app Wingtip Tickets nella sottoscrizione di Azure. L'installazione include l'inserimento di dati di esempio per diversi tenant. È possibile installare l'applicazione e gli script di gestione per tutti i modelli in tutta sicurezza perché le installazioni non interagiscono o interferiscono tra loro.

#### <a name="code-in-github"></a>Codice in Github

Il codice dell'applicazione e gli script di gestione sono tutti disponibili in GitHub:

- Modello di **app autonoma**: [repository WingtipTicketsSaaS-StandaloneApp](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp)
- Modello di **database per tenant**: [repository WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant).
- Modello di **multi-tenant partizionato**: [repository WingtipTicketsSaaS-MultiTenantDB](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDB).

Per tutti i modelli elencati in precedenza viene riutilizzata la stessa base di codice dell'app Wingtip Tickets. Per iniziare a creare progetti SaaS personalizzati, è possibile usare il codice disponibile in GitHub.



## <a name="major-database-tenancy-models"></a>Principali modelli di tenancy di database

Wingtip Tickets è un'applicazione SaaS per la vendita di biglietti per eventi. Wingtip offre i servizi richiesti dalle strutture per eventi. Le caratteristiche di ogni struttura sono le seguenti:

- Paga per essere ospitata nell'applicazione.
- È un *tenant* in Wingtip.
- Ospita eventi. Gli eventi interessati sono i seguenti:
    - Prezzi dei biglietti.
    - Vendite dei biglietti.
    - Clienti che acquistano i biglietti.

L'app, unitamente agli script di gestione e alle esercitazioni, illustra uno scenario SaaS completo. Lo scenario include le attività seguenti:

- Provisioning dei tenant.
- Monitoraggio e gestione delle prestazioni.
- Gestione degli schemi.
- Creazione di report e analisi tra tenant.

Tutte le attività vengono fornite indipendentemente dalle dimensioni richieste.



## <a name="code-samples-for-each-tenancy-model"></a>Esempi di codice per ogni modello di tenancy

Viene dato risalto a un set di modelli di applicazione. È però possibile che in altre implementazioni vengano combinati elementi di due o più modelli.

#### <a name="standalone-app-model"></a>Modello di app autonoma

![Modello di app autonoma][standalone-app-model-62s]

In questo modello si usa un'applicazione a tenant singolo. Di conseguenza, questo modello richiede un solo database e archivia i dati relativi a un unico tenant. Il tenant è quindi completamente isolato rispetto agli altri tenant nel database.

È possibile usare questo modello quando si vendono istanze dell'app a client diversi, in modo che ognuno possa eseguirlo autonomamente. Il client è quindi l'unico tenant. Mentre il database archivia i dati per un solo client, i dati vengono archiviati per più clienti del client.

#### <a name="database-per-tenant"></a>Database per tenant

![Modello di database per tenant][database-per-tenant-model-35d]

Questo modello include più tenant nell'istanza dell'applicazione. Per ogni nuovo tenant viene tuttavia allocato un altro database utilizzabile solo dal nuovo tenant.

Questo modello fornisce l'isolamento completo del database per ogni tenant. Il servizio database SQL di Azure include funzionalità sufficientemente sofisticate per rendere plausibile questo modello.

- [Introduzione a un esempio di app SaaS multi-tenant di database SQL][saas-dbpertenant-wingtip-app-overview-15d] per maggiori informazioni su questo modello.

#### <a name="sharded-multi-tenant-databases-the-hybrid"></a>Modello ibrido con database multi-tenant partizionati

![Modello di database multi-tenant partizionato ibrido][sharded-multitenantdb-model-hybrid-79m]

Questo modello include più tenant nell'istanza dell'applicazione. Anche questo modello include più tenant in alcuni o tutti i relativi database. Questo modello è utile per offrire diversi livelli di servizio in modo che i clienti possano pagare di più se danno importanza all'isolamento completo del database.

Lo schema di ogni database include un identificatore del tenant. Tale identificatore è presente anche nei database in cui è archiviato un solo tenant.

- [Introduzione a un esempio di app SaaS multi-tenant di database SQL][saas-multitenantdb-get-started-deploy-89i]



## <a name="tutorials-for-each-tenancy-model"></a>Esercitazioni per ogni modello di tenancy

Ogni modello tenancy è documentato nel modo seguente:

- Una serie di articoli di esercitazioni.
- Codice sorgente archiviato in un repository Github dedicato al modello:
    - Codice dell'applicazione Wingtip Tickets.
    - Codice di script per gli scenari di gestione.

#### <a name="tutorials-for-management-scenarios"></a>Esercitazioni per gli scenari di gestione.

Gli articoli delle esercitazioni per ogni modello illustrano gli scenari di gestione seguenti:

- Provisioning del tenant.
- Monitoraggio e gestione delle prestazioni.
- Gestione degli schemi.
- Creazione di report e analisi tra tenant.
- Ripristino di un tenant a un punto precedente.
- Ripristino di emergenza.



## <a name="next-steps"></a>Passaggi successivi

- [Introduzione a un esempio di app SaaS multi-tenant di database SQL][saas-dbpertenant-wingtip-app-overview-15d] per maggiori informazioni su questo modello.

- [Modelli di tenancy di database delle applicazioni SaaS multi-tenant][multi-tenant-saas-database-tenancy-patterns-60p]



<!-- Image references. -->

[standalone-app-model-62s]: media/saas-tenancy-welcome-wingtip-tickets-app/model-standalone-app.png "Modello di app autonoma"

[database-per-tenant-model-35d]: media/saas-tenancy-welcome-wingtip-tickets-app/model-database-per-tenant.png "Modello di database per tenant"

[sharded-multitenantdb-model-hybrid-79m]: media/saas-tenancy-welcome-wingtip-tickets-app/model-sharded-multitenantdb-hybrid.png "Modello di database multi-tenant partizionato ibrido"



<!-- Article references. -->

[saas-dbpertenant-wingtip-app-overview-15d]: saas-dbpertenant-wingtip-app-overview.md

[multi-tenant-saas-database-tenancy-patterns-60p]: saas-tenancy-app-design-patterns.md

[saas-multitenantdb-get-started-deploy-89i]: saas-multitenantdb-get-started-deploy.md


