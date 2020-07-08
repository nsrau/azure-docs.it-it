---
title: Risolvere i problemi relativi alle connessioni-database di Azure per PostgreSQL-server singolo
description: Informazioni su come risolvere i problemi di connessione al database di Azure per PostgreSQL-server singolo.
keywords: connessione postgresql, stringa di connessione, problemi di connettività, errore temporaneo, errore di connessione
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: c0011889e59470d94e650a19c6713074155c63fa
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85106559"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-postgresql---single-server"></a>Risolvere i problemi di connessione al database di Azure per PostgreSQL-server singolo

I problemi di connessione possono avere varie cause tra cui:

* Impostazioni del firewall
* Timeout della connessione
* Informazioni di accesso non corrette
* Raggiungimento del limite massimo in alcune risorse di Database di Azure per PostgreSQL
* Problemi con l'infrastruttura del servizio
* Manutenzione eseguita nel servizio
* Modifica dell'allocazione del calcolo del server tramite l'aumento del numero di vCore o lo spostamento a un livello di servizio diverso

I problemi di connessione a Database di Azure per PostgreSQL possono essere classificati a grandi linee come segue:

* Errori temporanei (di breve durata o intermittenti)
* Errori non temporanei o permanenti (errori che si ripetono regolarmente)

## <a name="troubleshoot-transient-errors"></a>Risolvere i problemi causati da errori temporanei

Gli errori temporanei si verificano quando si esegue la manutenzione, quando il sistema rileva un errore di hardware o di software o quando si modifica il numero di vCore o il livello di servizio del server. Nel servizio Database di Azure per PostgreSQL è integrata la disponibilità elevata e il servizio è progettato per attenuare questi tipi di problemi automaticamente. L'applicazione perde tuttavia la connessione al server per un breve periodo di tempo, in genere inferiore a 60 secondi al massimo. Alcuni eventi necessitano talvolta di più tempo per l'attenuazione, ad esempio quando una transazione di grandi dimensioni provoca un ripristino a esecuzione prolungata.

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Passaggi per risolvere problemi di connettività temporanei

1. Controllare nel [Dashboard dei servizi di Microsoft Azure](https://azure.microsoft.com/status) le eventuali interruzioni note che si sono verificate durante il periodo in cui sono stati segnalati errori dall'applicazione.
2. Le applicazioni che si connettono a un servizio cloud, ad esempio Database di Azure per PostgreSQL, devono prevedere il verificarsi di errori temporanei e implementare la logica di ripetizione per gestirli, invece di lasciare che vengano visualizzati agli utenti come errori dell'applicazione. Per le procedure consigliate e le linee guida di progettazione per la gestione degli errori temporanei, vedere [Handling of transient connectivity errors for Azure Database for PostgreSQL](concepts-connectivity.md) (Gestione di errori di connettività temporanei per Database di Azure per PostgreSQL).
3. Quando un server sta per raggiungere i limiti delle risorse, gli errori possono essere interpretati come problemi di connettività temporanei. Vedere [Limiti del Database di Azure per PostgreSQL](concepts-limits.md).
4. Se i problemi di connettività persistono oppure se l'applicazione rileva l'errore per più di 60 secondi o se vengono visualizzate più occorrenze dell'errore in un dato giorno, inoltrare una richiesta di supporto tecnico di Azure selezionando **Ottieni supporto** nel sito [Supporto tecnico di Azure](https://azure.microsoft.com/support/options) .

## <a name="troubleshoot-persistent-errors"></a>Risolvere gli errori persistenti

Se l'applicazione continua a non riuscire a connettersi a Database di Azure per PostgreSQL, il problema è in genere uno dei seguenti:

* Configurazione del firewall del server: assicurarsi che il firewall del server di database di Azure per PostgreSQL sia configurato per consentire le connessioni dal client, inclusi i server proxy e i gateway.
* Configurazione del firewall client: il firewall del client deve consentire le connessioni al server di database. È necessario consentire gli indirizzi IP e le porte del server, nonché i nomi di applicazioni, ad esempio PostgreSQL, in alcuni firewall.
* Errore dell'utente: è possibile che i parametri di connessione, ad esempio il nome del server nella stringa di connessione o un suffisso * \@ ServerName* mancante nel nome utente, non siano stati digitati.
* Se il _server di errore non è configurato per consentire le connessioni IPv6_, si noti che il livello Basic non supporta gli endpoint del servizio VNet. È necessario rimuovere l'endpoint Microsoft. SQL dalla subnet che tenta di connettersi al server di base.
* Se viene visualizzato l'errore _di connessione sslmode valore "* * *" non valido quando il supporto SSL non è compilato in_ errore, significa che il client PostgreSQL non supporta SSL. Probabilmente, il libpq sul lato client non è stato compilato con il flag "--with-OpenSSL". Provare a connettersi con un client PostgreSQL con supporto SSL. 

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Passaggi per risolvere problemi di connettività permanenti

1. Configurare le [regole del firewall](howto-manage-firewall-using-portal.md) per consentire l'indirizzo IP del client. Ai soli fini di test temporanei, impostare una regola del firewall usando 0.0.0.0 come indirizzo IP iniziale e 255.255.255.255 come indirizzo IP finale. Il server verrà così aperto a tutti gli indirizzi IP. Se questo risolve il problema di connettività, rimuovere la regola e creare una regola del firewall per un indirizzo o un intervallo di indirizzi IP adeguatamente limitato.
2. In tutti i firewall tra il client e Internet assicurarsi che la porta 5432 sia aperta per le connessioni in uscita.
3. Verificare la stringa di connessione e le altre impostazioni di connessione.
4. Controllare l'integrità del servizio nel dashboard. Se si ritiene che vi sia un'interruzione a livello di area, vedere [Panoramica della continuità aziendale con Database di Azure per PostgreSQL](concepts-business-continuity.md) per la procedura di ripristino in una nuova area.

## <a name="next-steps"></a>Passaggi successivi

* [Handling of transient connectivity errors for Azure Database for PostgreSQL](concepts-connectivity.md) (Gestione degli errori di connettività temporanei per Database di Azure per PostgreSQL)
