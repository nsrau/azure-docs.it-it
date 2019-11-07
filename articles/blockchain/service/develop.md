---
title: Panoramica sullo sviluppo di servizi blockchain di Azure
description: Introduzione allo sviluppo di soluzioni nel servizio Azure blockchain.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/11/2019
ms.topic: conceptual
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: a9444847fb75bdf01cabba98057605afbe03c9fc
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73577436"
---
# <a name="azure-blockchain-service-development-overview"></a>Panoramica sullo sviluppo di servizi blockchain di Azure

Con il servizio Azure blockchain è possibile creare reti blockchain del Consorzio per abilitare scenari aziendali come il rilevamento di asset, il token digitale, la fedeltà e la ricompensa, la supply chain finanziaria e la provenienza. Questo articolo è un'introduzione allo sviluppo di servizi blockchain di Azure e agli argomenti chiave per implementare blockchain per le aziende.

## <a name="client-connection-to-azure-blockchain-service"></a>Connessione client al servizio Azure blockchain

Esistono diversi tipi di client per le reti blockchain, tra cui nodi completi, nodi leggeri e client remoti. Il servizio Azure blockchain compila una rete blockchain che include nodi. È possibile usare client diversi come gateway per il servizio Azure blockchain per lo sviluppo di blockchain. Il servizio Azure blockchain offre l'autenticazione di base o la chiave di accesso come endpoint di sviluppo. Di seguito sono riportati i client più diffusi che è possibile usare Connect.

### <a name="metamask"></a>Metamask

Metamask è un portafoglio basato su browser (client remoto), un client RPC e un'esplorazione di base di contratti. A differenza di altri portafogli del browser, metamask inserisce un'istanza di Web3 nel contesto JavaScript del browser, che funge da client RPC che si connette a un'ampia gamma di Ethereum blockchain (*mainnet*, *Ropsten Testnet*, *Kovan*Testnet, nodo RPC locale, e così via). È possibile configurare facilmente RPC personalizzate per connettersi al servizio Azure blockchain e avviare lo sviluppo di blockchain con Remix.

### <a name="geth"></a>Geth

Geth è l'interfaccia della riga di comando per l'esecuzione di un nodo Ethereum completo implementato in go. Non è necessario eseguire il nodo completo, ma è possibile avviare la console interattiva che fornisce un ambiente di runtime JavaScript che espone un'API JavaScript per interagire con il servizio Azure blockchain.

## <a name="development-framework-configuration"></a>Configurazione del Framework di sviluppo

Per sviluppare sofisticate soluzioni blockchain aziendali, è necessario un Framework di sviluppo per connettersi a diverse reti blockchain, gestire il ciclo di vita dei contratti intelligenti, automatizzare i test, distribuire smart Contract con script e dotare una console interattiva.

Tartufo è un noto framework di sviluppo blockchain per la scrittura, la compilazione, la distribuzione e il test di applicazioni decentralizzate in Ethereum blockchain. È anche possibile pensare al tartufo come a un Framework che tenta di integrare agevolmente lo sviluppo di contratti intelligenti e lo sviluppo Web tradizionale.

Anche il progetto più piccolo interagisce con almeno due nodi blockchain: uno nel computer dello sviluppatore e l'altro che rappresenta la rete in cui lo sviluppatore distribuisce l'applicazione. Ad esempio, la rete Ethereum pubblica principale o il servizio Azure blockchain. Il tartufo fornisce un sistema per la gestione degli artefatti di compilazione e distribuzione per ogni rete ed esegue questa operazione in modo da semplificare la distribuzione finale dell'applicazione. Per altre informazioni, vedere [Guida introduttiva: usare il tartufo per connettersi a una rete del servizio Azure blockchain](connect-truffle.md).

## <a name="ethereum-quorum-private-transaction"></a>Transazione privata quorum Ethereum

Il quorum è un protocollo di Ledger distribuito basato su Ethereum con transazioni con la privacy dei contratti e nuovi meccanismi di consenso. I miglioramenti principali rispetto a go-Ethereum includono:

* Privacy: il quorum supporta le transazioni private e i contratti privati attraverso la separazione dello stato pubblico e privato e USA scambi di messaggi crittografati peer-to-peer per il trasferimento diretto dei dati privati ai partecipanti di rete.
* Meccanismi di consenso alternativi, senza necessità di consenso di prova o di prova in una rete con autorizzazioni. Il quorum offre diversi meccanismi di consenso, progettati per catene di Consorzio, ad esempio ZATTERa e IBFT.  I servizi blockchain di Azure usano il meccanismo di consenso IBFT.

* Autorizzazione peer-autorizzazione per nodi e peer con contratti intelligenti, assicurando che solo le parti note possano partecipare alla rete
* Prestazioni più elevate: il quorum offre prestazioni superiori rispetto a Geth pubblici

Per un esempio di transazione privata, vedere [esercitazione: inviare una transazione usando il servizio Azure blockchain](send-transaction.md) .

## <a name="block-explorers"></a>Esplora blocchi

Gli Esplora blocchi sono browser blockchain online che visualizzano il contenuto del singolo blocco, i dati dell'indirizzo di transazione e la cronologia. Le informazioni di base sui blocchi sono disponibili tramite monitoraggio di Azure nel servizio Azure blockchain. Tuttavia, se sono necessarie informazioni più dettagliate durante lo sviluppo, gli Esplora blocchi possono essere utili.  Sono disponibili noti Esplora blocchi open source che è possibile usare. Di seguito è riportato un elenco di Esplora blocchi che funzionano con il servizio Azure blockchain:

* [Esplora servizi di Azure blockchain per Epiro](https://azuremarketplace.microsoft.com/marketplace/apps/blk-technologies.azure-blockchain-explorer-template?tab=Overview) da Web3 Labs
* [BlockScout](https://github.com/Azure-Samples/blockchain/blob/master/ledger/template/ethereum-on-azure/technology-samples/blockscout/README.md)

## <a name="tps-measurement"></a>Misurazione TPS

Quando blockchain viene usato in più scenari aziendali, la velocità delle transazioni al secondo (TPS) è importante per evitare colli di bottiglia e inefficienze del sistema. I tassi di transazione elevati possono essere difficili da gestire all'interno di un blockchain decentralizzato. Una misurazione accurata del TPS può essere interessata da diversi fattori, ad esempio thread del server, dimensioni della coda delle transazioni, latenza di rete e sicurezza. Se è necessario misurare la velocità di TPS durante lo sviluppo, uno strumento open source comune è [ChainHammer](https://github.com/drandreaskrueger/chainhammer).

## <a name="next-steps"></a>Passaggi successivi

[Guida introduttiva: usare il tartufo per connettersi a una rete del servizio Azure blockchain](connect-truffle.md)
