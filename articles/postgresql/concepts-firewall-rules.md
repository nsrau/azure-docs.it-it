---
title: Regole del firewall-database di Azure per PostgreSQL-server singolo
description: Questo articolo descrive come usare le regole del firewall per connettersi al database di Azure per PostgreSQL-server singolo.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/25/2019
ms.openlocfilehash: 28c8bccaf6be49b7220a32c781b79f106ad86e52
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74768640"
---
# <a name="firewall-rules-in-azure-database-for-postgresql---single-server"></a>Regole del firewall in database di Azure per PostgreSQL-server singolo
Il firewall del server database di Azure per PostgreSQL impedisce l'accesso al server di database finché non vengono specificati i computer autorizzati. Il firewall concede l'accesso al server in base all'indirizzo IP di origine di ogni richiesta.
Per configurare il firewall, creare regole del firewall che specificano gli intervalli di indirizzi IP accettabili. È possibile creare regole di firewall a livello di server.

**Regole firewall:** queste regole consentono ai client di accedere all'intero Database di Azure per il server PostgreSQL, ovvero a tutti i database all'interno dello stesso server logico. Le regole di firewall a livello di server possono essere configurate usando il portale di Azure o comandi dell'interfaccia della riga di comando. Per creare regole firewall a livello di server è necessario essere il proprietario o un collaboratore della sottoscrizione.

## <a name="firewall-overview"></a>Panoramica del firewall
Per impostazione predefinita, l'accesso al Database di Azure per il server PostgreSQL è bloccato dal firewall. Per iniziare a usare il server da un altro computer è necessario specificare una o più regole di firewall a livello di server che consentano di accedere al server. Usare le regole firewall per specificare gli intervalli di indirizzi IP da Internet a cui concedere l'accesso. L'accesso al sito Web del portale di Azure non è influenzato dalle regole di firewall.
I tentativi di connessione da Internet e Azure devono superare il firewall prima di poter raggiungere il database PostgreSQL, come illustrato nel diagramma seguente:

