---
title: Panoramica dello sviluppo di Azure Blockchain Service
description: Introduzione allo sviluppo di soluzioni nel servizio di Azure Blockchain.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: jackyhsu
manager: femila
ms.openlocfilehash: 388a5d8c80c3e2462602959e9d5cbc1452974d1f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65027901"
---
# <a name="azure-blockchain-service-development-overview"></a>Panoramica dello sviluppo di Azure Blockchain Service

Con il servizio di Azure Blockchain, è possibile creare reti di blockchain per abilitare gli scenari aziendali, ad esempio inventario dei beni, token digitali, fedeltà e ricompensa, supply chain finanziari e la provenienza consortium. Questo articolo è un'introduzione alla panoramica dello sviluppo del servizio di Azure Blockchain e argomenti più importanti per implementare blockchain per enterprise.

## <a name="client-connection-to-azure-blockchain-service"></a>Connessione client al servizio di Azure Blockchain

Esistono diversi tipi di client per reti di blockchain inclusi nodi completi, luce nodi e i client remoti. Azure Blockchain Service compila una blockchain di rete che include i nodi. È possibile usare diversi client come il gateway al servizio di Azure Blockchain per lo sviluppo di blockchain. Azure Blockchain Service offre l'autenticazione di base o la chiave di accesso come un endpoint di sviluppo. Di seguito sono riportati i client più diffusi è possibile usare la connessione.

### <a name="metamask"></a>MetaMask

MetaMask è un portafoglio basate su browser (client remoto), client RPC e contratto di base explorer. A differenza di portafogli altri browser, MetaMask inserisce un'istanza di web3 nel contesto del browser JavaScript, che agisce come un client RPC che si connette a una varietà di blockchain Ethereum (*mainnet*, *Ropsten testnet*, *Kovan testnet*, nodo RPC locale, ecc.). È possibile impostare facilmente a connettersi al servizio di Azure Blockchain e avviare lo sviluppo di blockchain con Remix RPC personalizzato.

### <a name="geth"></a>Geth

Geth è l'interfaccia della riga di comando per l'esecuzione di un nodo Ethereum completo implementato in viaggio. Non è necessario per l'esecuzione completa di nodo, ma può avviare la console interattiva che fornisce un ambiente di runtime di JavaScript che espone un'API JavaScript per interagire con il servizio di Azure Blockchain.

## <a name="development-framework-configuration"></a>Configurazione dell'infrastruttura di sviluppo

Per sviluppare soluzioni blockchain aziendali più sofisticate, un framework di sviluppo è necessario per connettersi a reti diverse blockchain, gestione del ciclo di vita di contratto smart, automatizzare i test, distribuire contratti intelligenti con gli script e dotare una console interattiva.

Truffle è un framework di sviluppo più diffusi blockchain per scrivere, compilare, distribuire e testare applicazioni decentralizzate in Ethereum blockchain. È anche possibile considerare Truffle come un framework che tenta di integrare facilmente sviluppo smart contract e lo sviluppo web tradizionale.

Anche il progetto più piccolo interagisce con almeno due nodi di blockchain: Uno nel computer dello sviluppatore e l'altro che rappresenta la rete in cui lo sviluppatore distribuisce l'applicazione. Ad esempio, la rete Ethereum pubblica principale o servizio di Azure Blockchain. Truffle offre un sistema di gestione degli elementi di compilazione e distribuzione per ogni rete e la memorizzazione avviene in modo da semplificare la distribuzione dell'applicazione finale. Per altre informazioni, vedere [Avvio rapido: Usare Truffle per connettersi a una rete un servizio di Azure Blockchain](connect-truffle.md).

## <a name="ethereum-quorum-private-transaction"></a>Transazione privato Ethereum Quorum

Quorum è un protocollo basato su Ethereum libro mastro distribuito con transazioni e sulla privacy di contratto e nuovi meccanismi di consenso. Miglioramenti chiave su Go Ethereum includono:

* Privacy - Quorum supporta transazioni private e contratti privati tramite la separazione dello stato pubbliche e private e Usa gli scambi di messaggi crittografati peer-to-peer per il trasferimento diretto di dati privati ai partecipanti di rete.
* Meccanismi di consenso alternativi - senza che sia necessario per il consenso di proof-of-work o proof-di-gioco in una rete con autorizzazione. Quorum offre diversi meccanismi del consenso progettate per le catene consortium, ad esempio IBFT e raggruppamento.  Servizi di Azure Blockchain Usa il meccanismo di consenso IBFT.

* Eseguire il peering Permissioning - nodo e peer permissioning smart Contract, assicurandosi solo le entità note tramite l'accesso alla rete
* Prestazioni più elevate - Quorum offre prestazioni più elevate rispetto a Geth pubblico

Per istruzioni dettagliate, vedere [Esercitazione: Invio di una transazione usando il servizio di Azure Blockchain](send-transaction.md) per un esempio di transazione privato.

## <a name="block-explorers"></a>Strumenti di esplorazione di blocco

Strumenti di esplorazione di blocco sono i browser di blockchain online che consentono di visualizzare il contenuto di singoli blocchi, i dati degli indirizzi delle transazioni e la cronologia. Informazioni di blocco di base sono disponibili tramite Monitoraggio di Azure nel servizio di Azure Blockchain, tuttavia, se è necessario informazioni più dettagliate durante lo sviluppo, strumenti di esplorazione di blocco può essere utile.  Sono disponibili strumenti di esplorazione di blocco open source più diffusi che è possibile usare. Di seguito è riportato un elenco di strumenti di esplorazione di blocco che funzionano con Azure Blockchain servizio:

* [Esplora il servizio Azure Blockchain](https://web3labs.com/azure-offer) Web3 Labs
* [BlockScout](https://github.com/Azure-Samples/blockchain/blob/master/ledger/template/ethereum-on-azure/technology-samples/blockscout/README.md)

## <a name="tps-measurement"></a>Misurazione dei programmi di transazione

Transazioni al secondo (TPS) velocità blockchain viene usato in più scenari aziendali, è importante per evitare i colli di bottiglia e le inefficienze di sistema. Frequenza delle transazioni elevata può essere difficile da gestire all'interno di una blockchain decentralizzata. Una misurazione accurata dei programmi di transazione potrà dipendere da diversi fattori, ad esempio thread del server, le dimensioni della coda di transazione, la latenza di rete e sicurezza. Se è necessario misurare la velocità di transazione durante lo sviluppo, è uno strumento open source più diffusi [ChainHammer](https://github.com/drandreaskrueger/chainhammer).

## <a name="next-steps"></a>Passaggi successivi

[Avvio rapido: Usare Truffle per connettersi a una rete un servizio di Azure Blockchain](connect-truffle.md)
