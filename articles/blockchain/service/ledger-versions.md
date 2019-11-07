---
title: Azure blockchain Service Ledger Versions, patching and upgrade
description: Panoramica delle versioni dei Ledger supportati nel servizio Azure blockchain, inclusi i criteri riguardanti l'applicazione di patch ai sistemi e gli aggiornamenti gestiti dall'utente e gestiti dal sistema.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/14/2019
ms.topic: conceptual
ms.service: azure-blockchain
ms.reviewer: janders
manager: femila
ms.openlocfilehash: 16d0f0876e7d75dfd0266468ddc3005b86fc632f
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73579810"
---
# <a name="supported-azure-blockchain-service-ledger-versions"></a>Versioni di Ledger del servizio blockchain di Azure supportate

Il servizio Azure blockchain usa il Ledger del [quorum](https://www.goquorum.com/developers) basato su Ethereum progettato per l'elaborazione di transazioni private in un gruppo di partecipanti noti, identificato come un consorzio nel servizio blockchain di Azure.

Attualmente, il servizio Azure blockchain supporta il [quorum versione 2.2.3](https://github.com/jpmorganchase/quorum/releases/tag/v2.2.3) e la [gestione transazioni tessera](https://github.com/jpmorganchase/tessera).

## <a name="managing-updates-and-upgrades"></a>Gestire gli aggiornamenti

Il controllo delle versioni in quorum viene eseguito tramite un rilascio principale, secondario e patch. Se, ad esempio, la versione del quorum è 2.0.1, il tipo di versione verrà categorizzato come indicato di seguito:

|Major | Minorenne  | Patch  |
| :--- | :----- | :----- |
| 2 | 0 | 1 | 

Il servizio Azure blockchain aggiorna automaticamente le versioni delle patch del quorum ai membri in esecuzione esistenti entro 30 giorni dalla disponibilità del quorum.

## <a name="availability-of-new-ledger-versions"></a>Disponibilità di nuove versioni del Ledger

Il servizio Azure blockchain fornisce le versioni principali e secondarie più recenti del Ledger del quorum entro 60 giorni dalla disponibilità del produttore del quorum. Quando si effettua il provisioning di un nuovo membro e un consorzio, per i consorzi è possibile scegliere un massimo di quattro versioni secondarie. L'aggiornamento da a una versione principale o secondaria non è al momento supportato. Ad esempio, se si esegue la versione 2. x, un aggiornamento alla versione 3. x non è al momento supportato. Analogamente, se si esegue la versione 2,2, non è attualmente supportato l'aggiornamento alla versione 2,3.

## <a name="next-steps"></a>Passaggi successivi

[Limiti nel servizio Azure blockchain](limits.md)
