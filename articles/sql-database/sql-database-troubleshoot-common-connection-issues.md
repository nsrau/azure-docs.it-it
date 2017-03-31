---
title: Risoluzione dei problemi di connessione comuni al database SQL di Azure
description: Passaggi per identificare e risolvere gli errori di connessione comuni al database SQL di Azure.
services: sql-database
documentationcenter: 
author: dalechen
manager: cshepard
editor: 
ms.assetid: ac463d1c-aec8-443d-b66e-fa5eadcccfa8
ms.service: sql-database
ms.custom: troubleshoot
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/03/2017
ms.author: daleche
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: 7e48069b84c1048617a86fbc334a04462b52deda
ms.lasthandoff: 03/10/2017


---
# <a name="troubleshoot-connection-issues-to-azure-sql-database"></a>Risoluzione dei problemi di connessione al database SQL di Azure
Quando la connessione al database SQL di Azure non riesce, vengono visualizzati [messaggi di errore](sql-database-develop-error-messages.md). Questo articolo tratta un argomento centrale che aiuta l'utente a risolvere i problemi di connettività del database SQL di Azure. Presenta le [cause comuni](#cause) dei problemi di connessione, consiglia [uno strumento di risoluzione dei problemi](#try-the-troubleshooter-for-azure-sql-database-connectivity-issues) che aiuta a identificare il problema e fornisce i passaggi necessari per risolvere gli [errori temporanei](#troubleshoot-transient-errors) e gli [errori persistenti o non temporanei](#troubleshoot-the-persistent-errors). Infine elenca [tutti gli articoli pertinenti sui problemi di connettività del database SQL di Azure](#all-topics-for-azure-sql-database-connection-problems).

