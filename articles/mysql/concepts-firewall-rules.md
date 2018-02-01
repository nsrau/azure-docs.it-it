---
title: Regole di firewall per il server MySQL del database di Azure | Documentazione Microsoft
description: Descrive le regole di firewall per il server MySQL del database di Azure.
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 1/20/2018
ms.openlocfilehash: 3cc879fb568965af98997889437c6348e0d341e1
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2018
---
# <a name="azure-database-for-mysql-server-firewall-rules"></a>Regole di firewall per il server MySQL del database di Azure
I firewall impediscono qualsiasi accesso al server di database finché non vengono specificati i computer autorizzati. Il firewall concede l'accesso al server in base all'indirizzo IP di origine di ogni richiesta.

Per configurare un firewall, creare regole del firewall che specificano gli intervalli di indirizzi IP accettabili. È possibile creare regole firewall a livello di server.

**Regole di firewall:** queste regole consentono ai client di accedere all'intero server MySQL del database di Azure, ovvero tutti i database all'interno dello stesso server logico. Le regole del firewall a livello di server possono essere configurate usando il portale di Azure o i comandi dell'interfaccia della riga di comando di Azure. Per creare regole firewall a livello di server è necessario essere il proprietario o un collaboratore della sottoscrizione.

## <a name="firewall-overview"></a>Panoramica del firewall
L'accesso a database a Database di Azure per il server MySQL è bloccato dal firewall per impostazione predefinita. Per iniziare a usare il server da un altro computer è necessario specificare una o più regole firewall a livello di server che consentano di accedere al server. Usare le regole firewall per specificare gli intervalli di indirizzi IP da Internet a cui concedere l'accesso. L'accesso al sito Web del portale di Azure non è influenzato dalle regole di firewall.

I tentativi di connessione da Internet e Azure devono prima superare il firewall per poter raggiungere il database di Azure dal database MySQL, come illustrato nel diagramma seguente:

![Esempio del flusso di funzionamento del firewall](./media/concepts-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet"></a>Connessione da Internet
Le regole di firewall a livello di server si applicano a tutti i database del database di Azure per il server MySQL.

Se l'indirizzo IP della richiesta rientra in uno degli intervalli specificati nelle regole del firewall a livello di server, viene consentita la connessione.

Se l'indirizzo IP della richiesta non rientra negli intervalli specificati in una delle regole del firewall a livello di database o di server, la richiesta di connessione ha esito negativo.

## <a name="connecting-from-azure"></a>Connessione da Azure
Per consentire alle applicazioni da Azure di stabilire la connessione al server di Database di Azure per MySQL, è necessario abilitare le connessioni da Azure. Ad esempio, per ospitare un'applicazione delle app Web di Azure o un'applicazione che viene eseguita in una macchina virtuale di Azure oppure per connettersi da un gateway di gestione dati di Azure Data Factory. Le risorse non devono essere necessariamente nella stessa rete virtuale (VNET) o nello stesso gruppo di risorse perché la regola del firewall consenta tali connessioni. Quando si tenta di connettersi al server di database da un'applicazione di Azure, il firewall verifica che le connessioni da Azure siano consentite. Sono disponibili alcuni metodi per consentire questi tipi di connessioni. Un'impostazione del firewall con indirizzo iniziale e finale uguale a 0.0.0.0 indica che queste connessioni sono consentite. In alternativa, è possibile impostare l'opzione **Consenti l'accesso a Servizi di Azure** su **Sì** nel portale dal riquadro **Sicurezza connessione** e fare clic su **Salva**. Se il tentativo di connessione non è consentito, la richiesta non raggiungerà il server di Database di Azure per MySQL.

> [!IMPORTANT]
> Questa opzione permette di configurare il firewall in maniera tale da consentire tutte le connessioni da Azure, incluse le connessioni dalle sottoscrizioni di altri clienti. Quando si seleziona questa opzione, assicurarsi che l'account di accesso e le autorizzazioni utente limitino l'accesso ai soli utenti autorizzati.
> 

![Configurare Possibilità di accedere ai servizi di Azure nel portale](./media/concepts-firewall-rules/allow-azure-services.png)

## <a name="programmatically-managing-firewall-rules"></a>Gestione di regole del firewall a livello di programmazione
Oltre al portale di Azure, le regole del firewall possono essere gestite a livello di programmazione usando l'interfaccia della riga di comando di Azure. Vedere anche [Creare e gestire Database di Azure per le regole di firewall MySQL tramite l'interfaccia della riga di comando di Azure](./howto-manage-firewall-using-cli.md)

## <a name="troubleshooting-the-database-firewall"></a>Risoluzione dei problemi del firewall del database
Quando l'accesso al servizio Database di Microsoft Azure per server MySQL non si comporta come previsto, considerare quanto segue:

* **Le modifiche all'elenco di consentiti non sono state ancora applicate:** per rendere effettive le modifiche apportate alla configurazione del firewall del database di Azure per server MySQL possono essere necessari fino a cinque minuti.

* **L'accesso non è autorizzato oppure è stata usata una password errata:** quando un account di accesso non dispone delle autorizzazioni per il server di database di Azure per server MySQL o la password utilizzata non è corretta, la connessione al server di database di Azure per server MySQL verrà negata. La creazione di un'impostazione del firewall fornisce ai client soltanto un'opportunità di connessione al server; ogni client deve fornire le credenziali di sicurezza necessarie.

* **Indirizzo IP dinamico:** se si dispone di una connessione Internet con indirizzi IP dinamici e si verificano problemi di comunicazione attraverso il firewall, è possibile provare una delle seguenti soluzioni:

* Chiedere al provider di servizi Internet (ISP) l'intervallo di indirizzi IP assegnato ai computer client che accedono al server di database di Azure per server MySQL, quindi aggiungere l'intervallo di indirizzi IP come una regola di firewall.

* Ottenere indirizzi IP statici per i computer client, quindi aggiungere gli indirizzi IP come regole del firewall.

## <a name="next-steps"></a>Passaggi successivi

[Creare e gestire regole di firewall per il database di Azure per MySQL mediante il portale di Azure](./howto-manage-firewall-using-portal.md)
[Creare e gestire regole di firewall per il database di Azure per MySQL mediante l'interfaccia della riga di comando di Azure](./howto-manage-firewall-using-cli.md)
