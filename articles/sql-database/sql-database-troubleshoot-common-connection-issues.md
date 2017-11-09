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
ms.custom: monitor & tune
ms.workload: On Demand
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/03/2017
ms.author: daleche
ms.openlocfilehash: cc9b1e1474e67628857dd80a63850634469ca5e8
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2017
---
# <a name="troubleshoot-connection-issues-to-azure-sql-database"></a>Risoluzione dei problemi di connessione al database SQL di Azure
Quando la connessione al database SQL di Azure non riesce, vengono visualizzati [messaggi di errore](sql-database-develop-error-messages.md). Questo articolo tratta un argomento centrale che aiuta l'utente a risolvere i problemi di connettività del database SQL di Azure. Presenta le [cause comuni](#cause) dei problemi di connessione, consiglia [uno strumento di risoluzione dei problemi](#try-the-troubleshooter-for-azure-sql-database-connectivity-issues) che aiuta a identificare il problema e fornisce i passaggi necessari per risolvere gli [errori temporanei](#troubleshoot-transient-errors) e gli [errori persistenti o non temporanei](#troubleshoot-persistent-errors). 

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
* [Errori non temporanei o permanenti (gli errori che si ripetono regolarmente)](#troubleshoot-persistent-errors)

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

Questo errore si verifica quando il database di Azure viene spostato o riconfigurato e di conseguenza l'applicazione perde la connessione al database SQL. La riconfigurazione del database SQL avviene in seguito a eventi pianificati, ad esempio nel caso degli aggiornamenti software, o non pianificati, ad esempio per l'arresto anomalo di un processo o il bilanciamento del carico. La maggior parte degli eventi di riconfigurazione è di breve durata e deve essere completata in meno di 60 secondi al massimo. Tuttavia, il completamento di questi eventi in alcuni casi può richiedere più tempo, ad esempio quando una transazione di grandi dimensioni provoca un ripristino a esecuzione prolungata.

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Passaggi per risolvere problemi di connettività temporanei

1. Controllare nel [Dashboard dei servizi di Microsoft Azure](https://azure.microsoft.com/status) le eventuali interruzioni note che si sono verificate durante il periodo nel quale sono stati segnalati errori dall'applicazione.
2. Le applicazioni che si connettono a un servizio cloud, come il database SQL di Azure, devono prevedere il verificarsi periodico di eventi di riconfigurazione e implementare la logica di ripetizione per gestire gli errori, invece di lasciare che vengano visualizzati dagli utenti come errori dell'applicazione. Per altre informazioni e per le strategie generali di ripetizione dei tentativi, vedere la sezione [Errori temporanei](sql-database-connectivity-issues.md) e le procedure consigliate e linee guida per la progettazione in [Panoramica dello sviluppo di database SQL](sql-database-develop-overview.md). Per informazioni dettagliate, vedere gli esempi di codice in: [Raccolte di connessioni per database SQL e Server SQL](sql-database-libraries.md) .
3. Quando un database sta per raggiungere i limiti delle risorse, può sembrare che si stia verificando un problema di connettività temporaneo. Vedere la pagina relativa alla [risoluzione dei problemi di prestazioni](sql-database-troubleshoot-performance.md).
4. Se i problemi di connettività persistono oppure se l'applicazione rileva l'errore per più di 60 secondi o se vengono visualizzate più occorrenze dell'errore in un dato giorno, inoltrare una richiesta di supporto tecnico di Azure selezionando **Ottieni supporto** nel sito [Supporto tecnico di Azure](https://azure.microsoft.com/support/options) .

## <a name="troubleshoot-persistent-errors"></a>Risolvere gli errori persistenti
Se l'applicazione non riesce a connettersi in maniera costante al database SQL di Azure, il problema è uno dei seguenti:

* Configurazione del firewall. Il firewall del database SQL di Azure o lato client blocca le connessioni al database SQL di Azure.
* Riconfigurazione della rete lato client: ad esempio un nuovo indirizzo IP o un nuovo server proxy.
* Errore dell'utente: ad esempio, digitazione errata dei parametri di connessione, come il nome del server nella stringa di connessione.

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Passaggi per risolvere problemi di connettività permanenti
1. Impostare le [regole del firewall](sql-database-configure-firewall-settings.md) per consentire l'indirizzo IP del client. Ai fini dei test temporanei, impostare una regola del firewall usando 0.0.0.0 come intervallo di indirizzi IP iniziale e 255.255.255.255 come intervallo di indirizzi IP finale. Il server verrà così aperto a tutti gli indirizzi IP. Se questo risolve il problema di connettività, rimuovere la regola e creare una regola del firewall per un indirizzo o un intervallo di indirizzi IP adeguatamente limitato. 
2. Assicurarsi che la porta 1433 sia aperta per le connessioni in uscita in tutti i firewall tra il client e Internet. Vedere [Configure the Windows Firewall to Allow SQL Server Access](https://msdn.microsoft.com/library/cc646023.aspx) (Configurare Windows Firewall per consentire l'accesso a SQL Server) e [Porte e protocolli necessari per la soluzione ibrida di gestione delle identità](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-ports) per altre informazioni sulle porte aggiuntive da aprire per l'autenticazione di Azure Active Directory.
3. Verificare la stringa di connessione e le altre impostazioni di connessione. Vedere la sezione sulla stringa di connessione nell' [argomento relativo ai problemi di connettività](sql-database-connectivity-issues.md#connections-to-azure-sql-database).
4. Controllare lo stato del servizio nel dashboard. Se si ritiene che si sia verificata un'interruzione regionale, vedere [Ripristinare un database SQL di Azure in seguito a un'interruzione del servizio](sql-database-disaster-recovery.md) per i passaggi di ripristino in una nuova area.

## <a name="next-steps"></a>Passaggi successivi
* [Risoluzione dei problemi di prestazioni del database SQL di Azure](sql-database-troubleshoot-performance.md)
* [Ricerca della documentazione su Microsoft Azure](http://azure.microsoft.com/search/documentation/)
* [Informazioni sugli aggiornamenti più recenti al servizio database SQL di Azure](http://azure.microsoft.com/updates/?service=sql-database)

## <a name="additional-resources"></a>Risorse aggiuntive
* [Panoramica dello sviluppo di database SQL](sql-database-develop-overview.md)
* [Linee guida generali per la gestione degli errori temporanei](../best-practices-retry-general.md)
* [Raccolte di connessioni per database SQL e Server SQL](sql-database-libraries.md)

