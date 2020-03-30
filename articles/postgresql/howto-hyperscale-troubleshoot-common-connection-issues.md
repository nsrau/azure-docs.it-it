---
title: Risolvere i problemi relativi alle connessioni - Hyperscale (Citus) - Database di Azure per PostgreSQLTroubleshoot connections - Hyperscale (Citus) - Azure Database for PostgreSQL
description: Informazioni su come risolvere i problemi di connessione al database di Azure per PostgreSQL - Hyperscale (Citus)
keywords: connessione postgresql, stringa di connessione, problemi di connettività, errore temporaneo, errore di connessione
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/8/2019
ms.openlocfilehash: c064aca484f85c44dada9888012140784a96863f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74977506"
---
# <a name="troubleshoot-connection-issues-to-azure-database-for-postgresql---hyperscale-citus"></a>Risolvere i problemi di connessione al database di Azure per PostgreSQL - Hyperscale (Citus)Troubleshoot connection issues to Azure Database for PostgreSQL - Hyperscale (Citus)

I problemi di connessione possono essere causati da diversi fattori, ad esempio:

* Impostazioni del firewall
* Timeout della connessione
* Informazioni di accesso errate
* Raggiunto limite di connessione per il gruppo di server
* Problemi con l'infrastruttura del servizio
* Manutenzione del servizio
* Il nodo coordinatore che esegue il failover su un nuovo hardware

In genere, i problemi di connessione a Hyperscale possono essere classificati come segue:

* Errori temporanei (di breve durata o intermittenti)
* Errori non temporanei o permanenti (errori che si ripetono regolarmente)

## <a name="troubleshoot-transient-errors"></a>Risolvere i problemi causati da errori temporanei

Errori temporanei si verificano per una serie di motivi. I più comuni includono la manutenzione del sistema, l'errore con hardware o software e gli aggiornamenti vCore del nodo coordinatore.

L'abilitazione della disponibilità elevata per i nodi del gruppo di server Hyperscale può ridurre automaticamente questi tipi di problemi. Tuttavia, l'applicazione deve comunque essere pronta a perdere brevemente la connessione. Anche altri eventi possono richiedere più tempo per mitigare, ad esempio quando una transazione di grandi dimensioni provoca un ripristino a esecuzione prolungata.

### <a name="steps-to-resolve-transient-connectivity-issues"></a>Passaggi per risolvere problemi di connettività temporanei

1. Controllare il dashboard del [servizio Microsoft Azure](https://azure.microsoft.com/status) per eventuali interruzioni note che si sono verificate durante il periodo in cui l'applicazione segnalava errori.
2. Le applicazioni che si connettono a un servizio cloud, ad esempio Hyperscale (Citus), devono aspettarsi errori temporanei e reagire normalmente. Ad esempio, le applicazioni devono implementare la logica di ripetizione dei tentativi per gestire questi errori anziché visualizzarli come errori dell'applicazione per gli utenti.
3. Quando il gruppo di server si avvicina ai limiti delle risorse, gli errori possono sembrare problemi di connettività temporanei. L'aumento della RAM dei nodi o l'aggiunta di nodi di lavoro e il ribilanciamento dei dati può essere utile.
4. Se i problemi di connettività persistono o durano più di 60 secondi o si verificano più di una volta al giorno, presentare una richiesta di supporto di Azure selezionando Ottieni supporto nel sito di supporto di Azure.If connectivity problems continue, or last more than [60](https://azure.microsoft.com/support/options) seconds, or happen more than once per day, file an Azure support request by selecting **Get Support** on the Azure Support site.

## <a name="troubleshoot-persistent-errors"></a>Risolvere gli errori persistenti

Se l'applicazione non riesce in modo permanente a connettersi a Hyperscale (Citus), le cause più comuni sono la configurazione errata del firewall o l'errore dell'utente.

* Configurazione del firewall del nodo coordinatore: assicurarsi che il firewall del server Hyperscale sia configurato per consentire le connessioni dal client, inclusi i server proxy e i gateway.
* Configurazione del firewall client: il firewall del client deve consentire le connessioni al server di database. Alcuni firewall richiedono non solo l'applicazione in base al nome, ma anche gli indirizzi IP e le porte del server.
* Errore utente: controllare la stringa di connessione. È possibile che i parametri non digitati in modo errato, ad esempio il nome del server. È possibile trovare le stringhe di connessione per vari framework di linguaggio e psql nel portale di Azure.You can find connection strings for various language frameworks and psql in the Azure portal. Passare alla pagina Stringhe di **connessione** nel gruppo di server Hyperscale (Citus). Tenere inoltre presente che i cluster Hyperscale (Citus) dispongono di un solo database e il nome predefinito è **citus**.

### <a name="steps-to-resolve-persistent-connectivity-issues"></a>Passaggi per risolvere problemi di connettività permanenti

1. Impostare [le regole del firewall](howto-hyperscale-manage-firewall-using-portal.md) per consentire l'indirizzo IP del client. Ai soli fini di test temporanei, impostare una regola del firewall usando 0.0.0.0 come indirizzo IP iniziale e 255.255.255.255 come indirizzo IP finale. Tale regola apre il server a tutti gli indirizzi IP. Se la regola risolve il problema di connettività, rimuoverlo e creare una regola del firewall per un indirizzo IP o un intervallo di indirizzi opportunamente limitato.
2. In tutti i firewall tra il client e Internet, assicurarsi che la porta 5432 sia aperta per le connessioni in uscita.
3. Verificare la stringa di connessione e le altre impostazioni di connessione.
4. Controllare l'integrità del servizio nel dashboard.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sui concetti delle regole del firewall nel database di [Azure per PostgreSQL - Hyperscale (Citus)Learn the concepts of Firewall rules in Azure Database for PostgreSQL - Hyperscale (Citus)](concepts-hyperscale-firewall-rules.md)
* Informazioni su come gestire le regole del firewall per il database di [Azure per PostgreSQL - Hyperscale (Citus)See to Manage firewall rules for Azure Database for PostgreSQL - Hyperscale (Citus)](howto-hyperscale-manage-firewall-using-portal.md)
