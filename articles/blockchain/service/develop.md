---
title: Panoramica dello sviluppo del servizio Blockchain di AzureAzure Blockchain Service development overview
description: Introduzione allo sviluppo di soluzioni nel servizio Blockchain di Azure.Introduction on developing solutions on Azure Blockchain Service.
ms.date: 03/26/2020
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: 4a98e5ea025894303bc5c77bba0c6154a08315f2
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80348371"
---
# <a name="azure-blockchain-service-development-overview"></a>Panoramica dello sviluppo del servizio Blockchain di AzureAzure Blockchain Service development overview

Con il servizio Blockchain di Azure, puoi creare reti blockchain del consorzio per abilitare scenari aziendali come il monitoraggio degli asset, il token digitale, la fedeltà e la ricompensa, la sicurezza finanziaria e la provenienza. Nelle sezioni seguenti viene introdotto lo sviluppo del servizio Blockchain di Azure per l'implementazione di soluzioni blockchain aziendali.

## <a name="connecting-to-azure-blockchain-service"></a>Connessione al servizio Blockchain di AzureConnecting to Azure Blockchain Service

Esistono diversi tipi di client per le reti blockchain, inclusi nodi completi, nodi leggeri e client remoti. Il servizio Blockchain di Azure crea una rete blockchain che include nodi. È possibile usare client diversi come gateway per il servizio Blockchain di Azure per lo sviluppo blockchain. Il servizio Blockchain di Azure offre l'autenticazione di base o la chiave di accesso come endpoint di sviluppo. Di seguito sono riportati i client più diffusi che è possibile utilizzare connect.

### <a name="visual-studio-code"></a>Visual Studio Code

È possibile connettersi ai membri del consorzio usando l'estensione di codice di Visual Studio di Azure Blockchain Development Kit.You can connect to consortium members using the Azure Blockchain Development Kit Visual Studio Code extension. Una volta connessi a un consorzio, è possibile compilare e distribuire contratti intelligenti a un membro del consorzio del servizio Azure Blockchain.

Per sviluppare soluzioni blockchain aziendali sofisticate, è necessario un framework di sviluppo per connettersi a diverse reti blockchain e gestire i cicli di vita dei contratti intelligenti. La maggior parte dei progetti interagisce con almeno due nodi blockchain. Gli sviluppatori utilizzano una blockchain locale durante lo sviluppo. Quando l'applicazione è pronta per il test o il rilascio, lo sviluppatore viene distribuito in una rete blockchain. Ad esempio, la principale rete Ethereum pubblica o il servizio Blockchain di Azure.For example, the main public Ethereum network or Azure Blockchain Service. Azure Blockchain Development Kit per Ethereum estensione in Visual Studio Code utilizza Tartufo. Il tartufo è un popolare framework di sviluppo blockchain per scrivere, compilare, distribuire e testare le applicazioni decentralizzate su blockchain Ethereum. Si può anche pensare al tartufo come a un framework che tenta di integrare senza soluzione di continuità lo sviluppo di contratti intelligenti e lo sviluppo web tradizionale.

Per altre informazioni, vedere [Guida introduttiva: Usare Visual Studio Code per connettersi a](connect-vscode.md)una rete del consorzio di servizi Blockchain di Azure.For more information, see Quickstart: Use Visual Studio Code to connect to an Azure Blockchain Service consortium network .

### <a name="metamask"></a>MetaMaschera

MetaMask è un portafoglio basato su browser (client remoto), client RPC ed Esplora contratti di base. A differenza di altri portafogli del browser, MetaMask inserisce un'istanza web3 nel contesto JavaScript del browser, agendo come un client RPC che si connette a una varietà di blockchain Ethereum (*mainnet*, *Ropsten testnet*, *Kovan testnet*, nodo RPC locale, ecc.). È possibile configurare RPC personalizzata facilmente per connettersi al servizio Blockchain di Azure e avviare lo sviluppo blockchain utilizzando Remix.You can set up custom RPC easily to connect to Azure Blockchain Service and start blockchain development using Remix.

Per altre informazioni, vedere [Guida introduttiva: Usare MetaMask per connettere e distribuire un contratto intelligenteFor](connect-metamask.md) more information, see Quickstart: Use MetaMask to connect and deploy a smart contract

### <a name="geth"></a>Geth