![Esempio del flusso di funzionamento del firewall](media/concepts-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet"></a>Connessione da Internet
Le regole firewall a livello di server si applicano a tutti i database dello stesso Database di Azure per il server PostgreSQL. Se l'indirizzo IP della richiesta rientra in uno degli intervalli specificati nelle regole firewall a livello di server, viene consentita la connessione.
Se l'indirizzo IP della richiesta non rientra negli intervalli specificati in una delle regole del firewall a livello di server, la richiesta di connessione non riesce.
Ad esempio, se l'applicazione si connette con il driver JDBC per PostgreSQL, potrebbe verificarsi questo errore durante il tentativo di connessione quando il firewall blocca la connessione.
> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: FATAL: nessuna voce pg\_hba.conf per l'host "123.45.67.890", utente "adminuser", database "postgresql", SSL

## <a name="connecting-from-azure"></a>Connessione da Azure
Per consentire alle applicazioni da Azure di stabilire la connessione al server di Database di Azure per PostgreSQL, è necessario abilitare le connessioni da Azure. Ad esempio, per ospitare un'applicazione delle app Web di Azure o un'applicazione che viene eseguita in una macchina virtuale di Azure oppure per connettersi da un gateway di gestione dati di Azure Data Factory. Le risorse non devono essere necessariamente nella stessa rete virtuale (VNet) o nello stesso gruppo di risorse perché la regola del firewall consenta tali connessioni. Quando si tenta di connettersi al server di database da un'applicazione di Azure, il firewall verifica che le connessioni da Azure siano consentite. Sono disponibili alcuni metodi per consentire questi tipi di connessioni. Un'impostazione del firewall con indirizzo iniziale e finale uguale a 0.0.0.0 indica che queste connessioni sono consentite. In alternativa, è possibile impostare l'opzione **Consenti l'accesso a Servizi di Azure** su **Sì** nel portale dal riquadro **Sicurezza connessione** e fare clic su **Salva**. Se il tentativo di connessione non è consentito, la richiesta non raggiungerà il server di Database di Azure per PostgreSQL.

> [!IMPORTANT]
> Questa opzione permette di configurare il firewall in maniera tale da consentire tutte le connessioni da Azure, incluse le connessioni dalle sottoscrizioni di altri clienti. Quando si seleziona questa opzione, assicurarsi che l'account di accesso e le autorizzazioni utente limitino l'accesso ai soli utenti autorizzati.
> 

![Configurare Possibilità di accedere ai servizi di Azure nel portale](media/concepts-firewall-rules/allow-azure-services.png)

### <a name="connecting-from-a-vnet"></a>Connessione da un VNet
Per connettersi in modo sicuro al database di Azure per il server PostgreSQL da una VNet, è consigliabile usare gli [endpoint del servizio VNet](./concepts-data-access-and-security-vnet.md). 

## <a name="programmatically-managing-firewall-rules"></a>Gestione di regole del firewall a livello di programmazione
Oltre al portale di Azure, le regole di firewall possono essere gestite a livello di programmazione usando l'interfaccia della riga di comando di Azure.
Vedere anche [Create and manage Azure Database for PostgreSQL firewall rules using Azure CLI](howto-manage-firewall-using-cli.md) (Creare e gestire regole firewall per il Database di Azure per PostgreSQL tramite l'interfaccia della riga di comando di Azure)

## <a name="troubleshooting-firewall-issues"></a>Risoluzione dei problemi relativi al firewall
Quando si effettua l'accesso al servizio Database di Microsoft Azure per il server PostgreSQL non si comporta come previsto, considerare quanto segue:

* **Le modifiche all'elenco Consentiti non sono state ancora applicate:** per rendere effettive le modifiche apportate al firewall del Database di Azure per il server PostgreSQL possono essere necessari fino a cinque minuti.

* **L'accesso non è autorizzato oppure è stata usata una password errata:** quando un account di accesso non dispone delle autorizzazioni per il Database di Azure per il server PostgreSQL o la password usata non è corretta, la connessione al Database di Azure per il server PostgreSQL verrà negata. La creazione di un'impostazione del firewall fornisce ai client soltanto un'opportunità di connessione al server; ogni client deve ancora fornire le credenziali di sicurezza necessarie.

   Ad esempio, se si usa un client JDBC potrebbe essere visualizzato l'errore seguente.
   > java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: FATAL: autenticazione della password non riuscita per l'utente "yourusername"

* **Indirizzo IP dinamico:** se si dispone di una connessione Internet con indirizzi IP dinamici e si verificano problemi di comunicazione attraverso il firewall, è possibile provare una delle seguenti soluzioni:

   * Richiedere al provider di servizi Internet (ISP) l'intervallo di indirizzi IP assegnato ai computer client che accedono al Database di Azure per il server MySQL, quindi aggiungere l'intervallo di indirizzi IP come regola firewall.

   * Ottenere indirizzi IP statici per i computer client, quindi aggiungere gli indirizzi IP statici come una regola del firewall.

* **L'IP del server sembra essere pubblico:** Le connessioni al database di Azure per il server PostgreSQL vengono instradate tramite un gateway di Azure accessibile pubblicamente. Tuttavia, l'indirizzo IP effettivo del server è protetto dal firewall. Per altre informazioni, vedere l' [articolo sull'architettura della connettività](concepts-connectivity-architecture.md). 

## <a name="next-steps"></a>Passaggi successivi
Per informazioni sulla creazione di regole del firewall a livello di server e di database, vedere:
* [Create and manage Azure Database for PostgreSQL firewall rules using the Azure portal](howto-manage-firewall-using-portal.md) (Creare e gestire regole firewall per il Database di Azure per PostgreSQL usando il portale di Azure)
* [Create and manage Azure Database for PostgreSQL firewall rules using Azure CLI](howto-manage-firewall-using-cli.md) (Creare e gestire regole firewall del Database di Azure per PostgreSQL tramite l'interfaccia della riga di comando di Azure)
- [Endpoint del servizio VNet nel database di Azure per PostgreSQL](./concepts-data-access-and-security-vnet.md)
