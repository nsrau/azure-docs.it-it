---
title: Regole del firewall in database di Azure per PostgreSQL-iperscalabilità (CITUS)
description: Questo articolo descrive le regole del firewall per database di Azure per PostgreSQL-iperscalabilità (CITUS).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 9/12/2019
ms.openlocfilehash: 567fb27ed942a24ab7d031d791e18fa487956fad
ms.sourcegitcommit: a6718e2b0251b50f1228b1e13a42bb65e7bf7ee2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2019
ms.locfileid: "71273732"
---
# <a name="firewall-rules-in-azure-database-for-postgresql---hyperscale-citus"></a>Regole del firewall in database di Azure per PostgreSQL-iperscalabilità (CITUS)
Il firewall del server database di Azure per PostgreSQL impedisce l'accesso al nodo coordinatore iperscalabile (CITUS) fino a quando non si specificano i computer autorizzati. Il firewall concede l'accesso al server in base all'indirizzo IP di origine di ogni richiesta.
Per configurare il firewall, creare regole del firewall che specificano gli intervalli di indirizzi IP accettabili. È possibile creare regole firewall a livello di server.

**Regole del firewall:** Queste regole consentono ai client di accedere al nodo coordinatore CITUS (iperscale), ovvero a tutti i database all'interno dello stesso server logico. Le regole del firewall a livello di server possono essere configurate usando il portale di Azure. Per creare regole firewall a livello di server è necessario essere il proprietario o un collaboratore della sottoscrizione.

## <a name="firewall-overview"></a>Panoramica del firewall
Per impostazione predefinita, tutto l'accesso al database al nodo coordinatore è bloccato dal firewall. Per iniziare a usare il server da un altro computer è necessario specificare una o più regole firewall a livello di server che consentano di accedere al server. Usare le regole firewall per specificare gli intervalli di indirizzi IP da Internet a cui concedere l'accesso. L'accesso al sito Web del portale di Azure non è determinato dalle regole firewall.
I tentativi di connessione da Internet e Azure devono superare il firewall prima di poter raggiungere il database PostgreSQL, come illustrato nel diagramma seguente:

![Esempio del flusso di funzionamento del firewall](media/concepts-hyperscale-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet-and-from-azure"></a>Connessione da Internet e da Azure

Un firewall del gruppo di server iperscalabile (CITUS) che può connettersi al nodo coordinatore del gruppo. Il firewall determina l'accesso consultando un elenco configurabile di regole. Ogni regola è un indirizzo IP o un intervallo di indirizzi consentiti in.

Quando il firewall blocca le connessioni, può causare errori dell'applicazione. Usando il driver JDBC per PostgreSQL, ad esempio, genera un errore simile al seguente:

> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: Irreversibile: nessuna\_voce scheda HBA. conf per l'host "123.45.67.890", utente "CITUS", database "CITUS", SSL

Per informazioni sulle modalità di definizione delle regole, vedere [creare e gestire regole del firewall](howto-hyperscale-manage-firewall-using-portal.md) .

## <a name="troubleshooting-the-database-server-firewall"></a>Risoluzione dei problemi del firewall del server di database
Quando l'accesso al servizio Microsoft Azure database per PostgreSQL-iperscale (CITUS) non si comporta come previsto, considerare questi punti:

* **Le modifiche all'elenco degli indirizzi consentiti non sono state ancora applicate:** Per rendere effettive le modifiche apportate alla configurazione del firewall di iperscalabilità (CITUS) è possibile che si verifichi un ritardo di cinque minuti.

* **L'utente non è autorizzato oppure è stata usata una password errata:** Se un utente non dispone delle autorizzazioni per il server o la password utilizzata non è corretta, la connessione al server viene negata. La creazione di un'impostazione del firewall fornisce ai client soltanto un'opportunità di connessione al server; ogni client deve ancora fornire le credenziali di sicurezza necessarie.

Ad esempio, se si usa un client JDBC potrebbe essere visualizzato l'errore seguente.
> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: ERRORE IRREVERSIBILE: l'autenticazione della password per l'utente "yourusername" non è riuscita

* **Indirizzo IP dinamico:** Se si dispone di una connessione Internet con indirizzi IP dinamici e si verificano problemi di comunicazione attraverso il firewall, è possibile provare una delle soluzioni seguenti:

* Chiedere al provider di servizi Internet (ISP) l'intervallo di indirizzi IP assegnato ai computer client che accedono al nodo coordinatore iperscale (CITUS) e quindi aggiungere l'intervallo di indirizzi IP come regola del firewall.

* Ottenere indirizzi IP statici per i computer client, quindi aggiungere gli indirizzi IP statici come una regola del firewall.

## <a name="next-steps"></a>Passaggi successivi
Per informazioni sulla creazione di regole del firewall a livello di server e di database, vedere:
* [Create and manage Azure Database for PostgreSQL firewall rules using the Azure portal](howto-hyperscale-manage-firewall-using-portal.md) (Creare e gestire regole firewall per il Database di Azure per PostgreSQL usando il portale di Azure)
