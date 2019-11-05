---
title: Gestione degli account per i token blockchain di Azure
description: Usando la gestione degli account dei token blockchain di Azure, è possibile creare gruppi e collegare account blockchain per controllare l'accesso alle azioni blockchain.
services: azure-blockchain
author: PatAltimore
ms.author: patricka
ms.date: 11/04/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: brendal
ms.openlocfilehash: b396b359585c8865c3b08284f4398076f37c7a6e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73512964"
---
# <a name="azure-blockchain-tokens-account-management"></a>Gestione degli account per i token blockchain di Azure

[!INCLUDE [Preview note](./includes/preview.md)]

Per una soluzione blockchain, gli utenti possono richiedere diversi livelli di accesso ai token creati con il servizio token blockchain di Azure. Nella maggior parte degli scenari blockchain è necessario pianificare e distribuire account blockchain diversi presenti sul Ledger. È anche necessario gestire l'accesso tra i partecipanti. Usando la gestione degli account dei token blockchain di Azure, è possibile creare gruppi e collegare account blockchain per controllare l'accesso alle azioni blockchain.

## <a name="blockchain-networks"></a>Reti blockchain

I token blockchain di Azure consentono la distribuzione e la gestione dei token in un set di reti blockchain. È possibile connettere un singolo Ledger blockchain o diversi Ledger blockchain al servizio.

## <a name="accounts"></a>Account

Per le reti blockchain connesse ai token blockchain di Azure, il servizio crea e gestisce le coppie di chiavi private-public dell'account ed esegue la firma e l'invio delle transazioni. I token blockchain di Azure forniscono anche il mapping delle identità per confrontare gli account con l'identità della chiave pubblica nel Ledger.

## <a name="groups"></a>Gruppi

I gruppi consentono di gestire un numero elevato di account blockchain tra le reti connesse. È possibile tenere traccia e controllare quali applicazioni e utenti nella directory hanno la possibilità di usare gli account tramite le API dei token blockchain di Azure. Ad esempio, è possibile raggruppare un set di account che rappresentano diverse linee di business o ruoli diversi e l'accesso ai token blockchain.

È anche possibile associare un gruppo a un utente Azure Active Directory o a un'entità servizio e l'entità dispone delle autorizzazioni per il gruppo e gli account associati.  

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui [modelli di token blockchain di Azure](templates.md)disponibili.
