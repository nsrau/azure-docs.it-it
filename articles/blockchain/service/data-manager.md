---
title: Che cos'è Blockchain Data Manager per il servizio Azure Blockchain?
description: Blockchain Data Manager consente di acquisire, trasformare e distribuire i dati della blockchain negli argomenti di Griglia di eventi.
ms.date: 11/04/2019
ms.topic: overview
ms.reviewer: chroyal
ms.openlocfilehash: 6583a99ef6efaefa732178d0228708cea82365b1
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326207"
---
# <a name="what-is-blockchain-data-manager-for-azure-blockchain-service"></a>Che cos'è Blockchain Data Manager per il servizio Azure Blockchain?

Blockchain Data Manager consente di acquisire, trasformare e distribuire i dati delle transazioni del servizio Azure Blockchain negli argomenti di Griglia di eventi di Azure, offrendo un'integrazione affidabile e scalabile del libro mastro della blockchain con i servizi di Azure.

Nella maggior parte degli scenari di blockchain aziendali, il libro mastro è solo una parte della soluzione. Ad esempio, per trasferire un asset da un'entità a un'altra, è necessario un meccanismo per l'invio della transazione. È quindi necessario un meccanismo per la lettura dei dati del libro mastro, per assicurarsi che la transazione sia stata eseguita e accettata e che le risultanti modifiche dello stato vengano poi integrate con la soluzione end-to-end. In questo esempio, se si scrive un contratto intelligente per il trasferimento di asset, è possibile usare Blockchain Data Manager per integrare le applicazioni e gli archivi dati fuori catena. Per l'esempio di trasferimento di asset, quando un asset viene trasferito nella blockchain, gli eventi e le modifiche dello stato delle proprietà vengono distribuiti da Blockchain Data Manager tramite Griglia di eventi. È quindi possibile usare più gestori di eventi possibili per Griglia di eventi per archiviare i dati della blockchain fuori catena o rispondere in tempo reale alle modifiche di stato.

Blockchain Data Manager esegue tre funzioni principali: acquisizione, trasformazione e distribuzione.

![Funzioni di Blockchain Data Manager](./media/data-manager/functions.png)

## <a name="capture"></a>Acquisizione

Ogni istanza di Blockchain Data Manager si connette a un nodo della transazione membro del servizio Azure Blockchain. Solo gli utenti con accesso al nodo della transazione possono creare una connessione, assicurando il controllo di accesso appropriato ai dati dei clienti. Un'istanza di Blockchain Data Manager acquisisce in modo affidabile tutti i dati di blocchi e transazioni non elaborati dal nodo della transazione ed è scalabile per supportare carichi di lavoro aziendali.

## <a name="transform"></a>Trasformare

È possibile usare Blockchain Data Manager per decodificare lo stato di eventi e proprietà configurando applicazioni di contratto intelligente all'interno di Blockchain Data Manager. Per aggiungere un contratto intelligente, fornire l'ABI e il bytecode del contratto. Blockchain Data Manager usa gli artefatti del contratto intelligente per decodificare e individuare gli indirizzi del contratto. Dopo l'aggiunta dell'applicazione blockchain all'istanza, Blockchain Data Manager individua dinamicamente l'indirizzo del contratto intelligente quando viene distribuito al consorzio e invia lo stato decodificato di eventi e proprietà alle destinazioni configurate.

## <a name="deliver"></a>Distribuzione

Blockchain Data Manager supporta più connessioni in uscita per gli argomenti di Griglia di eventi per qualsiasi istanza di Blockchain Data Manager specificata. È possibile inviare i dati della blockchain a una o più destinazioni. Usando Blockchain Data Manager, è possibile creare una soluzione scalabile di pubblicazione dei dati basata su eventi per qualsiasi distribuzione di blockchain.

## <a name="configuration-options"></a>Opzioni di configurazione

È possibile configurare Blockchain Data Manager per soddisfare le esigenze della soluzione. Ad esempio, è possibile effettuare il provisioning di:

* Una singola istanza di Blockchain Data Manager per un membro del servizio Azure Blockchain.
* Un'istanza di Blockchain Data Manager per ogni nodo della transazione del servizio Azure Blockchain. Ad esempio, i nodi di transazioni privati possono avere una propria istanza di Blockchain Data Manager per mantenere la riservatezza.
* Un'istanza di Blockchain Data Manager può supportare più connessioni di output. È possibile usare un'istanza di Blockchain Data Manager per gestire tutti i punti di integrazione di pubblicazione dei dati per un membro del servizio Azure Blockchain.

## <a name="next-steps"></a>Passaggi successivi

Provare a [creare un'istanza di Blockchain Data Manager](data-manager-portal.md) per un membro del servizio Azure Blockchain.
