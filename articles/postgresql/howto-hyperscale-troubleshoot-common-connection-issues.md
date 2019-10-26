---
title: Risolvere i problemi di connessione al database di Azure per PostgreSQL-iperscalabilità (CITUS)
description: Informazioni su come risolvere i problemi di connessione a database di Azure per PostgreSQL-iperscalabilità (CITUS)
keywords: connessione postgresql, stringa di connessione, problemi di connettività, errore temporaneo, errore di connessione
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/8/2019
ms.openlocfilehash: b812b730cebba4dbf0735f49f544e53bf7f8787c
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72952147"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-postgresql---hyperscale-citus"></a>Risolvere i problemi di connessione al database di Azure per PostgreSQL-iperscalabilità (CITUS)

I problemi di connessione possono essere causati da diversi fattori, ad esempio:

* Impostazioni del firewall
* Timeout della connessione
* Informazioni di accesso non corrette
* Limite di connessione raggiunto per il gruppo di server
* Problemi con l'infrastruttura del servizio
* Manutenzione del servizio
* Failover del nodo coordinatore sul nuovo hardware

In genere, i problemi di connessione a iperscalabilità possono essere classificati come segue:

* Errori temporanei (di breve durata o intermittenti)
* Errori non temporanei o permanenti (errori che si ripetono regolarmente)

## <a name="troubleshoot-transient-errors"></a>Risolvere i problemi causati da errori temporanei

Gli errori temporanei si verificano per diversi motivi. I più comuni includono la manutenzione del sistema, l'errore con hardware o software e gli aggiornamenti del nodo coordinatore vCore.

L'abilitazione della disponibilità elevata per i nodi del gruppo di server con iperscalabilità può attenuare automaticamente questi tipi di problemi. Tuttavia, l'applicazione deve comunque essere preparata a perdere la connessione brevemente. Inoltre, altri eventi possono richiedere più tempo per attenuare, ad esempio quando una transazione di grandi dimensioni causa un ripristino con esecuzione prolungata.

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Passaggi per risolvere problemi di connettività temporanei

1. Controllare il [Dashboard del servizio Microsoft Azure](https://azure.microsoft.com/status) per eventuali interruzioni note che si sono verificate durante il periodo in cui l'applicazione ha segnalato errori.
2. Le applicazioni che si connettono a un servizio cloud, ad esempio iperscale (CITUS), dovrebbero prevedere errori temporanei e rispondere normalmente. Ad esempio, le applicazioni devono implementare la logica di ripetizione dei tentativi per gestire questi errori anziché esporli agli utenti come errori dell'applicazione.
3. Poiché il gruppo di server si avvicina ai limiti delle risorse, gli errori possono sembrare problemi di connettività temporanei. L'aumento della RAM del nodo o l'aggiunta di nodi di lavoro e il ribilanciamento dei dati possono risultare utili.
4. Se i problemi di connettività continuano o durano più di 60 secondi oppure si verificano più di una volta al giorno, è possibile archiviare una richiesta di supporto di Azure selezionando **Ottieni supporto** nel sito del [supporto tecnico di Azure](https://azure.microsoft.com/support/options) .

## <a name="troubleshoot-persistent-errors"></a>Risolvere gli errori persistenti

Se l'applicazione non riesce a connettersi in modo permanente a iperscalabilità (CITUS), le cause più comuni sono la configurazione del firewall o l'errore dell'utente.

* Configurazione del firewall del nodo coordinatore: assicurarsi che il firewall del server iperscalabile sia configurato per consentire le connessioni dal client, inclusi i server proxy e i gateway.
* Configurazione del firewall client: il firewall del client deve consentire le connessioni al server di database. Per alcuni firewall è necessario consentire non solo l'applicazione per nome, ma consentendo gli indirizzi IP e le porte del server.
* Errore dell'utente: controllare la stringa di connessione. È possibile che siano presenti parametri non tipizzati, come il nome del server, o un suffisso di *\@nomeserver* nel nome utente.

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Passaggi per risolvere problemi di connettività permanenti

1. Impostare le [regole del firewall](howto-hyperscale-manage-firewall-using-portal.md) per consentire l'indirizzo IP del client. Ai soli fini di test temporanei, impostare una regola del firewall usando 0.0.0.0 come indirizzo IP iniziale e 255.255.255.255 come indirizzo IP finale. Tale regola apre il server a tutti gli indirizzi IP. Se la regola risolve il problema di connettività, rimuoverlo e creare una regola del firewall per un indirizzo IP o un intervallo di indirizzi appropriato.
2. In tutti i firewall tra il client e Internet assicurarsi che la porta 5432 sia aperta per le connessioni in uscita.
3. Verificare la stringa di connessione e le altre impostazioni di connessione.
4. Controllare l'integrità del servizio nel dashboard.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sui concetti delle [regole del firewall in database di Azure per PostgreSQL-iperscalabilità (CITUS)](concepts-hyperscale-firewall-rules.md)
* Vedere come [gestire le regole del firewall per database di Azure per PostgreSQL-iperscalabilità (CITUS)](howto-hyperscale-manage-firewall-using-portal.md)