Geth è l'interfaccia della riga di comando per l'esecuzione di un nodo Ethereum completo implementato in Go. Non è necessario eseguire un nodo completo, ma è possibile avviare la console interattiva che fornisce un ambiente di runtime JavaScript esponendo un'API JavaScript per interagire con il servizio Blockchain di Azure.You don't need to run full node but can launch its interactive console that provides a JavaScript runtime environment exposing a JavaScript API to interact with Azure Blockchain Service.

Per altre informazioni, vedere Guida introduttiva: Usare Geth per connettersi a un nodo di transazione del servizio Blockchain di Azure.For more information, see [Quickstart: Use Geth to attach to an Azure Blockchain Service transaction node.](connect-geth.md)

## <a name="ethereum-quorum-private-transactions"></a>Transazioni private del quorum Ethereum

Quorum è un protocollo di contabilità distribuita basato su Ethereum con la privacy delle transazioni più i contratti e nuovi meccanismi di consenso. I principali miglioramenti rispetto a Go-Ethereum includono:

* **Privacy** - Quorum supporta le transazioni private e i contratti privati attraverso la separazione dello stato pubblico e privato e utilizza scambi di messaggi crittografati peer-to-peer per il trasferimento diretto di dati privati ai partecipanti alla rete.
* **Meccanismi di consenso alternativi** - il consenso di prova del lavoro o di prova di svolgimento non è necessario per una rete autorizzata. Quorum offre molteplici meccanismi di consenso che sono progettati per catene di consorzi come RAFT e IBFT.Il servizio Blockchain di Azure usa il meccanismo di consenso IBFT.Azure Blockchain Service uses the IBFT consensus mechanism.
* **Autorizzazione peer:** l'autorizzazione per nodi e peer tramite contratti intelligenti garantisce che solo le parti note possano unirsi alla rete.
* **Prestazioni più elevate** - Quorum offre prestazioni più elevate rispetto a Geth pubblico.

## <a name="block-explorers"></a>Blocca esploratori

Gli esploratori di blocchi sono browser blockchain online che visualizzano il contenuto dei singoli blocchi, i dati dell'indirizzo delle transazioni e la cronologia. Le informazioni di base sui blocchi sono disponibili tramite Monitoraggio di Azure nel servizio Blockchain di Azure.Basic block information is available through Azure Monitor in Azure Blockchain Service. Tuttavia, se sono necessarie informazioni più dettagliate durante lo sviluppo, gli esploratori di blocchi possono essere utili.  Gli esploratori di blocchi seguenti funzionano con il servizio Blockchain di Azure:The following block explorers work with Azure Blockchain Service:

* [Epirus Azure Blockchain Service Explorer](https://azuremarketplace.microsoft.com/marketplace/apps/blk-technologies.azure-blockchain-explorer-template?tab=Overview) da Web3 Labs
* [BlockScout (Blocco)](https://github.com/Azure-Samples/blockchain/blob/master/ledger/template/ethereum-on-azure/technology-samples/blockscout/README.md)

È anche possibile creare il proprio esploratore di blocchi usando Blockchain Data Manager e Azure Cosmos DB, vedere [Esercitazione: Usare Blockchain Data Manager per inviare dati a Azure Cosmos DB.](data-manager-cosmosdb.md)

## <a name="tps-measurement"></a>Misurazione TPS

Poiché la blockchain viene utilizzata in scenari più aziendali, la velocità delle transazioni al secondo (TPS) è importante per evitare colli di bottiglia e inefficienze del sistema. Tassi di transazione elevati possono essere difficili da mantenere all'interno di una blockchain decentralizzata. Una misurazione TPS accurata può essere influenzata da diversi fattori, ad esempio thread del server, dimensione della coda delle transazioni, latenza di rete e sicurezza. Se è necessario misurare la velocità TPS durante lo sviluppo, un popolare strumento open-source è [ChainHammer](https://github.com/drandreaskrueger/chainhammer).

## <a name="next-steps"></a>Passaggi successivi

Prova una guida introduttiva usando Azure Blockchain Development Kit per Ethereum per connetterti a un consorzio nel servizio Blockchain di Azure.Try a quickstart using Azure Blockchain Development Kit for Ethereum to attach to a consortium on Azure Blockchain Service.

> [!div class="nextstepaction"]
> [Usare Visual Studio Code per connettersi al servizio Azure Blockchain](connect-vscode.md)