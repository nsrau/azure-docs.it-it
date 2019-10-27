---
title: Regole firewall per il server Database di Azure per MariaDB
description: Informazioni sull'uso delle regole del firewall per abilitare le connessioni al database di Azure per il server MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 10/25/2019
ms.openlocfilehash: 82e07edc615fd8c1ef0ebc84cf57035727bbcdf6
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2019
ms.locfileid: "72965252"
---
# <a name="azure-database-for-mariadb-server-firewall-rules"></a>Regole firewall per il server Database di Azure per MariaDB
I firewall impediscono qualsiasi accesso al server di database finché non vengono specificati i computer autorizzati. Il firewall concede l'accesso al server in base all'indirizzo IP di origine di ogni richiesta.

Per configurare un firewall, creare regole del firewall che specificano gli intervalli di indirizzi IP accettabili. È possibile creare regole di firewall a livello di server.

**Regole di firewall:** queste regole consentono ai client di accedere all'intero server Database di Azure per MariaDB, ovvero a tutti i database all'interno dello stesso server logico. Le regole del firewall a livello di server possono essere configurate usando il portale di Azure o i comandi dell'interfaccia della riga di comando di Azure. Per creare regole firewall a livello di server è necessario essere il proprietario o un collaboratore della sottoscrizione.

## <a name="firewall-overview"></a>Panoramica del firewall
L'accesso al server Database di Azure per MariaDB è bloccato dal firewall per impostazione predefinita. Per iniziare a usare il server da un altro computer è necessario specificare una o più regole di firewall a livello di server che consentano di accedere al server. Usare le regole firewall per specificare gli intervalli di indirizzi IP da Internet a cui concedere l'accesso. L'accesso al sito Web del portale di Azure non è influenzato dalle regole di firewall.

I tentativi di connessione da Internet e Azure devono prima superare il firewall per poter raggiungere il server Database di Azure per MariaDB, come illustrato nel diagramma seguente:

![Esempio del flusso di funzionamento del firewall](./media/concepts-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet"></a>Connessione da Internet
Le regole di firewall a livello di server si applicano a tutti i database del server Database di Azure per MariaDB.

Se l'indirizzo IP della richiesta rientra in uno degli intervalli specificati nelle regole del firewall a livello di server, viene consentita la connessione.

Se l'indirizzo IP della richiesta non rientra negli intervalli specificati in una delle regole del firewall a livello di database o di server, la richiesta di connessione ha esito negativo.

## <a name="connecting-from-azure"></a>Connessione da Azure
Per consentire alle applicazioni da Azure di stabilire la connessione al server Database di Azure per MariaDB, è necessario abilitare le connessioni da Azure. Ad esempio, per ospitare un'applicazione delle app Web di Azure o un'applicazione che viene eseguita in una macchina virtuale di Azure oppure per connettersi da un gateway di gestione dati di Azure Data Factory. Le risorse non devono essere necessariamente nella stessa rete virtuale (VNet) o nello stesso gruppo di risorse perché la regola del firewall consenta tali connessioni. Quando si tenta di connettersi al server di database da un'applicazione di Azure, il firewall verifica che le connessioni da Azure siano consentite. Sono disponibili alcuni metodi per consentire questi tipi di connessioni. Un'impostazione del firewall con indirizzo iniziale e finale uguale a 0.0.0.0 indica che queste connessioni sono consentite. In alternativa, è possibile impostare l'opzione **Consenti l'accesso a Servizi di Azure** su **Sì** nel portale dal riquadro **Sicurezza connessione** e fare clic su **Salva**. Se il tentativo di connessione non è consentito, la richiesta non raggiunge il server Database di Azure per MariaDB.

> [!IMPORTANT]
> Questa opzione permette di configurare il firewall in maniera tale da consentire tutte le connessioni da Azure, incluse le connessioni dalle sottoscrizioni di altri clienti. Quando si seleziona questa opzione, assicurarsi che l'account di accesso e le autorizzazioni utente limitino l'accesso ai soli utenti autorizzati.
> 

![Configurare Possibilità di accedere ai servizi di Azure nel portale](./media/concepts-firewall-rules/allow-azure-services.png)

### <a name="connecting-from-a-vnet"></a>Connessione da un VNet
Per connettersi in modo sicuro al database di Azure per il server MariaDB da un VNet, provare a usare gli [endpoint del servizio VNet](./concepts-data-access-security-vnet.md). 

## <a name="programmatically-managing-firewall-rules"></a>Gestione di regole del firewall a livello di programmazione
Oltre al portale di Azure, le regole del firewall possono essere gestite a livello di programmazione usando l'interfaccia della riga di comando di Azure. 

Vedere anche [creare e gestire regole del firewall di database di Azure per MariaDB usando l'interfaccia della riga di comando di Azure](./howto-manage-firewall-cli.md)

## <a name="troubleshooting-firewall-issues"></a>Risoluzione dei problemi relativi al firewall
Quando l'accesso al server Database di Azure per MariaDB non si comporta come previsto, considerare quanto segue:

* **Le modifiche all'elenco dei consentiti non sono state ancora applicate:** per rendere effettive le modifiche apportate alla configurazione del firewall del server Database di Azure per MariaDB possono essere necessari fino a cinque minuti.

* **L'accesso non è autorizzato oppure è stata usata una password errata:** quando un account di accesso non dispone delle autorizzazioni per il server Database di Azure per MariaDB o la password utilizzata non è corretta, la connessione al server Database di Azure per MariaDB verrà negata. La creazione di un'impostazione del firewall fornisce ai client soltanto un'opportunità di connessione al server; ogni client deve fornire le credenziali di sicurezza necessarie.

* **Indirizzo IP dinamico:** se si dispone di una connessione Internet con indirizzi IP dinamici e si verificano problemi di comunicazione attraverso il firewall, è possibile provare una delle seguenti soluzioni:

   * Chiedere al provider di servizi Internet (ISP) l'intervallo di indirizzi IP assegnato ai computer client che accedono al server Database di Azure per MariaDB, quindi aggiungere l'intervallo di indirizzi IP come una regola firewall.

   * Ottenere indirizzi IP statici per i computer client, quindi aggiungere gli indirizzi IP come regole del firewall.

* **L'IP del server sembra essere pubblico:** Le connessioni al database di Azure per il server MariaDB vengono instradate tramite un gateway di Azure accessibile pubblicamente. Tuttavia, l'indirizzo IP effettivo del server è protetto dal firewall. Per altre informazioni, vedere l' [articolo sull'architettura della connettività](concepts-connectivity-architecture.md). 

## <a name="next-steps"></a>Passaggi successivi
- [Creare e gestire le regole firewall di Database di Azure per MariaDB con il portale di Azure](./howto-manage-firewall-portal.md)
- [Creare e gestire regole del firewall di database di Azure per MariaDB usando l'interfaccia della riga di comando](./howto-manage-firewall-cli.md)
- [Endpoint del servizio VNet nel database di Azure per MariaDB](./concepts-data-access-security-vnet.md)
