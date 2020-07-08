---
title: Azure blockchain Service Ledger Versions, patching, & upgrade
description: Panoramica delle versioni dei Ledger supportati nel servizio Azure blockchain. Inclusione di criteri per l'applicazione di patch e aggiornamenti dei sistemi.
ms.date: 06/30/2020
ms.topic: conceptual
ms.reviewer: ravastra
ms.openlocfilehash: ea7c54e229178320329204a3199ab2b4c44058fe
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85807741"
---
# <a name="supported-azure-blockchain-service-ledger-versions"></a>Versioni di Ledger del servizio blockchain di Azure supportate

Il servizio Azure blockchain usa il Ledger del [quorum](https://www.goquorum.com/developers) basato su Ethereum progettato per l'elaborazione di transazioni private in un gruppo di partecipanti noti, identificato come un consorzio nel servizio blockchain di Azure.

Attualmente, il servizio Azure blockchain supporta la [versione quorum 2.6.0](https://github.com/jpmorganchase/quorum/releases/tag/v2.6.0) e la [gestione transazioni tessera](https://github.com/jpmorganchase/tessera).

## <a name="managing-updates-and-upgrades"></a>Gestire gli aggiornamenti

Il controllo delle versioni in quorum viene eseguito tramite un rilascio principale, secondario e patch. Se, ad esempio, la versione del quorum è 2.0.1, il tipo di versione verrà categorizzato come indicato di seguito:

|Principale | Minorenne  | Patch  |
| :--- | :----- | :----- |
| 2 | 0 | 1 | 

Il servizio Azure blockchain aggiorna automaticamente le versioni delle patch del quorum ai membri in esecuzione esistenti entro 30 giorni dalla disponibilità del quorum.

## <a name="availability-of-new-ledger-versions"></a>Disponibilità di nuove versioni del Ledger

Il servizio Azure blockchain fornisce le versioni principali e secondarie più recenti del Ledger del quorum entro 60 giorni dalla disponibilità del produttore del quorum. Quando si effettua il provisioning di un nuovo membro e un consorzio, per i consorzi è possibile scegliere un massimo di quattro versioni secondarie. L'aggiornamento da a una versione principale o secondaria non è al momento supportato. Ad esempio, se si esegue la versione 2. x, un aggiornamento alla versione 3. x non è al momento supportato. Analogamente, se si esegue la versione 2,2, non è attualmente supportato l'aggiornamento alla versione 2,3.

## <a name="how-to-check-quorum-ledger-version"></a>Come controllare la versione Ledger del quorum

È possibile controllare la versione del quorum nel membro del servizio Azure blockchain collegandosi al nodo usando Geth o visualizzando i log di diagnostica.

### <a name="using-geth"></a>Uso di Geth

Connettersi al nodo del servizio blockchain di Azure usando Geth. Ad esempio: `geth attach https://myblockchainmember.blockchain.azure.com:3200/<Access key>`.

Una volta connesso il nodo, Geth segnala la versione quorum simile all'output seguente:

``` text
instance: Geth/v1.9.7-stable-9339be03(quorum-v2.6.0)/linux-amd64/go1.13.12
```

Per altre informazioni sull'uso di Geth, vedere [Guida introduttiva: usare Geth per connettersi a un nodo di transazione del servizio blockchain di Azure](connect-geth.md).

### <a name="using-diagnostic-logs"></a>Uso dei log di diagnostica

Se si abilitano i log di diagnostica, la versione del quorum viene segnalata per i nodi di transazione. Il seguente messaggio di log informativo del nodo include ad esempio la versione quorum.

``` text 
{"NodeName":"transaction-node","Message":"INFO [06-22|05:31:45.156] Starting peer-to-peer node instance=Geth/v1.9.7-stable-9339be03(quorum-v2.6.0)/linux-amd64/go1.13.12\n"}
{"NodeName":"transaction-node","Message":"[*] Starting Quorum node with QUORUM_VERSION=2.6.0, TESSERA_VERSION=0.10.5 and PRIVATE_CONFIG=/working-dir/c/tm.ipc\n"}
111
```

Per altre informazioni sui log di diagnostica, vedere [monitorare il servizio Azure blockchain tramite monitoraggio di Azure](monitor-azure-blockchain-service.md#diagnostic-settings).

## <a name="how-to-check-genesis-file-content"></a>Come controllare il contenuto del file Genesis

Per controllare il contenuto del file Genesis del nodo blockchain, è possibile usare l'API JavaScript Ethereum seguente:

``` bash
admin.nodeInfo.protocols
```
È possibile chiamare l'API usando una console di Geth o una libreria Web3. Per altre informazioni sull'uso di Geth, vedere [Guida introduttiva: usare Geth per connettersi a un nodo di transazione del servizio blockchain di Azure](connect-geth.md).

## <a name="next-steps"></a>Passaggi successivi

[Limiti nel servizio Azure blockchain](limits.md)
