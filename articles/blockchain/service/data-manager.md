---
title: Che cos'è blockchain Data Manager per il servizio Azure blockchain
description: Blockchain Data Manager per acquisire, trasformare e recapitare i dati blockchain agli argomenti di griglia di eventi.
services: azure-blockchain
author: PatAltimore
ms.author: patricka
ms.date: 11/04/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: chroyal
ms.openlocfilehash: e5fc38767d6127e341e257c23411d23b739d0362
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73518242"
---
# <a name="what-is-blockchain-data-manager-for-azure-blockchain-service"></a>Che cos'è blockchain Data Manager per il servizio Azure blockchain?

Blockchain Data Manager acquisisce, trasforma e recapita i dati delle transazioni del servizio blockchain di Azure negli argomenti di griglia di eventi di Azure che forniscono l'integrazione di blockchain Ledger affidabile e scalabile con i servizi di Azure.

Nella maggior parte degli scenari blockchain aziendali, un Ledger blockchain è una parte di una soluzione. Ad esempio, per trasferire un asset da un'entità a un'altra, è necessario un meccanismo per l'invio della transazione. È quindi necessario un meccanismo per la lettura dei dati Ledger per assicurarsi che la transazione sia stata accettata e che le modifiche dello stato risultante siano quindi integrate con la soluzione end-to-end. In questo esempio, se si scrive un contratto intelligente per trasferire gli asset, è possibile usare blockchain Data Manager per integrare le applicazioni e gli archivi dati fuori catena. Per l'esempio di trasferimento di asset, quando un asset viene trasferito in blockchain, gli eventi e le modifiche dello stato della proprietà vengono recapitati da blockchain Data Manager tramite griglia di eventi. È quindi possibile usare più gestori di eventi possibili per la griglia di eventi per archiviare i dati di blockchain fuori catena o reagire in tempo reale alle modifiche di stato.

Blockchain Data Manager esegue tre funzioni principali: acquisizione, trasformazione e recapito.

![Funzioni Data Manager blockchain](./media/data-manager/functions.png)

## <a name="capture"></a>Acquisizione

Ogni istanza di blockchain Data Manager si connette a un nodo di transazione membro del servizio blockchain di Azure. Solo gli utenti con accesso al nodo di transazione possono creare una connessione garantendo il controllo di accesso appropriato ai dati dei clienti. Un'istanza di Data Manager blockchain acquisisce in modo affidabile tutti i dati del blocco non elaborato e delle transazioni non elaborati dal nodo della transazione e può essere ridimensionato per supportare carichi di lavoro aziendali

## <a name="transform"></a>Trasformare

È possibile usare blockchain Data Manager per decodificare lo stato degli eventi e delle proprietà configurando le applicazioni Smart Contract all'interno di blockchain Data Manager. Per aggiungere un contratto intelligente, fornire l'ABI e il bytecode del contratto. Blockchain Data Manager utilizza gli artefatti Smart Contract per decodificare e individuare gli indirizzi del contratto. Dopo aver aggiunto l'applicazione blockchain all'istanza, blockchain Data Manager individua in modo dinamico l'indirizzo del contratto intelligente quando il contratto intelligente viene distribuito al Consorzio e invia lo stato dell'evento e della proprietà decodificato alle destinazioni configurate.

## <a name="deliver"></a>Distribuzione

Blockchain Data Manager supporta le connessioni in uscita per più argomenti di griglia di eventi per qualsiasi istanza di blockchain Data Manager specificata. È possibile inviare i dati di blockchain a una singola destinazione o inviare dati blockchain a più destinazioni. Usando blockchain Data Manager, è possibile creare una soluzione scalabile di pubblicazione dei dati basata su eventi per qualsiasi distribuzione di blockchain.

## <a name="configuration-options"></a>Opzioni di configurazione

È possibile configurare blockchain Data Manager per soddisfare le esigenze della soluzione. Ad esempio, è possibile effettuare il provisioning di:

* Singola istanza di blockchain Data Manager per un membro del servizio blockchain di Azure.
* Istanza di blockchain Data Manager per ogni nodo di transazione del servizio blockchain di Azure. Ad esempio, i nodi di transazione privati possono avere una propria istanza di blockchain Data Manager per mantenere la riservatezza.
* Un'istanza di Data Manager blockchain può supportare più connessioni di output. È possibile usare un'istanza di blockchain Data Manager per gestire tutti i punti di integrazione di pubblicazione dei dati per un membro del servizio blockchain di Azure.

## <a name="next-steps"></a>Passaggi successivi

Provare [a creare un'istanza di Blockchain Data Manager](data-manager-portal.md) per un membro del servizio blockchain di Azure.
