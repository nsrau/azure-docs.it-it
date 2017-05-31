---
title: Regole di firewall per il server MySQL del database di Azure | Documentazione Microsoft
description: Descrive le regole di firewall per il server MySQL del database di Azure.
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonh
ms.assetid: 
ms.service: mysql-database
ms.tgt_pltfrm: portal
ms.topic: article
ms.date: 05/10/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 511cc50c28b9b114b763f3c46cb1e26f15575349
ms.contentlocale: it-it
ms.lasthandoff: 05/10/2017

---

# <a name="azure-database-for-mysql-server-firewall-rules"></a>Regole di firewall per il server MySQL del database di Azure
I firewall impediscono qualsiasi accesso al server di database finché non vengono specificati i computer autorizzati. Il firewall concede l'accesso al server in base all'indirizzo IP di origine di ogni richiesta.

Per configurare il firewall, creare regole del firewall che specificano gli intervalli di indirizzi IP accettabili. È possibile creare regole di firewall a livello di server.

**Regole di firewall:** queste regole consentono ai client di accedere all'intero server MySQL del database di Azure, ovvero tutti i database all'interno dello stesso server logico. Le regole di firewall a livello di server possono essere configurate usando il portale di Azure o comandi dell'interfaccia della riga di comando. Per creare regole di firewall a livello di server è necessario essere il proprietario o un collaboratore della sottoscrizione.

## <a name="firewall-overview"></a>Panoramica del firewall
Per impostazione predefinita, l'accesso database al database di Azure per il server MySQL è bloccato dal firewall. Per iniziare a usare il server da un altro computer è necessario specificare una o più regole di firewall a livello di server che consentano di accedere al server. Usare le regole di firewall per specificare quali intervalli di indirizzi IP da Internet consentire. L'accesso al sito Web del portale di Azure non è influenzato dalle regole di firewall.

I tentativi di connessione da Internet e Azure devono prima superare il firewall per poter raggiungere il database di Azure dal database MySQL, come illustrato nel diagramma seguente:

![Esempio del flusso di funzionamento del firewall](./media/concepts-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet"></a>Connessione da Internet
Le regole di firewall a livello di server si applicano a tutti i database del database di Azure per il server MySQL.

Se l'indirizzo IP della richiesta rientra in uno degli intervalli specificati nelle regole firewall a livello di server, viene consentita la connessione.

Se l'indirizzo IP della richiesta non rientra negli intervalli specificati in una delle regole firewall a livello di database o di server, la richiesta di connessione ha esito negativo.

## <a name="programmatically-managing-firewall-rules"></a>Gestione di regole del firewall a livello di programmazione
Oltre al portale di Azure, le regole di firewall possono essere gestite a livello di programmazione usando l'interfaccia della riga di comando di Azure. Vedere anche [Creare e gestire Database di Azure per le regole di firewall MySQL tramite l'interfaccia della riga di comando di Azure](./howto-manage-firewall-using-cli.md)

## <a name="troubleshooting-the-database-firewall"></a>Risoluzione dei problemi del firewall del database
Quando l'accesso al servizio di database di Microsoft Azure per server MySQL non si comporta come previsto, considerare quanto segue:

* **Le modifiche all'elenco di consentiti non sono state ancora applicate:** per rendere effettive le modifiche apportate alla configurazione del firewall del database di Azure per server MySQL possono essere necessari fino a cinque minuti.

* **L'accesso non è autorizzato oppure è stata usata una password errata:** quando un account di accesso non dispone delle autorizzazioni per il server di database di Azure per server MySQL o la password utilizzata non è corretta, la connessione al server di database di Azure per server MySQL verrà negata. La creazione di un'impostazione del firewall fornisce ai client soltanto un'opportunità di connessione al server; ogni client deve fornire le credenziali di sicurezza necessarie.

* **Indirizzo IP dinamico:** se si dispone di una connessione Internet con indirizzi IP dinamici e si verificano problemi di comunicazione attraverso il firewall, è possibile provare una delle seguenti soluzioni:

* Chiedere al provider di servizi Internet (ISP) l'intervallo di indirizzi IP assegnato ai computer client che accedono al server di database di Azure per server MySQL, quindi aggiungere l'intervallo di indirizzi IP come una regola di firewall.

* Ottenere indirizzi IP statici per i computer client, quindi aggiungere gli indirizzi IP come regole del firewall.

## <a name="next-steps"></a>Passaggi successivi

[Creare e gestire regole di firewall per il database di Azure per MySQL mediante il portale di Azure](./howto-manage-firewall-using-portal.md)
[Creare e gestire regole di firewall per il database di Azure per MySQL mediante l'interfaccia della riga di comando di Azure](./howto-manage-firewall-using-cli.md)

