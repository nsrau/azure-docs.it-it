---
title: "Il database nel server non è attualmente disponibile durante la connessione al database SQL | Microsoft Docs"
description: "Risolvere l&quot;errore relativo al database nel server che non è attualmente disponibile quando un&quot;applicazione si connette al database SQL."
services: sql-database
documentationcenter: 
author: dalechen
manager: felixwu
editor: 
keywords: "Il database nel server non è attualmente disponibile durante la connessione al database SQL"
ms.assetid: f61999ac-d46b-448a-8830-3b04978d84ec
ms.service: sql-database
ms.custom: troubleshoot
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/21/2016
ms.author: daleche
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: ede7672196e71bc493cd9b758982445e991def1b


---
# <a name="error-database-on-server-is-not-currently-available-when-connecting-to-sql-database"></a>Errore "Il database nel server non è attualmente disponibile" durante la connessione al database SQL
[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

Quando un'applicazione si connette a un database SQL di Azure, viene visualizzato il messaggio di errore seguente:

```
Error code 40613: "Database <x> on server <y> is not currently available. Please retry the connection later. If the problem persists, contact customer support, and provide them the session tracing ID of <z>"
```

> [!NOTE]
> Questo messaggio di errore è in genere temporaneo.
> 
> 

Questo errore si verifica quando il database di Azure viene spostato o riconfigurato e di conseguenza l'applicazione perde la connessione al database SQL. La riconfigurazione del database SQL avviene in seguito a eventi pianificati, nel caso ad esempio degli aggiornamenti software, o non pianificati, ad esempio per l'arresto di un processo o il bilanciamento del carico. La maggior parte degli eventi di riconfigurazione è di breve durata e deve essere completata in meno di 60 secondi al massimo. Tuttavia, il completamento di questi eventi in alcuni casi può richiedere più tempo, ad esempio quando una transazione di grandi dimensioni provoca un ripristino a esecuzione prolungata.

## <a name="steps-to-resolve-transient-connectivity-issues"></a>Passaggi per risolvere problemi di connettività temporanei
1. Controllare nel [Dashboard dei servizi di Microsoft Azure](https://azure.microsoft.com/status) le eventuali interruzioni note che si sono verificate durante il periodo nel quale sono stati segnalati errori dall'applicazione.
2. Le applicazioni che si connettono a un servizio cloud, come il database SQL di Azure, devono prevedere il verificarsi periodico di eventi di riconfigurazione e implementare la logica di ripetizione per gestire gli errori, invece di lasciare che vengano visualizzati dagli utenti come errori dell'applicazione. Per altre informazioni e per le strategie generali di ripetizione dei tentativi, vedere la sezione [Errori temporanei](sql-database-connectivity-issues.md) e le procedure consigliate e linee guida per la progettazione in [Panoramica dello sviluppo di database SQL](sql-database-develop-overview.md). Per informazioni dettagliate, vedere gli esempi di codice in: [Raccolte di connessioni per database SQL e Server SQL](sql-database-libraries.md) .
3. Quando un database sta per raggiungere i limiti delle risorse, può sembrare che si stia verificando un problema di connettività temporaneo. Vedere la pagina relativa alla [risoluzione dei problemi di prestazioni](sql-database-troubleshoot-performance.md).
4. Se i problemi di connettività persistono oppure se l'applicazione rileva l'errore per più di 60 secondi o se vengono visualizzate più occorrenze dell'errore in un dato giorno, inoltrare una richiesta di supporto tecnico di Azure selezionando **Ottieni supporto** nel sito [Supporto tecnico di Azure](https://azure.microsoft.com/support/options) .

## <a name="next-steps"></a>Passaggi successivi
* Se viene visualizzato un errore diverso, leggere il [messaggio di errore](sql-database-develop-error-messages.md) per indicazioni sulla causa.
* Se il problema persiste, consultare il materiale sussidiario in [Risoluzione dei problemi di connessione comuni al database SQL di Azure](sql-database-troubleshoot-common-connection-issues.md).




<!--HONumber=Nov16_HO3-->


