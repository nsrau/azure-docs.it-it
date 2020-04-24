---
title: Limiti del servizio Azure blockchain
description: Panoramica del servizio e dei limiti funzionali nel servizio Azure blockchain
ms.date: 04/02/2020
ms.topic: conceptual
ms.reviewer: ravastra
ms.openlocfilehash: 71e1bebf10fa0142870d03977182472da1ad031f
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80676524"
---
# <a name="limits-in-azure-blockchain-service"></a>Limiti nel servizio Azure blockchain

Il servizio Azure blockchain presenta limiti di servizio e funzionale, ad esempio il numero di nodi di cui un membro può disporre, le restrizioni del Consorzio e gli importi di archiviazione.

## <a name="pricing-tier"></a>Piano tariffario

I limiti massimi per le transazioni e i nodi validator dipendono dal fatto che il servizio Azure blockchain sia stato provisioning a piani tariffari Basic o standard.

| Piano tariffario | Numero massimo di nodi transazione | Numero massimo di nodi validator |
|:---|:---:|:---:|
| Basic | 10 | 1 |
| Standard | 10 | 2 |

La rete del consorzio deve avere almeno due nodi di livello standard del servizio Azure blockchain. I nodi di livello standard includono due nodi validator. Quattro nodi validator sono necessari per soddisfare i [consensi di tolleranza di errore bizantino di Istanbul](https://github.com/jpmorganchase/quorum/wiki/Quorum-Consensus).

Usare il livello Basic per lo sviluppo, i test e i concetti di prova. Usare il livello standard per le distribuzioni di livello produzione. È necessario usare anche il livello *Standard* anche se si usa Blockchain Data Manager o si invia un volume elevato di transazioni private.

Dopo la creazione di membri non è possibile passare dal piano tariffario Basic a quello Standard.

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

    Ma devono essere riinvitati a partecipare al Consorzio e creare un nuovo membro. Le risorse membro esistenti non vengono eliminate per mantenere le transazioni cronologiche.

* **Tutti i membri di un consorzio devono usare la stessa versione di Ledger**

    Per altre informazioni sulle versioni di patch, aggiornamenti e Ledger disponibili nel servizio Azure blockchain, vedere applicazione di [patch, aggiornamenti e versioni](ledger-versions.md).

## <a name="performance"></a>Prestazioni

Non utilizzare la funzione *ETH. Estimate* gas per ogni invio di transazione. La funzione *ETH. Estimate* è a elevato utilizzo di memoria. La chiamata della funzione più volte riduce drasticamente le transazioni al secondo.

Se possibile, usare un valore di gas conservativo per inviare le transazioni e ridurre al minimo l'uso di *ETH. Estimate*.

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui criteri relativi a patch e aggiornamenti per i sistemi- [patch, aggiornamenti e versioni](ledger-versions.md).