In caso di problemi di connessione, provare i passaggi di risoluzione dei problemi descritti in questo articolo.
[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="cause"></a>Causa
I problemi di connessione possono dipendere da una delle seguenti cause:

* Impossibilità di applicare le procedure consigliate e delle linee guida di progettazione durante il processo di progettazione delle applicazioni.  Per iniziare, vedere [Panoramica dello sviluppo di database SQL](sql-database-develop-overview.md) .
* Riconfigurazione del database SQL di Azure
* Impostazioni del firewall
* Timeout della connessione
* Informazioni di accesso non corrette
* Raggiungimento del limite massimo su alcune risorse del database SQL di Azure

I problemi di connessione al database SQL di Azure possono essere classificati a grandi linee nelle seguenti categorie:

* [Errori temporanei (di breve durata o intermittenti)](#troubleshoot-transient-errors)
* [Errori non temporanei o permanenti (gli errori che si ripetono regolarmente)](#troubleshoot-the-persistent-errors)

## <a name="try-the-troubleshooter-for-azure-sql-database-connectivity-issues"></a>Provare la procedura di risoluzione dei problemi di connettività del database SQL di Azure
Se si verifica un errore di connessione specifico, provare [questo strumento](https://support.microsoft.com/help/10085/troubleshooting-connectivity-issues-with-microsoft-azure-sql-database)che consente di identificare rapidamente e risolvere il problema.

## <a name="troubleshoot-transient-errors"></a>Risolvere i problemi causati da errori temporanei

Quando un'applicazione si connette a un database SQL di Azure, viene visualizzato il messaggio di errore seguente:

```
Error code 40613: "Database <x> on server <y> is not currently available. Please retry the connection later. If the problem persists, contact customer support, and provide them the session tracing ID of <z>"
```

> [!NOTE]
> Questo messaggio di errore è in genere temporaneo.
> 
> 

Questo errore si verifica quando il database di Azure viene spostato o riconfigurato e di conseguenza l'applicazione perde la connessione al database SQL. La riconfigurazione del database SQL avviene in seguito a eventi pianificati, nel caso ad esempio degli aggiornamenti software, o non pianificati, ad esempio per l'arresto di un processo o il bilanciamento del carico. La maggior parte degli eventi di riconfigurazione è di breve durata e deve essere completata in meno di 60 secondi al massimo. Tuttavia, il completamento di questi eventi in alcuni casi può richiedere più tempo, ad esempio quando una transazione di grandi dimensioni provoca un ripristino a esecuzione prolungata.

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Passaggi per risolvere problemi di connettività temporanei

1. Controllare nel [Dashboard dei servizi di Microsoft Azure](https://azure.microsoft.com/status) le eventuali interruzioni note che si sono verificate durante il periodo nel quale sono stati segnalati errori dall'applicazione.
2. Le applicazioni che si connettono a un servizio cloud, come il database SQL di Azure, devono prevedere il verificarsi periodico di eventi di riconfigurazione e implementare la logica di ripetizione per gestire gli errori, invece di lasciare che vengano visualizzati dagli utenti come errori dell'applicazione. Per altre informazioni e per le strategie generali di ripetizione dei tentativi, vedere la sezione [Errori temporanei](sql-database-connectivity-issues.md) e le procedure consigliate e linee guida per la progettazione in [Panoramica dello sviluppo di database SQL](sql-database-develop-overview.md). Per informazioni dettagliate, vedere gli esempi di codice in: [Raccolte di connessioni per database SQL e Server SQL](sql-database-libraries.md) .
3. Quando un database sta per raggiungere i limiti delle risorse, può sembrare che si stia verificando un problema di connettività temporaneo. Vedere la pagina relativa alla [risoluzione dei problemi di prestazioni](sql-database-troubleshoot-performance.md).
4. Se i problemi di connettività persistono oppure se l'applicazione rileva l'errore per più di 60 secondi o se vengono visualizzate più occorrenze dell'errore in un dato giorno, inoltrare una richiesta di supporto tecnico di Azure selezionando **Ottieni supporto** nel sito [Supporto tecnico di Azure](https://azure.microsoft.com/support/options) .

## <a name="troubleshoot-persistent-errors-non-transient-errors"></a>Risolvere i problemi causati da errori permanenti (errori non temporanei)
Se l'applicazione non riesce a connettersi in maniera costante al database SQL di Azure, il problema è uno dei seguenti:

* Configurazione del firewall. Il firewall del database SQL di Azure o lato client blocca le connessioni al database SQL di Azure.
* Riconfigurazione della rete lato client: ad esempio un nuovo indirizzo IP o un nuovo server proxy.
* Errore dell'utente: ad esempio, digitazione errata dei parametri di connessione, come il nome del server nella stringa di connessione.

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Passaggi per risolvere problemi di connettività permanenti
1. Impostare le [regole del firewall](sql-database-configure-firewall-settings.md) per consentire l'indirizzo IP del client. Ai fini dei test temporanei, impostare una regola del firewall usando 0.0.0.0 come intervallo di indirizzi IP iniziale e 255.255.255.255 come intervallo di indirizzi IP finale. Il server verrà così aperto a tutti gli indirizzi IP. Se questo risolve il problema di connettività, rimuovere la regola e creare una regola del firewall per un indirizzo o un intervallo di indirizzi IP adeguatamente limitato. 
2. Assicurarsi che la porta 1433 sia aperta per le connessioni in uscita in tutti i firewall tra il client e Internet. Vedere [Configure the Windows Firewall to Allow SQL Server Access](https://msdn.microsoft.com/library/cc646023.aspx) (Configurare Windows Firewall per consentire l'accesso a SQL Server) e [Porte e protocolli necessari per la soluzione ibrida di gestione delle identità](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-ports) per altre informazioni sulle porte aggiuntive da aprire per l'autenticazione di Azure Active Directory.
3. Verificare la stringa di connessione e le altre impostazioni di connessione. Vedere la sezione sulla stringa di connessione nell' [argomento relativo ai problemi di connettività](sql-database-connectivity-issues.md#connections-to-azure-sql-database).
4. Controllare lo stato del servizio nel dashboard. Se si ritiene che si sia verificata un'interruzione regionale, vedere [Ripristinare un database SQL di Azure in seguito a un'interruzione del servizio](sql-database-disaster-recovery.md) per i passaggi di ripristino in una nuova area.

## <a name="all-topics-for-azure-sql-database-connection-problems"></a>Tutti gli argomenti relativi ai problemi di connessione del database SQL di Azure
La tabella seguente elenca tutti gli argomenti relativi ai problemi di connessione che riguardano direttamente il servizio database SQL di Azure.

| &nbsp; | Titolo | Descrizione |
| ---:|:--- |:--- |
| 1 |[Risoluzione dei problemi di connessione al database SQL di Azure](sql-database-troubleshoot-common-connection-issues.md) |Questa è la pagina di destinazione per la risoluzione dei problemi di connettività del database SQL di Azure. Descrive come identificare e risolvere gli errori temporanei e gli errori permanenti o non temporaneo del database SQL di Azure. |
| 2 |[Risolvere, diagnosticare ed evitare gli errori di connessione SQL e gli errori temporanei per il database SQL](sql-database-connectivity-issues.md) |Informazioni su come risolvere, diagnosticare ed evitare un errore di connessione SQL o errore temporaneo nel database SQL di Azure. |
| 3 |[Linee guida generali per la gestione degli errori temporanei](../best-practices-retry-general.md) |Fornisce indicazioni generali per la gestione degli errori temporanei durante la connessione al database SQL di Azure. |
| 4 |[Risolvere i problemi di connettività del database SQL di Microsoft Azure](https://support.microsoft.com/help/10085/troubleshooting-connectivity-issues-with-microsoft-azure-sql-database) |Questo strumento aiuta a identificare e risolvere gli errori di connettività. |
| 5 |[Risolvere l'errore "Il database &lt;x&gt; nel server &lt;y&gt; non è attualmente disponibile. Eseguire nuovamente la connessione in un secondo momento"](sql-database-troubleshoot-connection.md) |Descrive come identificare e risolvere un errore 40613: "Il database &lt;x&gt; sul server &lt;y&gt; non è attualmente disponibile. Eseguire nuovamente la connessione in un secondo momento. |
| 6 |[Codici di errore SQL per le applicazioni client del database SQL: errore di connessione e altri problemi del database](sql-database-develop-error-messages.md) |Offre informazioni sui codici di errore SQL per le applicazioni client del database SQL, ad esempio errori di connessione comuni del database, problemi di copia del database ed errori generali. |
| 7 |[Indicazioni sulle prestazioni del database SQL di Azure per i singoli database](sql-database-performance-guidance.md) |Fornisce indicazioni per determinare quale livello di servizio è più adatto per l'applicazione. Fornisce inoltre suggerimenti per ottimizzare l'applicazione in modo da sfruttare al meglio il database SQL di Azure. |
| 8 |[Panoramica dello sviluppo di database SQL](sql-database-develop-overview.md) |Offre collegamenti agli esempi di codice per le varie tecnologie che è possibile usare per connettersi e interagire con il database SQL di Azure. |

## <a name="next-steps"></a>Passaggi successivi
* [Risoluzione dei problemi di prestazioni del database SQL di Azure](sql-database-troubleshoot-performance.md)
* [Ricerca della documentazione su Microsoft Azure](http://azure.microsoft.com/search/documentation/)
* [Informazioni sugli aggiornamenti più recenti al servizio database SQL di Azure](http://azure.microsoft.com/updates/?service=sql-database)

## <a name="additional-resources"></a>Risorse aggiuntive
* [Panoramica dello sviluppo di database SQL](sql-database-develop-overview.md)
* [Linee guida generali per la gestione degli errori temporanei](../best-practices-retry-general.md)
* [Raccolte di connessioni per database SQL e Server SQL](sql-database-libraries.md)


