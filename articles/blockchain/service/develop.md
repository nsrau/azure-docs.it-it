---
title: Panoramica sullo sviluppo di servizi blockchain di Azure
description: Introduzione allo sviluppo di soluzioni nel servizio Azure blockchain.
ms.date: 11/22/2019
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: 3748a1ca473d817f536ba7c912d2485ffc14de2d
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455887"
---
# <a name="azure-blockchain-service-development-overview"></a>Panoramica sullo sviluppo di servizi blockchain di Azure

Con il servizio Azure blockchain è possibile creare reti blockchain del Consorzio per abilitare scenari aziendali come il rilevamento di asset, il token digitale, la fedeltà e la ricompensa, la supply chain finanziaria e la provenienza. Le sezioni seguenti introducono lo sviluppo di servizi blockchain di Azure per l'implementazione di soluzioni blockchain aziendali.

## <a name="connecting-to-azure-blockchain-service"></a>Connessione al servizio Azure blockchain

Esistono diversi tipi di client per le reti blockchain, tra cui nodi completi, nodi leggeri e client remoti. Il servizio Azure blockchain compila una rete blockchain che include nodi. È possibile usare client diversi come gateway per il servizio Azure blockchain per lo sviluppo di blockchain. Il servizio Azure blockchain offre l'autenticazione di base o la chiave di accesso come endpoint di sviluppo. Di seguito sono riportati i client più diffusi che è possibile usare Connect.

### <a name="visual-studio-code"></a>Visual Studio Code

È possibile connettersi ai membri del Consorzio usando Azure blockchain Development Kit Visual Studio Code estensione. Una volta connessi a un consorzio, è possibile compilare e distribuire contratti intelligenti a un membro del consorzio del servizio Azure Blockchain.

Per altre informazioni, vedere [Guida introduttiva: usare Visual Studio Code per connettersi a una rete di Azure blockchain Service Consortium](connect-vscode.md).

### <a name="metamask"></a>Metamask

Metamask è un portafoglio basato su browser (client remoto), un client RPC e un'esplorazione di base di contratti. A differenza di altri portafogli del browser, metamask inserisce un'istanza di Web3 nel contesto JavaScript del browser, che funge da client RPC che si connette a un'ampia gamma di Ethereum blockchain (*mainnet*, *Ropsten Testnet*, *Kovan*Testnet, nodo RPC locale, e così via). È possibile configurare facilmente RPC personalizzate per connettersi al servizio Azure blockchain e avviare lo sviluppo di blockchain con Remix.

Per altre informazioni, vedere [Guida introduttiva: usare metamask per connettere e distribuire un contratto intelligente](connect-metamask.md)

### <a name="geth"></a>Geth

Geth è l'interfaccia della riga di comando per l'esecuzione di un nodo Ethereum completo implementato in go. Non è necessario eseguire il nodo completo, ma è possibile avviare la console interattiva che fornisce un ambiente di runtime JavaScript che espone un'API JavaScript per interagire con il servizio Azure blockchain.

Per altre informazioni, vedere [Guida introduttiva: usare Geth per connettersi a un nodo di transazione del servizio blockchain di Azure](connect-geth.md).

## <a name="development-framework-configuration"></a>Configurazione del Framework di sviluppo

Per sviluppare sofisticate soluzioni blockchain aziendali, è necessario un Framework di sviluppo per connettersi a diverse reti blockchain e gestire i cicli di vita di contratti intelligenti.

Tartufo è un noto framework di sviluppo blockchain per la scrittura, la compilazione, la distribuzione e il test di applicazioni decentralizzate in Ethereum blockchain. È anche possibile pensare al tartufo come a un Framework che tenta di integrare agevolmente lo sviluppo di contratti intelligenti e lo sviluppo Web tradizionale.

