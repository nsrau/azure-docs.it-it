---
title: Azure Blockchain Service supportate le versioni di contabilità, l'applicazione di patch e aggiornamento
description: Panoramica delle versioni Ledger supportati nel servizio di Azure Blockchain, inclusi i criteri relativi sistemi dell'applicazione di patch e sistema gestito e aggiornamenti gestita dall'utente.
services: azure-blockchain
keywords: blockchain
author: PatAltimore
ms.author: patricka
ms.date: 05/02/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: janders
manager: femila
ms.openlocfilehash: 53f65ec91a1e0f1e5a6322f0125bf83cd3e400b2
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66399096"
---
# <a name="supported-azure-blockchain-service-ledger-versions"></a>Versioni supportate di contabilità generale del servizio di Azure Blockchain

Usa Azure Blockchain Service basato su Ethereum [Quorum](https://www.goquorum.com/developers) ledger progettato per l'elaborazione delle transazioni private all'interno di un gruppo di partecipanti noti, identificato come un consorzio nel servizio di Azure Blockchain.

Attualmente, il servizio di Blockchain di Azure supporta [Quorum versione 2.2.1](https://github.com/jpmorganchase/quorum/releases/tag/v2.2.1) e [gestore transazioni Tessera](https://github.com/jpmorganchase/tessera).

## <a name="managing-updates-and-upgrades"></a>Gestire gli aggiornamenti

Controllo delle versioni di quorum viene eseguita tramite una versione principale e secondaria e versioni di patch. Ad esempio, se la versione del Quorum è 2.0.1, tipo di versione potrebbe essere classificato nel modo seguente:

|Major | Minorenne  | Patch  |
| :--- | :----- | :----- |
| 2 | 0 | 1 | 

Azure Blockchain Service Aggiorna automaticamente le versioni di patch del Quorum per i membri esistenti in esecuzione entro 30 giorni di reso disponibile dal Quorum.

## <a name="availability-of-new-ledger-versions"></a>Disponibilità di nuove versioni di contabilità generale

Azure Blockchain Service fornisce le versioni più recenti principali e secondarie della contabilità Quorum entro 60 giorni di essere disponibile dal produttore del Quorum. Un massimo di quattro versioni secondarie vengono forniti per consorzi possibile scegliere durante il provisioning di un nuovo membro e consortium. Versione esegue l'aggiornamento da un principale o secondaria non è attualmente supportata.

## <a name="next-steps"></a>Passaggi successivi

[Limiti di servizio di Azure Blockchain](limits.md)
