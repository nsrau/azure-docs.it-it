---
title: Limiti del servizio Azure blockchain
description: Panoramica del servizio e dei limiti funzionali nel servizio Azure blockchain
ms.date: 11/22/2019
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: f4001ee520f3f3136d1bac5ca047c80526fc92e6
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455649"
---
# <a name="limits-in-azure-blockchain-service"></a>Limiti nel servizio Azure blockchain

Il servizio Azure blockchain presenta limiti di servizio e funzionale, ad esempio il numero di nodi di cui un membro può disporre, le restrizioni del Consorzio e gli importi di archiviazione.

## <a name="pricing-tier"></a>Pricing tier

I limiti massimi per le transazioni e i nodi validator dipendono dal fatto che il servizio Azure blockchain sia stato provisioning a piani tariffari Basic o standard.

| Pricing tier | Numero massimo di nodi transazione | Numero massimo di nodi validator |
|:---|:---:|:---:|
| Basic | 10 | 1 |
| Standard | 10 | 2 |

La modifica del piano tariffario tra Basic e standard dopo la creazione di membri non è supportata.

## <a name="storage-capacity"></a>Capacità di archiviazione

La quantità massima di spazio di archiviazione che può essere usata per nodo per i log e i dati di Ledger è 1,8 terabyte.

La riduzione delle dimensioni dello spazio di archiviazione del log e del Ledger non è supportata.

## <a name="consortium-limits"></a>Limiti del Consorzio

* I **nomi di Consorzio e membri devono essere univoci** da altri nomi di Consorzio e membri nel servizio Azure blockchain.

* **Non è possibile modificare i nomi di membri e consorzi**

* **Tutti i membri di un consorzio devono trovarsi nello stesso piano tariffario**

* **Tutti i membri che fanno parte di un consorzio devono trovarsi nella stessa area**

    Il primo membro creato in un consorzio impone l'area. I membri invitati al consorzio devono trovarsi nella stessa area del primo membro. La limitazione di tutti i membri alla stessa area contribuisce a garantire che il consenso alla rete non sia influenzato negativamente.

* **Un consorzio deve avere almeno un amministratore**

    Se è presente un solo amministratore in un consorzio, questi non possono rimuovere se stessi dal consorzio o eliminare il membro fino a quando un altro amministratore non viene aggiunto o promosso nel Consorzio.

* **Non è possibile aggiungere di nuovo i membri rimossi dal Consortium**

    Ma devono essere riinvitati a partecipare al Consorzio e creare un nuovo membro. La risorsa membro esistente non viene eliminata per mantenere le transazioni cronologiche.

* **Tutti i membri di un consorzio devono usare la stessa versione di Ledger**

    Per altre informazioni sulle versioni di patch, aggiornamenti e Ledger disponibili nel servizio Azure blockchain, vedere applicazione di [patch, aggiornamenti e versioni](ledger-versions.md).

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui criteri relativi a patch e aggiornamenti per i sistemi- [patch, aggiornamenti e versioni](ledger-versions.md).
