<properties
	pageTitle="Risoluzione dell'errore Il database nel server non è attualmente disponibile per il database SQL di Azure"
	description="Passaggi per identificare e risolvere gli errori di connessione per il database SQL di Azure."
	services="sql-database"
	documentationCenter=""
	authors="dalechen"
	manager="felixwu"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/29/2016"
	ms.author="daleche"/>

# Risoluzione dell'errore "Il database &lt;x&gt; nel server &lt;y&gt; non è attualmente disponibile. Eseguire un altro tentativo di connessione più tardi.
[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

Quando un'applicazione si connette a un database SQL di Azure, viene visualizzato il messaggio di errore seguente:

```
Error code 40613: "Database <x> on server <y> is not currently available. Please retry the connection later. If the problem persists, contact customer support, and provide them the session tracing ID of <z>"
```

> [AZURE.NOTE] Questo messaggio di errore è in genere temporaneo.

Questo errore si verifica quando il database di Azure viene spostato o riconfigurato e di conseguenza l'applicazione perde la connessione al database SQL. La riconfigurazione del database SQL avviene in seguito a eventi pianificati, nel caso ad esempio degli aggiornamenti software, o non pianificati, ad esempio per l'arresto di un processo o il bilanciamento del carico. La maggior parte degli eventi di riconfigurazione è di breve durata e deve essere completata in meno di 60 secondi al massimo. Tuttavia, il completamento di questi eventi in alcuni casi può richiedere più tempo, ad esempio quando una transazione di grandi dimensioni provoca un ripristino a esecuzione prolungata.

## Passaggi per risolvere problemi di connettività temporanei
1.	Controllare il [Dashboard dei servizi di Microsoft Azure](https://azure.microsoft.com/status) per le interruzioni note che si sono verificate durante il periodo nel quale sono stati segnalati errori dall'applicazione.
2. Le applicazioni che si connettono a un servizio cloud, come il database SQL di Azure, devono prevedere il verificarsi periodico di eventi di riconfigurazione e implementare la logica di ripetizione per gestire gli errori, invece di lasciare che vengano visualizzati dagli utenti come errori dell'applicazione. Esaminare la sezione [Errori temporanei](sql-database-connectivity-issues.md) e [le procedure consigliate e linee guida per la progettazione](sql-database-connect-central-recommendations.md) per altre informazioni e per le strategie di replica generali. Quindi, vedere gli [esempi di codice](sql-database-develop-quick-start-client-code-samples.md) per i dettagli.
3.	Quando un database sta per raggiungere i limiti delle risorse, può sembrare che si stia verificando un problema di connettività temporaneo. Vedere la pagina relativa alla [risoluzione dei problemi di prestazioni](sql-database-troubleshoot-performance.md).
4.	Se i problemi di connettività persistono, oppure se l'applicazione rileva l'errore per più di 60 secondi o se vengono visualizzate più occorrenze dell'errore in un dato giorno, inoltrare una richiesta di supporto tecnico di Azure selezionando **Ottenere supporto** sul sito del [supporto tecnico di Azure](https://azure.microsoft.com/support/options).

## Passaggi successivi
- Se si riceve un errore diverso, leggere il [messaggio di errore](sql-database-develop-error-messages.md) per indicazioni sulla causa.
- Se il problema persiste, consultare il materiale sussidiario in [Risoluzione dei problemi di connessione comuni al database SQL di Azure](sql-database-troubleshoot-common-connection-issues.md).

<!---HONumber=AcomDC_0330_2016-->