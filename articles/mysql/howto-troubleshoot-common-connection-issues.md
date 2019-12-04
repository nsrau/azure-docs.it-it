---
title: Risolvere i problemi di connessione-database di Azure per MySQL
description: Informazioni su come risolvere i problemi di connessione a database di Azure per MySQL, inclusi errori temporanei che richiedono tentativi, problemi del firewall e interruzioni.
keywords: connessione mysql,stringa di connessione,problemi di connettività,errore temporaneo,errore di connessione
author: jan-eng
ms.author: janeng
ms.service: mysql
ms.topic: troubleshooting
ms.date: 12/02/2019
ms.openlocfilehash: 8c1c521488270ff0bfe3b306470345bc2cd885ce
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74773531"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-mysql"></a>Risolvere i problemi di connessione a Database di Azure per MySQL

I problemi di connessione possono avere varie cause tra cui:

* Impostazioni del firewall
* Timeout della connessione
* Informazioni di accesso non corrette
* Raggiungimento del limite massimo in alcune risorse di Database di Azure per MySQL
* Problemi con l'infrastruttura del servizio
* Manutenzione eseguita nel servizio
* Modifica dell'allocazione del calcolo del server tramite l'aumento del numero di vCore o lo spostamento a un livello di servizio diverso

I problemi di connessione a Database di Azure per MySQL possono essere classificati a grandi linee come segue:

* Errori temporanei (di breve durata o intermittenti)
* Errori non temporanei o permanenti (errori che si ripetono regolarmente)

## <a name="troubleshoot-transient-errors"></a>Risolvere i problemi causati da errori temporanei

Gli errori temporanei si verificano quando si esegue la manutenzione, quando il sistema rileva un errore di hardware o di software o quando si modifica il numero di vCore o il livello di servizio del server. Nel servizio Database di Azure per MySQL è integrata la disponibilità elevata e il servizio è progettato per attenuare questi tipi di problemi automaticamente. L'applicazione perde tuttavia la connessione al server per un breve periodo di tempo, in genere inferiore a 60 secondi al massimo. Alcuni eventi necessitano talvolta di più tempo per l'attenuazione, ad esempio quando una transazione di grandi dimensioni provoca un ripristino a esecuzione prolungata.

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Passaggi per risolvere problemi di connettività temporanei

1. Controllare nel [Dashboard dei servizi di Microsoft Azure](https://azure.microsoft.com/status) le eventuali interruzioni note che si sono verificate durante il periodo in cui sono stati segnalati errori dall'applicazione.
2. Le applicazioni che si connettono a un servizio cloud, ad esempio Database di Azure per MySQL, devono prevedere il verificarsi di errori temporanei e implementare la logica di ripetizione dei tentativi per gestire gli errori, invece di lasciare che vengano visualizzati dagli utenti come errori dell'applicazione. Per le procedure consigliate e le linee guida di progettazione per la gestione degli errori temporanei, vedere [Handling of transient connectivity errors for Azure Database for MySQL](concepts-connectivity.md) (Gestione di errori di connettività temporanei per Database di Azure per MySQL).
3. Quando un server sta per raggiungere i limiti delle risorse, gli errori possono essere interpretati come problemi di connettività temporanei. Vedere [Limiti di Database di Azure per MySQL](concepts-limits.md).
4. Se i problemi di connettività persistono oppure se l'applicazione rileva l'errore per più di 60 secondi o se vengono visualizzate più occorrenze dell'errore in un dato giorno, inoltrare una richiesta di supporto tecnico di Azure selezionando **Ottieni supporto** nel sito [Supporto tecnico di Azure](https://azure.microsoft.com/support/options) .

## <a name="troubleshoot-persistent-errors"></a>Risolvere gli errori persistenti

Se l'applicazione continua a non riuscire a connettersi a Database di Azure per MySQL, il problema è in genere uno dei seguenti:

* Configurazione del firewall del server: assicurarsi che il firewall del server di database di Azure per MySQL sia configurato per consentire le connessioni dal client, inclusi i server proxy e i gateway.
* Configurazione del firewall client: il firewall del client deve consentire le connessioni al server di database. È necessario consentire gli indirizzi IP e le porte del server, nonché i nomi di applicazioni, ad esempio MySQL, in alcuni firewall.
* Errore dell'utente: è possibile che i parametri di connessione, ad esempio il nome del server nella stringa di connessione o un suffisso di *\@ServerName* mancante nel nome utente, non siano stati digitati.

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Passaggi per risolvere problemi di connettività permanenti

1. Impostare le [regole del firewall](howto-manage-firewall-using-portal.md) per consentire l'indirizzo IP del client. Ai soli fini di test temporanei, impostare una regola del firewall usando 0.0.0.0 come indirizzo IP iniziale e 255.255.255.255 come indirizzo IP finale. Il server verrà così aperto a tutti gli indirizzi IP. Se questo risolve il problema di connettività, rimuovere la regola e creare una regola del firewall per un indirizzo o un intervallo di indirizzi IP adeguatamente limitato.
2. Verificare che la porta 3306 sia aperta per le connessioni in uscita in tutti i firewall tra il client e Internet.
3. Verificare la stringa di connessione e le altre impostazioni di connessione. Vedere [Come connettere le applicazioni a Database di Azure per MySQL](howto-connection-string.md).
4. Controllare l'integrità del servizio nel dashboard. Se si ritiene che vi sia un'interruzione a livello di area vedere [Panoramica della continuità aziendale con Database di Azure per MySQL](concepts-business-continuity.md) per la procedura di ripristino in una nuova area.

## <a name="next-steps"></a>Passaggi successivi

* [Gestione degli errori di connettività temporanei per Database di Azure per MySQL](concepts-connectivity.md)
