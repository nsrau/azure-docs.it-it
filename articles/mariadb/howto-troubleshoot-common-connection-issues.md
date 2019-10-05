---
title: Risolvere i problemi di connessione a database di Azure per MariaDB
description: Informazioni su come risolvere i problemi di connessione al database di Azure per MariaDB, inclusi errori temporanei che richiedono tentativi, problemi del firewall e interruzioni.
author: jan-eng
ms.author: janeng
ms.service: mariadb
ms.topic: troubleshooting
ms.date: 11/09/2018
ms.openlocfilehash: a8354cdc364f221a086f4d2f4ec7da95c55baf6e
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973525"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-mariadb"></a>Risolvere i problemi di connessione a database di Azure per MariaDB

I problemi di connessione possono avere varie cause tra cui:

* Impostazioni del firewall
* Timeout della connessione
* Informazioni di accesso non corrette
* Raggiungimento del limite massimo in alcune risorse di Database di Azure per MariaDB
* Problemi con l'infrastruttura del servizio
* Manutenzione eseguita nel servizio
* Modifica dell'allocazione del calcolo del server tramite l'aumento del numero di vCore o lo spostamento a un livello di servizio diverso

I problemi di connessione a Database di Azure per MariaDB possono essere classificati a grandi linee come segue:

* Errori temporanei (di breve durata o intermittenti)
* Errori non temporanei o permanenti (errori che si ripetono regolarmente)

## <a name="troubleshoot-transient-errors"></a>Risolvere i problemi causati da errori temporanei

Gli errori temporanei si verificano quando si esegue la manutenzione, quando il sistema rileva un errore di hardware o di software o quando si modifica il numero di vCore o il livello di servizio del server. Nel servizio Database di Azure per MariaDB è integrata la disponibilità elevata e il servizio è progettato per attenuare questi tipi di problemi automaticamente. L'applicazione perde tuttavia la connessione al server per un breve periodo di tempo, in genere inferiore a 60 secondi al massimo. Alcuni eventi necessitano talvolta di più tempo per l'attenuazione, ad esempio quando una transazione di grandi dimensioni provoca un ripristino a esecuzione prolungata.

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Passaggi per risolvere problemi di connettività temporanei

1. Controllare nel [Dashboard dei servizi di Microsoft Azure](https://azure.microsoft.com/status) le eventuali interruzioni note che si sono verificate durante il periodo in cui sono stati segnalati errori dall'applicazione.
2. Le applicazioni che si connettono a un servizio cloud, ad esempio Database di Azure per MariaDB, devono prevedere il verificarsi di errori temporanei e implementare la logica di ripetizione dei tentativi per gestire gli errori, invece di lasciare che vengano visualizzati dagli utenti come errori dell'applicazione. Per le procedure consigliate e le linee guida di progettazione per la gestione degli errori temporanei, vedere [Handling of transient connectivity errors for Azure Database for MariaDB](concepts-connectivity.md) (Gestione di errori di connettività temporanei per Database di Azure per MariaDB).
3. Quando un server sta per raggiungere i limiti delle risorse, gli errori possono essere interpretati come problemi di connettività temporanei. Vedere [Limiti di Database di Azure per MariaDB](concepts-limits.md).
4. Se i problemi di connettività persistono oppure se l'applicazione rileva l'errore per più di 60 secondi o se vengono visualizzate più occorrenze dell'errore in un dato giorno, inoltrare una richiesta di supporto tecnico di Azure selezionando **Ottieni supporto** nel sito [Supporto tecnico di Azure](https://azure.microsoft.com/support/options) .

## <a name="troubleshoot-persistent-errors"></a>Risolvere gli errori persistenti

Se l'applicazione continua a non riuscire a connettersi a Database di Azure per MariaDB, il problema è in genere uno dei seguenti:

* Configurazione del firewall: il firewall del server di Database di Azure per MariaDB o del lato client blocca le connessioni.
* Riconfigurazione di rete sul lato client: è stato aggiunto un nuovo indirizzo IP o un server proxy.
* Errore utente: Ad esempio, è possibile che i parametri di connessione digitati in modo non consentiti, ad esempio il nome del server nella stringa di connessione o un suffisso *\@servername* mancante nel nome utente.

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Passaggi per risolvere problemi di connettività permanenti

1. Impostare le [regole del firewall](howto-manage-firewall-portal.md) per consentire l'indirizzo IP del client. Ai soli fini di test temporanei, impostare una regola del firewall usando 0.0.0.0 come indirizzo IP iniziale e 255.255.255.255 come indirizzo IP finale. Il server verrà così aperto a tutti gli indirizzi IP. Se questo risolve il problema di connettività, rimuovere la regola e creare una regola del firewall per un indirizzo o un intervallo di indirizzi IP adeguatamente limitato.
2. Verificare che la porta 3306 sia aperta per le connessioni in uscita in tutti i firewall tra il client e Internet.
3. Verificare la stringa di connessione e le altre impostazioni di connessione. Vedere [Come connettere le applicazioni a Database di Azure per MariaDB](howto-connection-string.md).
4. Controllare l'integrità del servizio nel dashboard. Se si ritiene che vi sia un'interruzione a livello di area vedere [Panoramica della continuità aziendale con Database di Azure per MariaDB](concepts-business-continuity.md) per la procedura di ripristino in una nuova area.

## <a name="next-steps"></a>Passaggi successivi

* [Gestione degli errori di connettività temporanei per Database di Azure per MariaDB](concepts-connectivity.md)