La maggior parte dei progetti interagisce con almeno due nodi blockchain. Gli sviluppatori usano un blockchain locale durante lo sviluppo. Quando l'applicazione è pronta per il test o il rilascio, lo sviluppatore distribuisce in una rete blockchain. Ad esempio, la rete Ethereum pubblica principale o il servizio Azure blockchain. Il tartufo può essere usato per compilare e distribuire i contratti intelligenti per ogni rete e semplifica la distribuzione finale dell'applicazione. Per altre informazioni, vedere [Guida introduttiva: usare il tartufo per connettersi a una rete del servizio Azure blockchain](connect-truffle.md).

## <a name="ethereum-quorum-private-transactions"></a>Transazioni private quorum Ethereum

Il quorum è un protocollo di Ledger distribuito basato su Ethereum con transazioni con la privacy dei contratti e nuovi meccanismi di consenso. I miglioramenti principali rispetto a go-Ethereum includono:

* **Privacy** : il quorum supporta le transazioni private e i contratti privati attraverso la separazione dello stato pubblico e privato e USA scambi di messaggi crittografati peer-to-peer per il trasferimento diretto dei dati privati ai partecipanti di rete.
* **Meccanismi di consenso alternativi** : i consensi di prova di lavoro o di prova di scommessa non sono necessari per una rete con autorizzazioni. Il quorum offre diversi meccanismi di consenso, progettati per catene di Consorzio, ad esempio ZATTERa e IBFT.  Il servizio Azure blockchain usa il meccanismo di consenso IBFT.
* **Autorizzazione peer** : il nodo e l'autorizzazione peer con contratti intelligenti garantiscono che solo le parti note possano essere aggiunte alla rete.
* **Prestazioni più elevate** : il quorum offre prestazioni superiori rispetto a Geth pubblici.

## <a name="block-explorers"></a>Esplora blocchi

Gli Esplora blocchi sono browser blockchain online che visualizzano il contenuto del singolo blocco, i dati dell'indirizzo di transazione e la cronologia. Le informazioni di base sui blocchi sono disponibili tramite monitoraggio di Azure nel servizio Azure blockchain. Tuttavia, se sono necessarie informazioni più dettagliate durante lo sviluppo, gli Esplora blocchi possono essere utili.  Gli Esplora blocchi seguenti funzionano con il servizio Azure blockchain:

* [Esplora servizi di Azure blockchain per Epiro](https://azuremarketplace.microsoft.com/marketplace/apps/blk-technologies.azure-blockchain-explorer-template?tab=Overview) da Web3 Labs
* [BlockScout](https://github.com/Azure-Samples/blockchain/blob/master/ledger/template/ethereum-on-azure/technology-samples/blockscout/README.md)

È anche possibile creare una finestra di esplorazione dei blocchi personalizzata usando blockchain Data Manager e Azure Cosmos DB, vedere [esercitazione: usare Data Manager blockchain per inviare dati a Azure Cosmos DB](data-manager-cosmosdb.md).

## <a name="tps-measurement"></a>Misurazione TPS

Quando blockchain viene usato in più scenari aziendali, la velocità delle transazioni al secondo (TPS) è importante per evitare colli di bottiglia e inefficienze del sistema. I tassi di transazione elevati possono essere difficili da gestire all'interno di un blockchain decentralizzato. Una misurazione accurata del TPS può essere interessata da diversi fattori, ad esempio thread del server, dimensioni della coda delle transazioni, latenza di rete e sicurezza. Se è necessario misurare la velocità di TPS durante lo sviluppo, uno strumento open source comune è [ChainHammer](https://github.com/drandreaskrueger/chainhammer).

## <a name="next-steps"></a>Passaggi successivi

Provare una guida introduttiva usando Azure blockchain Development Kit per Ethereum per la connessione a un consorzio nel servizio blockchain di Azure.

> [!div class="nextstepaction"]
> [Usare Visual Studio Code per connettersi al servizio Azure blockchain](connect-vscode.md)