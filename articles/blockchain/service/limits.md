---
title: Limiti di Blockchain di Azure
description: Panoramica del servizio e i limiti funzionali nel servizio di Azure Blockchain
services: azure-blockchain
keywords: blockchain
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: janders
manager: femila
ms.openlocfilehash: 169ec7a8ef407af3f754046aa8e3b06793a7e962
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65028171"
---
# <a name="limits-in-azure-blockchain-service"></a>Limiti di servizio di Azure Blockchain

Azure Blockchain Service, servizio e i limiti funzionali, ad esempio il numero di nodi che può avere un membro, consortium restrizioni e dimensioni di archiviazione.

## <a name="pricing-tier"></a>Piano tariffario

Limiti massimi per le transazioni e i nodi di validator variano a seconda se si esegue il provisioning del servizio di Azure Blockchain a Basic o Standard piani tariffari.

| Piano tariffario | Numero massimo nodi di transazione | Numero massimo nodi di convalida |
|:---|:---:|:---:|
| Basic | 10 | 1 |
| Standard | 10 | 2 |

Modifica il livello di prezzo tra Basic e Standard dopo la creazione di membri non è supportata.

## <a name="storage-capacity"></a>Capacità di archiviazione

La quantità massima di spazio di archiviazione che possa essere usati per ogni nodo per i log e dati di contabilità è di 1 terabyte.

La riduzione delle dimensioni di archiviazione di contabilità generale e di log non è supportata.

## <a name="consortium-limits"></a>Limiti Consortium

* **Nomi di membri e Consortium devono essere univoci** da altri nomi di membri e consortium nel servizio di Blockchain di Azure.

* **Non è possibile modificare i nomi di membro e consortium**

* **Tutti i membri in un consorzio devono essere nello stesso piano tariffario**

* **Tutti i membri che fanno parte di un consorzio devono risiedere nella stessa area**

    Il primo membro creato in un consorzio determina l'area. Membri invitati da consorzio devono risiedere nella stessa area come il primo membro. Limitazione a tutti i membri della stessa area aiuta a garantire il consenso di rete non è comporti conseguenze negative.

* **Un consorzio deve avere almeno un amministratore**

    Se è presente solo un amministratore in un consorzio, e non può rimuovere se stessi dal consorzio o eliminare il membro fino a quando non viene aggiunto o alzare di livello il consorzio un altro amministratore.

* **Impossibile aggiungerla di nuovo i membri rimossi dal consorzio**

    Piuttosto, essi devono essere invitato di nuovo di unire il consorzio e creare un nuovo membro. La risorsa di membri esistenti non vengono eliminati per mantenere la cronologia delle transazioni.

* **Tutti i membri in un consorzio necessario usare la stessa versione di contabilità generale**

    Per altre informazioni sull'applicazione di patch, aggiornamenti e le versioni di contabilità disponibile nel servizio di Azure Blockchain, vedere [l'applicazione di patch, aggiornamenti e le versioni](ledger-versions.md).

## <a name="next-steps"></a>Passaggi successivi

* [L'applicazione di patch, aggiornamenti e le versioni](ledger-versions.md)
