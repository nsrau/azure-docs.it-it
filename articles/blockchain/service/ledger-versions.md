---
title: Versioni di contabilità generale, patch, & aggiornamento del servizio Blockchain di AzureAzure Blockchain Service ledger versions, patching, & upgrade
description: Panoramica delle versioni dei libri contabili supportati nel servizio Blockchain di Azure, inclusi i criteri relativi all'applicazione di patch ai sistemi e agli aggiornamenti gestiti dal sistema e gestiti dall'utente.
ms.date: 11/20/2019
ms.topic: conceptual
ms.reviewer: janders
ms.openlocfilehash: 8d4a77699dd80743acfadd4d72d6d75bc1939b3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74325179"
---
# <a name="supported-azure-blockchain-service-ledger-versions"></a>Versioni contabili supportate del servizio Blockchain di AzureSupported Azure Blockchain Service ledger versions

Il servizio Blockchain di Azure usa il registro Quorum basato su Ethereum progettato per l'elaborazione di transazioni private all'interno di un gruppo di partecipanti noti, identificato come un consorzio nel servizio Blockchain di Azure.Azure Blockchain Service uses the Ethereum-based [Quorum](https://www.goquorum.com/developers) ledger designed for the processing of private transactions within a group of known participants, identified as a consortium in Azure Blockchain Service.

Attualmente, il servizio Blockchain di Azure supporta [la versione quorum 2.2.3](https://github.com/jpmorganchase/quorum/releases/tag/v2.2.3) e il [gestore delle transazioni Tessera.](https://github.com/jpmorganchase/tessera)

## <a name="managing-updates-and-upgrades"></a>Gestire gli aggiornamenti

Il controllo delle versioni in Quorum viene eseguito tramite un rilascio principale, secondario e patch. Ad esempio, se la versione quorum è 2.0.1, il tipo di versione verrà classificato come segue:

|Principale | Minorenne  | Patch  |
| :--- | :----- | :----- |
| 2 | 0 | 1 | 

Azure Blockchain Service automatically updates patch releases of Quorum to existing running members within 30 days of being made available from Quorum.

## <a name="availability-of-new-ledger-versions"></a>Disponibilità di nuove versioni contabili

Il servizio Blockchain di Azure offre le versioni principali e secondarie più recenti del registro Quorum entro 60 giorni dalla disponibilità del produttore quorum. Sono disponibili al massimo quattro rilasci minori per i consorzi tra cui scegliere quando si esegue il provisioning di un nuovo membro e consorzio. L'aggiornamento da una versione principale o secondaria non è attualmente supportato. Ad esempio, se si esegue la versione 2.x, un aggiornamento alla versione 3.x non è attualmente supportato. Analogamente, se si esegue la versione 2.2, un aggiornamento alla versione 2.3 non è attualmente supportato.

## <a name="next-steps"></a>Passaggi successivi

[Limiti nel servizio Blockchain di AzureLimits in Azure Blockchain Service](limits.md)
